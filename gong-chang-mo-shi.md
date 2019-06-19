---
description: >-
  属于创建者模式，将模块中对象的创建和对象的使用进行分离，外界对于这些对象只需要知道它们的接口，而不需要知道其中具体的实现细节，以此来使整个系统的设计更加符合单一职责的原则。
---

# 工厂模式

### 存在的意义

* 工厂模式的存在就是为了**解耦，将对象的创建和使用分离。**
* **降低代码重复度**，对象的创建过于复杂时放到工厂模式中只需要书写一次即可。

就拿 KFC 来举例，你只需要去 KFC 告诉工作人员汉堡的名字即可拿到对应的汉堡，**不需要每个来 KFC 的人都自己做汉堡**，而且**你也不关心汉堡是如何做的**，**KFC 员工也不会关心你如何使用汉堡**，也实现了一定程度上的解耦。

### 简单工厂模式

简单工厂模式又叫静态工厂模式，可以根据参数的不同返回不同类的实例，简单工厂模式专门定义一个类来负责创建其他类的实例，被创建的实例通常都有共同的父类。

```javascript
// 汉堡工厂
const Hamburger = function(name) {
  function ChickenBurger() {
    this.name = '鸡腿堡'
    this.price = 12
  }

  function BeefBurger() {
    this.name = '牛肉汉堡'
    this.price = 15
  }
  
  function VeggieBurger() {
    this.name = '蔬菜汉堡'
    this.price = 10
  }

  switch (name) {
    case '鸡腿堡':
      return new ChickenBurger()
      break
    case '牛肉汉堡':
      return new BeefBurger()
      break
    case '蔬菜汉堡':
      return new VeggieBurger()
      break
    default:
      throw new Error('对不起，本店没有你想要的汉堡')
  }
}

const chickenBuger = new Hamburger('鸡腿堡')

```

ES6 实现方法

```javascript
class Hamburger {
// 因为各个汉堡的结构基本相同，此处可以抽象出来
  constructor(info) {
    this.name = info.name
    this.price = info.price
  }
  // 不会被实例继承，可以由 Hamburger 直接调用
  static getHamburger(name) {
    switch (name) {
      case '鸡腿堡':
        return new Hamburger({name: '鸡腿堡', price: 12})
        break
      case '牛肉汉堡':
        return new Hamburger({name: '牛肉汉堡', price: 15})
        break
      case '蔬菜汉堡':
        return new Hamburger({name: '蔬菜汉堡', price: 10})
        break
      default:
        throw new Error('对不起，本店没有你想要的汉堡')
    }
  }
}

const chickenBuger = Hamburger.getHamburger('鸡腿堡')
```

但是简单工厂也存在一些问题，当需要创建的对象过多或者创建对象的逻辑变得复杂的时候，工厂函数就会显得十分庞大臃肿，其复杂度将会直线提高。

### 工厂方法模式

工厂方法模式将实际创建对象的工作推迟到了子类中，工厂父类负责定义创建产品对象的公共接口。

```javascript
const Hamburger = function(name) {
  // this 是当前对象，则说明是用 new 创建的, 否则 this 应该是全局对象
  if(this instanceof Hamburger) {
    return new this[name]() // 这边将 this[name]() 就相当于 CHickenBuger() 返回对应的实例
  } else {
    // 如果不是用 new 创建的，就返回给他用 new 创建的 Hamburger 实例
    return new Hamburger(name)
  }
}

Hamburger.prototype = {
  ChickenBuger: function() {
    this.name = '鸡腿堡'
    this.price = 12
  },
  BeefBuger: function() {
    this.name = '牛肉汉堡'
    this.price = 15
  },
  VeggieBurger: function() {
    this.name = '蔬菜汉堡'
    this.price = 10
  }
}

const chickenBuger = Hamburger('ChickenBuger')
```

