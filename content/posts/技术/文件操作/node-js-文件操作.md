---
title: "Node.js 文件操作"
date: 2023-08-03T21:19:55+08:00
lastmod: 2023-08-04T11:56:37+08:00
categories: ["技术"]
tags: ["文件操作", "Node.js"]
summary: ""
---

## 介绍

Node 通过 fs 模块操作文件，由于有很多 API，容易混乱，这里记录下常用的

在官网 [https://nodejs.dev/en/api/v20/fs/](https://nodejs.dev/en/api/v20/fs/) 有讲到所有的操作都有同步、回调、promise 的形式，所以在网上的文章中能看到每篇文章用的方法都不一样

> All file system operations have synchronous, callback, and promise-based forms, and are accessible using both CommonJS syntax and ES6 Modules (ESM).

### 回调、同步、promise API

- 回调：最原始的异步操作方式
- 同步：同步操作文件，由于会阻塞程序运行，效率不如异步方式，仅在少数情况下使用
- promise：也是异步操作，只不过写法变了，可以用 async await 使写法更自然，是三者中最推荐的用法

以 readFile（读取文件）API 为例，看看这三种写法有何不同

回调

```js
const fs = require('fs');

fs.readFile('1.md', 'utf8', (err, data) => {
  if (err) throw err;
  console.log(data);
});
```

同步

```js
const fs = require('fs');

try {
  // 没有回调参数，返回值是读取的数据
  const data = fs.readFileSync('1.md', 'utf8');
  console.log(data);
} catch (err) {
  console.log(err.message);
}
```

promise

```js
// 引入 fs/promises
const fsp = require('fs/promises');

const read = async () => {
  try {
    const data = await fsp.readFile('1.md', 'utf8');
    console.log(data);
  } catch (err) {
    console.log(err.message);
  }
};

read();
```

注：
- 从 Node v10.1.0 开始，promise 的引入写法是 `require('fs').promises`
- 从 Node v14.0.0 开始，promise 的引入写法是 `require('fs/promises')`

## 常用 API

在个人写一些脚本的场景，对于效率要求不是那么高，可以使用同步 API，写起来更方便

下面均使用同步 API

### 读

```js
fs.readFileSync(path[, options])

// eg.
const data = fs.readFileSync('1.md', 'utf8');
```

### 写

```js
fs.writeFileSync(file, data[, options])

// eg.
fs.writeFileSync('1.md', 'hello world');
```

注：
- options 类型是 `String | Object`。若为 String，则表示编码。若为 Object，除了 encoding（编码），还可以传递 flag（标识符），可以实现追加写入等功能。
- 另外还可以流式读写（`fs.createReadStream`、`fs.createWriteStream`），优点是一块一块地读写文件，更加节省内存，暂未详细了解

### 移动文件

```js
fs.renameSync(oldPath, newPath)
// eg.
fs.renameSync('src/1.md', 'dest/1.md');
```

注：若 newPath 的目录不存在，会移动失败，需要先用 `fs.mkdirSync` 创建文件夹

### 复制文件

```js
fs.copyFileSync(src, dest)

// eg.
fs.copyFileSync('src/1.md', 'dest/1.md');
```

注：若 dest 的目录不存在，会复制失败，需要先用 `fs.mkdirSync` 创建文件夹

### 复制文件夹

[How to copy a Folder recursively in Node.js](https://bobbyhadz.com/blog/copy-folder-recursively-in-node-js)

```js
fs.cpSync(src, dest[, options])
```

注：该函数是 Node v16.7.0 中添加的，目前还是实验性功能。若不想使用该函数，可以循环调用 `fs.copyFileSync`。

### 创建文件夹

```js
fs.mkdirSync(path[, options])

// eg.
fs.mkdirSync('./dir1');
fs.mkdirSync('./dir1/dir2', { recursive: true });
```

注：第二个参数是 Object，设置 `recursive: true` 可递归创建目录

若文件夹已存在，调用 `fs.mkdirSync` 会抛出 Error，所以在执行前可以先判断文件夹是否存在，下面是常用写法

```js
// 用 existsSync 判断文件夹是否存在
if (!fs.existsSync(dir)){
  fs.mkdirSync(dir);
}
```

## 通配符匹配

fs 模块本身未支持通配符匹配，可以安装 [glob](https://github.com/isaacs/node-glob) 模块来实现该功能（暂未详细了解）

如果不想安装 glob 模块，可以参考 [这个方法](https://stackoverflow.com/a/42734993)：

```js
const fs = import('fs');
const dirCont = fs.readdirSync( dir );
const files = dirCont.filter( ( elm ) => elm.match(/.*\.(html?)/ig));
```

通过 `fs.readdirSync` 读取文件夹下的所有文件名，通过字符串的 match 方法正则匹配，用 filter 过滤匹配的文件
