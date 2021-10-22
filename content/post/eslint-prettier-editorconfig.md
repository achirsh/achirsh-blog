---
title: "代码规范之ESLint、Prettier、EditorConfig"
date: 2021-10-14
draft: false
summary: "代码规范之ESLint、Prettier、EditorConfig"
---

# 代码规范之ESLint、Prettier、EditorConfig

## 简单归纳

    EditorConfig：跨编辑器和IDE编写代码，保持一致的简单编码风格

    Prettier：专注于代码格式化的工具，美化代码

    ESLint：代码质量检测、编码风格约束等

## EditorConfig

EditorConfig有助于从事同一项目的多个开发人员在跨多个编辑器和IDE使用时保持一致的编码风格。
        
    EditorConfig项目包含一个用于定义编码样式的文件格式和一个文件编辑器插件集合，这些文件编辑器插件使用编辑器可以读取文件格式并遵循定义的样式。

### 解读

#### 依赖编辑器IDE的支持

某些编辑器已默认集成对EditorConfig的支持，比如常见的：Webstorm、IntelliJ IDEA等
而另一些编辑器则需要借助安装对应的插件来支持：比如Visual Studio Code、Atom等

使用Visual Studio Code安装的必装插件：EditorConfig for VS Code

#### 支持多种文件格式

编辑器读取到文件格式会匹配并遵循配置文件定义的规则

#### 就近原则

打开文件时，EditorConfig插件会在打开的文件的目录中以及每个父目录查找名为.editorconfig的文件。如果到达根文件路径或找到root=true的EditorConfig文件，将停止对.editorconfig文件的搜索。离文件最近的配置规则生效，优先级更高，一般在根目录设置一个配置文件即可。

#### 配置文件.editorconfig

定义规则配置，来避免常见的代码格式不一致和丑陋的diffs。例如常见配置项：

    # http://editorconfig.org
    root = true

    # 说明
    ## 设置文件编码为UTF-8
    ## 用两个空格代替制表符
    ## 在保存时删除尾部的空白字符
    ## 在文件结尾添加一个空白行
    [*]
    indent_style = space
    indent_size = 2
    end_of_line = lf
    charset = utf-8
    trim_trailing_whitespace = true
    insert_final_newline = true

    [*.md]
    trim_trailing_whitespace = false

    [Makefile]      
    indent_style = tab

## Prettier

Prettier是一个诞生于2016年就迅速流行起来的专注于代码格式化的工具
Prettier只关注格式化，并不具有lint检查语法等能力。它通过解析代码并匹配自己的一套规则，来强制执行一致的代码展示格式。
它在美化代码方面有很大的优势，配合ESLint可以对ESLint格式化基础上做一个很好的补充。

### 那么如何使用呢？

    1、单独使用，配合编辑器IDE代码格式化
    2、与ESlint等配合使用

#### 单独使用，配合编辑器IDE作代码格式化

以VScode为例，首先安装Prettier插件

VSCode内置的代码格式化工具可以指定为由prettier接管，此时右下角会显示为Prettier。可以自行配置格式化触发机制：换行时格式化、保存文件时格式化、还是自行快捷键触发

在项目根目录设置.prettierrc单独配置：

    {
        // 设置强制单引号
        "singleQuote": true,
        // 为多行数组的非末尾行添加逗号 es5的对象，数组等
        "trailingComma": "es5",
        // 每行最大宽度
        "printWidth": 100,
        // 设置语句末尾不加分号
        "semi": false,
        // jsx中使用单引号
        "jsxSingleQuote": true
    }

最后格式化的生效策略同样是就近原则，一步步匹配目标文件最近父目录的配置文件，越近优先级越高。

## ESLint

ESLint是一个在javacript代码中通过规则模式匹配作代码识别和报告的插件化的检测工具，它的目的是保证代码规范的一致性和及时发现代码问题、提前避免错误发生。
ESLint的关注点是代码质量，检查代码风格并且会提示不合符风格规范的代码。除此之外ESLint也具有一部分代码格式化的功能。

### 作用及优势

    检查语法错误，避免低级bug

    统一团队代码风格

    确保代码遵循最佳实践

这样就能极大提高项目中多人协作开发时的效率、代码的可读性以及可维护性

### ESLint特点

一、ESLint的所有规则都被设计成可插拔的

每条校验规则都是独立的，可以单独开启或关闭，还可以将结果设置成警告或者错误
在规则编写时，每个规则都是单独的文件和对应的格式化方法

二、ESLint是完全可配置的

ESLint被设计为完全可配置的，除了规则可拔插，还可以编写自定义规则、引入社区规则配置集、插件等，让ESLint更契合每个项目的具体需求情况
通过eslint-plugin-react配置包扩展支持React语法
通过@typescript-eslint/parser解析器支持typescript语法及检验等

三、ESLint使用Node.js编写

在前端项目中便于安装且有一个快速的运行环境
减轻了开发者编写自定义规则的门槛

四、ESlint解析时将源码先转换成AST

ESLint使用户Esprima将源代码解析成AST来分析代码中的模式，再通过匹配规则定义识别和报告搜集的代码信息。
虽然多转换一层效率略微降低，好处是可以支持使用任意规则来检测AST是否符合预期，这使得ESLint高可扩展性

### 支持的配置文件格式

ESLint支持几种格式的配置文件：

    javascript：使用.eslintrc.js然后输出一个配置对象
    YAML：使用.eslintrc.yaml或.eslintrc.yml去定义配置的结构
    JSON：使用.eslintrc.json去定义配置的结构，ESLint的JSON文件允许javascript风格的注释
    package.json：在package.json里创建一个eslintConfig属性，在那里定义你的配置

