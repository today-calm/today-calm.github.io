---
title: "浏览器文件操作（File System Access API）"
date: 2023-08-02T23:29:18+08:00
lastmod: 2023-08-03T00:31:39+08:00
categories: ["技术"]
tags: ["文件操作"]
summary: ""
---

## 介绍

File System Access API 是新提出来的浏览器文件操作 API，相比于 [以前的文件操作方式](https://developer.chrome.com/articles/browser-fs-access/#the-traditional-way-of-dealing-with-files)，优点主要有：
- 代码更加简洁
- 能覆盖写入文件（需要用户允许）

值得一提的是以前写入文件的方式（通过 a 标签下载文件）无法覆盖写入，只能新建文件，而 File System Access API 能够覆盖写入，这是个强大的功能

## 使用

### 读取文件

showOpenFilePicker API

```js
// 作为全局变量，因为在读取、写入文件时可能都用到
let fileHandle;

btn.addEventListener('click', async () => {
  // 用户在文件选择框内选择文件，返回所有 handle
  [fileHandle] = await window.showOpenFilePicker();
  
  // 返回 File 对象
  const file = await fileHandle.getFile();
  
  // 通过 text() 方法，从 blob 中读取文本内容，返回 string
  const contents = await file.text();
  
  textArea.value = contents;
});
```

### 写入文件

#### 创建新文件

showSaveFilePicker API

```js
async function getNewFileHandle() {
  const options = {
    types: [
      {
        description: 'Text Files',
        accept: {
          'text/plain': ['.txt'],
        },
      },
    ],
  };

  // 出现保存文件弹窗，创建一个新 hanlde
  const handle = await window.showSaveFilePicker(options);
  
  return handle;
}
```

#### 覆盖写入

createWritable API

```js
async function writeFile(fileHandle, contents) {
  // 复用已读取文件的 handle，创建 stream，并询问用户是否允许页面编辑文件
  const writable = await fileHandle.createWritable();
  
  // 将内容写入 stream，write 方法接受 string 类型参数
  await writable.write(contents);

  // stream 关闭后，改动才写入磁盘
  await writable.close();
}
```

### 更多用法

可参考 [Using the File System Access API](https://developer.chrome.com/articles/file-system-access/#how-to-use)

## 兼容性

该 API 在旧浏览器上可能有兼容性问题，需要 polyfill

[browser-fs-access](https://github.com/GoogleChromeLabs/browser-fs-access) 是谷歌开发者开发的一个库，会优先使用 File System Access API，若浏览器不支持，则 fallback 到旧的文件操作方法

## 安全性

File System Access API 也考虑了安全性：
- 在读取文件时，会让用户选择文件（和旧方法一样）
- 在写入文件时，
    - 若为新文件，则让用户选择保存位置
    - 若文件已存在，则会弹出提示，询问用户是否允许该网站编辑此文件

![](../../assets/image-20230802234540.png)

当用户允许编辑后，之后网页每次覆盖写入该文件都不会再询问用户，直到该网页被关闭

用户也可以随时撤回授权，如下图所示

![](../../assets/image-20230802234412.png)

## 参考资料

- [Reading and writing files and directories with the browser-fs-access library](https://developer.chrome.com/articles/browser-fs-access/#the-file-system-access-api)  
- [The File System Access API: simplifying access to local files](https://developer.chrome.com/articles/file-system-access)  
- [JavaScript File System Access API Tutorial - Read, Write and Save Files](https://www.youtube.com/watch?v=8EcBJV0sOSU)  
- [Text Editor](https://googlechromelabs.github.io/text-editor/)：一个 Demo
