---
title: Study Notes of JavaScript
description: JavaScript 笔记
date: 2019-07-31 16:54:45
categories: 
 - Front-end
 - JavaScript
tags: JavaScript
---

<img src="https://www.tutorialrepublic.com/lib/images/javascript-illustration.png" width="100%"/>

<!-- more -->

## ForEach

JS 中 forEach 遍历数组的时候是无法通过 `break` 或者 `return false` 来中断的。

## 取整

### 向下取整

> 向下取整就是在坐标轴上从左到右取离原数最近的整数
>
> | x | ⌊x⌋ |
> |:-:|:-:|
	| -1.9 | -2 |
	| 1.9 | 1 |

#### number | 0

JS 没有整数的概念，所有的数值型都是双精度浮点数。而[浮点数无法直接进行位运算](https://www.zhihu.com/question/319351649)，在用位运算符时，JS 会先把操作数转变成整数再进行位运算，而 0 与其他值异或或者按位或都不会改变操作数，进而实现取整。

但是对于正数来说会进行向下取整，而对于负数则会进行向上取整。

```
·> -1.9 | 0
<· -1
-------------
·> -1.1 | 0
<· -1
-------------
·> 1.1 | 0
<· 1
-------------
·> 1.9 | 0
<· 1
```

## 树的遍历

> [JS 中遍历树| Deng's Blog](http://objcer.com/2017/02/26/traverse-the-tree/)
>
> [二叉树遍历（前序、中序、后序、层次遍历、深度优先、广度优先）](https://blog.csdn.net/My_Jobs/article/details/43451187)

### 递归遍历

需求说明：公司项目中要求增加一个排序按钮，对地址树进行排序。排序则需要对树进行遍历，递归的排序子结点。

<iframe height="265" style="width: 100%;" scrolling="no" title="Tree Sort" src="//codepen.io/libo9527/embed/gVjedo/?height=265&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/libo9527/pen/gVjedo/'>Tree Sort</a> by libo9527
  (<a href='https://codepen.io/libo9527'>@libo9527</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

递归代码：

```js
recursive (root) {
  if (!root || !root.childNodes) {
    return
  }
  ...
  root.childNodes.forEach((node) => {
    this.recursive(node)
  })
}
```

## Array

### unshift()

> [Array.prototype.unshift() | MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/unshift)
>
> **unshift()** 方法将一个或多个元素添加到数组的**开头**，并返回该数组的**新长度**(该方法修改原有数组)

```javascript
var array1 = [1, 2, 3];

console.log(array1.unshift(4, 5));
// expected output: 5

console.log(array1);
// expected output: Array [4, 5, 1, 2, 3]
```

### map()

> [Array.prototype.map()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/map)
>
> `map()` 方法创建一个新数组，其结果是该数组中的每个元素都调用一个提供的函数后返回的结果。

### splice()

> [Array.prototype.splice() - MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/splice)
>
> **splice()** 方法通过删除或替换现有元素或者原地添加新的元素来修改数组,并以数组形式返回被修改的内容。此方法会改变原数组。

#### 获取数组的最后一个元素

> [JavaScript 获取数组的最后一个元素方法（多种）](https://www.jianshu.com/p/a8d6a1fe4b60)

浅复制的slice方法

```javascript
var args = [1, 2, 3];
var lastElement = args.slice(-1); // [3]
```

### 判空

> [Check if array is empty or does not exist. JS](https://stackoverflow.com/questions/24403732/check-if-array-is-empty-or-does-not-exist-js)

Js Array 并没有 isEmpty() 这种直接判空的方法，需要使用如下方式进行判空

```javascript
if (!array || array.length === 0) {
    // array empty or does not exist
}
```

### 遍历删除子结点

#### 描述

具有层级结构的结点数组，如何在遍历的过程中删除所有的子结点

#### 方法

从后往前遍历数组，然后在遍历过程中删除子结点。需要注意的是**数组中结点的顺序必须保证子结点一定在父结点之后**。

```javascript
let tree = [{
    id: 1,
    name: 'Aisa',
    parentId: 0
}, {
    id: 11,
    name: 'China',
    parentId: 1
}, {
    id: 2,
    name: 'Europe',
    parentId: 0
}, {
    id: 21,
    name: 'France',
    parentId: 1
}]

console.log('########### Before ###########')
console.log('tree:', tree)
tree.forEach((item) => {
    console.log(`name : ${item.name}`)
})

let ids = tree.map(item => item.id)
for (let i = tree.length - 1; i >= 0; i--) {
    let node = tree[i]
    if (ids.includes(node.parentId)) {
        tree.splice(i, 1)
    }
}


console.log('########### After ###########')
console.log('tree:', tree)
tree.forEach((item) => {
    console.log(`name : ${item.name}`)
})
```

```
########### Before ########### 
tree: [{"id":1,"name":"Aisa","parentId":0},{"id":11,"name":"China","parentId":1},{"id":2,"name":"Europe","parentId":0},{"id":21,"name":"France","parentId":1}] 
name : Aisa 
name : China 
name : Europe 
name : France 
########### After ########### 
tree: [{"id":1,"name":"Aisa","parentId":0},{"id":2,"name":"Europe","parentId":0}] 
name : Aisa 
name : Europe 
```

### indexOf()

> [`Array.prototype.indexOf()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/indexOf)
>
> **indexOf()**方法返回在数组中可以找到一个给定元素的第一个索引，如果不存在，则返回-1。

### includes()

> [`Array.prototype.includes()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/includes)
>
> **includes()** 方法用来判断一个数组是否包含一个指定的值，根据情况，如果包含则返回 true，否则返回false。

### from()

> [Array.from() | MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/from)
>
> `Array.from()` 方法从一个类似数组或可迭代对象中创建一个新的，浅拷贝的数组实例。

#### 将 Set 转化为 Array

> [How to convert Set to Array?](https://stackoverflow.com/questions/20069828/how-to-convert-set-to-array)

```javascript
let array = Array.from(mySet);
```

## Map

### delete()

> [Map.prototype.delete()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Map/delete)
>
> `delete()` 方法用于移除 `Map` 对象中指定 key 的键值对。

## String

### localeCompare()

> [String.prototype.localeCompare() | MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String/localeCompare)
>
> **localeCompare()** 方法返回一个数字来指示一个参考字符串是否在排序顺序前面或之后或与给定字符串相同。

项目中在对树结点按字母排序时使用：

```js
root.childNodes.sort((a, b) => {
  if (sortDirection === 'asc') {
    return a.data.englishName.localeCompare(b.data.englishName)
  } else if (sortDirection === 'desc') {
    return b.data.englishName.localeCompare(a.data.englishName)
  }
})
```

## Console

> [console.log()](https://developer.mozilla.org/zh-CN/docs/Web/API/Console/log)
>
> [Chrome 控制台不完全指南](https://www.cnblogs.com/Wayou/p/chrome-console-tips-and-tricks.html)

```javascript
console.log(`temp的值为: ${temp}`)
```

## Element

### element.className.replace()

> [How TO - Remove a Class](https://www.w3schools.com/howto/howto_js_remove_class.asp)

```javascript
var element = document.getElementById("myDIV");
element.classList.remove("mystyle");
```

