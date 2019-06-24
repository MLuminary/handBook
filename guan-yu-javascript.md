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

