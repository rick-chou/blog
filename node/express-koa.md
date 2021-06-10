## Express

通过 express 我们可以轻松的构建一个 web 服务器 例如以下代码就在 3000 端口创建了一个 web 服务器

```js
const express = require('express');

const app = express();

app.listen(3000, () => {
  console.log('start');
});
```

在我的理解中 express 就是一个对一系列中间件调用的函数

比如常见的处理 get 和 post 请求的方法都是中间件的调用

当我们 require express 的时候 本质上是导入了一个函数

可以查看源码 以下是入口文件 express.js 的截取代码

```js
exports = module.exports = createApplication;

/**
 * Create an express application.
 *
 * @return {Function}
 * @api public
 */

function createApplication() {
  var app = function (req, res, next) {
    app.handle(req, res, next);
  };

  mixin(app, EventEmitter.prototype, false);
  mixin(app, proto, false);

  // expose the prototype that will get set on requests
  app.request = Object.create(req, {
    app: { configurable: true, enumerable: true, writable: true, value: app },
  });

  // expose the prototype that will get set on responses
  app.response = Object.create(res, {
    app: { configurable: true, enumerable: true, writable: true, value: app },
  });

  app.init();
  return app;
}
```

express.js 导出了一个函数 createApplication 也就是我们 require 导入的函数

在这个函数中初始化了一个 app 的函数 拥有 req / res / next 等参数 并通过 minin 这个函数混入了很多属性到 app 中

具体的 API 可查阅 <a href="http://expressjs.com/en/4x/api.html">http://expressjs.com/en/4x/api.html</a>

### 中间件

传递给 express 的一个回调函数 中间件位于请求和响应之间 所以它可以

• 执行任何代码

• 更改请求和响应对象

• 结束请求响应周期 res.end

• 调用下一个中间件

我们可以使用 app.use 的方法在全局注册中间件 这样所有的路由都会匹配到这个中间件

也可以在具体的路由中使用中间件 例如如下

```js
const middlewareA = ((req,res,next)=>{
  do something...
})

app.use(middlewareA)

app.get('/', middlewareA ,(req,res,next)=>{
  do something...
})
```

### 托管静态文件

```js
const express = require('express');
const path = require('path');

const app = express();

// 通过/static 来访问public文件夹
app.use('/static', express.static(path.join(__dirname, 'public')));

app.listen(8080, () => {
  console.log('静态资源服务器部署成功');
});
```

### get

get 请求的参数都在 url 中 我们可以通过 query 和 params 这两种形式来获取

```js
const express = require('express');

const app = express();

app.get('/query', (req, res, next) => {
  res.json(req.query);
});

app.get('/param/:id', (req, res, next) => {
  res.json(req.params);
});

app.listen(3000, () => {
  console.log('start');
});
```

### post

post 请求的参数在 body 但是如果我们直接打印 body 是看不到任何结果的

这是需要引入 body-parser 这个库

这个库和不同版本的 express 集成情况如下

- 3.x 内置

- 4.x 分离

- 4.16 内置函数

所以如果是 4.x 4.16 以前的版本 我们需要手动安装这个第三方库

```js
const express = require('express');

const app = express();

// 老版本写法 不推荐
// var bodyParser = require("body-parser");
// app.use(bodyParser.urlencoded({ extended: false }));
// app.use(bodyParser.json());

// 4.16以后的写法 推荐
// extended false 表示使用node的内置模块querystring来解析
// true 则表示使用第三方模块qs来解析
app.use(express.urlencoded({ extended: false }));
app.use(express.json());

app.post('/login', (req, res, next) => {
  console.log(req.body);
});

app.listen(8000, () => {
  console.log('start');
});
```

### 文件上传

如果使用原生的方法实现文件上传 后台处理起来会非常麻烦 因为二进制流中不仅有文件的信息 还有 header 的一些相关信息 我们可以打印一些相关的数据来看一下

```js
const express = require('express');

const app = express();

app.post('/upload', (req, res, next) => {
  console.log(req.headers);
  let data = '';
  req.on('data', (buffer) => {
    data += buffer;
  });
  req.on('end', () => {
    console.log(data.toString());
  });
});

app.listen(8080, () => {
  console.log('start~~~');
});
```

然后我们访问 localhost:8080/upload 并上传一个文件 就可以看到打印台输出了一段乱码的二进制流和请求头的 headers

headers 信息如下

```json
{
  "user-agent": "PostmanRuntime/7.13.0",
  "accept": "*/*",
  "cache-control": "no-cache",
  "postman-token": "e48f538d-2988-4e39-8d50-80fdede0ed02",
  "host": "localhost:8080",
  "accept-encoding": "gzip, deflate",
  "content-type": "multipart/form-data; boundary=--------------------------372446874075094600561084",
  "content-length": "376074",
  "connection": "keep-alive"
}
```

