# python virtualenv 使用

1.  默认已经下载pip 否则自行下载`sudo apt-get install python3-pip` or `sudo apt-get install python2-pip`
2.  下载 virtualenv `pip3 install virtualenv` 
3.  创建 env  

		1. `mkdir env_test`, 
		2.  `cd env_test`, 
		3. `virtualenv venv --no-site-package` // 参数代表创    建的虚拟环境不受外界包的影响


4.  创建不同版本下的虚拟环境
	> `virtualenv -p python3 venv --no-site-package `
 `virtualenv -p python2 venv --no-site-package `
 `virtualenv -p python3.4 venv --no-site-package `
 以上是三种不同版本的创建

5.  运行虚拟环境
	> `cd venv` 
	> `source ./bin/activate`
	>  此时已经进入虚拟环境
(venv)...:~/yl/python/python_reptile/text/venv$ 
	> 退出： `deactivate`
		