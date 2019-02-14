---
title: 书评-YDKJS入门与进阶
date: 2019/01/31
updated: 2019/01/31
categories: 前端
thumbnail: https://raw.githubusercontent.com/getify/You-Dont-Know-JS/master/up%20%26%20going/cover.jpg
---

## 第一章：进入编程
### 代码
代码是一组告诉计算机要执行什么任务的特殊指令。通常保存在文本文件中，也可以在浏览器的开发者控制台中直接键入代码。
### 语句
一组单词，数字，和执行一种具体任务的操作符构成了一个语句。语句包含了变量、字面量、操作符。例如`a = b * 2;` 就是一个语句，而 a、b 是变量，2是字面量，=、* 是操作符。
### 表达式
一个表达式是一个引用，指向变量或值，或者一组用操作符组合的变量和值。
```
2 -> 字面量表达式
b -> 变量表达式
b * 2 -> 算数表达式
a = b * 2 -> 赋值表达式
alert(a) -> 函数表达式
```
### 执行一个程序
JavaScript引擎实际上在即时地自上而下的编译程序然后立即运行编译好的代码。

## 第二章：进入 JavaScript
### 值与类型
内建类型有：string，number，boolean，null，undefined，symbol，object。
通常我们可以用 typeof 来判断类型，但是 JS 中存在一个 bug，就是 typeof null 为 object，这个可以通过 Object.prototype.toString.call(null) 来进行判断。
### 值的比较
#### true 和 false
'', 0, -0, NaN, null, undefined, false 转 Boolean 为 false，其余都是true。
#### 等价性
不准确的描述：== 检查值的等价性而 === 检查值和类型两者的等价性。
合理的描述： == 在允许强制转化的条件下检查值的等价性， === 是在不允许强制转换的条件下检查值的等价性。
### this 标识符
```javascript
function foo() {
	console.log( this.bar );
}

var bar = "global";

var obj1 = {
	bar: "obj1",
	foo: foo
};

var obj2 = {
	bar: "obj2"
};

// --------

foo();				// "global"
obj1.foo();			// "obj1"
foo.call( obj2 );	// "obj2"
new foo();			// undefined
```
1. foo() 最终在非 strict 模式中将 this 设置为全局对象 —— 在 strict 模式中，this 将会是 undefined 而且你会在访问 bar 属性时得到一个错误 —— 所以 this.bar 的值是 global。
2. obj1.foo() 将 this 设置为对象 obj1。
3. foo.call(obj2) 将 this 设置为对象 obj2。
4. new foo() 将 this 设置为一个新的空对象。
### 原型
它的最简单的方法是使用称为`Object.create(..)`的内建工具。
```javascript
var foo = {
	a: 42
};

// 创建 `bar` 并将它链接到 `foo`
var bar = Object.create( foo );

bar.b = "hello world";

bar.b;		// "hello world"
bar.a;		// 42 <-- 委托到 `foo`
```
### 旧的与新的
有两种主要的技术可以将新的JavaScript特性“带到”老版本的浏览器中：填补和转译。
#### 填补
ES6定义了一个称为Number.isNaN(..)的工具，来为检查NaN值提供一种准确无误的方法
```javascript
if (!Number.isNaN) {
	Number.isNaN = function isNaN(x) {
		return x !== x;
	};
}
```
优秀填补例子： [ES5-Shim](https://github.com/es-shims/es5-shim) 和 [ES6-Shim](https://github.com/es-shims/es6-shim)
#### 转译
ES6增加了一个称为“默认参数值”的新特性。
```javascript
function foo(a = 2) {
	console.log( a );
}

foo();		// 2
foo( 42 );	// 42
```
转译器会将它转换为老版本语法
```javascript
function foo() {
	var a = arguments[0] !== (void 0) ? arguments[0] : 2;
	console.log( a );
}
```
优秀转译器例子： [Babel](https://babeljs.io) 和 [Traceur](https://github.com/google/traceur-compiler)

## 第三章：进入 YDKJS
### 作用域与闭包
### this 与对象原型
### 类型与文法
### 异步与性能
### ES6与未来
