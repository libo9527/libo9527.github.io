---
title: Study Notes of Bootstrap
description: 简洁、直观、强悍的前端开发框架，让web开发更迅速、简单。
date: 2019-01-03 17:47:45
categories:
 - 前端
 - Bootstrap
tags:
 - Bootstrap
---

<img src="https://www.tutorialrepublic.com/lib/images/bootstrap-illustration.png" width="100%"/>

<!-- more -->

> [Bootstrap](https://getbootstrap.com/2.3.2/)
>
> [Bootstrap中文网](http://www.bootcss.com/)

## BootstrapValidator

> [BootstrapValidator](http://bootstrapvalidator.votintsev.ru/)
>
> [BootstrapValidator指南](https://mrbird.cc/BootstrapValidator%E6%8C%87%E5%8D%97.html)
>
> [BootstrapValidator超详细教程](https://blog.csdn.net/u013938465/article/details/53507109)

```javascript
$(document).ready(function() {
    $(formSelector).bootstrapValidator({
        excluded: ..., // 指定不验证的情况
        feedbackIcons: ...,	// 指定验证后验证字段的提示字体图标。（默认是bootstrap风格）
        live: 'enabled', // 生效规则
        message: 'This value is not valid',	// 通用错误提示语
        submitButtons: 'button[type="submit"]', // 指定提交的按钮
        trigger: null, // 统一触发验证方式
        fields: ... // 表单域配置
    });
});
```

