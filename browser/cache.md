<img src="https://cdn.jsdelivr.net/gh/LuckyChou710/blog-images/js/http/cache.jpg" />

## 目的

浏览器缓存（Browser Caching）是为了节约网络的资源加速浏览

浏览器在用户磁盘上对最近请求过的文档进行存储，当访问者再次请求这个页面时

浏览器就可以从本地磁盘显示文档，这样就可以加速页面的阅览

## 简单释义

浏览器缓存其实就是浏览器保存通过 HTTP 获取的所有资源

是浏览器将网络资源存储在本地的一种行为

浏览器的缓存机制是根据 HTTP 报文的缓存标识进行的

## 策略

通常浏览器缓存策略分为两种

强缓存（**Expires**，**cache-control**）

协商缓存（**last-modified** ，**Etag**）

并且缓存策略都是通过设置 HTTP Header 来实现的

<img src="https://cdn.jsdelivr.net/gh/LuckyChou710/blog-images/js/http/http4.png" />

### 强制缓存

#### Expires

response header 里的过期时间，浏览器再次加载资源时，如果在这个过期时间内，则命中强制缓存。

#### Cache-Control

当值设为 max-age=300 时，则代表在这个请求正确返回时间（浏览器也会记录下来）的 5 分钟内再次加载资源，就会命中强缓存。

<img src="https://cdn.jsdelivr.net/gh/LuckyChou710/blog-images/js/http/http7.png">

#### Expires 和 Cache-Control 的区别

1. Expires 是 http1.0 的产物，Cache-Control 是 http1.1 的产物

2. 两者同时存在的话，Cache-Control 优先级高于 Expires

3. 在某些不支持 HTTP1.1 的环境下，Expires 就会发挥用处。所以 Expires 其实是过时的产物，现阶段它的存在只是一种兼容性的写法

4. Expires 是一个具体的服务器时间，这就导致一个问题，如果客户端时间和服务器时间相差较大，缓存命中与否就不是开发者所期望的。Cache-Control 是一个时间段，控制就比较容易

### 协商缓存

#### ETag / If-None-Match

这两个要一起说。Etag 是上一次加载资源时，服务器返回的 response header，是对该资源的一种唯一标识，只要资源有变化，Etag 就会重新生成。浏览器在下一次加载资源向服务器发送请求时，会将上一次返回的 Etag 值放到 request header 里的 If-None-Match 里，服务器接受到 If-None-Match 的值后，会拿来跟该资源文件的 Etag 值做比较，如果相同，则表示资源文件没有发生改变，命中协商缓存

#### Last-Modified 和 If-Modified-Since

这两个也要一起说。Last-Modified 是该资源文件最后一次更改时间，服务器会在 response header 里返回，同时浏览器会将这个值保存起来，在下一次发送请求时，放到 request header 里的 If-Modified-Since 里，服务器在接收到后也会做比对，如果相同则命中协商缓存。

<img src="https://cdn.jsdelivr.net/gh/LuckyChou710/blog-images/js/http/http8.png">

#### ETag 和 Last-Modified 区别

1. 在方式上，Etag 是对资源的一种唯一标识，而 Last-Modified 是该资源文件最后一次更改时间

2. 在精确度上，Etag 要优于 Last-Modified。Last-Modified 的时间单位是秒，如果某个文件在 1 秒内改变了多次，那么他们的 Last-Modified 其实并没有体现出来修改，但是 Etag 每次都会改变确保了精度；如果是负载均衡的服务器，各个服务器生成的 Last-Modified 也有可能不一致

3. 在性能上，Etag 要逊于 Last-Modified，毕竟 Last-Modified 只需要记录时间，而 Etag 需要服务器通过算法来计算出一个 hash 值

4. 在优先级上，服务器校验优先考虑 Etag。

## 浏览器缓存过程

1. 浏览器第一次加载资源，服务器返回 200，浏览器将资源文件从服务器上请求下载下来，并把 response header 及该请求的返回时间一并缓存

2. 下一次加载资源时，先比较当前时间和上一次返回 200 时的时间差，如果没有超过 cache-control 设置的 max-age，则没有过期，命中强缓存，不发请求直接从本地缓存读取该文件（如果浏览器不支持 HTTP1

3. 则用 expires 判断是否过期）；如果时间过期，则向服务器发送 header 带有 If-None-Match 和 If-Modified-Since 的请求

4. 服务器收到请求后，优先根据 Etag 的值判断被请求的文件有没有做修改，Etag 值一致则没有修改，命中协商缓存，返回 304；如果不一致则有改动，直接返回新的资源文件带上新的 Etag 值并返回 200

5. 如果服务器收到的请求没有 Etag 值，则将 If-Modified-Since 和被请求文件的最后修改时间做比对，一致则命中协商缓存，返回 304；不一致则返回新的 last-modified 和文件并返回 200

<img src="https://cdn.jsdelivr.net/gh/LuckyChou710/blog-images/js/http/http9.png">

## 存储位置

从存储位置来看，浏览器缓存一共分为四种，并且各自有优先级，当依次查找缓存且都没有命中的时候，才会去请求网络

- Service Worker

- Memory Cache

- Disk Cache

- Push Cache

### Service Worker

Service Worker 是运行在浏览器背后的独立线程，一般可以用来实现缓存功能。使用 Service Worker 的话，传输协议必须为 HTTPS

因为 Service Worker 中涉及到请求拦截，所以必须使用 HTTPS 协议来保障安全

**Service Worker 的缓存与浏览器其他内建的缓存机制不同，它可以让我们自由控制缓存哪些文件、如何匹配缓存、如何读取缓存，并且缓存是持续性的**

Service Worker 实现缓存功能一般分为三个步骤

1. 首先需要先注册 Service Worker

