---
title: Linux Command
description: Linux 命令
date: 2019-07-31 16:32:20
categories: Linux
tags:
---

<img src="https://opensource.com/sites/default/files/styles/image-full-size/public/lead-images/tux_linux_penguin_code_binary.jpg?itok=TxGxW0KY" width="100%"/>

<!-- more -->

> [Linux命令大全](https://man.linuxde.net/)

## 文件和目录管理

### mkdir

> [mkdir](https://man.linuxde.net/mkdir)

```bash
$ mkdir -p /media/userfiles/cd/temp
# -p, --parents
no error if existing, make parent directories as needed
```

### mv

> [mv | Linux命令大全](https://man.linuxde.net/mv)
>
> **mv命令**用来对文件或目录重新命名，或者将文件从一个目录移到另一个目录中。

#### 实例

- 将 admin 文件夹重命名为 ledger-admin

  ```shell
  # cd 到 admin 上一级
  $ mv admin ledger-admin
  ```

- 将文件 a.txt 从目录 `/a` 移动到 `/b`

  ```shell
  $ mv /a/a.txt /b/a.txt
  ```

  