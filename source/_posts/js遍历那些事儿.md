---
title: js遍历那些事儿
date: 2024-08-02 14:14:16
tags:
categories: js
---
本文总结js中的各种常用遍历。

## 基本用法

### for
最基本的遍历写法，通过索引来遍历。
```javascript
let arr = [1, 2, 3]
for(let i = 0; i < arr.length; i++){
  console.log(i) // 索引，数组下标 0, 1, 2
  console.log(arr[i]) // 数组下标所对应的元素 1, 2, 3
}
```

### forEach
写法比for简便，遍历所有元素，不返回任何值。
```javascript
let arr = [1, 2, 3]
arr.forEach(i => console.log(i)) // 1, 2, 3
```

### map
映射，常用于基于原数组返回新数组(不改变原数组)。
```javascript
let arr = [1, 2, 3]
const newArr = arr.map(i => i + 1)
console.log(arr) // [1, 2, 3]
console.log(newArr) // [2, 3, 4]
```

### filter
筛选，常用于过滤原数组来产生新数组，不改变原数组。
```javascript
let arr = [1, 2, 3]
const newArr = arr.filter(i => i > 1)
console.log(arr) // [1, 2, 3]
console.log(newArr) // [2, 3]
```

以上4种遍历基本满足大部分需求，下面看一些用的较少的遍历方法。

### reduce
将数组中的元素逐个进行处理，并将它们合并为一个值。功能十分强大,回调函数可以进行各种复杂的操作，包括条件判断、对象构建等。

语法：
```javascript
array.reduce(function(total, currentValue, currentIndex, arr), initialValue)
```

- callback （执行数组中每个值的函数，包含四个参数）
  - total- 必需 （初始值, 或者计算结束后的返回值）
  - currentValue - 必需 （数组中当前被处理的元素）
  - currentIndex - 可选 （当前元素在数组中的索引）
  - arr - 可选 （调用 reduce 的数组）
- initialValue - 可选 （作为第一次调用 callback 的第一个参数,如果不提供，第一次回调会使用数组的第一个元素）

示例（最简单的累加）：
```javascript
let arr = [1, 2, 3]
const sum = arr.reduce(function(prev, cur, index, arr) {
  console.log(prev, cur, index)
  return prev + cur
})
console.log(arr, sum)

// 输出：
// 1 2 1
// 3 3 2
// [1, 2, 3] 6
```

比如我们要统计字符串中每个字母出现的次数：

```javascript
const arrString = 'abcdaabc'

arrString.split('').reduce(function(res, cur) {
    res[cur] = (res[cur] || 0) + 1
    return res
}, {})

// 输出：
// {a: 3, b: 2, c: 2, d: 1}
```

### every
条件都满足返回true。

```javascript
let arr = [1, 2, 3]
const res = arr.every((item, index) => {
  return item > 1
})
console.log(res) // false
```

### some
有一个条件满足返回true。

```javascript
let arr = [1, 2, 3]
const res = arr.some((item, index) => {
  return item > 1
})
console.log(res) // true
```

### for...in
主要用于遍历对象的可枚举属性(还会遍历原型链上的可枚举属性)。

遍历数组(*不推荐，会遍历数组的所有可枚举属性，包括非索引属性和原型链上的属性*)：

```javascript
let arr = [1, 2, 3]
for(let index in arr) {
  console.log(index, arr[index])
}

// 输出：
// 0 1
// 1 2
// 2 3
```

遍历对象：

```javascript
let obj = { a: 1, b: 2, c: 3 }
for(let key in obj) {
  console.log(key, obj[key])
}

// 输出：
// a: 1
// b: 2
// c: 3
```

### for...of
用于遍历可迭代对象（例如 Array, Map, Set, String, TypedArray，NodeList以及其他 DOM 集合，arguments 对象等）的可迭代属性（不可迭代属性会被忽略）。

```javascript
const arr = [1, 2, 3];

for (const value of arr) {
  console.log(value);
}

// 输出：
// 1
// 2
// 3
```

`for...in`和`for...of`的区别：

```javascript
Object.prototype.objCustom = function () {}
Array.prototype.arrCustom = function () {}

let iterable = [3, 5, 7]
iterable.foo = "hello"

for (let i in iterable) {
  console.log(i) // 0, 1, 2, "foo", "arrCustom", "objCustom"
}

for (let i of iterable) {
  console.log(i) // 3, 5, 7
}
```

可以看到，主要有3点不同：
- 1.`for...in`遍历key，`for...of`遍历value
- 2.`for...in`遍历的是可枚举属性，`for...of`遍历的是可迭代属性
- 3.对于array的不可迭代元属性`objCustom`、`arrCustom`和`实例属性foo`，在`for...of`循环中都被忽略


