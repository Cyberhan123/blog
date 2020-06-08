---
title: webpack 模块异步加载机制
urlname: ugcmol
date: 2020-05-26 20:28:29 +0800
tags: []
categories: []
---

原文  [https://segmentfault.com/a/1190000020233387?utm_source=tag-newest](https://segmentfault.com/a/1190000020233387?utm_source=tag-newest)

精简：

```javascript
((modules) => {
  // 3.module传入的为所有打包后的结果
  var installedModules = {};
  function __webpack_require__(moduleId) {
    if (installedModules[moduleId]) {
      // 做缓存的可以先不理
      return installedModules[moduleId].exports;
    }
    var module = (installedModules[moduleId] = {
      // 5.创建模块,每个模块都有一个exports对象
      i: moduleId,
      l: false,
      exports: {},
    });
    modules[moduleId](module, module.exports, __webpack_require__); // 6.调用对应的模块函数,将模块exports传入
    module.l = true;
    // 8.用户会将结果放到module.exports对象上
    return module.exports;
  }
  function startup() {
    // 通过入口开始加载
    return __webpack_require__("./src/index.js"); // 默认返回的是 module.exports结果;
  }
  return startup(); // 4.启动加载
})({
  // 1.列出打包后的模块
  "./src/a.js": (module) => {
    eval(
      "module.exports = 'webyouxuan'\n\n//# sourceURL=webpack:///./src/a.js?"
    );
  },
  "./src/index.js": (__unusedmodule, __unusedexports, __webpack_require__) => {
    // 7.加载其他模块，拿到其他模块的module.exports结果
    eval(
      'let webyouxuan = __webpack_require__(/*! ./a */ "./src/a.js");\nconsole.log(webyouxuan)\n\n//# sourceURL=webpack:///./src/index.js?'
    );
  },
});
```

## 懒加载

我们在 index 入口文件中，采用 **import**语法动态导入文件

```
const button = document.createElement('button');
button.innerHTML = '关注 webyouxuan';
document.body.appendChild(button);
document.addEventListener('click',()=>{
    import('./a').then(data=>{
        console.log(data.default);
    })
});
```

再回头看编译的结果，貌似好像打包出来的结果有些复杂啦，没关系！其实核心很简单就是个 jsonp 加载文件。
打包出来的结果多了个**src_a_js.main**.js

```
(window["webpackJsonp"] = window["webpackJsonp"] || []).push([
  ["src_a_js"],
  {
    "./src/a.js": module => {
      eval(
        "module.exports = 'webyouxuan'\n\n//# sourceURL=webpack:///./src/a.js?"
      );
    }
  }
]);
```

最终会通过 script 标签加载这个文件，加载后默认会调用 window 下的 > **webpackJsonp**的 push 方法
我们来看看 **main.js** 发生了哪些变化，话说有代码些小复杂，分段来看
先来看**index 模块**做了那些事，为了看着方便我来把代码整理一下

```
"./src/index.js":
 ((__unusedmodule, __unusedexports, __webpack_require__) => {
eval(`
    const button = document.createElement('button');
    button.innerHTML = '关注 webyouxuan';
    document.body.appendChild(button);
    document.addEventListener('click',()=>{
       __webpack_require__.e("src_a_js").then(
          __webpack_require__.t.bind(__webpack_require__, "./src/a.js", 7)).then(data=>{
            console.log(data.default);
          })
     })
    `);
 })
```

这里出现了两个方法 `__webpack_require__.e`和`__webpack_require__.t`
`__webpack_require__.e`方法看似是用来加载文件的，咱们来找一找

```
__webpack_require__.f = {};
__webpack_require__.e = (chunkId) => { // chunkId => src_a_js动态加载的模块
    return Promise.all(Object.keys(__webpack_require__.f).reduce((promises, key) => {
        __webpack_require__.f[key](chunkId, promises); // 调用j方法 将数组传入
        return promises;
    }, []));
};
var installedChunks = {
    "main": 0 // 默认main已经加载完成
};
// f方法上有个j属性
__webpack_require__.f.j = (chunkId, promises) => {
    var installedChunkData = Object.prototype.hasOwnProperty.call(installedChunks, chunkId) ? installedChunks[chunkId] : undefined;
    if(installedChunkData !== 0) {  // 默认installedChunks肯定没有要加载的模块
        if(installedChunkData) {
            promises.push(installedChunkData[2]);
        } else {
            if(true) { // 创建一个promise 把当前的promise 成功失败保存到 installedChunks
                var promise = new Promise((resolve, reject) => {
                    installedChunkData = installedChunks[chunkId] = [resolve, reject];
                });
                // installedChunks[src_a_js] = [resolve,reject,promise]
                promises.push(installedChunkData[2] = promise);
                // 这句的意思是看是否配置publicPath，配置了就加个前缀
                var url = __webpack_require__.p + __webpack_require__.u(chunkId);
                // 1)创建script标签
                var script = document.createElement('script');
                var onScriptComplete;
                script.charset = 'utf-8';
                script.timeout = 120;
                script.src = url; // 2)开始加载这个文件
                var error = new Error();
                onScriptComplete = function (event) { //  完成工作
                    script.onerror = script.onload = null;
                    clearTimeout(timeout);
                    var reportError = loadingEnded();
                    if(reportError) {
                        var errorType = event && (event.type === 'load' ? 'missing' : event.type);
                        var realSrc = event && event.target && event.target.src;
                        error.message = 'Loading chunk ' + chunkId + ' failed.\n(' + errorType + ': ' + realSrc + ')';
                        error.name = 'ChunkLoadError';
                        error.type = errorType;
                        error.request = realSrc;
                        reportError(error);
                    }
                };
                var timeout = setTimeout(function(){ // 超时工作
                    onScriptComplete({ type: 'timeout', target: script });
                }, 120000);
                script.onerror = script.onload = onScriptComplete;
                document.head.appendChild(script); // 3)将标签插入到页面
            } else installedChunks[chunkId] = 0;
        }
    }
};
```

虽然代码量比较多，其实核心就干了一件事 ：> **创建 script 标签**，文件加载回来那肯定就会调用 push 方法咯！
先跳过这段看下面的

```
(window["webpackJsonp"] = window["webpackJsonp"] || []).push([
  ["src_a_js"],
  {
    "./src/a.js": module => {
      eval(
        "module.exports = 'webyouxuan'\n\n//# sourceURL=webpack:///./src/a.js?"
      );
    }
  }
]);
```

```
function webpackJsonpCallback(data) { // 3) 文件加载后会调用此方法
    var chunkIds = data[0]; // data是什么来着，你看看src_a_js怎么写的你就知道了 看上面！ ["src_a_js"]
    var moreModules = data[1]; // 获取新增的模块
    var moduleId, chunkId, i = 0, resolves = [];
    for(;i < chunkIds.length; i++) {
        chunkId = chunkIds[i];
        if(Object.prototype.hasOwnProperty.call(installedChunks, chunkId) && installedChunks[chunkId]) {
            // installedChunks[src_a_js] = [resolve,reject,promise] 这个是上面做的
            // 很好理解 其实就是取到刚才放入的promise的resolve方法
            resolves.push(installedChunks[chunkId][0]);
        }
        installedChunks[chunkId] = 0; // 模块加载完成
    }
    for(moduleId in moreModules) { // 将新增模块与默认的模块进行合并 也是就是modules模块，这样modules中就多了动态加载的模块
        if(Object.prototype.hasOwnProperty.call(moreModules, moduleId)) {
            __webpack_require__.m[moduleId] = moreModules[moduleId];
        }
    }
    if(runtime) runtime(__webpack_require__);
    if(parentJsonpFunction) parentJsonpFunction(data);
    while(resolves.length) { // 调用promise的resolve方法，这样e方法就调用完成了
        resolves.shift()();
    }
};
var jsonpArray = window["webpackJsonp"] = window["webpackJsonp"] || []; // 1) window["webpackJsonp"]等于一个数组
var oldJsonpFunction = jsonpArray.push.bind(jsonpArray);
jsonpArray.push = webpackJsonpCallback; // 2) 重写了数组的push方法
var parentJsonpFunction = oldJsonpFunction;
```

加载的文件会调用 **webpackJsonpCallback**方法，内部就是将新增的模块合并到**modules**上,并且让`__webpack_require__.e`完成

```
__webpack_require__.t = function(value, mode) { // t方法其实很简单就是
    if(mode & 1) value = this(value); // 就是调用__webpack_require__加载最新的模块
};
```

这样用户就可以拿到新增的模块结果啦~~~，源码虽难，但是多看几遍总会有收获！
到此我们就将 webpack5 的懒加载功能整个过了一遍，其实思路和 webpack4 的懒加载一样呢~，不过不得不说 webpack5 打包出来的代码更加简洁啦！ 期待 webpack5 正式发版！！
