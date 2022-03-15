# Portal

## 简介

Portals 提供了一种很好的将子节点渲染到父组件以外的 DOM 节点的方式。

通常你的 APP 只有一个 Root

```html
<body>
  <div id="root"></div>
</body>
```

使用 Portal 之后，可以变成下面这样

```html
<body>
  <div id="root"></div>
  <div id="portal"></div>
</body>
```
