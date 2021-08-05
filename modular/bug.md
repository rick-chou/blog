## global is not defined

### Que

在`vite`下使用了`react-codemirror2`

控制台报错`global is not defined`

### Solution

在入口文件 `index.html` 中加入

```html
<script>
  window.global = window;
</script>
```
