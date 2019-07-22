---
description: >-
  命令模式中的命令指的是一个执行某些特定事情的指令，命令模式常见的场景是:
  有时候需要向某些对象发送请求，但是并不知道请求的接受者是谁，也不知道请求的操作是什么。此时希望一种松耦合的方式来设计程序，使得请求发送者和请求接受者能消除彼此之间的耦合关系。
---

# 命令模式

### 特点

命令模式的本质是对命令进行封装，将发出命令的责任和执行命令的责任分割开

每个命令都是一个操作：请求的一方发出请求，要求执行一个操作；接受的一方收到请求，并执行操作，命令模式使请求本身成为一个「对象」，这个对象和其他对象一样可以被存储和传递

### 电视机遥控器

电视是请求的接受者，遥控器是请求的发送者，遥控器上有一些按钮，不同的按钮对应电视机的不同操作。在这个例子中，每个按钮实际上就是每个命令。

```javascript
const TV = {
  // 
}
// 将命令抽象出来
const Command = {
  turnOff: () => {
    console.info('关闭电视')
  },
  turnOn: () => {
    console.info('打开电视')
  },
  volumeIncrease: () => {
    console.info('音量增加')
  }
}
​
const bindCommand = (obj, command) => {
  command.call(obj)
}
​
const CemoteControl = {
  key1: bindCommand(Command.turnOff),
  key2: bindCommand(Command.trunOn),
  key3: bindCommand(Command.volumeIncrease)
}
```

## 小结

JavaScript 作为将函数作为一等对象的语言，命令模式也早已融入到了 JavaScript 语言中。因此实现起来自己感觉略显鸡肋，并没有感觉很实用….

