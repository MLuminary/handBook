---
description: >-
  模板方法模式是一种只需使用继承就可以实现的非常简单的模式模板方法模式由两部分结构组成，第一部分是抽象父类，第二部分是具体的实现子类。通常在抽象父类中封装了子类的算法框架，包括一些公共方法以及封装子类中所有方法的执行顺序。子类通过继承这个抽象类，也继承了整个算法结构，并且可以选择重写父类的方法。
---

# 模板方法模式

## 用途

假如我们有一些平行的子类，各个子类之间有一些相同的行为，也有一些不同行为。实际上，相同的行为可以被搬移到另一个单一的地方，模板方法模式就是为了解决这个问题。在模板方法模式中，子类实现中的相同部分被上移到父类中，而将不同的部分留待子类来 实现。这也很好地体现了泛化的思想。

## Coffee or Tea

### Coffee

泡咖啡的步骤通常如下:

1. 把水煮沸
2. 用沸水冲泡咖啡
3. 把咖啡倒进杯子
4. 加糖和牛奶

```javascript
const Coffee = function() {}
Coffee.prototype.boilWater = function() {
  console.log('把水煮沸')
}
Coffee.prototype.brewCoffeeGriends = function() {
  console.log('用沸水冲泡咖啡')
}
Coffee.prototype.pourInCup = function() {
  console.log('把咖啡倒进杯子')
}
Coffee.prototype.addSugarAndMilk = function() {
  console.log('加糖和牛奶')
}
Coffee.prototype.init = function() {
  this.boilWater()
  this.brewCoffeeGriends()
  this.pourInCup()
  this.addSugarAndMilk()
}
const coffee = new Coffee()
coffee.init()
```

### Tea

泡茶的步骤与泡咖啡的步骤相差并不大，通常如下：

1. 把水煮沸
2. 用沸水浸泡茶叶
3. 把茶水倒进杯子
4. 加柠檬

```javascript
const Tea = function() {}
Tea.prototype.boilWater = function() {
  console.log('把水煮沸')
}
Tea.prototype.steepTeaBag = function() {
  console.log('用沸水浸泡茶叶')
}
Tea.prototype.pourInCup = function() {
  console.log('把茶水倒进杯子')
}
Tea.prototype.addLemon = function() {
  console.log('加柠檬')
}
Tea.prototype.init = function() {
  this.boilWater()
  this.steepTeaBag()
  this.pourInCup()
  this.addLemon()
}
const tea = new Tea()
tea.init()
```

### 分离共同点

我们分别泡好了一杯咖啡和一壶茶，经过思考和比较，我们发现咖啡和茶的冲泡过程是大同小异的。

* 原料不同：一个是咖啡，一个是茶，我们可以抽象为「饮料」
* 方式不同：一个是冲泡，一个是浸泡，我们抽象为「泡」
* 调料不同：一个是糖和牛奶，一个是柠檬，我们抽象为「调料」

```javascript
var Beverage = function() {}
Beverage.prototype.boilWater = function() {
  console.log('把水煮沸')
}
Beverage.prototype.brew = function() {
  throw new Error('请子类实现')
}
Beverage.prototype.pourInCup = function() {
  throw new Error('请子类实现')
}
Beverage.prototype.addCondiments = function() {
  throw new Error('请子类实现')
}
Beverage.prototype.init = function() {
  this.boilWater()
  this.brew()
  this.pourInCup()
  this.addCondiments()
}
```

### 创建 Coffee 和 Tea

饮料只是抽象的存在，我们要继承饮料类，并在子类中实现除 `boilWater` 的方法

```javascript
const Coffee = function() {}
Coffee.prototype = new Beverage()
Coffee.prototype.brew = function() {
  console.log('用沸水冲泡咖啡')
}
Coffee.prototype.pourInCup = function() {
  console.log('把咖啡倒进杯子')
}
Coffee.prototype.addCondiments = function() {
  console.log('加糖和牛奶')
}
const Coffee = new Coffee()
Coffee.init()
```

当调用 coffee 对象的 init 方法时，由于 coffee 对象和 Coffee 构造器的原型 prototype 上都没有对应的 init 方法，所以该请求会顺着原型链，被委托给Coffee 的「父类」Beverage 原型上的 init 方法。

而 Beverage.prototype.init 方法中已经规定好了泡饮料的顺序，所以我们能成功地泡出一杯 咖啡。

Tea 类基本如 coffee 类，再次就不写出来了。

本章一直讨论的是模板方法模式，那么在上面的例子中，到底谁才是所谓的模板方法呢?答 案是 Beverage.prototype.init。

Beverage.prototype.init 被称为模板方法的原因是，该方法中封装了子类的算法框架，它作 为一个算法的模板，指导子类以何种顺序去执行哪些方法。在 Beverage.prototype.init 方法中， 算法内的每一个步骤都清楚地展示在我们眼前。

