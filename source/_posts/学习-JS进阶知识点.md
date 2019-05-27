---
title: 学习-JS基础知识点
date: 2019/05/27
categories: 前端
---

### 前言
本篇文章介绍面试中经常问的JS进阶知识点，主要是模拟实现JS中的常用函数。<!-- more -->文章中的关联问题可以在面试官问了问题后自己进行引申这些相关话题，从而引导面试官询问自己擅长的部分。后续会持续推出HTML知识点、CSS知识点、webpack知识点、react知识点、组件设计相关知识点、浏览器相关知识点、网络相关知识点、算法相关知识点等文章进行全面的知识梳理。

### call简单实现
```javascript
Function.prototype.myCall = function(context) {
  if (typeof this !== 'function') {
    throw new Error('Error');
  }
  context = context || window;
  context.fn = this;
  const args = [...arguments].slice(1);
  const result = context.fn(...args);
  delete context.fn;
  return result;
}
```

### apply简单实现
```javascript
Function.prototype.myApply = function(context, arr) {
  if (typeof this !== 'function') {
    throw new Error('Error');
  }
  context = context || window;
  context.fn = this;
  let result;
  if (!arr) {
    result = context.fn();
  } else {
    result = context.fn(...arr);
  }
  delete context.fn;
  return result;
}
```

### bind简单实现
```javascript
Function.prototype.myBind = function(context) {
  if (typeof this !== 'function') {
    throw new Error('Error');
  }
  const self = this;
  const args = [...arguments].slice(1);
  const fNOP = function() {};
  const f = function() {
    const bindArgs = [...arguments].slice(1);
    return self.apply(this instanceof f ? this : context, args.concat(bindArgs))
  }
  fNOP.prototype = this.prototype;
  f.prototype = new fNOP();
  return f;
}
```

### new简单实现
```javascript
function objectFactory() {
  let obj = {};
  const Con = [].shift.call(arguments);
  obj.__proto__ = Con.prototype;
  const result = con.apply(obj, arguments);
  return typeof result === 'object' ? result : obj; 
}
```