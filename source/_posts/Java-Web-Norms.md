---
title: Java Web Norms
description: Java Web 开发手册
comments: false
hidden: false
top: false
date: 2020-05-18 11:52:15
categories: 
- Norms
- Java Web
tags:
---

<img src="https://img.alicdn.com/tfs/TB1piQUC4D1gK0jSZFsXXbldVXa-750-199.jpg" width="100%"/>

<!-- more -->

分层领域模型规约:
> [阿里巴巴Java开发手册（泰山版）.pdf](https://github.com/alibaba/p3c/blob/master/%E9%98%BF%E9%87%8C%E5%B7%B4%E5%B7%B4Java%E5%BC%80%E5%8F%91%E6%89%8B%E5%86%8C%EF%BC%88%E6%B3%B0%E5%B1%B1%E7%89%88%EF%BC%89.pdf)

- DO/PO(Data Object/Persistent Object): 此对象与数据库表结构对应,通过 DAO 层向上传输数据源对象 
 
- DTO(Data Transfer Object): 数据传输对象, Service 或 Manager 向外传输的对象。  

- BO(Business Object): 业务对象, 可以由 Service 层输出的封装业务逻辑的对象  

- Query: 数据查询对象,各层接收上层的查询请求。注意超过2个参数的查询封装,禁止使用Map类      

- VO(View Object): 显示层对象,通常是 Web 向模板渲染引擎层传输的对象
