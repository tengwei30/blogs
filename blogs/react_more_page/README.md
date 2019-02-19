# webpack4 搭建 react 多页面应用

在往下看之前请确保nodejs > 9.X

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

## 初始化项目

```bash

# 创建项目
mkdir webpack_demo && cd webpack_demo && npm init -y
然后安装上面的目录结构进行项目创建

# 安装依赖并添加至 `package.json`
npm install --save webpack webpack-cli webpack-dev-server @babel/core @babel/preset-env @babel/preset-react @babel/preset-stage-0


