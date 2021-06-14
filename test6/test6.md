## 我的作业6    

姓名：王浩

学号：201810414123

班级：软件工程1班

## 基于Oracle的外卖系统数据库设计

```
 设计项目涉及的表及表空间使用方案。至少5张表和5万条数据，两个表空间。
 设计权限及用户分配方案。至少两类角色，两个用户。
 在数据库中建立一个程序包，在包中用PL/SQL语言设计一些存储过程和函数，实现比较复杂的业务逻辑，用模拟数据进行执行计划分析。
 设计自动备份方案或则手工备份方案。
```

## 评分标准

| 评分项     | 评分标准                     | 满分 |
| :--------- | :--------------------------- | :--- |
| 文档整体   | 文档内容详实、规范，美观大方 | 10   |
| 表设计     | 表，表空间设计合理，数据合理 | 20   |
| 用户管理   | 权限及用户分配方案设计正确   | 20   |
| PL/SQL设计 | 存储过程和函数设计正确       | 30   |
| 备份方案   | 备份方案设计正确             | 20   |

# 1 E-R模型设计

## 1.1 实体模型

根据外卖系统的应用场景分析，共有5个实体（Entity）,它们分别是外卖用户、外卖店、菜品、外卖订单、外卖订单详情。具体说明如下：

1、外卖用户（WM_USER）：用户包括用户ID（ID）,用户名（USER_NAME）,用户密码（USER_PWD）,用户地址（USER_ADDRESS）,用户电话（USER_PHONE）,余额（BALANCE）,注册时间（CREATE_DATE）。用户实体如下图1.1.1。

![img](用户实体.png) 

​															图 1.1.1 WM_USER实体

2、外卖店（SHOP）：外卖店包括外卖店ID（ID）,外卖店名（USER_NAME）,外卖店电话（SHOP_PHONE）,销售量（SELL_NUM）,评分（SCORE）。外卖店实体如下图1.1.2。

![img](外卖店实体.png) 

 																	图 1.1.2 SHOP实体

3、菜品（DISH）：菜品包括菜品ID（ID）,菜品名（DISH_NAME）,菜品价格（DISH_PRICE）等,其中菜品的属性中还包括外卖店ID（SHOP_ID），外卖店ID不能为空，表示菜品必须属于某个外卖店。菜品实体如下图1.1.3。

![img](菜品实体.png) 

​																	图 1.1.3 DISH实体

 

4、外卖订单（WM_ORDER）：订单包括订单ID（ID）,下单时间（ORDER_TIME）,订单总价（SUM_PRICE）等,其中订单的属性中还包括用户（USER_ID），用户ID不能为空，表示订单必须属于某个用户。订单的属性还有外卖店(SHOP_ID),外卖店ID不能为空，一个订单必须来自某个外卖店。订单实体如下图1.1.4。

![img](订单实体.png) 

​																图 1.1.4 WM_ORDER实体

5、订单详单（WM_ORDER_DETAIL）：订单详细包括订单详单ID（ID）,菜品价格（DISH_PRICE）,菜品数量（DISH_NUM）等,其中订单详单的属性中还包括菜品（DISH_ID），菜品ID不能为空，表示订单详单必须属于含有菜品。订单的属性还有订单详单所属的订单(ORDER_ID),订单ID不能为空，一个订单详单必须属于某个订单。订单详单实体如下图1.1.5。

![img](订单详情实体.png) 

​						 							图 1.1.5 WM_ORDER_DETAIL实体

## 1.2 实体联系模型

1、用户可以点外卖，因此用户和菜品之间就有一个“购买”的联系，一个用户可以购买多个菜品且一个菜品可以被多个用户购买，所以菜品和用户是多对多的对应关系。外卖店可以“出售”多个菜品，因此外卖店和菜品之间存在一对多的对应关系。对应关系下图1.2.1。

![img](用户_菜品_外卖店关系.png) 

 										图 1.2.1 用户、菜品、外卖店关系简图

2、销售关系可以细分为订单和订单详单两个实体，订单中存储下单时间、送达时间、订单金额信息，订单详单中存储菜品ID、菜品数量、菜品价格信息。一个用户可以拥有多个订单，每个订单包含多个订单详单，一个菜品可以被多个订单详单包含。对应关系下图1.2.2。

