## 生成随机 json

```js
import Mock from 'mockjs';

Mock.mock({
  'string|1-10': '★', //随机生成 1-10 个字符串"★"
  'string2|3': '★', //固定生成 3 个字符串"★"
  'number|+1': 202, //每次请求自增 1,初始值为 202
  'number2|1-100.1-10': 1, //生成一个浮点数，整数部分1-100，小数部分保留1-10 位。。
  'boolean|1-2': true, //值为 true 的概率是 1/(1+2)，值为 false 的概率同样是 2/3。
  regexp: /[a-z][A-Z][0-9]/, //随机生成满足正则的字符串
  'object|2': {
    310000: '上海市',
    320000: '江苏省',
    440000: '广东省',
  }, //对象中随机选取 2 个属性，生成对象
  'array|1': ['AMD', 'CMD'], //数组中随机选取 1 个元素，最终生成值
  'arrayRepeat|3': ['AMD', 'CMD'], //重复数组元素 3 次，最终生成数组
  date: '@date', //生成随机日期
});
```

## 拦截请求 （不会真的发送请求）

```js
import Mock from 'mockjs';

Mock.mock('/api', {
  'string|4': [
    {
      'name1|2-9': '哈',
      'name2|5': '哈',
    },
  ],
  'number|4': [
    {
      'id|+1': 1,
      'num1|2-9': 1,
      'num2|1-100.1-10': 1,
    },
  ],
  'boolean|4': [
    {
      'success1|1': ['true', 'false'],
      'success2|1': true,
    },
  ],
  'obj|2': {
    name: '@cname',
    region: '@region',
  },
  'Mock.Random|4': [
    {
      boo1: '@boolean',
      img: '@image',
      name: '@name',
      color: '@color',
      title: '@title',
    },
  ],
});
```
