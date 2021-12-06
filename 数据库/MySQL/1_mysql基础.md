# MySQL-基础篇

- 主要来自博客：https://blog.csdn.net/m0_46153949/article/details/107099793

- Overview
  - 初识Mysql
    - 为什么？
    - 是什么？
    - 有什么用？
  - SQL语言分类
    - DDL
    - DML
    - DQL
    - DCL
    - TCL
  - DQL语言详解
  - DML语言详解
  - DDL语言详解
  - TCL语言详解
  - 其他
    - 视图
    - 变量
    - 存储过程和函数
    - 流程控制结构

## 1. 初识MySQL

- 为什么要学习数据库？
  - 实现数据持久化
  - 使用完整的管理系统对数据进行统一管理、易于查询、方便使用
- 数据库的相关概念
  - DB：DataBase：存储数据的仓库，保存一系列由组织的数据
  - DBMS：Database Management System：用于操作和管理数据库的系统软件，用于建立、使用和维护数据库
    - 常见的数据库管理系统：MySQL、Oracle、DB2、SqlServer等
  - SQL：Structure Query Language
    - 结构化查询语言：专门用来与数据库通信的语言
    - 优点：
      - 简单易学
      - 几乎所有的DBMS都支持SQL
      - 灵活可进行非常复杂和高级的数据库操作
  - 以上三者的关系：
    - DBMS通过SQL来创建、管理、操作DB
- 数据库存储数据的特点
  - 数据放入数据表中，表再放入数据库中
  - 一个数据库中可以有多个表，每个表都有一个名字来标识自己，表名具有唯一性
  - 表具有一些特性，规定了数据再表中如何存储，类似java中类的设计
  - 表由列组成(也称为字段)，所有的表都是由一个或者多个列组成的，每一列类似java中类的属性
  - 表中的数据是按行存储的，每一行类似java中的对象

### 1.1 MySQL安装与使用

- 介绍：

  - MySQL数据库隶属于MySQLAB公司，总部位于瑞典，后被oracle收购。
  - 优点：
    - 成本低：开放源代码，一般可以免费试用；
    - 简单：很容易安装和使用；
    - 性能高：执行很快

- 安装：

  - DBMS分为两类：
    - 基于共享文件系统的DBMS （Access）
    - 基于客户机——服务器的DBMS（MySQL、Oracle、SqlServer）。
    - 版本可分为：社区版（免费），企业版（收费）
  - window平台的下载和安装和卸载：也可以百度orb站看视频
    - 下载：https://dev.mysql.com/downloads/mysql/
    - 安装卸载：https://blog.csdn.net/Y_BlueBlack/article/details/81433401

- 启动与停止MySQL

  - 法一：右击电脑=>管理=>服务=>启动或者停止MySQL服务

  - 法二：使用管理员权限打开cmd，输入命令

    - ```mysql
      启动：net start mysql(服务名)
      停止：net stop mysql(服务名)
      ```

- MySQL基本使用

  - 如果没有配置mysql对应的环境变量，可以直接cmd到mysql的安装包的bin目录下使用这些命令

  - 登录和登出：
  
    - ```mysql
      登录
      mysql -h 主机名 -u 用户名 -p 密码
      如：mysql -h localhost -u root -p123456
      
      退出
    exit或者ctrl+c
      ```
  
  - 常用命令介绍
  
    - ```mysql
      1. 进入mysql，在命令行中输入
      mysql -uroot -p
      
      2. 查看mysql中有哪些数据库
      show databases;
      
      3. 新建一个数据库
      create database 数据库名;
      
      4. 选择一个数据库
      use 数据库名称;
      
      5. 查看使用数据库中的数据表
      show tables;
      
      6. 查看指定数据库中的数据表
      show tables from 数据库名;
      
      7. 查询当前所在的数据库
      select database();
      
      8. 新建一张数据表
      create table math(
      	id int,
          name varchar(20)
      );
      
      9. 查看表的结构
      desc 表名
      
      10. 查看表中的所有记录
      select * from 表名;
      
      11. 向表中插入记录
      insert into 表名(列名列表) values (列对应的值的列表);
      注意：插入varchar 或者data类型的数据时要用单引号引起来
      
      12. 修改表中的记录
      update 表名 set 列1=更新后的值,列2=更新后的值,... where ...;
      
      13. 删除表中的记录
      delete from 表名 where ...
      
      14. 删除数据表
      drop table 表名;
      ```
  
  - 查看MySQL服务端版本
  
    - ```mysql
      登录到mysql服务端后，
      select version();
      
      没有登录到msyql服务端,在cmd中输入：
      mysql --version
      ```
  
- MySQL语法规范

  - 不区分大小写，但是建议关键字大写，表名、列名小写
  - 每句话用;或者\g结尾，最后使用;结尾
  - 每条命令根据需要，可以进行缩进或者换行
  - 注释
    - 单行注释：#xxx
    - 单行注释：--xxxx
    - 多行注释：/\*xxx\*/

### 1.2 SQL语言的分类

- DQL：Data Query Language：数据查询语言。用于检索数据库中的数据，主要是Select
- DML：Data Manipulation Language：数据操纵语言。用于改变数据库中的数据，主要包括
  - insert、update、delete
- DDL：Data Define Language：数据定义语言。用于数据库和表的创建、修改、删除，主要包括
  - create、drop、alter
- DCL：Data Control Language：数据控制语言。用于定义用户访问权限和安全级别，主要包括
  - grant、revoke
- TCL：Transcation Control Language：事务控制语言，用于维护数据的一致性，主要包括
  - commit、roolback、savepoint

### 1.3 后续需要使用到的数据表文件

- myemployees文件

