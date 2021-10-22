---
title: "前端模块化iife，CJS，AMD，UMD，ESM的区别"
date: 2021-10-06
draft: false
summary: "前端模块化iife，CJS，AMD，UMD，ESM的区别"
---
# 前端模块化iife，CJS，AMD，UMD，ESM的区别

## CommonJs/CJS

    CommonJS是一种规范。

### 概念

针对环境：web浏览器外的（非web浏览器环境的）javacript项目
项目目标：javascript生态的模块化解决方案
主要示例：尤其是使用nodejs用于服务端javascript编程
浏览器：浏览器不能直接执行commonjs代码，需要通过编译转化
如何识别：我们可以通过是否使用require()和module.exports来识别是否使用了commonjs

    CommonJS并没有称为ECMA组织发布的模块化标准(ES Module)，但又很有ECMA成员参与其中。

### 特点

    1、所有代码都运行在模块作用域中，不会污染全局变量
    2、模块按照在代码中的顺序，一次同步加载
    3、模块会在运行时且执行，执行得到对象A，后续通过require获取的都是对对象A值的拷贝（换句话说，模块可以多次加载，在第一次加载时执行并缓存其结果，后续加载会直接返回该结果），要想模块再次执行，必须清除缓存。

### NodeJS的模块化

    1、在执行模块代码之前，NodeJS会使用如下的函数封装器将其封装
    
    通过闭包的形式避免了变量污染；
    提供了看似全局，实际上是模块特定的变量；

    (function(exports, require, module, __filename, __dirname) {
        // 模块的代码实际上在这里
    })

    2、可以通过module.exports导出模块内容
    3、变量exports是对module.exports的引用，所以不能对exports有赋值操作

    exports = module.exports;
    exports变量是在模块的文件级作用域内可用的，且在模块执行之前赋值给module.exports
    因此module.exports.f=...可以间接地携程exports.f=...

    // 错误用法，exports被重复赋值，此function并未被导出
    exports = function(x) {console.log(x)}

    // 正确用法
    exports.a = function(x) {console.log(x)}

    // 如果一个模块的对外接口，就是一个单一的值，最好不要使用exports输出，最好使用module.exports输出

    4、通过require(id)引入模块、JSON、或本地文件

    5、require.cache被引入的模块将被缓存到这个对象中，如果删除该对象的某个模块会导致下次require的时候重新加载该模块。

## AMD（Asynchronous Module Definition）

    javacript的异步模块化定义方案

### 概念

    针对环境：web浏览器
    项目目标：javascript生态的模块化解决方案
    主要示例：require.js
    如何识别：我们可以通过是否使用define(id?, dependencies?, factory)来识别是否使用了AMD规范

    // 其中对于require, exports, beta这几个依赖可不填
    define('alpha', ['require', 'exports', 'beta'], function(require, exports, beta) {
        exports.verb = function() {
            return beta.verb()
            // or
            return require('beta').verb()
        }
    })

### 特点

    1、所有代码都运行在块作用域中，不会污染全局变量
    2、模块会被异步加载
    3、依赖模块加载完成后，会立即执行其回调函数（即factory函数）
    4、主模块会等所有的依赖模块加载完成后，再调用其对应的回调函数（依赖前置）

### require.js的模块加载原理

#### 简单使用

首先简单介绍一下require.js的使用：

    1、在html文件内，需要有一个script标签引入require.js以及项目的入口文件main.js
    2、文件main.js里的就是项目的主要逻辑了

#### 原理介绍

    不过RequireJS从2.0开始，也改成了可以延迟执行

目的：

    理解require使用script标签
    关于script标签是否加载成功可以通过onload事件来判断

## UMD（Universal Module Definition）

    UMD提供了支持多种风格的“通用”模式，在兼容CommonJS和AMD规范的同时，还兼容全局引用的方式。

    (function (root, factory) {
        if (typeof define === "function" && define.amd) {
            define(["jquery", "underscore"], factory);
        } else if (typeof exports === "object") {
            module.exports = factory(require("jquery"), require("underscore"));
        } else {
            root.Requester = factory(root.$, root._);
        }
    }(this, function ($, _) {
        // this is where I defined my module implementation

        var Requester = { // ... };

        return Requester;
    }));

### 原理

    实现原理很简单

    1、判断是否支持AMD，若存在则使用AMD方式加载模块，否则继续步骤2
    2、判断是否支持commonjs，若存在则使用node.js的模块格式，否则继续步骤3
    3、将模块公开到全局(window或global)


## ESM

    ES Module是用于处理模块的ECMAScript标准。现代浏览器（高版本）以基本支持ES Module。

    <script type="module" src="index.js"></script>

### 特点

    1、所有代码都运行在模块作用域中，不会污染全局变量
    2、在编译时输出模块
    3、输出的模块内容为只读，不可修改
    4、不会缓存模块结果，每次都会动态执行模块内容

### ES6的import & export

    1、import语句会被提升
    2、import的变量都是只读的
    3、import是静态执行，所以不用表达式
    4、import语句支持singleton模式（如果多次重复执行同一句import语句，那么只会执行一次，而不会执行多次）
    5、export需要输出一个对象或变量声明语句
    6、export default相当于输出了一个名叫default的变量/对象
    7、export与import连用


## 该怎么实现？

### 只支持NodeJS的require写法

    package.json: 
        main: 'index.js'
    其中index.js使用cjs写法(module.exports = xxx)

### 只支持NodeJS的import写法

    package.json: 
        main: 'index.mjs' 或 type: 'module', main: 'index.js'
    其中index.mjs或index.js使用esm写法(export default xxx)

### 同时支持NodeJS的require和import



