---
title: Study Notes of Json
description: Json 笔记
comments: false
hidden: false
top: false
date: 2020-09-16 14:01:39
categories:
tags:
---

<img src="https://i.ytimg.com/vi/0IoG-mSvWSo/maxresdefault.jpg" width="100%"/>

<!-- more -->

## Fastjson

### json string > List

```java
List<User> userList = JSON.parseArray(userStr, User.class);
```

### SimplePropertyPreFilter

#### 剔除某个属性

```java
// 去掉 product 中的 productText 字段
SimplePropertyPreFilter filter = new SimplePropertyPreFilter();
filter.getExcludes().add("productText");
JSON.toJSONString(product, filter);
```

### 自定义序列化与反序列化

#### 自定义序列化

> [JSON最佳实践](http://kimmking.github.io/2017/06/06/json-best-practice/)
>
> [使用serializeUsing制定属性的序列化类](https://gakkil.gitee.io/2019/12/22/fastjson-de-jin-jie-er/#toc-heading-8)
>
> [Java Unescape HTML to String Example - HowToDoInJava](https://howtodoinjava.com/java/string/unescape-html-to-string/)

转换 productTitle 字段中包含的 HTML 特殊字符

1. 自定义序列化器

   ```java
   import org.apache.commons.text.StringEscapeUtils;
   
   public class StringEscapeSerialization implements ObjectSerializer {
   
     @Override
     public void write(JSONSerializer serializer, Object object, Object fieldName, Type fieldType, int features) throws IOException {
       SerializeWriter out = serializer.out;
       if (object == null) {
         out.writeNull();
       } else if (object instanceof String) {
         out.writeString(StringEscapeUtils.unescapeHtml4((String) object));
       }
     }
   }
   ```

2. 使用自定义的序列化器

   ```java
   public class Product {
   
     @JSONField(serializeUsing= StringEscapeSerialization.class)
     private String productTitle;
   }
   ```

3. 测试

   ```java
   public static void main(String[] args) {
     Product product = new Product();
     product.setProductTitle("iPhone X Pro &quot;HK&quot;");
     String s = JSON.toJSONString(product);
     System.out.println(s);
   }
   ```
   
4. 结果

   ```
   {"productTitle":"iPhone X Pro \"HK\""}
   ```

## json-smart

## GSON