```mysql
/*
SQLyog Ultimate v10.00 Beta1
MySQL - 5.5.15 : Database - myemployees
*********************************************************************
*/


/*!40101 SET NAMES utf8 */;

/*!40101 SET SQL_MODE=''*/;

/*!40014 SET @OLD_UNIQUE_CHECKS=@@UNIQUE_CHECKS, UNIQUE_CHECKS=0 */;
/*!40014 SET @OLD_FOREIGN_KEY_CHECKS=@@FOREIGN_KEY_CHECKS, FOREIGN_KEY_CHECKS=0 */;
/*!40101 SET @OLD_SQL_MODE=@@SQL_MODE, SQL_MODE='NO_AUTO_VALUE_ON_ZERO' */;
/*!40111 SET @OLD_SQL_NOTES=@@SQL_NOTES, SQL_NOTES=0 */;
CREATE DATABASE /*!32312 IF NOT EXISTS*/`myemployees` /*!40100 DEFAULT CHARACTER SET gb2312 */;

USE `myemployees`;

/*Table structure for table `departments` */

DROP TABLE IF EXISTS `departments`;

CREATE TABLE `departments` (
  `department_id` int(4) NOT NULL AUTO_INCREMENT,
  `department_name` varchar(3) DEFAULT NULL,
  `manager_id` int(6) DEFAULT NULL,
  `location_id` int(4) DEFAULT NULL,
  PRIMARY KEY (`department_id`),
  KEY `loc_id_fk` (`location_id`),
  CONSTRAINT `loc_id_fk` FOREIGN KEY (`location_id`) REFERENCES `locations` (`location_id`)
) ENGINE=InnoDB AUTO_INCREMENT=271 DEFAULT CHARSET=gb2312;

/*Data for the table `departments` */

insert  into `departments`(`department_id`,`department_name`,`manager_id`,`location_id`) values (10,'Adm',200,1700),(20,'Mar',201,1800),(30,'Pur',114,1700),(40,'Hum',203,2400),(50,'Shi',121,1500),(60,'IT',103,1400),(70,'Pub',204,2700),(80,'Sal',145,2500),(90,'Exe',100,1700),(100,'Fin',108,1700),(110,'Acc',205,1700),(120,'Tre',NULL,1700),(130,'Cor',NULL,1700),(140,'Con',NULL,1700),(150,'Sha',NULL,1700),(160,'Ben',NULL,1700),(170,'Man',NULL,1700),(180,'Con',NULL,1700),(190,'Con',NULL,1700),(200,'Ope',NULL,1700),(210,'IT ',NULL,1700),(220,'NOC',NULL,1700),(230,'IT ',NULL,1700),(240,'Gov',NULL,1700),(250,'Ret',NULL,1700),(260,'Rec',NULL,1700),(270,'Pay',NULL,1700);

/*Table structure for table `employees` */

DROP TABLE IF EXISTS `employees`;

CREATE TABLE `employees` (
  `employee_id` int(6) NOT NULL AUTO_INCREMENT,
  `first_name` varchar(20) DEFAULT NULL,
  `last_name` varchar(25) DEFAULT NULL,
  `email` varchar(25) DEFAULT NULL,
  `phone_number` varchar(20) DEFAULT NULL,
  `job_id` varchar(10) DEFAULT NULL,
  `salary` double(10,2) DEFAULT NULL,
  `commission_pct` double(4,2) DEFAULT NULL,
  `manager_id` int(6) DEFAULT NULL,
  `department_id` int(4) DEFAULT NULL,
  `hiredate` datetime DEFAULT NULL,
  PRIMARY KEY (`employee_id`),
  KEY `dept_id_fk` (`department_id`),
  KEY `job_id_fk` (`job_id`),
  CONSTRAINT `dept_id_fk` FOREIGN KEY (`department_id`) REFERENCES `departments` (`department_id`),
  CONSTRAINT `job_id_fk` FOREIGN KEY (`job_id`) REFERENCES `jobs` (`job_id`)
) ENGINE=InnoDB AUTO_INCREMENT=207 DEFAULT CHARSET=gb2312;

/*Data for the table `employees` */

insert  into `employees`(`employee_id`,`first_name`,`last_name`,`email`,`phone_number`,`job_id`,`salary`,`commission_pct`,`manager_id`,`department_id`,`hiredate`) values (100,'Steven','K_ing','SKING','515.123.4567','AD_PRES',24000.00,NULL,NULL,90,'1992-04-03 00:00:00'),(101,'Neena','Kochhar','NKOCHHAR','515.123.4568','AD_VP',17000.00,NULL,100,90,'1992-04-03 00:00:00'),(102,'Lex','De Haan','LDEHAAN','515.123.4569','AD_VP',17000.00,NULL,100,90,'1992-04-03 00:00:00'),(103,'Alexander','Hunold','AHUNOLD','590.423.4567','IT_PROG',9000.00,NULL,102,60,'1992-04-03 00:00:00'),(104,'Bruce','Ernst','BERNST','590.423.4568','IT_PROG',6000.00,NULL,103,60,'1992-04-03 00:00:00'),(105,'David','Austin','DAUSTIN','590.423.4569','IT_PROG',4800.00,NULL,103,60,'1998-03-03 00:00:00'),(106,'Valli','Pataballa','VPATABAL','590.423.4560','IT_PROG',4800.00,NULL,103,60,'1998-03-03 00:00:00'),(107,'Diana','Lorentz','DLORENTZ','590.423.5567','IT_PROG',4200.00,NULL,103,60,'1998-03-03 00:00:00'),(108,'Nancy','Greenberg','NGREENBE','515.124.4569','FI_MGR',12000.00,NULL,101,100,'1998-03-03 00:00:00'),(109,'Daniel','Faviet','DFAVIET','515.124.4169','FI_ACCOUNT',9000.00,NULL,108,100,'1998-03-03 00:00:00'),(110,'John','Chen','JCHEN','515.124.4269','FI_ACCOUNT',8200.00,NULL,108,100,'2000-09-09 00:00:00'),(111,'Ismael','Sciarra','ISCIARRA','515.124.4369','FI_ACCOUNT',7700.00,NULL,108,100,'2000-09-09 00:00:00'),(112,'Jose Manuel','Urman','JMURMAN','515.124.4469','FI_ACCOUNT',7800.00,NULL,108,100,'2000-09-09 00:00:00'),(113,'Luis','Popp','LPOPP','515.124.4567','FI_ACCOUNT',6900.00,NULL,108,100,'2000-09-09 00:00:00'),(114,'Den','Raphaely','DRAPHEAL','515.127.4561','PU_MAN',11000.00,NULL,100,30,'2000-09-09 00:00:00'),(115,'Alexander','Khoo','AKHOO','515.127.4562','PU_CLERK',3100.00,NULL,114,30,'2000-09-09 00:00:00'),(116,'Shelli','Baida','SBAIDA','515.127.4563','PU_CLERK',2900.00,NULL,114,30,'2000-09-09 00:00:00'),(117,'Sigal','Tobias','STOBIAS','515.127.4564','PU_CLERK',2800.00,NULL,114,30,'2000-09-09 00:00:00'),(118,'Guy','Himuro','GHIMURO','515.127.4565','PU_CLERK',2600.00,NULL,114,30,'2000-09-09 00:00:00'),(119,'Karen','Colmenares','KCOLMENA','515.127.4566','PU_CLERK',2500.00,NULL,114,30,'2000-09-09 00:00:00'),(120,'Matthew','Weiss','MWEISS','650.123.1234','ST_MAN',8000.00,NULL,100,50,'2004-02-06 00:00:00'),(121,'Adam','Fripp','AFRIPP','650.123.2234','ST_MAN',8200.00,NULL,100,50,'2004-02-06 00:00:00'),(122,'Payam','Kaufling','PKAUFLIN','650.123.3234','ST_MAN',7900.00,NULL,100,50,'2004-02-06 00:00:00'),(123,'Shanta','Vollman','SVOLLMAN','650.123.4234','ST_MAN',6500.00,NULL,100,50,'2004-02-06 00:00:00'),(124,'Kevin','Mourgos','KMOURGOS','650.123.5234','ST_MAN',5800.00,NULL,100,50,'2004-02-06 00:00:00'),(125,'Julia','Nayer','JNAYER','650.124.1214','ST_CLERK',3200.00,NULL,120,50,'2004-02-06 00:00:00'),(126,'Irene','Mikkilineni','IMIKKILI','650.124.1224','ST_CLERK',2700.00,NULL,120,50,'2004-02-06 00:00:00'),(127,'James','Landry','JLANDRY','650.124.1334','ST_CLERK',2400.00,NULL,120,50,'2004-02-06 00:00:00'),(128,'Steven','Markle','SMARKLE','650.124.1434','ST_CLERK',2200.00,NULL,120,50,'2004-02-06 00:00:00'),(129,'Laura','Bissot','LBISSOT','650.124.5234','ST_CLERK',3300.00,NULL,121,50,'2004-02-06 00:00:00'),(130,'Mozhe','Atkinson','MATKINSO','650.124.6234','ST_CLERK',2800.00,NULL,121,50,'2004-02-06 00:00:00'),(131,'James','Marlow','JAMRLOW','650.124.7234','ST_CLERK',2500.00,NULL,121,50,'2004-02-06 00:00:00'),(132,'TJ','Olson','TJOLSON','650.124.8234','ST_CLERK',2100.00,NULL,121,50,'2004-02-06 00:00:00'),(133,'Jason','Mallin','JMALLIN','650.127.1934','ST_CLERK',3300.00,NULL,122,50,'2004-02-06 00:00:00'),(134,'Michael','Rogers','MROGERS','650.127.1834','ST_CLERK',2900.00,NULL,122,50,'2002-12-23 00:00:00'),(135,'Ki','Gee','KGEE','650.127.1734','ST_CLERK',2400.00,NULL,122,50,'2002-12-23 00:00:00'),(136,'Hazel','Philtanker','HPHILTAN','650.127.1634','ST_CLERK',2200.00,NULL,122,50,'2002-12-23 00:00:00'),(137,'Renske','Ladwig','RLADWIG','650.121.1234','ST_CLERK',3600.00,NULL,123,50,'2002-12-23 00:00:00'),(138,'Stephen','Stiles','SSTILES','650.121.2034','ST_CLERK',3200.00,NULL,123,50,'2002-12-23 00:00:00'),(139,'John','Seo','JSEO','650.121.2019','ST_CLERK',2700.00,NULL,123,50,'2002-12-23 00:00:00'),(140,'Joshua','Patel','JPATEL','650.121.1834','ST_CLERK',2500.00,NULL,123,50,'2002-12-23 00:00:00'),(141,'Trenna','Rajs','TRAJS','650.121.8009','ST_CLERK',3500.00,NULL,124,50,'2002-12-23 00:00:00'),(142,'Curtis','Davies','CDAVIES','650.121.2994','ST_CLERK',3100.00,NULL,124,50,'2002-12-23 00:00:00'),(143,'Randall','Matos','RMATOS','650.121.2874','ST_CLERK',2600.00,NULL,124,50,'2002-12-23 00:00:00'),(144,'Peter','Vargas','PVARGAS','650.121.2004','ST_CLERK',2500.00,NULL,124,50,'2002-12-23 00:00:00'),(145,'John','Russell','JRUSSEL','011.44.1344.429268','SA_MAN',14000.00,0.40,100,80,'2002-12-23 00:00:00'),(146,'Karen','Partners','KPARTNER','011.44.1344.467268','SA_MAN',13500.00,0.30,100,80,'2002-12-23 00:00:00'),(147,'Alberto','Errazuriz','AERRAZUR','011.44.1344.429278','SA_MAN',12000.00,0.30,100,80,'2002-12-23 00:00:00'),(148,'Gerald','Cambrault','GCAMBRAU','011.44.1344.619268','SA_MAN',11000.00,0.30,100,80,'2002-12-23 00:00:00'),(149,'Eleni','Zlotkey','EZLOTKEY','011.44.1344.429018','SA_MAN',10500.00,0.20,100,80,'2002-12-23 00:00:00'),(150,'Peter','Tucker','PTUCKER','011.44.1344.129268','SA_REP',10000.00,0.30,145,80,'2014-03-05 00:00:00'),(151,'David','Bernstein','DBERNSTE','011.44.1344.345268','SA_REP',9500.00,0.25,145,80,'2014-03-05 00:00:00'),(152,'Peter','Hall','PHALL','011.44.1344.478968','SA_REP',9000.00,0.25,145,80,'2014-03-05 00:00:00'),(153,'Christopher','Olsen','COLSEN','011.44.1344.498718','SA_REP',8000.00,0.20,145,80,'2014-03-05 00:00:00'),(154,'Nanette','Cambrault','NCAMBRAU','011.44.1344.987668','SA_REP',7500.00,0.20,145,80,'2014-03-05 00:00:00'),(155,'Oliver','Tuvault','OTUVAULT','011.44.1344.486508','SA_REP',7000.00,0.15,145,80,'2014-03-05 00:00:00'),(156,'Janette','K_ing','JKING','011.44.1345.429268','SA_REP',10000.00,0.35,146,80,'2014-03-05 00:00:00'),(157,'Patrick','Sully','PSULLY','011.44.1345.929268','SA_REP',9500.00,0.35,146,80,'2014-03-05 00:00:00'),(158,'Allan','McEwen','AMCEWEN','011.44.1345.829268','SA_REP',9000.00,0.35,146,80,'2014-03-05 00:00:00'),(159,'Lindsey','Smith','LSMITH','011.44.1345.729268','SA_REP',8000.00,0.30,146,80,'2014-03-05 00:00:00'),(160,'Louise','Doran','LDORAN','011.44.1345.629268','SA_REP',7500.00,0.30,146,80,'2014-03-05 00:00:00'),(161,'Sarath','Sewall','SSEWALL','011.44.1345.529268','SA_REP',7000.00,0.25,146,80,'2014-03-05 00:00:00'),(162,'Clara','Vishney','CVISHNEY','011.44.1346.129268','SA_REP',10500.00,0.25,147,80,'2014-03-05 00:00:00'),(163,'Danielle','Greene','DGREENE','011.44.1346.229268','SA_REP',9500.00,0.15,147,80,'2014-03-05 00:00:00'),(164,'Mattea','Marvins','MMARVINS','011.44.1346.329268','SA_REP',7200.00,0.10,147,80,'2014-03-05 00:00:00'),(165,'David','Lee','DLEE','011.44.1346.529268','SA_REP',6800.00,0.10,147,80,'2014-03-05 00:00:00'),(166,'Sundar','Ande','SANDE','011.44.1346.629268','SA_REP',6400.00,0.10,147,80,'2014-03-05 00:00:00'),(167,'Amit','Banda','ABANDA','011.44.1346.729268','SA_REP',6200.00,0.10,147,80,'2014-03-05 00:00:00'),(168,'Lisa','Ozer','LOZER','011.44.1343.929268','SA_REP',11500.00,0.25,148,80,'2014-03-05 00:00:00'),(169,'Harrison','Bloom','HBLOOM','011.44.1343.829268','SA_REP',10000.00,0.20,148,80,'2014-03-05 00:00:00'),(170,'Tayler','Fox','TFOX','011.44.1343.729268','SA_REP',9600.00,0.20,148,80,'2014-03-05 00:00:00'),(171,'William','Smith','WSMITH','011.44.1343.629268','SA_REP',7400.00,0.15,148,80,'2014-03-05 00:00:00'),(172,'Elizabeth','Bates','EBATES','011.44.1343.529268','SA_REP',7300.00,0.15,148,80,'2014-03-05 00:00:00'),(173,'Sundita','Kumar','SKUMAR','011.44.1343.329268','SA_REP',6100.00,0.10,148,80,'2014-03-05 00:00:00'),(174,'Ellen','Abel','EABEL','011.44.1644.429267','SA_REP',11000.00,0.30,149,80,'2014-03-05 00:00:00'),(175,'Alyssa','Hutton','AHUTTON','011.44.1644.429266','SA_REP',8800.00,0.25,149,80,'2014-03-05 00:00:00'),(176,'Jonathon','Taylor','JTAYLOR','011.44.1644.429265','SA_REP',8600.00,0.20,149,80,'2014-03-05 00:00:00'),(177,'Jack','Livingston','JLIVINGS','011.44.1644.429264','SA_REP',8400.00,0.20,149,80,'2014-03-05 00:00:00'),(178,'Kimberely','Grant','KGRANT','011.44.1644.429263','SA_REP',7000.00,0.15,149,NULL,'2014-03-05 00:00:00'),(179,'Charles','Johnson','CJOHNSON','011.44.1644.429262','SA_REP',6200.00,0.10,149,80,'2014-03-05 00:00:00'),(180,'Winston','Taylor','WTAYLOR','650.507.9876','SH_CLERK',3200.00,NULL,120,50,'2014-03-05 00:00:00'),(181,'Jean','Fleaur','JFLEAUR','650.507.9877','SH_CLERK',3100.00,NULL,120,50,'2014-03-05 00:00:00'),(182,'Martha','Sullivan','MSULLIVA','650.507.9878','SH_CLERK',2500.00,NULL,120,50,'2014-03-05 00:00:00'),(183,'Girard','Geoni','GGEONI','650.507.9879','SH_CLERK',2800.00,NULL,120,50,'2014-03-05 00:00:00'),(184,'Nandita','Sarchand','NSARCHAN','650.509.1876','SH_CLERK',4200.00,NULL,121,50,'2014-03-05 00:00:00'),(185,'Alexis','Bull','ABULL','650.509.2876','SH_CLERK',4100.00,NULL,121,50,'2014-03-05 00:00:00'),(186,'Julia','Dellinger','JDELLING','650.509.3876','SH_CLERK',3400.00,NULL,121,50,'2014-03-05 00:00:00'),(187,'Anthony','Cabrio','ACABRIO','650.509.4876','SH_CLERK',3000.00,NULL,121,50,'2014-03-05 00:00:00'),(188,'Kelly','Chung','KCHUNG','650.505.1876','SH_CLERK',3800.00,NULL,122,50,'2014-03-05 00:00:00'),(189,'Jennifer','Dilly','JDILLY','650.505.2876','SH_CLERK',3600.00,NULL,122,50,'2014-03-05 00:00:00'),(190,'Timothy','Gates','TGATES','650.505.3876','SH_CLERK',2900.00,NULL,122,50,'2014-03-05 00:00:00'),(191,'Randall','Perkins','RPERKINS','650.505.4876','SH_CLERK',2500.00,NULL,122,50,'2014-03-05 00:00:00'),(192,'Sarah','Bell','SBELL','650.501.1876','SH_CLERK',4000.00,NULL,123,50,'2014-03-05 00:00:00'),(193,'Britney','Everett','BEVERETT','650.501.2876','SH_CLERK',3900.00,NULL,123,50,'2014-03-05 00:00:00'),(194,'Samuel','McCain','SMCCAIN','650.501.3876','SH_CLERK',3200.00,NULL,123,50,'2014-03-05 00:00:00'),(195,'Vance','Jones','VJONES','650.501.4876','SH_CLERK',2800.00,NULL,123,50,'2014-03-05 00:00:00'),(196,'Alana','Walsh','AWALSH','650.507.9811','SH_CLERK',3100.00,NULL,124,50,'2014-03-05 00:00:00'),(197,'Kevin','Feeney','KFEENEY','650.507.9822','SH_CLERK',3000.00,NULL,124,50,'2014-03-05 00:00:00'),(198,'Donald','OConnell','DOCONNEL','650.507.9833','SH_CLERK',2600.00,NULL,124,50,'2014-03-05 00:00:00'),(199,'Douglas','Grant','DGRANT','650.507.9844','SH_CLERK',2600.00,NULL,124,50,'2014-03-05 00:00:00'),(200,'Jennifer','Whalen','JWHALEN','515.123.4444','AD_ASST',4400.00,NULL,101,10,'2016-03-03 00:00:00'),(201,'Michael','Hartstein','MHARTSTE','515.123.5555','MK_MAN',13000.00,NULL,100,20,'2016-03-03 00:00:00'),(202,'Pat','Fay','PFAY','603.123.6666','MK_REP',6000.00,NULL,201,20,'2016-03-03 00:00:00'),(203,'Susan','Mavris','SMAVRIS','515.123.7777','HR_REP',6500.00,NULL,101,40,'2016-03-03 00:00:00'),(204,'Hermann','Baer','HBAER','515.123.8888','PR_REP',10000.00,NULL,101,70,'2016-03-03 00:00:00'),(205,'Shelley','Higgins','SHIGGINS','515.123.8080','AC_MGR',12000.00,NULL,101,110,'2016-03-03 00:00:00'),(206,'William','Gietz','WGIETZ','515.123.8181','AC_ACCOUNT',8300.00,NULL,205,110,'2016-03-03 00:00:00');

/*Table structure for table `jobs` */

DROP TABLE IF EXISTS `jobs`;

CREATE TABLE `jobs` (
  `job_id` varchar(10) NOT NULL,
  `job_title` varchar(35) DEFAULT NULL,
  `min_salary` int(6) DEFAULT NULL,
  `max_salary` int(6) DEFAULT NULL,
  PRIMARY KEY (`job_id`)
) ENGINE=InnoDB DEFAULT CHARSET=gb2312;

/*Data for the table `jobs` */

insert  into `jobs`(`job_id`,`job_title`,`min_salary`,`max_salary`) values ('AC_ACCOUNT','Public Accountant',4200,9000),('AC_MGR','Accounting Manager',8200,16000),('AD_ASST','Administration Assistant',3000,6000),('AD_PRES','President',20000,40000),('AD_VP','Administration Vice President',15000,30000),('FI_ACCOUNT','Accountant',4200,9000),('FI_MGR','Finance Manager',8200,16000),('HR_REP','Human Resources Representative',4000,9000),('IT_PROG','Programmer',4000,10000),('MK_MAN','Marketing Manager',9000,15000),('MK_REP','Marketing Representative',4000,9000),('PR_REP','Public Relations Representative',4500,10500),('PU_CLERK','Purchasing Clerk',2500,5500),('PU_MAN','Purchasing Manager',8000,15000),('SA_MAN','Sales Manager',10000,20000),('SA_REP','Sales Representative',6000,12000),('SH_CLERK','Shipping Clerk',2500,5500),('ST_CLERK','Stock Clerk',2000,5000),('ST_MAN','Stock Manager',5500,8500);

/*Table structure for table `locations` */

DROP TABLE IF EXISTS `locations`;

CREATE TABLE `locations` (
  `location_id` int(11) NOT NULL AUTO_INCREMENT,
  `street_address` varchar(40) DEFAULT NULL,
  `postal_code` varchar(12) DEFAULT NULL,
  `city` varchar(30) DEFAULT NULL,
  `state_province` varchar(25) DEFAULT NULL,
  `country_id` varchar(2) DEFAULT NULL,
  PRIMARY KEY (`location_id`)
) ENGINE=InnoDB AUTO_INCREMENT=3201 DEFAULT CHARSET=gb2312;

/*Data for the table `locations` */

insert  into `locations`(`location_id`,`street_address`,`postal_code`,`city`,`state_province`,`country_id`) values (1000,'1297 Via Cola di Rie','00989','Roma',NULL,'IT'),(1100,'93091 Calle della Testa','10934','Venice',NULL,'IT'),(1200,'2017 Shinjuku-ku','1689','Tokyo','Tokyo Prefecture','JP'),(1300,'9450 Kamiya-cho','6823','Hiroshima',NULL,'JP'),(1400,'2014 Jabberwocky Rd','26192','Southlake','Texas','US'),(1500,'2011 Interiors Blvd','99236','South San Francisco','California','US'),(1600,'2007 Zagora St','50090','South Brunswick','New Jersey','US'),(1700,'2004 Charade Rd','98199','Seattle','Washington','US'),(1800,'147 Spadina Ave','M5V 2L7','Toronto','Ontario','CA'),(1900,'6092 Boxwood St','YSW 9T2','Whitehorse','Yukon','CA'),(2000,'40-5-12 Laogianggen','190518','Beijing',NULL,'CN'),(2100,'1298 Vileparle (E)','490231','Bombay','Maharashtra','IN'),(2200,'12-98 Victoria Street','2901','Sydney','New South Wales','AU'),(2300,'198 Clementi North','540198','Singapore',NULL,'SG'),(2400,'8204 Arthur St',NULL,'London',NULL,'UK'),(2500,'Magdalen Centre, The Oxford Science Park','OX9 9ZB','Oxford','Oxford','UK'),(2600,'9702 Chester Road','09629850293','Stretford','Manchester','UK'),(2700,'Schwanthalerstr. 7031','80925','Munich','Bavaria','DE'),(2800,'Rua Frei Caneca 1360 ','01307-002','Sao Paulo','Sao Paulo','BR'),(2900,'20 Rue des Corps-Saints','1730','Geneva','Geneve','CH'),(3000,'Murtenstrasse 921','3095','Bern','BE','CH'),(3100,'Pieter Breughelstraat 837','3029SK','Utrecht','Utrecht','NL'),(3200,'Mariano Escobedo 9991','11932','Mexico City','Distrito Federal,','MX');

/*!40101 SET SQL_MODE=@OLD_SQL_MODE */;
/*!40014 SET FOREIGN_KEY_CHECKS=@OLD_FOREIGN_KEY_CHECKS */;
/*!40014 SET UNIQUE_CHECKS=@OLD_UNIQUE_CHECKS */;
/*!40111 SET SQL_NOTES=@OLD_SQL_NOTES */;


```

- girls文件

```mysql
/*
SQLyog Ultimate v10.00 Beta1
MySQL - 5.7.18-log : Database - girls
*********************************************************************
*/


/*!40101 SET NAMES utf8 */;

/*!40101 SET SQL_MODE=''*/;

/*!40014 SET @OLD_UNIQUE_CHECKS=@@UNIQUE_CHECKS, UNIQUE_CHECKS=0 */;
/*!40014 SET @OLD_FOREIGN_KEY_CHECKS=@@FOREIGN_KEY_CHECKS, FOREIGN_KEY_CHECKS=0 */;
/*!40101 SET @OLD_SQL_MODE=@@SQL_MODE, SQL_MODE='NO_AUTO_VALUE_ON_ZERO' */;
/*!40111 SET @OLD_SQL_NOTES=@@SQL_NOTES, SQL_NOTES=0 */;
CREATE DATABASE /*!32312 IF NOT EXISTS*/`girls` /*!40100 DEFAULT CHARACTER SET utf8 */;

USE `girls`;

/*Table structure for table `admin` */

DROP TABLE IF EXISTS `admin`;

CREATE TABLE `admin` (
  `id` INT(11) NOT NULL AUTO_INCREMENT,
  `username` VARCHAR(10) NOT NULL,
  `password` VARCHAR(10) NOT NULL,
  PRIMARY KEY (`id`)
) ENGINE=INNODB AUTO_INCREMENT=3 DEFAULT CHARSET=utf8;

/*Data for the table `admin` */

INSERT  INTO `admin`(`id`,`username`,`password`) VALUES (1,'john','8888'),(2,'lyt','6666');

/*Table structure for table `beauty` */

DROP TABLE IF EXISTS `beauty`;

CREATE TABLE `beauty` (
  `id` INT(11) NOT NULL AUTO_INCREMENT,
  `name` VARCHAR(50) NOT NULL,
  `sex` CHAR(1) DEFAULT '女',
  `borndate` DATETIME DEFAULT '1987-01-01 00:00:00',
  `phone` VARCHAR(11) NOT NULL,
  `photo` BLOB,
  `boyfriend_id` INT(11) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=INNODB AUTO_INCREMENT=13 DEFAULT CHARSET=utf8;

/*Data for the table `beauty` */

INSERT  INTO `beauty`(`id`,`name`,`sex`,`borndate`,`phone`,`photo`,`boyfriend_id`) VALUES (1,'柳岩','女','1988-02-03 00:00:00','18209876577',NULL,8),(2,'苍老师','女','1987-12-30 00:00:00','18219876577',NULL,9),(3,'Angelababy','女','1989-02-03 00:00:00','18209876567',NULL,3),(4,'热巴','女','1993-02-03 00:00:00','18209876579',NULL,2),(5,'周冬雨','女','1992-02-03 00:00:00','18209179577',NULL,9),(6,'周芷若','女','1988-02-03 00:00:00','18209876577',NULL,1),(7,'岳灵珊','女','1987-12-30 00:00:00','18219876577',NULL,9),(8,'小昭','女','1989-02-03 00:00:00','18209876567',NULL,1),(9,'双儿','女','1993-02-03 00:00:00','18209876579',NULL,9),(10,'王语嫣','女','1992-02-03 00:00:00','18209179577',NULL,4),(11,'夏雪','女','1993-02-03 00:00:00','18209876579',NULL,9),(12,'赵敏','女','1992-02-03 00:00:00','18209179577',NULL,1);

/*Table structure for table `boys` */

DROP TABLE IF EXISTS `boys`;

CREATE TABLE `boys` (
  `id` INT(11) NOT NULL AUTO_INCREMENT,
  `boyName` VARCHAR(20) DEFAULT NULL,
  `userCP` INT(11) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=INNODB AUTO_INCREMENT=5 DEFAULT CHARSET=utf8;

/*Data for the table `boys` */

INSERT  INTO `boys`(`id`,`boyName`,`userCP`) VALUES (1,'张无忌',100),(2,'鹿晗',800),(3,'黄晓明',50),(4,'段誉',300);

/*!40101 SET SQL_MODE=@OLD_SQL_MODE */;
/*!40014 SET FOREIGN_KEY_CHECKS=@OLD_FOREIGN_KEY_CHECKS */;
/*!40014 SET UNIQUE_CHECKS=@OLD_UNIQUE_CHECKS */;
/*!40111 SET SQL_NOTES=@OLD_SQL_NOTES */;

```

