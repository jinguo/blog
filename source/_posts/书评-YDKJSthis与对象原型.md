## 第一章：this 是什么
this 不是编写时绑定，而是运行时绑定。它依赖于函数调用的上下文条件。 this 绑定与函数声明的位置没有任何关系，而与函数被调用的方式紧密相连。

this 既不是函数自身的引用，也不是函数词法作用域的引用。

this 实际上是在函数被调用时建立的一个绑定，它指向什么是完全由函数被调用的调用点来决定的。

首先展示一下 this 的动机和用途:
```javascript
function identify() {
	return this.name.toUpperCase();
}

function speak() {
	var greeting = "Hello, I'm " + identify.call( this );
	console.log( greeting );
}

var me = {
	name: "Kyle"
};

var you = {
	name: "Reader"
};

identify.call( me ); // KYLE
identify.call( you ); // READER

speak.call( me ); // Hello, I'm KYLE
speak.call( you ); // Hello, I'm READER
```
与使用 this 相反地，你可以明确地将环境对象传递给 `identify()` 和 `speak()`.
```javascript
function identify(context) {
	return context.name.toUpperCase();
}

function speak(context) {
	var greeting = "Hello, I'm " + identify( context );
	console.log( greeting );
}

identify( you ); // READER
speak( me ); // Hello, I'm KYLE
```
this 机制提供了更优雅的方式来隐含地传递一个对象引用，导致更加干净的API设计和更容易的复用。

## 第二章：this 豁然开朗
首先我们展示一下调用栈和调用点：
```javascript
function baz() {
    // 调用栈是: `baz`
    // 我们的调用点是 global scope（全局作用域）

    console.log( "baz" );
    bar(); // <-- `bar` 的调用点
}

function bar() {
    // 调用栈是: `baz` -> `bar`
    // 我们的调用点位于 `baz`

    console.log( "bar" );
    foo(); // <-- `foo` 的 call-site
}

function foo() {
    // 调用栈是: `baz` -> `bar` -> `foo`
    // 我们的调用点位于 `bar`

    console.log( "foo" );
}

baz(); // <-- `baz` 的调用点
```
在分析代码来寻找（从调用栈中）真正的调用点时要小心，因为它是影响 this 绑定的唯一因素。

那么调用点是如何决定在函数执行期间 this 指向哪里的。

我们有四种 this 绑定规则：默认绑定、隐含绑定、明确绑定、new 绑定。

判定 this 的步骤：

1. 我们可以先判断函数是通过 new 被调用的嘛？如果是， this 就是新构建的函数。

2. 然后判断函数是通过 call 或 apply 被调用，甚至是隐藏在 bind 硬绑定中吗？ 如果是，this 就是那个被明确指定的对象。

3. 然后判断函数是通过环境对象被调用的嘛？ 如果是，this 就是那个环境对象。

4. 否则，使用默认的 this。如果在 strict mode 下，就是 undefined，否则就是 global 对象。