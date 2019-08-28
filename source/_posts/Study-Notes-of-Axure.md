---
title: Study Notes of Axure
description: Axure 笔记
date: 2019-08-16 09:58:10
categories: Axure
tags: Axure
---

<img src="http://humbleux.com/wp-content/uploads/edd/2017/03/Axure-responsive-Store-template-1-Cover.png" width="100%"/>

<!-- more -->

## 界面详解

> [Axure RP 9 教程—基本功能介绍1-界面介绍](https://www.axure.com.cn/78577/)

## 移动画布

- Axure RP 9支持负坐标，可以通过按住空格键不放，拖动画布来调整编辑区域的位置。
- 按住空格键不放，通过鼠标滚轮垂直方向移动画布，按住 Shift 键不放，通过鼠标滚轮水平方向移动画布
- 点击画布坐标尺左上角的十字交叉（Command + 9）可以回到原点。

## 钢笔

### 绘制路径

- 封闭路径：开始锚点与结束锚点重合
- 开放路径：双击或ESC
- 直转曲：按住左键拖动，根据曲线杆调整弯曲方向

### 编辑路径

- 编辑路径：点击控点激活，拖动锚点编辑
- 多选：按住 Shift 选
- 直曲切换：双击锚点/右键选择直或曲
- 拖动曲线杆调整弯曲程度
- 按住 Command 可以仅调整一边的弧度

### 锚点增删

- 点击路径，新建锚点
- 删除锚点：选择锚点，Del删除/右键删除

## 母版

母版是为了让页面中某一模块内容可以重复使用，并且在修改内容时能够同步更新。例如网站主导航、页脚等。

- 可以利用动态面板的【固定到浏览器】功能实现网站悬浮小窗口

## 单位

### 屏幕尺寸

屏幕大小，屏幕对角线的长度，单位是英寸，1英寸 = 2.54 厘米

### 分辨率

屏幕拥有的像素总数以及屏幕水平（宽）和垂直（高）方向的像素数量，单位为像素。

### 像素密度

ppi（pixels per inch）指每英寸所拥有的像素数。

计算公式：
$$
\sqrt(X^2 + Y^2) / Z
$$

- X：垂直像素数
- Y：水平像素数
- Z：屏幕尺寸
- 即：对角线上的像素数除以屏幕对角线长度

#### ppi 与 dpi

dpi（dot per inch）指每英寸所能印刷出来的网点数，是衡量打印机打印精度的主要参数之一。

### 视网膜屏幕

当你拿的手机距离你约 25-30 厘米，如果手机 ppi 达到 300 以上，你的眼睛将无法分辨出像素点。这就是苹果提出的视网膜屏幕。

### Android 开发单位

Android 程序开发统一使用虚拟尺寸单位：dp/dip（Density-independent pixels）、sp（Scale-independent Pixels）

- dp/dip：用于 Android 元素尺寸，如按钮、导航尺寸
- sp：用于 Android 字体尺寸大小

