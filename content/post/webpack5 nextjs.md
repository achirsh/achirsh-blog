---
title: "在NextJS中使用Webpack5的微前端"
date: 2021-09-14
draft: false
summary: "在NextJS中使用Webpack5的微前端"
---

# 在NextJS中使用Webpack 5的微前端

Webpack5的联合模块使用使创建微前端变得简单。它可以通过实验性功能标志在NextJS 10+中使用，它使集成微前端变得轻而易举。你首先构建一个外部的微前端，并将其作为**Module federated**部署到你选择的目的地。然后你在你的NextJS应用程序中更新一些配置，剩下的由Webpack完成。这有点神奇🦄。

我不会深入研究它们是什么（这个话题非常广泛，而且有更多合格的资料），而是研究如何在NextJS应用程序中添加一个微前端。为了营造气氛，这里试图围绕网络应用和微前端出现的时间做一个简短的历史回顾。



## 时间线

- 2000年

  在大多数情况下，Web应用程序通常是利用Rails或Django等框架的单体应用来构建的。早期的Twitter、Instagram和Github就是一些例子。

- 2011年

  然后，微服务开始尝试并大规模解决组织问题，例如实现更大的团队自主性，以及一些技术问题，如改进的故障隔离和扩展性。

  *”微服务“本身是在2011年的一次软件架构师活动中首次亮相，该术语用于描述当时许多与会者正在尝试的一种架构风格。Netflix和亚马逊是微服务的早期先驱之一。*

- 2012年

  React的出现为高性能的客户端Web应用程序以及不可避免的单一前端铺平了道路。

- 2016年

  ThoughtWorks开始评估一种称为”微前端“的新技术，以获得与微服务提供的类似好处，但用于前端。

- 2020年

  webpack5发布，**Module Federated**为相对年轻的微前端范例打开了新的可能性。



## NextJS应用程序中的微前端

所以微前端实际上已经存在了一段时间，并以各种方式实现。Webpack5及其**Module Federated**功能是生态系统中较新且令人兴奋的新增功能之一。

*注意：我可以交替使用”微前端“和**module ferdertion**这两个术语*

### 要求

要在NextJS应用程序中实现微前端，你需要使用NextJS或更高版本，并且需要一个ferderated module

### 微前端

我特定的微前端--一个简单的按钮--是官方联合模块双向示例的最小修改分支。

它建立后被拖入公共的AWS S3桶，作为一个快速的概念验证。大部分配置在带有ModuleFederationPlugin的webpack.config.js中。

webpack.config.js

    const HtmlWebpackPlugin = require("html-webpack-plugin")
    const { ModuleFederationPlugin } = require("webpack").container
    const path = require('path')
    const deps = require('./package.json').dpendencies
    
    module.exports = {
        entry: './scr/index',
        mode: 'development',
        devServer: {
            contentBase: path.join(__dirname, 'dist'),
            port: 3002,
        },
        output: {
            publicPath: 'auto',
        },
        resolve: {
            extensions: ['.tsx', '.ts', '.js'],
        },
        module: {
            rules: [
                {
                    test: /\.tsx?$/,
                    loader: "ts-loader",
                    include: path.resolve(__dirname, 'src'),
                    exclude: /node_modules/,
                },
                {
                    test: /\.jsx?$/,
                    loader: 'babel-loader',
                    exclude: /node_modules/,
                    options: {
                        presets: ['@babel/preset-react'],
                    },
                },
                {
                    test: /\.css$/i,
                    use: ['style-loader', 'css-loader']
                },
            ],
        },
        plugins: [
            new ModuleFederationPlugin({
                name: "app2",
                filename: "remoteEntry.js",
                exposes: {
                    './Button': './src/Button',
                    './Tree': './src/FileTree/Tree',
                    './Folder': './src/FileTree/Folder',
                    './File': './src/FileTree/File'
                },
                shared: [
                    {
                        ...deps,
                        react: {
                            // eager: true
                            singleton: true,
                            requiredVersion: deps.react,
                        },
                        "react-dom": {
                            // eager: true
                            singleton: true,
                            requiredVersion: deps['react-dom']
                        },
                    },
                ],
            }),
            new HtmlWebpackPlugin({
                template: './public/index.html'
            })
        ]
    }
    
**注意**: name, filename, exposes字段很重要，因为它们将被消费应用引用。

### 应用程序

NextJS 10 配备了Webpack5，作为一个实验性的功能，你可以用一个功能标志来启用。
NextJS 11 默认带有Webpack5，无需要配置

#### 安装NextJS 10+

    yarn add next@latest
    
在next.config.js中将future.webpack5标志设置为true，并推送一个新的ModuleFederationPlugin到插件列表。