### Object.keys，values，entries
对于普通对象（需要注意和map的区别）：
- Object.keys(obj)：返回一个包含该对象所有的键的数组。
- Object.values(obj)：返回一个包含该对象所有的值的数组。
- Object.entries(obj)：返回一个包含该对象所有 [key, value] 键值对的数组。

```javascript
let user = {
  name: "John",
  age: 30,
}
console.log(Object.keys(user)) // ["name", "age"]
console.log(Object.values(user)) // ["John", 30]
console.log(Object.entries(user)) // [ ["name","John"], ["age",30] ]
```

Object.entries把 obj 变成由键/值对组成的数组，然后使用 Object.fromEntries可以将结果转回成原来的对应

```javascript
let user = {
  name: "John",
  age: 30,
}

console.log(Object.entries(user)) // [ ["name","John"], ["age",30] ]
console.log(Object.fromEntries(Object.entries(user))) // { name: 'John', age: 30 }
```

## 中断循环
中断循环,推荐使用break，continue不退出循环，只跳过当前循环，if条件判断替换continue，可读性更高。
for循环（`for`/`for...in`/`for...of`）可通过break退出循环。

```javascript
let arr = [1, 2, 3]
for (let i = 0; i < arr.length; i++) {
  console.log(i)
  if(i === 1) break
}

// 输出：
// 0
// 1


for (let value of arr) {
    console.log(value)
    if(value === 1) break
}

// 输出：
// 1

let obj = { a: 1, b: 2, c: 3 }
for(let key in obj) {
  console.log(key, obj[key])
  if (key === 'a') break
}

// 输出：
// a 1

try {
  let arr = [1, 2, 3]
  arr.forEach(i => {
    console.log(i)
    if(i === 1) {
      throw new Error('End Iterative')
    }
  })
} catch(e) {
  console.log(e)
}

// 输出：
// 11
// Error: End Iterative
```

> 总结，有`for`关键字时，可以通过break退出循环，forEach可以通过throw Error的方式退出循环(但不推荐这样写，推荐用for)。

## 异步
当遍历碰到`async`、 `await`、 `Promise`时，又该怎么写呢？

```javascript
let arr = [20, 10, 30]
const sleep = (ms) => {
  return new Promise((resolve) => setTimeout(resolve, ms))
}
```
上面有个数组`arr`和耗时操作`sleep`，如果没有耗时操作，就是下面同步的写法：

```javascript
const syncRes = arr.map((i) => {
  console.log('loop', i)
  console.log(i)
  return i
})

console.log('syncRes', syncRes)

// 输出：
// loop 20
// 20
// loop 10
// 10
// loop 30
// 30
// syncRes [20, 10, 30]
```

如果有耗时操作，像下面这样，怎么保持输出还是`[20, 10, 30]`呢？

```javascript
await sleep(i)
return i
```

最简单的方法可以用for循环（`for`/`for...in`/`for...of`都一样）：
```javascript
let asyncRes1 = []
for (let i of arr){
  console.log('loop', i)
  await sleep(i)
  console.log(i)
  asyncRes1.push(i)
}
console.log('asyncRes1', asyncRes1)

// 输出：
// loop 20
// 20
// loop 10
// 10
// loop 30
// 30
// asyncRes1 [20, 10, 30]
```

`map`可以像这样`Promise.all(arr.map(async (...) => ...))`：

```javascript
const asyncRes = await Promise.all(arr.map(async (i) => {
  console.log('loop', i)
  await sleep(i)
  console.log(i)
  return i
}))

console.log('asyncRes', asyncRes)

// 输出：
// loop 20
// loop 10
// loop 30
// 10
// 20
// 30
// asyncRes [20, 10, 30]
```

如果不用`Promise.all`，就是下面的效果：

```javascript
const asyncRes = arr.map(async (i) => {
  console.log('loop', i)
  await sleep(i)
  console.log(i)
  return i
})

console.log('asyncRes', asyncRes)

// 输出：
// loop 20
// loop 10
// loop 30
// asyncRes [Promise, Promise, Promise]
// 10
// 20
// 30
```

碰到reduce时`async (prev, cur) => await prev`：

```javascript
const asyncRes = await arr.reduce(async (prev, cur) => {
  console.log('loop', cur)
  await sleep(cur)
  const res = (await prev) + cur
  console.log(res)
  return res
}, 0)

console.log('asyncRes', asyncRes)

// 输出：
// loop 20
// loop 10
// loop 30
// 20
// 30
// 60
// asyncRes 60
```

在`forEach`、`filter`等其他循环中需要使用异步，先用`map`、`reduce`、`for...of`处理。

## 总结
- 基本用法要熟记特性，灵活选用
- 需要中断就用for（`for`/`for...in`/`for...of`），使用`break`退出
- 碰到异步用for（`for`/`for...in`/`for...of`）/`map`/`reduce`

## 参考文档
- [Asynchronous array functions in Javascript](https://advancedweb.hu/asynchronous-array-functions-in-javascript/)
