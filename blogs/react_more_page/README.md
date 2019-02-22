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
	