- 工资等级

```mysql
CREATE TABLE job_grades
(grade_level VARCHAR(3),
 lowest_sal  INT,
 highest_sal INT);

INSERT INTO job_grades
VALUES ('A', 1000, 2999);

INSERT INTO job_grades
VALUES ('B', 3000, 5999);

INSERT INTO job_grades
VALUES('C', 6000, 9999);

INSERT INTO job_grades
VALUES('D', 10000, 14999);

INSERT INTO job_grades
VALUES('E', 15000, 24999);

INSERT INTO job_grades
VALUES('F', 25000, 40000);

```

- 学生

```mysql
DROP DATABASE IF EXISTS student;
CREATE DATABASE student;
USE student;
CREATE TABLE student(
	studentno VARCHAR(10) NOT NULL PRIMARY KEY,
	studentname VARCHAR(20) NOT NULL,
	loginpwd VARCHAR(8) NOT NULL,
	sex CHAR(1) ,
	majorid INT NOT NULL REFERENCES grade(majorid),
	phone VARCHAR(11),
	email VARCHAR(20) ,
	borndate DATETIME
)DEFAULT CHARSET=utf8;

CREATE TABLE major(
	majorid INT NOT NULL AUTO_INCREMENT PRIMARY KEY,
	majorname VARCHAR(20) NOT  NULL

)DEFAULT CHARSET=utf8;
CREATE TABLE result(
	id INT NOT NULL AUTO_INCREMENT PRIMARY KEY,
	studentno VARCHAR(10) NOT NULL REFERENCES student(studentno),
	score DOUBLE
)DEFAULT CHARSET=utf8;


INSERT INTO major VALUES(NULL,'javaee');
INSERT INTO major VALUES(NULL,'html5');
INSERT INTO major VALUES(NULL,'android');


INSERT INTO student VALUES('S001','张三封','8888','男',1,'13288886666','zhangsanfeng@126.com','1966-9-1');
INSERT INTO student VALUES('S002','殷天正','8888','男',1,'13888881234','yintianzheng@qq.com','1976-9-2');
INSERT INTO student VALUES('S003','周伯通','8888','男',2,'13288886666','zhoubotong@126.com','1986-9-3');
INSERT INTO student VALUES('S004','张翠山','8888','男',1,'13288886666',NULL,'1995-9-4');
INSERT INTO student VALUES('S005','小小张','8888','女',3,'13288885678','xiaozhang@126.com','1990-9-5');

INSERT INTO student VALUES('S006','张无忌','8888','男',2,'13288886666','zhangwuji@126.com','1998-8-9');
INSERT INTO student VALUES('S007','赵敏','0000','女',1,'13288880987','zhaomin@126.com','1998-6-9');
INSERT INTO student VALUES('S008','周芷若','6666','女',1,'13288883456','zhouzhiruo@126.com','1996-7-9');
INSERT INTO student VALUES('S009','殷素素','8888','女',1,'13288886666','yinsusu@163.com','1986-1-9');
INSERT INTO student VALUES('S010','宋远桥','6666','男',3,'1328888890','songyuanqiao@qq.com','1996-2-9');


INSERT INTO student VALUES('S011','杨不悔','6666','女',2,'13288882345',NULL,'1995-9-9');
INSERT INTO student VALUES('S012','杨逍','9999','男',1,'13288885432',NULL,'1976-9-9');
INSERT INTO student VALUES('S013','纪晓芙','9999','女',3,'13288888765',NULL,'1976-9-9');
INSERT INTO student VALUES('S014','谢逊','9999','男',1,'13288882211',NULL,'1946-9-9');
INSERT INTO student VALUES('S015','宋青书','9999','男',3,'13288889900',NULL,'1976-6-8');



INSERT INTO result VALUES(NULL,'s001',100);
INSERT INTO result VALUES(NULL,'s002',90);
INSERT INTO result VALUES(NULL,'s003',80);

INSERT INTO result VALUES(NULL,'s004',70);
INSERT INTO result VALUES(NULL,'s005',60);
INSERT INTO result VALUES(NULL,'s006',50);
INSERT INTO result VALUES(NULL,'s006',40);
INSERT INTO result VALUES(NULL,'s005',95);
INSERT INTO result VALUES(NULL,'s006',88);
```

## 2. DQL语言

### 2.1 基础查询

- 语法：select 查询列表 from 表名

- 特点：

  - 查询列表：可以是表中的字段、常量值、表达式、函数
  - 查询的结果：可以是一个虚拟的表格

- 代码实例

- ```mysql
  USE myemployees;
  
  # 1.查询表中的单个字段
  SELECT last_name FROM employees;
  
  # 2.查询表中的多个字段
  SELECT last_name,salary,email FROM employees;
  
  # 3.查询表中的所有字段
  SELECT * FROM employees;
  
  # 4.查询常量 字符类型和日期类型的常量值必须使用单引号引起来！
  select 常量值;
  如：select 100;
  select 'join';
  
  # 5. 查询函数
  select 函数名(实参列表);
  如：
  SELECT VERSION();
  
  # 6. 查询表达式
  SELECT 100%98;
  
  # 7. 起别名：使用情况：便于理解、查询字段有重名情况下，使用别名区分
  # 方式1：使用AS
  SELECT 100/98 AS 结果;
  # 如：
  SELECT last_name AS 姓, first_name AS 名 FROM employees;
  # 方式2：使用空格
  SELECT last_name 姓, first_name 名 FROM employees;
  
  # 8. 去重
  SELECT distinct 字段名 form 表名;
  # 如：
  #案例:查询员工表中涉及的所有部门编号
  SELECT DISTINCT department_id FROM employees;
  
  # 9. +号的作用
  /*
  java中的+号:
  1.运算符:两个操作数都为数据型
  2.连接符:只要有一个操作数为字符串
  =================================
  mysql中的+号:
  只能作为运算符
  select 100+90; 两个操作数都为数值型,做加法运算
  select '123+90';其中一方为字符型,试图将字符型数值转换为数值型
  		如果转换成功,则继续做加法运算
  select 'john'+90; 如果转换失败,则将字符型数值转换成0
  
  select null+0; 只要其中一方为null,则结果肯定为null.
  */
  #案例:查询员工的名和姓,并显示为姓名=》失败！
  SELECT last_name+first_name AS 姓名 FROM employees; 
  
  #10.【补充】concat函数 
  /*
  功能：拼接字符
  select concat(字符1，字符2，字符3,...);
  */
  SELECT CONCAT('a','b','c') AS 结果 FROM employees;
  SELECT CONCAT(last_name,first_name) AS 姓名 FROM employees;
  
  #11.【补充】ifnull函数
  #功能：判断某字段或表达式是否为null，如果为null 返回指定的值，否则返回原本的值
  SELECT IFNULL(commission_pct,0) FROM employees;
  SELECT IFNULL(NULL,0);
  
  #12.【补充】isnull函数
  #功能：判断某字段或表达式是否为null，如果是，则返回1，否则返回0
  SELECT ISNULL(NULL);
  SELECT ISNULL('NULL');
  SELECT ISNULL(0);
  ```

### 2.2 条件查询

- 语法：select 查询列表 from 表名 where 筛选条件;

- 分类

  - 条件表达式筛选

    - ```mysql
      条件运算符:> < = != <> >= <= <=>安全等于
      ```

  - 逻辑表达式筛选

    - ```mysql
      逻辑运算符:
      	&& || |
      	and or not
      	
      	&& 和 and:两个条件都为true，结果为true，反之为false
      	|| 和 or:只要有一个条件为true，结果为true，反之为false
      	! 或 not:如果连接的条件本身为false，结果为true，反之为false	
      ```

  - 模糊查询筛选

    - ```mysql
      like:一般搭配通配符使用，可以判断字符型或数值型
      通配符：%任意多个字符，_任意单个字符
      like、between and、in、is null
      ```

- 代码实例

- ```mysql
  # 1.按条件表达式筛选
  
  #案例1:查询工资>12000的员工信息
  SELECT * FROM employees WHERE salary>12000;
  #案例2:查询部门编号不等于90号的员工名和部门编号
  SELECT last_name,department_id FROM employees WHERE department_id <> 90;
  
  # 2.按逻辑表达式筛选
  
  #案例1:查询工资z在10000到20000之间的员工名、工资及奖金
  SELECT last_name,salary,commission_pct FROM employees WHERE salary>=10000 AND salary<=20000;
  #案例2:查询部门编号不是在90-110之间,或者工资高于15000的员工信息
  SELECT * FROM employees WHERE department_id <90 OR department_id>110 OR salary>15000;
  
  
  # 3.模糊查询
  /*注意事项：
  1.提高语句简洁度
  2.包含临界值
  3.两个临界值不能调换顺序
  */
  # 3.1 like
  #案例1:查询员工名中包含字符a的员工信息
  SELECT * FROM employees WHERE last_name LIKE '%a%';
  #案例2:查询员工名中第三个字符为b，第五个字符为a的员工名和工资
  SELECT last_name,salary FROM employees WHERE last_name LIKE '__b_a%';
  #案例3:查询员工名种第二个字符为_的员工名
  SELECT last_name FROM employees WHERE last_name LIKE '_\_%';
  
  # 3.2 between and
  #案例1:查询员工编号在100到120之间的员工信息
  SELECT * FROM employees WHERE employee_id>=100 AND employee_id<=120;
  SELECT * FROM employees WHERE employee_id BETWEEN 100 AND 120;
  
  # 3.3 in
  /*
  含义:判断某字段的值是否属于in列表中的某一项
  特点:
   1.使用in提高语句简洁度
   2.in列表的值类型必须一致或兼容
  */
  #案例1:查询员工的工种编号是IT_PROG、AD_VP、AD_PRES中的一个员工名和工种编号
  
  SELECT last_name,job_id FROM employees WHERE job_id='IT_PROG' OR job_id='AD_PRES' OR job_id='AD_VP';
  
  SELECT last_name,job_id FROM employees WHERE job_id IN('IT_PROG','AD_PRES','AD_VP');
  
  # 3.4 is null
  /*
  =或<>不能用于判断null值
  <=>、is null、 或 is not null 可以判断null值
  */
  #案例1:查询没有奖金的员工名和奖金率
  SELECT last_name,commission_pct FROM employees WHERE commission_pct IS NULL;
  SELECT last_name,commission_pct FROM employees WHERE commission_pct <=> NULL;
  SELECT last_name,commission_pct FROM employees WHERE commission_pct IS NOT NULL;
  
  # 补充：安全等于<=>
  
  #案例1:查询没有奖金的员工名和奖金率
  SELECT last_name,commission_pct FROM employees WHERE commission_pct <=> NULL;
  
  #案例2:查询工资为12000的员工信息
  SELECT last_name,commission_pct FROM employees WHERE salary <=> 12000;
  
  #is null 和 <=>的差异：
  #	      普通类型的数值	null值		可读性
  # is null	×			  √		 	 √
  # <=>		√		 	  √			 ×
  ```

### 2.3 排序查询

- 语法：select 查询列表 from 表 【where 筛选条件】order by 子句 DESC或者ASC;

- 特点：

  - ASC代表升序、DESC代表降序、不写默认为升序
  - order by 子句中可以支持单个字段、多个字段、表达式、函数、别名
  - order by 子句一般放在查询语句的最后面，limit子句除外

- 代码示例

- ```mysql
  #案例1:查询员工信息,要求工资从高到低排序
  SELECT * FROM employees ORDER BY salary DESC;
  SELECT * FROM employees ORDER BY salary;
  
  #案例2:查询部门编号是>=90，按入职时间的先后进行排序
  SELECT * FROM employees WHERE department_id>=90 ORDER BY hiredate ASC;
  
  #案例3:按年薪的高低显示员工的信息和年薪【按表达式排序】
  SELECT *,salary*12*(1+IFNULL(commission_pct,0)) 年薪 FROM employees 
  ORDER BY salary*12*(1+IFNULL(commission_pct,0)) DESC; 
  
  #案例4:按年薪的高低显示员工的信息和年薪【按别名排序】
  SELECT *,salary*12*(1+IFNULL(commission_pct,0)) 年薪 FROM employees 
  ORDER BY  年薪 DESC;
  
  #案例5:按姓名的长度显示员工的姓名和工资【按函数排序】
  SELECT LENGTH(last_name) 字节长度,last_name,salary
  FROM employees
  ORDER BY LENGTH(last_name) DESC;
  
  #案例6:查询员工共信息,先要求按工资排序，再按员工编号排序【按多个字段排序】
  SELECT * FROM employees
  ORDER BY salary ASC,employee_id DESC;
  ```

### 2.4 常见函数

- 概念：类似于Java中的方法、函数。将一组逻辑语句封装在方法体中，对外暴露方法名

- 优点：隐藏实现细节、提高代码重用性

- 语法：select 函数名(实参列表) 【from 表】;

- 特点

  - 叫什么==函数名
  - 干什么==函数功能

- 分类

  - 单行函数==》对数据单一作用
    - 字符函数、数学函数、日期函数、其他函数、流程控制函数
  - 分组函数(聚合函数、组函数)==》统计使用，对数据整合分析使用
    - sum 求和、avg 平均值、max 最大值、min最小值count 计算个数
    - sum和avg一般用于处理数值型
    - max、min、count可以处理任何数据类型
    - 以上分组函数都忽略null
    - 都可以搭配distinct使用，实现去重的统计
      -  select sum(distinct 字段) from 表;
      - count(字段)：统计该字段非空值的个数
      -  count(*):统计结果集的行数
    - 和分组函数一同查询的字段，要求是group by后出现的字段

- 代码示例

