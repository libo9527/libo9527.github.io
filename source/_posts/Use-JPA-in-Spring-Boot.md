---
title: Use JPA in Spring Boot
description: JPA的使用
hidden: true
date: 2019-01-07 11:23:01
categories:
 - JAVA
 - Spring Boot
 - Datasource
tags:
 - Spring Boot
 - JPA
---

<img src="https://javadeveloperzone.com/wp-content/uploads/2018/02/Spring-boot-jpa-mysql-example-1024x488.jpg" width="100%"/>

<!-- more -->

## 什么是JPA

​	JPA 的全称叫做 Java Persistence API，是一个基于 O/R 映射的标准规范。目前 JPA 的主要实现有Hibernate、EclipseLink、OpenJPA等，事实上，由于 Hibernate 在数据访问解决技术领域的绝对霸主地位，JPA的标准基本是由 Hibernate 来主导的。Spring 框架为我们提供了 Spring Data JPA，可以减少我们使用 JPA 时的代码量。

## 配置

1. `spring.jpa.show-sql=true`：日志中打印 SQL 语句。

2. `spring.jpa.hibernate.ddl-auto=create`：配置实体类维护数据库表结构的具体行为。
   - update：当实体类的属性发生变化时，表结构跟着更新。
   - create：启动的时候删除现有的表，并根据实体类重新生成表。
   - create-drop：启动时根据实体类生成表，但是当 sessionFactory 关闭的时候表会被删除。
   - validate：启动时验证实体类和数据表是否一致，不会创建新表，但是会插入新值。
   - none：什么都不做。

3. `spring.jpa.database-platform=org.hibernate.dialect.MySQL5InnoDBDialect`

   在 SrpingBoot 2.0 版本中，Hibernate 创建数据表的时候，默认的数据库存储引擎选择的是 MyISAM （之前好像是 InnoDB，这点比较诡异）。这个参数是在建表的时候，将默认的存储引擎切换为 InnoDB 用的。

## 使用

