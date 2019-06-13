---
title: 使用http-proxy-middleware解决前端开发中跨域的问题
date: 2018-08-08 09:53:39
tags: 前端工具
---

#### 一、使用http-proxy-middleware中间件解决跨域问题
1. 后端服务是用tornado创建的一个服务(可以根据自己熟悉的搭建一个后端服务器)
``` bash
...
class JsonHandler(tornado.web.RequestHandler):
    def get(self):
        li = [
            {"id": 0, "name": "张三", "age": 20},
            {"id": 1, "name": "李四", "age": 25},
            {"id": 2, "name": "王五", "age": 27}
        ]
        self.write(json.dumps(li))

...
```
2. 安装包
``` bash
npm install http-proxy-middleware --save-dev
npm install webpack --save-dev --save-dev
npm install webpack-dev-server --save-dev
npm install axios --save
```
3. webpack.config.js文件
``` bash
var proxy = require('http-proxy-middleware');
module.exports = {
    entry:{
        index:'./index.js', 
    },
    output:{
        path:__dirname,
        filename:'[name].build.js',
    },
    module:{
        loaders:[]
    },
    resolve:{
        extensions:['.js',".css",".jsx"]
    },
    devServer: {
        proxy: {
          '/api': { // api表示当前项目请求的key
            target: 'http://xxxxxxxx:8000', // 代理服务器路径
            pathRewrite: {'^/api' : '/'}, // 重写路径
            changeOrigin: true
          }
        }
     }
}
```
4. 请求数据
``` bash
import axios from 'axios';
var obtn = document.getElementById('btn');

obtn.addEventListener('click',function(){
    axios.get('/api/json').then(res=>{
        console.log(res);
    })
})
```


