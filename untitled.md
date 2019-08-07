---
description: 指一个类只有一个实例，且该类能自行创建这个实例的一种模式，为了节省内存资源、保证数据内容的一致性。
---

# 单例模式

### 模式动机

在某些系统中，只有一个实例是非常必要的。比如任务管理器，首先从需求上来讲，一个任务管理器窗口完全满足需要。多个任务管理器窗口只会造成**额外的开销**，还需要处理各个窗口数据同步的问题。

### 实现

用一个变量来标记当前的类是否创建过对象，如果没创建过，返回创建之后的实例，如果之前已经创建过，**则返回之前创建过的实例**。

![&#x5355;&#x4F8B;&#x6A21;&#x5F0F;&#x300C;&#x4EFB;&#x52A1;&#x7BA1;&#x7406;&#x5668;&#x300D;](.gitbook/assets/image.png)

### 通用的单例中间类

我们编写一个生成单例类的通用类

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

可以看到第二次调用 `TaskManageSingleton` 时传入的参数并未生效，可以得知返回的是之前已经生成的 `TaskManage` 实例

