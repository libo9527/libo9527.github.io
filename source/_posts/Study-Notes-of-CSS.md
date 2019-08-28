---
title: Study Notes of CSS
description: CSS 笔记
date: 2019-07-31 19:16:15
categories: 
- Front-end
- CSS
tags:
---

<img src="https://miro.medium.com/max/1200/1*OFsc0SD55jhi8cjo7aCA4w.jpeg" width="100%"/>

<!-- more -->

## 垂直居中

### Flexbox 布局中实现元素的水平垂直居中

> [justify-content | MDN](https://developer.mozilla.org/zh-CN/docs/Web/CSS/justify-content)
>
> [align-items | MDN](https://developer.mozilla.org/zh-CN/docs/Web/CSS/align-items)

```css
#container{
    display: flex;
    justify-content: center;
    align-items: center;
}
```

<iframe height="265" style="width: 100%;" scrolling="no" title="bXmeLr" src="//codepen.io/libo9527/embed/bXmeLr/?height=265&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/libo9527/pen/bXmeLr/'>bXmeLr</a> by libo9527
  (<a href='https://codepen.io/libo9527'>@libo9527</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

## position: sticky

> [position | MDN](https://developer.mozilla.org/zh-CN/docs/Web/CSS/position)
>
> [杀了个回马枪，还是说说position:sticky吧« 张鑫旭-鑫空间-鑫生活](https://www.zhangxinxu.com/wordpress/2018/12/css-position-sticky/)

需求说明：实现导航的跟随定位效果

<iframe width="100%" height="300" src="https://www.zhangxinxu.com/study/201812/position-sticky.html" frameborder="0" style="max-width:100%;border:1px solid #ddd;"></iframe>

```css
nav {
    position: -webkit-sticky;
    position: sticky;
    top: 0;
}
```

