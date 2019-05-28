---
title: 学习-JS进阶知识点
date: 2019/05/28
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
function myNew() {
  let obj = {};
  const Con = [].shift.call(arguments);
  obj.__proto__ = Con.prototype;
  const result = con.apply(obj, arguments);
  return typeof result === 'object' ? result : obj; 
}
```

### instanceof简单实现
```javascript
function myInstanceof(left, right) {
  while(true) {
    if (left === null || left === undefined) return false
    if (left.__proto__ === right.prototype) return true
    left = left.__proto__
  }
}
```

### debounce简单实现
```javascript
function debounce(func, wait, immediate) {
  let timeout;
  return function() {
    const context = this;
    const args = arguments;
    if (timeout) clearTimeout(timeout)
    if (immediate) {
      const callNow = !timeout;
      timeout = setTimeout(function() {
        timeout = null;
      }, wait)
      if (callNow) func.apply(context, args)
    } else {
      timeout = setTimeout(function() {
        func.apply(context, args)
      }, wait);
    }
  }
}
```

### throttle简单实现
```javascript
function throttle(func, wait, options) {
  var timeout, context, args, result;
  var previous = 0;
  if (!options) options = {};

  var later = function() {
    previous = options.leading === false ? 0 : new Date().getTime();
    timeout = null;
    func.apply(context, args);
    if (!timeout) context = args = null;
  }

  return function() {
    var now = new Date().getTime();
    if (!previous && options.leading === false) previous = now;
    var remaining = wait - (now-previous);
    context = this;
    args = arguments;
    if (remaining <= 0 || remaining > wait) {
      if (timeout) {
        clearTimeout(timeout);
        timeout = null
      }
      previous = now;
      func.apply(contet, args);
      if (!timeout) context = args = null;
    } else if (!timeout && options.trailing !== false) {
      timeout = setTimeout(later, remaining);
    }
  }
}
```

### 乱序简单实现
```javascript
function shuffle(a) {
  for (let i = a.length; i; i--) {
    let j = Math.floor(Math.random() * i);
    [a[i - 1], a[j]] = [a[j], a[i - 1]];
  }
  return a;
}
```