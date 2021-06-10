## video

```html
<video
  src="cn/video/A.mp4"
  muted
  autoplay
  controls
  poster="poster.png"
  loop
></video>
```

### attribute

- muted 静音
- autoplay 自动播放
- controls 控制条
- poster="poster.png" 第一次没有播放时的封面
- loop 循环播放

<!--more-->

### 实现视频的自动播放

由于浏览器层面的一些限制

自动播放 会影响用户体验 所以浏览器已经做出限制

所以这里存在误区：

- autoplay 属性能实现自动播放

- 通过 video api 中的 play 来控制视频的自动播放

解决方案：

- 和 autoplay 属性一起加上静音标签 muted 因为静音下视频可以自动播放

- 静音播放后 如需播放声音 需要加上交互 在用户交互后 自行解除静音

### Plugin

使用过的一个还不错的 video 插件

<a href="https://www.npmjs.com/package/plyr">https://www.npmjs.com/package/plyr</a>
