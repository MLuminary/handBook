---
description: 适配器模式的作用是解决两个软件实体间的接口不兼容问题。使用适配器模式后，原本由于接口不兼容而不能工作的两个软件实体可以一起工作
---

# 适配器模式

### 现实的应用场景

例如 vga 转 hdmi 的转接口，mac book pro 没有 vga 的插口，但是某些显示器还是只有 vga 的接口，你又不想扔掉你的显示器和 vga 线，所以你必须得买一个 vga 转 hdmi 的转接口，这样你的 mac book pro 才能正常连接显示器

### 应用

假设谷歌地图与百度地图的渲染如下

```javascript
const googleMap = {
  show: function() {
    console.log('开始渲染谷歌地图')
  }
}
const baiduMap = {
  display: function() {
    console.log('开始渲染百度地图')
  }
}
```

但是我们的渲染函数 `renderMap` 默认调用地图的 `show` 方法，所以如果 `renderMap` 直接调用 `baiduMap` 那肯定会报错，所以我们可以写一个百度地图的适配器

```javascript
var baiduMapAdapter = {
  show: function() {
    baiduMap.display()
  }
}
// 这样就可以正确的渲染了
renderMap(googleMap)
renderMap(baiduMap)
```

## 小结

适配器模式是一对相对简单的模式，我们可能会觉得适配器模式有点像代理模式和装饰器模式，但其实不然。适配器模式主要是为了解决已有两个接口之间不匹配的问题，他不需要考虑这两个接口是怎么实现的。装饰器模式是为了加强对象的功能，而代理模式是为了控制对对象的访问。