- ```mysql
  #  字符函数
  # 1.length 获取参数值的字节值
  SELECT LENGTH('subei');
  SELECT LENGTH('鬼谷子qwe');
  
  SHOW VARIABLES LIKE '%char%';
  
  # 2.concat 拼接字符串
  SELECT CONCAT(last_name,'_',first_name) 姓名 FROM employees;
  
  # 3.upper:变大写、lower：变小写
  SELECT UPPER('ton');
  SELECT LOWER('ton');
  
  #示例：将姓变大写，名变小写，然后拼接
  SELECT CONCAT(UPPER(last_name),LOWER(first_name)) 姓名 FROM employees;
  
  
  # 4.substr、substring
  #注意:索引从1开始
  #截取从指定所有处后面的所以字符
  SELECT SUBSTR('吴刚伐桂在天上',4) out_put;
  
  #截取从指定索引处指定字符长度的字符
  SELECT SUBSTR('吴刚伐桂在天上',1,2) out_put;
  
  #案例:姓名中首字符大写,其他字符小写，然后用_拼接,显示出来
  SELECT CONCAT(UPPER(SUBSTR(last_name,1,1)),'_',LOWER(SUBSTR(last_name,2))) out_put FROM employees;
  
  # 5.instr:获取子串第一次出现的索引,找不到返回0
  SELECT INSTR('MySQL技术进阶','技术') AS out_put;
  
  # 6.trim:去前后空格
  
  SELECT LENGTH(TRIM('	霍山	')) AS out_put;
  # 从'++++李刚+++刘邦+++'去除前后的+
  SELECT TRIM('+' FROM '++++李刚+++刘邦+++') AS out_put;
  
  # 7.lpad:用指定的字符实现左填充指定长度
  SELECT LPAD('梅林',8,'+') AS out_put;
  
  # 8.rpad:用指定的字符实现右填充指定长度
  SELECT RPAD('梅林',5,'&') AS out_put;
  
  # 9.replace:替换
  SELECT REPLACE('莉莉伊万斯的青梅竹马是詹姆','詹姆','斯内普') AS out_put;
  
  # =============
  # 数学函数
  # 1.round:四舍五入
  SELECT ROUND(1.45);
  SELECT ROUND(1.567,2);
  
  # 2.ceil:向上取整,返回>=该参数的最小整数
  SELECT CEIL(1.005);
  SELECT CEIL(-1.002);
  
  # 3.floor:向下取整,返回<=该参数的最大整数
  SELECT FLOOR(-9.99);
  
  # 4.truncate:截断
  SELECT TRUNCATE(1.65,1);
  
  # 5.mod:取余
  SELECT MOD(10,3);
  
  # 6.rand:获取随机数，返回0-1之间的小数
  SELECT RAND();
  
  
  # =============
  # 日期函数
  # 1.now:返回当前系统时间+日期
  SELECT NOW();
  
  # 2.year:返回年
  SELECT YEAR(NOW());
  SELECT YEAR(hiredate) 年 FROM employees;
  
  # 3.month:返回月
  #MONTHNAME:以英文形式返回月
  SELECT MONTH(NOW());
  SELECT MONTHNAME(NOW());
  
  # 4.day:返回日
  #DATEDIFF:返回两个日期相差的天数
  SELECT DAY(NOW());
  SELECT DATEDIFF('2020/06/30','2020/06/21');
  
  # 5.str_to_date:将字符通过指定格式转换成日期
  SELECT STR_TO_DATE('2020-5-13','%Y-%c-%d') AS out_put;
  
  # 6.date_format:将日期转换成字符
  SELECT DATE_FORMAT('2020/6/6','%Y年%m月%d日') AS out_put;
  SELECT DATE_FORMAT(NOW(),'%Y年%m月%d日') AS out_put;
  
  # 7.curdate:返回当前日期
  SELECT CURDATE();
  
  # 8.curtime:返回当前时间
  SELECT CURTIME();
  
  # =============
  # 其他函数
  # 1.version 当前数据库服务器的版本
  SELECT VERSION();
  
  # 2.database 当前打开的数据库
  SELECT DATABASE();
  
  # 3.user当前用户
  SELECT USER();
  
  # 4.password('字符')：返回该字符的密码形式
  SELECT PASSWORD('a');
  
  # 5.md5('字符'):返回该字符的md5加密形式
  SELECT MD5('a');
  
  
  # =============
  # 流程控制函数
  
  # 1.if函数: if else效果
  SELECT IF(10<5,'大','小');
  
  SELECT last_name,commission_pct,IF(commission_pct IS NULL,'没奖金！！！','有奖金!!!') 备注 FROM employees;
  
  # 2.case函数
  #使用一:switch case 的效果
  /*
  java中
  switch(变量或表达式){
  	case 常量1:语句1;break;
  	...
  	default:语句n;break;
  }
  
  mysql中
  
  case 要判断的变量或表达式
  when 常量1 then 要显示的值1或语句1
  when 常量2 then 要显示的值2或语句2
  ...
  else 要显示的值n或语句n
  end
  
  #案例:查询员工的工资,要求:
  
  部门号=30,显示的工资为1.1倍
  部门号=40,显示的工资为1.2倍
  部门号=50,显示的工资为1.3倍
  其他部门,显示的工资为原工资
  
  */
  
  SELECT salary 原始工资,department_id,
  CASE department_id
  WHEN 30 THEN salary*1.1
  WHEN 40 THEN salary*1.2
  WHEN 50 THEN salary*1.3
  ELSE salary
  END AS 新工资
  FROM employees;
  
  # 3.case函数的使用二:类似于多重if
  /*
  java中:
  if(条件1){
  	语句1;
  }else if(条件2){
  	语句2;
  }
  ...
  else{
  	语句n;
  }	
  
  mysql中:
  case 
  when 条件1 then 要显示的值1或语句1
  when 条件2 then 要显示的值2或语句2
  ...
  else 要显示的值n或语句n
  end
  */
  
  #案例:查询员工的工资的情况
  /*
  如果工资>20000，显示A级别
  如果工资>15000，显示B级别
  如果工资>10000，显示c级别
  否则，显示D级别
  */
  SELECT salary,
  CASE
  WHEN salary>20000 THEN 'A'
  WHEN salary>15000 THEN 'B'
  WHEN salary>10000 THEN 'C'
  ELSE 'D'
  END AS 工资等级
  FROM employees;
  ```

- ```mysql
  # =============
  # 分组函数
  # 1.简单使用
  SELECT SUM(salary) FROM employees;
  SELECT AVG(salary) FROM employees;
  SELECT MAX(salary) FROM employees;
  SELECT MIN(salary) FROM employees;
  SELECT COUNT(salary) FROM employees;
  
  SELECT SUM(salary) 和,ROUND(AVG(salary),2) 平均,MAX(salary) 最高,MIN(salary) 最低,COUNT(salary) 个数
  FROM employees;
  
  # 2.参数支持哪些数据类型
  SELECT SUM(last_name),AVG(last_name) FROM employees;
  SELECT SUM(hiredate),AVG(hiredate) FROM employees;
  
  SELECT MAX(last_name),MIN(last_name) FROM employees;
  SELECT MAX(hiredate),MIN(hiredate) FROM employees;
  
  SELECT COUNT(commission_pct) FROM employees;
  SELECT COUNT(last_name) FROM employees;
  
  # 3.是否忽略null
  
  SELECT SUM(commission_pct),AVG(commission_pct) FROM employees;
  
  SELECT commission_pct FROM employees;
  
  SELECT SUM(commission_pct),AVG(commission_pct),SUM(commission_pct)/35,AVG(commission_pct)/107 FROM employees;
  
  SELECT MAX(commission_pct),MIN(commission_pct) FROM employees;
  
  SELECT COUNT(commission_pct) FROM employees;
  
  # 4.和distinct搭配
  
  SELECT SUM(DISTINCT salary),SUM(salary) FROM employees;
  
  SELECT COUNT(DISTINCT salary),COUNT(salary) FROM employees;
  
  # 5.count函数详解
  
  SELECT COUNT(salary) FROM employees;
  SELECT COUNT(*) FROM employees;
  SELECT COUNT(1) FROM employees;
  /*
  效率上：
  MyISAM存储引擎，count(*)最高
  InnoDB存储引擎，count(*)和count(1)效率>count(字段)
  */
  # 6.和分组函数一同查询的字段有限制
  
  SELECT AVG(salary),employee_id FROM employees;
  
  ```

### 2.5 分组查询

- 语法：

  - ```mysql
    select 分组函数,分组后的字段
    from 表
    【where 筛选条件】
    group by 分组的字段
    【having 分组后的筛选】
    【order by 排序列表】
    ```

  - 注意:查询列表必须特殊,要求是分组函数和group by后出现的字段

- 特点

  - 分组查询中的筛选条件分为两类

    - ```mysql
      			使用关键字	筛选的表	位置
      分组前筛选	where		原始表		group by的前面
      分组后筛选	having		分组后的结果	group by的后面
      1.分组函数做条件肯定是放在having子句中
      2.能用分组前筛选的，就优先考虑使用分组前筛选
      ```

  - group by子句支持单个字段分组，多个字段分组(多个字段之间用逗号隔开没有顺序要求),表达式或函数(使用较少)

  - 也可以添加排序(排序放在整个分组查询的最后)

- 代码示例：

- ```mysql
  #引入:查询每个部门的平均工资
  SELECT AVG(salary) FROM employees;
  
  #案例1:查询每个工种的最高工资
  SELECT MAX(salary),job_id FROM employees 
  GROUP BY job_id;
  
  
  #案例2:查询每个位置上的部门个数
  SELECT COUNT(*),location_id
  FROM departments
  GROUP BY location_id;
  
  # 1. 添加筛选条件
  #案例1:查询邮箱中包含a字符的，每个部门的平均工资
  SELECT AVG(salary),department_id FROM employees
  WHERE email LIKE '%a%' GROUP BY department_id;
  
  #案例2:查询有奖金的每个领导手下员工的最高工资
  SELECT MAX(salary),manager_id FROM employees
  WHERE commission_pct IS NOT NULL
  GROUP BY manager_id;
  
  # 2. 添加复杂的筛选条件
  #案例1:查询哪个部门的员工个数>2
  #1.查询每个部门的员工个数
  SELECT COUNT(*),department_id FROM employees
  GROUP BY department_id;
  #2.根据1的结果进行筛选，查询哪个部门的员工个数大于2
  SELECT COUNT(*),department_id FROM employees
  GROUP BY department_id HAVING COUNT(*)>2;
  
  
  #案例2:查询每个工种有奖金的员工的最高工资>12000的工种编号和最高工资 
  #1.查询每个工种有奖金的员工的最高工资 
  SELECT MAX(salary),job_id FROM employees 
  WHERE commission_pct IS NOT NULL GROUP BY job_id; 
  
  #2.根据结果继续筛选，最高工资>12000 
  
  SELECT MAX(salary), job_id FROM employees 
  WHERE commission_pct IS NOT NULL GROUP BY job_id 
  HAVING MAX(salary)>12000; 
  
  # 3.按表达式或函数分组
  #案例:按员工姓名的长度分组,查询每一组的员工个数,筛选员工个数>5
  
  #1.查询每个长度的员工个数 
  SELECT COUNT(*),LENGTH(last_name) len_name 
  FROM employees GROUP BY LENGTH(last_name); 
  
  #2.添加筛选条件
  SELECT COUNT(*) c,LENGTH(last_name) len_name 
  FROM employees GROUP BY len_name HAVING c>5;
  
  # 4.按多个字段查询
  #案例:查询每个部门每个工种的员工的平均工资
  
  SELECT AVG(salary),department_id,job_id
  FROM employees GROUP BY department_id,job_id;
  
  # 5.添加排序
  #案例:查询每个部门每个工种的员工的平均工资,按平均工资的高低查询
  
  SELECT AVG(salary),department_id,job_id
  FROM employees GROUP BY department_id,job_id
  ORDER BY AVG(salary) DESC;
  
  ```


### 2.6 连接查询

- 含义：连接查询又称多表查询，当查询数据来自多个表时，就会用到连接查询

- 笛卡尔乘积

  - 表1 ： m行，表2：n行，结果：m*n行
  - 发生原因：没有有效的连接条件
  - 如何避免：添加有效的连接条件

- 分类：

  - 按年代分类：
    - sql92标准：仅仅支持内连接
      - 内连接：根据某种条件，取多个表的交集！
    - sql99标准：支持：内连接+外连接+交叉连接
  - 按功能分类：
    - 内连接
    - 等值连接
    - 非等值连接
    - 自连接
    - 外连接：
      - 左外连接
      - 右外连接
      - 全外连接
    - 交叉连接

#### 2.6.1 sql92标准实例

- ```mysql
  # 1.等值连接
  /*
  1.多表等值连接的结果为多表的交集部分
  2.n表连接，至少需要n-1个连接条件
  3.多表的顺序没有要求
  4.一般需要为表起别名
  5.可以搭配前面的所有子句使用，如：排序、分组、筛选
  */
  #案例1：查询员工名和对应的部门名
  select last_name,department_name
  from employees,departments
  where employees.'department_id'=departments.'department_id';
  
  # 2.为表起别名
  /*
  1.提高语句的简洁度
  2.区分多个重名的字段
  注意：如果表起了别名，则查询的字段就不能使用原来的表名去限定
  */
  # 查询员工名、工种号、工种名
  SELECT e.last_name,e.job_id,j.job_title
  FROM employees  e,jobs j
  WHERE e.`job_id`=j.`job_id`;
  
  # 3.验证两个表的顺序是否可以调换
  SELECT e.last_name,e.job_id,j.job_title
  FROM jobs j,employees e
  WHERE e.`job_id`=j.`job_id`;
  
  # 4.添加筛选条件
  #案例：查询有奖金的员工名、部门名
  select last_name,department_name,commission_pct
  from employees e,departments d
  where e.'department_od' = d.'department_id' And e.'commission_pct' is not NULL;
  
  
  # 查询城市名中第二个字符为o的部门名和城市名
  select department_name,city
  from departments d,locations l
  where d.'location_id' = l.'location_id'
  AND city like "_o%";
  
  # 5.可以加分组
  #案例：查询每个城市的部门个数
  select count(*) 个数,city
  from departments d,locations l
  where d.'loction_id' = l.'location_id'
  group by city;
  
  # 6.可以加排序
  #案例：查询每个工种的工种名和员工的个数，并且按员工个数降序
  select job_title,count(*)
  from employees e,jobs j
  where e.'job_id' = j.'job_id'
  group by job_title
  order by count(*) desc;
  
  # 7.可以实现三表连接？
  #案例：查询员工名、部门名和所在的城市
  select last_name,department_name,city
  from employees e,departments d,locations l
  where e.'department_id' = d.'department_id' 
  AND d.'location_id' = l.'location_id'
  And city like 's%'
  order by department_name desc;
  
  
  # 2.非等值连接
  #案例1：查询员工的工资和工资级别
  select salary,grade_level
  from employees e,job_grades g
  where salary between g.'lowest_sal' and g.'highest_sal'
  and g.'grade_level'='A';
  
  /*
  select salary,employee_id from employees;
  select * from job_grades;
  CREATE TABLE job_grades
  (grade_level VARCHAR(3),
   lowest_sal  int,
   highest_sal int);
  
  INSERT INTO job_grades
  VALUES ('A', 1000, 2999);
  
  INSERT INTO job_grades
  VALUES ('B', 3000, 5999);
  
  INSERT INTO job_grades
  VALUES('C', 6000, 9999);
  
  INSERT INTO job_grades
  VALUES('D', 10000, 14999);
  
  INSERT INTO job_grades
  VALUES('E', 15000, 24999);
  
  INSERT INTO job_grades
  VALUES('F', 25000, 40000);
  
  */
  
  # 3.自连接
  #案例：查询 员工名和上级的名称
  select e.employee_id, e.last_name,m.employee_id,m.last_name
  from employees e, employees m
  where e.'manager_id'=m.'employee_id';
  ```


#### 2.6.2 sql99标准实例

- 语法

  - ```mysql
    select 查询列表
    from 表1 别名 【连接类型】 join 表2 别名 on 连接条件
    【where 筛选条件】
    【group by 分组】
    【having 筛选条件】
    【order by 排序列表】
    ```

- 分类：

  - 内连接：inner
  - 外连接：
    - 左外：left【outer】
    - 右外：right 【outer】
    - 全外：full 【outer】
  - 交叉连接：cross

