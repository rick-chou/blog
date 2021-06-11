### Mac

```zsh
open -n -a '/Applications/Google Chrome.app' --args --user-data-dir="/tmp/chrome_dev_test" --disable-web-security
```

### Windows

```text
- 在电脑上新建一个目录，例如：C:\MyChromeDevUserData

- 在属性页面中的目标输入框里加上 --disable-web-security --user-data-dir=C:\MyChromeDevUserData，--user-data-dir 的值就是刚才新建的目录

- 点击应用和确定后关闭属性页面，并打开 chrome 浏览器
```

### 2021.4 更新

浏览器发送请求 但是无法带上 cookie 导致 页面一直重定向

1、在谷歌浏览器中搜索 chrome://flags/
2、在出现的页面搜索框中搜索 SameSite
3、把第一个改为 disable
4、然后重启浏览器
