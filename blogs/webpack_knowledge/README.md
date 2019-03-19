# webpack 知识点整理

## 	基础点
#### 		模块化
##### 定义
- 模块化是指把一个复杂的系统分解到多个模块以方便编码。

##### 优点
- 可以避免命名空间的冲突（想想很久以前的网页开发，可能两个库用同一个名称的情况）
- 更好的管理项目依赖和版本
- 更方便的控制依赖的加载顺序

#### CommonJS
##### **核心思想**
- 通过 require 方法来同步地加载依赖的其他模块，通过 module.exports 导出需要暴露的接口

##### **加载原理**
- CommonJS模块就是一个脚本文件，require命令第一次加载该脚本时就会执行整个脚本，然后在内存中生成该模块的一个说明对象。以后用到这个模块时，就会到对象的exports属性中取值。即使再次执行require命令，也不会再次执行该模块，而是到缓存中取值。CommonJS模块是加载时执行，即脚本代码在require时就全部执行。一旦出现某个模块被“循环加载”，就只输出已经执行的部分，没有执行的部分不会输出。

	循环加载：指的是a脚本的执行依赖b脚本，b脚本的执行依赖a脚本
```js
	{
	  id: '', //模块名，唯一
	  exports: { //模块输出的各个接口
		...
	  },
	  loaded: true, //模块的脚本是否执行完毕
	  ...
	}
```

##### **导入导出代码**
```bash
// 导入
const moduleA = require('./moduleA');
// 导出
module.exports = moduleA.someFunc;
```
##### **优点**
- 代码可复用于 Node.js 环境下并运行，例如做同构应用；
- 通过 NPM 发布的很多第三方模块都采用了 CommonJS 规范。

##### **缺点**
- 无法直接运行在浏览器环境下，必须通过工具转换成标准的 ES5 

##### AMD
##### **核心思想**
- 为了解决针对浏览器环境的模块化问题 与CommonJS的最大不同在于它采用异步的方式去加载依赖的模块

##### **导入导出代码**
```bash
// 定义一个模块
define('module', ['dep'], function(dep) {
  return exports;
});

// 导入和使用
require(['module'], function(module) {
});
```

##### **优点**
- 可直接运行在浏览器环境和Node 环境
- 可异步加载
- 可并行加载多个依赖

##### **缺点**
- JavaScript 运行环境没有原生支持 AMD，需要先导入实现了 AMD 的库后才能正常使用

#### ES6 模块化
- 为了取代CommonJS 和 AMD，成为浏览器和服务器的通用模块解决方案

##### **导入导出代码**
```bash
// 导入
import { readFile } from 'fs';
import React from 'react';
// 导出
export function hello() {};
export default {
  // ...
};
```

#### 		Webpack
- 问题：webpack 和 grunt、gulp 之间的区别，选用webpack的原因

#### **Loader**
#### **Loader**
- webpack 只能理解 JavaScript 和 JSON 文件。loader 让 webpack 能够去处理其他类型的文件，并将它们转换为有效模块

##### **注意**
- use 属性的值需要是一个由 Loader 名称组成的数组，Loader 的执行顺序是由后到前的
- loader 可以通过query 和Object（options）的方式进去传值

#### **Plugin**
##### **定义**
- 用来扩展webpack功能的，基本处理一下loader无法实现的功能，包括包括：打包优化，资源管理，注入环境变量等等

## 配置

1. ### ** Entry **
	entry是配置模块的入口，可抽象成输入，Webpack 执行构建的第一步将从入口开始***搜寻及递归解析***出所有入口依赖的模块。

	##### **Entry类型**
	- String/array/Object  demo 请移步官网，可以是其中一种或多种类型组合

	##### Chunk 名称
	- Webpack 会为每个生成的 Chunk 取一个名称，Chunk 的名称和 Entry 的配置有关：
		- 如果 entry 是一个 string 或 array，就只会生成一个 Chunk，这时 Chunk 的名称是 main
		- 如果 entry 是一个 object，就可能会出现多个 Chunk，这时 Chunk 的名称是 object 键值对里键的名称。

	##### ** 动态配置Entry **
	```bash
		// 同步函数
		entry: () => {
		  return {
			a:'./pages/a',
			b:'./pages/b',
		  }
		};

		// 异步函数
		entry: () => {
		  return new Promise((resolve)=>{
			resolve({
			   a:'./pages/a',
			   b:'./pages/b',
			});
		  });
		};
	```

2. ### **Output** 输出

	#### filename -- 配置输出文件的名称 -- filename: '[name].js'
	-   [name] 代表用内置的 name 变量去替换[name]，每个要输出的 Chunk 都会通过这个函数去拼接出输出的文件名称
	- 内置变量：
	|  变量名 |  含义 |
	| :------------: | :------------: |
	| id  |  Chunk 的唯一标识，从0开始  |
	| name  | Chunk 的名称  |
	| hash  |  Chunk 的唯一标识的 Hash 值 |
	| chunkHash  |  Chunk 内容的 Hash 值 |
	
	- **Hash 和 Chunkhash 的长度是可指定的，[Hash:8] 代表取8位 Hash 值，默认是20位**