![img](订单_订单详单关系.png) 

​													图 1.2.2 订单、订单详单关系简图

# 2 数据表的设计

## 2.1 用户表设计

外卖用户表（WM_USER）包括ID、USER_NAME、USER_PWD、USER_ADDRESS、USER_PHONE、BALANCE、REGIST_DATE属性。详见下表2.1.1。

| 字段名       | 数据类型          | 可以为空 | 注释                       |
| ------------ | ----------------- | -------- | -------------------------- |
| ID           | NUMBER(6,0)       | NO       | 用户ID,主键                |
| USER_NAME    | VARCHAR2(40 BYTE) | NO       | 用户账户，非空             |
| USER_PWD     | VARCHAR2(40 BYTE) | NO       | 用户密码，非空             |
| USER_ADDRESS | VARCHAR2(80 BYET) | NO       | 用户收货地址               |
| USER_PHONE   | VARCHAR2(40 BYTE) | NO       | 用户电话号码               |
| BALANCE      | NUMBER(8,2)       | YES      | 用户账号余额，默认为0      |
| REGIST_DATE  | DATE              | NO       | 注册日期，采用分区存储方式 |

​															表 2.1.1 用户表USER

## 2.2 外卖店表设计

外卖店表（SHOP）包括ID、SHOP_NAME、SHOP_PHONE、SELL_NUM、SCORE属性。详见下表2.2.1。

| 字段名     | 数据类型          | 可以为空 | 注释                                |
| ---------- | ----------------- | -------- | ----------------------------------- |
| ID         | NUMBER(6,0)       | NO       | 外卖店ID,主键                       |
| SHOP_NAME  | VARCHAR2(40 BYTE) | NO       | 外卖店名，非空                      |
| SHOP_PHONE | VARCHAR2(40 BYTE) | NO       | 外卖店电话号码                      |
| SELL_NUM   | NUMBER(8,2)       | YES      | 外卖店销售总量，默认为0             |
| SCORE      | NUMBER(8,2)       | NO       | 外卖店评分，只能取值：1，2，3，4，5 |

​														表 2.2.1 外卖店表SHOP

## 2.3 菜品表设计

菜品表（DISH）包括ID、DISH_NAME、DISH_PRICE、SHOP_ID属性。详见下表2.3.1。

| 字段名     | 数据类型          | 可以为空 | 注释                               |
| ---------- | ----------------- | -------- | ---------------------------------- |
| ID         | NUMBER(6,0)       | NO       | 菜品ID,主键                        |
| DISH_NAME  | VARCHAR2(40 BYTE) | NO       | 菜品名，非空                       |
| DISH_PRICE | NUMBER(8,2)       | NO       | 菜品售价                           |
| SHOP_ID    | NUMBER(6,0)       | NO       | 所属的外卖店号，外卖店表SHOP的外键 |

​														表 2.3.1 菜品表DISH

## 2.4 订单表设计

外卖订单表（WM_ORDER）包括ID、USER_ID、SHOP_ID、ORDER_TIME、SUM_PRICE属性。详见下表2.4.1。

| 字段名     | 数据类型     | 可以为空 | 注释                                    |
| ---------- | ------------ | -------- | --------------------------------------- |
| ID         | NUMBER(10,0) | NO       | 订单编号,主键,值来自于序列:SEQ_ORDER_ID |
| USER_ID    | NUMBER(6,0)  | NO       | 用户ID，用户表USER的外键                |
| SHOP_ID    | NUMBER(6,0)  | NO       | 外卖店ID，外卖店表SHOP的外键            |
| ORDER_TIME | DATE         | NO       | 下单时间，应该采用分区存储方式          |
| SUM_PRICE  | SUM_PRICE    | YES      | 外卖订单总金额，默认为0                 |

​														表 2.4.1 订单表ORDER

## 2.5 订单详单表设计

外卖订单详单表（WM_ORDER_DETAIL）包括ID、ORDER_ID、DISH_NAME、DISH_NUM,DISH_PRICE属性。详见下表2.5.1。