- ```mysql
  # 一、内连接
  /*
语法：
  select 查询列表
  from 表1 别名
  inner join 表2 别名
  on 连接条件;
  
  分类：
  	等值
  	非等值
  	自连接
  特点：
  	添加排序、分组、筛选
  	inner可以省略
  	筛选条件放在where后面，连接条件放在on后面，提高分离性，便于阅读
  	inner join连接和sql92语法中的等值连接效果是一样的，都是查询多表的交集
  */
  
  # 1.等值连接
  # 案例1.查询员工名、部门名
  select last_name,department_name 
  from departments d inner join employees e on e.'department_id' = d.'department_id';
  
  # 案例2.查询名字中包含e的员工名和工种名（添加筛选）
  SELECT last_name,job_title 
  FROM employees e INNER JOIN jobs j ON e.`job_id`=  j.`job_id`
  WHERE e.`last_name` LIKE '%e%';
  
  
  # 案例3.查询部门个数>3的城市名和部门个数，（添加分组+筛选）
  
  # 1.查询每个城市的部门个数+筛选条件
  select city,count(*) 部门个数
  from departments d inner join locations l on d.'location_id'=l.'location_id'
  group by city
  having count(*)>3;
  
  # 案例4.查询哪个部门的员工个数>3的部门名和员工个数，并按个数降序（添加排序）
  
  # 1. 查询员工个数
  select count(*),department_name
  from employees e inner join departments d on e.'department_id'=d.'department_id'
  group by department_name;
  # 2. 在1的结果上筛选员工个数>3的记录，并且排序
  select count(*) 个数,department_name
  from employees e inner join departments d on e.'department)id'=d.'department_id'
  group by department_name
  having count(*)>3
  order by count(*) desc;
  
  #案例5.查询员工名、部门名、工种名，并按部门名降序（添加三表连接）
  select last_name,department_name,job_title
  from employees e inner join departments d on e.'department_id'=d.'department_id' inner join jobs j on e.'job_id' = j.'job_id'
  order by department_name desc;
  
  # 2.非等值连接
  
  #查询员工的工资级别
  select salary,grade_level
  from employees e join job_grades g on e.'salary' between g.'lowest_sal' and g.'highest_sal';
  
  # 查询工资级别的个数>20的个数，并且按工资级别降序
  select count(*),grade_level
  from employees e join g on e.'salary' between g.'lowest_sal' and g.'highest_sal'
  group by grade_level
  having count(*)>20
  order by grade_level desc;
  
  # 3.自连接
  
  #查询员工的名字、上级的名字
  select e.last_name,m.last_name
  from employees e join employees m on e.'manager_id' = m.'employee_id';
  
  #查询姓名中包含字符k的员工的名字、上级的名字
  SELECT e.last_name,m.last_name
  FROM employees e
  JOIN employees m
  ON e.`manager_id`= m.`employee_id`
  WHERE e.`last_name` LIKE '%k%';
  
  # 二、外连接
  /*
  应用场景：用于查询一个表中有，另一个表没有的记录
   
  特点：
  1、外连接的查询结果为主表中的所有记录
  	如果从表中有和它匹配的，则显示匹配的值
  	如果从表中没有和它匹配的，则显示null
  	外连接查询结果=内连接结果+主表中有而从表没有的记录
  2、左外连接，left join左边的是主表
     右外连接，right join右边的是主表
  3、左外和右外交换两个表的顺序，可以实现同样的效果 
  4、全外连接=内连接的结果+表1中有但表2没有的+表2中有但表1没有的
  */
  
  #查询男朋友 不在男神表的的女神名
  select * from beauty;
  select * from boys;
  
  # 左外连接
  select b.*,bo.* 
  from boys bo left outer join beatuty b on b.'boyfriend_id'=bo.'id'
  where b.'id' is NULL;
  
  # 案例1：查询哪个部门没有员工
  # 使用左外
  select d.*,e.employee_id 
  from departments d left outer join employees e on d.'department_id' = e.'department_id'
  where e.'employee_id' is NULL;
  
  # 使用右外
  select d.*,e.employee_id
  from employees e right outer join departments d on d.'department_id'=e.'department_id'
  where e.'employee_id' is NULL;
  
  # 全外
  
  USE girls;
  SELECT b.*,bo.* FROM beauty b
  FULL OUTER JOIN boys bo ON b.`boyfriend_id` = bo.id;
  
  #交叉连接
  SELECT b.*,bo.* FROM beauty b CROSS JOIN boys bo;
  ```
  
- sql92标准和sql99标准的对比

  - 功能：sql99支持的较多
  - 可读性：sql99实现连接条件和筛选条件的分离，可读性较高

#### 2.6.3 子查询

- 含义：出现在其他语句中的select语句，被称为子查询或者内查询，外部的查询语句被称为主查询或外查询

- 分类：

  - 按结构集的行列数不同：
    - 标量子查询：结果集只有一行一列
    - 列子查询：结果集只有一列多行
    - 行子查询：结果集只有一行多列
    - 表子查询：结果集一般为多行多列
  - 按子查询出现的位置：
    - select后面：仅支持标量子查询
    - from后面：支持表子查询
    - where、having后面
      - 标量子查询(单行)
      - 列子查询(多行)
      - 行子查询
    - exists 后面：表子查询

- ```mysql
  - # 一、where或having后面
  
    /*
    1、标量子查询（单行单列子查询）
    2、列子查询（一列多行子查询）
    3、行子查询（一行多列）
  
    特点：
    	子查询放在小括号内
    	子查询一般放在条件的右侧
    	标量子查询，一般搭配着单行操作符使用
    		> < >= <= = <>
    	列子查询，一般搭配着多行操作符使用
    		in、any/some、all
    	子查询的执行优先于主查询执行，主查询的条件用到了子查询的结果
    */
  
  
    #1.标量子查询
  
    # 案例1：谁的工资比 Abel 高?
  
    # 先查询Abel的工资
  
    select salary
    from employees 
    where last_name='Abel';
  
    # 再查询谁比Abel的高
  
    select *
    from employees
    where salary>(
    	select salary 
        from employees
        where last_name='Able'
    );
  
    #案例2：返回job_id与141号员工相同，salary比143号员工多的员工 姓名，job_id 和工资
  
    # 2.1查询141号员工的job_id
  
    select job_id 
    from employees e
    where employees_id = 141;
  
    # 2.2 查询143号员工的salary
  
    select salary 
    from employees
    where employees_id = 143;
  
    # 2.3 组合起来
  
    select last_name,job_id,salary
    from employees
    where job_id = (
    		select job_id
        	from employees
        	where employee_id = 141
    	)
    and salary > (
    	select salary
        from employees
        where employee_id = 143
    );
  
  
    #案例3：返回公司工资最少的员工的last_name,job_id和salary
  
    # 3.1查询公司的最低工资
  
    select min(salary) from employees;
  
    # 3.2 组合起来
  
    select last_name,job_id,salary
    from employees
    where salary=(
    	select min(salary)
        from employees
    );
  
  
    #案例4：查询最低工资大于50号部门最低工资的部门id和其最低工资
  
    # 4.1 查询50号部门的最低工资
  
    select min(salary)
    from employees
    where department_id = 50;
  
    # 4.2 查询每个部门的最低工资
  
    select min(salary),department_id
    from employees
    group by department_id;
  
    # 4.3 组合筛选
  
    select min(salary),department_id
    from employees
    group by department_id
    having min(salary)>(
    	select min(salary)
        from employees
        where department_id = 50
    );
  
    # 2.列子查询（一列多行子查询）
  
    #案例1：返回location_id是1400或1700的部门中的所有员工姓名
  
    # 1.1查询location_id是1400或1700的部门编号
  
    select distinct department_id
    from departments
    where location_id in (1400,1700)
  
    # 1.2 查询员工姓名，要求部门号是1.1列表中的某一个
  
    select last_name
    from employees
    where department_id <> ALL(
    	select distinct department_id
        from departments
        where location_id in(1400,1700)
    );
  
  
    # 案例2：返回其它工种中比job_id为‘IT_PROG’工种任一工资低的员工的员工号、姓名、job_id 以及salary
  
  # 2.1查询job_id为‘IT_PROG’部门任一工资
  
    selecr distinct salary 
    from employees
    where job_id = "IT_RPOH";
  
    # 查询员工号、姓名、job_id 以及salary，salary<(2.1)的任意一个
  
    select last_name,employee_id,job_id,salary
    from employees
    where salary < ANY(
    	select distinct salary
        from employees
        where job_id = 'IT_PROG'
    ) AND job_id <> 'IT_PROG';
  
    # 等同于
  
    select last_name,employee_id,job_id,salary
    from employees
    where salary<(
    	select max(salary)
        from employees
        where job_id = 'IT_PROG'
    ) AND job_id <> 'IT_PROG';
  
    #案例3：返回其它部门中比job_id为‘IT_PROG’部门所有工资都低的员工   的员工号、姓名、job_id 以及salary
  
    SELECT last_name,employee_id,job_id,salary
    FROM employees
    WHERE salary<ALL(
    	SELECT DISTINCT salary
    	FROM employees
    	WHERE job_id = 'IT_PROG'
  
    ) AND job_id<>'IT_PROG';
  
    #等同于
  
    SELECT last_name,employee_id,job_id,salary
    FROM employees
    WHERE salary<(
    	SELECT MIN( salary)
    	FROM employees
    	WHERE job_id = 'IT_PROG'
  
    ) AND job_id<>'IT_PROG';
  
    # 3.行子查询（一行多列）
  
    # 案例：查询员工编号最小并且工资最高的员工信息
  
    # 查询最小的员工编号
  
    SELECT MIN(employee_id) FROM employees;
  
    # 查询最高工资
  
    SELECT MAX(salary) FROM employees;
  
    #③查询员工信息
  
    SELECT * FROM employees
    WHERE employee_id=(
    	SELECT MIN(employee_id)
    	FROM employees
    )AND salary=(
    	SELECT MAX(salary)
    	FROM employees
    );
  
    # 二、select后面
  
    /*
    仅仅支持标量子查询
    */
  
    #案例1：查询每个部门的员工个数
  
    SELECT d.*,(
    	SELECT COUNT(*)
    	FROM employees e
    	WHERE e.department_id = d.`department_id`
     ) 个数
     FROM departments d;
  
    #案例2：查询员工号=102的部门名 
  
    SELECT (
    	SELECT department_name,e.department_id
    	FROM departments d
    	INNER JOIN employees e
    	ON d.department_id=e.department_id
    	WHERE e.employee_id=102
    	
    ) 部门名;
  
  
    # 三、from后面
  
    /*
    将子查询结果充当一张表，要求必须起别名
    */
  
    # 案例1：查询每个部门的平均工资的工资等级
  
    #1.1 查询每个部门的平均工资
  
    SELECT AVG(salary),department_id
    FROM employees GROUP BY department_id;
  
    SELECT * FROM job_grades;
  
    #组合连接1.1的结果集和job_grades表，筛选条件平均工资 between lowest_sal and highest_sal
  
    SELECT  ag_dep.*,g.`grade_level`
    FROM (
    	SELECT AVG(salary) ag,department_id
    	FROM employees
    	GROUP BY department_id
    ) ag_dep
    INNER JOIN job_grades g
    ON ag_dep.ag BETWEEN lowest_sal AND highest_sal;
  
    # 四、exists后面（相关子查询）
  
    /*
    语法：
    exists(完整的查询语句)
    结果：
    1或0
    */
    SELECT EXISTS(SELECT employee_id FROM employees WHERE salary=300000);
  
    #案例1：查询有员工的部门名
  
    #in
  
    SELECT department_name
    FROM departments d
    WHERE d.`department_id` IN(
    	SELECT department_id
    	FROM employees
    );
  
    #exists
  
    SELECT department_name
    FROM departments d
    WHERE EXISTS(
    	SELECT *
    	FROM employees e
    	WHERE d.`department_id`=e.`department_id`
    );
  
    #案例2：查询没有女朋友的男神信息
  
  #in
  
    SELECT bo.*
    FROM boys bo
    WHERE bo.id NOT IN(
    	SELECT boyfriend_id
    	FROM beauty
    );
  
    #exists
  
    SELECT bo.*
    FROM boys bo
    WHERE NOT EXISTS(
    	SELECT boyfriend_id
    	FROM beauty b
    	WHERE bo.`id`=b.`boyfriend_id`
    );
  ```

### 2.7 分页查询

- 应用场景：当要显示的数据量太大，需要分页提交sql请求

- 语法：

  - ```mysql
    select 查询列表
    from 表 【join type join 表2 on 连接条件】
    where 筛选条件
    group by 分组字段
    having 分组后的筛选
    order by 排序的字段
    limit 【offset，】 size;
    注意
    	offset要显示条目的起始索引（起始索引从0开始）
    	size 要显示的条目个数
    	显示的数据行号范围：offset到offset+size
    ```

- 使用：

  - ```mysql
    1 limit语句放在查询语句的最后
    2 公式
    	要显示的页数 page，每页的条目数size
    	
    select 查询列表 from 表
    limit (page-1)*size,size;
    	
    size=10
    page  
    1	0
    2  	10
    3	20
    
    ```

- 代码示例

- ```mysql
  #案例1：查询前五条员工信息
  SELECT * FROM  employees LIMIT 0,5;
  SELECT * FROM  employees LIMIT 5;
  
  #案例2：查询第11条——第25条
  SELECT * FROM employees LIMIT 10,15;
  
  #案例3：有奖金的员工信息，并且工资较高的前10名显示出来
  SELECT * FROM employees 
  WHERE commission_pct IS NOT NULL 
  ORDER BY salary DESC LIMIT 10 ;
  ```

### 2.8 联合查询

- 含义：union：将多条查询语句的结果合并成一个结果

- 语法：

  - ```mysql
    查询语句1
    union 【all】
    查询语句2
    union 【all】
    ...
    ```

- 意义：

  - 可以将一条复杂的查询语句拆分成多条语句
  - 适用于查询多个表，并且查询的列基本一致

- 特点：

  - 要求多条查询语句的查询列数是一致的
  - 要求多条查询语句查询的每一列的类型和顺序最好一致
  - union关键字默认去重，使用union all可以包含重复项

- 代码示例

- ```mysql
  #引入的案例：查询部门编号>90或邮箱包含a的员工信息
  
  SELECT * FROM employees WHERE email LIKE '%a%' OR department_id>90;
  
  SELECT * FROM employees  WHERE email LIKE '%a%'
  UNION
  SELECT * FROM employees  WHERE department_id>90;
  ```

## 3. DML语言

- Data Manipulation Languange：数据操纵语言。用于改变数据库中的数据表的数据，主要包括插入、修改、删除

### 3.1 插入语句

- 方法一：经典的插入语句

- 语法：

  - ```mysql
    insert into 表名(字段名,...) values (值,...);
    ```

- 特点：

  - 要求值的类型和字段的类型一致或者兼容
  - 字段的个数和顺序不一定要与原始表中的字段个数和顺序一致，但是必须保证值和字段要一一对应
  - 如果表中有可以为null的字段，则可以通过以下两种方式插入null值
    - 法一：字段和值都省略
    - 法二：字段写上，值用null
  - 字段和值的个数必须一致
  - 字段名可以省略，此时默认所有列

- 代码实例

- ```mysql
  SELECT * FROM beauty;
  #1.插入的值的类型要与列的类型一致或兼容
  INSERT INTO beauty(id,NAME,sex,borndate,phone,photo,boyfriend_id)
  VALUES(13,'唐艺昕','女','1990-4-23','1898888888',NULL,2);
  
  #2.不可以为null的列必须插入值。可以为null的列如何插入值？
      #方法一：
      INSERT INTO beauty(id,NAME,sex,borndate,phone,photo,boyfriend_id)
      VALUES(13,'唐艺昕','女','1990-4-23','1898888888',NULL,2);
  
      #方法二：
      INSERT INTO beauty(id,NAME,sex,phone)
      VALUES(15,'娜扎','女','1388888888');
  
  #3.列的顺序是否可以调换
  INSERT INTO beauty(NAME,sex,id,phone)
  VALUES('蒋欣','女',16,'110');
  
  #4.列数和值的个数必须一致
  
  INSERT INTO beauty(NAME,sex,id,phone)
  VALUES('关晓彤','女',17,'110');
  
  #5.可以省略列名，默认所有列，而且列的顺序和表中列的顺序一致
  
  INSERT INTO beauty
  VALUES(18,'张飞','男',NULL,'119',NULL,NULL);
  
  ```

- 方法二：使用set key=value的写法

- 语法：

  - ```mysql
    insert into 表名 
    set 列名=值, 列名=值...;
    ```

- 代码示例

- ```mysql
  insert into beauty set id=16,name='刘涛',phone='999';
  ```

- 两种方式的区别

  - 方法一：
    - 支持一次插入多行
    - 支持子查询

- 代码示例

- ```mysql
  # 1.方式一支持一次插入多行，语法如下：
  insert into 表名【(字段名,..)】 values(值，..),(值，...),...;
  # 2.方式一支持子查询，语法如下：
      insert into 表名 查询语句;
      
  #1、方式一支持插入多行,方式二不支持
  INSERT INTO beauty
  VALUES(23,'唐艺昕1','女','1990-4-23','1898888888',NULL,2)
  ,(24,'唐艺昕2','女','1990-4-23','1898888888',NULL,2)
  ,(25,'唐艺昕3','女','1990-4-23','1898888888',NULL,2);
  
  #2、方式一支持子查询，方式二不支持
  INSERT INTO beauty(id,NAME,phone) SELECT 26,'宋茜','11809866';
  
  INSERT INTO beauty(id,NAME,phone) 
  SELECT id,boyname,'1234567' FROM boys WHERE id<3;       
  ```

### 3.2 修改语句

- 修改单张数据表的数据(记录)

- 语法：

  - ```mysql
    update 表名
    set
    列名=值,
    列名=值,
    ...
    where 筛选条件;
    ```

- 代码实例

- ```mysql
  #1.修改单表的记录
  #案例1：修改beauty表中姓唐的女神的电话为13899888899
  UPDATE beauty SET phone = '13899888899'
  WHERE NAME LIKE '唐%';
  
  #案例2：修改boys表中id为2的名称为张飞，魅力值 10
  UPDATE boys SET boyname='张飞',usercp=10
  WHERE id=2;
  
  ```

- 修改多张数据库表的数据(记录)

- 语法：

  - ```mysql
    # sql92语法：
    update 表1 别名,表2 别名...
    set 列=值,...
    where 连接条件 and 筛选条件;
    ```

  - ```mysql
    # sql99语法：
    update 表1 别名 inner|left|right join 表2 别名 
    on 连接条件
    set 列=值,...
    where 筛选条件;
    ```

- 代码示例

