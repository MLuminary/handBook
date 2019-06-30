---
description: >-
  JavaScript
  中没有提供关于面向对象中类式的继承，也没有在语言层面提供抽象类和接口的支持。因此，在用设计模式编写代码的时候，必须要跟传统面向对象语言进行区分        
  - 曾探
---

# 关于 JavaScript

## 多态

> 同一操作作用于不同的对象上面，可以产生不同的解释和不同的执行结果。

多态背后的思想实际上就是吧「做什么」和「谁去做」分离开，要实现这一点，必须要消除类型耦合的关系。但 JavaScript 中变量类型是在运行期可变的，这就意味着 **JavaScript 对象的多态性是与生俱来的**

### **实现**

对于 Java 来说，如果想要实现多态，需要采用「向上转型」的方法。

```java
public abstract class Animal {
  abstract void makeSound();
}

public class Duck extends Animal {
  public void makeSound() {
    System.out.println("嘎嘎嘎");
  }
}

public class Chicken extends Animal {
  public void makeSound() {
     System.out.println("咯咯咯");
  }
}

public class AnimalSound {
  // 此处参数的类型传 Animal 可以同时接受 Duck 或 Chicken
  public void makeSound(Animal animal) {
    animal.makeSound();
  }
}

public class Test {
  public static void main(String args[]) {
    AnimalSound AnimalSound = new AnimalSound();
    Animal duck = new Duck();
    Animal chicken = new Chicken();
    AnimalSound.makeSound(duck); // 嘎嘎嘎
    AnimalSound.makeSound(chicken); // 咯咯咯
  }
}
```

为了让 `AnimalSound` 实现多态，需要写一个 `Animal` 类来让子类继承，其实只是为了解决类型的问题。JavaScript 因为其动态性就完全不存在这种问题。

```javascript
const makeSound = function( animal ) {
  animal.makeSound()
}

const Duck = function(){}
Duck.prototype.makeSound = function() {
  console.info('嘎嘎嘎')
}

const Chicken = function(){}
Chicken.prototype.makeSound = function() {
  console.info('咯咯咯')
}

//ES6

const makeSound = animal => {
  animal.makeSound()
}

class Duck {
  makeSound() {
    console.info('嘎嘎嘎')
  }
}

class Chicken {
  makeSound() {
    console.info('咯咯咯')
  }
}

makeSound(new Duck()) // 嘎嘎嘎
```

### 作用

> 多态的最根本好处在于，你不必再向对象询问“你是什么类型”而后根据得到的答案调用对象的某个行为                                      ---《重构： 改善既有代码的设计》

当有命令发出时，每个对象都知道自己要做什么，并不需要确认每个对象的类型或者值后再告诉他们需要做什么。换句话说，**多态最根本的作用就是通过把过程化的条件分支语句转化为对象的多态性，从而消除这些条件分支语句**。

例如如下例子，`renderMap` 会根据 `type` 值去渲染谷歌地图或者百度地图，但是如果又增加了其他地图，则必须改动 `renderMap` 中的代码，这就违反了「开闭原则」，即 **软件中的对象（类，模块，函数等等）应该对于扩展是开放的，但是对于修改是封闭的。**而且参数还是一些魔法字符串，很容易写错。

```javascript
const googleMap = {
  show() {
    console.info('谷歌地图')
  }
}

const baiduMap = {
  show() {
    console.info('百度地图')
  }
}

const renderMap = type => {
  if (type === 'google') {
    googleMap.show()
  } else if (type === 'baidu') {
    baiduMap.show()
  }
}

renderMap('baidu')
```

因此，我们需要让这些对象接受到 `renderMap` 命令时自己应该知道该干什么，我们改写 `renderMap` 如下。

```javascript
const renderMap = map => {
  if ( map.show instanceof Function ) {
    map.show()
  }
}

renderMap( baiduMap )
```

## 封装

> 将信息隐藏

封装包含对「数据的封装」：类似私有变量提供共有方法来获得，对「实现的封装」： 例如 lodash 中各种各样的方法，对「类型的封装」，隐藏掉对象的类型。对「变化的封装」。

## 原型模式

> 在以类为中心的面向对象编程语言中，类和对象的关系可以想象成铸模和铸件的关系，对象总是从类中创建而来。而在原型编程的思想中，类并不是必须的，一个对象是通过克隆另外一个对象所得到的

