---
title: "hugo使用教程"
date: 2021-09-13
draft: false
summary: "hugo使用教程"
---

# hugo使用教程（Mac）

## 1、安装依赖

安装hugo需要使用Homebrew，安装好之后，执行命令

    brew install hugo

安装hugo完毕之后，终端输入hugo version查看hugo版本

    hugo version

## 2、创建新项目

    hugo new site quickstart

## 3、添加主题

    cd quickstart
    
    git init

    git submodule add "https:// 主题地址" themes/主题名称

然后将主题添加到配置中

    echo 'theme = "主题名称"'  >> config.toml

## 4、添加页面

    hugo new posts/my-first-post.md

编辑新创建的文件，这时它是这样显示的

    title: 'My First Post'
    date: '2021-09-13'
    draft: true

## 5、启动hugo服务

    hugo server -D

## 6、自定义主题

打开config.toml

    baseURL = "https://example.org/"
    languageCode = "en-us"
    title = "My New Hugo Site"
    theme = "主题名称"

将上面的title更换为自己的

## 7、构建页面（打包）

    hugo -D