**警告**：这不是针对Next 10的文件！

    module.exports = {
        // if on Next 10
        future: {
            webpack5: ture,
        },
        // if on Next 11
        webpack5: true,    // 默认是true，所以你可以忽略这个
        webpack(config, options): {
            /** Webpack5 **/
            console.log(options.webpack.version)
            
            const { ModuleFederationPlugin } = options.webpack.container;
            
            config.plugins.push(
                new ModuleFederationPlugin({
                    remotes: {
                        app2: "app2@https://kevinmfe.s3.amazonaws.com/app2/dist/remoteEntry.js",
                    },
                    shared: [],
                })
            );
            return config;
        }
    }

remotes字段允许消费者应用程序导入联合模块。这里使用的是联合模块的name和filename：

    `{someKey}: {name}@domain/path/to/{file}`


### 执行

要导入一个联合模块，你可以使用dynamic。任何包含在联合模块的exposes字段中的模块都可以在导入路径中使用：
    
    `{someKey}/{exposes.Button|exposes/SomeModule|etc.}`

.src/pages/[year]/[month]/[day]/[slug.tsx]

    import dynamic from 'next/dynamic'
    
    // 这与React.lazy()的行为或多或少相同
    // 和 <React.Suspense>，或 react-loadable
    
    const MfeButton = dynamic(
        async () => {
            await new Promise((res) => setTimeout(res, 1500))
            return await import('app2/button')
        },
        {
            ssr: false,
            loading: ({ error }) => {
                if (error) {
                    return <Error label={error.name}>{error.message}</Error>
                }
                return <Skeleton></Skeleton>
            }
        }
    )

    const mdxComponents = {
        MfeButton: dynamic(() => import('app2/Button'), {
            ssr: false,
            loading: () => <Skeleton></Skeleton>,
        }),
    }
    
    export default function MyPage() {
        return <MfeButton label={"a label prop"} />
    }


以下是本网站实际的微前端实现图。目前，该框架是非常基本的，但可以很容易地扩展到包括诸如用于微前端存储桶的专用CloudFront分发、用于服务器端渲染琴断的Lambda@Edge或用于单个微前端数据需求的Api网管等内容。

![image](https://thekevinwang.com/image/2021/03/26/mfe_dark.webp)


## 经验之谈

这是一个概念证明，说明将联合模块/微前端集成到NextJS应用程序中是多么容易。尽管是一个过于简单的例子，但这是一个有趣的探索性项目。

说到这里，最初由一个心理障碍，有点难以客户，可能是因为我没有意识到微前端的主题/模式有多么广泛。Webpack5和联合模块仅占主题的一小部分。

Webpack5生态系统仍处于起步阶段，最大的知识来源是冒险的早期采用者的博客文章。围绕该主题的Stack Overflow帖子也非常有限。

我仍然想亲自探索的事情是：
    
    跨多个不相关的微前端共享反应状态管理
    
    在不同的框架中创建微前端，如Vue、Angular或React Native Web
    
    导入所述微前端
    
    服务器端渲染联合模块
    
    从单个联合模块中获取数据
    
    成功渲染使用状态钩子的多个联合React函数组件的树
    
    成功渲染多个联合React有状态类组件的树
    

## 注意事项

动态导入联合模块会丢失静态类型检查。但是，我可以看到通过将静态类型发布到npm来解决这个问题

在这篇文章和显示的设置中，我似乎无法得到一个使用hook的联合React组件的正常工作示例，并且它似乎是ModuleFederationPlugin和一些NextJS内部工作之间固有的不兼容/限制

    react.development.js:1476 Uncaught Error: Invalid hook call. Hooks can only be called inside of the body of a function component. This could happen for one of the following reasons:
    1. You might have mismatching versions of React and the renderer (such as React DOM)
    2. You might be breaking the Rules of Hooks
    3. You might have more than one copy of React in the same app
    See https://reactjs.org/link/invalid-hook-call for tips about how to debug and fix this problem.

我尝试了多种选项，包括一些自定义加载组件，并成功呈现了使用useState hook的单身族组件，但解决方案太复杂，无法维护IMO。生态系统仍然非常不成熟，所以我希望很快就会有更多关于此的官方模式出现。

See：[https://stackoverflow.com/questions/63942391/webpack-5-module-federation-hooks-in-remote-module-not-working](https://stackoverflow.com/questions/63942391/webpack-5-module-federation-hooks-in-remote-module-not-working)


## 调试

在浏览器控制台中，你可以运行它来检查联合模块的存在

    window.app2.get("./Button").then(factory => factory()).then(mod => console.log(mod))

    Module {__esModule: true, Symbol(Symbol.toStringTag): "Module"} ℹ️
        default: (...)
        Symbol(Symbol.toStringTag): "Module"
        __esModule: true
    ▶️ get default: ()=>a
    ▶️ __proto__: Object