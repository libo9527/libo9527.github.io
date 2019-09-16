---
title: Study Notes of Apache Commons
description: Apache Commons 笔记
date: 2019-08-30 10:11:51
categories:
- Apache
- Commons
tags: Apache Commons
---

<img src="https://commons.apache.org/images/commons-logo.png" width="100%"/>

<!-- more -->

## Commons Lang

> ## Commons Lang
>
> The standard Java libraries fail to provide enough methods for manipulation of its core classes. Apache Commons Lang provides these extra methods.
>
> Lang provides a host of helper utilities for the java.lang API, notably String manipulation methods, basic numerical methods, object reflection, concurrency, creation and serialization and System properties. Additionally it contains basic enhancements to java.util.Date and a series of utilities dedicated to help with building methods, such as hashCode, toString and equals.

### StringEscapeUtils

```java
public static void main(String[] args) {
  System.out.println("&apos;s-Hertogenbosch");
  System.out.println(StringEscapeUtils.unescapeXml("&apos;s-Hertogenbosch"));
  //&apos;s-Hertogenbosch
	//'s-Hertogenbosch
}
```

