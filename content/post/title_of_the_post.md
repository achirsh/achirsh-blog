---
title: "微前端"
date: 2021-09-07
draft: false
summary: "微前端"
---

# 微前端(Micro Frontends)

```
好的前端开发是困难的。扩展前端开发，使许多团队能够同时在一个大型复杂的产品上工作，这就更难了。在这片文章中，我们将介绍最近的一个趋势，即把前端单体分解成许多更小、更容易管理的部分，以及这种架构如何提高团队在前端代码方面的效率和效益。除了谈论各种好处和成本外，我们还将介绍一些可用的实施方案，并将深入研究一个完整的示范性应用，以展示该技术。
```

## 作者

```
Cam Jackson

Cam Jackson是 Thoughtworks 的全栈网络开发人员和顾问，对大型组织如何扩展其前端开发流程和实践特别感兴趣。他曾与多个行业和国家的客户合作，帮助他们更有效地交付网络应用。
```

## 目录

```
好处
    渐进式升级
    简单、解耦的代码库
    独立部署
    自主的团队
    简而言之
例子
集成方法
    服务器端模块组合
    构建时集成
    通过iframes的运行时集成
    通过javacript的运行时集成
    通过Web组件的运行时集成
样式设计
共享的组件库
跨应用通信
后台通信
测试
详细示例
    容器
    微前端
    通过路由进行跨应用程序通信
    常规内容
    基础设施
缺点
    有效载荷大小
    环境差异
    操作和管理的复杂性
结论
```