- ```mysql
  #2.修改多表的记录
  #案例 1：修改张无忌的女朋友的手机号为114
  UPDATE boys bo
  INNER JOIN beauty b ON bo.`id`=b.`boyfriend_id`
  SET b.`phone`='114selec',bo.`userCP`=1000
  WHERE bo.`boyName`='张无忌';
  
  #案例2：修改没有男朋友的女神的男朋友编号都为2号
  UPDATE boys bo
  RIGHT JOIN beauty b ON bo.`id`=b.`boyfriend_id`
  SET b.`boyfriend_id`=2 WHERE bo.`id` IS NULL;
  
  ```

### 3.3 删除语句

- 方法一：直接使用delete

- 语法：

  - ```mysql
    # 1、单表的删除【★】
    delete from 表名 where 筛选条件
    
    # 2、多表的删除【补充】
    # sql92语法：
    delete 表1的别名,表2的别名
    from 表1 别名,表2 别名
    where 连接条件
    and 筛选条件;
    
    # sql99语法：
    delete 表1的别名,表2的别名
    from 表1 别名
    inner|left|right join 表2 别名 on 连接条件
    where 筛选条件;
    
    ```

- 代码示例

- ```mysql
  # 1.单表的删除
  #案例：删除手机号以9结尾的女神信息
  DELETE FROM beauty WHERE phone LIKE '%9';
  SELECT * FROM beauty;
  
  # 2.多表的删除
  #案例：删除张无忌的女朋友的信息=>多表中删除单表的数据
  DELETE b
  FROM beauty b
  INNER JOIN boys bo ON b.`boyfriend_id` = bo.`id`
  WHERE bo.`boyName`='张无忌';
  
  #案例：删除黄晓明的信息以及他女朋友的信息
  DELETE b,bo
  FROM beauty b
  INNER JOIN boys bo ON b.`boyfriend_id`=bo.`id`
  WHERE bo.`boyName`='黄晓明';
  ```

- 方法二：truncate

- 语法：

  - ```mysql
    truncate table 表名;
    ```

- **区别：面试**

  - delete可以加where条件，truncate不能加
  - truncate删除效率更高
  - 如果要删除的表中有自增长列
    - 使用delete删除后，自增长列的值从断点开始
    - 使用truncate删除后，再插入数据，自增长列的值从1开始
  - truncate删除没有返回值。delete删除有返回值
  - truncate删除不能回滚，delete删除可以回滚

## 4. DDL语言

- Data Define Language：数据定义语言。用于数据库和表的创建、修改、删除，主要包括 create、drop、alter

### 4.1 库的管理

- 创建库

  - ```mysql
    create database 【if not exists】 库名 【character set 字符集名】;
    # 案例
    create databse if not exists books;
    ```

- 修改库

  - ```mysql
    alter database 库名 character set 字符集名;
    # 案例
    alter database books character set gbk;
    ```

- 删除库

  - ```mysql
    drop database 【if exists】 库名;
    # 案例
    drop database if exists books;
    ```

### 4.2 表的管理

- 表的创建

  - ```mysql
    create table 表名(
    	列名 列的类型 【(长度) 约束】,
    	列名 列的类型 【(长度) 约束】,
       	...
    	列名 列的类型 【(长度) 约束】
    );
    
    # 案例
    create table book(
    	id int,
        bName varchar(20),
        price double,
        authodId int,
        publishDate datetime
    );
    
    desc book;
    ```

- 表的修改

  - ```mysql
    # 1.添加列
    alter table 表名 add column 列名 类型 【first|after 字段名】;
    # 2.修改列的类型或者约束
    alter table 表名 modify column 列名 新类型 【新约束】;
    # 3.修改列名
    alter table 表名 change column 旧列名 新列名 类型;
    # 4.删除列
    alter table 表名 drop column 列名;
    # 5.修改表名
    alter table 表名 rename 【to】 新表名;
    
    # 案例：
    # 1.修改列名和类型
    ALTER TABLE book CHANGE COLUMN publishdate pubDate DATETIME ;
    
    # 2.修改列的类型或约束
    ALTER TABLE book MODIFY COLUMN pubdate TIMESTAMP;
    
    # 3.添加新列
    ALTER TABLE author ADD COLUMN annual DOUBLE; 
    
    # 4.删除列
    ALTER TABLE book_author DROP COLUMN  annual;
    
    # 5.修改表名
    ALTER TABLE book_author RENAME TO author;
    
    DESC book;
    ```

- 表的删除

  - ```mysql
    drop table【if exists】 表名;
    
    案例：
    DROP TABLE IF EXISTS book_author;
    
    SHOW TABLES;
    
    #通用的写法：
    DROP DATABASE IF EXISTS 旧库名;
    CREATE DATABASE 新库名 ;
    
    DROP TABLE IF EXISTS 旧表名;
    CREATE TABLE  表名();
    ```

- 表的复制

  - ```mysql
    # 1.复制表的结构
    create table 表名 like 旧表;
    # 复制表的结构+数据
    create table 表名
    select 查询列表 
    from 旧表 
    【where 筛选】;
    
    
    # 案例
    #案例：创建表author
    CREATE TABLE IF NOT EXISTS author (
      id INT,
      au_name VARCHAR (20),
      nation VARCHAR (10)
    );
    DESC author ;
    INSERT INTO author VALUES
    (1,'村上春树','日本'),
    (2,'莫言','中国'),
    (3,'冯唐','中国'),
    (4,'金庸','中国');
    
    SELECT * FROM Author;
    SELECT * FROM copy2;
    
    #1.仅仅复制表的结构
    CREATE TABLE copy LIKE author;
    
    #2.复制表的结构+数据
    CREATE TABLE copy2 SELECT * FROM author;
    
    #只复制部分数据
    CREATE TABLE copy3 SELECT id,au_name
    FROM author WHERE nation='中国';
    
    #仅仅复制某些字段
    CREATE TABLE copy4 SELECT id,au_name
    FROM author WHERE 0;
    ```


### 4.3 数据类型详解

#### 4.3.1 数值型

> - 整型

- 分类：

  - ```mysql
    tinyint、smallint、mediumint、int/integer、bigint
    1	       2		   3	       4		 8
    ```

- 特点：

  - 默认是有符号，如果需要设置为无符号，需要添加unsigned关键字
  - 如果插入的数值超出了整型的范围，会报out of range异常
  - 长度代表显示的最大宽度，如果不够会用0在左边填充，但是需要搭配zero fill使用，如果不设置长度，会有默认长度

- 代码实例

- ```mysql
  #1.如何设置无符号和有符号
  DROP TABLE IF EXISTS tab_int ;
  
  CREATE TABLE tab_int (
      t1 INT (7) ZEROFILL, 
      t2 INT (7) ZEROFILL
  ) ;
  
  DESC tab_int ;
  
  INSERT INTO tab_int VALUES (- 123456) ;
  
  INSERT INTO tab_int VALUES (- 123456, - 123456) ;
  
  INSERT INTO tab_int VALUES (2147483648, 4294967296) ;
  
  INSERT INTO tab_int VALUES (123, 123) ;
  
  SELECT * FROM tab_int ;
  
  ```

> - 浮点型

- 定点数：dec(M,D)；decimal(M,D)

- 浮点数：float(M,D) 4；double(M,D) 8

- 特点：

  - M：代表整数部分+小数部分的个数，D：代表小数部分的个数
  - 如果超出范围，则报out of range异常，并且插入临界值(事实上似乎不会！)
  - M和D都可以省略，对应定点数，M默认为0，D默认为0
  - 如果要求精度搞，则优先考虑使用定点数

- ```mysql
  #测试M和D
  DROP TABLE tab_float ;
  
  CREATE TABLE tab_float (f1 FLOAT, f2 DOUBLE, f3 DECIMAL) ;
  
  SELECT * FROM tab_float ;
  
  DESC tab_float ;
  
  INSERT INTO tab_float VALUES(123.4523,123.4523,123.4523);
  INSERT INTO tab_float VALUES(123.456,123.456,123.456);
  INSERT INTO tab_float VALUES(123.4,123.4,123.4);
  INSERT INTO tab_float VALUES(1523.4,1523.4,1523.4);
  
  #原则：
  #所选择的类型越简单越好，能保存数值的类型越小越好
  ```

#### 4.3.2 字符型

- 较短的文本：char、varchar

- 其他：

  - binary和varbinary用于保存较短的二进制
  - enum用于保存枚举
  - set用于保存集合

- 较长的文本：text、blob(较大的二进制)

- 特点：

  - ```mysql
      		写法			M的意思								特点		空间的耗费	效率
          	char	char(M)		最大的字符数，可以省略，默认为1	 固定长度的字符	比较耗费	 高
          
     varchar	varchar(M)	最大的字符数，不可以省略			可变长度的字符	   比较节省		低
      
    ```

- ```mysql
  CREATE TABLE tab_char(
  	c1 ENUM('a','b','c')
  );
  # 只能插入之前定义在枚举中的元素
  INSERT INTO tab_char VALUES('a');
  INSERT INTO tab_char VALUES('b');
  INSERT INTO tab_char VALUES('c');
  INSERT INTO tab_char VALUES('m');
  # 不区分大小写！！！
  INSERT INTO tab_char VALUES('A');
  
  SELECT * FROM tab_set;
  
  CREATE TABLE tab_set(
  	s1 SET('a','b','c','d')
  );
  # 只能插入之前定义在集合中的元素
  INSERT INTO tab_set VALUES('a');
  INSERT INTO tab_set VALUES('A,B');
  INSERT INTO tab_set VALUES('a,c,d');
  
  ```

#### 4.3.3 日期类型

- 分类：

  - date：只保存日期
  - time：只保存时间
  - year：只保存年
  - datetime：保存日期+时间
  - timestamp：保存日期+时间

- 特点：

  - ```mysql
    			字节		范围			时区等的影响
    datetime	 8		1000——9999	       不受
    timestamp	 4	    1970-2038	        受
    ```

- ```mysql
  CREATE TABLE tab_date(
  	t1 DATETIME,
  	t2 TIMESTAMP
  );
  
  INSERT INTO tab_date VALUES(NOW(),NOW());
  
  SELECT * FROM tab_date;
  
  SHOW VARIABLES LIKE 'time_zone';
  
  SET time_zone='+9:00';
  ```

### 4.4 常见的约束

- 含义：约束是一种限制，用于限制表中的数据，保证表中的数据正确和可靠性

- 分类：六大约束

  - NOT NULL：非空，用于保证该字段的值不能为空，如：姓名、学号
  - DEFAULT：默认，用于保证该字段有默认值，如：性别
  - PRIMARY KEY：主键，用于保证该字段的值具有唯一性，并且非空，如：学号、员工编号
  - UNIQUE：唯一，用于保证该字段的值具有唯一性，可以为空，如：座位号
  - CHECK：检查约束(mysql中不支持)，如：年龄、性别
  - FOREIGN KEY：外键，用于限制两个表的关系，保证该字段的值必须来自于主表的关联列的值，在从表中添加外键约束，用于引用主表中某列的值，如：学生表中的专业编号

- 添加约束的时机

  - 创建表时或者修改表时

- 约束的添加分类：

  - 列级约束：六大约束语法都支持，但是外键约束没有效果
  - 表级约束：除了非空，默认，其他的都支持

- 语法：

  - ```mysql
    create table 表名(
    	字段名 字段类型 列级约束,
        字段名 字段类型,
        ...
        表级约束
    );
    ```

#### 4.4.1 创建表时添加约束

- 添加列级约束：

  - 直接在字段名和类型后面追加约束类型即可，只支持：default、not null、primary key、unique！

- ```mysql
  #先新建一个库
  CREATE DATABASE students;
  
  USE students;
  
  DROP TABLE stuinfo;
  
  CREATE TABLE stuinfo(
  	id INT PRIMARY KEY,#主键
  	stuName VARCHAR(20) NOT NULL UNIQUE,#非空
  	gender CHAR(1) CHECK(gender='男' OR gender ='女'),#检查
  	seat INT UNIQUE,#唯一
  	age INT DEFAULT  18,#默认约束
  	majorId INT REFERENCES major(id)#外键
  );
  
  CREATE TABLE major(
  	id INT PRIMARY KEY,
  	majorName VARCHAR(20)
  );
  
  #查看stuinfo中的所有索引，包括主键、外键、唯一
  SHOW INDEX FROM stuinfo;
  
  ```

- 添加表级约束：

  - 在各个字段的最下面使用【constraint 约束名 约束类型(字段名)】 实现

- ```mysql
  DROP TABLE IF EXISTS stuinfo;
  CREATE TABLE stuinfo(
  	id INT,
  	stuname VARCHAR(20),
  	gender CHAR(1),
  	seat INT,
  	age INT,
  	majorid INT,
  	
  	CONSTRAINT pk PRIMARY KEY(id),#主键
  	CONSTRAINT uq UNIQUE(seat),#唯一键
  	CONSTRAINT ck CHECK(gender ='男' OR gender  = '女'),#检查
  	CONSTRAINT fk_stuinfo_major FOREIGN KEY(majorid) REFERENCES major(id)#外键	
  );
  
  SHOW INDEX FROM stuinfo;
  
  ```

- 通用写法

- ```mysql
  CREATE TABLE IF NOT EXISTS stuinfo(
  	id INT PRIMARY KEY,
  	stuname VARCHAR(20),
  	sex CHAR(1),
  	age INT DEFAULT 18,
  	seat INT UNIQUE,
  	majorid INT,
  	CONSTRAINT fk_stuinfo_major FOREIGN KEY(majorid) REFERENCES major(id)
  );
  
  create table 表名(
    	字段名 字段类型 not null,#非空
    	字段名 字段类型 primary key,#主键
    	字段名 字段类型 unique,#唯一
    	字段名 字段类型 default 值,#默认
    	constraint 约束名 foreign key(字段名) references 主表（被引用列）
  );
  
  注意：
    			   支持类型		      可以起约束名			
  列级约束		除了外键		     不可以
  表级约束		除了非空和默认	  可以，但对主键无效
    
  列级约束可以在一个字段上追加多个，中间用空格隔开，没有顺序要求
  ```

- primary key和unique的区别

- ```mysql
  		保证唯一性  是否允许为空    一个表中可以有多少个   是否允许组合
  主键		√				×			至多有1个           √，但不推荐
  唯一		√				√			可以有多个          √，但不推荐
  
  ```

#### 4.4.2 修改表时添加约束

- 语法：

- ```mysql
  1、添加列级约束
  alter table 表名 modify column 字段名 字段类型 新约束;
  
  2、添加表级约束
  alter table 表名 add 【constraint 约束名】 约束类型(字段名) 【外键的引用】;
  ```

- ```mysql
  DROP TABLE IF EXISTS stuinfo;
  CREATE TABLE stuinfo(
  	id INT,
  	stuname VARCHAR(20),
  	gender CHAR(1),
  	seat INT,
  	age INT,
  	majorid INT
  );
  
  DESC stuinfo;
  #1.添加非空约束
  ALTER TABLE stuinfo MODIFY COLUMN stuname VARCHAR(20)  NOT NULL;
  
  #2.添加默认约束
  ALTER TABLE stuinfo MODIFY COLUMN age INT DEFAULT 18;
  
  #3.添加主键
      #①列级约束
      ALTER TABLE stuinfo MODIFY COLUMN id INT PRIMARY KEY;
      #②表级约束
      ALTER TABLE stuinfo ADD PRIMARY KEY(id);
  
  #4.添加唯一
      #①列级约束
      ALTER TABLE stuinfo MODIFY COLUMN seat INT UNIQUE;
      #②表级约束
      ALTER TABLE stuinfo ADD UNIQUE(seat);
  
  #5.添加外键
  ALTER TABLE stuinfo ADD CONSTRAINT fk_stuinfo_major FOREIGN KEY(majorid) REFERENCES major(id); 
  
  ```

#### 4.4.3 修改表时删除约束

- ```mysql
  #1.删除非空约束
  ALTER TABLE stuinfo MODIFY COLUMN stuname VARCHAR(20) NULL;
  
  #2.删除默认约束
  ALTER TABLE stuinfo MODIFY COLUMN age INT ;
  
  #3.删除主键
  ALTER TABLE stuinfo DROP PRIMARY KEY;
  
  #4.删除唯一
  ALTER TABLE stuinfo DROP INDEX seat;
  
  #5.删除外键
  ALTER TABLE stuinfo DROP FOREIGN KEY fk_stuinfo_major;
  
  SHOW INDEX FROM stuinfo;
  ```

- 修改表时添加或者删除约束总结

- ```mysql
  1、非空
  添加非空
  alter table 表名 modify column 字段名 字段类型 not null;
  删除非空
  alter table 表名 modify column 字段名 字段类型 ;
  
  2、默认
  添加默认
  alter table 表名 modify column 字段名 字段类型 default 值;
  删除默认
  alter table 表名 modify column 字段名 字段类型 ;
  
  3、主键
  添加主键
  alter table 表名 add【 constraint 约束名】 primary key(字段名);
  删除主键
  alter table 表名 drop primary key;
  
  4、唯一
  添加唯一
  alter table 表名 add【 constraint 约束名】 unique(字段名);
  删除唯一
  alter table 表名 drop index 索引名;
  
  5、外键
  添加外键
  alter table 表名 add【 constraint 约束名】 foreign key(字段名) references 主表（被引用列）;
  删除外键
  alter table 表名 drop foreign key 约束名;
  
  ```

