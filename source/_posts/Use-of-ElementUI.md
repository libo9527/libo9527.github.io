---
title: Use-of-ElementUI
description: ElementUI 的使用
top: false
date: 2019-07-31 10:58:29
categories: Front-end
tags: ElementUI
---

<img src="https://i.loli.net/2019/07/31/5d41178cb31bc93582.png" width="100%"/>

<!-- more -->

> [ElementUI](https://element.eleme.cn)

## Scrollbar 滚动条

> [Demo code](https://codepen.io/libo9527/pen/jgmYmb)

![项目中的使用](https://i.loli.net/2019/07/31/5d4135303465a78467.png)

注：如果不需要横向滚动条，将 wrap 层的 `overflow-x` 置为 `hidden` 即可。

## ERROR

### 懒加载情况下无法添加一级结点

#### 描述

懒加载情况下无法通过 append/insertBefore 等方法添加一级结点

#### 报错

```
console_runner-1df7d3399bdc1f40995a35209755dcfd8c7547da127f6469fd81e5fba982f6af.js:1 [Vue warn]: Error in v-on handler: "TypeError: Cannot read property 'indexOf' of undefined"

found in

---> <ElButton> at packages/button/src/button.vue
       <ElCard> at packages/card/src/main.vue
         <Root>
```

#### CODE

<iframe height="300" style="width: 100%;" scrolling="no" title="ElemnetUI Tree 添加顶级结点" src="//codepen.io/libo9527/embed/qBWyyYx/?height=265&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/libo9527/pen/qBWyyYx/'>ElemnetUI Tree 添加顶级结点</a> by libo9527
  (<a href='https://codepen.io/libo9527'>@libo9527</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>