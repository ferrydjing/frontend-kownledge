# Event Loop

javascript 是单线程语言，所有的任务都是通过事件循环完成的。大体的运行顺序为

    主线程开始运行->遇到待处理的宏任务放入宏任务队列->遇到待处理的微任务放入微任务队列->主线程结束->有没有待执行的微任务->浏览器渲染->下一个待执行的宏任务

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
   // 实现一
   Function.prototype.bindFn = function bind(thisArg) {
     if (typeof this !== 'function') {
       throw new TypeError(this + ' must be  a function')
     }
     var self = this
     var args = [].slice.call(arguments, 1)

     var bound = function () {
       var boundArgs = [].slice.call(arguments)
       return self.apply(thisArg, args.concat(boundArgs))
     }

     return bound
   }
   // 最终版本
   Function.prototype.bindFn = function bind(thisArg) {
     if (typeof this !== 'function') {
       throw new TypeError(this + ' must be  a function')
     }
     var self = this
     var args = [].slice.call(arguments, 1)

     var bound = function () {
       var boundArgs = [].slice.call(arguments)
       var finalArgs = args.concat(boundArgs)
       if (!new.target) {
         return self.apply(thisArg, finalArgs)
       } else {
         if (self.prototype) {
           function Empty() {}
           Empty.prototype = self.prototype
           bound.prototype = new Empty()
         }

         var result = self.apply(thisArg, finalArgs)
         var isObject = typeof result === 'object' && result !== null
         var isFn = typeof resuld === 'function'
         if (isObject || isFn) {
           return result
         }
         return this
       }
     }

     return bound
   }
   ```

3. 模拟 apply 和 call

   apply 和 call 除了接受参数不同，其功能是一致的

   ```javascript
   // apply实现
   function getGlobalObject() {
     return this
   }
   Function.prototype.applyFn = function apply(thisArg, argsArray) {
     if (typeof this !== 'function') {
       throw new TypeError(this + ' is not a function')
     }

     if (typeof argsArray === 'undefined' || argsArray === null) {
       argsArray = []
     }

     if (argsArray !== new Object(argsArray)) {
       throw new TypeError('CreateListFromArrayLike called on non-object')
     }

     if (typeof thisArg === 'undefined' || thisArg === null) {
       thisArg = getGlobalObject()
     }

     thisArg = new Object(thisArg)
     var __fn = '__fn'
     thisArg[__fn] = this
     var result = thisArg[__fn](...argsArray)
     delete thisArg[__fn]
     return result
   }
   ```

## 箭头函数

如果箭头函数被非箭头函数调用，则 this 继承至调用它的非箭头函数， 如果是全局调用则为 window 对象

需要注意的点：

1. 没有自己的 this、arguments
2. 没有原型
3. 不能改变 this 的指向
4. 不能使用 new 来调用

# promise

1.  解决回调问题
2.  并发请求
3.  解决异步问题

    ```javascript
    class Promise {
      constructor(executor) {
        // 初始化 state 为等待态
        this.state = 'pending'
        // 成功的值
        this.value = undefined
        // 失败的原因
        this.reason = undefined
        let resolve = (value) => {
          // state 改变,resolve 调用就会失败
          if (this.state === 'pending') {
            // resolve 调用后，state 转化为成功态
            this.state = 'fulfilled'
            // 储存成功的值
            this.value = value
          }
        }
        let reject = (reason) => {
          // state 改变,reject 调用就会失败
          if (this.state === 'pending') {
            // reject 调用后，state 转化为失败态
            this.state = 'rejected'
            // 储存失败的原因
            this.reason = reason
          }
        }
        // 如果 executor 执行报错，直接执行 reject
        try {
          executor(resolve, reject)
        } catch (err) {
          reject(err)
        }
      }
    }
    ```

# 浏览器缓存机制

1. 强缓存
2. 协商缓存

# 浏览器渲染原理

重排和重绘

- 增加、删除或者修改`DOM`节点
- 移动`DOM`,开始动画
- 修改`css`，改变大小、位置时，或者`display：none`时候触发重排，修改颜色等为重绘
- 修改网页的默认字体时。
- Resize 窗口的时候（移动端没有这个问题），或是滚动的时候。
- 内容的改变，(用户在输入框中写入内容也会)。
- 激活伪类，如:hover。
- 计算 offsetWidth 和 offsetHeight。

# 函数式编程

# HTML5 Web Worker

用途

1. 懒加载
2. 数据处理，分析
3. canvas 图形绘制

注意的地方

1. 无法访问`DOM`
2. 同源限制
3. 没有`window`

```javascript
var worker = new Worker('worker.js')
```

# Service Worker

# 事件（捕获和冒泡）

window.onerror

window.addeventlistener('error', (error) => {
console.log(error)
}, true)

捕获事件流是由父->子的过程
冒泡则是由子->父的过程

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

2. 节流

```javascript
// 思路：在规定时间内只触发一次
function throttle(fn, delay) {
  // 利用闭包保存时间
  let prev = Date.now()
  return function () {
    let context = this
    let arg = arguments
    let now = Date.now()
    if (now - prev >= delay) {
      fn.apply(context, arg)
      prev = Date.now()
    }
  }
}

function fn() {
  console.log('节流')
}
addEventListener('scroll', throttle(fn, 1000))
```

3. 防抖

```javascript
// 思路:在规定时间内未触发第二次，则执行
function debounce(fn, delay) {
  // 利用闭包保存定时器
  let timer = null
  return function () {
    let context = this
    let arg = arguments
    // 在规定时间内再次触发会先清除定时器后再重设定时器
    clearTimeout(timer)
    timer = setTimeout(function () {
      fn.apply(context, arg)
    }, delay)
  }
}

function fn() {
  console.log('防抖')
}
addEventListener('scroll', debounce(fn, 1000))
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

# vue 相关

1. 双向绑定

   2.0 使用 defineproperty

   ```javascript
   var obj = {
     txt: ''
   }
   Object.defineProperty(obj, 'txt', {
     get: function () {
       console.log('get val')
     },
     set: function (value) {
       console.log('set value', value)
       document.querySelector('#res').innerHTML = value
     }
   })
   ```

   3.0 使用 proxy

   ```javascript
   var obj2 = {
     name: ''
   }
   var p = new Proxy(obj2, {
     get() {
       console.log('proxy get')
     },
     set(obj, prop, val) {
       console.log('proxy set', obj, prop, val)
       document.querySelector('#res').innerHTML = val
     }
   })
   ```

# Object

JavaScript 提供了一个内部数据结构，用来描述对象的属性，控制它的行为，比如该属性是否可写、可遍历等等。这个内部数据结构称为“属性描述对象”（attributes object）。每个属性都有自己对应的属性描述对象，保存该属性的一些元信息。

1. value, 属性值，默认为 undefined
2. writable,表示属性值（value）是否可改变（即是否可写）
3. enumerable, 表示属性是否可枚举（for in）
4. configable, 表示该属性是否可以被删除
5. get, 该属性取值函数，默认为 undefined
6. set, 该属性存值函数，默认为 undefined

## Object.seal

使得一个对象既无法添加新属性，也无法删除旧属性

## Object.freeze

使得一个对象无法添加新属性、无法删除旧属性、也无法改变属性的值，使得这个对象实际上变成了常量

## Object.preventExtensions

使得一个对象无法再添加新的属性