| 字段名     | 数据类型          | 可以为空 | 注释                                                |
| ---------- | ----------------- | -------- | --------------------------------------------------- |
| ID         | NUMBER(10,0)      | NO       | 订单详单编号,主键,值来自于序列:SEQ_ORDER_DETAILS_ID |
| ORDER_ID   | NUMBER(10,0)      | NO       | 订单ID，订单表ORDER的外键                           |
| DISH_NAME  | VARCHAR2(40 BYTE) | NO       | 菜品名                                              |
| DISH_NUM   | NUMBER(8,2)       | NO       | 菜品销售数量，必须大于0                             |
| DISH_PRICE | NUMBER(8,2)       | NO       | 菜品售价                                            |

​													表 2.5.1 订单详单表ORDER_DETAIL



# 3 用户创建与表空间分配

## 3.1 表空间创建

本次实验需要用到wh01、wh02两个表空间，下面用SYSTEM用户创建表空间wh01和wh02，给wh01表空间分配两个数据文件：pdbtest_wh01_1.dbf和pdbtest_wh01_2.dbf，这两个数据文件初始大小都为100M，即表空间的初始大小为200M。给wh02表空间分配两个数据文件：pdbtest_wh02_1.dbf和pdbtest_wh02_2.dbf，这两个数据文件初始大小都为100M，即表空间的初始大小为200M

代码如下：

```sql
CREATE TABLESPACE wh01 DATAFILE

'E:\app\root\oradata\orcl\pdborcl\pdbtest_wh01_1.dbf'

 SIZE 100M AUTOEXTEND ON NEXT 256M MAXSIZE UNLIMITED，

'E:\app\root\oradata\orcl\pdborcl\pdbtest_wh01_2.dbf' 

 SIZE 100M AUTOEXTEND ON NEXT 256M MAXSIZE UNLIMITED

EXTENT MANAGEMENT LOCAL SEGMENT SPACE MANAGEMENT AUTO;

 

CREATE TABLESPACE wh02 DATAFILE

'E:\app\root\oradata\orcl\pdborcl\pdbtest_wh02_1.dbf'

 SIZE 100M AUTOEXTEND ON NEXT 256M MAXSIZE UNLIMITED，

'E:\app\root\oradata\orcl\pdborcl\pdbtest_wh02_2.dbf' 

 SIZE 100M AUTOEXTEND ON NEXT 256M MAXSIZE UNLIMITED

EXTENT MANAGEMENT LOCAL SEGMENT SPACE MANAGEMENT AUTO;
```

运行截图如下：

![img](表空间创建.png) 

 

## 3.2 角色和用户创建

创建shopkeeper和customer两个角色,tom和wanghao两个用户，给shopkeeper角色授予connect, resource, create view权限,给customer角色授予connect, resource权限，。用户默认使用表空间wh01,分配90M空间并将角色shopkeeper授权给用户tom,分配90M空间并将角色customer授权给用户wanghao。

代码如下：

```sql
CREATE ROLE shopkeeper;

GRANT connect,resource,CREATE VIEW TO shopkeeper;

CREATE USER tom IDENTIFIED BY 123 DEFAULT TABLESPACE wh01 TEMPORARY TABLESPACE temp;

ALTER USER tom QUOTA 90M ON wh01;

GRANT shopkeeper TO tom;

CREATE ROLE customer;

GRANT connect,resource TO customer;

CREATE USER wanghao IDENTIFIED BY 123 DEFAULT TABLESPACE wh01 TEMPORARY TABLESPACE temp;

ALTER USER wanghao QUOTA 90M ON wh01;

GRANT customer TO wanghao;
```

 

截图如下：

 

![img](用户角色创建1.png) 

![img](用户角色创建2.png)

# 4 表，程序包，函数和存储过程创建及执行计划分析

本次实验需要创建外卖用户表WM_USER、外卖店表SHOP、菜品表DISH、外卖订单表WM_ORDER、外卖订单详单表WM_ORDER_DETAIL五个表。

## 4.1 外卖用户表创建

创建外卖用户表WM_USER，id为主键，根据注册日期按范围分区,将2019年之前到2020年之间的数据存储在wh01表空间中，将2021年数据存储在wh02表空间中。

代码如下：

