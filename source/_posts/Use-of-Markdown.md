---
title: Use of Markdown
categories:
  - Markdown
tags:
  - Markdown
date: 2018-06-01 10:13:36
description: Markdown
---

<img src="https://i.loli.net/2018/12/26/5c23262feb7b6.jpg" width="100%"/>

<!-- more -->

> Markdown 是一个 Web 上使用的文本到HTML的转换工具，可以通过简单、易读易写的文本格式生成结构化的HTML文档。目前 github、Stackoverflow 等网站均支持这种格式。——[Markdown](http://www.markdown.cn/)
>

## 生成目录

TOC：table of contents

```shell
[TOC]
```

## 修改图片尺寸

> [Typora-Resize Images](https://support.typora.io/Resize-Image/)

使用`img`标签指定图片样式，eg：

```html
<img src="https://www.google.com/doodles/kamma-rahbeks-241st-birthday" width="100%" />
```

## 表格

> [表格](http://xianbai.me/learn-md/article/extension/table.html)

### 单元格和表头

使用 `|` 来分隔不同的单元格，使用 `-` 来分隔表头和其他行：

```
copy列名 | 列名
--- | ---
列值 | 列值
```

| 列名 | 列名 |
| :--- | :--- |
| 列值 | 列值 |

### 对齐方式

在表头下方的分隔线标记中加入 `:`，即可标记下方单元格内容的对齐方式：

- `:---` 代表左对齐
- `:--:` 代表居中对齐
- `---:` 代表右对齐

```
copy| left | center | right |
| :--- | :----: | ----: |
| aaaa | bbbbbb | ccccc |
| a    | b      | c     |
```

| left | center | right |
| :--- | :----: | ----: |
| aaaa | bbbbbb | ccccc |
| a    |   b    |     c |

## 页面内跳转

1. 页面内定义一个锚，及要跳转到的位置

   `<span id="1">XXX</span>`

2. 使用如下格式定义链接即可

   `[跳转到XXX](#1)`