如果仔细对比 你就会发现二进制流中包含了 header 中的 content-type 中的 boundary 还有文件的 mime 类型等 如果不加处理的直接使用 fs 模块将这个二进制流写入到文件中 那么最终文件肯定无法被正确解析

如果引入 multer 模块 那么文件上传功能就会变得很简单了

例如 我们需要用户上传头像 代码如下

```js
const express = require('express');
const multer = require('multer');
const path = require('path');

const storage = multer.diskStorage({
  destination: function (req, file, cb) {
    //  第一个参数为err的相关信息 node的回调函数都是错误优先的回调
    //  第二个参数为文件上传的目的地
    cb(null, 'uploads');
  },
  filename: function (req, file, cb) {
    // 同样的 第一个参数为错误信息 第二个参数为文件名
    cb(
      null,
      file.fieldname + '-' + Date.now() + path.extname(file.originalname)
    );
  },
});

const upload = multer({ storage: storage });

const app = express();

app.post('/upload', upload.single('avatar'), (req, res, next) => {
  console.log(req.file);
});

app.listen(8080, () => {
  console.log('start~~~');
});
```

上述方法演示的是单文件上传 req.file 中存储了文件的相关信息 如下

```json
{
  "fieldname": "avatar",
  "originalname": "CHOU16.jpg",
  "encoding": "7bit",
  "mimetype": "image/jpeg",
  "destination": "uploads",
  "filename": "avatar-1616384964609.jpg",
  "path": "uploads\\avatar-1616384964609.jpg",
  "size": 375864
}
```

如果需要支持多文件上传 例如还需要上传用户背景图 代码可改写成如下

```js
app.post(
  '/fields',
  upload.fields([
    { name: 'avatar', macCount: 1 },
    { name: 'bg', maxCount: 2 },
  ]),
  (req, res, next) => {
    console.log(req.files);
  }
);

// {
//   avatar: [
//     {
//       fieldname: 'avatar',
//       originalname: 'CHOU1.jpg',
//       encoding: '7bit',
//       mimetype: 'image/jpeg',
//       destination: 'uploads',
//       filename: 'CHOU1.jpg',
//       path: 'uploads/CHOU1.jpg',
//       size: 646567
//     }
//   ],
//   bg: [
//     {
//       fieldname: 'bg',
//       originalname: 'CHOU2.jpg',
//       encoding: '7bit',
//       mimetype: 'image/jpeg',
//       destination: 'uploads',
//       filename: 'CHOU2.jpg',
//       path: 'uploads/CHOU2.jpg',
//       size: 398519
//     }
//   ]
// }
```

如果是需要多张背景图上传 同一个 field 则可以用 array 的写法 第二个参数为最大可上传的数量

如果超过了最大数量 multer 会返回 MulterError: Unexpected field

```js
app.post('/array', upload.array('bg', 3), (req, res, next) => {
  console.log(req.files);
});

// [
//   {
//     fieldname: 'bg',
//     originalname: 'CHOU1.jpg',
//     encoding: '7bit',
//     mimetype: 'image/jpeg',
//     destination: 'uploads',
//     filename: 'CHOU1.jpg',
//     path: 'uploads/CHOU1.jpg',
//     size: 646567
//   },
//   {
//     fieldname: 'bg',
//     originalname: 'CHOU2.jpg',
//     encoding: '7bit',
//     mimetype: 'image/jpeg',
//     destination: 'uploads',
//     filename: 'CHOU2.jpg',
//     path: 'uploads/CHOU2.jpg',
//     size: 398519
//   }
// ]
```

### router

如果将所有的请求处理都放在 index.js 中处理 那么 index.js 的代码就会变的很臃肿 这个时候我们可以使用路由来拆分我们的代码

例如项目中有一个用户模块 实现增删改查的功能

我们就可以新建一个 user.js 文件

```js
const express = require('express');

const router = express.Router();

router.get('/add', (req, res, next) => {
  res.end('added');
});

router.post('/delete', (req, res, next) => {
  res.end('deleted');
});

router.post('/update', (req, res, next) => {
  res.end('updated');
});

router.post('/select', (req, res, next) => {
  res.end('selected');
});

module.exports = router;
```

然后在 index.js 中导入这个路由

```js
const express = require('express');
const userRouter = require('./user');

const app = express();

app.use('/user', userRouter);

app.listen(3000, () => {
  console.log('start');
});
```

### DEMO

express 中的中间件都是同步代码 即一个中间件执行完毕才会去执行另一个中间件中的代码

例如我们有如下功能要实现 在 ABC 三个中间件中 分别追加给 message 追加内容 然后在 A 中间件中输出结果

