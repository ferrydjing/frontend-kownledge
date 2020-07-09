# Event Loop

## 宏任务

当前调用栈中执行的任务称为宏任务。包含 script 全部代码、setTimeout、setInterval、setImmediate、I/O、UI Rendering。

## 微任务

当前（此次事件循环中）宏任务执行完，在下一个宏任务开始之前需要执行的任务为微任务。包含 Process.nextTick（Node 独有）、Promise、Object.observe(废弃)、MutationObserver

# 深拷贝

```javascript
const originArray = [1, 2, 3, 4, 5]
const originObj = { a: 'a', b: 'b', c: [1, 2, 3], d: { dd: 'dd' } }
```

1. 利用 JSON 使用

```javascript
const cloneArr = JSON.parse(JSON.stringfy(orgiginArray))
const cloneObj = JSON.parse(JSON.stringfy(originObj))
```

注：
不能复制方法

2. 利用递归方法处理

```javascript
function deepCopy(source) {
  const targetObj = source.constructor === Array ? [] : {}
  for (let key in source) {
    if (source.hasOwnProperty(key)) {
      if (source[key] && typeof source[key] === 'object') {
        targetObj[key] = source[key].constructor === Array ? [] : {}
        targetObj[key] = deepCopy(source[key])
      } else {
        targetObj[key] = source[key]
      }
    }
  }
  return targetObj
}
```

# 原型、原型链

1. constructor，是函数本身的引用

```javascript
function Person(name) {
  this.name = name
}

var p = new Person('Tom')
p.constructor === Person // true
p.constructor === Object // false
```

2. new 的实现

```javascript
function create() {
  var obj = new Object()
  var args = [].shift.call(arguments)
  obj.__proto__ = args.prototype

  var ret = args.apply(obj, arguments)
  return ret instanceof Object ? ret : obj
}

function Car(color) {
  this.color = color
}

var p = create(Car, 'red')
console.log(p)
console.log(p.color)
```

3. 原型链

   每个对象都有原型对象，通过`__proto__`指向上一个原型，原型对象也能拥有原型，一层一层最终指向 null,这个就是原型链

4. 无限累加

```javascript
function add(a) {
  function sum(b) {
    a = a + b
    return sum
  }

  sum.toString = function () {
    return a
  }

  return sum
}
```

# 继承

# new this

# call apply bind

# promise

# 浏览器缓存机制

# 浏览器渲染原理

# 函数式编程

# HTML5 Web Worker

# Service Worker

# 数据处理

1. 数据去重

```javascript
const originArr = []
// 生成[0, 100000]之间的随机数
for (let i = 0; i < 100000; i++) {
  originArr.push(0 + Math.floor((100000 - 0 + 1) * Math.random()))
}

// 方法一
Array.prototype.unique = function () {
  let newArr = []
  for (let i = 0; i < this.length; i++) {
    if (newArr.indexOf(this[i]) === -1) {
      newArr.push(this[i])
    }
  }
  return newArr
}

// 方法二
Array.prototype.unique = function () {
  return Array.from(new Set(this))
  return [...new Set(this)]
}
```

# es6
