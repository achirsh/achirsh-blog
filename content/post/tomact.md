---
title: "tomact mac安装使用"
date: 2021-09-13
draft: false
summary: "tomact mac安装使用"
---

# tomact mac安装使用

## 启动tomact

进入到下载的文件目录下，进入bin目录，输入./startup.sh，如果提示Permissiondenied是因为缺少权限，这时需要赋予超级管理员权限

    sudo chmod 755 *.sh

然后再次，就可以启动了


## 关闭tomact

同样是在bin目录下，在终端输入./shutdown.sh

## tomact的目录结构及作用

    bin: 存放tomact的命令
         catalina.bat命令
         startup.bat -> catalina.bat start
         shutdown.bat -> catalina.bat stop

    conf: 存放tomact的配置信息。其中server.xml文件是核心的配置文件

    lib: 支持tomact软件运行的jar包。其中还有技术支持包，如servlet，jsp

    logs: 运行过程的日志信息

    temp: 临时目录

    webapps: 共享资源目录。jsp运行时产生的临时文件就存放在这里

    work: tomcat的运行目录。jsp运行时产生的临时文件就存放在这里

    WebRoot: web应用的跟目录

    静态资源: (html+css+js+image+vedio)

    WEB-INF: 固定写法

    classes: (可选)固定写法。存放class字节码文件

    lib: (可选)固定写法。存放jar包文件

    web.xml
        注意：
        1、WEB-INF目录里面的资源不能通过浏览器直接访问
        2、如果希望访问到WEB-INF里面的资源，就必须把资源配置到一个叫web.xml的文件中

## 注意事项

如果服务器启动后信息提示Tomact started，但是在浏览器中访问后提示“无法连接至服务器”，则有可能是因为tomact使用的jdk版本过低，不符合tomact 9.0对jdk的最低要求，解决方法如下：

    1、去官网下载最新的jdk
    2、在Mac上根据提示安装jdk
    3、在控制台输入命令/usr/libexec/java_home得到目前mac中jdk的位置
    4、cd至～/目录下，执行vim .bash_profile，打开该文件
    5、加入或者修改:export JAVA_HOME="你的jdk位置"
    6、保存退出，在控制台输入source .bash_profile
    7、重启启动tomact。登陆浏览器输入网址后应该会显示正确的提示

## 配置java web服务器

    运行的话把代码放在webapps目录下，然后在终端执行
    sudo sh shutdown.sh关闭服务器，然后再输入
    sudo sh startuo.sh打开服务器，表示服务器重启(会自动导入这个web)