```js
const express = require('express');

const app = express();

const middlewareA = (req, res, next) => {
  req.message = '';
  req.message += 'A';
  next();
  res.end(req.message);
};

const middlewareB = (req, res, next) => {
  req.message += 'B';
  next();
};

const middlewareC = async (req, res, next) => {
  req.message += 'C';
  next();
};

app.use(middlewareA);
app.use(middlewareB);
app.use(middlewareC);

app.listen(8000, () => {
  console.log(8000);
});
```

next 函数会在中间件栈中找到下一个中间件并执行 所以 middlewareA 中的 res.end 会在所有中间件都执行完毕后才执行 输入 ABC

那么 如果我们在第三个中间件中用定时器来模拟异步操作 最终的结果会怎么样呢

将 middlewareC 改造成如下

```js
const middlewareC = (req, res, next) => {
  setTimeout(() => {
    req.message += 'C';
  }, 0);
  next();
};
```

通过访问 8000 端口 我们可以看到这次最终的输出变成了 AB 由此可见

**中间件中的代码都是同步调用的**

而这 也是 express 面对异步场景下的一种无力 而 koa 则不一样

## Koa

koa 支持 async 和 await 的用法 这就意味着在 koa 中可以抛去 express 中回调函数的写法 用一种更优雅的方式来解决异步场景

### 基本使用

与 express 不同的是 koa 导出的不是函数 而是一个名为 Application 的对象

所以在使用上我们只需要 new 一个实例即可 其他用法和 Express 基本相似

```js
const Koa = require('koa');

const app = new Koa();

app.listen(8080, () => {
  console.log('Koa');
});
```

### 路由

这里我们借助第三方模块 koa-router 因为 koa 本身很纯净 基本所有功能都要通过第三方插件来实现

新建一个 user.js 的路由模块 Koa 将 express 中的 request 和 response 都合成到了上下文对象 context 中 简写为 ctx

```js
const Router = require('koa-router');

const userRouter = new Router({ prefix: '/user' });

userRouter.get('/home', (ctx, next) => {
  ctx.body = 'welcome~~';
});

userRouter.post('/login', (ctx, next) => {
  ctx.body = 'login';
});

module.exports = userRouter;
```

然后在 index 中引入 user.js

```js
const Koa = require('koa');

const userRouter = require('./router');

const app = new Koa();

app.use(userRouter.routes());

app.listen(8080, () => {
  console.log('Koa');
});
```

### 处理请求

koa 中需要引入 koa-bodyparser 来解析 json 和 urlencoded

```js
const Koa = require('koa');
const Router = require('koa-router');
const bodyParser = require('koa-bodyparser');

const app = new Koa();
const router = new Router();

app.use(bodyParser());
app.use(router.routes());

// 解析query
router.get('/query', (ctx, next) => {
  console.log(ctx.request.query);
});

// 解析params
router.get('/params/:id', (ctx, next) => {
  console.log(ctx.request.params);
});

// 解析urlencoded
router.post('/urlencoded', (ctx, next) => {
  console.log(ctx.request.body);
});

// 解析json
router.post('/json', (ctx, next) => {
  console.log(ctx.request.body);
});

app.listen(8080, () => {
  console.log('start');
});
```

注意 koa-bodyparser 中间件需要最先被使用

### 异步处理

重新回到 express 中的 demo 如果想在 koa 中处理异步操作就变的非常简单了

```js
const Koa = require('koa');
const axios = require('axios');
const app = new Koa();

const middlewareA = async (ctx, next) => {
  ctx.msg = '';
  ctx.msg += 'A';
  await next();
  ctx.body = ctx.msg;
};

const middlewareB = async (ctx, next) => {
  ctx.msg += 'B';
  await next();
};

const middlewareC = async (ctx) => {
  const res = await axios.get('https://koa.bootcss.com/');
  ctx.msg += res.data;
};

app.use(middlewareA);
app.use(middlewareB);
app.use(middlewareC);

app.listen(8080, () => {
  console.log('Koa');
});
```

### 洋葱模型

洋葱模型其实不是什么高大尚的概念 通过下图我们不难发现

所有中间件都会被 request 访问两次 就像剥洋葱一样 这就是洋葱模型

<img src="https://gitee.com/LuckyChou/blog-images/raw/master/node/middleware.png" />

## express 对比 koa

- express 是完整和强大的，其中帮助我们内置了非常多好用的功能;

- koa 是简洁和自由的，它只包含最核心的功能，并不会对我们使用其他中间件进行任何的限制。 甚至是在 app 中连最基本的 get、post 都没有给我们提供;我们需要通过自己或者路由来判断请求方式或者其他功能

- koa 中间件支持 async await
