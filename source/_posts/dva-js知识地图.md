---
title: dva.js知识地图
date: 2018-10-31 16:17:55
tags:
---
#### Promises
Promise 用于更优雅地处理异步请求。比如发起异步请求：
```
fetch('/api/todos')
  .then(res => res.json())
  .then(data => ({ data }))
  .catch(err => ({ err }));

```
定义 Promise 。
```
const delay = (timeout) => {
  return new Promise(resolve => {
    setTimeout(resolve, timeout);
  });
};

delay(1000).then(_ => {
  console.log('executed');
});
```