---
title: JavaScript 模块化
date: 2020-08-24
tags:
 - JavaScript
categories:
 - JS篇
---


## commonjs


require / exports


<!--more-->

### exports

```js
const Tool = {
  agent: {
    getActivityList: () => {
      console.log("activityList...")
    },
    updateActivityList: () => {
      console.log("updateActivityList...")
    }
  },
  serve: {
    sendMeg: (data) => {
      console.log(data)
    }
  }
}

exports.Tool = Tool
```

### require

```js
const { Tool } = require('./commonjs exports')

Tool.serve.sendMeg('Hello')

console.log(Tool.agent.getActivityList)
```

## ES6

### export
```js
const Tool = {
  agent: {
    getActivityList: () => {
      console.log("activityList...")
    },
    updateActivityList: () => {
      console.log("updateActivityList...")
    }
  },
  serve: {
    sendMeg: (data) => {
      console.log(data)
    }
  }
}

export default Tool
```

### require
```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>ES6 Module</title>
</head>

<body>
  <script type="module">
    import Tool from './ES6 export.js'
    console.log(Tool)
  </script>
</body>

</html>
```

浏览器还未很好的支持ES6 Module

script标签默认type属性为javascript

在引入module时 需要告知浏览器这是module
