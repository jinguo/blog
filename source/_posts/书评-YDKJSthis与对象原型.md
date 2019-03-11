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

如果传递 null 或 undefined 作为 call、 apply 或 bind 的 this 绑定参数，那么这些值会被忽略掉，取而代之的是默认绑定规则将适用于这个调用。这样会带来一些潜在的危险。如果你这样处理一些函数调用，而且那些函数确实使用了 this 引用，那么默认绑定规则意味着它可能会不经意间引用 global 对象。很显然，这样的陷阱会导致多种非常难诊断和追踪的 bug。安全的做法是通过 `Object.create(null)` 来作为绑定参数。

我们知道硬绑定是一种通过将函数强制绑定到特定的 this 上，来防止函数调用在不经意间退回到默认绑定的策略。问题是，硬绑定极大地降低了函数的灵活性，阻止我们手动使用隐含绑定或后续的明确绑定来覆盖 this。

软化绑定的思路就是为默认绑定提供不同的默认值，同时保持函数可以通过隐含绑定或明确绑定技术来手动绑定 this。

下面我们就分别展示硬绑定和软化绑定的代码实现
硬绑定
```javascript
if (!Function.prototype.bind) {
	Function.prototype.bind = function(oThis) {
		if (typeof this !== "function") {
			// 可能的与 ECMAScript 5 内部的 IsCallable 函数最接近的东西，
			throw new TypeError( "Function.prototype.bind - what " +
				"is trying to be bound is not callable"
			);
		}

		var aArgs = Array.prototype.slice.call( arguments, 1 ),
			fToBind = this,
			fNOP = function(){},
			fBound = function(){
				return fToBind.apply(
					(
						this instanceof fNOP &&
						oThis ? this : oThis
					),
					aArgs.concat( Array.prototype.slice.call( arguments ) )
				);
			};

		fNOP.prototype = this.prototype;
		fBound.prototype = new fNOP();

		return fBound;
	};
}
```
软绑定
```javascript
if (!Function.prototype.softBind) {
	Function.prototype.softBind = function(obj) {
		var fn = this,
			curried = [].slice.call( arguments, 1 ),
			bound = function bound() {
				return fn.apply(
					(!this ||
						(typeof window !== "undefined" &&
							this === window) ||
						(typeof global !== "undefined" &&
							this === global)
					) ? obj : this,
					curried.concat.apply( curried, arguments )
				);
			};
		bound.prototype = Object.create( fn.prototype );
		return bound;
	};
}
```

## 第三章：对象
对象是大多是 JS 程序依赖的基本构建块儿。

一个常见的错误论断是“JavaScript中的一切都是对象”。这明显是不对的。比如 string、number、boolean、null、undefined 这几个简单基本类型自身不是 object。

一个 JSON 安全的对象可以简单地这样复制：
```javascript
var newObj = JSON.parse( JSON.stringify( someObj ) );
```

浅拷贝可以使用 `Object.assign()`来实现。

所有的属性都用属性描述符来描述，通过`Object.getOwnPropertyDescriptor()`来查看，会发现一个对象不仅只有 value 值，还有 writable、enumerable 和 configurable。

当然我们也可以使用`Object.defineProperty()`来添加新属性或使用期望的性质来修改既存的属性。

writable 控制着改变属性值的能力。

configurable 控制着该对象是否可配置

enumerable 控制该对象是否可枚举

防止扩展：`Object.preventExtensions()`可以防止一个对象被添加新的属性，同时保留其他既存的对象属性。

封印：`Object.seal()`既不能添加更多的属性，也不能重新配置或删除既存属性，但是你依然可以修改他们的值。

冻结：`Object.freeze()`阻止任何对对象或对象直属属性的改变。

属性不必非要包含值 —— 它们也可以是带有 getter/setter 的“访问器属性”。
```javascript
var myObject = {
	// 为 `a` 定义 getter
	get a() {
		return this._a_;
	},

	// 为 `a` 定义 setter
	set a(val) {
		this._a_ = val * 2;
	}
};

myObject.a = 2;

myObject.a; // 4
```

你也可以使用 ES6 的 for..of 语法，在数据结构（数组，对象等）中迭代值，它寻找一个内建或自定义的 @@iterator 对象，这个对象由一个 next() 方法组成，通过这个 next() 方法每次迭代一个数据。
```javascript
var myObject = {
	a: 2,
	b: 3
};

Object.defineProperty( myObject, Symbol.iterator, {
	enumerable: false,
	writable: false,
	configurable: true,
	value: function() {
		var o = this;
		var idx = 0;
		var ks = Object.keys( o );
		return {
			next: function() {
				return {
					value: o[ks[idx++]],
					done: (idx > ks.length)
				};
			}
		};
	}
} );

// 手动迭代 `myObject`
var it = myObject[Symbol.iterator]();
it.next(); // { value:2, done:false }
it.next(); // { value:3, done:false }
it.next(); // { value:undefined, done:true }

// 用 `for..of` 迭代 `myObject`
for (var v of myObject) {
	console.log( v );
}
// 2
// 3
```

## 第四章：混合（淆）“类”的对象


