## 手撸 webpack react 框架

### 第一步 

- 创建项目
  - 执行 mkdir fleece && npm init -y 创建项目

  - 执行 mkdir build config server src 创建基本文件夹

  - 执行 cd src && touch index.html main.js 创建入口文件

  - 在/src中执行 mkdir views && touch index.js

- 安装依赖

  - 执行 npm install --save webpack webpack-cli webpack-dev-server @babel/core @babel/preset-env @babel/preset-react @babel/preset-stage-0 babel-loader react react-dom react-router-dom redux react-redux redux-thunk html-webpack-plugin

- 运行项目

  - 在build 中 执行 touch webpack.config.js, 在根目录下执行 touch .babelrc

  ```json
  <!-- .babelrc -->
  <!-- 
    babel preset env 负责将ES6语法转换成ES5
    babel preset react 负责将JSX语法转化为JavaScript
   -->
  {
    "presets": ["@babel/preset-env", "@babel/preset-react"]
  }
  ```
  ```js
  <!-- webpck.config.js -->
    const webpack = require('webpack');
    const path = require('path');
    const HtmlWebpackPlugin = require('html-webpack-plugin');


    module.exports = {
      mode: 'development',
      entry: './src/main.js',
      output: {
        path: path.resolve(__dirname, '/dist'),
        publicPath: '/',
        filename: 'bundle.js'
      },
      module: {
        rules: [
          {
            test: /\.(js|jsx)$/,
            exclude: /node_modules/,
            use: ['babel-loader']
          }
        ]
      },
      resolve: {
        extensions: ['.js', '.jsx']
      },
      plugins: [
        new HtmlWebpackPlugin({ template: path.join(__dirname, '../src/index.html') }),
        new webpack.HotModuleReplacementPlugin()
      ],
      devServer: {
        host: '127.0.0.1',
        port: '8888',
        contentBase: './dist',
        hot: true,
        open: true
      }
    };
  ```
  ```json
  <!-- package.json/scripts -->
  "start": "webpack-dev-server --config ./build/webpack.config.js",
  ```
  - 执行 npm start 然后返回127.0.0.1:8888 如果能看到I am react 证明第一步启动成功


- 添加依赖
  - 执行npm install --save style-loader css-loader sass-loader file-loader 

  ```js
  <!-- 替换webpack.config.js => module => rules数组里面的配置 -->
    [
      {
        test: /\.(js|jsx)$/,
        exclude: /node_modules/,
        // ?cacheDirectory 表示传给 babel-loader 的参数，用于缓存 babel 编译结果加快重新编译速度
        use: ['babel-loader?cacheDirectory'],
      },
      {
        // 命中 SCSS 文件
        test: /\.scss$/,
        // 使用一组 Loader 去处理 SCSS 文件。
        // 处理顺序为从后到前，即先交给 sass-loader 处理，再把结果交给 css-loader 最后再给 style-loader。
        use: ['style-loader', 'css-loader', 'sass-loader'],
        // 排除 node_modules 目录下的文件
        exclude: path.resolve(__dirname, 'node_modules'),
      },
      {
        // 对非文本文件采用 file-loader 加载
        test: /\.(gif|png|jpe?g|eot|woff|ttf|svg|pdf)$/,
        use: ['file-loader'],
      },
      {
        test:/\.(png|jpg|gif|jpeg|svg)$/,
        use:[
            {
                loader: "url-loader",
                options: {
                    name: "[name].[hash:5].[ext]",
                    limit: 1024, // size <= 1kib
                    outputPath: "img"
                }
            }
        ]
      }
    ]
  ```

- 生产环境的构建 && 代码拆分压缩
  - 执行 cd build && touch webpack.dev.js webpack.prod.js
  - 执行 npm install --save webpack-merge clean-webpack-plugin uglifyjs-webpack-plugin mini-css-extract-plugin optimize-css-assets-webpack-plugin

