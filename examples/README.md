# 例子

目前提供了若干例子方便接入参考。

## 目录结构

```
|---build
|    |---miniprogram.config.js // mp-webpack-plugin 插件配置
|    |---webpack.config.js // web 端 webpack 配置
|    |---webpack.mp.config.js // 小程序端 webpack 配置
|
|---dist 目标代码目录
|    |---web // web 端
|    |---mp // 小程序端
|
|---src // 源码目录
|    |---index // 主入口
|         |---main.js // web 端用主入口
|         |---main.mp.js // 小程序端用主入口
|
|---index.html // web 端输出页面
```

## 构建 web 端代码

```
npm run build
```

在 dist/web 目录下会输出 web 端目标代码。

## 构建小程序端代码

```
npm run mp
```

然后进入 dist/mp 目录下执行：

```
npm install
```

用开发者工具将 dist/mp 目录作为小程序项目导入之后，点击工具栏下的`构建 npm`，即可预览效果。

## demo 列表

* demo1：vue-router
* demo2：reduce-loader + vue-improve-loader
* demo3：内置组件
* demo4：vue-cli-plugin-kbone
* demo5：多页
* demo6：[omis](https://github.com/Tencent/omi/tree/master/packages/omis) + kbone
* demo7：多页 + 分包
* demo8：贪吃蛇小游戏（[omis](https://github.com/Tencent/omi/tree/master/packages/omis) + kbone）
* demo9：tabBar
* demo10：自定义组件