#### 4.4.4 自增长列

- 含义：可以不用手动的插入值，系统提供默认的序列值

- 特点

  - 不用手动插入值，可以自动提供序列值，默认从1 开始，步长为1
    - 如果要更改起始值：手动插入值
    - 如果要更改步长：更改系统变量
      - set auto_increment_increment=值；
  - 一个表最多只有一个自增长列
  - 自增长列只能支持数值类型
  - 自增长列必须为一个key

- ```mysql
  #一、创建表时设置标识列
  DROP TABLE IF EXISTS tab_identity;
  
  CREATE TABLE tab_identity(
  	id INT  ,
  	NAME FLOAT UNIQUE AUTO_INCREMENT,
  	seat INT 
  );
  TRUNCATE TABLE tab_identity;
  
  INSERT INTO tab_identity(id,NAME) VALUES(NULL,'john');
  
  INSERT INTO tab_identity(NAME) VALUES('lucy');
  
  SELECT * FROM tab_identity;
  
  SHOW VARIABLES LIKE '%auto_increment%';
  
  SET auto_increment_increment=3;
  
  ```

- 语法总结

- ```mysql
  一、创建表时设置自增长列
  create table 表(
  	字段名 字段类型 约束 auto_increment
  );
  
  二、修改表时设置自增长列
  alter table 表 modify column 字段名 字段类型 约束 auto_increment;
  
  三、删除自增长列
  alter table 表 modify column 字段名 字段类型 约束;
  
  ```

## 5. DCL

- Data Control Language：数据库控制语言，用来定义数据库的访问特权和安全级别，以及创建用户

### 5.1 管理用户

- 添加用户：

  - 语法：

  - ```mysql
    CREATE USER '用户名'@'主机名' IDENTIFIED BY '密码';
    # 主机名：localhost表示本地登录，%表示外部任意主机登录
    
    # 实例
    create user 'king'@'%' identified by '123456';
    # 要在mysql数据库下才能查询到user表
    select user from user;
    ```

- 查询用户：

  - 语法：

  - ```mysql
    # 要在mysql数据库下才能查询到user表
    select user from user;
    ```

- 删除用户：

  - 语法：

  - ```mysql
    drop user '用户名'@'主机名';
    
    # 实例
    drop user 'king'@'%';
    select user from user;
    ```

- 修改用户密码：

  - 语法：

  - ```mysql
    # 法一
    #  5.7版本下的mysql数据库下已经没有password这个字段了，password字段改成了authentication_string
    update user set password = password('新密码')
    where user = '用户名';
    
    # 法二
    set password for '用户名'@'%'=password('新密码');
    ```

  - 忘记root密码？

  - ```mysql
        a. cmd 输入 net stop mysql  -- 停止mysql服务 需要管理员运行该cmd
        b. 使用无验证方式启动mysql服务： mysqld --skip-grant-tables
        c. 打开新的cmd窗口,直接输入mysql命令，敲回车。就可以登录成功
        d. use mysql;
        e. update user set password = password('你的新密码') where user = 'root';
        f. 关闭两个窗口
        g. 打开任务管理器，手动结束mysqld.exe 的进程
        h. 启动mysql服务
        i. 使用新密码登录。
    ```

### 5.2 权限管理

- 查询权限

  - 语法：

  - ```mysql
    SHOW GRANTS FOR '用户名'@'主机名';
    
    # 实例
    show grants for 'king'@'%';
    ```

- 授予权限：

  - 语法

  - ```mysql
    GRANT 权限列表 ON 数据库名.表名 TO '用户名'@'主机名';  -- 标准格式
    
    # 如果需要授予特别多的权限的话，这种方法显然特别麻烦，这里可以先授予全部权限，然后再撤销部分权限
     -- 给张三本机用户授予所有的权限
    GRANT ALL ON *.* TO 'zhangsan'@'localhost'; 
    ```

- 撤销权限：

  - 语法

  - ```mysql
    REVOKE 权限列表 ON 数据库名.表名 FROM '用户名'@'主机名';
    
    -- 撤销李四在所有机器上面的 UPDATE 权限
    REVOKE UPDATE ON db3.`account` FROM 'lisi'@'%';  
    
    # 授予远程访问的所有权限
    # 法一
    grant all privileges on *.* to 'root'@'%' identified by 'root';
    # 法二：
    grant all privileges on *.* to 'root'@'%' with grant option;
    ```

- mysql8开始不能再隐式使用grant命令创建用户，应该：

- ```mysql
  mysql> CREATE USER 'root'@'%' IDENTIFIED BY 'root';
  mysql> GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' WITH GRANT OPTION;
  ```

## 6. TCL语言

- Transcation Control Language：事务控制语言，用于维护数据的一致性，主要包括：commit、roolback、savepoint

### 6.1 事务

- 事务：一个或者一组sql语句组成的一个执行单元，这个执行单元要么全部执行，要么全部不执行！

- 事务的特性：ACID

  - 原子性：一个事务不可再被分割，要不都执行，要不都不执行
  - 一致性：一个事务执行会使数据从一个一致状态转换到另一个一致状态
  - 隔离性：一个事务的执行不受其他事务的干扰
  - 持久性：一个事务一旦提交，则会永久的改变数据库的数据

- 事务的分类：

  - 隐式事务(自动事务)：没有明显的开启和结束，本身就是一条事务可以自动提交，如：insert，update，delete
  - 显示事务：事务具有明显的开启和结束的标记；
    - 前提：必须先设置自动提交功能禁用

- 具体语法：

  - ```mysql
    步骤1：开启事务
    set autocommit=0;
    start transaction;可选的
    
    步骤2：编写事务中的sql语句(select insert update delete)
    语句1;
    语句2;
    ...
    
    步骤3：结束事务
    commit;提交事务
    rollback;回滚事务
    ```

- ```mysql
  SHOW VARIABLES LIKE 'autocommit';
  SHOW ENGINES;
  
  #1.演示事务的使用步骤
  DROP TABLE IF EXISTS account;
  CREATE TABLE account(
  	id INT PRIMARY KEY AUTO_INCREMENT,
  	username VARCHAR(20),
  	balance DOUBLE
  );
  INSERT INTO account(username,balance)
  VALUES('张无忌',1000),('赵敏',1000);
  
  #开启事务
  SET autocommit=0;
  START TRANSACTION;
  #编写一组事务的语句
  UPDATE account SET balance = 1000 WHERE username='张无忌';
  UPDATE account SET balance = 1000 WHERE username='赵敏';
  
  #结束事务
  ROLLBACK;
  #commit;
  
  SELECT * FROM account;
  
  # 回滚点
  # 语法
  savepoint 节点名;
  
  # 演示savepoint 的使用
  SET autocommit=0;
  
  START TRANSACTION;
  DELETE FROM account WHERE id=25;
  SAVEPOINT a;#设置保存点
  DELETE FROM account WHERE id=28;
  ROLLBACK TO a;#回滚到保存点
  
  SELECT * FROM account;
  ```

### 6.2 并发事务

- 事务的并发问题是如何发生的？

  - 多个事务同时操作同一个数据库的相同数据时

- 并发问题都有哪些？

  - 对于同时运行的多个事务，当这些事务访问数据库中相同的数据时，如果没有采取必要的隔离机制，就会导致各种并发问题，主要有：
  - 脏读：一个事务读取了其他事务还没有提交的数据，也就是读到的是其他事务将要“更新”的数据
  - 不可重复读：一个事务多次读取，结果不一样
  - 幻读：一个事务读取了其他事务还没有提交的数据，也就是只是读到其他事务“插入”的数据

- 数据库事务的隔离性：

  - 数据库系统必须具有隔离并发运行各个事务的能力，使它们不会相互影响，以避免各种并发问题

- 隔离级别：

  - 一个事务与其他事务隔离的程度
  - 数据库规定了多种事务隔离级别，不同隔离级别对应不同的干扰程度，隔离级别越高，数据一致性就越好，但是并发性就越弱

- 如何解决并发问题？

  - 通过设置隔离级别来解决并发问题

- 隔离级别：

- ```mysql
  							脏读			不可重复读		  幻读
  read uncommitted:读未提交     ×                ×              ×        
  read committed：读已提交      √                ×              ×
  repeatable read：可重复读     √                √              ×
  serializable：串行化          √                √              √
  ```

- Oracle支持的两种事务隔离级别：READ COMMITED、SERIALIZEABLE。默认的隔离级别是read commited

- Mysql支持4种事务隔离级别，默认的隔离级别是：repeatable read

- ```mysql
  # 查看隔离级别
  select @@tx_isolation;
  # 设置隔离级别
  set session|global transaction isolation level 隔离级别;
  
  #2.演示事务对于delete和truncate的处理的区别
  SET autocommit=0;
  START TRANSACTION;
  
  DELETE FROM account;
  ROLLBACK;
  
  ```

## 7. 视图

- 什么是视图？
  
  - 虚拟表，和普通表一样使用，是mysql5.1版本出现的新特性，可以通过表动态生成数据
  
- 应用场景：
  - 多个地方用到同样的查询结果
  - 该查询结果使用的sql语句比较复杂
  
- 优点：

  - 重用sql语句
  - 简化复杂的Sql操作，不必知道它的查询细节
  - 保护数据，提高安全性

- ```mysql
  SELECT stuname,majorname FROM stuinfo s
  INNER JOIN major m ON s.`majorid`= m.`id`
  WHERE s.`stuname` LIKE '张%';
  
  CREATE VIEW v1 AS
  SELECT stuname,majorname
  FROM stuinfo s
  INNER JOIN major m ON s.`majorid`= m.`id`;
  
  SELECT * FROM v1 WHERE stuname LIKE '张%';
  
  ```

### 7.1 视图的创建

- 语法：

  - ```mysql
    create view 视图名 as 查询语句;
    ```

- ```mysql
  USE myemployees;
  
  #1.查询姓名中包含a字符的员工名、部门名和工种信息
   #①创建
   CREATE VIEW myv1 AS
   SELECT last_name,department_name,job_title
   FROM employees e
   JOIN departments d ON e.department_id  = d.department_id
   JOIN jobs j ON j.job_id  = e.job_id;
  
   #②使用
   SELECT * FROM myv1 WHERE last_name LIKE '%a%';
  
  #2.查询各部门的平均工资级别
  #①创建视图查看每个部门的平均工资
  CREATE VIEW myv2 AS
  SELECT AVG(salary) ag,department_id
  FROM employees GROUP BY department_id;
  
  #②使用
  SELECT myv2.`ag`,g.grade_level 
  FROM myv2 JOIN job_grades g
  ON myv2.`ag` BETWEEN g.`lowest_sal` AND g.`highest_sal`;
  
  #3.查询平均工资最低的部门信息
  SELECT * FROM myv2 ORDER BY ag LIMIT 1;
  
  #4.查询平均工资最低的部门名和工资
  CREATE VIEW myv3 AS
  SELECT * FROM myv2 ORDER BY ag LIMIT 1;
  
  SELECT d.*,m.ag FROM myv3 m
  JOIN departments d
  ON m.`department_id`=d.`department_id`;
  
  ```

### 7.2 视图的修改

- 语法：

  - ```mysql
    # 方法一
    create or replace view 视图名 as 查询语句
    # 方法二
    alter view 视图名 as 查询语句;
    ```

- ```mysql
  #方式一：
  /*
  create or replace view  视图名 as 查询语句;
  */
  SELECT * FROM myv3 
  
  CREATE OR REPLACE VIEW myv3 AS
  SELECT AVG(salary),job_id
  FROM employees GROUP BY job_id;
  
  #方式二：
  /*
  语法：
  alter view 视图名 as 查询语句;
  */
  ALTER VIEW myv3 AS SELECT * FROM employees;
  
  ```

### 7.3 视图的删除

- 用户可以一次删除一个或者多个视图，前提是用户必须拥有该视图的drop权限

- 语法：

  - ```mysql
    drop view 视图1,视图2...;
    ```

- ```mysql
  /*
  语法：drop view 视图名,视图名,...;
  */
  DROP VIEW emp_v1,emp_v2,myv3;
  ```

### 7.4 视图的查看

- 语法：

  - ```mysql
    desc 视图名;
    show create view 视图名;
    ```

- ```mysql
  DESC myv3;
  SHOW CREATE VIEW myv3;
  ```

### 7.5 视图内数据的增删改查

- 插入：insert

- 修改：update

- 删除：delete

- 查看：select

- 对应的表也会随之增删改查

  ```mysql
  CREATE OR REPLACE VIEW myv1
  AS
  SELECT last_name,email,salary*12*(1+IFNULL(commission_pct,0)) "annual salary"
  FROM employees;
  
  CREATE OR REPLACE VIEW myv1
  AS
  SELECT last_name,email
  FROM employees;
  
  SELECT * FROM myv1;
  SELECT * FROM employees;
  #1.插入
  
  INSERT INTO myv1 VALUES('张飞','zf@qq.com');
  
  #2.修改
  UPDATE myv1 SET last_name = '张无忌' WHERE last_name='张飞';
  
  #3.删除
  DELETE FROM myv1 WHERE last_name = '张无忌';
  ```

- **注意：**视图一般是用于查询而不是更新的，因此具备以下特点的视图都不允许更新

  - 包含以下关键字的sql语句：
    - 分组函数、group by、distinct、having、union、union all
  - 常量视图
  - select 中包含子查询
  - join
  - from一个不能更新的视图
  - where 子句的子查询引用了from子句中的表

- ```mysql
  #具备以下特点的视图不允许更新
  #①包含以下关键字的sql语句：分组函数、distinct、group  by、having、union或者union all
  
  CREATE OR REPLACE VIEW myv1
  AS
  SELECT MAX(salary) m,department_id
  FROM employees
  GROUP BY department_id;
  
  SELECT * FROM myv1;
  
  #更新
  UPDATE myv1 SET m=9000 WHERE department_id=10;
  
  #②常量视图
  CREATE OR REPLACE VIEW myv2
  AS
  SELECT 'john' NAME;
  
  SELECT * FROM myv2;
  
  #更新
  UPDATE myv2 SET NAME='lucy';
  
  #③Select中包含子查询
  
  CREATE OR REPLACE VIEW myv3
  AS
  SELECT department_id,(SELECT MAX(salary) FROM employees) 最高工资
  FROM departments;
  
  #更新
  SELECT * FROM myv3;
  UPDATE myv3 SET 最高工资=100000;
  
  #④join
  CREATE OR REPLACE VIEW myv4
  AS
  SELECT last_name,department_name
  FROM employees e
  JOIN departments d
  ON e.department_id  = d.department_id;
  
  #更新
  SELECT * FROM myv4;
  UPDATE myv4 SET last_name  = '张飞' WHERE last_name='Whalen';
  INSERT INTO myv4 VALUES('陈真','xxxx');
  
  #⑤from一个不能更新的视图
  CREATE OR REPLACE VIEW myv5
  AS SELECT * FROM myv3;
  
  #更新
  SELECT * FROM myv5;
  UPDATE myv5 SET 最高工资=10000 WHERE department_id=60;
  
  #⑥where子句的子查询引用了from子句中的表
  
  CREATE OR REPLACE VIEW myv6
  AS
  SELECT last_name,email,salary
  FROM employees
  WHERE employee_id IN(
  	SELECT  manager_id
  	FROM employees
  	WHERE manager_id IS NOT NULL
  );
  
  #更新
  SELECT * FROM myv6;
  UPDATE myv6 SET salary=10000 WHERE last_name = 'k_ing';
  
  ```

- 视图和表的对比

- ```mysql
  		关键字		  是否占用物理空间			使用
  视图	   view		占用较小，只保存sql逻辑	一般用于查询
  表	    table		保存实际的数据			  增删改查
  
  ```

## 8. Mysql编程

- 变量的分类：

  - 系统变量：
    - 全局变量
    - 会话变量
  - 自定义变量：
    - 用户变量
    - 局部变量

### 8.1 系统变量

- 变量由系统定义，不是用户定义，属于服务器层面

- 全局变量需要添加global关键字

- 会话变量需要添加session关键字，如果不写，默认会话级别 

- 语法：

  - ```mysql
    # 1. 查看所有系统变量
    show global|【session】variables;
    
    # 2. 查看满足条件的部分系统变量
    show global|【session】 variables like '%char%';
    
    # 3.查看指定的系统变量的值
    select @@global|【session】系统变量名;
    
    # 4.为某个系统变量赋值
    方式一：
    set global|【session】系统变量名=值;
    方式二：
    set @@global.|【session】系统变量名=值;
    ```