近年来，[微服务](https://martinfowler.com/articles/microservices.html)大受欢迎，许多组织使用这种架构风格来避免大型、单体后端的局限性。虽然关于这种构建服务器端软件的风格已经写了很多，但许多公司仍在为单体的前端代码库而挣扎。

也许你想构建一个渐进式或响应式 Web 应用程序，但找不到一个容易开始将这些功能整合到现有代码的地方。也许您想开始使用新的 JavaScript 语言功能（或可以编译为 JavaScript 的无数语言之一），但您无法将必要的构建工具安装到现有的构建过程中。或者你只是想扩展你的开发，以便多个团队可以同时在一个产品上工作，但现有单体的耦合和复杂性意味着每个人都在互相踩踏。这些都是真实的问题，都会对你向客户有效提供高质量体验的能力产生负面影响。

最近，我们看到越来越多的人关注整体架构和组织结构，这对于复杂的现代网络开发来说是必要的。特别是，我们看到出现了将前端单体分解成更小、更简单的块的模式，这些块可以独立开发、测试和部署，同时仍然作为一个单一的有凝聚力的产品出现在客户面前。我们称这种技术为**微前端**，我们将其定义为：

*一种架构风格，独立交付的前端应用程序被组成一个更大的整体*

在 2016 年 11 月期的 Thoughtworks 技术雷达中，我们将[微前端](https://www.thoughtworks.com/radar/techniques/micro-frontends)列为组织应该评估的技术。后来我们将其推广到试用版，最后推广到采用，这意味着我们将其视为一种经过验证的方法，在有意义的情况下你应该使用。

![image](https://martinfowler.com/articles/micro-frontends/radar.png)

*图1：微型前端已经多次出现在技术雷达上。*



我们从微前端看到的一些主要好处是：

- 更小、更有凝聚力和可维护的代码库
- 具有解耦、自主团队的更可扩展的组织
- 能够以比以前更多的增量方式升级、更新、甚至重写前端的部分内容 



这些突出优势与微服务可以提供的优势相同，这并非巧合。



当然，在软件架构方面没有免费的午餐——所有东西都是有成本的。一些微前端实现会导致依赖关系的重复，增加用户必须下载的字节数。此外，团队自主性的急剧增加可能会导致团队工作方式的分裂。尽管如此，我们相信这些风险是可以管理的，而且微前端的好处往往超过了成本。



[APPLICATION ARCHITECTURE](https://martinfowler.com/tags/application%20architecture.html)

[FRONT-END](https://martinfowler.com/tags/front-end.html)

[MICROSERVICES](https://martinfowler.com/tags/microservices.html)

[原文地址](https://martinfowler.com/articles/micro-frontends.html)  



## 好处(Benefits)

我们没有用具体的技术方法或实施细节来定义微前端，而是把重点放在出现的属性和它们带来的好处上。

### 渐进式升级

对于许多组织来说，这是他们微前端之旅的开始。旧的、大型的、单一的前端被过去的技术栈或在交付压力下编写的代码所束缚，它已经到了完全重写的地步了。为了避免全部重新的[危险](https://www.joelonsoftware.com/2000/04/06/things-you-should-never-do-part-i/)，我们更愿意把旧的应用程序一块一块地[扼杀](https://martinfowler.com/bliki/StranglerApplication.html)掉，同时继续向我们的客户提供新功能而不被单体应用拖累。

这通常导致了微前端架构的出现。一旦一个团队有了在对旧世界几乎没有修改的情况下将一个功能全部投入生产的经验，其他团队也会想加入新的世界。现有的代码仍需要维护，在某些情况下，继续向其添加新功能可能是有意义的，但现在可以选择。

最终结果是，我们被赋予了更多的自由，可以对我们产品的各个部分进行逐一决定，并对我们的架构、依赖项和用户体验进行逐渐式升级。如果我们的主框架有一个重大的突破性变化，每一个微前端都可以在合理的时候进行升级，而不是被迫停止世界，一次性升级所有内容。如果我们想尝试新技术或新的交互模式，我们可以以一种比以前更孤立的方式进行。



### 简单、解耦的代码库

根据定义，每个单独的微前端的源代码将比单一的单片式前端的源代码小得多。这些较小的代码库往往更简单，更容易被开发人员使用。特别是，我们避免了因组件之间无意和不适当的耦合而产生的复杂性，这些组件之间不应该相互了解。通过在应用程序的[有界边界](https://martinfowler.com/bliki/BoundedContext.html)范围内绘制更粗的线，我们使这种意外耦合更难出现。

当然，一个单一的、高层次的架构决定（即“让我们做微前端”）并不能替代良好的老式干净代码。我们并不是想让自己免于思考我们的代码并为其质量付出努力。相反，我们正试图让错误的决定变得困难，而使好的决定变更容易，来使自己陷入[成功的陷阱](https://blog.codinghorror.com/falling-into-the-pit-of-success/)。例如，在有界限的上下文中共享领域模型变得更加困难，所以开发人员不太可能这样做。类似地，微前端促使您明确和慎重地考虑数据和事件如何在应用程序的不同部分之间流动，而这是我们无论如何都应该做的事情！



### 独立部署

正如微服务一样，微前端的独立部署能力是关键。这减少了任何特定部署的范围，这反过来又减少了相关的风险。不管您的微前端代码以何种方式或在哪里托管，每个微前端都应该有自己的持续交付管道，用于构建、测试并将其部署到生产。我们应该能够部署每个微前端，而很少考虑到其他代码库或管道的发布状态。旧的单体应用是否处于固定的、手动的、每季度的发布周期，或者隔壁的团队是否已将一个半完成或损坏的功能推送到他们的主分支中，都无关紧要。如果一个给定的微前端准备进入生产，它应该能够这样做，而这个决定应该由构建和维护它的团队。

![image](https://martinfowler.com/articles/micro-frontends/deployment.png)

*图2：每个微型前端都被独立部署到生产中*



### 自主的团队

作为我们的代码库和发布周期解耦的更高层次的好处，我们在拥有完全独立的团队方面取得了很大的进展，他们可以拥有产品的一部分，从构思到生产甚至更多。团队可以完全拥有为客户提供价值所需的一切，这使他们能够快速有效地行动。要做到这一点，我们的团队需要围绕业务功能的垂直切面组建，而不是围绕技术能力。一种简单的方法是根据最终用户将看到的内容分割产品，因为每个微前端封装应用程序的一个页面，并由一个团队端到端拥有。与围绕技术或“横向”问题（如样式设计、表单或验证）组建的团队相比，这样的团队工作的凝聚力更高。

![image](https://martinfowler.com/articles/micro-frontends/horizontal.png)*图3：每个应用程序应该由一个团队拥有*



### 简而言之

简而言之，微前端就是将大而可怕的东西切成更小、更易于管理的部分，然后明确它们之间的依赖关系。我们的技术选择、我们的代码库、我们的团队和我们的发布流程都应该能够彼此独立地运行和发展，而无需过度协调。  


## 例子(The example)

想象一下，在一个网站上，客户可以在其中订购外卖食品。从表面上看，这是一个相当简单的概念，但如果你想做好它，有大量的细节是令人惊讶的：

- 应该有一个登陆页面，客户可以浏览和搜索餐馆。餐馆应该是可以搜索的，并可以通过任何数量的属性进行过滤，包括价格、菜肴或顾客以前点过的东西。
- 每家餐厅都需要要自己的页面，显示其菜单项，并允许顾客选择他们想吃的东西，包括折扣、餐饮优惠和特殊要求。
- 顾客们应该有一个个人资料页面，他们可以在其中查看他们的订单历史记录、跟踪交付和自定义他们的付款选项。

![image](https://martinfowler.com/articles/micro-frontends/wireframe.png)

*图4：一个送餐网站可能有几个合理的复杂页面*



每个页面都有足够的复杂性，我们可以很容易地证明每个页面都有一个专门的团队，并且每个团队都应该能够独立于所有其他团队在他们的页面上工作。他们应该能够开发、测试、部署和维护他们的代码，而不必担心与其他团队发生冲突或协调。但是，我们的客户仍然应该看到一个单一的、无缝的网站。

在本文的其余部分，我们将在需要示例代码或场景的任何地方使用此示例应用程序。

## 集成方法(Integration approaches)

鉴于上面相当宽松的定义，有许多方法可以合理地称为微前端。在本节中，我们将展示一些示例并讨论它们的权衡。所有方法都有一个相当自然的架构--一般来说，应用程序中的每个页面都有一个微前端，并且有一个单一的**容器应用程序**，它：
    

- 渲染常见的页面元素，例如页眉和页脚
- 解决跨域问题，如认证和导航
- 将各种微前端放在页面上，并告诉每个微前端何时何地渲染自己

![image](https://martinfowler.com/articles/micro-frontends/composition.png)

*图5：你通常可以从页面的视觉结构中推导出你的架构*



### 服务器端模块组合

我们从一个明显的非创新的前端开发方法开始--在服务器上用多个模板或片段来渲染HTML。我们有一个index.html，它包含所有常见的页面元素，然后使用服务器端includes来插入来自片段HTML文件的特定页面元素：

```
<html lang="en" dir="ltr">
    <head>
        <meta charset="utf-8">
        <title>Feed me</title>
    </head>
    <body>
        <h1>Feed me</h1>
    </body>
</html>
```

我们使用Nginx为这个文件提供服务，通过匹配正在请求的URL来配置$PAGE变量：

```
server: {
    listen 8080;
    server_name localhost;
    root: /usr/share/nginx/html;
    index index.hmtl;
    ssi on;
    
    # Redirect /to /brower
    rewrite ^/$ http://localhost:8080/brower redirect;
    
    # Decide which HTML fragment to insert based on the URL
    location /browse {
        set $PATH 'browse'
    }
    location /order {
        set $PATH 'order'
    }
    location /profile {
        set $PATH 'profile'
    }
    
    #All locations should render through index.html
    error_page 404 /index.html;
}
```

这是相当标准的服务器端组合。我们有理由称其为微服务的原因是，我们将代码分割成这样一种方式，即每一块都代表一个独立的领域概念，可以由一个独立的团队交付。这里没有显示的是这些不同的HTML文件是如何在网络服务器上结束的，但假设它们每个都有自己的部署管道，这使得我们可以在不影响或不考虑任何其他页面的情况下对一个页面进行部署修改。

为了获得更大的独立性，可以有一个单独的服务器负责渲染和服务每个微前端，有一个服务器在前面向其他服务器发出请求。通过对响应的仔细缓存，这可以在不影响延迟的情况下完成。

![image](https://martinfowler.com/articles/micro-frontends/ssi.png)

*图6：这些服务器中的每一个都可以独立构建和部署到*



这个例子说明了微前端不一定是一种新技术，也不一定很复杂。只要我们注意我们的技术决定如何影响我们的代码库和团队的自主性，无论我们的技术栈如何，我们都可以实现许多相同的好处。




### 构建时集成

我们有时看到的一种方法是将每个微前端作为一个包发布，并让容器应用程序将它们全部包含为库依赖项。下面是容器的package.json对于我们的例子应用来说可能是这样的：

```
{
    "name": "@feed-me/container",
    "version": "1.0.0",
    "description": "A food delivery web app",
    "dependencies": {
        "@feed-me/browse-restaurants": "^1.2.3",
        "@feed-me/order-food": "^4.5.6",
        "@feed-me/user-profile": "^7.8.9"
    }
}
```

起初，这似乎是有意义的。它产生了一个单一的可部署的javascript捆绑包，像往常一样，使我们能够从我们的各种应用程序汇总去掉共同的依赖性。然而，这种方法意味着我们必须重新编译和发布每个微前端，以便发布对产品任何单个部分的更改。就像微服务一样，我们已经看到这种**锁步发布过程**所带来的过程，我们强烈建议不要使用这种微前端方式。

在费尽心思将我们的应用程序划分为可以独立开发和测试的离散代码库之后，我们不要在发布阶段重新引入所有这些耦合。我们应该找到一种方法，在运行时，而不是在构建时整合我们的微型前端。




### 通过iframes的运行时集成

在浏览器中把应用程序组合在一起的最简单的方法之一是最不起眼的iframe。就其性质而言，iframe使人们可以很容易地从独立的子页面中建立一个页面。它们还提供了一个很好的隔离度，即样式和全局变量不会相互干扰。

```
<html>
    <head>
        <title>Feed me!</title>
    </head>
    <body>
        <h1>Welcome to Feed me!</h1>
        <iframe id="micro-frontend-container"></iframe>
        
        <script type="text/javascript">
            const microFrontendsByRoute = {
                '/': 'https://browse.example.com/index.html',
                '/order-food': 'https://order.example.com/index.html',
                '/user-profile': 'https://profile.example.com/index.html',
            }
            
            const iframe = document.getElementById('micro-frontend-container');
            iframe.src = microFrontendsByRoute[window.location.pathname];
        </script>
    </body>
</html>
```

就像[服务器端包含选项](https://martinfowler.com/articles/micro-frontends.html#Server-sideTemplateComposition)一样，用iframes构建页面并不是一种新的技术，也许看起来并不那么令人兴奋。但是，如果我们重新审视[前面列出的](https://martinfowler.com/articles/micro-frontends.html#Benefits)微前端的主要好处，只要我们小心翼翼地分割应用程序和组织我们的团队，iframes大多符合这个要求。

我们经常看到很多人不愿意选择iframe。虽然有些不情愿似乎是由于直觉认为iframe有点“糟糕“，但人们有一些很好的理由避免使用它们。上面提到的简单隔离确实会使它们不如其他选项灵活。在应用程序的不同部分之间构建集成可能很困难，因此它们使路由、历史记录和深层链接更加复杂，并且它们为使您的页面完全影响提出了一些额外的挑战。




### 通过javacript的运行时集成

我们将描述的下一种方法可能是最灵活的方法，也是我们看到团队最常采用的方法。每个微前端都是用<script>标签包含在页面上，并在加载时暴露一个全局函数作为其入口点。然后容器应用程序确定应该挂载哪个微前端，并调用相关函数来告诉微前端何时何地渲染自己。

```
<html>
  <head>
    <title>Feed me!</title>
  </head>
  <body>
    <h1>Welcome to Feed me!</h1>

    <script src='https://browse.example.com/bundle.js' />
    <script src="https://order.example.com/bundle.js" />
    <script src="https://profile.example.com/bundle.js" />

    <div id="micro-frontend-root"></div>

    <script type="text/javascript">
        const microFrontendsByRoute = {
            '/': window.renderBrowseRestaurants,
            '/order-food': window.renderOrderFood,
            '/user-profile': window.renderUserProfile,
        };  
        const renderFunction = microFrontendsByRoute[window.location.pathname];
        renderFunction('micro-frontend-root');
    </script>
  </body>
</html>
```

上面显然是一个原始的例子，但它展示了基本的技术。与构建时集成不同，我们可以独立部署每个bundle.js文件。而且与iframe不同的是，我们可以完全灵活地在我们的微前端之间构建集成。我们可以通过多种方式扩展上述代码，例如，只在需要时下载每个Javascript bundle，或者在渲染一个微前端时将数据传入和传出。

这种方法的灵活性与独立的可部署性相结合，使其成为我们的默认选择。当我们进入[完整地示例](https://martinfowler.com/articles/micro-frontends.html#TheExampleInDetail)时，我们将更详细地探讨他。




### 通过Web组件的运行时集成

前面方法的一个变化是，每个微前端定义一个HTML自定义元素供容器实例化，而不是定义一个全局函数供容器调用。

```
<html>
    <head>
        <title>Feed me!</title>
    </head>
    <body>
        <h1>Welcome to Feed me!</h1>
        <script src="https://browse.example.com/bundle.js"></script>
        <script src="https://order.example.com/bundle.js"></script>
        <script src="https://profile.example.com/bundle.js"></script>
        
        <div id="micro-frontend-root"></div>
        
        <script type="text/javacript">
            const webComponentByRoute = {
                '/': 'micro-frontend-broswe-restaurants',
                '/order-food': 'micro-frontend-order-food',
                'use-profile': 'micro-frontend-user-profile'
            }
            
            const webComponentType = webComponentByRoute[window.loaction.pathname];
            const root = document.getElementById('micro-frontend-root');
            const webComponent = document.createElement(webComponentType);
            root.appendChild(webComponent);
        </script>
    </body>
</html>
```

这里的最终结果与前面的例子很相似，主要区别在于你选择以"Web组件方式"进行操作。如果您喜欢Web组件规范，并且喜欢使用浏览器提供的功能的想法，那么这是一个不错的选择。如果您更喜欢在容器应用程序和微前端之间定义自己的接口，那么你可能更喜欢前面的例子。

## 样式s设计(Styling)

CSS作为一种语言本质上是全局的、继承的和级联的，传统上没有模块系统、命名空间或封装。这些功能中的一些现在确实存在，但通常缺乏浏览器支持。在一个微前端的环境中，许多这样的问题会变得更加严重。例如，如果一个团队的微前端有一个样式表，上面写h2{color:black}另一个说h2{color: blue}，而这两个选择器都附加到同一个页面上，那么有人会感到失望！这不是一个新问题，但由于这些选择器是由不同团队在不同时间编写的，并且代码可能分散在不同的存储库中，使得它更难被发现。

多年来，人们发明了许多方法来使CSS更易于管理。有些人选择使用严格的命名约定，例如[BEM](http://getbem.com/)，以确保选择器仅适用于预定的地方。其他不喜欢单独依赖开发人员纪律的人使用预处理器，例如[SASS](https://sass-lang.com/)，其选择器嵌套可以用作命名空间的一种形式。一种较新的方法是使用[CSS modules](https://github.com/css-modules/css-modules)或各种[CSS-in-JS](https://mxstbr.com/thoughts/css-in-js/)库之一以编程方式应用所有样式，以确保仅在开发人员打算的地方直接应用样式。或者对于更基于平台的方法，[shadow DOM](https://developer.mozilla.org/en-US/docs/Web/Web_Components/Using_shadow_DOM)还提供样式隔离。

你选择的方法并不重要，只要你找到一种方法，确保开发人员可以独立编写他们的样式，并确信他们的代码在组合单个应用程序时的行为是可预测的。


## 共享组件库(Shared component libraries)

我们在上面提到过微前端的视觉一致性很重要，其中一个方法是开发一个共享的、可重用的UI组件库。总的来说，我们认为这是一个好主意，尽管它很难做好。创建这样一个库的主要好处是通过代码的重复使用和视觉上的一致性来减少工作量。此外，你的组件库可以作为一个活的样式指南，它可以成为开发人员和设计人员之间合作的一个重要点。

最容易出错的事情之一就是过早地创建过多的这些组件。创建一个[Foundation Framework](https://martinfowler.com/bliki/FoundationFramework.html)是很诱人的，它具有所有应用程序都需要的所有通用视觉效果。然后，经验告诉我们，在实际使用组件之前，很难甚至不可能猜测它们的API应该是什么，这会导致组件早期的大量流失。出于这个原因，我们更倾向于让团队在他们需要的时候在他门的代码中创建自己的组件，即使这在最初会造成一些重复。让模式自然而然地出现，一旦组件的API变得明显，你就可以把重复的代码收集到共享库中，并确信你有一些成熟的东西。

最明显的共享对象是”愚蠢的“视觉基元，如图标、标签和按钮。我们还可以共享更复杂的组件，这些组件可能包含大量的UI逻辑，比如一个自动完成的下拉搜索字段。或者一个可排序、可过滤、分页的表格。然而，要注意确保你的共享组件只包含UI逻辑，而没有业务或领域逻辑。当领域逻辑被放入一个共享组件库时，它会在整个应用程序中产生高度的耦合，并增加改变的难度。因此，例如，您通常不应该尝试共享ProductTable，它将包含关于”产品”到底是什么以及它应该如何表现的各种假设。这样的领域建模和业务逻辑属于微前端的应用代码，而不是共享库。

与任何共享的内部库一样，围绕其所有权和管理存在一些棘手的问题。一种模型是说，作为一种共享资产，“每个人”都拥有它，尽管在实践中这通常意味着没有人拥有它。它可以迅速成为一个没有明确约定或技术愿景的不一致代码的大杂烩。在另一个极端，如果共享库的开发是完全集中的，那么创建组件的人和使用它们的人之间就会出现很大的脱节。我们所见过的最好的模式是任何人都可以为库做贡献，但是有一个[保管人](https://martinfowler.com/bliki/ServiceCustodian.html)（一个人或一个团队）负责确保这些贡献的质量、一致性和有效性。维护共享库的工作需要强大的技术技能，还需要培养多个团队之间合作的人机关系技巧。


## 跨应用通信(Cross-application communication)

关于微前端最常见的问题之一是如何让它们相互交流。一般而言，我们建议让他们尽可能少地交流，因为这通常会重新引入我们最初试图避免的那种不适当的耦合。

这就是说，经常需要某种程度的跨应用通信。[自定义事件](https://developer.mozilla.org/en-US/docs/Web/Guide/Events/Creating_and_triggering_events)允许微前端间接地进行通信，这是尽量减少直接耦合的好方法，尽管它确实使确定和执行存在于微前端之间存在的契约更加困难。另外，将回调和数据向下传递的React模型（在这种情况下，从容器应用程序向下传递到微前端）也是一个很好的解决方案，它使契约更加明确。第三种选择是使用地址栏作为一种通信机制，我们将在后面详细探讨。

无论我们选择什么方法，我们都希望我们的微前端通过向对方发送消息或事件进行通信，并避免任何共享状态。就像在微服务之间共享数据库一样，一旦我们共享数据结构和领域模型，就会产生大量耦合，并且很难进行更改。

与样式设计一样，有几种不同的方法可以在这里很好地工作。最重要的是要认真思考你要引入什么样的耦合，以及你将如何随着时间的推移保持这种契约。就像微服务之间的集成一样，如果没有一个跨不同应用和团队的协调升级过程，将就无法对你的集成进行突破性的改变。

你还应该考虑你将如何自动验证集成不会中断。功能测试是一种方法，但由于实现和维护的成本，我们倾向于限制功能测试的数量。另外，你可以实现某种形式的[消费者驱动的合同](https://martinfowler.com/articles/consumerDrivenContracts.html)，这样每个微前端可以指定它对其他微前端的要求，而不需要实际集成并在浏览器中一起运行它们。



*如果您使用的是redux，通常的做法是为整个应用程序创建一个单一的、全局的、共享的存储。然而，如果每个微前端都应该是它自己的独立应用程序，那么每一个微前端都有自己的redux存储是有意义的。redux文档甚至提到“将Redux应用作为更大应用中的一个组件来隔离”是拥有多个存储空间的一个有效理由。*


## 后端通信(Backend communication)

如果我们有独立的团队在前端应用程序上工作，那么后端开发呢？我们坚信全栈团队的价值，它们拥有自己的应用程序的开发，从视觉代码一直到API开发，以及数据库和基础设施代码。在这里有帮助的一种模式是BFF模式，即每个前端应用都有一个相应的后端，其目的只是为了满足该前端的需求。虽然BFF模式最初可能意味着每个前端渠道（网络、移动等）都有专门的后台，但它可以很容易地扩展为每个微前端都有一个后台。

这里有很多变量需要考虑，[BFF](https://samnewman.io/patterns/architectural/bff/)可能是自含的，有自己的业务逻辑和数据库，或者它可能只是下游服务的一个聚合器。如果有下游服务，拥有微前端及其BFF的团队可能会也可能不会拥有其中的一些服务。如果微前端只有一个与之通信的API，并且该API相当稳定，那么建立一个BFF可能根本没有什么价值。这里的指导原则是，建立一个特定的微前端的团队不必等待其他团队为他们构建东西。因为，如果一个微前端的每一个新功能也需要后端更改，这就是建立一个由一个团队拥有的BFF的有力证据。

![image](https://martinfowler.com/articles/micro-frontends/bff.png)

*图7：有许多不同的方式来构建你的前端/后端关系*



另一个常见的问题是，微前端应用程序的用户应该如何在服务器上进行认证和授权？显然，我们客户应该只需要认证一次，所以认证通常属于跨领域关注的范畴，应该由容器应用程序拥有。容器可能有某种登陆表单，我们通过它获得某种令牌。该令牌将由容器拥有，并可以在初始化时注入到每个微前端。最后，微前端可以向服务器发出的任何请求发送令牌，而服务器可以做任何需要的验证。

## 测试(Testing)

在测试方面，我们认为单体前端和微前端之间没有太大区别。一般来说，无论你使用什么策略来测试一个单体前端端，都可以在每个单独的微前端复制。也就是说，每个微前端都应该有自己全面的自动化测试套件，确保代码的质量和正确性。

然后，明显的差距是各种微前端与容器应用程序的集成测试。这可以使用你首选的功能/端到端测试工具(如Selenium或Cypress)来完成，但不要太过分；功能测试应该只涵盖在[测试金字塔](https://martinfowler.com/bliki/TestPyramid.html)的较低级别无法测试的方面。我们的意思是，使用单元测试来覆盖你的低级业务逻辑和渲染逻辑，然后使用功能测试来验证页面是否正确组装。例如，你可以在特定URL加载完全集成的应用程序，并断言相关微前端的硬编码标题存在于页面上。

如果有跨越微前端的用户旅程，那么你可以使用功能测试来覆盖这些，但保持功能测试的重点是验证前端的集成，而不是每个微前端的内部业务逻辑，这些应该已经被单元测试所覆盖。如上所述，消费者驱动的合同可以帮助直接指定微前端之间发生的交互，而无需集成环境和功能测试的不稳定性。

## 详细示例(The example in detail)

本文的大部分内容将是我们对我们的示例应用程序的一种实现方式的详细解释。我们将主要关注容器应用程序和微前端如何使用javascript集成在一起，因为这可能是最有趣和最复杂的部分。您可以在[https://demo.microfrontends.com](https://note.youdao.com/)上实时查看部署的最终结果，并且可以在Github上查看完整的源代码。
![image](https://martinfowler.com/articles/micro-frontends/screenshot-browse.png)

*图8：完整的微观前台演示应用程序的 "浏览 "登陆页面*



这个演示都是用React.js构建的，所以值得一提的是，React并没有垄断这种架构。微前端可以使用许多不同的工具或框架来实现。我们在这里选择了React是因为它很受欢迎，也因为我们自己对它很熟悉。



### 容器

我们将从[容器](https://github.com/micro-frontends-demo/container)开始，因为它是我们客户的入口点。让我们看看我们可以从它的package.json了解到什么：

```
{
    "name": "@micro-frontends-demo/container",
    "description": "Entry point and container for a micro frontends demo",
    "scripts": {
        "start": "PORT=3000 react-app-rewired start",
        "build": "react-app-rewired build",
        "test": "react-app-rewired test"
    },
    "dependencies": {
        "react": "^16.4.0",
        "react-dom": "^16.4.0",
        "react-router-dom": "^4.2.2",
        "react-scripts": "^2.1.8"
    },
    "devDependencies": {
        "enzyme": "^3.3.0",
        "enzyme-adapter-react-16": "^1.1.1",
        "jest-enzyme": "^6.0.2",
        "react-app-rewire-micro-frontends": "^0.0.1",
        "react-app-rewired": "^2.1.1"
    },
    "config-overrides-path": "node_modules/react-app-rewire-micro-frontends"
}
```

从对React和react-scripts的依赖中，我们可以得出结论，这是一个用[create-react-app](https://facebook.github.io/create-react-app/)创建的React.js应用程序。更有趣的是，那里没有提到我们要组合在一起形成最终应用的微前端。如果我们在这里将它们指定为库的依赖项，我们就会走向构建时集成的道路，正如前面提到的，这往往会在我们的发布周期中造成问题的耦合。

为了了解我们如何选择和显示一个微前端，让我们看一下App.js。我们使用[React Router](https://reacttraining.com/react-router/)将当前URL与预定义的路由列表进行匹配，并呈现相应的组件：

```
<Switch>
    <Route exact path="/" component={Browse} />
    <Route exact path="/restaurant/:id" component={Restaurant} />
    <Route exact path="/random" component={Random} />
</Switch>
```

*在react-script第版1中，可以让多个应用程序在一个页面上共存而不会发生冲突，但是第版2使用了一些webpack功能，当两个或更多的应用程序视图在一个页面上渲染自己时，会导致错误。出于这个原因，我们使用react-app-rewired来覆盖react-scripts的一些内部webpack配置。这修复了这些错误，并让我们继续依靠react-scripts来为我们管理构建工具。*

Random组件并不是那么有趣--它只是将页面重定向到一个随机选择的餐厅URL。Browse和Restaurant组件如下所示：

```
const Browse = ({ history }) => (
    <MicroFrontend history={history} name="Browse" host={browseHost} />
)

const Restaurant = ({ history }) => (
    <MicroFrontend history={history} name="Restaurant" host={restaurantHost} />
)
```

在这两种情况下，我们都渲染了一个MicroFrontend组件。除了历史对象(在这后面会变得很重要)，我们指定应用程序的唯一名称，以及可以下载其捆绑包的主机。这个配置驱动的URL在本地运行时类似于[http://localhost:3001](https://note.youdao.com/),或者在生产中则是[https://browse.demo.microfrontends.com](https://note.youdao.com/)。

在App.js中选择了一个微前端，现在我们将在MicroFrontend.js中渲染它，这只是另一个React组件：

```
class MicroFrontend extends React.Component {
    render() {
        return <main id={`${this.props.name}-container`} />
    }
}
```

*这不是整个类，我们很快会看到它的更多方法。*



渲染时，我们所做的就是在页面上放一个容器元素，其ID对微前端是唯一的。这就是我们要告诉我们的微前端渲染自身的地方。我们使用React的componentDidMount作为下载和挂载微前端的触发器：

```
class MicroFrontend...

componentDidMount() {
    const { name, host } = this.props;
    const scriptId = `micro-frontend-script-${name}`;
    
    if (document.getElementById(scriptId)) {
        this.renderMicroFrontend();
        return;
    }
    
    fetch(`${host}/asset-manifest.json`)
        .then(res => res.json())
        .then(manifest => {
            const script = document.createElement('script');
            script.id = scriptId;
            script.src = `${host}${manifest['main.js']}`;
            script.onload = this.renderMicroFrontend;
            document.head.appendChild(script);
        })
}
```

首先，我们检查相关的脚本，它有一个唯一的ID，是否已经被下载，在这种情况下，我们可以立即渲染它。如果没有，我们从相应的主机上获取asset-manifest.json文件，以便查找主脚本资产的完整URL。一旦我们设置了脚本的URL，剩下的就是将其附加到文档中，使用了一个onload处理程序来渲染微前端：

```
class MicroFrontend

renderMicroFrontend = () => {
    const { name, history } = this.props;
    
    window[`render${name}`](`${name}-container`, history);
};
```

在上面的代码中，我们正在调用一个全局函数，叫做window.renderBrowse，它是由我们刚刚下载的脚本放在那里的。我们把微前端应该呈现的<main>元素的ID和一个历史对象传递给它，我们很快就会解释。**这个全局函数的签名是容器应用程序和微前端之间的关键契约**。这是任何通信或集成应该发生的地方，因此保持它相当轻量级，使其易于维护，并在未来添加新的微前端。每当我们想要做一些想要做的一些需要改此代码的事情时，我们应该仔细考虑一下这对我们的代码库的耦合性和合同的维护意味着什么。

还有最后一件事，就是处理清理工作。当我们的MicroFrontend组件取消卸载(从DOM中删除)时，我们也要取消挂载相关的微前端。为此，每个微前端都有一个相应的全局函数，我们从适当的React生命周期方法中调用它：

```
class MicroFrontend...

componentWillUnmount() {
    const { name } = this.props;  

    window[`unmount${name}`](`${name}-container`)
}
```

就其自身的内容而言，容器直接渲染的是网站的顶层标题和导航栏，因为这些在所有页面中都是不变的。这些元素的CSS已被仔细编写，以确保它只为标题内的元素进行样式设计，因此它不应与微前端中的任何样式代码冲突。

容器应用程序就这样结束了！这是相当初级的，但这给我们提供了一个shell，可以在运行时动态下载我们的微前端，并将它们粘合在一起，形成一个单一页面的凝聚力。这些微前端可以独立部署到生产中，而不需要对任何其他的微前端或容器本身做任何改变。




### 微前端

继续这个故事的合乎逻辑的地方是我们一直提到的全局渲染函数。我们应用程序的主页是一个可过滤的餐馆列表，其入口点如下所示：

```
import React from 'react';
import ReactDOM from 'react-dom';
import APP from './App';
import registerServiceWorkder from './registerServiceWorker';

window.renderBrowse = (containerId, history) => {
    ReactDOM.render(<App history={history} />, document.getElementById(containerId));
    registerServiceWorkder();
}

window.unmountBrowse = containerId => {
    ReactDOM.unmountComponentAtNode(document.getElementById(containerId));
}
```

通常在React.js应用程序中，对ReactDOM.render的调用将在顶级范围内，这意味着一旦这个脚本被加载，它就会立即开始渲染到一个硬编码的DOM元素。对于这个应用程序，我们需要能够控制渲染发生的时间和地点，所以我们将它包装在一接收DOM元素的ID作为参数的函数中，并将该函数附加到全局window对象。我们还可以看到用于清理的相应的卸载函数。

虽然我们已经看到了当微前端集成到整个容器应用中时如何调用这个函数，但这里成功的最大标准之一是我们可以独立开发和运行微前端。因此，每个微前端也有自己的index.html，其中有一个内联脚本，可以在容器外以”独立“模式渲染应用程序：

```
<html lang="en">
    <head>
        <title>Restaurant order</title>
    </head>
    <body>
        <main id="container" />
        <script type="text/javascript">
            window.onload = () => {
                window.renderRestaurant('container')
            }
        </script>
    </body>
</html>
```

![image](https://martinfowler.com/articles/micro-frontends/screenshot-order.png)

*图9：每个微型前端都可以作为一个独立的应用程序在容器外运行。*



从这一点上看，微前端大多只是普通的React应用程序。”[浏览](https://github.com/micro-frontends-demo/browse)”应用程序从后端获取餐厅列表，提供用于搜索和过滤餐厅的<input>元素，并渲染导航到特定餐厅的React Router<Link>元素。那时我们将切换到第二个“[订购](https://github.com/micro-frontends-demo/restaurant-order)”的微前端，它渲染一个单一的餐厅及其菜单。
![image](https://martinfowler.com/articles/micro-frontends/demo-architecture.png)

*图10：这些微前端仅通过路由更改交互，而不是直接交互*



关于我们的微前端，最后值得一提的是，它们都使用styled-components来处理所有样式。这个CSS-in-JS库可以很容易地将样式与特定组件相关联，因此我们保证一个微前端的样式不会泄漏出去，影响容器或其他微前端。



### 通过路由进行跨应用程序通信

我们在前面提到，跨应用的通信应该保持在最低限度。在这个例子中，我们唯一的要求是，浏览页面需要告诉餐厅页面要加载哪家餐厅。这里我们将看到如何使用客户端路由来解决这个问题。

这里涉及的三个React应用程序都使用React Router进行声明式路由，但初始化的略有不同。对于容器应用程序，我们创建了一个<BrowseRouter>，它会在内部实例化一个历史对象。这也是我们之前一直在强调的历史对象。我们用这个对象来操作客户端的历史记录，我们也可以用它来连接多个React Router。在我们的微前端，我们像这样初始化Router：

```
<Router history={this.props.history}>
```

在这种情况下，我们不是让React Router实例化另一个历史对象，而是为它提供容器应用程序传入的实例。所有<Router>实例现在都是相连的，所以在任何一个实例中触发的路由变化都会反映在所有实例中。这给了我们一个简单的方法，通过URL将”参数“从一个微前端传递到另一个。例如，在浏览微前端中，我们有一个这样的链接：

```
<Link to={`/restaurant/${restaurant.id}`}>
```

当这个链接被点击时，容器中的路由将被更新，它将看到新的URL并确定餐厅的微前端应该被挂载和渲染。那个微前端自己的路由逻辑将从URL中提取餐厅ID并呈现正确的信息。

希望这个例子的流程显示了不起眼的URL的灵活性和强大功能。除了对共享和书签之外，在这个特定的架构中，它可以成为一种有用的方式，在微前端之间的传递意图。为这个目的使用页面URL可以满足很多要求：

- 它的结构是一个定义明确的、开放的标准
- 它可以被页面上的任何代码全局访问
- 其有限的规模鼓励只发送少量数据
- 它是面向用户的，这就鼓励了一个忠实于领域模型的结构
- 它是声明性的，而不是命令性的。也就是说：”我们在这里“，而不是”请做这件事“。
- 它迫使微前端间接地进行通信，而不是直接了解或依赖对方。

当使用路由作为我们在微前端之间的通信模式时，我们选择的路由构成了一个合同。在这种情况下，我们已经设定了一个想法，即一家餐厅可以在/restaurant/:restaurantId上查看，如果不更新所有引用它的应用程序，我们就不能更改该路由。鉴于这个合同的重要性，我们应该进行自动化测试来检查合同是否得到遵守。




### 常规内容

虽然我们希望我们的团队和我们的微前端尽可能独立，但有些东西应该是共同的。我们在前面写到共享组件库可以帮助实现微前端的之间的一致性，但对于这个小演示来说，组件库是多余的。因此，我们有一个小型的[通用内容库](https://github.com/micro-frontends-demo/content)，包括图片、JSON数据和CSS，它们通过网络提供所有微前端。

还有一件事，我们可以选择在微前端之间共享：库的依赖项。正如我们不久将描述的那样，依赖关系的重复是微前端的一个常见缺点。尽管在不同的应用程序之间共享这些依赖项有其自身的困难，但对于这个演示应用程序来说，还值得讨论一下如何实现。

第一步是选择要共享的依赖项。对我们编译的代码的快速分析表明，大约50%的包是由react和react-dom贡献的。除了它们的规模之外，这两个库是我们最”核心“的依赖，所以我们知道所有的微前端都可以从提取它们中受益。最后，这些都是稳定的、成熟的库，它们通常会在两个主要版本之间引入重大改变，所以跨应用的升级工作应该不会太困难。

至于实际的提取，我们需要做的就是我们的webpck配置中把这些库标记为[外部](https://webpack.js.org/configuration/externals/)，我们可以通过类似于前面描述的重新布线来做到这一点。

```
module.exports = {
    config.externals = {
        react: 'React',
        'react-dom': 'ReactDOM'
    }
    return config;
};
```

然后我们向每个index.html文件添加几个脚本标签，从我们的共享内容服务器中获取这两个库。

```
<body>
    <noscript>
        You need to enable Javascript to run this app.
    </noscript>
    <div id="root" />
    <script src="%REACT_APP_CONTENT_HOST%/react.prod-16.8.6.min.js"></script>
    <script src="%REACT_APP_CONTENT_HOST%/react-dom.prod-16.8.6.min.js"></script>
</body>
```

跨团队共享代码总是一件很棘手的事情。我们需要确保我们只分享我们真正想要共同的东西，以及我们想要在多个地方同时改变的东西。然而，如果我们谨慎对待我们分享的内容和不分享的内容，那么就可以获得真正的好处。



### 基础设施

该应用程序托管在AWS上，核心基础设施（S3存储桶、CloudFront分配、域、证书等），使用Terraform代码的[集中存储库](https://github.com/micro-frontends-demo/infra)一次性配置完成。每个微前端都有自己的资源库，在[Travis CI](https://travis-ci.org/micro-frontends-demo/)上有自己的持续部署管道，构建、测试并将其静态资产部署到这些S3存储桶中。这平衡了集中式基础设施管理的便利性和独立部署的灵活性。

注意，每个微前端（和容器）都有自己的桶。这意味着它可以自由支配里面的内容，而且我们不需要担心来自另一个团队或应用程序的对象名称冲突，或冲突的访问管理规则。

## 缺点(Downsides)

在这篇文章的开头，我们提到了微前端需要权衡，就像任何架构一样。我们提到的好处确实需要付出代价，我们将在此处介绍。

### 有效载荷大小(Payload size)



独立构建的javascript包会造成共同依赖项的重复，增加了我们必须通过网络发送给最终用户的字节数。例如，如果每个微前端都包含自己的React副本，那么我们就会迫使客户下载n次React。页面性能和用户参与度/转化率之间有[直接的关系](https://developers.google.com/web/fundamentals/performance/why-performance-matters/)，世界上大部分地区的互联网基础设施比高度发达城市的人习惯的慢得多，因此我们有很多理由关心下载量。


这个问题并不容易解决。我们希望让团队独立编译他们的应用程序，以便他们可以自主工作，而我们又希望以这样一种方式构建我们的应用程序，使它们能够共享共同的依赖关系，这两者之间存在着内在的矛盾。一种方法是将共同的依赖关系从我们的编译包中外部化，正如我们为演示应用程序所描述的那样。但是，一旦我们沿着这条路走下去，我们就重新引入了一些构建时耦合到我们的微前端。现在它们之间有一个隐含的契约，即“我们都必须使用这些依赖的确切版本”。如果一个依赖中发生重大变化，我们最终可能需要一个大型的协调升级工作和一个一次性的锁定发布事件。这也就是我们一开始想用微前端来避免的一切！

这种固有的紧张关系是一个困难的问题，但它也不全是坏消息。首先，即使我们选择对重复的依赖关系不做任何处理，每个单的页页面仍有可能比我们建立一个单一的单体前端时加载得更快。原因是，通过独立编译每个页面，我们有效地实现了我们自己的代码分割形式。在经典的单体应用中，当应用程序中的任何一个页面被加载时，我们通常会一次性下载每个页面的源代码和依赖项。通过独立构建，任何一个页面的加载将只下载该页面的源代码和依赖项。这可能会导致最初的页面加载速度更快，但随后的导航速度会更慢，因为用户不得不在每个页面上重新下载相同的依赖项。如果我们遵守纪律，不让我们的微前端因不必要的依赖项而膨胀，或者如果我们知道用户通常只关注应用程序中的一两个页面，那么即使存在重复的依赖项，我们也很可能在性能方面获得净收益。

在上一段中，有很多“可能”和“可能”，这突出了一个事实，即每个应用总是有它独特的性能特征。如果您想确定某个特定的变化会对性能产生什么样的影响，那么没有什么可以替代对实际情况的测量，最好是在生产中。我们已经看到一些团队为额外的几千字节的 JavaScript 而苦恼，但却要去下载许多兆字节的高分辨率图像，或者对一个非常慢的数据库运行昂贵的查询。因此，尽管考虑每个架构决策对性能的影响很重要，但请确保您知道真正的瓶颈在哪里。



### 环境差异



我们应该能够开发一个单个的微前端，而不需考虑其他团队正在开发的所有其他微前端。我们甚至可以在 "独立 "模式下运行我们的微前端，在一个空白页面上，而不是在生产中容纳它的容器应用程序中。这可以使开发变得更加简单，特别是当真正的容器是一个复杂的、遗留的代码库时，当我们使用微前端来做从旧世界到新世界的逐步迁移时，往往就是这种情况。然而，在一个与生产环境完全不同的环境中进行开发存在相关风险。如果我们开发时的容器与生产时的容器表现不同，那么我们可能会发现我们的微前端被损坏，或者在部署到生产时表现不同。特别值得关注的是，全局样式可能由容器或其他微前端带来。

这里的解决方案与我们必须担心环境差异的情况没有什么不同。如果我们在一个不类似于生产环境的本地开发，我们需要确保定期将我们的微前端集成并部署到类似生产的环境中，并且我们应该在这些环境中进行测试（手动和自动化）以尽早发现集成问题。这并不能完全解决这个问题，但最终这是我们必须权衡的另一个权衡：简化开发环境的生产力的提升是否值得集成问题的风险？答案将取决于项目的情况！



### 操作和管理的复杂性



最后一个缺点是与微服务直接并行。作为一个更加分布式的架构，微前端将不可避免地导致有更多的东西需要管理——更多的存储库、更多的工具、更多的构建/部署管道、更多的服务器、更多的域等等。所以在采用这样的架构之前，你有几个问题应该考虑：

- 你是否有足够的自动化，以可行地提供和管理额外的所需基础设施？
- 你的前端开发、测试和发布流程是否扩展到许多应用程序？
- 你是否能接受围绕工具和开发实践的决策变得更加分散和不可控？
- 你将如何确保你的许多独立的前端代码库的质量、一致性或治理达到最低水平？

我们也许可以再写一整篇文章来讨论这些话题。我们想说的主要一点是，当你选择微前端时，根据定义，你是选择创建许多小东西，而不是一个大东西。你应该考虑你是否有足够的技术和组织成熟度来采用这种方法而不造成混乱。


## 结论(Conslusion)

随着前端代码库多年来不断变得更加复杂，我们看到对更多可扩展架构的需求越来越大。我们需要能够划出清晰的界限，在技术和领域实体之间建立正确的耦合和内聚力水平。我们应该能够在独立、自主的团队中扩展软件交付。

虽然这不是唯一的方法，但我们已经看到了许多真实的案例，在这些案例中，微前端提供了这些好处，而且我们已经能够随着时间的推移，将这种技术逐渐应用于传统的代码库和新的代码库。无论微前端是否适合你和你的组织，我们只能希望这将是一个持续的趋势的一部分，即前端工程和架构被认真对待，我们知道它值得。


## 致谢(Acknowledgements)

非常感谢 Charles Korn、Andy Marks 和 Willem Van Ketwich 的全面审查和详细反馈。

还要感谢 Bill Codding、Michael Strasser 和 Shirish Padalkar 在 Thoughtworks 内部邮件列表中提供的意见。

还要感谢 Martin Fowler 的反馈，并感谢他在他的网站上为这篇文章提供了一个家。

最后，感谢 Evan Bottcher 和 Liauw Fendy 的鼓励和支持。

## 重大修订

2019年6月19日：发表了关于弊端的最后一篇文章

2019年6月17日：发表了带有实例的部分内容

2019年6月13日：发表了从造型到测试的部分。

2019年6月11日：发表了关于整合方法的部分。

2019年6月10日：发表了第一部分：涵盖福利

