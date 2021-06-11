---
title: JavaScript 方法库
date: 2020-08-10
tags:
  - JavaScript
categories:
  - JS篇
description: 一些些工具函数
---

### cookie 转 JSON

```js
function cookieToJson() {
  let cookieArr = document.cookie.split(';')
  let obj = {}
  cookieArr.forEach((i) => {
    let arr = i.split('=')
    obj[arr[0].trim()] = arr[1]
    // 第二个key开始 key前面有一个空格 要去掉
  })
  return obj
}
```


### 保存cookie内容

```js
function SetCookie(name,value){
    var Days = 1;   //cookie 将被保存一天
    var exp  = new Date();  //获得当前时间
    exp.setTime(exp.getTime() + Days*24*60*60*1000);  //换成毫秒
    document.cookie = name + "="+ escape (value) + ";expires=" + exp.toGMTString();
}
```

### 读取cookie内容

```js
function getCookie(name) {
  var arr,
    reg = new RegExp('(^| )' + name + '=([^;]*)(;|$)')
  if ((arr = document.cookie.match(reg))) return unescape(arr[2])
  else return null
}
```


### 判断是否为微信浏览器

```js
function is_weixn() {
  var ua = navigator.userAgent.toLowerCase()
  if (ua.match(/MicroMessenger/i) == 'micromessenger') {
    return true
  } else {
    return false
  }
}
```

### 判断是否为移动端浏览器

```js
function isMobile() {
  if (
    window.navigator.userAgent.match(
      /(phone|pad|pod|iPhone|iPodios|iPad|Android|Mobile|BlackBerry|IEMobile|MQQBrowser|JUCFennec|wOSBrowser|BrowserNG|WebOS|Symbian|Windows Phone)/i
    )
  ) {
    return true // 移动端
  } else {
    return false // PC端
  }
}
```


### 分页

```js
var data = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

var result = []

var chunk = 3

for (var i = 0; i < data.length; i += chunk) {
  result.push(data.slice(i, i + chunk))
}

console.log(result)

// [ [ 1, 2, 3 ], [ 4, 5, 6 ], [ 7, 8, 9 ], [ 10 ] ]
```


### 获取url参数

```js
function getQuery(name) {
  var reg = new RegExp('(^|&)' + name + '=([^&]*)(&|$)', 'i')
  var r = window.location.search.substr(1).match(reg)
  if (r != null) return decodeURI(r[2])
  return null
}
```


### url search 转 object

```js
const getUrlSearchMap = (url) => {
    let search;
    if (url !== undefined) {
        let hashIndex = url.lastIndexOf('#');
        let searchIndex = url.lastIndexOf('?');
        if (searchIndex !== -1) {
            search = hashIndex === -1 ?
                url.substr(searchIndex + 1)
                :
                url.substring(searchIndex, hashIndex);
 
        } else if (hashIndex !== -1) {
            search = url.substr(hashIndex + 1);
        } else {
            return {};
        }
    } else {
        search = window.location.search;
    }
    if (!search) {
        return {};
    }
 
    let hashArr = search.replace('?', '').split('&');
    let hashMap = {};
    for (let i = 0; i < hashArr.length; i++) {
        let tempArr = hashArr[i].split('='),
            k = tempArr[0],
            v = tempArr[1] || true;
        if (hashMap[k]) {
            if (Array.isArray(hashMap[k])) {
                hashMap[k].push(v);
            } else {
                hashMap[k] = [hashMap[k], v];
            }
        } else {
            hashMap[k] = v;
        }
    }
    return hashMap;
};
```

### 获取页面滚动元素

把下面的代码粘贴到调试工具中运行一下，然后滚动页面，就可以看到是哪个元素产生的滚动了

```js
function findScroller(element) {
    element.onscroll = function() { console.log(element) }

    Array.from(element.children).forEach(findScroller)
}

findScroller(document.body)
```