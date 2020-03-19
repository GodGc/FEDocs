# webpack

## webpack 提取多页面（项目）都引用的库 vendor

## webpack 分模块打包、多入口打包/多页面打包？

使用 `optimization` 代码分割

  ```javascript
    optimization: {
      splitChunks: {
        cacheGroups: {
          // 打包业务中公共代码  priority低，后提取
          // 只提取common和styles目录下的文件，打包成common.js和common.css
          // 否则会把任意目录下的文件也打包进common里，造成污染
          common: {
            name: "common",
            chunks: "initial",
            test: /[\\/]src[\\/](common|styles)/,
            minSize: 1,
            priority: 0,
            minChunks: 3,
          },
          // 打包node_modules中的文件 priority更高，先提取
          vendor: {
            name: "vendor",
            test: /[\\/]node_modules[\\/]/,
            chunks: "initial",
            priority: 10,
            minChunks: 2,
          }
        }
      },
      runtimeChunk: { name: 'manifest' } // 运行时代码
    },
  ```
