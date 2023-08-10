---
layout: mypost
title: 大文件分片上传预研
tags: 前端
---

1. 目录
   {:toc}

<!--more-->

## 背景

以前的文件上传，大多用的是 element plus 的 el-upload 组件进行上传，一般都是一些 Excel 表格等小文件

> 普通的文件上传参考：[前端文件如何上传](https://daotin.github.io/posts/2020/07/14/前端文件如何上传.html "前端文件如何上传") 或者使用 el-upload 组件进行上传

但是对于一些比较大的文件，比如几百 M，甚至上 G 的文件，就不太适合了。具体可以参考下面介绍。

所以，在这种情况下，就需要考虑传输大文件的替代方法，因此有必要了解一下前端大文件上传的原理和实现方式。

## 传统的文件上传存在的问题

前端大文件上传解决方案是指通过前端技术实现上传大文件的一种解决方案。它主要为了解决以下问题而诞生：

1. 大文件上传耗时长，容易导致请求超时。
2. 上传速度和效率：原始的文件上传方式可能需要等待整个文件上传完成才能提交，耗时较长。占用服务器和网络带宽资源，可能影响其他用户的访问速度。
3. 如果上传中断，需要重新上传整个文件，效率低下。
4. 难以实现上传进度的显示和控制。

## 文件切片上传的优势

1. 将大文件分割为更小的文件切片，分多次上传，提高上传效率和稳定性。
2. 提供上传进度的监控和展示，提高用户体验。
3. 充分利用浏览器的并发上传能力，减轻服务器负担。
4. 实现断点续传功能，避免重复上传已上传的部分。

## 需要实现的功能点

前端大文件上传一般需要实现的功能点如下：

1.  **分片上传**：将大文件切分成多个小块，逐个上传。这样可以提高上传速度和稳定性，并且在上传失败时只需重新上传失败的分片，而不需要重新上传整个文件。
2.  **并发上传数量管理**：同时上传多个分片，以提高上传速度和效率。
3.  **断点续传**：记录已上传的分片信息，以便在上传中断或失败后能够继续上传。这样可以保证文件上传的完整性，并提供更好的用户体验。
4.  **上传进度显示**：实时展示文件上传的进度，让用户了解上传的状态。可以通过进度条、百分比等方式进行展示。
5.  **支持取消上传**：在上传的途中，可以取消本次上传，当再次上传时，会执行断点续传。

### 分片上传

分片上传就是将大文件分成一个个小文件（切片），将切片进行上传，等到后端接收到所有切片，再将切片合并成大文件。

在 JavaScript 中，文件 FIle 对象是 Blob 对象的子类，Blob 对象包含一个重要的方法`slice`，通过这个方法，我们就可以对二进制文件进行拆分。

下面是一个拆分文件的示例：

```js
// 切片大小
const ChunkSize = 10 * 1024 * 1024;

// 生成文件切片
function generateChunks(file: any) {
  const chunks = [];
  let cur = 0;
  while (cur < file.size) {
    chunks.push({ file: file.slice(cur, cur + ChunkSize) });
    cur += ChunkSize;
  }
  return chunks;
}
```

采用 Promise.all 并发的方式上传切片

```js
function uploadChunks(chunks: any) {
  const requests = chunks.map((chunk: any, index: number) => {
    const formData = new FormData();
    formData.append("chunk", chunk.file);
    formData.append("hash", "hash-" + index);
    formData.append("index", index.toString());
    return apis.upload.uploadChunk(formData);
  });
  return Promise.all(requests);
}
```

### 管理上传并发量

我们上传切片的时候，所有的文件切片一起使用 Promise.all 发起几十个 HTTP 请求，也会导致卡顿，所以我们就需要手动管理上传任务的并发数量。

下面是一个并发请求管理函数：

```js
/**
假设你有9个请求在数组 `requests` 中，并且并发限制 `limit` 为3。那么我们期望在任何时刻，
都最多有3个请求在同时进行，并且当其中的某个请求完成时，我们希望从队列中取出下一个请求来进行处理。
下面是代码执行过程的分解：
1. **初始化阶段**: 初始化一个空的 `results` 数组来存储每个请求的结果，并创建一个函数 `processRequest`，用于逐个处理队列中的请求。
2. **并发执行阶段**: 通过`Array.from`创建一个 `Promise` 数组 `concurrent`，该数组包含3个执行中的 `processRequest` 调用的Promise。这3个Promise代表了同时执行的3个请求。
3. **递归处理阶段**: 每次调用 `processRequest`，它都会从 `requests` 队列中取出一个请求，并用 `await` 执行它。一旦请求完成，结果会被推入 `results` 数组，并且函数会再次递归调用自身，检查队列中是否还有更多的请求。
   - 如果队列中还有请求，则取出下一个请求并递归处理。
   - 如果队列为空，则递归调用将停止。
4. **等待所有并发请求完成**: 通过 `await Promise.all(concurrent)`，我们等待所有并发的请求完成。由于我们的递归处理方式，这包括等待任何后续从队列中取出的请求。
5. **返回结果**: 返回包含所有请求结果的数组 `results`。
关于如何补充新的请求，每次一个请求完成并将结果添加到 `results` 数组后，`processRequest` 函数就会再次递归调用自身。如果 `requests` 队列中还有请求，则会立即取出并开始处理下一个请求。
`concurrent` 数组存储的内容是并发执行中的3个 `processRequest` 调用的Promise。这3个Promise会同时开始处理，每个都处理 `requests` 队列中的一个请求，并在其中一个请求完成后立即补充新的请求（如果队列中还有请求）。
 */
async function handleRequests(requests: any[], limit: number) {
  const results = [] as any[] // 存储所有请求的结果

  // 定义一个递归函数，用于处理请求并控制并发数量
  async function processRequest() {
    if (requests.length > 0) {
      // 如果还有请求在队列中
      const request = requests.shift() // 从队列中取出一个请求
      console.log('request剩余==>', requests.length)

      const result = await request() // 执行请求并等待其完成
      results.push(result) // 将结果存储
      // 递归调用，确保有新的请求补充进去
      await processRequest()
    }
  }

  // 创建一个 Promise 数组，用于执行并发的请求
  const concurrent = Array.from({ length: Math.min(limit, requests.length) }, () => processRequest())

  // 等待所有并发的请求完成
  await Promise.all(concurrent)

  return results // 返回所有请求的结果
}
```

// TODO 进阶探索？

由于切片上传速度跟当前网速相关，所以在对上传任务的并发数量进行管理时，我们需要确定切片的大小。那该如何确定切片的大小呢？

我们可以借鉴 **TCP 协议的慢启动逻辑**，去让切片的大小和当前网速匹配，这样，我们就可以通过网速确定切片的大小。

### 分片卡顿问题

在文件上传之前，我们需要在前端计算出一个文件的 Hash 值作为唯一标识，用来向后端询问切片的列表。但是对于一个 2GB 大小的文件来说，即使是使用 MD5 算法来计算 Hash 值，也会造成浏览器的卡顿。那怎么解决计算 Hash 值时，浏览器的卡顿的问题呢？

对于卡顿问题，我们可以通过 `web-worker` 去解决，我们这里的 hash.js，就相当于浏览器主进程的分身，用分身就可以去计算 Hash 值，不耽误主进程的任务。

在实例化 web-worker 时，我们单独创建一个 hash.js 文件放在 public 目录下，通过`importScripts` 函数导入 spark-md5 插件，来计算出文件的 hash 值。

在 worker 线程中，接受文件切片 chunks ，利用 readAsArrayBuffer 读取每个切片的 ArrayBuffer 并不断传入 spark-md5 中，每计算完一个切片通过 postMessage 向主线程发送一个进度事件，全部完成后将最终的 hash 发送给主线程。

```js
// /public/hash.js
importScripts("./spark-md5.min.js"); // 请确保路径是正确的

self.onmessage = function (e) {
  const { chunks } = e.data;
  const hashes = [];

  let progress = 0;
  for (const chunk of chunks) {
    const spark = new SparkMD5.ArrayBuffer();
    const reader = new FileReaderSync();
    const arrayBuffer = reader.readAsArrayBuffer(chunk.file);
    spark.append(arrayBuffer);
    const hash = spark.end();
    hashes.push(hash);
    progress++;
    self.postMessage({ progress: (progress / chunks.length) * 100 });
  }

  self.postMessage({ hashes });
};
```

在主线程中，使用 `postMessage` 给 worker 线程传入所有切片 chunks ，并监听 worker 线程发出的 postMessage 事件拿到文件 hash。

```typescript
const worker = new Worker("/hash.js");

async function calculateHashes(chunks: any[]) {
  return new Promise((resolve) => {
    worker.onmessage = function (e) {
      if (e.data.hashes) {
        resolve(e.data.hashes);
      } else if (e.data.progress) {
        console.log(`Progress: ${e.data.progress}%`); // 可以用于显示进度
      }
    };
  });
}

// 生成文件切片
function generateChunks(file: any, chunkSize: number) {
  const chunks = [];
  let start = 0;
  const fileRaw = file.raw;
  while (start < fileRaw.size) {
    chunks.push({
      file: fileRaw.slice(start, start + chunkSize),
    });
    start += chunkSize;
  }
  worker.postMessage({ chunks }); // 向 worker 发送 chunks
  return chunks;
}
```

// TODO 但是在计算量过大，也可能会出现卡顿问题。

于是，我们可以借鉴 React 的 `Fiber` 解决方案，使用浏览器的空闲时间去计算 Hash。

在下面的代码中，我们使用 requestIdleCallback 启动空闲时间的计算任务，能很好地解决这个问题。

下面是示例代码：

```typescript
let count = 0;
const workLoop = async (deadline) => {
  // 计算，并且当前帧还没结束
  while (count < chunks.length && deadline.timeRemaining() > 1) {
    await appendToSpark(chunks[count].file);
    count++;
    // 没有了 计算完毕
    if (count < chunks.length) {
      // 计算中
      this.hashProgress = Number(((100 * count) / chunks.length).toFixed(2));
      // console.log(this.hashProgress)
    } else {
      // 计算完毕
      this.hashProgress = 100;
      resolve(spark.end());
    }
  }
  window.requestIdleCallback(workLoop);
};
window.requestIdleCallback(workLoop);
```

## 断点续传

即使将大文件拆分成切片上传，我们仍需等待所有切片上传完毕，在等待过程中，可能发生一系列导致部分切片上传失败的情形，如网络故障、页面关闭等。由于切片未全部上传，因此无法通知服务端合成文件。这种情况下可以通过**断点续传**来进行处理。

断点续传指的是：可以从已经上传部分开始继续上传未完成的部分，而没有必要从头开始上传，节省上传时间。

断点续传的原理在于前端/服务端需要`记住`已上传的切片，这样下次上传就可以跳过之前已上传的部分，有两种方案实现记忆的功能

- 前端使用 localStorage 记录已上传的切片 hash
- 服务端保存已上传的切片 hash，前端每次上传前向服务端获取已上传的切片

第一种是前端的解决方案，第二种是服务端，而前端方案有一个缺陷，如果换了个浏览器就失去了记忆的效果，所以最好选后者。

因为演示的原因，这里的示例代码采用 localStorage 进行存储。主要有以下两点：

1.  生成 requests 的时候进行过滤
2.  在 uploadChunk 接口完成后，进行记录

```js
// 生成上传请求，排除已完成的分片
function generateRequests(chunks: any, chunkHashes: any[]) {
  abortController.value = new AbortController();
  signal.value = abortController.value.signal;

  const completedChunks = JSON.parse(
    localStorage.getItem("completedChunks") || "[]"
  );
  const setCompletedChunks = new Set(completedChunks);

  const requests = chunks
    .map((chunk: any, index: number) => {
      const hash = chunkHashes[index];
      const formData = new FormData();
      // 过滤未上传成功的
      if (!setCompletedChunks.has(hash)) {
        console.log("没有");
        formData.append("chunk", chunk.file);
        formData.append("hash", hash); // 使用 hash 属性
        formData.append("index", index.toString());
        return () =>
          apis.upload
            .uploadChunk(
              { index, hash },
              { signal: abortController.value.signal }
            )
            .then(() => {
              // 接口完成，记录在localstorage
              completedChunks.push(hash);
              localStorage.setItem(
                "completedChunks",
                JSON.stringify(completedChunks)
              );
            });
      } else {
        return null;
      }
    })
    .filter(Boolean);
  return requests;
}
```

## 上传进度显示

通过[xhr.upload](https://link.juejin.cn?target=https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest/upload "xhr.upload")中的`progress`方法可以实现监控每一个切片上传进度。

进度的计算也分两种方式：

1.  一种是前端用一个计数器，分片每次上传完毕，计数器自增，然后除以分片总数即可
2.  分片每次上传完毕，后端会返回一个接收到切片的数组，然后前端通过`数组.length / 总切片数` 就能算出当前的进度。

具体代码略。

## 取消上传

上传暂停的实现也比较简单，通过`abortController`可以取消当前未完成上传切片的上传，实现上传暂停的效果，恢复上传就跟断点续传类似，先获取已上传的切片列表，然后重新发送未上传的切片。

1.  **创建 AbortController**： 创建一个`AbortController`实例，并从中获取`signal`。
2.  **传递 signal 给接口请求**： 将`signal`传递给每个接口请求的选项中。
3.  **调用 abort 方法**： 当需要取消请求时，调用`AbortController`的`abort`方法。

```js
// 生成上传请求，排除已完成的分片
function generateRequests(chunks: any, chunkHashes: any[]) {
  abortController.value = new AbortController();
  signal.value = abortController.value.signal;

  const completedChunks = JSON.parse(
    localStorage.getItem("completedChunks") || "[]"
  );
  const setCompletedChunks = new Set(completedChunks);

  const requests = chunks
    .map((chunk: any, index: number) => {
      const hash = chunkHashes[index];
      const formData = new FormData();
      // 过滤未上传成功的
      if (!setCompletedChunks.has(hash)) {
        console.log("没有");
        formData.append("chunk", chunk.file);
        formData.append("hash", hash); // 使用 hash 属性
        formData.append("index", index.toString());
        return () =>
          apis.upload
            .uploadChunk(
              { index, hash },
              { signal: abortController.value.signal }
            )
            .then(() => {
              // 接口完成，记录在localstorage
              completedChunks.push(hash);
              localStorage.setItem(
                "completedChunks",
                JSON.stringify(completedChunks)
              );
            });
      } else {
        return null;
      }
    })
    .filter(Boolean);
  return requests;
}

// 取消上传的函数
const abortController = ref();
const signal = ref();

function handleCancel() {
  console.log("⭐取消上传");
  abortController.value.abort(); // 取消与该信号相关联的所有请求
  showCancel.value = false;
}
```

当调用 handleCancel 函数时，由于绑定的是同一个`signal`，所以与该`AbortController`的`signal`关联的所有请求将被取消。

## 总结

文件上传是前端经常会遇到的业务功能，我们通常会使用普通的文件上传方式，或者使用 el-upload 等组件进行上传。但是如果文件过大，则会出现耗时过长，请求超时，如果失败则需要重新上传等问题。由此，诞生了大文件分片上传的方案。

一般来说，大文件上传方案通常包括以下几个功能点：切片上传，并发数量管理，取消上传，断点续传，和上传进度管理。

切片上传就是将大文件切分成多个小块，然后利用 http 的并发请求进行批量上传，减少上传的时间。

并发数量管理，如果切片数量过大，同时发起很多 http 请求的话也会导致页面卡顿，所有有必要对并发请求数量进行控制。

取消上传是当用户上传到一半时，如果关闭/刷新页面，或者手动取消后，可以再次上传，并且采用断点续传的方式进行，不需要重头开始上传，节省带宽和时间。

最后就是上传进度管理，我们可以清楚的看到上传的进度，提高了用户体验。

> 文章的完整示例代码参见：[upload.vue](https://github.com/Daotin/create-vue3-template/blob/master/src/views/demo/upload/upload.vue) （可打开控制台看并发控制）

## 待实现的逻辑

- [ ] 以 fileHash 作为 localStorage 的 key 进行分片存储，或者使用上传成功后，后端返回的数据
- [ ] 借鉴 **TCP 协议的慢启动逻辑**，计算切片的大小和当前网速匹配
- [ ] hash 计算的 React 的 `Fiber` 解决方案

## 第三方上传组件

- [vue-simple-uploader](https://github.com/simple-uploader/vue-uploader "vue-simple-uploader") （star 1.9k）

## 参考文章

- [https://juejin.cn/post/6844904046436843527](https://juejin.cn/post/6844904046436843527 "https://juejin.cn/post/6844904046436843527")
- [https://juejin.cn/post/7110121072032219166](https://juejin.cn/post/7110121072032219166 "https://juejin.cn/post/7110121072032219166")
- [https://juejin.cn/post/6844904055819468808](https://juejin.cn/post/6844904055819468808 "https://juejin.cn/post/6844904055819468808")（[大圣示例代码](https://github.com/shengxinjing/file-upload "大圣示例代码")）
- [https://juejin.cn/post/7220365570456551481](https://juejin.cn/post/7220365570456551481 "https://juejin.cn/post/7220365570456551481")
- [https://juejin.cn/post/7255189826226602045](https://juejin.cn/post/7255189826226602045 "https://juejin.cn/post/7255189826226602045")
