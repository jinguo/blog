---
title: 书评-YDKJS类型与文法
date: 2019/03/14
categories: 前端
thumbnail: https://raw.githubusercontent.com/getify/You-Dont-Know-JS/master/types%20&%20grammar/cover.jpg
---

## 第一章：类型
JavaScript 定义了七种内建类型：null、undefined、boolean、number、string、object、symbol。

typeof 操作符可以检测给定值的类型。
```javascript
typeof undefined     === "undefined"; // true
typeof true          === "boolean";   // true
typeof 42            === "number";    // true
typeof "42"          === "string";    // true
typeof { life: 42 }  === "object";    // true

// 在 ES6 中被加入的！
typeof Symbol()      === "symbol";    // true
```

typeof 操作符是有 bug 的， 那就是 typeof null === "object"; // true 这个 bug 已经存在了近二十年，而且好像永远也不会被修复了，以为修复它可能会引起更多的 bug。

如果你想用 null 类型来测试 null 值，需要一个符合条件:
```javascript
var a = null;

(!a && typeof a === "object"); // true
```

特别需要注意的是 typeof function(){} 是 "function"。

typeof 操作符总是返回字符串。

## 第二章：值
Array 有两个点需要注意：
1. 如果用 delete 删除 Array 上的元素，他只是移除这个值槽，不会更新 length 属性。
2. 如果一个可以被强制转换为10进制 number 的 string 值被用作键的话，它会认为你想使用 number 索引而不是一个 string 键！

类数组转换成数组一般使用以下两种方式：
1. Array.prototype.slice.call(arguments)
2. Array.from(arguments);

JavaScript 的 number 的实现基于"IEEE 754"标准，通常被称为浮点。Javascript 明确使用了这个标准的“双精度”

针对 0.1 + 0.2 === 0.3 // false 这个问题的解释简单说是因为 0.1 和 0.2 的二进制表现形式是不精准的，所以他们相加时，结果不是精确的0.3.

测试是否是整数可以使用 Number.isInteger(),模拟实现如下
```javascript
if (!Number.isInteger) {
	Number.isInteger = function(num) {
		return typeof num == "number" && num % 1 == 0;
	};
}
```

测试是否是安全整数可以使用 Number.isSafeInteger(),模拟实现如下
```javascript
if (!Number.isSafeInteger) {
	Number.isSafeInteger = function(num) {
		return Number.isInteger( num ) &&
			Math.abs( num ) <= Number.MAX_SAFE_INTEGER;
	};
}
```

表达式 void __ 会“躲开”任何值，所以这个表达式的结果总是值 undefined。它不会修改任何已经存在的值；只是确保不会有值从操作符表达式中返回来。

在 ES6 中，有一个新工具可以用于测试两个值的绝对等价性，而没有任何这些例外。它称为 Object.is(..):
```javascript
var a = 2 / "foo";
var b = -3 * 0;

Object.is( a, NaN );	// true
Object.is( b, -0 );		// true

Object.is( b, 0 );		// false
```
Object.is()的模拟实现如下：
```javascript
if (!Object.is) {
	Object.is = function(v1, v2) {
		// 测试 `-0`
		if (v1 === 0 && v2 === 0) {
			return 1 / v1 === 1 / v2;
		}
		// 测试 `NaN`
		if (v1 !== v1) {
			return v2 !== v2;
		}
		// 其他情况
		return v1 === v2;
	};
}
```

## 第三章：原生类型
原生类型实际上就是内建函数，有以下几种 String()、Number()、Boolean()、Array()、Object()、Function()、RegExp()、Date()、Error()、Symbol()

## 第四章：强制转换
在观察代码时如果一个类型转换明显是有意为之的，那么它就是“明确强制转换”，而如果这个类型转换是做为其他操作的不那么明显的副作用发生的，那么它就是“隐含强制转换”。
```javascript
var a = 42;

var b = a + "";			// 隐含强制转换

var c = String( a );	// 明确强制转换
```

JSON.stringify(..) 工具在遇到 undefined、function、和 symbol 时将会自动地忽略它们。如果在一个 array 中遇到这样的值，它会被替换为 null（这样数组的位置信息就不会改变）。如果在一个 object 的属性中遇到这样的值，这个属性会被简单地剔除掉。

但如果你试着 JSON.stringify(..) 一个带有循环引用的 object，就会抛出一个错误。

如果你打算 JSON 字符串化一个可能含有非法 JSON 值的对象，或者如果这个对象中正好有不适于序列化的值，那么你就应当为它定义一个 toJSON() 方法，返回这个 object 的一个 JSON 安全 版本。

宽松等价是==操作符，而严格等价是===操作符。

关于这两个操作符的一个非常常见的误解是：“==检查值的等价性，而===检查值和类型的等价性。”

正确的描述是：“==允许在等价性比较中进行强制转换，而===不允许强制转换”。

比较 null 和 undefiined，宽松等价两个相等

比较 number 和 string，把 string 转换成 number

比较 boolean 和其他，把 boolean 转换成 number

比较 object 和非 object，把 object 调用 ToPrimitive() 方法

## 第五章：文法
JavaScript文法有相当多的微妙之处，我们作为开发者应当比平常多花一点儿时间来关注它。一点儿努力可以帮助你巩固对这个语言更深层次的知识。

语句和表达式在英语中有类似的概念 —— 语句就像句子，而表达式就像短语。表达式可以是纯粹的/自包含的，或者他们可以有副作用。

JavaScript文法层面的语义用法规则（也就是上下文），是在纯粹的语法之上的。例如，用于你程序中不同地方的{ }可以意味着块儿，object 字面量，（ES6）解构语句，或者（ES6）被命名的函数参数。

JavaScript有几种类型的错误，但很少有人知道它有两种类别的错误：“早期”（编译器抛出的不可捕获的）和“运行时”（可以try..catch的）。所有在程序运行之前就使它停止的语法错误都明显是早期错误，但也有一些别的错误。