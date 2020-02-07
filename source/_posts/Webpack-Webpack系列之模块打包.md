---
title: Webpack-Webpack系列之模块打包
date: 2020-01-14 15:21:00
tags: [Webpack]
categories: 前端
---

#### 导语
> webpack是一个现代JavaScript应用程序的静态模块打包器，在webpack处理应用程序时，它会在内部创建一个依赖图，用于映射到项目需要的每个模块，然后将所有这些依赖生成到一个或多个bundle。

<!--more-->

# 模块打包

## 多种不同的模块机制

### Commonjs

CommonJS最初只为服务端而设计，直到有了Browserify——一个运行在Node.js环境下的模块打包工具，它可以将CommonJS模块打包为浏览器可以运行的单个文件。这意味着客户端的代码也可以遵循CommonJS标准来编写了。

CommonJS中规定每个文件是一个模块，所有的变量及函数只有自己能访问，对外是不可见的。

#### 导出

在CommonJS中，通过module.exports可以导出模块中的内容：

```javascript
module.exports = {
    name: "calculate"
}
```

CommonJS模块内部会有一个module对象用于存放当前模块的信息。

属性。为了书写方便，CommonJS也支持另一种简化的导出方式—直接使用exports:

```javascript
exports.name = "caculate"
```

另外，要注意导出语句不代表模块的末尾，在module.exports或exports后面的代码依旧会照常执行。

#### 导入

在CommonJS中使用require进行模块导入：

```javascript
const calculator = require('./caculate.js')
const name = calculator.name
```

当我们require一个模块时会有两种情况：

* require的模块是第一次被加载。这时会首先执行该模块，然后导出内容。

* require的模块曾被加载过。这时该模块的代码不会再次执行，而是直接导出上次执行后得到的结果。

模块会有一个module对象用来存放其信息，这个对象中有一个属性loaded用于记录该模块是否被加载过。它的值默认为false，当模块第一次被加载和执行过后会置为true，后面再次加载时检查到module.loaded为true，则不会再次执行模块代码。

### ES6 Module

ES6 Module也是将每个文件作为一个模块，每个模块拥有自身的作用域，不同的是导入、导出语句。

ES6 Module会自动采用严格模式 -- "use strict"。

#### 导出

在ES6 Module中使用export命令来导出模块。

export有两种形式：

* 命名导出

* 默认导出

```javascript
export const name = "calculate"
export const add = "add"
```

```javascript
const name = "calculate"
const add = "add"
export { name, add }
```

在使用命名导出时，可以通过as关键字对变量重命名。如：

```javascript
const name = "calculate"
const add = "add"
export { name, add as sum }
```

将export default 默认导出，我们可以理解为对外输出了一个名为default的变量，因此不需要像命名导出一样进行变量声明，直接导出值即可。

```javascript
export default "calculate"
export default  class {...}
export default function() {
  ...
}
```

#### 导入

ES6 Module中使用import语法导入模块。

```javascript
import { name } from './caculate.js'
```

与命名导出类似，我们可以通过as关键字可以对导入的变量重命名。如：

```javascript
import { name as caculate } from './caculate.js'
```

在导入多个变量时，我们还可以采用整体导入的方式。如：

```javascript
import * as caculate from './caculate.js'
const name = caculate.name
```

对于默认导出来说，import后面直接跟变量名，并且这个名字可以自由指定（比如这里是myCalculator）。如：

```javascript
import myCalculator from './caculate.js'
```

### AMD

AMD是英文Asynchronous Module Definition（异步模块定义）的缩写:

```javascript
define('getSum', ['calculate'], function(math) {
  return function(a, b) {
    console.log(calculate.add(a, b))
  }
})
```

在AMD中使用define函数来定义模块，它可以接受3个参数:

第1个参数是当前模块的id，相当于模块名；

第2个参数是当前模块的依赖，比如上面我们定义的getSum模块需要引入calculator模块作为依赖；

第3个参数用来描述模块的导出值，可以是函数或对象。如果是函数则导出的是函数的返回值；如果是对象则直接导出对象本身。

和CommonJS类似，AMD也使用require函数来加载模块，只不过采用异步的形式。

```javascript
require(['getSum'], function(getSum) {
  getSum(2, 3)
})
```

require的第1个参数指定了加载的模块，第2个参数是当加载完成后执行的回调函数。

通过AMD这种形式定义模块的好处在于其模块加载是非阻塞性的，当执行到require函数时并不会停下来去执行被加载的模块，而是继续执行require后面的代码，这使得模块加载操作并不会阻塞浏览器。

## 模块打包原理

Webpack 将项目中成百上千个有依赖关系的模块组织在一起，打包后将会成为如下的形式：

```javascript
// 立即执行匿名函数
(function(module) {
    // 模块缓存
    var installedModules = {}
    // 实现 require
    function __webpack_require__(moduleId) {
        // ...
    }
    // 执行入口模块的加载
    return __webpack_require__(__webpack_require__.s = 0) {
        // modules: 以 key-value 的形式存储所有被打包的模块
        0: function (module, exports, __webpack_require__) {
           // 打包入口
            module.exports = __webpack_require__("3qiv")
        },
        "3qiv": function (module, exports, __webpack_require__) {
            // 打包入口
            module.exports = __webpack_require__("3qiv")
        }
    }
})
```

上面的bundle分为以下几个部分：

* 最外层立即执行匿名函数。它用来包裹整个bundle，并构成自身的作用域。

* installedModules对象。每个模块只在第一次被加载的时候执行，之后其导出值就被存储到这个对象里面，当再次被加载的时候直接从这里取值，而不会重新执行。

* __webpack_require__函数。对模块加载的实现，在浏览器中可以通过调用__webpack_require__(module_id)来完成模块导入。

* modules对象。工程中所有产生了依赖关系的模块都会以key-value的形式放在这里。key可以理解为一个模块的id，由数字或者一个很短的hash字符串构成；value则是由一个匿名函数包裹的模块实体，匿名函数的参数则赋予了每个模块导出和导入的能力。

bundle是如何在浏览器中执行的：

* 在最外层的匿名函数中会初始化浏览器执行环境，包括定义installedModules对象、__webpack_require__函数等，为模块的加载和执行做一些准备工作。

* 加载入口模块。每个bundle都有且只有一个入口模块，例如：index.js是入口模块，在浏览器中会从它开始执行。

* 执行模块代码。如果执行到了module.exports则记录下模块的导出值；如果中间遇到require函数（准确地说是__webpack_require__），则会暂时交出执行权，进入__webpack_require__函数体内进行加载其他模块的逻辑。

* 在__webpack_require__中会判断即将加载的模块是否存在于installedModules中。如果存在则直接取值，否则回到上一步，执行该模块的代码来获取导出值。

* 所有依赖的模块都已执行完毕，最后执行权又回到入口模块。当入口模块的代码执行到结尾，也就意味着整个bundle运行结束。

不难看出，第3步和第4步是一个递归的过程。Webpack为每个模块创造了一个可以导出和导入模块的环境，但本质上并没有修改代码的执行逻辑，因此代码执行的顺序与模块加载的顺序是完全一致的，这就是Webpack模块打包的奥秘。
