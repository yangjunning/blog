---
title: Vue.js - 封装Axios实现全局的loading自动显示效果（结合Element UI）
date: 2020-02-20 17:26:54
categories:
  - [前端开发, Vue.js]
tags:
  - 转载
---

在 vue 项目中，我们通常会使用 axios 库来与后台进行数据交互。而当我们发起 ajax 请求时，常常需要在页面上显示一个加载框（Loading 效果），然后等数据返回后自动将其隐藏。要实现这个功能，我们可以在每次请求前手动显示个加载框，等收到数据后又将其隐藏。但如果每个请求要都这么做，就略显麻烦。
下面通过样例演示如何封装一个带 loading 效果的 Axios 组件，它能够对请求和响应进行拦截从而实现 loading 的自动显示与隐藏，并且在请求失败时自动弹出消息提示框显示错误信息。

原文出自：www.hangge.com  转载请保留原文链接：https://www.hangge.com/blog/cache/detail_2554.html

<!--more-->

## 组件封装

（1）在项目中创建一个 http.js，里面内容是对 Axios 进行二次封装，代码如下：

```js
import axios from 'axios';
import { Message,Loading } from 'element-ui';
import _ from 'lodash';

const http = axios.create({
    baseURL:process.env.BASE_URL, //设置请求的base url
    timeout:40000 //超时时长
});

//loading对象
let loading;

//当前正在请求的数量
let needLoadingRequestCount = 0;

//显示loading
function showLoading(target) {
  // 后面这个判断很重要，因为关闭时加了抖动，此时loading对象可能还存在，
  // 但needLoadingRequestCount已经变成0.避免这种情况下会重新创建个loading
  if (needLoadingRequestCount === 0 && !loading) {
    loading = Loading.service({
      lock: true,
      text: "Loading...",
      background: 'rgba(255, 255, 255, 0.5)',
      target: target || "body"
    });
  }
  needLoadingRequestCount++;
}

//隐藏loading
function hideLoading() {
  needLoadingRequestCount--;
  needLoadingRequestCount = Math.max(needLoadingRequestCount, 0); //做个保护
  if (needLoadingRequestCount === 0) {
    //关闭loading
    toHideLoading();
  }
}

//防抖：将 300ms 间隔内的关闭 loading 便合并为一次。防止连续请求时， loading闪烁的问题。
var toHideLoading = _.debounce(()=>{
      loading.close();
      loading = null;
    }, 300);

//添加请求拦截器
http.interceptors.request.use(config => {
  //判断当前请求是否设置了不显示Loading
  if(config.headers.showLoading !== false){
    showLoading(config.headers.loadingTarget);
  }
  return config;
}, err => {
  //判断当前请求是否设置了不显示Loading
  if(config.headers.showLoading !== false){
    hideLoading();
  }
  Message.error('请求超时!');
  return Promise.resolve(err);
});

//响应拦截器
http.interceptors.response.use(
    response => {
      //判断当前请求是否设置了不显示Loading（不显示自然无需隐藏）
      if(response.config.headers.showLoading !== false){
        hideLoading();
      }
      return response;
    },
    error => {
      //判断当前请求是否设置了不显示Loading（不显示自然无需隐藏）
      if(error.config.headers.showLoading !== false){
        hideLoading();
      }
      if(error.response && error.response.data && error.response.data.message) {
        var jsonObj = JSON.parse(error.response.data.message);
        Message.error(jsonObj.message);
      }else{
        Message.error(error.message);
      }
      return Promise.reject(error);
    }
);

export default http;
```

**代码说明：**

- 基本原理是通过 axios 提供的请求拦截和响应拦截的接口，控制 `loading` 的显示或者隐藏。同时在请求失败时还会自动弹出消息提示框显示错误信息。
- `loading` 效果这里采用的是 Element UI 中提供的 Loading 组件来实现。而错误消息提示框则用的是 Element UI 中的 Message 组件来实现。
- 内部有个计数器，确保同一时刻如果有多个请求的话，不会同时出现多个 `loading`，而是只有 1 个。并且在所有请求结束后才会隐藏 `loading`。
- 使用了 `lodash` 的 `debounce` 防抖。因为有时会碰到在一次请求完毕后又立刻又发起一个新的请求的情况（比如删除一个表格条目后立刻进行刷新）。这种情况会造成连续 `loading` 两次，并且中间有一次极短的闪烁。通过防抖可以让 300ms 间隔内的 `loading` 便合并为一次，避免闪烁的情况。
- 默认所有请求都会自动有 `loading` 效果。如果某个请求不需要 `loading` 效果，可以在请求 `header` 中 `showLoading` 设置为 `false`。
- 默认的 `loading` 效果时全屏的（覆盖在 `body` 上）。如果某个请求是需要在某个指定元素上显示 `loading` 效果，可以将请求 `header` 中 `loadingTarget` 设置为该元素的选择符。

（2）接着在 main.js 中将该组件引入，并定义成原型属性方便使用。

```js
import http from './utils/http.js'
Vue.prototype.axios = http
```

## 使用样例

（1）正常情况下，每次发起请求页面上都会显示一个全屏的 `loading` 效果。

```js
this.axios.get("/api/menu").then(response => {
  this.menus = response.data;
})
```

![](https://www.hangge.com/blog_uploads/201908/2019081010013559515.png)

（2）如果在请求 `header` 中传递个 `showLoading:false` 参数，则该请求不会有 `loading` 效果。

```js
this.axios.get("/api/getDeviceDatas",{headers: {'showLoading': false}})
.then(response => {
  this.tableData = response.data;
})
```

![](https://www.hangge.com/blog_uploads/201908/2019081010075766014.png)

（3）如果将请求 `header` 中 `loadingTarget` 设置为页面上某个元素的选择符，则 `loading` 效果会只显示在个元素区域上。比如我们下面代码我们只让 loading 遮罩显示在弹出框的内容区域上。

```js
this.axios.get("/api/controlApp?method=" + method, {headers: {'loadingTarget': '#dialogContent'}})
.then(response => {
  // 重新加载数据
  this.loadAppData();
  this.$message.success('执行成功!');
})
```

![](https://www.hangge.com/blog_uploads/201908/2019081010141930853.png)
