# weex转android踩坑之旅（一）

1. 编译代码
    * weex platform add android
    * weex build android

          // 坑
          // 在build时报错，未找到ANDROID_HOME
          // 原因： 在~/.bash_profile 里将ANDROID_HOME暴露出来
          export ANDROID_HOME=/Users/yanglin/Library/Android/sdk // 个人路径不同，自行寻找sdk位置
          export PATH=$PATH:$ANDROID_HOME/tools:$ANDROID_HOME/platform-tools
2. 使用AS载入项目
    * 修改build.gradle里的参数与当前环境配置的一样，具体配置可新建android项目查看
    * 同时gradle文件替换
    * 运行项目
  
          // 坑
          /* 在AS 3.1.4中出现 
              1. Cannot set the value of read-only property ‘outputFile’ for ApkVariantOutputImpl_Decorated 
              2. Absolute path are not supported when setting an output file name
          */

          // 这是原本的代码
          applicationVariants.all { variant ->
              variant.outputs.each { output ->
                  def outputFile = output.outputFile
                  if (outputFile != null && outputFile.name.equals('app-debug.apk')) {
                      def fileName = outputFile.name.replace("app-debug.apk", "weex-app.apk")
                      output.outputFile = new File(outputFile.parent, fileName)
                  }
              }
          }

          // 修改后的代码
          applicationVariants.all { variant ->
              variant.outputs.all { output ->  // 此处的each 变为 all
                  def outputFile = output.outputFile
                  if (outputFile != null && outputFile.name.equals('app-debug.apk')) {
                      def fileName = outputFile.name.replace("app-debug.apk", "weex-app.apk")
                      outputFileName = new File("../../../release/", fileName)
                      // 此处变为相对路径 以及 output.outputFile 变为 outputFileName
                  }
              }
          }

          // 再次运行后出现
          // Annotation processors must be explicitly declared now
          // 原因：版本问题
          // 在build.gradle中的defaultConfig中添加
          javaCompileOptions { annotationProcessorOptions { includeCompileClasspath = true } }

3. 成功在真机上运行
    * 但是又出现 weex render error: -2013

          // 巨坑
          // 原因： js包有问题
          // 突然发现使用weex create 创建的项目引入vuex导致安卓报错
          // Exception: TypeError: undefined is not an object (evaluating 'this.$store.getters')
          // 求助大牛给我解答一下。。。

# 彻底弃坑，没有任何办法！！！！