```sql
CREATE TABLE  WM_USER

(

 ID NUMBER(6, 0) NOT NULL

, USER_NAME VARCHAR2(40 BYTE) NOT NULL

, USER_PWD VARCHAR2(40 BYTE) NOT NULL

, USER_ADDRESS VARCHAR2(80 BYTE) NOT NULL

, USER_PHONE VARCHAR2(40 BYTE) NOT NULL

, BALANCE NUMBER(8,2) DEFAULT 0

, REGIST_DATE DATE NOT NULL

, CONSTRAINT USER_PK PRIMARY KEY

 (

    ID

 )

 USING INDEX

 (

   CREATE UNIQUE INDEX USER_PK ON WM_USER (ID ASC)

   LOGGING

   TABLESPACE WH01

   PCTFREE 10

   INITRANS 2

   STORAGE

   (

    BUFFER_POOL DEFAULT

   )

   NOPARALLEL
 )

 ENABLE

)

TABLESPACE WH01

PCTFREE 10

INITRANS 1

STORAGE

(

 BUFFER_POOL DEFAULT

)

NOCOMPRESS

NOPARALLEL

PARTITION BY RANGE (REGIST_DATE)

(

 PARTITION PARTITION_2019 VALUES LESS THAN (TO_DATE(' 2020-01-01 00:00:00', 'SYYYY-MM-DD HH24:MI:SS', 'NLS_CALENDAR=GREGORIAN'))

 NOLOGGING

 TABLESPACE WH01

 PCTFREE 10

 INITRANS 1

 STORAGE

 (

  BUFFER_POOL DEFAULT

 )
 NOCOMPRESS NO INMEMORY

, PARTITION PARTITION_2020 VALUES LESS THAN (TO_DATE(' 2021-01-01 00:00:00', 'SYYYY-MM-DD HH24:MI:SS', 'NLS_CALENDAR=GREGORIAN'))

 NOLOGGING

 TABLESPACE WH01

 PCTFREE 10

 INITRANS 1

 STORAGE

 (

  BUFFER_POOL DEFAULT

 )

 NOCOMPRESS NO INMEMORY

, PARTITION PARTITION_2021 VALUES LESS THAN (TO_DATE(' 2022-01-01 00:00:00', 'SYYYY-MM-DD HH24:MI:SS', 'NLS_CALENDAR=GREGORIAN'))

 NOLOGGING

 TABLESPACE WH02

 PCTFREE 10

 INITRANS 1

 STORAGE

 (

  BUFFER_POOL DEFAULT

 )

 NOCOMPRESS NO INMEMORY
);
```

运行结果截图如下：

![img](创建用户表.png) 

## 4.2 外卖店表创建

创建外卖用户表SHOP，id为主键。

代码如下：

```sql
CREATE TABLE SHOP

(

 ID NUMBER(6, 0) NOT NULL 

, SHOP_NAME VARCHAR2(40 BYTE) NOT NULL

, SHOP_PHONE VARCHAR2(40 BYTE) NOT NULL

, SELL_NUM NUMBER(8,2) DEFAULT 0

, SCORE NUMBER(8,2) NOT NULL

, CONSTRAINT SHOP_PK PRIMARY KEY 

 (

  ID 

 )

 USING INDEX 

 (

   CREATE UNIQUE INDEX SHOP_PK ON SHOP (ID ASC) 

   LOGGING 

   TABLESPACE WH01

   PCTFREE 10 

   INITRANS 2 

   STORAGE 

   ( 

​    BUFFER_POOL DEFAULT 

   ) 

   NOPARALLEL 

 )

 ENABLE 

) 

LOGGING 

TABLESPACE WH01

PCTFREE 10 

INITRANS 1 

STORAGE 

( 

 BUFFER_POOL DEFAULT 

) 

NOCOMPRESS 

NO INMEMORY 

NOPARALLEL;
```

运行结果截图如下：

![img](创建外卖店表.png) 

## 4.3 菜品表创建

创建菜品表DISH，id为主键，SHOP_ID为外卖店表SHOP的外键。

代码如下：

