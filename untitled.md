---
description: 指一个类只有一个实例，且该类能自行创建这个实例的一种模式，为了节省内存资源、保证数据内容的一致性。
---

# 单例模式

### 模式动机

在某些系统中，只有一个实例是非常必要的。比如任务管理器，首先从需求上来讲，一个任务管理器窗口完全满足需要。多个任务管理器窗口只会造成**额外的开销**，还需要处理各个窗口数据同步的问题。

### 实现

用一个变量来标记当前的类是否创建过对象，如果没创建过，返回创建之后的实例，如果之前已经创建过，**则返回之前创建过的实例**。

![&#x5355;&#x4F8B;&#x6A21;&#x5F0F;&#x300C;&#x4EFB;&#x52A1;&#x7BA1;&#x7406;&#x5668;&#x300D;](.gitbook/assets/image.png)

### 代码

```javascript
function TaskManage(name) {
  this.name
}

TaskManage.getInstance = (function() {
  let instance = null
  return function(name) {
    if (!instance) {
      instance = new TaskManage(name)
    }
    return instance
  }
})()

// 获取对象1

const a = TaskManage.getInstance('a')
// 获取对象2
const b = TaskManage.getInstance('b')
// 进行比较
console.info(a === b) // true
```

可以看到 `a` 和 `b` 是相等的，但是假如我们想让打开的英雄联盟也是单例，我们就必须再写一个英雄联盟的类，并添加 `getInstance` 方法，这样显然 `getInstance` 是重复编写的，我们可以将其抽出来单独作为一个类。

### 通用的单例中间类

首先我们将控制生成单例类的 `getInstance` 方法抽离出来

```javascript
// 获取单独的实例
const singleton = function(fn) {
    const instance;
    return function() {
        return instance || (instance = fn.apply(this, arguments));
    }
}

// 个人认为这样也可以
const singleton = function(Fn) {
    const instance
    return function() {
        return instance || (instance = new Fn(...arguments))
    }
}

```

我们再编写一个任务管理器的类

```javascript
const taskManage = function(name){
    this.name = name
}

taskManage.prototype.getName = function() {
    return this.name
}
```

然后我们将 `taskManage` 变为单例

```javascript
const TaskManageSingleton = singleton(taskManage)
const win = TaskManageSingleton('win')
const mac = TaskManageSingleton('mac')

console.info(win.getName()) // 'win'
console.info(mac.getName()) // 'win'
```

可以看到第二次调用 `TaskManageSingleton` 时传入的参数并未生效，可以得知返回的是之前已经生成的 `TaskManage` 实例。

