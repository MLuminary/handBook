---
description: >-
  给对象动态的增加职责的方式称为装饰者模式，装饰者模式能够在不改变对象自身的基础上，在程序运行期间给对象动态地添加职责。跟继承相比，装饰者是一种更轻便灵活的做法，这是一种「即用即付」的方式，比如天冷了就多穿一件外套
---

# 装饰者模式

### 用途

在传统的面向对象语言中，给对象添加功能常常使用继承的方式，但是继承的方式并不灵活， 还会带来许多问题:一方面会导致超类和子类之间存在强耦合性，当超类改变时，子类也会随之 改变;另一方面，继承这种功能复用方式通常被称为“白箱复用”，“白箱”是相对可见性而言的， 在继承方式中，超类的内部细节是对子类可见的，继承常常被认为破坏了封装性。

### JavaScript 的装饰者

```javascript
var plane = {
  fire: function() {
    console.log('发射普通子弹')
  }
}

var missileDecorator = function() {
  console.log('发射导弹')
}

var atomDecorator = function() {
  console.log('发射原子弹')
}

var fire1 = plane.fire

plane.fire = function() {
  fire1()
  missileDecorator()
}

var fire2 = plane.fire

plane.fire = function() {
  fire2()
  atomDecorator()
}

plane.fire()
// 发射普通子弹
// 发射导弹
// 发射原子弹
```

### AOP 装饰函数

提供 after 与 before 两个方法，可以在不改变函数本身的情况下动态给此函数添加新功能

```javascript
Function.prototype.before = function(beforefn) {
  const __self = this // 保存原函数的引用
  return function() {
    beforefn.apply(this, arguments) // 执行新函数，且保证 this 不被劫持，新函数接受的参数也会被原封不动地传入原函数，新函数在原函数之前执行
    return __self.apply(this, arguments) // 执行原函数并返回原函数的执行结果
  }
}

Function.prototype.after = function(afterfn) {
  const __self = this
  return function() {
    let ret = __self.apply(this, arguments)
    afterfn.apply(this, arguments)
    return ret
  }
}
```

分离业务代码和数据统计代码，无论在什么语言中，都是 AOP 的经典应用之一。在项目开发的结尾阶段难免要加上很多统计数据的代码，这些过程可能让我们被迫改动早已封装好的函数。

假设有一个登录 button, 点击 button 会弹出登录浮层，与此同时要进行数据上报，来统计有多少用户点击了这个登录 button

```javascript
const showLogin = function() {
  log()
  console.info('打开登录浮层')
}
const log = function() {
  console.info('上报数据')
}
button.onclick = showLogin
```

我们看到在 showLogin 函数里，既要负责打开登录浮层，又要负责数据上报，这是两个层面 的功能，在此处却被耦合在一个函数里。使用 AOP 分离之后

```javascript
const showLogin = function() {
  console.info('打开登录浮层')
}
const log = function() {
  console.info('上报数据')
}

button.click = showLogin.after(log)
```

## 小结

装饰者模式与代理模式非常像，但其实区别还是很大的，区别有以下两点

* 代理模式代理对象与本体对象的关系可以静态的表达，也就是说这种关系一开始就可以被确定。而装饰者模式是一种动态的表达，他在一开始并不能确定对象的全部功能
* 装饰者和代理模式都是**加强功能**，但装饰者模式是实实在在的为对象增加新的职责和行为，而代理做的事情还是跟本体一样，只不过他可以比本体更「聪明」一点，比如当图片还未加载前先用默认图片替换。

