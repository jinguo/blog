---
title: 学习-ES6基础知识点
date: 2019/05/16
categories: 前端
---

## 块级作用域
Q: 如何理解let和const，和var有什么区别？
A: let 和 const 没有变量提升、不能重复声明、不绑定全局作用域。
关联Q: 如何理解临时性死区?
关联A: JavaScript 引擎会扫描变量声明，会放入临时死区，如果发现如果在声明变量之前访问这些变量，会导致报错。
关联Q: 有没有研究过babel是怎么编译let的?
关联A: 如果有两个一样的变量名，会编译成不同的变量名，如果是循环中的申明，它会编译成两个方法，进行循环调用传入参数的方法。

## 箭头函数
Q: 箭头函数有什么特性?
A: 没有 this、没有 arguments、不能用 new 关键字调用、没有`new.target`、没有原型、没有 super。
关联Q: 如何确定箭头函数的this?
关联A: 通过查找作用域链来确定 this，也就是最近一层非箭头函数的this。

## Symbol类型
Q: Symbol 有哪些特性?
A: Symbol的值表示独一无二的值、typeof 类型是 Symbol、不能使用 new 命令、Symbol 一个对象，调用的是对象的`toString()`、相同参数的 Symbol 函数返回值不相等、Symbol值不能与其他类型进行运算、Symbol 可以用于对象的属性名
关联Q: 为什么不能使用new命令？
关联A: 因为 Symbol 是原始类型，不是对象。
关联Q: 可以用forin或者forof获取属性名吗？
关联A: 只能用`Object.getOwnPropertySymbols`获取属性名

## 迭代器与forof
Q: 什么是迭代器？
A: 迭代器就是一个拥有 next 方法的对象，每次调用`next()`会返回一个 value 和 done 属性的结果对象。
关联Q: forof可以遍历哪些对象，可以直接遍历一个普通对象吗？
关联A: 数组、Set、Map、类数组对象、Generator 对象、字符串，forof可以遍历的本质是对象中有 Symbol.iterator 属性。

## Promise
Q: 如何理解Promise?
A: 优点：链式调用，解决回调的控制反转和回调地狱的问题，缺点： 无法取消 Promise，无法得知 pending 状态
关联Q: Promise内部实现？
关联A: 一般会让你手写 Promise 的 某一个函数，可以自己去实现一下整个 Promise，这里就不贴代码了。

## class
Q: 分别用es5和es6的方式实现继承？
A: es6 比较简单，直接用 extends 实现。es5 比较典型的实现方式就是寄生组合继承，核心思想就是将父类的原型赋值给子类，并且将构造函数设置为子类。这里同样不贴代码了。

## 模块化
