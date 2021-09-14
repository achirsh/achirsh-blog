---
title: "husky + lint-staged + prettier + eslint"
date: 2021-09-07
draft: false
summary: "husky + lint-staged + prettier + eslint"
---

# 使用husky、lint-staged、prettier、eslint做代码提交验证

参考
https://www.jianshu.com/p/0964bd8797d7
https://blog.csdn.net/qq_34707272/article/details/111037034
https://blog.csdn.net/flitrue/article/details/106328972

在软件开发过程中，代码风格检查(Code Linting)是保障代码规范和一致性的有效手段。过去，Lint的工作一般在Code Review或者CI的时候进行，但这样会导致问题的反馈链，浪费不必要的时间。因此，我们需要利用Git的Pre Commit钩子，将Lint过程放到开发者提交代码之前。

## 简介
简单介绍一下这四个工具：

- prettier 用来优化代码格式，比如缩进、空格、分号等等
- husky 可以用于实现各种Git Hook。这里主要用到pre-commit这个hook，在执行commit之前，运行一些自定义操作
- lint-staged 用于对Git暂存区中的文件执行代码检测。
- eslint


## 安装
首先，我们使用下面的命令把husky和lint-staged安装到package.json的devDependencies中：

    npm install husky lint-staged prettier --save-dev
    或者
    yarn add husky lint-staged prettier --dev
    
## 配置package.json
将下面的代码追加到package.json文件中：

    {
        "lint-staged": {
            "src/**/*.js": [
              "eslint --ext .js",
              "prettier --write"
            ]
        }
    }
    
    
## 配置Eslint
在项目根目录下新建.eslintrc.js

    module.exports = {
        root: true,
        env: {
            browser: true,
            node: true,
            es6: true
        }
    }

## 配置prettier
在项目根目录下新建.prettierrc

    {
        // 行宽 default:80
        printWidth: 100,
        // tab 宽度 default:2
        tabWidth: 4,
        // 使用 tab 键 default:false
        useTabs: false,
        // 语句行末是否添加分号 default:true
        semi: false,
        // 是否使用单引号 default:false
        singleQuote: true,
        // 对象需要引号在加 default:"as-needed"
        quoteProps: 'as-needed',
        // jsx单引号 default:false
        jsxSingleQuote: true,
        // 最后一个对象元素加逗号 default:"es5"
        trailingComma: 'es5',
        // 在对象字面量声明所使用的的花括号后（{）和前（}）输出空格 default:true
        bracketSpacing: true,
        // 将 > 多行 JSX 元素放在最后一行的末尾，而不是单独放在下一行（不适用于自闭元素）。default:false
        jsxBracketSameLine: false,
        // (x) => {} 是否要有小括号 default:"always"
        arrowParens: 'always',
        // default:0
        rangeStart: 0,
        // default:Infinity
        rangeEnd: Infinity,
        // default:false
        insertPragma: false,
        // default:false
        requirePragma: false,
        // 不包装 markdown text default:"preserve"
        proseWrap: 'never',
        // HTML空白敏感性 default:"css"
        htmlWhitespaceSensitivity: 'strict',
        // 在 *.vue 文件中 Script 和 Style 标签内的代码是否缩进 default:false
        vueIndentScriptAndStyle: true,
        // 末尾换行符 default:"lf"
        endOfLine: 'auto',
        // default:"auto"
        embeddedLanguageFormatting: 'auto',
        overrides: [
            {
                files: '*.md',
                options: {
                    tabWidth: 2,
                },
            },
        ],
    }