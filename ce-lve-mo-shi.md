---
description: 定义一系列的算法，把它们一个个的封装起来，并且使它们可以互相替换
---

# 策略模式

## 具体实现

很多公司的年终奖是根据员工的工资基数和年底绩效情况来发放的。例如，绩效为 S 的人年 终奖有 4 倍工资，绩效为 A 的人年终奖有 3 倍工资，而绩效为 B 的人年终奖是 2 倍工资。假设财务部要求我们提供一段代码，来方便他们计算员工的年终奖

```javascript
const calculateBonus = function(performanceLevel, salary) {
  if (performanceLevel === "S") {
    return salary * 4
  }
  if (performanceLevel === "A") {
    return salary * 3
  }
  if (performanceLevel === "B") {
    return salary * 2
  }
}

calculateBonus("B", 20000) 
```

以上的方式很好的满足了此次的需求，但是缺点也是显而易见的

假如再增加一种绩效，是需要改动 `calculateBonus` 里的代码的；此函数也会越来越庞大，之后会难于维护；其中的计算逻辑也难以复用

### 使用策略模式优化

将不变的部分和变化的部分隔开是每个设计模式的主题，策略模式也不例外，**策略模式的目的就是将算法的使用与算法的实现相分离**

我们先来模仿传统面向对象语言中的实现，首先是相应算法的实现

```javascript
const performanceS = function() {}
performanceS.prototype.calculate = function(salary) {
  return salary * 4
}
const performanceA = function() {}
performanceA.prototype.calculate = function(salary) {
  return salary * 3
}
const performanceB = function() {}
performanceB.prototype.calculate = function(salary) {
  return salary * 2
}
```

定义接口类，开始使用算法得的想要的数据

```javascript
const Bonus = function() {
  this.salary = null
  this.strategy = null
}

// 原始工资
// 绩效等级对应的策略对象
Bonus.prototype.setSalary = function(salary) {
  this.salary = salary // 设置员工的原始工资
}
Bonus.prototype.setStrategy = function(strategy) {
  this.strategy = strategy // 设置员工绩效等级对应的策略对象
}
Bonus.prototype.getBonus = function() {
  // 取得奖金数额
  return this.strategy.calculate(this.salary)// 把计算奖金的操作委托给对应的策略对象
}
```

如此这般，我们增加一个种绩效方式时，只需要再写一个新的类来实现就好了，而不需要改动任何现存的代码，下面是 `Bonus` 的使用

```javascript
const bonus = new Bonus()

bonus.setSalary(10000)
bonus.setStrategy(new performanceS()) // 设置策略对象
console.log(bonus.getBonus()) // 输出:40000
bonus.setStrategy(new performanceA()) // 设置策略对象
console.log(bonus.getBonus()) // 输出:30000
```

### JavaScript 版本的策略模式

传统面向对象语言中，策略对象需要策略类来生成再在原型上添加策略「方法」，但在 JavaScript 中，函数也是对象。所以各个算法的实现可以直接定义为函数对象。

```javascript
const strategies = {
  S: function(salary) {
    return salary * 4
  },
  A: function(salary) {
    return salary * 3
  },
  B: function(salary) {
    return salary * 2
  }
}
```

那接口类也会因此变得不同，我们可以用更简单的方法去接受用户的请求。

```javascript
const calculateBonus = function(level, salary) {
  return strategies[level](salary)
}
console.log(calculateBonus("S", 20000)) // 输出:80000
console.log(calculateBonus("A", 10000)) // 输出:30000
```

但其实如果把策略模式仅仅用来封装算法，未免有点大材小用，在实际开发中，我们通常会把算法的含义扩散开来，使策略模式也可以用来封装 一系列的「业务规则」。只要这些业务规则指向的目标一致，并且可以被替换使用，我们就可以用策略模式来封装它们。