```sql
CREATE TABLE DISH 

(

 ID NUMBER(6, 0) NOT NULL 

, DISH_NAME VARCHAR2(40 BYTE) NOT NULL

, DISH_PRICE NUMBER(8,2) NOT NULL

, SHOP_ID NUMBER(6,0) NOT NULL

, CONSTRAINT DISH_PK PRIMARY KEY 

 (

  ID 

 )

 USING INDEX 

 (

   CREATE UNIQUE INDEX DISH_PK ON DISH (ID ASC) 

   LOGGING 

   TABLESPACE WH01

   PCTFREE 10 

   INITRANS 2 

   STORAGE 

   ( 

​    BUFFER_POOL DEFAULT 

   ) 

   NOPARALLEL 

 )

 ENABLE

, CONSTRAINT SHOP_DISH FOREIGN KEY

 (

 SHOP_ID 

 )

 REFERENCES SHOP

 (

 ID 

 )

 ENABLE 

)  

LOGGING 

TABLESPACE WH01

PCTFREE 10 

INITRANS 1 

STORAGE 

( 

 BUFFER_POOL DEFAULT 

) 

NOCOMPRESS 

NO INMEMORY 

NOPARALLEL;
```

运行结果截图如下：

![img](创建菜品表.png) 

## 4.4 外卖订单表创建

创建外卖订单表WM_ORDER，id为主键，外卖订单表按范围分区进行存储，将2019年之前到2020年的数据存储在wh01表空间中，将2021年的数据存储在wh02表空间中。

代码如下：

```sql
CREATE TABLE WM_ORDER

(

 ID NUMBER(10,0) NOT NULL

, ORDER_TIME DATE NOT NULL

, SUM_PRICE NUMBER(8,2) DEFAULT 0

, USER_ID NUMBER(6,0) NOT NULL

, SHOP_ID NUMBER(6,0) NOT NULL

, CONSTRAINT ORDER_PK PRIMARY KEY

 (

  ID

 )

 USING INDEX

 (

   CREATE UNIQUE INDEX ORDER_PK ON WM_ORDER (ID ASC)

   LOGGING

   TABLESPACE WH01

   PCTFREE 10

   INITRANS 2

   STORAGE

   (

​    BUFFER_POOL DEFAULT

   )

   NOPARALLEL

 )

 ENABLE

)

TABLESPACE WH01

PCTFREE 10

INITRANS 1

STORAGE

(

 BUFFER_POOL DEFAULT

)

NOCOMPRESS

NOPARALLEL

PARTITION BY RANGE (ORDER_TIME)

(

 PARTITION PARTITION_2019 VALUES LESS THAN (TO_DATE(' 2020-01-01 00:00:00', 'SYYYY-MM-DD HH24:MI:SS', 'NLS_CALENDAR=GREGORIAN'))

 NOLOGGING

 TABLESPACE WH01

 PCTFREE 10

 INITRANS 1

 STORAGE

 (

  BUFFER_POOL DEFAULT

 )

 NOCOMPRESS NO INMEMORY

, PARTITION PARTITION_2020 VALUES LESS THAN (TO_DATE(' 2021-01-01 00:00:00', 'SYYYY-MM-DD HH24:MI:SS', 'NLS_CALENDAR=GREGORIAN'))

 NOLOGGING

 TABLESPACE WH01

 PCTFREE 10

 INITRANS 1

 STORAGE

 (

  BUFFER_POOL DEFAULT

 )

 NOCOMPRESS NO INMEMORY

, PARTITION PARTITION_2021 VALUES LESS THAN (TO_DATE(' 2022-01-01 00:00:00', 'SYYYY-MM-DD HH24:MI:SS', 'NLS_CALENDAR=GREGORIAN'))

 NOLOGGING

 TABLESPACE WH02

 PCTFREE 10

 INITRANS 1

 STORAGE

 (

  BUFFER_POOL DEFAULT

 )

 NOCOMPRESS NO INMEMORY

);
```

运行结果截图如下：

![img](创建订单表.png) 

## 4.5 外卖订单详单表创建

创建外卖订单表WM_ORDER_DETAIL，id为主键，使用引用分区进行存储让其数据也按主表WM_ORDER的日期范围进行分区存储，通过引用分区语句"PARTITION BY REFERENCE(order_detail_fk1)",利用外键order_detail_fk1关联到主表wm_order，使从表按主表的分区方案与主表存储在同一分区中。

