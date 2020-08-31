# 手写代码相关

## 判断一个数字为素数（质数）

```javascript
function isPrime(num) {
  // 判断是否为数字
  if (typeof num !== 'number' && !Number.isInteger(num)) {
    return false
  }

  if (num === 2) {
    // 2为质数
    return true
  } else if (num % 2 === 0) {
    // 排除偶数
    return false
  }

  let sqrtRoot = Math.sqrt(num)
  for (let i = 3; i <= sqrtRoot; i += 2) {
    if (num % i === 0) {
      return false
    }
  }
  return true
}
```

## 斐波那契

```js
function fibonacci(n) {
  if (n <= 0) {
    return 0
  }
  if ([1, 2].indexOf(n) !== -1) {
    return 1
  }
  if (n > 2) {
    return fibonacci(n - 1) + fibonacci(n - 2)
  }
}
```

## 最大公约数

```js
function getMaximumCommonDivisor(a, b) {
  let res = 1,
    divisor = 2
  if (a < 2 || b < 2) {
    return 1
  }
  while (a >= divisor && b >= divisor) {
    if (a % divisor === 0 && b % divisor === 0) {
      res = divisor
    }
    divisor++
  }
  return res //循环获取最大值
}
console.log(getMaximumCommonDivisor(24, 60))
```

## 因数分解

```js
function primes(n) {
  let arr = []
  for (let i = 2; i <= n; i++) {
    while (n !== i) {
      if (n % i !== 0) {
        break
      }
      arr.push(i)
      n /= i
    }
  }
  arr.push(n)
  return arr
}
```

## 判断回文

```js
const validatePalindrome = (s) => {
  let flag = false
  for (let [i, j] = [0, s.length - 1]; i < j; i++, j--) {
    if (s[i] !== s[j]) {
      if (flag) {
        return false
      }
      if (s[i] === s[j - 1]) {
        j -= 1
        flag = true
      } else if (s[i + 1] === s[j]) {
        i += 1
        flag = true
      } else {
        return false
      }
    }
  }
  return true
}
```

## 判断数组中是否有两数之和

```js
const validateSum = (arr, sum) => {
  for (let [i, j] = [0, arr.length - 1]; i < j; i++, j--) {
    if (arr[i] + arr[j] === sum) {
      return true
    }
  }
  return false
}
```

## 贪心算法

```js
const canCompleteCircuit = (gas, cost) => {
  if (gas.length !== cost.length) return -1
  if (!gas.length || !cost.length) return -1

  let len = gas.length - 1
  let validArr = []
  // 选出花费小于等于储备的下标
  gas.forEach((item, idx) => {
    if (item >= cost[idx]) {
      validArr.push(idx)
    }
  })

  let store = 0,
    pay = 0,
    len2 = validArr.length

  for (let i = 0; i < len2; i++) {
    let x = validArr[i]
    store = gas[x]
    pay = cost[x]
    y = x === len ? 0 : x + 1
    while (store >= pay && y !== x) {
      store += gas[y]
      pay += cost[y]
      y = y === len ? 0 : y + 1
    }

    if (store >= pay && y === x) {
      return y
    }
  }
  return -1
}
```

## 链表

```js
class Node {
  constructor(key, value) {
    this.key = key
    this.value = value
    this.next = null
  }
}

class LinkList {
  head = null

  constructor() {
    this.head = new Node('head')
  }

  insert(key, value, nodeKey) {
    let newNode = new Node(key, value)
    let node = this.find(nodeKey)
    if (node) {
      newNode.next = node.next
      node.next = newNode
    } else {
      let cur = this.head
      while (cur.next) {
        cur = cur.next
      }
      cur.next = newNode
    }
  }

  remove(key) {
    let node = this.find(key)
    if (node) {
      let preNode = this.findPrev(key)
      preNode.next = node.next
    }
  }

  find(key) {
    let cur = this.head.next
    while (cur) {
      if (cur.key === key) {
        return cur
      }
      cur = cur.next
    }
    return null
  }

  findPrev(key) {
    let cur = this.head
    while (cur.next && cur.next.key !== key) {
      cur = cur.next
    }
    return cur
  }

  dispay() {
    let cur = this.head.next
    while (cur) {
      console.log(cur.key, cur.value)
      cur = cur.next
    }
  }
}
```

## 双向链表

```js
class Node {}
```
