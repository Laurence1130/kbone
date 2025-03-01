## 进阶

因为 Web 端和小程序端的差异性，此文档提供了一些进阶用法、优化方式和开发建议。

* [多页开发](#多页开发)
* [使用小程序内置组件](#使用小程序内置组件)
* [使用小程序自定义组件](#使用小程序自定义组件)
* [自定义 app.js 和 app.wxss](#自定义-appjs-和-appwxss)
* [扩展 dom/bom 对象和 API](#扩展-dombom-对象和-api)
* [代码优化](#代码优化)
* [开发建议](#开发建议)

### 多页开发

对于多页面的应用，在 Web 端可以直接通过 a 标签或者 location 对象进行跳转，但是在小程序中则行不通；同时 Web 端的页面 url 实现和小程序页面路由也是完全不一样的，因此对于多页开发最大的难点在于如何进行页面跳转。

1. 修改 webpack 配置

对于多页应用，此处和 Web 端一致，有多少个页面就需要配置多少个入口文件。如下例子，这个应用中包含 page1、page2 和 page2 三个页面：

```js
// webpack.mp.config.js
module.exports = {
    entry: {
        page1: path.resolve(__dirname, '../src/page1/main.mp.js'),
        page2: path.resolve(__dirname, '../src/page2/main.mp.js'),
        page3: path.resolve(__dirname, '../src/page3/main.mp.js'),
    },
    // ... other options
}
```

2. 修改 webpack 插件配置

`mp-webpack-plugin` 这个插件的配置同样需要调整，需要开发者提供各个页面对应的 url 给 kbone。

```js
module.exports = {
    origin: 'https://test.miniprogram.com',
    entry: '/page1',
    router: {
        page1: ['/(home|page1)?', '/test/(home|page1)'],
        page2: ['/test/page2/:id'],
        page3: ['/test/page3/:id'],
    },
    // ... other options
}
```

其中 origin 即 window.location.origin 字段，使用 kbone 的应用所有页面必须同源，不同源的页面禁止访问。entry 页面表示这个应用的入口 url。router 配置则是各个页面对应的 url，可以看到每个页面可能不止对应一个 url，而且这里的 url 支持参数配置。

有了以上几个配置后，就可以在 kbone 内使用 a 标签或者 location 对象进行跳转。kbone 会将要跳转的 url 进行解析，然后根据配置中的 origin 和 router 查找出对应的页面，然后拼出页面在小程序中的路由，最后通过小程序 API 进行跳转（利用 wx.redirectTo 等方法）。

> PS：通过多页可以支持使用小程序 tabBar，还可以使用小程序分包与预下载机制，更多详细配置信息可以[点此查看](./miniprogram.config.js)。

> PS：具体例子可参考 [demo5](../examples/demo5) 和 [demo7](../examples/demo7)

### 使用小程序内置组件

需要明确的是，如果没有特殊需求的话，请尽量使用 html 标签来编写代码，使用内置组件时请按需使用。这是因为绝大部分内置组件外层都会被包裹一层自定义组件，如果自定义组件的实例数量达到一定量级的话，理论上是会对性能造成一定程度的影响，所以对于 view、text、image 等会被频繁使用的内置组件，如果没有特殊需求的话请直接使用 div、span、img 等 html 标签替代。

部分内置组件可以直接使用 html 标签替代，比如 input 组件可以使用 input 标签替代。目前已支持的可替代组件列表：

* `<input />` --> input 组件
* `<input type="radio" />` --> radio 组件
* `<input type="checkbox" />` --> checkbox 组件
* `<label><label>` --> label 组件
* `<textarea></textarea>` --> textarea 组件
* `<img />` --> image 组件
* `<video></video>`  --> video 组件
* `<canvas></canvas>` --> canvas 组件

还有一部分内置组件在 html 中没有标签可替代，那就需要使用 `wx-component` 标签或者使用 `wx-` 前缀，基本用法如下：

```html
<!-- wx-component 标签用法 -->
<wx-component behavior="picker" mode="region" @change="onChange">选择城市</wx-component>
<wx-component behavior="button" open-type="share" @click="onClickShare">分享</wx-component>

<!-- wx- 前缀用法 -->
<wx-picker mode="region" @change="onChange">选择城市</wx-picker>
<wx-button open-type="share" @click="onClickShare">分享</wx-button>
```

如果使用 `wx-component` 标签表示要渲染小程序内置组件，然后 behavior 字段表示要渲染的组件名；其他组件属性传入和官方文档一致，事件则采用 vue 的绑定方式。

`wx-component` 或 `wx-` 前缀已支持内置组件列表：

* cover-image 组件
* cover-view 组件
* scroll-view 组件
* view 组件
* icon 组件
* progress 组件
* text 组件
* button 组件
* editor 组件
* picker 组件
* slider 组件
* switch 组件
* navigator 组件
* camera 组件
* image 组件
* live-player 组件
* live-pusher 组件
* map 组件
* ad 组件
* official-account 组件
* open-data 组件
* web-view 组件

> PS：button 标签不会被渲染成 button 内置组件，如若需要请按照上述原生组件使用说明使用。

> PS：原生组件的表现在小程序中表现会和 web 端标签有些不一样，具体可[参考原生组件说明文档](https://developers.weixin.qq.com/miniprogram/dev/component/native-component.html)。

> PS：原生组件下的子节点，div、span 等标签会被渲染成 cover-view，img 会被渲染成 cover-image，如若需要使用 button 内置组件请使用 `wx-component` 或 `wx-` 前缀。

> PS：如果将插件配置 runtime.wxComponent 的值配置为 `noprefix`，则可以用不带前缀的方式使用内置组件。

> PS：具体例子可参考 [demo5](../examples/demo3)

### 使用小程序自定义组件

需要明确的是，如果可以使用 Web 端组件技术实现的话请尽量使用 Web 端技术（如 vue、react 组件），使用自定义组件请按需使用。这是因为自定义组件外层会被包裹上 kbone 的自定义组件，而当自定义组件的实例数量达到一定量级的话，理论上是会对性能造成一定程度的影响。

要在 kbone 中使用自定义组件，需要将所有自定义组件和其依赖放到一个固定的目录，这个目录可以自己拟定，假设这个目录为 `src/custom-components`：

1. 修改 webpack 插件配置

在 `mp-webpack-plugin` 这个插件的配置中的 generate 字段内补充 **wxCustomComponent**，其中 root 是组件根目录，即上面提到的目录：`src/custom-component`，usingComponents 则用来配置要用到的自定义组件。

```js
module.exports = {
    generate: {
        wxCustomComponent: {
            root: path.join(__dirname, '../src/custom-components'),
			usingComponents: {
				'comp-a': 'comp-a/index',
				'comp-b': {
                    path: 'comp-b/index',
                    props: ['propa', 'propb'],
                    events: ['someevent'],
                },
			},
		},
    },
    // ... other options
}
```

usingComponents 里的声明和小程序页面的 usingComponents 字段类似。键为组件名，值可以为组件相对 root 字段的路径，也可以是一个配置对象。这个配置对象的 path 为组件相对路径，props 表示要这个组件会被用到的 properties，events 表示这个组件会被监听到的事件。

2. 将自定义组件放入组件根目录

下面以 comp-b 组件为例：

```html
<!-- comp-b.wxml -->
<view>comp-b</view>
<view>propa: {{propa}} -- propb: {{propb}}</view>
<button bintap="onTap">click me</button>
<slot></slot>
```

```js
// comp-b.js
Component({
    properties: {
        propa: {type: String, value: ''},
        propb: {type: String, value: ''},
    },
    methods: {
        onTap() {
            this.triggerEvent('someevent')
        },
    },
})
```

3. 使用自定义组件

假设使用 vue 技术，然后下面同样以 comp-b 组件为例：

```html
<template>
    <div>
        <comp-b :propa="propa" :propb="propb" @someevent="onEvent">
            <div>comp-b slot</div>
        </comp-b>
    </div>
</template>
<script>
export default {
    data() {
        return {propa: 'propa-value', propb: 'propb-value'}
    },
    methods: {
        onEvent(evt) {
            console.log('someevent', evt)
        },
    },
}
</script>
```

> PS：如果使用 react 等其他框架其实和 vue 同理，因为它们的底层都是调用 document.createElement 来创建节点。当在 webpack 插件配置声明了这个自定义组件的情况下，在调用 document.createElement 创建该节点时会被转换成创建 wx-custom-component 标签，类似于内置组件的 wx-component 标签。

> PS：具体例子可参考 [demo10](../examples/demo10)

### 自定义 app.js 和 app.wxss

在开发过程中，可能需要监听 app 的生命周期，这就需要开发者自定义 app.js。

1. 修改 webpack 配置

首先需要在 webpack 配置中补上 app.js 的构建入口，比如下面代码的 `miniprogram-app` 入口：

```js
// webpack.mp.config.js
module.exports = {
    entry: {
        'miniprogram-app': path.resolve(__dirname, '../src/app.js'),

        page1: path.resolve(__dirname, '../src/page1/main.mp.js'),
        page2: path.resolve(__dirname, '../src/page2/main.mp.js'),
    },
    // ... other options
}
```

2. 修改 webpack 插件配置

在 webpack 配置补完入口，还需要在 `mp-webpack-plugin` 这个插件的配置中补充说明，不然 kbone 会将 `miniprgram-app` 入口作为页面处理。

```js
module.exports = {
    generate: {
        app: 'miniprogram-app',
    },
    // ... other options
}
```

如上，将 webpack 构建中的入口名称设置在插件配置的 generate.app 字段上，那么构建时 kbone 会将这个入口的构建作为 app.js 处理。

3. 补充 src/app.js

```js
// 自定义 app.wxss
import './app.css'

App({
    onLaunch(options) {},
    onShow(options) {
        // 获取当前页面实例
        const pages = getCurrentPages() || []
        const currentPage = pages[pages.length - 1]

        // 获取当前页面的 window 对象和 document 对象
        if (currentPage) {
            console.log(currentPage.window)
            console.log(currentPage.document)
        }
    },
    onHide() {},
    onError(err) {},
    onPageNotFound(options) {},
})
```

> PS：app.js 不属于任何页面，所以没有真正的 window 和 document 对象，所有依赖这两个对象实现的代码在这里无法被直接使用。

> PS：具体例子可参考 [demo5](../examples/demo5)

### 扩展 dom/bom 对象和 API

kbone 能够满足大多数常见的开发场景，但是当遇到当前 dom/bom 接口不能满足的情况时，kbone 也提供了一系列 API 来扩展 dom/bom 对象和接口。

这里需要注意的是，下述所有对于 dom/bom 对象的扩展都是针对所有页面的，也就是说有一个页面对其进行了扩展，所有页面都会生效，因此在使用扩展时建议做好处理标志，然后判断是否已经被扩展过。

* 使用 window.$$extend 对 dom/bom 对象追加属性/方法

举个例子，假设需要对 `window.location` 对象追加一个属性 testStr 和一个方法 testFunc，可以编写如下代码：

```js
window.$$extend('window.location', {
    testStr: 'I am location',
    testFunc() {
        return `Hello, ${this.testStr}`
    },
})
```

这样便可以通过 `window.location.testStr` 获取新追加的属性，同时可以通过 `window.location.testFunc()` 调用新追加的方法。

* 使用 window.$$getPrototype 获取 dom/bom 对象的原型

如果遇到追加属性和追加方法都无法满足需求的情况下，可以获取到对应对象的原型进行操作：

```js
const locationPrototype = window.$$getPrototype('window.location')
```

如上例子，locationPrototype 便是 window.location 对象的原型。

* 对 dom/bom 对象方法追加前置/后置处理

除了上述的给对象新增和覆盖方法，还可以对已有的方法进行前置/后置处理。

前置处理即表示此方法会在执行原始方法之前执行，后置处理则是在之后执行。前置处理方法接收到的参数和原始方法接收到的参数一致，后置处理方法接收到的参数则是原始方法执行后返回的结果。下面给一个简单的例子：

```js
const beforeAspect = function(...args) {
    // 在执行 window.location.testFunc 前被调用，args 为调用该方法时传入的参数
}
const afterAspect = function(res) {
    // 在执行 window.location.testFunc 后被调用，res 为该方法返回结果
}
window.$$addAspect('window.location.testFunc.before', beforeAspect)
window.$$addAspect('window.location.testFunc.after', afterAspect)

window.location.testFunc('abc', 123) // 会执行 beforeAspect，再调用 testFunc，最后再执行 afterAspect
```

> PS：具体 API 可参考 [dom/bom 扩展 API](./domextend.md) 文档。

### 代码优化

代码优化主要在于代码体积的精简和 dom 树的精简：

1. 代码体积精简

在编译到小程序代码的时候，会将整个 vue runtime 和一些 vue 特性插件（如 vue-router、vuex 等）给打包进来，这样会导致代码包比较庞大，而这些代码是无法去除的，因此得从业务代码上着手进行一些缩减。业务上存在一些代码可以用小程序接口替代，这部分是完全不需要打包进来的，因此可以使用一个行内 loader 和环境变量来进行代码的去除，简单做法如下：

```
npm install --save-dev reduce-loader
```

```js
import Vue from 'vue'
import ActionSheet from 'reduce-loader!./action-sheet' // 使用行内 loader，剔除 action-sheet 文件的引入

// 通过注入的环境变量判断代码运行环境，进而执行不同的逻辑
if (!process.env.isMiniprogram) {
    // web 端
    ActionSheet.show([1, 2, 3], success)
} else {
    // 小程序端
    wx.showActionSheet({
        itemList: [1, 2, 3],
        success,
    })
}
```

2. dom 树的精简

对于一些站点会使用响应式设计，即 pc 端和 h5 端会共用一套代码，通常 pc 端很多节点在 h5 端是不需要展示出来的，这就需要在样式上对节点设置 `display: none`，而这些节点仍旧存在于 dom 树上，只是不渲染在视图上。如果这套代码直接转成小程序代码，也必定会创建一些无需展示的 dom 节点，这些节点本身是可以直接剔除。

因此可以使用另外一个 loader 对这些节点进行删减，在 webpack 配置中 vue loader 执行之前再添加一个 `vue-improve-loader`：

```
npm install --save-dev vue-improve-loader
```

```js
{
    test: /\.vue$/,
    use: [
        {
            loader: 'vue-loader',
            options: {
                compilerOptions: {
                    preserveWhitespace: false
                }
            }
        },
        'vue-improve-loader',
    ]
},
```

然后在 vue 文件中给要剔除的节点加上 check-reduce 属性：

```html
<!-- 删减前代码 -->
<template>
    <div>
        <span>some text</span>
        <a check-reduce>
            <span>some text other</span>
        </a>
    </div>
</template>
```

因为 web 端代码构建和小程序端代码构建走不同的配置，所以 web 端代码会忽略这个属性，而小程序端代码则会删减掉带这个属性的节点。以下便是会输出给 vue-loader 的代码，从构建层面上剔除掉不需要的节点。

```html
<!-- 删减后代码 -->
<template>
    <div>
        <span>some text</span>
    </div>
</template>
```

> PS：vue-improve-loader 必须在 vue-loader 之前执行，这样 vue-loader 才会接收到被删减后的代码。

### 开发建议

1. 虽然此方案将完整的 vue runtime 包含进来了，但必然存在一些无法直接适配的接口，比如 getBoundingClientRect，一部分会通过 dom/bom 扩展 api 间接实现，一部分则完全无法支持。**[查看 dom/bom 扩展 api 文档](./domextend.md)**。
2. 可能存在部分逻辑在 web 端和小程序端需要使用不同的实现，该部分代码可以抽离成一个单独的模块或者插件，暴露接口给业务端代码使用。在模块内可以使用上述提到的 `process.env.isMiniprogram` 环境变量进行判断区分当前运行环境。比如上述提到的 actionSheet 实现就可以抽离成一个 vue 插件实现。

> PS：注意这里使用 process.env.isMiniprogram 环境变量时尽量不要加其他动态条件，以方便 webpack 编译时剪除死代码，比如 `if (false) { console.log('xxxx') }` 就属于死代码

```js
// 正确使用方式
if (!process.env.isMiniprogram) {
    // web 端
    if (isIPhone) {
        // do something
    }
}

// 错误使用方式
if (!process.env.isMiniprogram && isIPhone) {
    // web 端
    // do something
}
```

3. 如果需要使用第三方库，尽量选择使用轻量的库，以缩减构建出来的代码体积。
4. vue 组件命名尽量不要和小程序内置组件同名。
5. 避免使用 id 选择器、属性选择器，尽量少用标签选择器和 * 选择器，尽可能使用 class 选择器代替。
6. 为了确保模板解析不出问题，标签上布尔值属性建议使用 = 号赋值的写法，如下例子所示：

```html
<input type="checkbox" checked="checked" />
<input type="checkbox" :checked="{{true}}" />
```
