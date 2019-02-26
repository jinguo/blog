---
title: 书评-YDKJS作用域与闭包
date: 2019/02/18
updated: 2019/02/18
categories: 前端
thumbnail: https://raw.githubusercontent.com/getify/You-Dont-Know-JS/master/scope%20&%20closures/cover.jpg
---
## 第一章 什么是作用域？
几乎所有语言的最基础模型之一就是在变量中存储值，并且在稍后取出或修改这些值的能力。
作用域就是定义如何在某些位置存储变量，以及如何在稍后找到这些变量。

### 编译器理论
编译的三个步骤

1.分词/词法分析：将一连串的字符打断成有意义的片段，称为 token，例子：`var a = 2;=> var, a, =, 2, ;`。

2.解析：将一个 token 的流转换成一个嵌套元素的树，它综合地表示了程序的语法结构。这棵树称为抽象语法树（AST）

3.代码生成：这个处理将抽象语法树转换为可执行的代码。

JavaScript 的编译和其他语言不同，不是提前发生在一个构建的步骤中。对于 JavaScript 来说，在许多情况下，编译发生在代码执行前的仅仅几微秒之内。为了确保最快的性能，JS 引擎使用了 JIT等等

### 理解作用域
引擎：负责从始至终的编译和执行我们的 JavaScript 程序。
编译器：引擎的朋友之一；处理所有解析和代码生成的活儿。
作用域：引擎的另一个朋友；收集并维护一张所有被声明的标识符的列表，并对当前执行中的代码如何访问这些变量强制实施一组严格的规则。

对于 `var a = 2;`这个语句，编译器将会这样处理：

1.遇到`var a`，编译器让作用域去查看对于这个特定的作用域集合，变量 a 是否已经存在了。如果是，编译器就忽略这个声明并继续前进。否则，编译器就让作用域去为这个作用域集合声明一个称为 a 的新变量。

2.然后编译器为引擎生成稍后要执行的代码，来处理赋值 `a = 2`。引擎 运行的代码首先让作用域 去查看在当前的作用域集合中是否有一个称为 a 的变量可以访问。如果有，引擎就使用这个变量。如果没有，引擎会喊出一个错误。

### 嵌套的作用域
嵌套的作用域就像一个代码块儿或函数被嵌套在另一个代码块儿或函数中一样，作用域被嵌套在其他的作用域中。

遍历嵌套作用域的简单规则：引擎从当前执行的作用域开始，在那里查找变量，如果没有找到，就向上走一级继续查找，如此类推。如果到了最外层的全局作用域，那么查找就会停止，无论它是否找到了变量。

## 第二章 词法作用域
作用域的工作方式有两种占统治地位的模型。其中第一种是最常见的词法作用域，另一种是动态作用域。

JavaScript 所采用的作用域模型是词法作用域。

词法作用域是在词法分析时被定义的作用域。

欺骗词法作用域：eval 函数和 with 关键字的使用。

欺骗词法作用域的使用会导致更低下的性能。因为 JS 引擎的一些优化原理都归结在实质上在进行词法分析时可以静态地分析代码，并提前决定所有的变量和函数声明在什么位置。如果发现一个 eval 或是 with，它实质上就不得不假定自己知道的所有标识符的位置可能是无效的。

## 第三章 函数与块儿作用域

### 函数作用域
函数中的作用域也就是声明的每一个函数都为自己创建了一个作用域。
立即调用函数表达式可以生成一个自己的作用域。

### 块儿作用域
ES6引入了 let 和 const，它们都会创建一个块儿作用域。
let 做出的声明不会在他们所出现的块儿的作用域中提升。

## 第四章 提升
在代码的任何部分被执行之前，所有的声明，变量和函数，都会首先被处理。以下是两个例子
```javascript
a = 2;
var a;
console.log( a ); // 2
```
```javascript
console.log( a ); // undefined
var a = 2;
```
函数声明会被提升，但是函数表达式不会。
```javascript
foo();
function foo() {
	console.log( a ); // undefined
	var a = 2;
}
```
```javascript
foo(); // 不是 ReferenceError， 而是 TypeError! 因为变量 foo 被提升了，但是值为 undefined

var foo = function bar() {
	// ...
};
```
函数声明和变量声明都会被提升。但是函数会首先被提升，然后才是变量。
```javascript
foo(); // 1
var foo;
function foo() {
	console.log( 1 );
}
foo = function() {
	console.log( 2 );
};
```
这个代码被引擎解释执行为
```javascript
function foo() {
	console.log( 1 );
}
foo(); // 1
foo = function() {
	console.log( 2 );
};
```
这里的一个注意点就是 var foo 是一个重复的声明，会被忽略。

## 第五章 作用域闭包
闭包就是函数能够记住并访问它的词法作用域，即使当这个函数在它的词法作用域之外执行时。
以下例子是一个典型的闭包案例
```javascript
function foo() {
	var a = 2;
	function bar() {
		console.log( a );
	}
	return bar;
}
var baz = foo();
baz(); // 2 -- 哇噢，看到闭包了，伙计。
```
我们生活中经常使用到的闭包
```javascript
function wait(message) {
	setTimeout( function timer(){
		console.log( message );
	}, 1000 );

}
wait( "Hello, closure!" );
```
还有模块就是利用了闭包的力量，我们看下面的代码
```javascript
function CoolModule() {
	var something = "cool";
	var another = [1, 2, 3];

	function doSomething() {
		console.log( something );
	}

	function doAnother() {
		console.log( another.join( " ! " ) );
	}

	return {
		doSomething: doSomething,
		doAnother: doAnother
	};
}

var foo = CoolModule();

foo.doSomething(); // cool
foo.doAnother(); // 1 ! 2 ! 3
```