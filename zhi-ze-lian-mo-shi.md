---
description: 使多个对象都有机会处理请求，从而避免请求的发送者和接受者之间的耦合关系，将这些对象连成一条链，并沿着这条链传递该请求，直到有一个对象处理它为止
---

# 职责链模式

### 现实情况

当公交车上人特别多时，经常情况下乘客从前门根本上去不，很多乘客都是从后门上公交，需要将公交卡向前面递，通常要在 N 个人手上传递，才能最终刷卡。

### 应用

假设我们负责一个售卖手机的电商网站，经过分别交纳 500 元定金和 200 元定金的两轮预定 后\(订单已在此时生成\)，现在已经到了正式购买的阶段。

公司针对支付过定金的用户有一定的优惠政策。在正式购买后，已经支付过 500 元定金的用 户会收到 100 元的商城优惠券，200 元定金的用户可以收到 50 元的优惠券，而之前没有支付定金 的用户只能进入普通购买模式，也就是没有优惠券，且在库存有限的情况下不一定保证能买到。

我们的订单页面是 PHP 吐出的模板，在页面加载之初，PHP 会传递给页面几个字段。

* orderType: 表示订单类型\(定金用户或者普通购买用户\)，code 的值为 1 的时候是 500 元 定金用户，为 2 的时候是 200 元定金用户，为 3 的时候是普通购买用户。
* pay: 表示用户是否已经支付定金，值为 true 或者 false, 虽然用户已经下过 500 元定金的 订单，但如果他一直没有支付定金，现在只能降级进入普通购买模式。
* stock: 表示当前用于普通购买的手机库存数量，已经支付过 500 元或者 200 元定金的用 户不受此限制。

```javascript
var order = function(orderType, pay, stock) {
  if (orderType === 1) {
    // 500 元定金购买模式
    if (pay === true) {
      // 已支付定金
      console.log('500 元定金预购, 得到 100 优惠券')
    } else {
      // 未支付定金，降级到普通购买模式
      if (stock > 0) {
        // 用于普通购买的手机还有库存
        console.log('普通购买, 无优惠券')
      } else {
        console.log('手机存货不足')
      }
    }
  } else if (orderType === 2) {
    if (pay === true) {
      // 200 元定金购买模式
      console.log('200 元定金预购, 得到 50 优惠券')
    } else {
      if (stock > 0) {
        console.log('普通购买, 无优惠券')
      } else {
        console.log('手机库存不足')
      }
    }
  } else if (orderType === 3) {
    if (stock > 0) {
      console.log('普通购买，无优惠券')
    } else {
      console.log('手机存货不足')
    }
  }
}

order(1, true, 500)
```

这样写 `order` 函数不仅巨大到难以阅读，而且需要经常进行修改。虽然目前项目能正常运行，但接下来的维护工作非常困难。

### 使用职责链模式重构

先把 500 元订单、200 元订单以及普通分成 3 个函数

接下来把 orderType、pay、stock 这 3 个字段当作参数传递给 500 元订单函数，如果该函数不符合处理条件，则把这个请求传递给后面的 200 元订单函数，如果 200 元订单函数依然不能处理该请求，则继续传递请求给普通购买函数，代码如下

```javascript
// 500 元订单
var order500 = function(orderType, pay, stock) {
  if (orderType === 1 && pay === true) {
    console.log('500 元定金预购, 得到 100 优惠券')
  } else {
    order200(orderType, pay, stock) // 将请求传递给 200 元订单
  }
}

// 200 元订单
var order200 = function(orderType, pay, stock) {
  if (orderType === 2 && pay === true) {
    console.log('200 元定金预购, 得到 50 优惠券')
  } else {
    orderNormal(orderType, pay, stock) // 将请求传递给普通订单 }
  }
}
// 普通购买订单
var orderNormal = function(orderType, pay, stock) {
  if (stock > 0) {
    console.log('普通购买, 无优惠券')
  } else {
    console.log('手机库存不足')
  }
}
// 测试结果:
order500(1, true, 500)
order500(1, false, 500)
order500(2, true, 500)
order500(3, false, 500)
order500(3, false, 0)
```

