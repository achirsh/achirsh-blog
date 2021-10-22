---
title: "next项目搭建"
date: 2021-09-13
draft: false
summary: "next项目搭建"
---

# next项目搭建

## 创建项目

    npx create-next-app my-project --typescript

## Tailwind CSS

### 安装Tailwind CSS

    # If you're on Next.js v10
    npm install tailwindcss@latest postcss@latest autoprefixer@latest

    # If you're on Next.js v9 或者更旧版本
    npm install tailwindcss@npm:@tailwindcss/postcss7-compat @tailwindcss/postcss7-compat postcss@^7 autoprefixer@^9

### 创建Tailwind CSS配置文件

接下来，生成您的tailwind.config.js和postcss.config.js文件：

    npx tailwindcss init -p

这将会在你的项目根目录创建一个最小化的tailwind.config.js文件：

    module.exports = {
        purge: [],
        darkMode: false, // or 'media' or 'class'
        theme: {
            extend: {},
        },
        variants: {
            extend: {},
        },
        plugins: [],
    }

这也将会创建一个包含已配置好的tailwindcss和autoprefixer的postcss.config.js配置文件：

    module.exports = {
        plugins: {
            tailwindcss: {},
            autoprefixer: {},
        },
    }

如果您计划使用其它的 PostCSS 插件，请阅读我们的将 PostCSS 作为您的 preprocessor 的文档。

## 配置Tailwind来移除生产环境下没有使用到的样式声明

在你的tailwind.config.js文件中，配置purge选项指定所有的pages和components文件，使得Tailwind可以在生产构建中对未使用的样式进行摇树优化。

    module.exports = {
        purge: ['./pages/**/*.js', './components/**/*.js'],
        darkMode false,
        theme: {
            extend: {}
        },
        wariants: {
            extend: {}
        },
        plugins: []
    }

请阅读我们单独的[优化生产指南](https://www.tailwindcss.cn/docs/optimizing-for-production)，以了解更多关于摇树优化未使用的样式来获得最佳性能的信息。

## 在CSS中假如Tailwind

在Next.js项目的CSS中加入Tailwind时，有两种方法可以选择：

### 在JS中直接导入Tailwind

如果你不打算在你的项目中编写任何自定义CSS，最快的方法是直接在pages/_app.js中导入Tailwind。

    import './styles/globals.css'
    import 'tailwindcss/tailwind.css'

    function MyApp({ Component, pageProps }) {
        return <Component {...pageProps} />
    }

    export default MyApp

如果你不打算使用它们，你可以安全地删除Next.js默认为你创建的任何CSS文件，如global.css和Home.module.css。区人保在你的组件中也删除对它们的任何引用。

### 在CSS中引入Tailwind

打开Next.js默认生成的./style/global.css并使用@tailwind指令来包含Tailwind的base、components和utilities来替换原来的文件内容

    /* ./styles/global.css */
    @tailwind base;
    @tailwind components;
    @tailwind utilities;

Tailwind会在构建时将这些指令转换成所有基于你配置的设计系统生成的样式文件。