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

需求说明：公司项目中要求增加一个排序按钮，对地址树进行排序。排序则需要对树进行遍历，递归的排序子节点。

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

## Map

### delete()

> [Map.prototype.delete()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Map/delete)
>
> `delete()` 方法用于移除 `Map` 对象中指定 key 的键值对。

