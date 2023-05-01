---
title: "按需加载 polyfill 的方法"
date: 2023-04-05T14:42:18+08:00
lastmod: 2023-05-01T11:57:59+08:00
categories: ["技术"]
tags: ["兼容性", "polyfill"]
summary: "本文记录了 3 种按需加载 polyfill 的方法"
---

本文是阅读 [How to load polyfills only when needed](https://3perf.com/blog/polyfills/)  后做的笔记

## 方法一：Polyfill.io

- Polyfill.io 根据浏览器 UA 返回相应的 polyfill  
- 可携带 url query `features=xxx` 来只请求某几个 api 的 polyfill，可在 [官网](https://polyfill.io/v3/url-builder) 构造 url  

注：需要自己部署（CDN），防止请求挂了导致低版本浏览器崩溃

接下来尝试用一下，使用 postman 请求 https://polyfill.io/v3/polyfill.js?features=String.prototype.replaceAll

使用最新 Chrome UA
```txt
Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/111.0.0.0 Safari/537.36
```

返回结果：
```js
/* Polyfill service v3.111.0
 * For detailed credits and licence information see https://github.com/financial-times/polyfill-service.
 * 
 * Features requested: String.prototype.replaceAll
 *  */


/* No polyfills needed for current settings and browser */
```

使用旧版本 Chrome UA（来自 [Chrome User Agent Strings](https://www.useragentstring.com/pages/Chrome/)）
```txt
Mozilla/5.0 (Windows NT 5.1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/41.0.2224.3 Safari/537.36
```

返回结果：
```js
/* Polyfill service v3.111.0
 * For detailed credits and licence information see https://github.com/financial-times/polyfill-service.
 * 
 * Features requested: String.prototype.replaceAll
 * 
 * - _ESAbstract.Call, License: CC0 (required by "String.prototype.replaceAll", "_ESAbstract.ToString", "_ESAbstract.ToPrimitive", "_ESAbstract.OrdinaryToPrimitive")
 * - _ESAbstract.CreateMethodProperty, License: CC0 (required by "String.prototype.replaceAll", "Symbol.replace", "Symbol", "Object.keys")
 * - Object.keys, License: MIT (required by "String.prototype.replaceAll", "Symbol.replace", "Symbol")
 * - _ESAbstract.Get, License: CC0 (required by "String.prototype.replaceAll", "_ESAbstract.ToString", "_ESAbstract.ToPrimitive", "_ESAbstract.OrdinaryToPrimitive")
 * - _ESAbstract.HasOwnProperty, License: CC0 (required by "String.prototype.replaceAll", "Symbol.replace", "Symbol", "Object.getOwnPropertyDescriptor")
 * - _ESAbstract.IsCallable, License: CC0 (required by "String.prototype.replaceAll", "_ESAbstract.ToString", "_ESAbstract.ToPrimitive", "_ESAbstract.OrdinaryToPrimitive")
 * - _ESAbstract.RequireObjectCoercible, License: CC0 (required by "String.prototype.replaceAll")
 * - _ESAbstract.ToBoolean, License: CC0 (required by "String.prototype.replaceAll", "RegExp.prototype.flags")
 * - _ESAbstract.ToObject, License: CC0 (required by "String.prototype.replaceAll", "Symbol.replace", "Symbol", "Object.getOwnPropertyDescriptor")
 * - _ESAbstract.GetV, License: CC0 (required by "String.prototype.replaceAll", "_ESAbstract.GetMethod")
 * - _ESAbstract.GetMethod, License: CC0 (required by "String.prototype.replaceAll", "_ESAbstract.ToString", "_ESAbstract.ToPrimitive")
 * - _ESAbstract.Type, License: CC0 (required by "String.prototype.replaceAll", "Symbol.replace", "Symbol", "Object.getOwnPropertyDescriptor", "_ESAbstract.ToPropertyKey")
 * - RegExp.prototype.flags, License: MIT (required by "String.prototype.replaceAll")
 * - _ESAbstract.GetSubstitution, License: CC0 (required by "String.prototype.replaceAll")
 * - _ESAbstract.IsRegExp, License: CC0 (required by "String.prototype.replaceAll")
 * - _ESAbstract.OrdinaryToPrimitive, License: CC0 (required by "String.prototype.replaceAll", "_ESAbstract.ToString", "_ESAbstract.ToPrimitive")
 * - _ESAbstract.StringIndexOf, License: CC0 (required by "String.prototype.replaceAll")
 * - _ESAbstract.ToPrimitive, License: CC0 (required by "String.prototype.replaceAll", "Symbol.replace", "Symbol", "Object.getOwnPropertyDescriptor", "_ESAbstract.ToPropertyKey")
 * - _ESAbstract.ToString, License: CC0 (required by "String.prototype.replaceAll", "Symbol.replace", "Symbol", "Object.getOwnPropertyDescriptor", "_ESAbstract.ToPropertyKey")
 * - _ESAbstract.ToPropertyKey, License: CC0 (required by "String.prototype.replaceAll", "Symbol.replace", "Symbol", "Object.getOwnPropertyDescriptor")
 * - Object.getOwnPropertyDescriptor, License: CC0 (required by "String.prototype.replaceAll", "Symbol.replace", "Symbol")
 * - Symbol, License: MIT (required by "String.prototype.replaceAll", "Symbol.replace")
 * - Symbol.replace, License: MIT (required by "String.prototype.replaceAll")
 * - String.prototype.replaceAll, License: MIT */

// 下面是具体实现，省略
```

## 方法二：type="module"

利用 type="module" 区分浏览器是否支持 ES6。这种方式局限性较大，不记录了。

## 方法三：babel useBuiltIns

- 配置 "entry" 会按 target 去除多余的 polyfill
- 配置 "usage" 会结合 target 和实际使用的 api 去除多余的 polyfill

注：要把 node_modules 也交给 babel 处理，否则 node_modules 不会 polyfill

## 文末总结

- Polyfill.io 特点：在运行时获取浏览器版本，只下载当前版本需要的 polyfill，但无法去除未使用的 polyfill。  
- useBuiltIns 特点：在编译时去除未使用的 polyfill，但只能针对一个 target 版本编译，无法动态获取用户浏览器版本。  

文章最后给了一种最优的做法，结合了 Polyfill.io 和 babel useBuiltIns 的优点：使用 useBuiltIns 配置，构建不同 target 的产物，根据 UA 分发相应的产物。
