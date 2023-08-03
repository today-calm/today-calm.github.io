---
title: "浏览器文件下载（使用 a 标签）"
date: 2023-08-03T14:42:10+08:00
lastmod: 2023-08-03T14:56:44+08:00
categories: ["技术"]
tags: ["文件操作"]
summary: ""
---

参考：
- [Blob 用作 URL](https://zh.javascript.info/blob#blob-yong-zuo-url)
- [How to create a file in memory for user to download, but not through server?](https://stackoverflow.com/questions/3665115/how-to-create-a-file-in-memory-for-user-to-download-but-not-through-server)

```js
const download = (content, fileName, type) => {
  const link = document.createElement('a');
  const blob = new Blob([content], { type });
  link.href = URL.createObjectURL(blob);
  link.download = fileName;
  link.click();
  URL.revokeObjectURL(link.href);
};

download('hello', '1.md', 'text/plain');
```

注：
- Blob 构造函数的参数
    - 第一个参数是数组，表示 `Blob`/`BufferSource`/`String` 类型的值的数组
    - 第二个参数是一个对象，type 表示 Blob 类型，通常是 MIME 类型，如 `image/png`
- a 标签 download 属性的作用：当用户点击时，会下载文件。若有值，则该值会作为文件名
- `createObjectURL`：建立 blob 与 URL 的映射，该 URL 可用来下载文件
- `revokeObjectURL`：销毁 blob，释放内存。若未手动调用，则只能等到文档 unload 时释放内存