2. 然后监听到 install 事件以后就可以缓存需要的文件

3. 那么在下次用户访问的时候就可以通过拦截请求的方式查询是否存在缓存，存在缓存的话就可以直接读取缓存文件，否则就去请求数据

当 Service Worker 没有命中缓存的时候，我们需要去调用 fetch 函数获取数据

也就是说，如果我们没有在 Service Worker 命中缓存的话，会根据缓存查找优先级去查找数据

但是不管我们是从 Memory Cache 中还是从网络请求中获取的数据，浏览器都会显示我们是从 Service Worker 中获取的内容

### Memory Cache

Memory Cache 也就是内存中的缓存，主要包含的是当前中页面中已经抓取到的资源,例如页面上已经下载的样式、脚本、图片等

读取内存中的数据肯定比磁盘快,内存缓存虽然读取高效，可是缓存持续性很短，会随着进程的释放而释放。 **一旦我们关闭 Tab 页面，内存中的缓存也就被释放了**

⚠️：**内存缓存在缓存资源时并不关心返回资源的 HTTP 缓存头 Cache-Control 是什么值，同时资源的匹配也并非仅仅是对 URL 做匹配，还可能会对 Content-Type，CORS 等其他特征做校验**

### Disk Cache

Disk Cache 也就是存储在硬盘中的缓存，读取速度慢点，但是什么都能存储到磁盘中，比 Memory Cache 胜在容量和存储时效性上

它会根据 HTTP Herder 中的字段判断哪些资源需要缓存，哪些资源可以不请求直接使用，哪些资源已经过期需要重新请求

并且即使在跨站点的情况下，相同地址的资源一旦被硬盘缓存下来，就不会再次去请求数据。绝大部分的缓存都来自 Disk Cache

### Push Cache

Push Cache（推送缓存）是 HTTP/2 中的内容，当以上三种缓存都没有命中时，它才会被使用。

**它只在会话（Session）中存在，一旦会话结束就被释放，并且缓存时间也很短暂，在 Chrome 浏览器中只有 5 分钟左右**，

同时它也并非严格执行 HTTP 头中的缓存指令。 他有如下的一些特性

- 所有的资源都能被推送，并且能够被缓存,但是 Edge 和 Safari 浏览器支持相对比较差

- Push Cache 中的缓存只能被使用一次

- 可以给其他域名推送资源

- 浏览器可以拒绝接受已经存在的资源推送

- 一旦连接被关闭，Push Cache 就被释放

- 可以推送 no-cache 和 no-store 的资源

- 多个页面可以使用同一个 HTTP/2 的连接，也就可以使用同一个 Push Cache。这主要还是依赖浏览器的实现而定，出于对性能的考虑，有的浏览器会对相同域名但不同的 tab 标签使用同一个 HTTP 连接

## 用户行为对浏览器缓存的控制

1. 地址栏访问，链接跳转是正常用户行为，将会触发浏览器缓存机制

2. F5 刷新，浏览器会设置 max-age=0，跳过强缓存判断，会进行协商缓存判断

3. ctrl+F5 刷新，跳过强缓存和协商缓存，直接从服务器拉取资源

## 三级缓存原理 (访问缓存优先级)

1. 先在内存中查找,如果有,直接加载

2. 如果内存中不存在,则在硬盘中查找,如果有直接加载

3. 如果硬盘中也没有,那么就进行网络请求

4. 请求获取的资源缓存到硬盘和内存

## 问题

### 内存缓存和硬盘缓存有什么区别？

> 口诀：存速时空（存的速食都被我吃空了）

| 区别     | 内存缓存         | 硬盘缓存         |
| -------- | ---------------- | ---------------- |
| 存储内容 | JS，字体，图片等 | CSS 等           |
| 读取速度 | 快               | 慢               |
| 时效性   | 进程关闭则清空   | 可以缓存较长时间 |
| 空间     | 空间小           | 空间大           |

- **内存缓存(from memory cache)**：内存缓存主要的两个特点，分别是快速读取和时效性：

  - 快速读取：内存缓存会将编译解析后的文件，直接存入该进程的内存中，占据该进程一定的内存资源，以方便下次运行使用时的快速读取。一般 JS,字体，图片等会放在内存缓存中

  - 时效性：一旦该进程关闭，则该进程的内存则会清空。

- **硬盘缓存(from disk cache)**：硬盘缓存则是直接将缓存写入硬盘文件中，读取缓存需要对该缓存存放的硬盘文件进行 I/O 操作，然后重新解析该缓存内容，读取复杂，速度比内存缓存慢。退出进程不会清空。一般 JS,字体，图片等会放在内存中，而 CSS 则会放在硬盘缓存中

### 浏览器的缓存存放在哪里，如何在浏览器中判断强制缓存是否生效？

判断是否命中强制缓存：当命中强制缓存时，状态码为 200, 请求对应的 Size 值则代表该缓存存放的位置，

分别为 from memory cache 和 from disk cache

from memory cache 代表使用内存中的缓存，from disk cache 则代表使用的是硬盘中的缓存，浏览器读取缓存的顺序为 memory > disk

### 为什么 CSS 会放在硬盘缓存中？

因为 CSS 文件加载一次就可渲染出来,我们不会频繁读取它,所以它不适合缓存到内存中,但是 js 之类的脚本却随时可能会执行

如果脚本在磁盘当中,我们在执行脚本的时候需要从磁盘取到内存中来,这样 IO 开销就很大了,有可能导致浏览器失去响应

### 那么既然内存缓存这么高效，我们是不是能让数据都存放在内存中呢？

这是不可能的。计算机中的内存一定比硬盘容量小得多，操作系统需要精打细算内存的使用，所以能让我们使用的内存必然不多
