---
title: Study Notes of Vue
description: Vue 笔记
date: 2019-08-13 15:15:04
categories: 
- Front-end
- Vue
tags: Vue
---

<img src="https://4.bp.blogspot.com/-QPkAcNH1NUQ/WRfc27-qWFI/AAAAAAAAy6g/74NNXOZ4pywIMCBbExG566EhxupnNDDrACLcB/s1600/vue-newsletter.png" width="100%"/>

<!-- more -->

## Vue Loader

### 深度作用选择器

如果你希望 `scoped` 样式中的一个选择器能够作用得“更深”，例如**影响子组件**，你可以使用 `>>>` 操作符：

有些像 Sass 之类的预处理器无法正确解析 `>>>`。这种情况下你可以使用 `/deep/` 操作符取而代之——这是一个 `>>>` 的别名，同样可以正常工作。