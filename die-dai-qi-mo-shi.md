---
description: >-
  迭代器模式是指提供一种方法顺序访问一个聚合对象中的各个元素，而又不需要暴露该对象的内部表示。迭代器模式可以把迭代的过程从业务逻辑中分离出来，在使用迭代器模式之后，即使不关心对象的内部构造，也可以按顺序访问其中的每个元素
---

# 迭代器模式

### 内部迭代器

```javascript
const each = function(arg, callback) {
  for (let i = 0; i < arg.length; i++) {
    callback(i, arg[i])
  }
}

each([1, 2, 3], (index, value) => {
  console.info(index, value) 
})

/*打印结果
  0 1
  1 2
  2 3*/
```

### 外部迭代器

```javascript
const Iterator = function(obj) {
  let currentIndex = 0

  const next = function() {
    currentIndex++
  }

  const done = function() {
    return currentIndex >= obj.length
  }

  const getCurrentItem = function() {
    return obj[currentIndex]
  }

  return {
    next,
    done,
    getCurrentItem
  }
}
```

内部迭代器已经在内部定义好了迭代规则，只需要一次调用即可。外部迭代器的虽然增加一点调用的复杂度，但是我们可以手工控制迭代的过程和顺序。

## 小结

迭代器模式是一种相对简单的模式，简单到很多时候我们都不认为它是一种设计模式。目前的绝大部分语言都内置了迭代器





