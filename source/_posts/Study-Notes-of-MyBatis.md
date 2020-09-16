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

## 参数传递

单参数，参数类型为 List 时，如果没有用 @param 指明变量名，在 xml 中是识别不到此变量名的，但可以使用 “list” 来代替。

```java
interface UserDao {
  List<User> selectByIds(List<Long> ids);
}
```

```xml
<select id="selectByIds" resultType="com.gzhennaxia.entity.User">
	select * from users where id in 
  <foreach collection="list" item="id" open="(" separator="," close=")">
  	#{id}
  </foreach>
</select>
```

## 延迟加载

### 原理

mybatis 的延迟加载是使用动态代理来实现的，如果是在 3.3.0版本以下，采用的是 CGLib 动态代理，之后的版本采用的是 JAVASSIST。

### 按需加载

> 背景：项目中配置了全局的懒加载，但某个接口响应速度太慢，想通过添加缓存层提高响应速度时，发现从 Redis 读取缓存后报错。Google 后发现是 Mybatis 的懒加载导致的。

当全局开启了延迟加载后，如果某条 sql 需要即时加载时，可以使用 fetchType 属性来开启即时加载。

```xml
<select id="selectCarrierName" resultType="string">
  SELECT DISTINCT dc.CARRIERNAME
  FROM DEVICECARRIER dc
  INNER JOIN DEVICECARRIERASSN dcr ON dc.CARRIERID = dcr.CARRIERID
  INNER JOIN PRODUCTDEVICES pd ON dcr.DEVICEID = pd.DEVICEID
  WHERE pd.PRODUCTID = #{productId}
</select>
<resultMap id="productDetailResult" type="com.sst.entity.vo.ProductVo" autoMapping="true">
  <id property="productId" column="PRODUCTID"/>
  <collection property="carrierName" column="PRODUCTID" javaType="java.util.ArrayList" ofType="java.lang.String"
              fetchType="eager"
              select="selectCarrierName"/>
</resultMap>

<select id="getProductDetail" resultMap="productDetailResult">
  SELECT PRODUCTID, PRODUCTNAME, PRODUCTPRICE FROM PRODUCTS WHERE PRODUCTID = #{productId}
</select>
```



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



