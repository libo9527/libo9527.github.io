---
title: Encode and Decode
description: Kinds of encoding.
top: false
date: 2019-01-03 15:54:49
tags:
 - Encoding
 - Decoding
---

<img src="http://www.habilelabs.io/wp-content/uploads/2016/08/encode-decode-model-of-communication.jpg" width="100%"/>

<!-- more -->

## 16进制编解码

> [16进制到文本字符串的转换，16进制-BeJSON.com - Json校验](https://www.bejson.com/convert/ox2str/)

```
hello world	>	\x68\x65\x6c\x6c\x6f\x20\x77\x6f\x72\x6c\x64
```

## ASCLL

```
你好	>	\u4f60\u597d
```

## Unicode

```
hello world	>	&#104;&#101;&#108;&#108;&#111;&#32;&#119;&#111;&#114;&#108;&#100;
```

## URL

```
http://www.baidu.com?q=你好	>	http://www.baidu.com?q=%E4%BD%A0%E5%A5%BD
```

