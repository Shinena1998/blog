---
title: "对于boolean与bit转化带来的查找问题"
date: 2019-03-23T00:14:14+08:00
categories:
  - Java
  - SpringBoot
  - ORM
  - MySql
tags:
  - Jpa
draft: false
---

由于mysql中没有boolean格式数据，因此spring.data.Jpa会把boolean数据自动转化为bit(1)类型进行储存。但这样存储会带来一个查找问题。<br />数据库<br />
![image.png](https://cdn.nlark.com/yuque/0/2019/png/259755/1552930473458-83cff290-0b3a-4046-ba8d-c27306c04174.png#align=left&display=inline&height=85&name=image.png&originHeight=85&originWidth=190&size=3816&status=done&width=190)
<a name="a7615a7e"></a>
### 在含有bit类型做查询时返回结果永真
```java
//查询分别是bit字段，非bit字段，全部返回
List<ReportInfoMysql> reports = reportRepository.findByProcessOrderByReportId(false);
List<ReportInfoMysql> reportInfoMysqls = reportRepository.findByCount(1);
List<ReportInfoMysql> reportInfoMysqls1 = reportRepository.findAll();
//查询结果
//可以看出process为true
//bit字段查询结果
reports = {ArrayList@11391}  size = 3
 0 = {ReportInfoMysql@11392} 
  reportId = 17
  process = true
  count = 1
  userMysqls = {PersistentBag@11436}  size = 1
 1 = {ReportInfoMysql@11432} 
  reportId = 20
  process = true
  count = 1
  userMysqls = {PersistentBag@11433}  size = 1
 2 = {ReportInfoMysql@11393} 
  reportId = 21
  process = true
  count = 1
  userMysqls = {PersistentBag@11394}  size = 1
//非bit字段查询结果
reportInfoMysqls = {ArrayList@11400}  size = 3
 0 = {ReportInfoMysql@11432} 
  reportId = 20
  process = true
  count = 1
  userMysqls = {PersistentBag@11433}  size = 1
 1 = {ReportInfoMysql@11393} 
  reportId = 21
  process = true
  count = 1
  userMysqls = {PersistentBag@11394}  size = 1
 2 = {ReportInfoMysql@11392} 
  reportId = 17
  process = true
  count = 1
  userMysqls = {PersistentBag@11436}  size = 1
//全部返回结果
reportInfoMysqls1 = {ArrayList@11428}  size = 3
 0 = {ReportInfoMysql@11432} 
  reportId = 20
  process = true
  count = 1
  userMysqls = {PersistentBag@11433}  size = 1
 1 = {ReportInfoMysql@11393} 
  reportId = 21
  process = true
  count = 1
  userMysqls = {PersistentBag@11394}  size = 1
 2 = {ReportInfoMysql@11392} 
  reportId = 17
  process = true
  count = 1
  userMysqls = {PersistentBag@11436}  size = 1
```

原因大概是因为bit在mysql中存储比较特别，其并不是二进制数据，而是ascii数据，当bit为0，则返回ascii为0的字符。因此为永真，然而0对应的字符空字符，java中int 0对应false。
<a name="594f9ab8"></a>
### 解决办法：手动让数据库将boolean数据存为tinyint的。

```java
@Column(columnDefinition = "tinyint")
private boolean process;//数据库存为tinyint类型，长度为4，length只对String有效
//继续使用上述例子
List<ReportInfoMysql> reports = reportRepository.findByProcessOrderByReportId(false);
List<ReportInfoMysql> reportInfoMysqls = reportRepository.findByCount(1);
List<ReportInfoMysql> reportInfoMysqls1 = reportRepository.findAll();
//结果与数据库一直
reports = {ArrayList@12334}  size = 3
 0 = {ReportInfoMysql@12335} 
  reportId = 17
  process = false
  count = 1
  userMysqls = {PersistentBag@12342}  size = 1
 1 = {ReportInfoMysql@12341} 
  reportId = 20
  process = false
  count = 1
  userMysqls = {PersistentBag@12343}  size = 1
 2 = {ReportInfoMysql@12336} 
  reportId = 21
  process = false
  count = 1
  userMysqls = {PersistentBag@12337}  size = 1
    
    
    
reportInfoMysqls = {ArrayList@12344}  size = 3
 0 = {ReportInfoMysql@12341} 
  reportId = 20
  process = false
  count = 1
  userMysqls = {PersistentBag@12343}  size = 1
 1 = {ReportInfoMysql@12336} 
  reportId = 21
  process = false
  count = 1
  userMysqls = {PersistentBag@12337}  size = 1
 2 = {ReportInfoMysql@12335} 
  reportId = 17
  process = false
  count = 1
  userMysqls = {PersistentBag@12342}  size = 1
    
    
reportInfoMysqls1 = {ArrayList@12354}  size = 3
 0 = {ReportInfoMysql@12341} 
  reportId = 20
  process = false
  count = 1
  userMysqls = {PersistentBag@12343}  size = 1
 1 = {ReportInfoMysql@12336} 
  reportId = 21
  process = false
  count = 1
  userMysqls = {PersistentBag@12337}  size = 1
 2 = {ReportInfoMysql@12335} 
  reportId = 17
  process = false
  count = 1
  userMysqls = {PersistentBag@12342}  size = 1
```

