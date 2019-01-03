---
title: Study Notes of Guns
description: 更简洁的后台管理系统
date: 2018-12-25 16:28:40
categories:
 - Java
 - Guns
tags:
 - Guns
---

![](https://i.loli.net/2018/12/26/5c23433372f23.jpg)

<!-- more -->

> Guns基于SpringBoot 2，致力于做更简洁的后台管理系统，完美整合springmvc + shiro + mybatis-plus + beetl!Guns项目代码简洁，注释丰富，上手容易，同时Guns包含许多基础模块(用户管理，角色管理，部门管理，字典管理等10个模块)，可以直接作为一个后台管理系统的脚手架!
>
> [http://stylefeng.cn](http://stylefeng.cn/)

## 后端分页

> 可参考 Guns 的【日志管理】模块
>
> [分页插件| MyBatis-Plus](https://mp.baomidou.com/guide/page.html)

### 使用

1. 前端初始化[Bootstrap Table](http://bootstrap-table.wenzhixin.net.cn/zh-cn/documentation/)时设置分页方式为服务端分页。（`static/modular/system/log/log.js`）
2. 后端接口使用`Guns`提供的工具类`common.constant.factory.PageFactory`来获取[`MyBatis-Plus`分页插件](https://mp.baomidou.com/guide/page.html)所需的`Page`对象。
3. 然后将`Page`对象传递到`Mapper`层进行查询，[`MyBatis-Plus`分页插件](https://mp.baomidou.com/guide/page.html)会自动帮你实现分页。
4. 将查询结果放入`Page`对象的`records`属性中。
5. 使用`Guns`的`core.common.page.PageInfoBT`对分页结果进行封装后返回给前端。

### 分析

1. Guns配置分页插件的地方在`com.dubbo.pay.config.datasource`

## 表单验证

> 参考 Guns 的【用户管理】模块
>
> [BootstrapValidator](http://bootstrapvalidator.votintsev.ru/)
>
> [BootstrapValidator指南](https://mrbird.cc/BootstrapValidator%E6%8C%87%E5%8D%97.html)

1. 必要的`css`和`js`文件在`src\main\webapp\WEB-INF\view\common\_container.html`文件中引入。

   ```html
   <!-- 全局css -->
   <link href="${ctxPath}/static/css/bootstrap.min.css?v=3.3.6" rel="stylesheet">
   <link href="${ctxPath}/static/css/plugins/validate/bootstrapValidator.min.css" rel="stylesheet">
   
   <!-- 全局js -->
   <script src="${ctxPath}/static/js/jquery.min.js?v=2.1.4"></script>
   <script src="${ctxPath}/static/js/bootstrap.min.js?v=3.3.6"></script>
   <script src="${ctxPath}/static/js/plugins/validate/bootstrapValidator.min.js"></script>
   ```

2. HTML 表单
