---
title: Study Notes of MyBatis
description: 一句话描述这篇文章
comments: false
hidden: false
top: false
date: 2020-03-26 15:16:16
categories:
tags:
---

<img src="https://i.loli.net/2020/03/27/aW4oqx6NYpOTibH.png" width="100%"/>

<!-- more -->

Mybatis 中，参数是List的时候，如果没有用@param指明变量名，在xml中是识别不到此变量名的，但可以使用“list”来代替。

## Others

### ResultMap 联合主键

> 联合主键和复合主键的区别：
>
> 联合主键是多个主键联合后形成的主键，常见于多对多关系表中。
>
> 复合主键是同一张表的多列组合成一个主键。
>
> 联合主键和复合主键都是多列组合而成，区别在于这些列是否是由其他表的主键而来。

项目中要查询哪种/那类商品需要用哪种包装材料！每种包装材料有多种不同的尺寸。

### 包装材料表 PACKAGING_MATERIAL

| **Field** | **Type**      | **Comment** |
| --------- | ------------- | ----------- |
| ID        | NUMBER(20)    | 主键        |
| CODE      | VARCHAR2(20)  | 代号        |
| NAME      | VARCHAR2(255) | 名称        |
| LENGTH    | VARCHAR2(20)  | 长度        |
| WIDTH     | VARCHAR2(20)  | 宽度        |
| HEIGHT    | VARCHAR2(20)  | 高度        |

### 包装材料-产品关系表 MATERIAL_PRODUCT_REL

| **Field**               | **Type**     | **Comment**              |
| ----------------------- | ------------ | ------------------------ |
| ID                      | NUMBER(20)   | 主键                     |
| PACKAGING_MATERIAL_CODE | VARCHAR2(20) | 包装材料代号             |
| PRODUCT_ID              | NUMBER(20)   | 商品ID                   |
| PRODUCT_CATEGORY_ID     | NUMBER(20)   | 商品类别ID               |
| MAX_PRODUCT_QUANTITY    | NUMBER(5)    | 最大可叠加打包的商品数量 |
| PACKAGING_LAYER         | NUMBER(2)    | 包几层                   |

```xml
<resultMap id="materialVoMap" type="com.sst.entity.vo.PackagingMaterialVo">
  <!-- 联合主键 -->
  <id column="PACKAGING_MATERIAL_CODE" property="packagingMaterialCode"/>
  <id column="PRODUCT_ID" property="productId"/>
  <id column="PRODUCT_CATEGORY_ID" property="productCategoryId"/>
  <result column="MAX_PRODUCT_QUANTITY" property="maxProductQuantity"/>
  <result column="PACKAGING_LAYER" property="packagingLayer"/>
  <collection property="packagingMaterials" ofType="com.sst.entity.po.PackagingMaterialPo">
    <id column="ID" property="id"/>
    <result column="NAME" property="name"/>
    <result column="CODE" property="code"/>
    <result column="LENGTH" property="length"/>
    <result column="WIDTH" property="width"/>
    <result column="HEIGHT" property="height"/>
  </collection>
</resultMap>
<select id="selectAll" resultMap="materialVoMap">
  SELECT
  mpr.PACKAGING_MATERIAL_CODE,
  mpr.PRODUCT_ID,
  mpr.PRODUCT_CATEGORY_ID,
  mpr.MAX_PRODUCT_QUANTITY,
  mpr.PACKAGING_LAYER,
  pm.ID,
  pm.NAME,
  pm.CODE,
  pm.LENGTH,
  pm.WIDTH,
  pm.HEIGHT
  FROM
  ( SELECT * FROM MATERIAL_PRODUCT_REL WHERE PRODUCT_ID IN
  <foreach collection="productIds" item="productId" open="(" separator="," close=")">
    #{productId}
  </foreach>
  OR PRODUCT_CATEGORY_ID IN
  <foreach collection="categoryIds" item="categoryId" open="(" separator="," close=")">
    #{categoryId}
  </foreach>
  ) mpr
  LEFT JOIN PACKAGING_MATERIAL pm ON mpr.PACKAGING_MATERIAL_CODE = pm.CODE
</select>
```



