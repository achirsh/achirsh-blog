---
title: "Tailwind"
date: 2021-09-13
draft: false
summary: "Tailwind"
---

# Tailwind

因为Tailwind是一个构建定制用户界面的框架，所有从开始设计时便考虑了定制。

默认情况下，Tailwind将在项目的根目录中查找一个可选的tailwind.config.js的文件，你可以在其中定义任何自定义选项

## tailwind.config.js 示例文件

    const colors = require('tailwindcss/colors')

    module.exports = {
        theme: {
            colors: {
                gray: colors.coolGray,
                blue: colors.lightBlue,
                red: colors.rose,
                pink: colors.fuchsia,
            },
            fontFamily: {
                sans: ['Graphik', 'sans-serif'],
                serif: ['Merriweather', 'serif'],
            },
            extend: {
                spacing: {
                    '128': '32rem',
                    '144': '36rem',
                },
                borderRadius: {
                    '4xl': '2rem',
                }
            }
        },
        variants: {
            extend: {
                borderColor: ['focus-visible'],
                opacity: ['disabled'],
            }
        }
    }

配置文件的每个部分都是可选的，因此你只需要指定要更改的内容即可。任何缺少的部分将会使用Tailwind的[默认配置](https://github.com/tailwindlabs/tailwindcss/blob/master/stubs/defaultConfig.stub.js)

## 创建配置文件

使用Tailwind CLI功能生成Tailwind配置文件，Tailwind CLI在你安装tailwind npm软件包时已经附带安装过了

    npx tailwindcss init

这将在项目的根目录下创建一个最小文件tailwind.config.js

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

## 使用其他文件名

要使用tailwind,config.js之外的文件名，请在命令行中将其作为参数传人：

    npx tailwindcss init tailwindcss-config.js

如果使用自定义文件名，则在PostCSS配置中将Tailwind做为插件引入时，也需要指定它：

    module.exports = {
        plugins: {
            tailwindcss: {
                config: './tailwindcss-config.js'
            }
        }
    }

## 创建PostCSS配置文件

如果你想在生成tailwind.config.js文件的同时生成一个基础的postcss.config.js，请使用-p标志。

    npx tailwindcss init -p

这将在你的项目中生成一个postcss.config.js文件，如下所示：

    module.exports = {
        plugins: {
            tailwindcss: {},
            autoprefixer: {},
        },
    }

## 生成全部默认配置

对于大多数用户，建议尽量减少配置文件，只指定你想自定义的内容。

如果你希望构建一个完整的配置文件，其中包括Tailwind的所有默认配置，请使用以下--full选项：

    npx tailwindcss init --full

你将得到一个与Tailwind内部使用的[默认配置文件](https://unpkg.com/browse/tailwindcss@latest/stubs/defaultConfig.stub.js)

## 配置

### 主题配置

在tailwind.config.js文件的theme部分，你可以定义你项目的调色板、类型比例、字体、断点、边框半径等。

    const colors = require('tailwindcss/colors')

    module.exports = {
        theme: {
            screens: {
                sm: '480px',
                md: '768px',
                lg: '976px',
                xl: '1440px',
            },
            colors: {
                gray: colors.coolGray,
                blue: colors.lightBlue,
                red: colors.rose,
                pink: colors.fuchsia,
            },
            fontFamily: {
                sans: ['Graphik', 'sans-serif'],
                serif: ['Merriweather', 'serif'],
            },
            extend: {
                spacing: {
                    '128': '32rem',
                    '144': '36rem',
                },
                borderRadius: {
                    '4xl': '2rem',
                }
            }
        }
    }