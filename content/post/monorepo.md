---
title: "Monorepo"
date: 2021-09-07
draft: false
summary: "Monorepo-大型前端项目的代码管理方式"
---

# Monorepo-大型前端项目的代码管理方式

## Monorepo

Monorepo是管理项目代码的一个方式，指在一个项目仓库(repo)中管理多个模块/包(package)，不同于常见的每个模块建一个repo。

目前有不少大型开源项目采用了这种方式，[如Babel](https://link.segmentfault.com/?url=https%3A%2F%2Fgithub.com%2Fbabel%2Fbabel):

    回购结构如何？
    Babel存储库作为由许多npm包组成的monorepo进行管理。
    
还有[create-react-app](https://link.segmentfault.com/?url=https%3A%2F%2Fgithub.com%2Ffacebook%2Fcreate-react-app)，[react-router](https://link.segmentfault.com/?url=https%3A%2F%2Fgithub.com%2FReactTraining%2Freact-router)等。可以看到这些项目的第一级目录的内容以脚手架为主，主要内容都在packages目录中、分多个package进行管理。

    ├── packages
    |   ├── pkg1
    |   |   ├── package.json
    |   ├── pkg2
    |   |   ├── package.json
    ├── package.json

monorepo最主要的好处是**统一的工作流**和**Code Sharing**。比如我想看一个package的代码、了解某段逻辑，不需要找它的repo，直接就在当前repo；当某个需求要修改多个package时，不需要分别到各自的repo进行修改、测试、发版或npm link，直接在当前repo修改，统一测试、统一发版。**只要搭建一套脚手架，就能管理（构建、测试、发布）多个package。**

不好的方面则主要是repo的体积较大。特别是，因为每个package理论上都是独立的，所以每个package都维护着自己的dependencies，而很大的可能性，package之间有不少相同的依赖，而这就可能使install时出现重复安装，使本来就很大的node_modules继续膨胀（我称这为[依赖爆炸]）。

基于对以上的理解，我认为当项目到一定的复杂度，需要且可以划分模块、但模块间联系紧密，比较适用monorepo组织代码。

最长最常见的monorepo解决方案是[Lerna](https://link.segmentfault.com/?url=https%3A%2F%2Fgithub.com%2Flerna%2Flerna)和yarn的workspaces特性。其中lerna是一个独立的包，其官网的介绍是：
    
    一个优化工作流程的工具，围绕着用git和npm管理多包仓库。

上面提到的Babel，create-react-app等都是使用lerna进行管理的。在项目repo以lerna.json声明packages后，lerna为项目提供了统一的repo依赖安装(lerna bootstrap)，同意的执行package script(leran run)，统一的npm发版(lerna publish)等特性。对于[依赖爆炸] 的问题，lerna在安装依赖时提供了 --hoist 选项，相同的依赖，会[提升]到repo根目录安装，但...太鸡肋了，lerna直接以字符串对比dependency的版本号，完全相同才提升，semver约定在这并不起作用。

具体的使用方法移步Lerna官网：[https://lerna.js.org](https://lerna.js.org)

而使用yarn作为包管理器的同学，可以在package.json中以workspaces字段声明packages，yarn就会以monorepo的方式管理packages。相比lerna，yarn突出的是对依赖的管理，包括packages的相互依赖、packages对第三方的依赖，yarn会以semver约定来分析dependencies的版本，安装依赖时更快、占用体积更小；但欠缺了[统一工作流]方面的实现。

yarn官网对workspace的详细说明：[Workspaces | Yarn](https://link.segmentfault.com/?url=https%3A%2F%2Fyarnpkg.com%2Flang%2Fen%2Fdocs%2Fworkspaces%2F)

lerna和yarn-workspace并不是只能选其一，大多monorepo即会使用lerna又会在package.json声明workspaces。这样的话，无论你的包管理器是npm还是yarn，都能发挥monorepo的优势；要是包管理是yarn，lerna就会把依赖安装交给yarn处理。

## 参考资料

[babel/monorepo.md at master · babel/babel](https://link.segmentfault.com/?url=https%3A%2F%2Fgithub.com%2Fbabel%2Fbabel%2Fblob%2Fmaster%2Fdoc%2Fdesign%2Fmonorepo.md)

[EHOIST_ROOT_VERSION warning hoisting question · Issue #872 · lerna/lerna](https://link.segmentfault.com/?url=https%3A%2F%2Fgithub.com%2Flerna%2Flerna%2Fissues%2F872)


## Others

### git-submodule

通过Git子模块，可以在当前repo中包含其它repos、作为当前repo的子目录使用，同时能够保持repos之间的独立

    # 在当前repo添加一个子模块
    git submodule add git@github.com:xxx/xxx.git path/to/xxx
    
可以在.gitmodule文件中看到当前repo有哪些submodule，分别的name，branch等。

    # clone含有submodule的repo后：
    # 初始化gitsubmodule信息
    git submodule init
    # 更新submodule，相当于git pull
    git submodule update
    
修改子模块文件后，在当前repo执行git status只会看到有模块的changes，而不是具体子模块文件：

    diff --git a/path/to/submodule b/path/to/submodule
    --- a/path/to/submodule
    +++ b/path/to/submodule
    @@ -1 +1 @@
    -Subproject commit xxxxxxx
    +Subproject commit xxxxxxx-dirty
        
dirty表示子模块的修改还不是commit。如果子模块的修改commit后，这个改动就会是具体的commit id。

子模块的其他commit，pull等操作，还是到其目录下，按普通repo操作即可。

#### 参考资料

Git - 子模块：
    [https://git-scm.com/book/zh/v...](https://link.segmentfault.com/?url=https%3A%2F%2Fgit-scm.com%2Fbook%2Fzh%2Fv2%2FGit-%25E5%25B7%25A5%25E5%2585%25B7-%25E5%25AD%2590%25E6%25A8%25A1%25E5%259D%2597)

man git-submodule

git submodules - Git diff says subproject is dirty - Stack Overflow
    [https://stackoverflow.com/que...](https://link.segmentfault.com/?url=https%3A%2F%2Fstackoverflow.com%2Fquestions%2F4873980%2Fgit-diff-says-subproject-is-dirty)


### git-bisect

git有一个以二分法帮助定位问题的命令--bisect。

    # 开始二分查找问题
    git bisect start
    # 标记当前有问题
    git bisect bad
    # 标记那个commit或tag是没问题的
    git bisect good v1.0.0
    
    # 此时git会checkout两个点之间的某个commit
    # 如果此时还是有问题
    git bisect bad
    # 如果此时没有问题
    git bisect good
    # 接着git会checkout下一个【有问题】和【没问题】之间的commit
    
    # 直接定位到问题，git会提示： xxxxxxxx is first bad commit
    
#### 参考资料

使用 Git 调试：[https://git-scm.com/book/zh/v...](https://link.segmentfault.com/?url=https%3A%2F%2Fgit-scm.com%2Fbook%2Fzh%2Fv1%2FGit-%25E5%25B7%25A5%25E5%2585%25B7-%25E4%25BD%25BF%25E7%2594%25A8-Git-%25E8%25B0%2583%25E8%25AF%2595)

