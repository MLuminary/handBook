---
description: 指一个类只有一个实例，且该类能自行创建这个实例的一种模式，为了节省内存资源、保证数据内容的一致性。
---

# 单例模式

### 存在的意义

在某些系统中，只有一个实例非常必要。比如任务管理器，首先从需求上来讲，一个任务管理器窗口完全满足需要。多个任务管理器窗口只会造成**额外的开销**，还要处理各个窗口数据的同步。

### 实现

用一个变量来标记当前的类是否创建过对象，如果没创建过，返回创建之后的实例，如果之前已经创建过，**则返回之前创建过的实例**。

### 使用构造函数自身的方法

```javascript
// 构造函数
function Singleton(name) {
    this.name = name
    this.instance = null
}
// 构造函数方法
Singleton.prototype.getName = () => {
    console.info(this.name)
}
// 单例控制
Singleton.getInstance = (name) => {
    if(!this.instance) {
        this.instance = new Singleton(name)
    }
    return this.instance
}

const a = Singleton.getInstance('a')
const b = Singleton.getInstance('b')

console.info(a, b) // true
```

也可以使用闭包

```javascript
// 构造函数
function Singleton(name) {
    this.name = name
}
// 构造函数方法
Singleton.prototype.getName = function() {
    console.info(this.name)
}
// 获取类的实例
Singleton.getInstance = (function() {
    let instance = null
    return function(name) {
        if(!instance) {
            instance = new Singleton(name)
        }
        return instance
    }        
})()

// 获取对象1
var a = Singleton.getInstance('a')
// 获取对象2
var b = Singleton.getInstance('b')
// 进行比较
console.info(a, b)
```

当然也可以使用 ES6 的 `class`

```javascript
class Singleton {
    constructor(name) {
        this.name = name
    }

    getName() {
        console.info(this.name)
    }

    getInstance = (function(){
        let instance = null
        return function(name) {
            if(!instance) {
                instance = new Singleton(name)
            }
            return instance
        }   
    })()
}

// 获取对象1
var a = Singleton.getInstance('a')
// 获取对象2
var b = Singleton.getInstance('b')
// 进行比较
console.info(a, b)
```

但是此方法必须要使用 `Singleton.getInstance()` 才能获取到想要的实例，这与我们习以为常的 `new`  关键字获取实例对象有很大区别。其实这种方法的实现思路就是用构造函数中的一个方法去控制此构造函数生成的实例为单例，既然我们想要用 `new`关键字来生成单例，那我们为何不用另一个构造函数来控制此构造函数的创建呢

### 使用另一个构造函数

```javascript
class Person {
    constructor(name) {
        this.name = name
    }
    getName() {
        return this.name
    }
}

const Singleton = (function() {
    let instance
    return function(name) {
        if(!instance) {
            instance = new Person(name)
        }
        return instance
    }
})()


// 获取对象1
let a = new Singleton('a')
let b = new Singleton('b')
console.info(a === b) // true

```

以上中间控制方法或类中的构造函数都是固定的，也就是说我们需要为不同的构造函数编写不同的中间方法或类，这样显然是不合理的。

### 通用的单例中间类「实际应用」

我发现现在流传在网上的基本上是如下这种

```javascript
// 获取单独的实例
var singleton = function(fn) {
    var instance;
    return function() {
        return instance || (instance = fn.apply(this, arguments));
    }
}

// 创建遮罩层
var createMask = function(){
    // 创建div元素
    var mask = document.createElement('div');
    // 设置样式
    mask.style.position = 'fixed';
    mask.style.top = '0';
    mask.style.right = '0';
    mask.style.bottom = '0';
    mask.style.left = '0';
    mask.style.opacity = 'o.75';
    mask.style.backgroundColor = '#000';
    mask.style.display = 'none';
    mask.style.zIndex = '98';
    document.body.appendChild(mask);
    // 单击隐藏遮罩层
    mask.onclick = function(){
        this.style.display = 'none';
    }
    return mask;
};

// 调用
var oMask = singleton(createMask)()
var eMask = singleton(createMask)()
// 但是 oMask 与 eMask 不相等，获取 eMask 时其实也创建了新的 div
```

这种调用方法其实仅仅是获取到了 `createMask` 中的返回值，每次 `singleton()` 其实都会执行 `var instance` ,所以对 `instance` 的判断其实是没用的。正确调用我觉得应该如下

```javascript
const maskSingleton = singleton(createMask) // 先用一个变量接一下
const oMask = maskSingleton()
const eMask = maskSingleton() // 此时返回的还是之前已经创建的 mask
```

但其实上面的例子并未很好的体现出通用惰性单例模式的作用，而且`oMask` 其实是获取不到 `createMask` 原型链上的方法的「也可能原本作者就不想获取 = =」，但我们之前的举例都是使用构造函数的举例，所以接下来我也会用构造函数的例子来展现。

```javascript
const singleton = function(Fn) {
    var instance
    return function() {
        return instance || (instance = new Fn(...arguments))
    }
}

const Person = function(name){
    this.name = name
}

Person.prototype.getName = function() {
    return this.name
}

const Animal = function(name) {
    this.name = name
}

Animal.prototype.getName = function() {
    return this.name
}

const PersonSingleton = singleton(Person)
const liSi = PersonSingleton('liSi')
const zhangSan = PersonSingleton('zhangSan')

const AnimalSingleton = singleton(Animal)
const cat = AnimalSingleton('cat')
const dog = AnimalSingleton('dog')

console.info(zhangSan.getName()) // 'liSi'
console.info(dog.getName()) // 'cat'
```

参考链接: [https://segmentfault.com/a/1190000012842251](https://segmentfault.com/a/1190000012842251)

