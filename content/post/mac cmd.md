---
title: "mac常用命令行"
date: 2021-09-14
draft: false
summary: "mac常用命令行"
---

# Mac常用命令行

    cd ~      	进入家目录
    cd ..         返回上级目录
    ls              查看当前目录下的文件
    ls -l          查看当前目录下文件的更多内容
    ls -a         查看当前目录下的文件(包括隐藏文件)
    ls -la
    mv a b     把a文件移动到b(把a文件重命名为b文件)
    cp a b      把a文件复制为b文件
    cp -a a b  把a文件复制为b文件(保留文件所有的权限属性)
    cp -r a b   把a文件夹复制为b文件
    cp -ra a b 把a文件夹复制为b文件(保留文件夹的所有的权限属性，可以看实时进度，用-rav)

    rm a          删除a文件(直接删除文件，不进入回收站)

    touch a b  创建a和b文件
    mkdir a b  创建a和b文件夹
    mkdir -p res/style/scss     创建多层级的目录

    less a        查看a文件
    pwd           查看当前的目录路径