---
permalink: /posts/new7
display: normal
title: ajax 和 axios、fetch 的区别
tags: web
date: '2018-12-12 20:53:00 +08:00'
comment: true
layout: post
component: web
---
1.jQuery ajax**

```
$.ajax({
   type: 'POST',
   url: url,
   data: data,
   dataType: dataType,
   success: function () {},
   error: function () {}
});


```

传统 Ajax 指的是 XMLHttpRequest（XHR）， 最早出现的发送后端请求技术，隶属于原始 js 中，核心使用 XMLHttpRequest 对象，多个请求之间如果有先后关系的话，就会出现**回调地狱**。  
JQuery ajax 是对原生 XHR 的封装，除此以外还增添了对 **JSONP** 的支持。经过多年的更新维护，真的已经是非常的方便了，优点无需多言；如果是硬要举出几个缺点，那可能只有：  

1. 本身是针对 MVC 的编程, 不符合现在前端 **MVVM** 的浪潮  
2. 基于原生的 XHR 开发，XHR 本身的架构不清晰。  
   3.JQuery 整个项目太大，单纯使用 ajax 却要引入整个 JQuery 非常的不合理（采取个性化打包的方案又不能享受 CDN 服务）  
3. 不符合关注分离（Separation of Concerns）的原则  
4. 配置和调用方式非常混乱，而且基于事件的异步模型不友好。  
   **PS:MVVM(Model-View-ViewModel), 源自于经典的 Model–View–Controller（MVC）模式。MVVM 的出现促进了 GUI 前端开发与后端业务逻辑的分离，极大地提高了前端开发效率。MVVM 的核心是 ViewModel 层，它就像是一个中转站（value converter），负责转换 Model 中的数据对象来让数据变得更容易管理和使用，该层向上与视图层进行双向数据绑定，向下与 Model 层通过接口请求进行数据交互，起呈上启下作用。View 层展现的不是 Model 层的数据，而是 ViewModel 的数据，由 ViewModel 负责与 Model 层交互，这就完全解耦了 View 层和 Model 层，这个解耦是至关重要的，它是前后端分离方案实施的最重要一环。**  
   如下图所示：  

![](http://upload-images.jianshu.io/upload_images/6943526-cb96269b27bf3d2d.png)

image.png

**2.axios**

```
axios({
    method: 'post',
    url: '/user/12345',
    data: {
        firstName: 'Fred',
        lastName: 'Flintstone'
    }
})
.then(function (response) {
    console.log(response);
})
.catch(function (error) {
    console.log(error);
});


```

Vue2.0 之后，尤雨溪推荐大家用 axios 替换 JQuery ajax，想必让 axios 进入了很多人的目光中。  
axios 是一个基于 Promise 用于浏览器和 nodejs 的 HTTP 客户端，本质上也是对原生 XHR 的封装，只不过它是 Promise 的实现版本，符合最新的 ES 规范，它本身具有以下特征：  

1. 从浏览器中创建 XMLHttpRequest  
2. 支持 Promise API  
3. 客户端支持防止 CSRF  
4. 提供了一些并发请求的接口（重要，方便了很多的操作）  
5. 从 node.js 创建 http 请求  
6. 拦截请求和响应  
7. 转换请求和响应数据  
8. 取消请求  
9. 自动转换 JSON 数据  
   **PS: 防止 CSRF: 就是让你的每个请求都带一个从 cookie 中拿到的 key, 根据浏览器同源策略，假冒的网站是拿不到你 cookie 中得 key 的，这样，后台就可以轻松辨别出这个请求是否是用户在假冒网站上的误导输入，从而采取正确的策略。**  
   **3.fetch**

```
try {
  let response = await fetch(url);
  let data = response.json();
  console.log(data);
} catch(e) {
  console.log("Oops, error", e);
}


```

fetch 号称是 AJAX 的替代品，是在 ES6 出现的，使用了 ES6 中的 promise 对象。Fetch 是基于 promise 设计的。Fetch 的代码结构比起 ajax 简单多了，参数有点像 jQuery ajax。但是，一定记住 **fetch 不是 ajax 的进一步封装，而是原生 js，没有使用 XMLHttpRequest 对象**。  
fetch 的优点：  

1. 符合关注分离，没有将输入、输出和用事件来跟踪的状态混杂在一个对象里  
2. 更好更方便的写法  
   坦白说，上面的理由对我来说完全没有什么说服力，因为不管是 Jquery 还是 Axios 都已经帮我们把 xhr 封装的足够好，使用起来也足够方便，为什么我们还要花费大力气去学习 fetch？  
   我认为 fetch 的优势主要优势就是：

```
1.  语法简洁，更加语义化
2.  基于标准 Promise 实现，支持 async/await
3.  同构方便，使用 [isomorphic-fetch](https://github.com/matthew-andrews/isomorphic-fetch)
4.更加底层，提供的API丰富（request, response）
5.脱离了XHR，是ES规范里新的实现方式


```

最近在使用 fetch 的时候，也遇到了不少的问题：  
fetch 是一个低层次的 API，你可以把它考虑成原生的 XHR，所以使用起来并不是那么舒服，需要进行封装。  
例如：

```
1）fetch只对网络请求报错，对400，500都当做成功的请求，服务器返回 400，500 错误码时并不会 reject，只有网络错误这些导致请求不能完成时，fetch 才会被 reject。
2）fetch默认不会带cookie，需要添加配置项： fetch(url, {credentials: 'include'})
3）fetch不支持abort，不支持超时控制，使用setTimeout及Promise.reject的实现的超时控制并不能阻止请求过程继续在后台运行，造成了流量的浪费
4）fetch没有办法原生监测请求的进度，而XHR可以


```

**总结：axios 既提供了并发的封装，也没有 fetch 的各种问题，而且体积也较小，当之无愧现在最应该选用的请求的方式。
