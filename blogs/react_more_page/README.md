# webpack4 搭建 react 多页面应用

在往下看之前请确保nodejs > 8.X

先上个凉的吃着

## 目录结构

```bash
.
├── dist                   # 编译结果
├── build                  # 配置文件
├── cli                    # sh脚本存放处
├── src                    # 
    ├── assets             # Store images, icons, ...
    ├── public             # UI components
    ├── services           # 项目开发中一些常用的方法 主要存放和请求有关的
    ├── statics            # 静态文件
    ├── template           # 模版文件
    ├── utils              # 项目开发中一些常用的方法 其实我觉得可以和services 放在一块，但是个人习惯还是分开啦
    └── views              # pages
├── .eslintrc              # Eslint config
├── .babelrc               # babel config
├── .gitignore             #
└── package.json           #
```

## 开始撸代码

1. 如何创建多页面应用，首先你需要先有一个单页面应用进行改装，这里我就不再敲单页面应用的案例啦～git上有[demo](https://github.com/tengwei30/blogs_demo/tree/master/webpack_demo "github单页面")，接下来的多页面也是基于上述单页面改装

1. 开始大工程
	[开箱即用react 多页面](https://github.com/tengwei30/blogs_demo/tree/master/webpack_demo_more)
	上面是多页面应用的demo 喜欢的请给个star
	
	在这不多说～只给大家说一下一些想法和注意点
	1. 单改多 === 大家都知道就是将入口改成多个，但是具体怎么实现？
		- demo中/build/pageEntry.js 文件就是专门处理多入口多页面的，我们借助glob 这个node 模型来获取入口文件 [glob](https://github.com/isaacs/node-glob) 
		- 获取完所有的目标文件接下来就是循环将所有的文件拼到入口上去啦（多页面页面配置的配置也是相同的道理）记得将webpack.base.config.js、webpack.dev.config.js、webpack.prod.config.js下面对应的代码
	1. 到此为止 基本一个简单的多页面应用已经完成啦，但是现在这样的代码并不能满足我们日常的开发，因为这样的话我们每一次的开发都要在src/views/下面去创建一个文件夹，然后一个一个的创建文件，哇～想想都好恶心，如果我要是这样直接给我同事去用，我相信他们会骂死我～好～为了不让队友骂，那我就想想怎么让他们一键生成
	1. 借助[shelljs](https://github.com/shelljs/shelljs)模块来简化模版的创建
		- 请移步到demo中cli文件夹下面的createHtml.sh
		-首先我们先在src/template下面建一个通用的模版
		- 然后就是就是写一个脚本实现指令执行的时候把template复制到对应的文件夹下面就可以啦～本项目中使用 npm run cli-create 来创建开发界面
		- 嗯嗯～现在队友们不骂我啦～哈哈哈
	1. 结果用了没几天，队友又开始抱怨啦～需求多啦～页面多啦，所以每回启动项目的时候所有的界面都会启动，可是每回我只调试某一个页面，不需要启动这么多～怎么办怎么办怎么办～好吧～那就干呗，还能怎么办
		- 请移步到demo中build文件夹下面的selectPage.js 文件 好吧～不知道咋说～大家自己看吧
	1. 现在基本上多页面的应用大部分主要用于原生应用内嵌H5页面，所以这就涉及到native和H5的通信和H5页面在移动端的调试问题啦
		- native和H5的通信可以查看[JSBridge](https://github.com/lzyzsd/JsBridge)
		- H5页面在移动端的调试 我在项目中引用的是eruda.js（用的时候别忘了在production环境要禁用哦！）
	1. 最后一点，移动端的适配demo中用的是vw，不过实际开发中你可以直接去写px；px转vw也已做好，关于vw的移动端适配大家可以google一下～
		
		
	
## 总结语～
	在放一遍[demo链接](https://github.com/tengwei30/blogs_demo/tree/master/webpack_demo_more)吧，喜欢个可以给个星星，灰常感谢，接下来会继续优化，启动之后可以选择打开某一个界面，也不需要手动去输入文件地址啦～哈哈哈～期待的话给star吧
	


