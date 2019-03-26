---
title: 学习-ES6之基础知识点
date: 2019/03/14
categories: 前端
thumbnail: https://raw.githubusercontent.com/getify/You-Dont-Know-JS/master/types%20&%20grammar/cover.jpg
---

1.如何理解let和const，和var有什么区别？
jinguo: let用来声明变量，const 声明一个只读的常量，一旦声明，常量的值就不能改变。而且const只声明不赋值就会报错。区别：let和const都不存在变量提升，具有暂时性死区，不允许重复声明。

3.下面循环中变量i是不是在每一次循环都是新的变量？如果是，它怎么知道上一轮循环的值？
```javascript
var a = [];
for(let i=0;i< 5;i++) {
  a[i] = function () {
    console.log(i);
  }
}
a[6](); // 6
```
jinguo: 变量在每一次循环都是全新的变量。因为 JavaScript 引擎内部会记住上一轮循环的值。

4.如何理解暂时性死区？
jinguo: 在代码块内，使用let命令声明变量之前，该变量都是不可用的。在这语法上，称为暂时性死区。因为只要块级作用域存在let命令，它所声明的变量就绑定了这个区域，不再受外界影响。

5.为什么需要块级作用域？
jinguo: 可以防止内层变量覆盖外层变量的值，也可以防止循环变量泄露成全局变量。

6.有没有研究过let是怎么编译成es5的？
jinguo: 如果有两个一样的变量名，会编译成不同的加了_的变量名，如果是循环，它会编译成两个方法，进行循环调用传入参数的方法。

7.怎么理解箭头函数？
jinguo: 箭头函数没有this，所以需要通过查找作用域链来确定this的值。箭头函数没有arguments，箭头函数不能通过new关键字调用，没有new.target，也没有原型，没有super。

8.可以通过for of遍历对象和数组嘛？如果不能那怎么做可以让它遍历？
jinguo: 可以遍历数组，不能遍历对象，需要在对象上加入Symbol.iterator属性，而数组已经默认添加了。

9.