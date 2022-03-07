---
title: "浏览器API"
date: 2021-11-01
draft: false
summary: "浏览器API"
---

# 浏览器API

##  Window:beforeunload event

当浏览窗口关闭或者刷新时，会处罚beforeunload事件。当前页面不会直接关闭，可以点击确定按钮关闭或刷新，也可以取消关闭或刷新。

事件使网页能够触发一个确认对话框，询问用户是否真的要离开该页面，如果用户确认，浏览器将导航到新页面，否则导航将取消。

根据规范，要显示确认对话框，事件处理程序需要在时间上调用preventDefault()

但是请注意，并非所有浏览器都支持此方法，而有些浏览器需要事件处理程序实现两个遗留方法中的一个作为代替：

```
  1、将字符串分配给事件的returnValue属性
  2、从事件处理程序返回一个字符串
```

示例：

```
  window.addEventListener('beforeunload', (event) => {
    // Cancel the event as stated by the standard.
    event.preventDefault();
    // Chrome requires returnValue to be set.
    event.returnValue = '';
  });
```