虽然这种方法代码结构已经清晰了很多，但是如果某天我们去掉 200 元订单，我们必须要还要改动 500 元中的代码。这种情况是违反开放-封闭原则的。

因此我们可以采用一种更灵活的方式，目标是让链中的各个节点可以灵活拆分和重组，我们约定，如果某个节点不能处理请求，则返回一个特定的字符串 `next` 来表示该请求需要继续往后面传递

```javascript
// 500 元订单
var order500 = function(orderType, pay, stock) {
  if (orderType === 1 && pay === true) {
    console.log('500 元定金预购, 得到 100 优惠券')
  } else {
    return 'next'
  }
}

// 200 元订单
var order200 = function(orderType, pay, stock) {
  if (orderType === 2 && pay === true) {
    console.log('200 元定金预购, 得到 50 优惠券')
  } else {
    return 'next'
  }
}
// 普通购买订单
var orderNormal = function(orderType, pay, stock) {
  if (stock > 0) {
    console.log('普通购买, 无优惠券')
  } else {
    console.log('手机库存不足')
  }
}
```

接下来需要把函数包装进职责链节点，我们定义一个构造函数 Chain, 在 new Chain 的时候传递的参数即为需要被包装的函数，同时它还拥有一个实例属性 this.successor， 表示在链中的下一个节点

```javascript
const Chain = function(fn) {
  this.fn = fn
  this.successor = null
}
Chain.prototype.setNextSuccessor = function(successor) {
  return (this.successor = successor)
}
Chain.prototype.passRequest = function() {
  // 调用自身函数
  var ret = this.fn.apply(this, arguments)
  if (ret === 'next') {
    // 调用下一个函数
    return (
      this.successor &&
      this.successor.passRequest.apply(this.successor, arguments)
    )
  }

  return ret
}
```

把三个订单函数分别包装成职责链的节点

```javascript
var chainOrder500 = new Chain( order500 )
var chainOrder200 = new Chain( order200 )
var chainOrderNormal = new Chain( orderNormal )
```

然后指定节点在职责链中的顺序

```javascript
chainOrder500.setNextSuccessor( chainOrder200 );
chainOrder200.setNextSuccessor( chainOrderNormal );
```

最后把请求传递给第一个节点

```javascript
chainOrder500.passRequest(1, true, 500) // 输出:500 元定金预购，得到 100 优惠券
chainOrder500.passRequest(2, true, 500) // 输出:200 元定金预购，得到 50 优惠券
chainOrder500.passRequest(3, true, 500) // 输出:普通购买，无优惠券
chainOrder500.passRequest(1, false, 0) // 输出:手机库存不足
```

通过改进，我们可以自由灵活地增加、移除和修改链中的节点顺序，假如某天网站运营人员又想出支持 300 元

```javascript
var order300 = function() {
  // 具体实现略
}
chainOrder300 = new Chain(order300)
chainOrder500.setNextSuccessor(chainOrder300)
chainOrder300.setNextSuccessor(chainOrder200)
```

### 用 AOP 实现职责链

```javascript
Function.prototype.after = function(fn) {
  const self = this
  return function() {
    // 拿到调用 after 的返回结果
    const ret = self.apply(this, arguments)
    if (ret === 'next') {
      return fn.apply(this, arguments)
    }
    return ret
  }
}
​
var order = order500yuan.after(order200yuan).after(orderNormal)
```

职责链中节点数量和顺序可以自由变化，我们可以在运行时决定链中包含哪些节点。作用域、原型链以及 DOM 节点的事件冒泡，都运用到了职责链模式的影子。

## 小结

用 AOP 来实现职责链既简单又巧妙，但这种把函数叠在一起的方式，同时也叠加了函数的 作用域，如果链条太长的话，也会对性能有较大的影响。