代码如下：

```sql
CREATE TABLE wm_order_detail

(

id NUMBER(10, 0) NOT NULL

, order_id NUMBER(10, 0) NOT NULL

, dish_name VARCHAR2(40 BYTE) NOT NULL

, dish_num NUMBER(8, 2) NOT NULL

, dish_price NUMBER(8, 2) NOT NULL

, CONSTRAINT order_detail_fk1 FOREIGN KEY  (order_id)

REFERENCES wm_order  (  id  )

ENABLE

)

TABLESPACE wh01

PCTFREE 10 INITRANS 1

STORAGE (BUFFER_POOL DEFAULT )

NOCOMPRESS NOPARALLEL

PARTITION BY REFERENCE (order_detail_fk1);
```

运行结果截图：

![img](创建外卖详单表.png) 

 

 

## 4.6 随机生成表数据

本次实验使用PL/SQL完成随机数据的生成，使用for循环5w次，当循环次数小于1w时生成1w条用户数据，然后生成5w条外卖店、菜品、订单数据，每个订单关联三个订单详单。外卖订单和订单详单的生成顺序是生成外卖订单数据后给这个订单生成三个订单详单，最后计算得出的订单详单中菜品数量乘菜品价格的总和就是外卖订单表中订单总价。

部分代码如下：

```sql
declare

 --定义外卖用户变量

 v_user_id number(6);

 v_user_name varchar2(100);

 v_user_pwd varchar2(100);

 ......

begin

 v_order_detail_id:=0;

 delete from wm_order_detail;

 delete from wm_order;

 for i in 1..50000

 loop

  if i mod 3 =0 then

   dt:=to_date('2019-6-1','yyyy-mm-dd')+(i mod 60); --PARTITION_2019

  elsif i mod 3 =1 then

   dt:=to_date('2020-6-1','yyyy-mm-dd')+(i mod 60); --PARTITION_2020

  else

   dt:=to_date('2021-6-1','yyyy-mm-dd')+(i mod 60); --PARTITION_2021

  end if;           

  --插入用户数据

 

  ......

  

   --计算每个订单的应收金额：

  select sum(DISH_NUM*DISH_PRICE) into m from WM_ORDER_DETAIL where ORDER_ID=v_order_id;

  if m is null then

   m:=0;

  end if;

  UPDATE WM_ORDER SET SUM_PRICE = m  WHERE ID=v_order_id;

  IF I MOD 1000 =0 THEN

   commit; 

  END IF;

 end loop;

end;
```

运行结果截图如下：

![img](插入表数据.png) 

查询每个表数据的数量：

![img](数据数量.png) 

## 4.7 创建程序包、函数和过程及执行计划分析

1、创建程序包waimai_package，设计了函数get_userpaybydate（）来查询用户点外卖花费的总金额，设计了过程add_dish（）来根据外卖店id添加菜品。

代码如下：

```sql
create or replace PACKAGE waimai_package Is

  function get_userpay(user_id number) return number;

  procedure add_dish(shop_id number,dish_name varchar2,dish_price number);

end waimai_package;
```

运行结果截图如下：

![img](创建程序包.png) 

2、创建程序体定义函数和过程。

部分代码如下：

```sql
create or replace PACKAGE body waimai_package Is

 

   function get_userpaybydate(user_id number) return number as

    begin

   declare sum_pay number;

		query_sql varchar2(200);

     begin

    query_sql := 'select sum(sum_price) from wm_order where user_id=' || user_id  ;

      execute immediate query_sql into sum_pay;

			 return sum_pay;

      end;

    end get_userpaybydate;

 

    procedure add_dish(shop_id number,dish_name varchar2,dish_price number) as

      begin

       declare maxId number;

       begin

        select max(id) into maxId from dish;

        insert into dish values(maxId+1,dish_name,dish_price,shop_id);

       commit;

       end;

      end add_dish;

  end waimai_package;
```

运行结果截图如下：

![img](创建程序体.png) 

3、使用自定义函数get_userpaybydate（）查询id为100的用户点的所有外卖的总金额。使用自定义存储过程add_dish()插入菜品数据。

运行结果截图如下：

![img](存储函数结果.png) 

![img](存储过程结果.png) 

