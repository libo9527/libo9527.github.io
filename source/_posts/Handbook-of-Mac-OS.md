---
title: Handbook of Mac OS
description: Mac OS 使用手册
date: 2019-08-10 09:36:05
categories: Mac-OS
tags: Mac-OS
---

<img src="https://assets.website-files.com/5acbcf3278f9ca8c8c178e76/5bae943f1d8edfa43e6a7ae2_main-mojave.jpg" width="100%"/>

<!-- more -->

## Keymap

> [Mac 键盘快捷键](<https://support.apple.com/zh-cn/HT201236>)
>
> [键盘符号对照表](<https://support.apple.com/zh-cn/guide/mac-help/cpmh0011/mac>)

### 新开一个终端窗口

`Command + T`

### 删除光标后面的字符

`fn + delete`

### 删除整行

`command + delete`

### 查看当前所有窗口

`Control + ↑`

### 显示最前面的应用的所有窗口

`Control + ↓`

### 切换同一应用下不同窗口

`Command + ~`

### 输入特殊字符

`control + command + space`

### 英文名称中间 `·` 的输入

中文输入法下的 `~` 键

### 翻页

`fn + ↑ / ↓`

`space/shift + space`

### 切换全屏窗口

`control + ← / →`

### 最大化窗口

`control + command + F`

### 最小化窗口

`Command + M`

### 截图

> [5种方法来在Mac OS X上截取屏幕截图 - wikiHow](https://zh.wikihow.com/在Mac-OS-X上截取屏幕截图)

- 对整个屏幕进行截图

  `command + shift + 3`

- 对屏幕的一部分进行截图

  `Command + Shift + 4 + 鼠标拉选`

- 对打开的窗口进行截图

  `Command + Shift + 4 + Space + 鼠标点选`

### 回到桌面

`command + F3` / `fn + f11`

### 窗口缩放

`command + '+' / '-'`

## Command Line Tools

> [Mac安装Command Line Tools](<https://www.jianshu.com/p/308d7be8b8df>)

Mac 下使用 git 需要安装Command Line Tools

## Mac 下 Chrome 快捷键

### 刷新

`Command + R`

## Mac 下 IDEA 快捷键

> [默认快捷键PDF](<https://resources.jetbrains.com/storage/products/intellij-idea/docs/IntelliJIDEA_ReferenceCard.pdf>)
>
> [快捷键PDF导出插件](https://disq.us/url?url=https%3A%2F%2Fplugins.jetbrains.com%2Fplugin%2F7066-keymap-exporter%3APzIda8xZJUI_yTke-yVWlDxszNw&cuid=476847)

### 回到历史光标位置

`control + command + ← / →`

### 切换选种字符大小写

`command + shift + U`

### 优化 import 信息

`control + option + o`

### 扩展选中字符

`option + ↑ / ↓`

- 比如光标在单词中间，想要选中整个单词时就可以用这个快捷键。
- 如果光标在 `{ `/  `}`前后时，可使用该快捷键选中整个 {} 的内容。
- 如果光标在 `;` 后，使用该快捷键可选中整行。

### 移动代码

`command + shift + ↑ / ↓`

## Mac 下 WebStorm 快捷键

> **[PDF]**[DEFAULT OS X KEYMAP - JetBrains](https://resources.jetbrains.com/storage/products/webstorm/docs/WebStorm_ReferenceCard.pdf)

### 多行输入

- 连续多行输入

  option + shift + 鼠标下拉选中连续行

- 隔行多行输入

  option + 鼠标点击多行选中

## Terminal

### 查看命令手册

```shell
$ man command
```

### 复制粘贴

pbcopy / pbpaste

实例：

- 将生成的 SSH 公钥复制到剪切板

  ```shell
  $ pbcopy < ~/.ssh/id_sra.pub
  # or
  $ cat ~/.ssh/id_sra.pub | pbcopy
  ```

### 打开目录或文件

open

> **-n**  Open a new instance of the application(s) even if one is already running.

实例：

- 在 finder 中打开当前目录

  ```shell
  $ open .
  ```

- 在 Atom 中打开指定文件

  ```shell
  $ open xxx.txt -a atom
  ```

- 微信双开

  ```shell
  $ open -n /Applications/WeChat.app/Contents/MacOS/WeChat
  ```

### 网络代理

> [Mac 终端环境下配置网络代理](/2020/03/03/Configing-Procy-of-Network-in-Mac-Terminal/)

## Spotlight

### 进入文件所在目录

选中搜索结果后按 `Command + 回车`，之后就能打开这个文件所在的文件夹，并且这个文件已被选中了。

选中 Spotlight 的一个搜索条目，按下 Command 键，在 Spotlight 的预览区下方就会出现所选文件的完整路径。

### 按文件类型和文件格式进行查找

```shell
kind:image keyword
```

```shell
kind:text keyword
```

### 检索 Markdown 文件

> [Markdown to Spotlight](https://gist.github.com/gereon/3150445#gistcomment-2764557)
>
> [Add Markdown Indexing to Mac OS X Spotlight](https://baltazaar.wordpress.com/2018/11/20/add-markdown-support-to-mac-os-x-spotlight/)
>
> [How to Force Spotlight to Index Markdown Files in El Capitan](http://theoveranalyzed.net/2015/12/3/how-to-force-spotlight-to-index-markdown-files-in-el-capitan)
>
> [Fixing Spotlight indexing of Markdown content](https://brettterpstra.com/2011/10/18/fixing-spotlight-indexing-of-markdown-content/)

1. `cp -r /System/Library/Spotlight/RichText.mdimporter ~/`

2. 打开 `RichText.mdimporter/Contents/Info.plist`，添加下面的代码。

   ```xml
   <string>net.daringfireball.markdown</string>
   ```

   ![](https://i.postimg.cc/dwn4fgdw/markdown-finder.png)

3. 重命名 RichText.mdimporter 为 Markdown.mdimporter。

   ```shell
   mv ~/RichText.mdimporter Markdown.mdimporter
   ```

4. 把 Markdown.mdimporter 拷贝到 ` /Library/Spotlight`

   ```shell
   sudo cp -R ~/Markdown.mdimporter /Library/Spotlight
   ```

5. 将 Markdown.mdimporter 导入系统

   ```shell
   mdimport -r /Library/Spotlight/Markdown.mdimporter
   ```

6. 强制 Spotlight 重新建立索引

   ```shell
   sudo mdutil -E /
   ```


### Spotlight 的命令行版本

在命令行中使用 `mdfind` 命令来实现 Spotlight 功能。

#### 限制目录进行检索

```shell
$ mdfind "keyword" -onlyin ~/Document
```

### 使用默认浏览器搜索

1. 打开 spotlight，输入要搜索的关键字
2. Command + b