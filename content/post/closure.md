---
title: "闭包"
date: 2021-09-13
draft: false
summary: "闭包"
---

# 闭包

闭包表示的是一个封闭的内存空间。每个函数都被创建的时候，都有一个与之关联的闭包。

每个函数被创建的时候，都有一个与之关联的闭包被同时创建。在新创建的函数内部，如果引用了外部作用域中的变量，那么这些变量都会被添加到该函数的闭包中。

外部函数调用之后其变量对象本应该被摧毁，但闭包的存在是我们仍然可以访问外部函数的变量对象，这就是闭包的重要概念。

    function outer() {
        let a = 1;           // 定义一个内部变量
        return function() { 
            return a         // 返回a变量值
        }
    }

    let b = outer()
    console.log(b())         // 1

创建闭包最常见方式，就是在一个函数内部创建另一个参数。

    function func() {
        let [a, b] = [1, 2];
        function closure() {
            return a + b
        }
        return closure
    }

闭包的作用域链包含它自己的作用域，以及包含它的函数的作用域和全局作用域

闭包的注意事项：
    通常，函数的作用域及其所有变量都会在函数执行完毕后被销毁。但是，在创建了一个闭包以后，这个函数的作用域就会一直保存到闭包不存在为止。

    function makeAdder() {
        return function(y) {
            return x + y
        }
    }

    let add5 = makeAdder(5)
    let add10 = makeAdder(10)

    console.log(add5(2))   // 7
    console.log(add10(2))   // 12

    // 释放对闭包的引用
    add5 = null
    add10 = null

从上述代码中可以看到add5和add10都是闭包。他们共享相同的函数定义，但是保存了不同的环境。在add5的环境中，x则为5。而在add10中，x则为10。最后通过null释放了add5和add10对闭包的引用

在javascript中，如果一个对象不再被引用，那么这个对象就会被垃圾回收机制回收

如果两个对象相互引用，而不再被第三者所引用，那么这两个相互引用的对象也会被回收

闭包只能取得包含函数汇总的任何变量的最会一个值

    function addFunc() {
        let arr = []
        for (let i = 0; i < 10; i++) {
            arr[1] = function() {
                return i
            }
        }
        return arr
    }

这时候i的值为10

想要保存在循环过程中的没一个i的值，需要在匿名函数外部再套用一个匿名函数，在这个匿名函数中定义另一个变量并且立即执行来保存i的值

    function arrFunc() {
        let arr = []
        for (let i = 0; i < 10; i++) {
            arr[i] = function(num) {
                return function() {
                    return num
                }
            }(i)
        }
        return arr
    }
    console.log(arrFunc()[1])())

这时最内部的匿名函数访问的是num的值，所有数组中10个匿名函数的返回值就是1-10

闭包的this对象

    let name = 'window'
    let obj = {
        name: 'object',
        getName: function() {
            return function() {
                return this.name
            }
        }
    }
    console.log(obj.getName()())

在上面这段代码中，obj.getName()()实际上是在全局作用域中调用了匿名函数，this指向了window

这里要理解函数名与函数功能是分隔开的，不要认为函数在哪里，其内部的this就指向哪里。

window才是匿名函数功能执行的环境

如果想使this指向外部函数的执行环境