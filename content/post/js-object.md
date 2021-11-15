---
title: "js Object构造函数"
date: 2021-11-01
draft: false
summary: "js Object构造函数"
---

#  Object

object构造函数创建一个对象包装器。

## Object.freeze()

Object.freeze()方法可以冻结一个对象。一个被冻结的对象再也不能被修改；冻结了一个对象则不能向这个对象添加新的属性，不能删除已有属性，不能修改该对象已有属性的可枚举型、可配置性、可写性，以及不能修改已有属性的值。此外，冻结一个对象后对该对象的原型也不能被修改。freeze()返回和和传入的参数相同的对象。

  const obj = {
    prop: 42
  }

  Object.freeze()

  obj.prop = 33

  console.log(obj.prop)  42