## **this**

- 在javascript 中我相信好多小伙伴和一样对this感到迷惑，最近在学习js的设计模式，突然发现对this傻傻分不清的话根本不知道如何进行下去，所以今天整理一下，分享出来

### 在JavaScript中 this 总是指向一个对象，而这个对象是在运行时基于函数执行环境动态绑定的（注意：非函数执行时的环境）
、

### 指向
- 作为对象的方法调用
- 作为普通函数调用
- 构造器调用
- Function.prototype.call 或 Function.prototype.apply调用

1、**作为对象的方法调用**
 - 作为对象方法调用时this指向的是该对象（大白话就是谁调用了这个函数这个this就指向谁）
	```js
	const obj = {
		a: 'tengwei',
		getA: function(){
			console.log(this === obj) // true（obj调用了getA函数）
		}
	}
	obj.getA()
	```
		- 扩展
	
	```js
	// eg 1:
	const obj = {
		a: 'tengwei',
		getB: function(){
			setTimeout(function() {
				console.log(this === obj) // false	此时this指向浏览器中最外层的window（因为this在全局函数setTimeout中的匿名函数里，并没有某个对象对它进行显示调用）
			})
		}
	}
	obj.getB()

	// eg 2:
	const obj = {
		a: 'tengwei',
		getC: function(){
			console.log(this === obj)
			setTimeout(() => {
				// 箭头函数并没有this 的概念，所以遇到箭头函数就会去找它外层的函数调用，所以此时this应该和getC的this是一致的
				console.log(this === obj) // true	此时this指向它的宿主对象
			})
		}
	}
	obj.getC()

	// eg 3:
	const obj = {
		a: 'tengwei',
		getD: () => {
			// 箭头函数并没有this 的概念，所以遇到箭头函数就会去找它外层的函数调用～而外层就是window对象啦
			console.log(this === obj) // false 此时this指向浏览器中最外层的window
		}
	}
	obj.getD()

	// eg 4:
	const obj = {
		a: 'tengwei',
		getE: function() {
			return function () {
				console.log(this === obj) // false 返回一个匿名函数，未被显性调用，此时this 指向window, 如果是箭头函数就是true
			}
		}
	}
	obj.getE()()
	```
2、**作为普通函数方式调用**
	### this 作为普通函数的方式调用的时候， 此时的this总是指向全局对象

```js
const obj = {
	a: 'tengwei',
	getA: function(){
		console.log(this === obj) // false(this 作为普通函数调用，指向window)
	}
}
const B = obj.getA
B()
```

3、**构造器调用**
### 构造函数就是个函数，this指向的是个对象，有屁股想this也指不到构造函数中去，所以构造函数中的this指向的是创建的实例对象无疑。（构造函数和普通函数的区别就在于构造函数的调用需要用 new 操作符）

- 用new创建对象时发生的事情：
	
	- 创建一个Object对象实例
	- 将构造函数的执行对象赋给新生成的这个实例
	- 执行构造函数中的代码
	- 返回新生成的对象实例

```js
	function A () {
		this.name = 'tengwei'
	}
	const B = new A()
	console.log(B.name) // tengwei 此时的this 指向实例出来的对象B
```

特殊情况：
- 当构造器函数显式的返回一个Object类型的对象的情况
```js
const A = function() {
	this.name = 'tengwei';
	return {
		name: 'xiaosong'
	}
	console.log(this)
}
const B = new A()
console.log(B.name) // xiaosong 此时的this指向的是返回的这个对象，而不是实例出来的对象
```

### Function.prototype.call 和 Function.prototype.apply
- call 和 apply的如何使用就不说啦，来说说它们的用途
	- 改变函数内部this的指向
		```js
			const obj1 = {
				name: 'tengwei'
			}
			const obj2 = {
				name: 'xiaosong'
			}
			window.name = 'window'
			const getName = function() {
				console.log(this.name)
			}
			getName()	// window
			getName.call(obj1) // tengwei
			getName.call(obj2) // xiaosong
		```
- 扩展：
	- 现在大部分的浏览器都内置了 Function.prototype.bind（写过react的小伙伴都知道） 来指定函数内部this的指向，下面说说bind的原理
		- 调用bind方法的时候，会先把function函数的引用保存下来，然后返回一个新的匿名函数，当执行这个function函数的时候，实际上先执行的是刚刚返回的这个新的匿名函数，而匿名函数中会添加call/aply的方法来执行原来的function函数，并将this 指向传入的对象

	```js
		Function.prototype.bind = function(context) {
			const _self = this	// 保存原函数
			return function () {	// 返回一个新函数
				return _self.apply(context, arguments)	// 执行新函数的时候，改变新函数this 的指向指到context上
			}
		}

		eg：
		const obj = {
			name: 'tengwei'
		}
		const func = function () {
			console.log(this.name)
		}.bind(obj)
		func()
	```
