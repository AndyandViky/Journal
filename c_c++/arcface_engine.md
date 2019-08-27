# linux下软虹人脸引擎学习笔记一
## 跑通软虹人脸检测

1. 官网下载开发包, 获取相关id key

2. 解压 __arcface_detection__ 获取源码

3. > 目录结构如下

        |----doc
        |    |----ARCSOFT_FACEDETECTION_DEVELOPER'S_GUIDE.pdf 开发文档
        |    |----releasenotes.txt 版本介绍以及一些相关笔记
        |----inc 外层接口文件
        |    |----arcsoft_fsdk_face_detection.h									
        |    |----amcomdef.h																	
        |    |----asvloffscreen.h							
        |    |----merror.h								
        |----lib 编译好的源码文件
        |    |----linux_x64                  
        |    |    |-libarcsoft_fsdk_face_detection.so								
        |----samplecode c++代码事例
        |    |----arcsoft_afd_samplecode.cpp

4. linux下编译c++ `g++ -o main main.cpp -larcsoft_fsdk_face_detection -std=c++11 -O2`

        相关参数:
            -O main 指定编译完成后的文件名
            -lxxx 指定公共资源库.so文件名 
            -Lxxx 指定资源库所在路径, 如果不填写按默认路径读取
            -std=c++11 指定支持c++11 g++ 版本在 __4.8.1__ 及以上完全支持 c++11
            -o2 增加编译时间和生成的代码的性能
            -Ixxx 指定.h文件所在路径  例: -I/usr/local/lib/h
        ps: 默认资源库路径一般为 /usr/local/lib 下
        ps: 修改(添加)默认路径  cd /etc/ld.so.conf.d/libc.conf
        ps: 更新修改(ld.so.conf.d)过后的配置  /sbin/ldconfig

ps: 切记使用正确的appId以及key, 图片格式为yuv
        