# webpack 构建优化

构建打点：speed-measure-webpack-plugin，它能够测量出在你的构建过程中，每一个 Loader 和 Plugin 的执行时长

优化策略：缓存、多核、抽离以及拆分

## 缓存

* 大部分 Loader 都提供了 cache 配置项
* 如果没提供，则使用 cache-loader

## 多核：happypack

## 抽离

* webpack-dll-plugin
* Externals

## 拆分：微服务

## 提升体验工具

* progress-bar-webpack-plugin
* webpack-build-notifier
* webpack-dashboard
