---
title: javascript前端批量下载文件并打包为zip
date: 2021-05-25 20:08:01
tags:
  - Promise.all
  - Promise.allSettled
  - for await of
  - jszip
  - 下载
categories: javascript
---

## 应用场景

后端提供了下载单个文件的接口，返回数据类型为 blob，现在要在没有额外后端接口的条件下，前端批量下载多个文件并打包生成 zip，供客户下载。

## 需要安装的依赖与需要前置了解的知识

- 需要用到 jszip 库，安装`npm install jszip`

- 在 for 循环中使用异步，使所有请求并发，并在最终等所有请求结束后遍历（了解 Promise.all、Promise.allSettled、for await of）

## 示例

```ts
// 引入jszip库
import jszip from "jszip";

const downloadAndZipAll = async () => {
  // 创建a标签，用于最终的zip下载
  const ele = document.createElement("a");
  // 新建zip实例
  const zip = new jszip();
  // 根据所有文件id请求所有文件blob
  // selectedDocument为选中的多个要下载的文件对象
  const allPromise = selectedDocuments.map((document) => {
    // downloadDocument为根据文件id请求blob数据的Promise函数
    return downloadDocument(document.document_id)
      .then((res) => {
        const blob: Blob = new Blob([res], {
          // 定义blob的类型，这里为docx类型
          type: "application/vnd.openxmlformats-officedocument.wordprocessingml.document",
        });
        // 返回包含文档信息与blob的新对象，文档信息用于后面为文档命名
        return { blob, ...document };
        // 若请求报错则返回null，用于将文件载入zip时，防止某一请求报错而导致无法生成zip
      })
      .catch(() => null);
  });
  // ES8的for await语法，需要在async函数中使用，用于待所有请求有结果后，遍历放入zip文件中
  for await (const item of allPromise) {
    // 若请求未报错，则返回值不为null，可将文档放入zip包
    if (item)
      // 将文件放入zip包，第一个参数为文件名，第二个参数为文档的blob数据
      zip.file(
        `${dayjs(item.make_time, "YYYY-MM-DD HH:mm").format("YYYY-MM-DD")}_${item.product_name}.docx`,
        item.blob
      );
  }
  // 除了使用for await语法外还可以使用Promise.all或Promise.allSettled
  // Promise.all若有一个请求报错，则Promise.all被中断，进入catch中，但若是在每一个单一请求中已做过错误处理，则不会进入catch
  // Promise.allSettled不论请求是否报错，只要响应了，就会返回
  for (const item of await Promise.all(allPromise)) {
    // 若请求未报错，则返回值不为null，可将文档放入zip包
    if (item)
      // 将文件放入zip包，第一个参数为文件名，第二个参数为文档的blob数据
      zip.file(
        `${dayjs(item.make_time, "YYYY-MM-DD HH:mm").format("YYYY-MM-DD")}_${item.product_name}.docx`,
        item.blob
      );
  }
  // await所有文件放入zip包后，生成zip的blob，供用户下载
  zip.generateAsync({ type: "blob" }).then((content) => {
    // 使用blob创建访问链接
    ele.href = URL.createObjectURL(content);
    // 模拟鼠标点击
    ele.click();
    // 释放访问链接
    URL.revokeObjectURL(ele.href);
  });
};
```

> 参考资料
>
> [Promise MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)
>
> [Promise.all 错误处理](https://zhuanlan.zhihu.com/p/25743960)
>
> [纯前端 JSzip 打包文件并下载-张鑫旭](https://www.zhangxinxu.com/wordpress/2019/08/js-zip-download/)
>
> [JSzip 官方文档 API](https://stuk.github.io/jszip/documentation/api_jszip.html)
>
> [Using async/await with a forEach loop](https://stackoverflow.com/questions/37576685/using-async-await-with-a-foreach-loop)
>
> [for await of VS Promise.all](https://stackoverflow.com/questions/59694309/for-await-of-vs-promise-all)
