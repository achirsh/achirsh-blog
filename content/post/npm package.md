---
title: "发布npm包"
date: 2021-09-13
draft: false
summary: "发布npm包"
---

# 发布npm包

## 1、注册账号

    - 进入网址：https://www.npmjs.com 注册一个npm账号
    - 进入邮箱验证nom账号(否则发布会报错)

## 2、床架一个简单的npm包

    - 在本地创建一个文件夹，如demo（注意名字应该是npm上没有的，可以在npm上搜索一下看你命名的名字是否存在）
    - cd进入该文件夹
    - 初始化项目，执行npm init -y得到package.json
    - 在文件夹demo中创建名为index.js的文件
    - 创建README.md文件

package.json

    {
        "name": "demo",      // 发布的报名，默认是上级文件名。不得与现在npm中的包名重复。报名不能有大写字母/空格/下滑线
        "version": "1.0.0",  // 默认版本号
        "description": "",   // 项目简介
        "main": "index.js",  // 入口文件，默认是index.js，可以修改成自己的文件
        "scripts": {         // 包含各种脚本执行命令
            "test": "echo \"Error: no test specified"\ && exit 1"
        },
        "keywords": [],
        "author": "",        // 作者
        "license": "ISC"     // 开源文件协议
    }

## 3、发布到npm

### 3.1、切换下载源 
    
    安装 npm install -g nrm
    nrm ls        // 查看当前的下载源，带*指向的就是当前的下载源，如果为npm则不需要切换
    nrm use npm   // 切换到npm的下载源

### 3.2、连接npm账户

    如果是本机第一次发布npm包：
    在终端中运行命令：npm addUser 提示输入账号，密码和邮箱，然后将提示创建成功

    非在第一次发布npm包：
    在终端输入npm login，然后输入你创建的账号和密码、邮箱，登陆

### 3.3、发布npm包

    npm publish

### 3.4、更新npm发布后的包

    更新npm包也是使用npm publish命令发布，不过必须更改npm包的版本，也就是package.json中的version字段，否则会报错

### 3.5、撤销命令（一般是慎重撤销）

    npm unpublic 包名@版本号 --force
    eg：npm unpublic demo@1.0.0 --force

说明：撤销发布的包被认为是一种不好的行为，因为如果有团队使用和依赖你的包时，撤销会造成跟大的影响

    - 根据规范，只有在发包的24小时内才允许撤销发布的包
    - 即使你撤销了发布的包，发包的时候也不能再和被撤销的包的名称和版本重复了（即不能名称相同，版本相同，因为这两者构成的唯一标识已经被”占用“了）

## 4、可能遇到的错误

### 4.1、需要提高版本号

    sh-neverleave:z-tool neverleave$ npm publish
    npm ERR! publish Failed PUT 400
    npm ERR! code E400
    npm ERR! deprecations must be strings : z-tool

    npm ERR! A complete log of this run can be found in:
    npm ERR!     /Users/neverleave/.npm/_logs/2018-11-23T10_52_01_742Z-debug.log
    sh-neverleave:z-tool neverleave$ npm publish


    sh-neverleave:z-tool neverleave$ npm publish
    npm ERR! publish Failed PUT 403
    npm ERR! code E403
    npm ERR! You cannot publish over the previously published versions: 1.0.3. : z-tool

    npm ERR! A complete log of this run can be found in:
    npm ERR!     /Users/neverleave/.npm/_logs/2018-11-23T11_24_57_662Z-debug.log
    sh-neverleave:z-tool neverleave$ 

解决方案：npm public --access public