在 JavaScript 中，根对象为 `Object.prototype` ，它是一个空对象，我们在 JavaScript 中遇到的每个对象，实际上都是从 `Object.prototype` 中克隆出来的。`Object.prototype` 对象就是它们的原型。

### 特点

* 所有的数据都是对象
* 要得到一个对象，不是通过实例化类，而是找到一个对象作为原型并克隆它
* 对象会记住它「构造器」的原型
* 如果对象无法响应某个请求，它会把这个请求委托给它自己的原型

举个栗子

```javascript
const obj = { name: 'haoqin', age: 23 }

const A = function() {
  this.age = 22
}

A.prototype = obj

const a = new A()
// 对象会记住它「构造器」的原型
console.info(Object.getPrototypeOf(a)) // { name: 'haoqin', age: 23 }
console.info(a.age) // 22
//如果对象无法响应某个请求，它会把这个请求委托给它自己的原型
console.info(a.name) // 'haoqin'
```

A 并不是类，而是「函数构造器」，它既可以被用作普通函数也可以被用作构造器。当使用 `new` 运算符来调用此函数，此函数就是一个构造器。`new` 的实现如下

```javascript
function createObj() {
  const obj = {} // 生成一个空对象
  Constructor = [].shift.call(arguments) // 获得传入的函数构造器
  obj.__proto__ = Constructor.prototype // 指向构造器的原型
  const ret = Constructor.apply(obj, arguments) // 将构造器中的属性绑定到 obj 中
  return typeof ret === 'object' ? ret : obj // 如果函数构造器本身就有返回值
}

createObj(Constructor, ...params)
```

## 高阶函数

* 函数可以作为参数被传递
* 函数可以作为返回值输出

### 柯里化

> 「currying」又称部分求值。一个 currying 的函数首先会接受一些参数，接受这些参数后，该函数不会立即根据参数求值，而是继续返回一个函数，刚才传入的参数在函数形成的闭包中被保存起来，等到函数真正被需要求值的时候，之前传入的参数都会被用于最后的求值

#### 用途

参数复用、提前返回、延迟计算

```javascript
// 柯里化函数
const currying = function(fn) {
  const args = [] // 将之前的传递的参数保留
  return function() {
    // 如果未传入参数，就将之前存储的参数传递给函数并调用
    if (arguments.length === 0) {
      return fn.apply(this, args)
    } else {
      // 将参数传递给 args 保存
      ;[].push.apply(args, arguments)
      // 返回自身函数
      return arguments.callee
    }
  }
}

const cost = function(...params) {
  let money = 0
  for (let i = 0, l = params.length; i < l; i++) {
    money += params[i]
  }
  return money
}
// 转化成 currying 函数
const costFnc = currying(cost)
costFnc(100)
costFnc(200)
costFnc(300)
costFnc(400)(500)
// 之前都没有在求值
console.info(costFnc()) // 1500
```

### 反柯里化

> 「unCurrying」反柯里化函数，从字面讲，意义和用法跟函数柯里化相比正好相反，扩大适用范围，创建一个应用范围更广的函数。使得本来只有特定对象才适用的方法，扩展到更多的对象。

```javascript
Function.prototype.unCurrying = function() {
  const self = this
  return function() {
    return Function.prototype.call.apply(self, arguments)
  }
}

const obj = { length: 3, "0": 1, "1": 2, "2": 3 }

const push = Array.prototype.push.unCurrying()

push(obj, 2)

console.info(obj) // { length: 4, "0": 1, "1": 2, "2": 3, "3": 2 }
```

此时 `self` 就是 `Array.prototype.push`, 返回的内容可以写成 

```javascript
 Function.prototype.call.apply(Array.prototype.push, arguments)
```

因为 `a.apply(b, params)` 其实就是 `b.a(param1, param2, ..)` 所以就等同于

```javascript
Array.prototype.push.call(param1, param2, ...)
```

那么 `push(obj, 2)` 其实就是

```javascript
push(obj, 2) = Array.prototype.push.call(obj , 2)
```

个人感觉「反柯里化」其实就起到了简洁代码的作用，不需要每次都写 `Array.prototype.push.call` 

