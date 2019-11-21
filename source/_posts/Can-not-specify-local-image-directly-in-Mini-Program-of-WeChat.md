---
title: Can not specify local image directly in Mini-Program of WeChat
description: 微信小程序中，background-image 无法直接指定本地图片
date: 2019-11-19 12:10:48
categories: WeChat-Mini-Program
tags: WeChat-Mini-Program
---

<img src="http://static.chiefmore.com/u/cms/www/201806/15094709cpct.png" width="100%"/>

<!-- more -->

> 微信小程序使用本地图片作为 background-image 时报错
>
> background-image: url("/images/bg.jpg");
>
> ![](http://code.py40.com/wp-content/uploads/2017/05/QQ_20170511110600.png)

## 设计理念

小程序倡导的是“轻应用”，本身只允许 2M 大小的程序，图片又比较占空间，所以小程序不推荐使用本地图片。

## 解决方案

### 外链

将图片放在服务器上，用远程图片链接地址代替。

### BASE64 编码

将图片进行 BASE64 编码，然后再使用。

```css
background-image: url("data:image/jpg;base64,iVBORw0KGgo=...");
```

### image 标签

> [CSS百分比padding实现比例固定图片自适应布局« 张鑫旭-鑫 ...](https://www.zhangxinxu.com/wordpress/2017/08/css-percent-padding-image-layout/)

```html
<view class="wapper">
    <image src="/images/bg.jpg" mode="widthFix"></image>
</view>
```

```css
.wapper {
    position: relative;
  	/* 图片高宽比 */
    padding-bottom: 56%;
    background-color: #ccc;
}

.wapper > image{
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
}
```