- ```mysql
  #1》全局变量
  /*
  作用域：针对于所有会话（连接）有效，但不能跨重启
  */
  # 1.查看所有全局变量
  SHOW GLOBAL VARIABLES;
  # 2.查看满足条件的部分系统变量
  SHOW GLOBAL VARIABLES LIKE '%char%';
  # 3.查看指定的系统变量的值
  SELECT @@global.autocommit;
  # 4.为某个系统变量赋值
  SET @@global.autocommit=0;
  SET GLOBAL autocommit=0;
  
  #2》会话变量
  /*
  作用域：针对于当前会话（连接）有效
  */
  # 1.查看所有会话变量
  SHOW SESSION VARIABLES;
  # 2.查看满足条件的部分会话变量
  SHOW SESSION VARIABLES LIKE '%char%';
  # 3.查看指定的会话变量的值
  SELECT @@autocommit;
  SELECT @@session.tx_isolation;
  # 4.为某个会话变量赋值
  SET @@session.tx_isolation='read-uncommitted';
  SET SESSION tx_isolation='read-committed';
  
  ```

### 8.2 自定义变量

- ```mysql
  #二、自定义变量
  /*
  说明：变量由用户自定义，而不是系统提供的
  使用步骤：
  1、声明
  2、赋值
  3、使用（查看、比较、运算等）
  */
  
  #1》用户变量
  /*
  作用域：针对于当前会话（连接）有效，作用域同于会话变量
  */
  
  #赋值操作符：=或:=
  # 1.声明并初始化
  SET @变量名=值;
  SET @变量名:=值;
  SELECT @变量名:=值;
  
  # 2.赋值（更新变量的值）
  #方式一：
  	SET @变量名=值;
  	SET @变量名:=值;
  	SELECT @变量名:=值;
  #方式二：
  	SELECT 字段 INTO @变量名
  	FROM 表;
  # 3.使用（查看变量的值）
  SELECT @变量名;
  
  
  #2》局部变量
  /*
  作用域：仅仅在定义它的begin end块中有效
  应用在 begin end中的第一句话
  */
  
  # 1.声明
  DECLARE 变量名 类型;
  DECLARE 变量名 类型 【DEFAULT 值】;
  
  
  # 2.赋值（更新变量的值）
  
  #方式一：
  	SET 局部变量名=值;
  	SET 局部变量名:=值;
  	SELECT 局部变量名:=值;
  #方式二：
  	SELECT 字段 INTO 具备变量名
  	FROM 表;
  # 3.使用（查看变量的值）
  SELECT 局部变量名;
  
  
  #案例：声明两个变量，求和并打印
  #用户变量
  SET @m=1;
  SET @n=1;
  SET @sum=@m+@n;
  SELECT @sum;
  
  #局部变量
  DECLARE m INT DEFAULT 1;
  DECLARE n INT DEFAULT 1;
  DECLARE SUM INT;
  SET SUM=m+n;
  SELECT SUM;
  
  # 用户变量和局部变量的对比
  		作用域			定义位置		语法
  用户变量	当前会话		会话的任何地方		加@符号，不用指定类型
  局部变量	定义它的BEGIN END中 	BEGIN END的第一句话	一般不用加@,需要指定类型
  
  ```

### 8.3 存储过程和函数

#### 8.3.1 存储过程

- 含义：一组预先编译好的SQL语句的集合，理解成批处理语句，类似于java中的方法，将一组完成特定功能的逻辑语句包装起来，对外暴露名字

- 优点：

  - 提高代码重用性
  - 简化操作
  - 减少编译次数并且减少和数据库服务器连接次数，提高效率

- 创建语法：

- ```mysql
  CREATE PROCEDURE 存储过程名(参数列表)
  BEGIN
  	存储过程体（一组合法的SQL语句）
  END
  
  注意：
  1、参数列表包含三部分
  参数模式  参数名  参数类型
  举例：
  in stuname varchar(20)
  
  参数模式：
  in：该参数可以作为输入，也就是该参数需要调用方传入值
  out：该参数可以作为输出，也就是该参数可以作为返回值
  inout：该参数既可以作为输入又可以作为输出，也就是该参数既需要传入值，又可以返回值
  
  2、如果存储过程体仅仅只有一句话，begin end可以省略
  存储过程体中的每条sql语句的结尾要求必须加分号。
  存储过程的结尾可以使用 delimiter 重新设置
  语法：
  delimiter 结束标记
  案例：
  delimiter $
  
  ```

- 调用语法：

- ```mysql
  CALL 存储过程名(实参列表);
  
  举例：
  调用in模式的参数：call sp1（‘值’）;
  调用out模式的参数：set @name; call sp1(@name);select @name;
  调用inout模式的参数：set @name=值; call sp1(@name); select @name;
  
  # 实例
  #1.空参列表
  #案例：插入到admin表中五条记录
  
  SELECT * FROM admin;
  
  DELIMITER $ # 将语句的结束符号从分号;临时改为两个$(可以是自定义)
  CREATE PROCEDURE myp1()
  BEGIN
  	INSERT INTO admin(username,`password`) 
  	VALUES('john1','0000'),('lily','0000'),('rose','0000'),('jack','0000'),('tom','0000');
  END $
  
  #调用
  CALL myp1()$
  
  #2.创建带in模式参数的存储过程
  
  #案例1：创建存储过程实现 根据女神名，查询对应的男神信息
  
  CREATE PROCEDURE myp2(IN beautyName VARCHAR(20))
  BEGIN
  	SELECT bo.*
  	FROM boys bo
  	RIGHT JOIN beauty b ON bo.id = b.boyfriend_id
  	WHERE b.name=beautyName;
  END $
  
  #调用
  CALL myp2('柳岩')$
  
  #案例2 ：创建存储过程实现，用户是否登录成功
  
  CREATE PROCEDURE myp3(IN username VARCHAR(20),IN PASSWORD VARCHAR(20))
  BEGIN
  	DECLARE result VARBINARY(20) DEFAULT '';#声明并初始化
  	
  	SELECT COUNT(*) INTO result#赋值
  	FROM admin
  	WHERE admin.username = username
  	AND admin.password = PASSWORD;
  	
  	SELECT result;#使用
  END $
  
  CALL myp3('张飞','8888')$
  
  CREATE PROCEDURE myp4(IN username VARCHAR(20),IN PASSWORD VARCHAR(20))
  BEGIN
  	DECLARE result INT DEFAULT 0;#声明并初始化
  	
  	SELECT COUNT(*) INTO result#赋值
  	FROM admin
  	WHERE admin.username = username
  	AND admin.password = PASSWORD;
  	
  	SELECT IF(result>0,'成功','失败');#使用
  END $
  
  #调用
  CALL myp4('张飞','8888')$
  
  
  #3.创建out 模式参数的存储过程
  #案例1：根据输入的女神名，返回对应的男神名
  
  CREATE PROCEDURE myp6(IN beautyName VARCHAR(20),OUT boyName VARCHAR(20))
  BEGIN
  	SELECT bo.boyname INTO boyname
  	FROM boys bo
  	RIGHT JOIN
  	beauty b ON b.boyfriend_id = bo.id
  	WHERE b.name=beautyName ;
  	
  END $
  
  #案例2：根据输入的女神名，返回对应的男神名和魅力值
  
  CREATE PROCEDURE myp7(IN beautyName VARCHAR(20),OUT boyName VARCHAR(20),OUT usercp INT) 
  BEGIN
  	SELECT boys.boyname ,boys.usercp INTO boyname,usercp
  	FROM boys 
  	RIGHT JOIN
  	beauty b ON b.boyfriend_id = boys.id
  	WHERE b.name=beautyName ;
  	
  END $
  
  #调用
  CALL myp7('小昭',@name,@cp)$
  SELECT @name,@cp$
  
  #4.创建带inout模式参数的存储过程
  #案例1：传入a和b两个值，最终a和b都翻倍并返回
  
  CREATE PROCEDURE myp8(INOUT a INT ,INOUT b INT)
  BEGIN
  	SET a=a*2;
  	SET b=b*2;
  END $
  
  #调用
  SET @m=10$
  SET @n=20$
  CALL myp8(@m,@n)$
  SELECT @m,@n$
  
  ```

- 查看语法

- ```mysql
  show create procedure 存储过程名;
  
  案例
  DESC myp2;
  SHOW CREATE PROCEDURE  myp2;
  ```

- 删除语法

- ```mysql
  drop procedure 存储过程名;
  
  案例:
  DROP PROCEDURE p1;
  DROP PROCEDURE p2,p3;#×
  ```

#### 8.3.2 函数

- 含义：一组预先编译好的SQL语句的集合，理解成批处理语句

- 区别：

  - 存储过程：可以有0个返回，也可以有多个返回，适合做批量插入、批量更新；
  - 函数：有且仅有1 个返回，适合做处理数据后返回一个结果。

- 创建语法：

- ```mysql
  CREATE FUNCTION 函数名(参数列表) RETURNS 返回类型
  BEGIN
  	函数体
  END
  
  /*
  注意：
  1.参数列表 包含两部分：
  参数名 参数类型
  2.函数体：肯定会有return语句，如果没有会报错
  如果return语句没有放在函数体的最后也不报错，但不建议
  
  return 值;
  3.函数体中仅有一句话，则可以省略begin end
  4.使用 delimiter语句设置结束标记
  */
  
  ```

- 调用语法

- ```mysql
  # 语法：
  SELECT 函数名(参数列表);
  
  
  DELIMITER $
  use employees $
  #1.无参有返回
  #案例：返回公司的员工个数
  CREATE FUNCTION myf1() RETURNS INT
  BEGIN	
  	DECLARE c INT DEFAULT 0;
  	SELECT COUNT(*) INTO c
  	FROM employees;
  	RETURN c;
  END $
  
  SELECT myf1()$
  
  
  #2.有参有返回
  #案例1：根据员工名，返回它的工资
  
  CREATE FUNCTION myf2(empName VARCHAR(20)) RETURNS DOUBLE
  BEGIN
  	SET @sal=0;#定义用户变量 
  	SELECT salary INTO @sal   #赋值
  	FROM employees
  	WHERE last_name = empName;
  	
  	RETURN @sal;
  END $
  
  SELECT myf2('kochhor') $
  
  #案例2：根据部门名，返回该部门的平均工资
  
  CREATE FUNCTION myf3(deptName VARCHAR(20)) RETURNS DOUBLE
  BEGIN
  	DECLARE sal DOUBLE ;
  	SELECT AVG(salary) INTO sal
  	FROM employees e
  	JOIN departments d ON e.department_id = d.department_id
  	WHERE d.department_name=deptName;
  	RETURN sal;
  END $
  
  SELECT myf3('IT')$
  
  ```

- 查看函数

- ```mysql
  show create function 函数名;
  
  SHOW CREATE FUNCTION myf3 $
  ```

- 删除函数

- ```mysql
  drop function 函数名;
  
  DROP FUNCTION myf3 $
  ```

### 8.4 流程控制结构

- 主要配合：存储过程和函数使用！

- 顺序结构：程序从上往下依次执行
- 分支结构：程序按条件进行选择执行，从两条或多条路径中选择一条执行
- 循环结构：程序满足一定条件下，重复执行一组语句

#### 8.4.1 分支结构

- if 函数：

  - 语法：if(条件,值1,值2);
  - 功能：实现双分支
  - 应用：可以作为表达式放在任何位置

- case结构：

  - 语法：看实例！

  - 特点：

    - 可以作为表达式，嵌套在其他语句中使用。
    - 可以放在任何地方，BEGIN END 中或BEGIN END 的外面可以作为独立的语句去使用，只能放在BEGIN END中如果WHEN中的值满足或条件成立，则执行对应的then后面的语句，并且结束CASE如果都不满足，则执行else中的语句或值。
    - sles可以省略，如果ELSE省略了，并且所有WHEN条件都不满足，则返回NULL。

  - 应用：

    - 可以放在任何位置，
    - 如果放在begin end 外面，作为表达式结合着其他语句使用
    - 如果放在begin end 里面，一般作为独立的语句使用

  - ```mysql
    # 语法
    情况1：类似于switch，一般用于实现等值判断。
    语法：
    case 变量或表达式
    when 值1 then 语句1;
    when 值2 then 语句2;
    ...
    else 语句n;
    end 
    
    情况2：类似于多重if语句，一般用于实现区间判断。
    语法：
    case 
    when 条件1 then 语句1;
    when 条件2 then 语句2;
    ...
    else 语句n;
    end 
    
    # 实例
    #案例 
    #创建存储过程，根据传入的成绩，来显示等级，比如传入的成绩：90-100, 显示A，80-90，显示B，60-80，显示c，否则，显示D
    
    CREATE PROCEDURE test_case (IN score INT) 
    BEGIN 
    	CASE 
    	WHEN score>=90 AND score<=100 THEN SELECT 'A'; 
    	WHEN score>=80 THEN SELECT 'B';
    	WHEN score>=60 THEN SELECT 'C'; 
    	ELSE SELECT 'D';
    	END CASE; 
    END $
    CALL test_case(95)$
    ```

- if 结构：

  - 语法：见实例！

  - 功能：类似于多重if，

  - 应用：只能应用再begin end中

  - ```mysql
    # 语法
    if 条件1 then 语句1;
    elseif 条件2 then 语句2;
    ....
    else 语句n;
    end if;
    
    #案例1：创建函数，实现传入成绩，如果成绩>90,返回A，如果成绩>80,返回B，如果成绩>60,返回C，否则返回D
    
    CREATE FUNCTION test_if(score FLOAT) RETURNS CHAR
    BEGIN
    	IF score>=90 THEN RETURN 'A';
    	ELSEIF score>=80 THEN RETURN 'B';
    	ELSEIF score>=60 THEN RETURN 'C';
    	ELSE RETURN 'D';
    	END IF;
    	RETURN ch;	
    END $
    
    SELECT test_case(56)$
    
    ```

####  8.4.2 循环结构

- 语法：见实例！

- 应用：只能放在begin end中

- ```mysql
  # 语法
  1、while
  语法：
  【名称:】while 循环条件 do
  		循环体
  end while 【名称】;
  2、loop
  语法：
  【名称：】loop
  		循环体
  end loop 【名称】;
  
  3、repeat
  语法：
  【名称:】repeat
  		循环体
  until 结束条件 
  end repeat 【名称】;
  
  # 对比
  ①这三种循环都可以省略名称，但如果循环中添加了循环控制语句（leave或iterate）则必须添加名称
  ②
  loop 一般用于实现简单的死循环
  while 先判断后执行
  repeat 先执行后判断，无条件至少执行一次
  
  # 循环控制语句：
  leave：类似于break，跳出当前循环
  iterate：类似于continue，用于结束本次循环，继续下一次！
  
  # 实例
  #1.没有添加循环控制语句
  #案例：批量插入，根据次数插入到admin表中多条记录
  USE girls$
  DROP PROCEDURE pro_while1$
  CREATE PROCEDURE pro_while1(IN insertCount INT)
  BEGIN
  	DECLARE i INT DEFAULT 1;
  	WHILE i<=insertCount DO
  		INSERT INTO admin(username,`password`) VALUES(CONCAT('Rose',i),'666');
  		SET i=i+1;
  	END WHILE;
  	
  END $
  
  CALL pro_while1(158)$
  
  select * from admin $
  /*
  int i=1;
  while(i<=insertcount){
  	//插入
  	i++;
  
  }
  */
  
  # a:类似于给循环起个名字！
  #2.添加leave语句
  #案例：批量插入，根据次数插入到admin表中多条记录，如果次数>20则停止
  TRUNCATE TABLE admin$
  DROP PROCEDURE test_while1$
  CREATE PROCEDURE test_while1(IN insertCount INT)
  BEGIN
  	DECLARE i INT DEFAULT 1;
  	a:WHILE i<=insertCount DO
  		INSERT INTO admin(username,`password`) VALUES(CONCAT('xiaohua',i),'0000');
  		IF i>=20 THEN LEAVE a;
  		END IF;
  		SET i=i+1;
  	END WHILE a;
  END $
  
  CALL test_while1(100)$
  
  select * from admin $
  
  #3.添加iterate语句
  #案例：批量插入，根据次数插入到admin表中多条记录，只插入偶数次
  TRUNCATE TABLE admin$
  DROP PROCEDURE test_while1$
  CREATE PROCEDURE test_while1(IN insertCount INT)
  BEGIN
  	DECLARE i INT DEFAULT 0;
  	a:WHILE i<=insertCount DO
  		SET i=i+1;
  		IF MOD(i,2)!=0 THEN ITERATE a;
  		END IF;
  		
  		INSERT INTO admin(username,`password`) VALUES(CONCAT('xiaohua',i),'0000');
  		
  	END WHILE a;
  END $
  
  CALL test_while1(100)$
  
  /*
  int i=0;
  while(i<=insertCount){
  	i++;
  	if(i%2==0){
  		continue;
  	}
  	插入
  }
  */
  
  select * from admin $
  
  ```

- 

