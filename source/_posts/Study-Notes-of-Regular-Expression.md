---
title: Study Notes of Regular Expression
description: 正则表达式学习笔记
date: 2020-02-24 10:48:06
categories:
- Study Notes
- Regular Expression
tags:
---

<img src="https://blog.malwarebytes.com/wp-content/uploads/2018/08/regex_puzzle-900x506.jpg" width="100%"/>

<!-- more -->

## 教程

> [learn-regex | GitHub](https://github.com/ziishaned/learn-regex)

## 什么是正则表达式？

> {% label info@正则表达式 %}<mark>是</mark>一组由字母和符号组成的{% label info@特殊文本 %}，它可以<mark>用来</mark>从文本中找出满足你想要的格式的句子。

## 元字符

> 元字符<mark>不</mark>代表他们本身的字面意思，他们都有特殊的含义。

| 元字符 | 描述                                                         |
| ------ | ------------------------------------------------------------ |
| .      | 匹配{% label info@任意 %}{% label danger@单个 %}字符<mark>除了</mark>换行符。 |
| [ ]    | 字符种类。匹配方括号内的任意字符。                           |
| [^ ]   | 否定的字符种类。匹配<mark>除了</mark>方括号里的任意字符      |
| *      | 匹配 <mark>>=0</mark> 个重复的在 `*` 号之前的字符。          |
| +      | 匹配 <mark>>=1</mark> 个重复的 `+` 号前的字符。              |
| ?      | 标记 `?` 之前的字符为<mark>可选</mark>.                      |
| {n,m}  | 匹配 num 个大括号之间的字符 (n <= num <= m).                 |
| (xyz)  | 字符集，匹配与 xyz <mark>完全相等</mark>的字符串.            |
| \|     | 或运算符，匹配符号{% label info@前 %}<mark>或</mark>{% label info@后 %}的字符. |
| \      | 转义字符，用于匹配一些<mark>保留字符</mark> `[ ] ( ) { } . * + ? ^ $ \ |` |
| ^      | 正则表达式的<mark>开始符号</mark>.                           |
| $      | 正则表达式的<mark>结束符号</mark>.                           |

## 实例

### 以 s1开头并且以s2结尾





