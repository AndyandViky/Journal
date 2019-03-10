# StrongLoop microgateway使用总结（三）
## 源码引入
### github地址`https://github.com/strongloop/microgateway`
#### 不使用manage管理工具

#### 网关访问多个接口获取后台数据聚合问题

1. 由于自带策略invoke只实现一个后台接口对应一个网关接口.这导致数据聚合无法实现

2. 自定义策略, 思想: 网关请求多个接口,拿到数据整合再返回

3. invoke 主要实现方法, 拿到yaml文件中的属性 target-url 经过一系列的验证再发送http或者https请求

4. muti-invoke 实现方法: 修改 yaml 文件属性, 将 target-url 改为 object 类型, 在原本的invoke 策略下添加外循环, 依次请求接口, 由于是异步请求, 所以我们使用Promise来顺序执行, 以便我们确保所有接口的请求都是完成的, 如果报错,发送报错信息.

        js代码如下
        // Copyright IBM Corp. 2016. All Rights Reserved.
        // Node module: microgateway
        // US Government Users Restricted Rights - Use, duplication or disclosure
        // restricted by GSA ADP Schedule Contract with IBM Corp.

        'use strict';
        var _ = require('lodash');
        var url = require('url');
        var qs = require('qs');
        var zlib = require('zlib');
        var dsc = require('microgateway-datastore/client/index.js');

        ////one-time effort: read the cipher table into memory
        //var cipherTable;
        //try {
        //  //the mapping table of TLS to OpenSSL ciphersuites
        //  cipherTable = require(__dirname + '/../../lib/cipher-suites.json');
        //}
        //catch (err) {
        //  logger.error('Warning! Cannot read the cipher table for invoke policy. %s',
        //          err);
        //  cipherTable = {};
        //}


        /**
        * Do the real work of the invoke policy: read the property and decide the
        * parametersBuffer.concat, establish the connection after everything is ready.
        */
        function _main(props, context, next, logger, writeDst, tlsProfile) {
        //the default settings and error object
        var readSrc; 
        var options = []; // 存放url数组
        var isSecured;  // https控制变量
        var verb;
        var useChunk = false;
        var timeout = 60;
        var compression = false;
        var error = { name: 'PropertyError' };
        var data; 
        var datas = [];// 最终数据存储
        var dataSz = 0;
        var count = 0; // 请求计数器
        // 状态数组，获取所有请求的状态
        var Status = [];
        var promiseArr = [];
        if (typeof props['target-url'] === 'object') {
            for(var u in props['target-url']){
                // 将参数中的url全部拿出
                options.push(url.parse(props['target-url'][u]));
            }
        }
        //target-url 
        // 循环遍历url数组，发送请求
        for(var i=0; i<options.length; i++){
            if (!options[i] || !options[i].hostname || !options[i].protocol ||
                (options[i].protocol !== 'http:' && options[i].protocol !== 'https:')) {
                error.message = 'Invalid target-url: "' + props['target-url'][i] + '"';
                next(error);
                return;
            } else if (options[i].protocol === 'https:') {
                if (!tlsProfile) {
                    logger.warn('[test-target] no TLS profile for a HTTPS connection');
                }
                isSecured = true; 
            }
            if (logger.info()) {
                logger.info('[muti-invoke] url: %s', maskQueryStringInURL(options[i].href));
            }

            //verb: default to request.verb
            verb = props.verb ? String(props.verb).toUpperCase() :
                    (context.request ? context.request.verb.toUpperCase() : undefined);
            if (verb !== 'POST' && verb !== 'GET' && verb !== 'PUT' &&
                verb !== 'DELETE' && verb !== 'OPTIONS' && verb !== 'HEAD' &&
                verb !== 'PATCH') {
                error.message = 'Invalid verb: "' + props.verb + '"';
                next(error);
                return;
            } else {
                options[i].method = verb;
            }
            logger.debug('[muti-invoke] verb: %s', verb);

            //http-version: 1.1
            if (props['http-version'] && props['http-version'] !== '1.1') {
                error.message = 'Invalid http-version: "' + props['http-version'] + '"';
                next(error);
                return;
            }

            //timeout: between 1 to 86400 seconds
            if (!isNaN(parseInt(props.timeout, 10))) {
                var tmp = parseInt(props.timeout, 10);
                if (tmp < 1) {
                    timeout = 1;
                } else if (tmp > 86400) {
                    timeout = 86400;
                } else {
                    timeout = tmp;
                }
            }
            logger.debug('[muti-invoke] timeout: %s seconds', timeout);

            //authentication
            if (props.username && props.password) {
                options[i].auth = props.username + ':' + props.password;
            }

            //readSrc: decide where to read the data
            if (props.input) {
                if (typeof props.input === 'string') {
                var theIn = context.get(props.input);
                if (typeof theIn === 'object') {
                    logger.info('[muti-invoke] will read data and headers from "%s"',
                            props.input);
                    readSrc = theIn;
                }
                }

                if (!readSrc) {
                logger.error('Cannot read data or headers from the input ' +
                        'property "%s"', props.input);
                error.message = 'Invalid input: "' + props.input + '"';
                next(error);
                return;
                }
            } else {
                readSrc = context.message;
            }

            //clone the readSrc.headers, because some headers need to be excluded
            options[i].headers = _.clone(readSrc.headers);

            //The headers that should not be copied
            var excludes = [ 'host', 'connection', 'content-length', 'transfer-encoding' ];
            //deal with the user-agent: default, custom, remove, nochange
            excludes.push('user-agent');

            //test if the content-type is urlencoded
            var isFormUrlEncoded;

            for (var hn in options[i].headers) {
                var target = hn.toLowerCase();
                if (target === 'content-type' &&
                        options[i].headers[hn] === 'application/x-www-form-urlencoded') {
                isFormUrlEncoded = true;
                }

                var index = excludes.indexOf(target);
                if (index >= 0) {
                // remove the header that shouldn't be sent
                delete options[i].headers[hn];
                // remove the header already processed
                excludes.splice(index, 1);
                }

                //early exit
                if (excludes.length === 0 && isFormUrlEncoded) {
                break;
                }
            }
            //inject the *default* User-Agent
            options[i].headers['User-Agent'] = 'APIConnect/5.0 (MicroGateway)';

            //prepare the data and dataSz
            data = (readSrc.body === undefined ? '' : readSrc.body);
            if (!Buffer.isBuffer(data) && typeof data !== 'string') {
                if (typeof data === 'object') {
                if (isFormUrlEncoded) {
                    data = qs.stringify(data);
                } else {
                    data = JSON.stringify(data);
                }
                } else {
                    data = String(data);
                }
            }
            dataSz = data.length;

            //chunked-upload
            if (props['chunked-upload'] && props['chunked-upload'] !== 'false') {
                useChunk = true;
            }
            logger.debug('[muti-invoke] useChunk: %s', useChunk);

            //compression
            if (props.compression === true || props.compression === 'true') {
                compression = true;
            }
            logger.debug('[muti-invoke] compression: %s', compression);

            //Compress the data or not
            if (compression) {
                options[i].headers['Content-Encoding'] = 'gzip';
            }

            //when compression is true, we can only use chunks
            if (!compression && !useChunk && verb !== 'GET' && verb !== 'HEAD' && verb !== 'OPTIONS') {
                options[i].headers['Content-Length'] = dataSz;
                logger.debug('[muti-invoke] content-length = %d', dataSz);
            }
            //sensitive data
            //logger.debug('[invoke] w/ headers: %j', options.headers, {});

            //setup the HTTPs settings
            var http = isSecured ? require('https') : require('http');
            if (isSecured) {
                options[i].agent = false; // do we really want to set this?  no conn pooling
                options[i].rejectUnauthorized = false;
                if (tlsProfile) {
                //key
                options[i].key = tlsProfile['private-key'];

                //cert
                for (var c in tlsProfile.certs) {
                    if (tlsProfile.certs[c]['cert-type'] === 'PUBLIC') {
                        options[i].cert = tlsProfile.certs[c].cert;
                        break;
                    }
                }

                //ca list
                options[i].ca = [];
                for (var p in tlsProfile.certs) {
                    if (tlsProfile.certs[p]['cert-type'] === 'CLIENT') {
                    logger.debug('[muti-invoke] uses the ca.name: %s',
                            tlsProfile.certs[p].name);
                    options[i].ca.push(tlsProfile.certs[p].cert);

                    }
                }

                if (options[i].ca.length > 0 || tlsProfile['mutual-auth']) {
                    options[i].rejectUnauthorized = true;
                    logger.debug('[muti-invoke] rejectUnauthorized = true');
                }
                //secureProtocol
                if (tlsProfile.protocols && Array.isArray(tlsProfile.protocols)) {
                    for (var j = 0; j < tlsProfile.protocols.length; j++) {
                        switch (tlsProfile.protocols[j]) {
                            case 'TLSv1':
                            options[i].secureProtocol = 'TLSv1_method';
                            break;
                            case 'TLSv11':
                            options[i].secureProtocol = 'TLSv1_1_method';
                            break;
                            case 'TLSv12':
                            options[i].secureProtocol = 'TLSv1_2_method';
                            break;
                            default:
                            logger.warn('[muti-invoke] unsupported secure protocol: %s',
                                    tlsProfile.protocols[j]);
                            break;
                    }
                    if (options[i].secureProtocol) {
                        break;
                    }
                    }
                }

                //use default ciphers
                options[i].honorCipherOrder = true;
                options[i].ciphers = 'HIGH:MEDIUM:!aNULL:!eNULL:!RC4:@STRENGTH';
                //var ciphers = [];
                //if (tlsProfile.ciphers && Array.isArray(tlsProfile.ciphers)) {
                //  for (var k=0; k<tlsProfile.ciphers.length; k++) {
                //    var cipher = cipherTable[tlsProfile.ciphers[k]];
                //    if (cipher) {
                //      logger.debug("[invoke] using cipher: %s", cipher);
                //      ciphers.push(cipher);
                //    }
                //    else
                //      logger.warn("[invoke] unknown cipher: %s", tlsProfile.ciphers[k]);
                //  }
                //  options.ciphers = ciphers.join(':');
                //}
                }
            }
            

            //write the request
            try {
                promiseArr.push(getData(http,options[i],writeDst,data,datas,logger,Status,compression,timeout))
            } catch (err) {
                if(count == options.length && datas.length==0){ // 如果请求发送完毕并且数据不为空
                    error.name = 'ConnectionError';
                    error.message = err.toString();
                    next(error);
                    return;
                }
                else if(count == options.length && datas.length != 0){ // 如果请求发送完毕出现错误但是数据取到一部分
                    next(); 
                }
                else{
                    continue; // 否则继续请求数据
                }
            }
        }
        // 依次执行
        Promise.all(promiseArr)
            .then(function () {
                // 返回数据
                    writeDst.body = datas;
                    //Let Express itself to decide the final transfer-encoding
                    var discard = [ 'transfer-encoding' ];
                    for (var m in discard) {
                        var tbd = discard[m];
                        for (var n in writeDst.headers) {
                            if (n.toLowerCase() === tbd) {
                                delete writeDst.headers[n];
                                break;
                            }
                        }
                    }
                    // 在最后一次的请求返回后再向用户返回数据
                    var flag = false; // 判断所有请求的状态中是否存在 2开头的状态码
                    for(var s=0; s<Status.length; s++){
                        if(/^2/.test(String(Status[s].code))){
                            flag = true;
                            // 如果存在结束循环
                            writeDst.status = {
                                code: Status[s].code,
                                reason: Status[s].reason };
                            break;
                        }
                    }
                    // 只要存在成功的请求，我们都返回成功
                    if (flag) { // 存在2xx，请求至少成功1次
                        logger.info('[muti-invoke] received a %d response', writeDst.status.code);
                        next();
                    } else {
                        logger.error('[muti-invoke] OperationError! Received a non-2xx response (code=%d)',response.statusCode);
                
                        error.name = 'OperationError';
                        error.message = response.statusCode + ': ' + response.reasonPhrase;
                        next(error);
                    }
            })
        }
        // 该方法返回一个promise 数组
        function getData(http,options, writeDst, data, datas, logger, Status,compression,timeout){
            return new Promise((resolve,reject) => {
                var request = http.request(options, function(response) {

                    Status.push({
                        url: options.href,
                        code: response.statusCode,
                        reason: response.reasonPhrase
                    });
            
                    writeDst.headers = {};
            
                    //note: there is no response.rawHeaders for node v0.10.43
                    var rhrs = response.rawHeaders || response.headers;
                    for (var j = 0; j < rhrs.length; j += 2) {
                        writeDst.headers[rhrs[j]] = rhrs[j + 1];
                    }
            
                    var chunks = [];
                    response.on('data', function(data) {
                        chunks.push(data);
                    });
            
                    response.on('end', function() {
                        logger.info('[muti-invoke] done');
            
                        //Decide whether the body should be a Buffer or JSON object.
                        //If the content-type says it is a JSON object, try to parse it.
                        var tmp = Buffer.concat(chunks);
                        var cEncode = response.headers['content-encoding']; //ex: gzip
                        var cType = response.headers['content-type'];
                        
                        if (!cEncode) {
                            if (cType === 'application/x-www-form-urlencoded') {
                                tmp = qs.parse(tmp.toString());
                            } else if (cType &&
                                cType.toLowertimeOutCase().indexOf('json') !== -1) {
                                try {
                                tmp = JSON.parse(tmp);
                                } catch (e) {
                                logger.warn('Failed parse the body (%s) as JSON: %s. ' +
                                        'Leave it as a Buffer object', cType, e);
                                }
                            }
                            //TODO: parse XML and check SOAPError when applicable?
                        }
                        // 数据聚合
                        datas.push({
                            status:{
                                url: options.href,
                                code: response.statusCode,
                                statusCode: tmp.statusCode,
                            },
                            data:tmp.msg
                        });
                        resolve()
                    });
                });
                request.setTimeout(timeout * 1000, function() {
                    logger.error('[muti-invoke] The invoke policy is timeouted.');
            
                    error.name = 'ConnectionError';
                    error.message = 'The invoke policy is timeouted.';
                    request.abort();
                    reject(error);
                });
            
                // setup the error callbanextck
                request.on('error', function(err) {
            
                    logger.error('[muti-invoke] request failed: %s', err);
            
                    error.name = 'ConnectionError';
                    error.message = err.toString();
            
                    reject(error);
                });
                if (compression) {
                    zlib.gzip(data, function(err, buffer) {
                        if (err) {
                            logger.error('[muti-invoke] cannot compress the data');
            
                            reject(err);
                            request.abort();
                        } else {
                            request.write(buffer);
                            request.end();
                        }
                    });
                } else {
                    request.write(data);
                    request.end();
                }
            })
        }
        /**
        * The entry point of the invoke policy.
        * Read the TLS profile first and do the real work then.
        */
        function invoke(props, context, flow) {
        var logger = flow.logger;

        //writeDst: first thing, decide where to write the response
        if (context.message === undefined) {
            context.message = {}; //In fact, this should never happen
        }
        var writeDst = context.message;

        //stop on error, default to the ConnectionError only
        var stopOnError = [ 'ConnectionError' ];
        var isDone = false;
        function _next(v) {
            if (!isDone) {
            isDone = true;

            if (v) {
                if (v.name !== 'PropertyError' &&
                        stopOnError.indexOf(v.name) === -1) {
                //ignore the error. Will continue with the next policy
                logger.info('[muti-invoke] ignore the error "%s" and continue', v.name);

                if (v.name === 'ConnectionError') {
                    writeDst.status = {
                    code: 500,
                    reason: 'URL Open error' };
                }

                flow.proceed();
                } else {
                //fail with the error
                if (v.name === 'ConnectionError') {
                    v.status = {
                    code: 500,
                    reason: 'URL Open error' };
                }

                flow.fail(v);
                }
            } else {
                flow.proceed();
            }
            }
        }

        var error = { name: 'PropertyError' };
        if (!props || typeof props !== 'object') {
            error.message = 'Invalid property object';
            _next(error);
            return;
        }

        if (props.output) {
            if (typeof props.output === 'string') {
            logger.info('[muti-invoke] the output destination will be set to %s',
                    props.output);
            var theOut = {};
            context.set(props.output, theOut);
            writeDst = theOut;
            } else {
            logger.error('[muti-invoke] the output property "%s" is not valid.');

            error.message = 'Invalid output: "' + props.output + '"';
            _next(error);
            }
        }

        if (Array.isArray(props['stop-on-error'])) {
            stopOnError = props['stop-on-error'];
        }
        logger.debug('[muti-invoke] stop-on-error is set to', stopOnError);

        var snapshotId = context.get('config-snapshot-id');
        var profile = props['tls-profile'];
        var tlsProfile;
        if (!profile || typeof profile !== 'string' || profile.length === 0) {
            _main(props, context, _next, logger, writeDst);
        } else {
            logger.debug('[muti-invoke] reading the TLS profile "%s"', profile);

            dsc.getTlsProfile(snapshotId, profile).then(
            function(result) {
                if (result !== undefined && Array.isArray(result) && result.length > 0) {
                tlsProfile = result[0];
                }

                if (!tlsProfile) {
                logger.error('[muti-invoke] cannot find the TLS profile "%s"', profile);

                error.message = 'Cannot find the TLS profile "' + profile + '"';
                _next(error);
                return;
                } else {
                _main(props, context, _next, logger, writeDst, tlsProfile);
                }
            },
            function(e) {
                logger.error('[muti-invoke] error w/ retrieving TLS profile: %s', e);

                error.message = e.toString();
                _next(error);
            });
        }
        }

        module.exports = function(config) {
        return invoke;
        };

        /*
        * return query string from url
        */
        function maskQueryStringInURL(url) {
        url = url || '';
        return url.replace(/\?.*?$/, '');
        }

5. 启动网关,在 yaml 文件中的 target-url 赋值为 ["http://localhost:5001/1", "http://localhost:5001/2"] 具体请自行设定

6. 执行 curl http://locakhost:5000/sapmle/echo 返回一个数据数组