如果同一个目录下有多个配置文件，ESLint 只会使用一个。优先级顺序如下：

    .eslintrc.js

    .eslintrc.yaml

    .eslintrc.yml

    .eslintrc.json

    .eslintrc

    package.json

### 配置文件说明

#### Rules-启用的规则及其各自的错误级别

ESLint附带大量的规则。你可以使用注释或配置文件修改你项目中要使用的规则。要改变一个规则设置，你必须将规则ID设置为下列值之一：

    off 或 0 - 关闭规则
    warn 或 1 - 开启规则，使用警告级别额错误：warn（不会导致程序退出）
    error 或 2 - 开启规则，使用错误级别的错误：error（当被触发的时候，程序会退出）、

#### Globals-配置额外的全局变量

启用ESLint规则后，当访问当前文件内未定义的变量时，no-undef规则将发出警告。
而有时候，我们是需要在其他文件访问一些全局变量的，且保证能正常取到值。这时可以在ESlint中定义这些全局变量，这样ESlint就不会发出警告了

    1、用注释指定全局变量，格式如下：

    /* global var1, var2 */


这定义了两个全局变量，var1和var2。如果你想选择性地指定这些全局变量可以被写入（而不是只被读取），那么你可以用一个"writable"的标志来设置它们：

    /* global var1:writable, var2:writable */

    2、配置文件中通过globals配置属性设置，对于每个全局变量键，将对应的值设置为"writable"以允许重写变量，或"readonly"不允许重写变量。例如：

    "globals": {
        "var1": "writable",
        "var2": "readonly"
    }

#### Environments - 指定脚本的运行环境

每种环境都有一组特定的预定义全局变量。如brower、node环境变量、es6环境变量等

    "env": {
        "browser": true,
        "commonjs": true,
        "es6": true
    }

#### Plugins - 第三方插件

ESLint支持使用第三方插件，先在项目中下载安装要引入的插件，配置文件中使用plugins关键字来存放插件名字的列表。插件名称可以省略eslint-plugin-前缀

    plugins: ['react', 'babel']   // eslint-plugin-react eslint-plugin-babel

#### Extends - 继承

一个配置文件可以被基础配置中一起用的规则继承

    extends: ['eslint:recommended', 'plugin:prettier/recommended']

#### 配置代码注释方式

有时候，我们需要在代码中忽略ESLint的某些规则检查,此时我们可以通过加入代码注释的方式解决:可以指定整个文件、某一行、某一区块开启/关闭某些或全部规则检查

    /* eslint-disable */         -- 禁用全部规则，放在文件顶部则整个文件范围都不检查
    /* eslint-disbale no-alert, no-console */   -- 禁用某些规则
    // eslint-disable-line       -- 当前行上禁用规则
    // eslint-disable-next-line  -- 下一行上禁用规则

### 使用ESLint

#### 安装ESLint

ESLint可以安装在当前项目中或全局环境中，但因项目存在的差异性，我们一般会将将它安装在当前项目中。

    yarn add eslint --save-dev

#### 安装插件和解析器

假如项目中使用了Typescript和React，则安装：

    // 我们需要安装@typescript-eslint/parser，替代默认的Espree解析器
    yarn add @typescript-eslint/parser --save-dev

    // 安装eslint-plugin-react配置包扩展支持React语法，安装@typescript-eslint/eslint-plugin提供额外的ts语法的规则
    yarn add eslint-plugin-react @typescript-eslint/eslint-plugin --save-dev

其他的插件和解析器请根据实际项目需要安装

#### 创建配置文件

我们在项目的根目录下创建一个.eslintrc.js，内容如下：

    module.exports = {
        parser: "@typescript-eslint/parser",
        plugins: ['react', '@typescript-eslint'],
        rules: {
            // 禁止使用var
            "no-var": "error",
            // 优先使用interface而不是type
            "@typescript-eslint/consistent-type-definitions": [
                "error",
                "interface"
            ]   
        }
    }

### 注意事项

#### 1、如何方便地开始使用ESLint，而且尽量不改动以前的代码？

如果你正在使用Git做项目代码管理，那么则可以借助husky + lint-staged + Prettier在Git提交时，自动强制校验并格式化且修复代码，而且只处理自己本次改动提交的文件。

采用这种pre-commit阶段增量校验的模式，尽量避免对老旧代码的影响，这种方式可以稳健地逐步完善老项目

#### 2、如果解决Prettier与ESLint的配置冲突问题

在代码格式化时采用Prettier规则，而我们代码校验使用的是ESLint，如果同一个规则配置不一致，往往就会出现冲突问题

    比如：字符串单、双引号的配置，eslint fix后把字符串变成单引号，再次编辑文件后，保存（Prettier）自动格式化后却又变成双引号，导致代码校验异常

解决方式一：要么修改eslintrc，要么修改prettier配置，让它们配置保持一致

解决方式二：禁用ESLint中和Prettier配置有冲突的规则，再使用Prettier来替代ESLint的格式化功能
安装eslint-config-prettier插件配置集，把其配置到eslintrc规则的尾部。执行ESLint命令，会禁用那些和Prettier配置有冲突的规则
安装eslint-plugin-prettier插件，先使用Prettier对代码进行格式化，再并对不一致的地方进行标记
这两个包配合使用，可以达到运行eslint \--fix时，采用Prettier的配置规则来格式化文件