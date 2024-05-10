---
layout: mypost
title: js 批量下载文件
tags:
  - JavaScript
---

```js
/**
 * 首先定义了一个 urls 数组，里面存放了要下载的文件地址。
 * 使用 Promise.all 并行下载这两个文件，然后将下载的数据转化为 blob 数据，
 * 最后循环遍历 blob 数据，依次创建一个带有下载属性的 a 标签，并将其插入到页面中，触发下载操作，
 * 然后再将该标签从页面中移除，并调用 URL.revokeObjectURL() 方法释放 URL 对象。
 */
export async function downloadBatchFiles(urls) {
	const responses = await Promise.all(urls.map((url) => fetch(url))); // 使用 Promise.all 并行下载多个文件
	const blobs = await Promise.all(responses.map((res) => res.blob())); // 转化为 blob 数据
	blobs.forEach((blob, index) => {
	  const url = urls[index];
	  const a = document.createElement("a");
	  a.href = URL.createObjectURL(blob);
	  a.download = url.substring(url.lastIndexOf("/") + 1);
	  document.body.appendChild(a);
	  a.click();
	  document.body.removeChild(a);
	  URL.revokeObjectURL(a.href);
	});
}

```
