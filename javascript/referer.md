---
title: JavaScript 防盗链
date: 2020-10-10
tags:
  - JavaScript
categories:
  - JS篇
---

> HTTP Referer是header的一部分，
  当浏览器向web服务器发送请求的时候，一般会带上Referer，
  告诉服务器我是从哪个页面链接过来的，服务器藉此可以获得一些信息用于处理。
  通过该头域的值，我们可以检测到访问目标资源的源地址

<!--more-->

## 背景

前段日子 在自学做项目的时候发现 明明在项目中引用了线上存在的图片 但是在自己的项目中却怎么也显示出来 

查阅资料后发现是这些第三方网站设置了**防盗链**

## 破解防盗链

先说说防盗链的原理，http 协议中，如果从一个网页跳到另一个网页，http 头字段里面会带个 Referer。

这里的Referer是由于历史原因导致了拼写错误 后来也就一直沿用。

图片服务器通过检测 Referer 是否来自规定域名，来进行防盗链。

如果盗用网站是 https 的 protocol，而图片链接是 http 的话，

则从 https 向 http 发起的请求会因为安全性的规定，而不带 referer，从而实现防盗链的绕过。

官方输出图片的时候，判断了来源(Referer)，就是从哪个网站访问这个图片，

如果是你的网站去加载这个图片，那么Referer就是：你的网站地址；

你网站地址，肯定没在官方的白名单内，所以就看不到图片了。

因此，若不发送Referer，也就是没有来源。那么官方那边，就认为是从浏览器直接访问的，所以就能加载正常的图片了。

```html
<meta name="referrer" content="never">
```

比如在掘金上 查看我的一篇文章中一张图片的Request Headers 就可以看到Referer

<img src="https://gitee.com/LuckyChou/blog-images/raw/master/js/referer.png" alt="referer" />