```js
  // 替换 webpack.config.js
  const path = require('path');
  const HtmlWebpackPlugin = require('html-webpack-plugin');


  module.exports = {
    entry: './src/main.js',
    output: {
      path: path.resolve(__dirname, '../dist'),
      filename: '[name].[chunkhash].js'
    },
    module: {
      rules: [
        {
          test: /\.(js|jsx)$/,
          exclude: /node_modules/,
          // ?cacheDirectory 表示传给 babel-loader 的参数，用于缓存 babel 编译结果加快重新编译速度
          use: ['babel-loader?cacheDirectory'],
        },
        {
          // 命中 SCSS 文件
          test: /\.scss$/,
          // 使用一组 Loader 去处理 SCSS 文件。
          // 处理顺序为从后到前，即先交给 sass-loader 处理，再把结果交给 css-loader 最后再给 style-loader。
          use: ['style-loader', 'css-loader', 'sass-loader'],
          // 排除 node_modules 目录下的文件
          exclude: path.resolve(__dirname, 'node_modules'),
        },
        {
          // 对非文本文件采用 file-loader 加载
          test: /\.(gif|png|jpe?g|eot|woff|ttf|svg|pdf)$/,
          use: ['file-loader'],
        },
        {
          test:/\.(png|jpg|gif|jpeg|svg)$/,
          use:[
              {
                  loader: "url-loader",
                  options: {
                      name: "[name].[hash:5].[ext]",
                      limit: 1024, // size <= 1kib
                      outputPath: "img"
                  }
              }
          ]
        }
      ]
    },
    resolve: {
      extensions: ['.js', '.jsx', '.json'],
    },
    plugins: [
      new HtmlWebpackPlugin({
        template: path.join(__dirname, '../src/index.html'),
        minify: {
          collapseWhitespace: true, //把生成的 index.html 文件的内容的没用空格去掉，减少空间
        },
        hash: true
      }),
    ],
  };
```
```js
  // webpack.dev.js
  const merge = require('webpack-merge')
  const baseWebpackConfig = require('./webpack.config.js')
  const webpack = require('webpack')

  module.exports = merge(baseWebpackConfig, {
    mode: "development",
    devtool: 'inline-source-map',
    watch: true,
    watchOptions: {
      // 不监听的文件或文件夹，支持正则匹配
      // 默认为空
      ignored: /node_modules/,
      // 监听到变化发生后会等300ms再去执行动作，防止文件更新太快导致重新编译频率太高
      // 默认为 300ms  
      aggregateTimeout: 300,
      // 判断文件是否发生变化是通过不停的去询问系统指定文件有没有变化实现的
      // 默认每秒轮询1000次
      poll: 1000
    },
    devServer: {
      contentBase: false,
      historyApiFallback: true,
      compress: true,
      inline: true,
      hot: true,
      open: true,
      quiet: true,
      host: '127.0.0.1',
      port: '8888'
    },
    plugins: [
      new webpack.NamedModulesPlugin(),
      new webpack.HotModuleReplacementPlugin(),
    ]
  });
```
```js
  // webpack.prod.js
  const merge = require('webpack-merge')
  const baseWebpackConfig = require('./webpack.config.js')
  const path = require('path')
  const webpack = require('webpack')
  const CleanWebpackPlugin = require('clean-webpack-plugin')
  const UglifyJSPlugin = require('uglifyjs-webpack-plugin')
  const MiniCssExtractPlugin = require('mini-css-extract-plugin')
  const OptimizeCSSAssetsPlugin = require('optimize-css-assets-webpack-plugin')

  function resolve (dir) {
    return path.join(__dirname, '..', dir)
  }

  module.exports = merge(baseWebpackConfig, {
    mode: 'production',
    output: {
      path: resolve('dist'),
      filename: "js/[name].[chunkhash].js",
      publicPath: '/',
      // 公共模块js单独放一个js文件夹
      chunkFilename: 'js/[name].[id].[chunkhash].js'
    },
    optimization: {
      splitChunks: {
        cacheGroups: {
          // 复用的文件，单独抽离 后续再优化此配置
          commons: {
            name: 'commons',
            chunks: 'all', 
            minChunks: 2,
            minSize: 1,
            priority: 0 
          },
          // 提取 node_modules 中代码
          vendor: { 
            name: 'vendor',
            test: /[\\/]node_modules[\\/]/,
            chunks: 'all', 
            priority: 10
          }
        }
      },
      /**
      * 提取 webpack 运行时代码
      * optimization.runtimeChunk 直接置为 true 或设置 name
      * webpack会添加一个只包含运行时(runtime)额外代码块到每一个入口
      * 注：这个需要看场景使用，会导致每个入口都加载多一份运行时代码
      */
      runtimeChunk: {
        name: 'manifest'
      },
      minimizer: [
        new UglifyJSPlugin({
          uglifyOptions: {
            cache: true,
            parallel: true,
            warnings: false,
            comments: false,
            sourceMap: true,
            compress: {
              // 移除 warning
              warnings: false,
              // 移除 console
              drop_console: true
            }
          }
        }),
        new OptimizeCSSAssetsPlugin({
          assetNameRegExp: /\.css\.*(?!.*map)/g,
          cssProcessor: require('cssnano'),
          cssProcessorOptions: {
              discardComments: { removeAll: true },
              safe: true,
              autoprefixer: false
          },
          canPrint: true
        })
      ],
      splitChunks: {
        minSize: 30000,
        minChunks: 2,
        maxAsyncRequests: 5,
        maxInitialRequests: 3,
        name: false,
        cacheGroups: {
          vendor: {
            test: /[\\/]node_modules[\\/]/,
            name: 'vendor',
            chunks: 'initial',
          }
        }
      }
    },
    plugins: [
      new CleanWebpackPlugin({
        root: path.resolve(__dirname, '../dist/'),
      }),
      // 根据模块的相对路径生成一个四位数的hash作为模块id
      new webpack.HashedModuleIdsPlugin(),
      // 压缩抽离样式
      new MiniCssExtractPlugin({
        filename: 'css/[name].[chunkhash].css',
        chunkFilename: 'css/[name].[chunkhash].css'
      }),
    ]
  });
```
```json
  //  替换package.json/scripts里面的指令
  
  "start": "webpack-dev-server --open --config ./build/webpack.dev.js",
    "build": "webpack --config ./build/webpack.prod.js"
```


