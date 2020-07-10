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

```javascript
// 公共代码
function Parent() {
  this.name = 'Tom'
}
Parent.prototype.sayhi = function () {
  console.log(this.name + ' say hi')
}
```

1. 原型链继承

```javascript
function Child() {}

Child.prototype = new Parent()

var child = new Child()
child.sayhi() // Tom say hi

问题：
   1. 引用类型的属性被所有实例共享
   2. 创建child时不能给parent传参
   3. child的constructor指向Parent而不是Child
```

2. 利用构造函数

   避免了 parent 属性被所有实例共享的问题， 且可以给 Parent 传参

```javascript
function Child() {
  Parent.call(this)
}
```

缺点：

- child 实例不能继承 Parent 原型链中的属性，方法

3. 组合

方法一：

```javascript
function Child() {
  Parent.call(this)
}
Child.prototype = Parent.prototype
Child.prototype.constructor = Child
```

方法二：

```javascript
function Parent(name) {
  this.name = name
}
Parent.prototype.sayhi = function () {
  console.log(this.name + ' say hi')
}
function object(o) {
  function f() {}
  f.prototype = o
  return new f()
}

function prototype(child, parent) {
  var prototype = object(parent.prototype)
  prototype.constructor = child
  child.prototype = prototype
}

function Child(age, name) {
  this.age = age
  Parent.call(this, name)
}
prototype(Child, Parent)
var child = new Child(1, 'Sam')
```

# new this

全局默认 this 指向 window， 函数中则看调用的位置， 全局调用指向 widnow(严格模式下为 undefined)

1. 模拟 new

   ```javascript
   function create() {
     var obj = new Object()
     console.log(arguments)
     var args = [].shift.call(arguments)
     obj.__proto__ = args.prototype
     console.log(args)
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

2. 模拟 bind

   ```javascript
   ```

3. 模拟 apply 和 call
   ```javascript
   ```

## 箭头函数

如果箭头函数被非箭头函数调用，则 this 继承至调用它的非箭头函数， 如果是全局调用则为 window 对象

需要注意的点：

1. 没有自己的 this、arguments
2. 没有原型
3. 不能改变 this 的指向
4. 不能使用 new 来调用

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

# 判断类型

```javascript
var a = [1, 2, 2]
Object.prototype.toString.call(a) // [object Array]

var b = { a: 1 }
Object.prototype.toString.call(b) // [object Object]

var c = 1
Object.prototype.toString.call(c) // [object Number]
```
