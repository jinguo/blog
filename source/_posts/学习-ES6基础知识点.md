---
title: 学习-ES6基础知识点
date: 2019/05/16
categories: 前端
---

### 前言
本篇文章介绍面试中经常问的ES6基础知识点。文章中的关联问题可以在面试官问了问题后自己进行引申这些相关话题，从而引导面试官询问自己擅长的部分。后续会持续推出HTML知识点、CSS知识点、JS进阶知识点、webpack知识点、react知识点、组件设计相关知识点、浏览器相关知识点、网络相关知识点、算法相关知识点等文章进行全面的知识梳理。
<!-- more -->
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
关联A: 一般会让你手写 Promise 的 某一个函数，可以自己去实现一下整个 Promise，在文章最后有简单实现的代码。

## class
Q: 分别用es5和es6的方式实现继承？
A: es6 比较简单，直接用 extends 实现。es5 比较典型的实现方式就是寄生组合继承，核心思想就是将父类的原型赋值给子类，并且将构造函数设置为子类。在文章最后有简单实现的代码。

## 模块化
Q: 模块化有什么好处？为什么使用模块化？
A: 解决命名冲突、提供复用性、提高代码可维护性。
关联Q: 如何实现模块化？
关联A: 可以使用立即执行函数实现模块化。
关联Q: CommonJS和es Module有哪些区别？
关联A: CommonJS 是同步导入，用于服务端；ES module 是异步导入，用于浏览器； CommonJS 输出的是对于值的拷贝，ES module 输出的是对值的引用；CommonJS 模块是运行时加载，ES module 模块是编译时输出接口

## Promise 源码实现
```javascript
const PENDING = 'pending'
const RESOLVED = 'resolved'
const REJECTED = 'rejected'

function MyPromise(fn) {
  this.state = PENDING
  this.value = null
  this.resolvedCallbacks = []
  this.rejectedCallbacks = []

  function resolve(value) {
    if (this.state === PENDING) {
      this.state = RESOLVED
      this.value = value
      this.resolvedCallbacks.forEach(cb => cb(value))
    }
  }

  function reject(value) {
    if (this.state === PENDING) {
      this.state = REJECTED
      this.value = value
      this.rejectedCallbacks.forEach(cb => cb(value))
    }
  }

  try {
    fn(resolve, reject)
  } catch (e) {
    reject(e)
  }
}

MyPromise.prototype.then = function(onFulfilled, onRejected) {
  onFulfilled = typeof onFulfilled === 'function' ? onFulfilled : v => v
  onRejected = typeof onRejected === 'function' ? onRejected : e => {throw e}
  const that = this
  let promise2 = new Promise((resolve, reject) => {
    if (that.state === PENDING) {
      that.resolvedCallbacks.push(() => {
        setTimeout(() => {
          try {
            const x = onFulfilled(that.value)
            resolvePromise(promise2, x, resolve, reject)
          } catch(e) {
            reject(e)
          }
        })
      })
      that.rejectedCallbacks.push(() => {
        setTimeout(() => {
          try {
            const x = onRejected(that.value)
            resolvePromise(promise2, x, resolve, reject)
          } catch(e) {
            reject(e)
          }
        })
      })
    }
    if (this.state === RESOLVED) {
      setTimeout(() => {
        try {
          const x = onFulfilled(this.value)
          resolvePromise(promise2, x, resolve, reject)
        } catch(e) {
          reject(e)
        }
      })
    }
    if (this.state === REJECTED) {
      setTimeout(() => {
        try {
          const x = onRejected(this.value)
          resolvePromise(promise2, x, resolve, reject)
        } catch(e) {
          reject(e)
        }
      })
      
    }
  })
  return promise2
}

function resolvePromise(promise2, x, resolve, reject) {
  if(promise2 === x) {
    reject(new TypeError('Error'))
  }
  if (x && typeof x === 'object' || typeof x === 'function') {
    let called;
    try {
      let then = x.then
      if (typeof then === 'function') {
        then.call(x, y => {
          if (called) return
          called = true
          resolvePromise(promise2, y, resolve, reject)
        }, e => {
          if (called) return
          called = true
          reject(e)
        })
      } else {
        if (called) return
        called = true
        resolve(x)
      }
    } catch(e) {
      if (called) return
      called = true
      reject(e)
    }
  } else {
    resolve(x)
  }
}

MyPromise.prototype.catch = function(onRejected) {
  return this.then(null, onRejected)
}

MyPromise.prototype.finally = function(callback) {
  return this.then(value => {
    return Promise.resolve(callback()).then(() => value)
  }, err => {
    return Promise.resolve(callbakck()).then(() => {throw err})
  })
}

MyPromise.all = function(promises) {
  return new Promise((resolve, reject) => {
    let index = 0
    let result = []
    if (promises.length === 0) {
      resolve(result)
    } else {
      for(let i = 0; i < promises.length; i++) {
        Promise.resolve(promises[i]).then(data => {
          result[i] = data
          if (++index === promises.length) resolve(result)
        }, err => {
          reject(err)
          return
        })
      }
    }
  })
}

MyPromise.race = function(promises) {
  return new Promise((resolve, reject) => {
    if (promises.length === 0) {
      return
    } else {
      for(let i = 0; i < promises.length; i++) {
        Promise.resolve(promises[i]).then(data => {
          resolve(data)
          return
        }, err => {
          reject(err)
          return
        })
      }
    }
  })
}
```

## 寄生组合继承
```javascript
  function Parent(value) {
    this.val = value
  }
  Parent.prototype.getValue = function() {
    console.log(this.val)
  }
  function Child(value) {
    Parent.call(this, value)
  }
  Child.prototype = Object.create(Parent.prototype)
  Child.prototype.constructor = Child
  
  const child = new Child(1)

```