![img](存储过程结果2.png) 

4、查询用户id为100的用户在2019-2020年间点外卖的所有订单的SQL语句执行计划分析。其中Rows=1，cost成本=554，一次全表搜索filter。在对于此实验中的大量数据执行查询操作时，由于我们按订单时间建立了范围分区，所以查询只需要在对应的Pstart=1和Pstop=2两个分区中进行查询，避免了对全部数据的搜索，极大提升了查询效率。

![img](执行计划.png) 

5、查询表空间使用情况

![img](表空间使用情况.png) 

 



# 5 备份与恢复

查看pdborcl数据库的数据文件。

代码和运行结果如下：

```sql
sqlplus system/123 @ 127.0.0.1/pdborcl

SELECT NAME FROM v$datafile

UNION ALL

SELECT MEMBER AS NAME FROM v$logfile

UNION ALL

SELECT NAME FROM v$controlfile;
```

![img](数据文件.png) 

## 5.1 全库0级备份

用sys用户以专用模式登录后进行全库0级备份（只作一次）。

代码如下：

```sql
rman target sys/123@127.0.0.1/orcl:dedicated

run{

configure retention policy to redundancy 1;

configure controlfile autobackup on;

configure controlfile autobackup format for device type disk to 'E:/APP/ROOT/rman_backup/%F';

configure default device type to disk;

crosscheck backup;

crosscheck archivelog all;

allocate channel c1 device type disk;

allocate channel c2 device type disk;

allocate channel c3 device type disk;

backup incremental level 0 database format 'E:/APP/ROOT/rman_backup/level0_%d_%T_%U.bak';

report obsolete;

delete noprompt obsolete;

delete noprompt expired backup;

delete noprompt expired archivelog all;

release channel c1;

release channel c2;

release channel c3;

}
```

运行结果截图如下：

![img](全库0级备份.png) 

## 5.2 全库1级增量备份

代码如下：

```sql
run{

configure retention policy to redundancy 1;

configure controlfile autobackup on;

configure controlfile autobackup format for device type disk to 'E:/APP/ROOT/rman_backup/%F';

configure default device type to disk;

crosscheck backup;

crosscheck archivelog all;

allocate channel c1 device type disk;

allocate channel c2 device type disk;

allocate channel c3 device type disk;

backup incremental level 1 database format 'E:/APP/ROOT/rman_backup/level1_%d_%T_%U.bak';

report obsolete;

delete noprompt obsolete;

delete noprompt expired backup;

delete noprompt expired archivelog all;

release channel c1;

release channel c2;

release channel c3;

}
```

运行结果截图如下：

![img](全库1级增量备份.png) 

## 5.3 全库完全恢复

代码如下：

```sql
sqlplus / as sysdba

SQL> select file_name from dba_data_files;

 

\- 全库停机

rman target /

RMAN> shutdown immediate;  或者 shutdown abort;

RMAN> exit

 

\- 数据文件改名，模拟文件损失

del E:/app/root/oradata/orcl/pdborcl/pdbtest_wh01_1.dbf 

 

\- 全库恢复

rman target /

RMAN> startup mount;

RMAN> restore database;

RMAN> recover database;

RMAN> alter database open;
```

运行结果截图如下：

![img](全库完全恢复.png) 

## 5.4 单库完全恢复

代码如下：

```sql
- system登录到pdborcl，查看pdborcl的数据文件

 sqlplus system/123@127.0.0.1/pdborcl

SQL> select file_name from dba_data_files;

\- 关闭pdborcl数据库

rman target sys/123@127.0.0.1/orcl:dedicated

RMAN> alter pluggable database pdborcl close;

RMAN> exit;

\- 模拟文件损失

del E:/app/root/oradata/orcl/pdborcl/pdbtest_wh01_1.dbf 

del E:/app/root/oradata/orcl/pdborcl/pdbtest_wh01_2.dbf 

\- 单库完全恢复

rman target sys/123@127.0.0.1/orcl:dedicated

RMAN> restore pluggable database pdborcl;

RMAN> recover pluggable database pdborcl;

RMAN> alter pluggable database pdborcl open;

RMAN> exit;
```

运行结果截图如下：

![img](单库完全恢复.png) 