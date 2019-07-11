---
description: >-
  当客户不方便访问一个对象或不满足需要的时候，提供一个替身对象来控制对这个对象的访问，所以客户实际上访问的是替身对象，替身对象对请求做出一些处理之后，再把请求转交给本体对象
---

# 代理模式

## 虚拟代理

> 把一些开销很大的对象，延迟到真正需要它的时候才去创建

例如图片的预加载技术，如果直接给 `img` 设置 `src` 属性，有时会因为图片太大或者网络不佳造成很长时间的空白，常见的做法是先用一张 loading 图占位，待真正的图片加载好之后再做替换。

首先创建一个普通的图像类，暴露一个 `setSrc` 接口用来设置 `img` 标签的 `src` 属性

```javascript
const myImage = (function() {
  const imgNode = document.createElement("img")
  document.body.appendChild(imgNode)

  return {
    setSrc: function(src) {
      imgNode.src = src
    }
  }
})()
```

引入代理对象 `proxyImg` ，在图片被真正加载好之前，页面中用本地图片来占位

```javascript
const proxyImage = (function() {
  const img = new Image()
  // 图片加载完成调用此方法
  img.onload = function() {
    myImage.setSrc(this.src)
  }
  return {
    setSrc: function(src) {
      // 立即用本地图片代替
      myImage.setSrc("local_pic_url")
      img.src = src // img 开始加载
    }
  }
})()

proxyImage.setSrc( 'real_pic_url' )
```

我们通过 `proxyImg` 间接的访问 `myImage` ，并在访问 `myImage` 过程中加入了额外的操作，比如在真正的图片加载好之前，将 `myImage` 的 `src` 先设置为本地的图片，待图片加载好之后再做替换。

## 缓存代理

> 缓存代理可以为一个开销很大的运算提供暂时的存储，在下次运算时，如果传递进来的参数跟之前完全一致，则可以直接返回之前的运算结果

这里以一个简单的乘积计算作为例子，我们假装他非常耗时

```javascript
const mult = function() {
  let a = 1
  for (let i = 1; i < arguments.length; i++) {
    a *= arguments[i]
  }
  return a
}
```

下面我们加入缓存代理，输入相同参数时不需要再次计算

```javascript
const proxyMult = (function() {
  const cache = {}
  return function() {
    const args = Array.prototype.join.call(arguments, ",")
    if (args in cache) {
      return cache[args]
    }
    return (cache[args] = mult.apply(this, arguments))
  }
})()
```