> [Spring Data JPA 2.1.0.RELEASE API](https://docs.spring.io/spring-data/jpa/docs/2.1.0.RELEASE/api/)

### 多条件查询

> [【一目了然】Spring Data JPA使用Specification动态构建多表查询、复杂查询及排序示例](https://www.jianshu.com/p/659e9715d01d)

1. dao 层接口继承 [JpaSpecificationExecutor](https://docs.spring.io/spring-data/jpa/docs/2.1.0.RELEASE/api/org/springframework/data/jpa/repository/JpaSpecificationExecutor.html) 接口。

2. 调用 [List<T> findAll(Specification<T> spec);](https://docs.spring.io/spring-data/jpa/docs/2.1.0.RELEASE/api/org/springframework/data/jpa/repository/JpaSpecificationExecutor.html#findAll-org.springframework.data.jpa.domain.Specification-)

3. 实现 [Specification](https://docs.spring.io/spring-data/jpa/docs/2.1.0.RELEASE/api/org/springframework/data/jpa/domain/Specification.html)

   ```java
    /**
     * @param root 根对象，即条件将被封装到该对象中。where 列名 = label.getXXX
     * @param query 查询关键字的封装。例如group by、order by等。几乎不用！
     * @param cb 用来封装条件对象。
     * @return 返回null代表没有任何条件
     */
   public Predicate toPredicate(Root<T> root, CriteriaQuery<?> query, CriteriaBuilder cb);
   ```

4. eg：

   ```java
   public List<Student> getStudent(String studentNumber,String name ,String nickName,
               Date birthday,String courseName,float chineseScore,float mathScore,
               float englishScore,float performancePoints) {
       Specification<Student> specification = new Specification<Student>(){
   
           @Override
           public Predicate toPredicate(Root<Student> root, CriteriaQuery<?> query, CriteriaBuilder cb) {
               //用于暂时存放查询条件的集合
               List<Predicate> predicatesList = new ArrayList<>();
               //--------------------------------------------
               //查询条件示例
               //equal示例
               if (!StringUtils.isEmpty(name)){
                   Predicate namePredicate = cb.equal(root.get("name"), name);
                   predicatesList.add(namePredicate);
               }
               //like示例
               if (!StringUtils.isEmpty(nickName)){
                   Predicate nickNamePredicate = cb.like(root.get("nickName"), '%'+nickName+'%');
                   predicatesList.add(nickNamePredicate);
               }
               //between示例
               if (birthday != null) {
                   Predicate birthdayPredicate = cb.between(root.get("birthday"), birthday, new Date());
                   predicatesList.add(birthdayPredicate);
               }
   
               //关联表查询示例
               if (!StringUtils.isEmpty(courseName)) {
                   Join<Student,Teacher> joinTeacher = root.join("teachers",JoinType.LEFT);
                   Predicate coursePredicate = cb.equal(joinTeacher.get("courseName"), courseName);
                   predicatesList.add(coursePredicate);
               }
   
               //复杂条件组合示例
               if (chineseScore!=0 && mathScore!=0 && englishScore!=0 && performancePoints!=0) {
                   Join<Student,Examination> joinExam = root.join("exams",JoinType.LEFT);
                   Predicate predicateExamChinese = cb.ge(joinExam.get("chineseScore"),chineseScore);
                   Predicate predicateExamMath = cb.ge(joinExam.get("mathScore"),mathScore);
                   Predicate predicateExamEnglish = cb.ge(joinExam.get("englishScore"),englishScore);
                   Predicate predicateExamPerformance = cb.ge(joinExam.get("performancePoints"),performancePoints);
                   //组合
                   Predicate predicateExam = cb.or(predicateExamChinese,predicateExamEnglish,predicateExamMath);
                   Predicate predicateExamAll = cb.and(predicateExamPerformance,predicateExam);
                   predicatesList.add(predicateExamAll);
               }
               //--------------------------------------------
               //排序示例(先根据学号排序，后根据姓名排序)
               query.orderBy(cb.asc(root.get("studentNumber")),cb.asc(root.get("name")));
               //--------------------------------------------
               //最终将查询条件拼好然后return
               Predicate[] predicates = new Predicate[predicatesList.size()];
               return cb.and(predicatesList.toArray(predicates));
           }
   
       };
       return repository.findAll(specification);
   }
   ```

> 使用 [jpa-spec](https://link.jianshu.com/?t=https%3A%2F%2Fgithub.com%2Fwenhao%2Fjpa-spec) 能够减少一些代码量

### 分页

1. dao 层接口继承 `JpaSpecificationExecutor`接口。

2. 调用 [Page<T> findAll(@Nullable Specification<T> spec, Pageable pageable)](https://docs.spring.io/spring-data/jpa/docs/2.1.0.RELEASE/api/org/springframework/data/jpa/repository/JpaSpecificationExecutor.html#findAll-org.springframework.data.jpa.domain.Specification-org.springframework.data.domain.Pageable-)

3. 实现 [Specification](https://docs.spring.io/spring-data/jpa/docs/2.1.0.RELEASE/api/org/springframework/data/jpa/domain/Specification.html)，及 [Pageable](https://docs.spring.io/spring-data/commons/docs/current/api/org/springframework/data/domain/Pageable.html?is-external=true)

   ```java
    /**
     * @param root 根对象，即条件将被封装到该对象中。where 列名 = label.getXXX
     * @param query 查询关键字的封装。例如group by、order by等。几乎不用！
     * @param cb 用来封装条件对象。
     * @param pageable 用来封装分页的对象。
     * @return 返回null代表没有任何条件
     */
   public Page<T> toPredicate(Root<T> root, CriteriaQuery<?> query, CriteriaBuilder cb, Pageable pageable);
   ```

4. eg：

   ```java
   public Page<User> selectAll(int pageNum, int pageSize) {
       Pageable pageable = new PageRequest(pageNum, pageSize);
       Page<User> users = userRepository.findAll(new Specification<Student>(){
   
           @Override
           public Predicate toPredicate(Root<Student> root, CriteriaQuery<?> query, CriteriaBuilder cb) {
               List<Predicate> predicatesList = new ArrayList<>();
               if (!StringUtils.isEmpty(name)){
                   Predicate namePredicate = cb.equal(root.get("name"), name);
                   predicatesList.add(namePredicate);
               }
               Predicate[] predicates = new Predicate[predicatesList.size()];
               return cb.and(predicatesList.toArray(predicates));
           }, pageable);
       }
   }
   ```

### 事务

> [JPA事务问题Executing an update/delete query](https://blog.csdn.net/moshowgame/article/details/80090453)
>
> [用Spring boot jpa update modify delete 数据和事务管理的那些坑](https://blog.csdn.net/hanghangde/article/details/53241150)

JPA 要求**没有事务支持，不能执行更新和删除操作**。

所以在 Service 层上必须加上事务注解`@Transactional`，如果没加则会报如下异常：`Executing an update/delete query`

