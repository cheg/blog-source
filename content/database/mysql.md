---
title: "Mysql学习笔记"
date: 2018-01-08T12:22:07+08:00
draft: false
---

<!--more-->

## 使用数据库

### 登录
```bash
mac: ~ $ mysql -h hostname -u username -p   # 标准形式
mac: ~ $ mysql -u root -p       # 本机登录时使用
mac: ~ $ mysql -u root          # 本机登录且没有密码时使用
```

### 查看所有数据库
```sql
mysql>show databases;
```

### 创建一个数据库
```sql
mysql>create database dbname character set utf-8;
```

### 选择要操作的数据库
```sql
# 1. 登录时指定数据库名
mac: ~ $ mysql -D dbname -h hostname -u username -p
# 2. 登录后使用use命令
mysql>use dbname;
```

### 创建数据库表
```sql
# 1. 登录时从文件导入
mac: ~ $mysql -D dbname -u root -p < createtable.sql
# 2. 登录后用create table命令
mysql>create table tablename
(   
    id int unsigned not null auto_increment primary key,
    name char(8) not null,
    sex char(4) not null,
    age tinyint unsigned not null,
    tel char(13) null defaut "-"
);

# 语句解说：
    "id" 为列的名称;
    "int" 指定该列的类型为 int(取值范围为 -8388608到8388607), 在后面我们又用 "unsigned" 加以修饰, 表示该类型为无符号型, 此时该列的取值范围为 0到16777215;
    "not null" 说明该列的值不能为空, 必须要填, 如果不指定该属性, 默认可为空;
    "auto_increment" 需在整数列中使用, 其作用是在插入数据时若该列为 NULL, MySQL将自动产生一个比现存值更大的唯一标识符值。在每张表中仅能有一个这样的值且所在列必须为索引列。
    "primary key" 表示该列是表的主键, 本列的值必须唯一, MySQL将自动索引该列。
    "char(8)" 表示存储的字符长度为8, 
    "tinyint" 的取值范围为 -127到128, default 属性指定当该列值为空时的默认值。
```
### 查看表信息
```sql
mysql>show tables;          # 查看已创建的表的名称
mysql>describe tablename;   # 查看创建的表的详细信息
```

## 操作数据库

### 向表中插入数据
```sql
mysql>insert into tablename(col1, col2, ...) values (value1, value2, ...);
# 如向表students中插入一条记录
mysql>insert into students values (NULL, "张三", "男", 20, "13388888888");
mysql>insert into students(name, sex, age) values ("李四", "女", 22);
```
### 查询表中的数据
```sql
mysql>select * from tablename;
mysql>select name, sex, age from students where age>18;
```

### 更新表中的数据
```sql
mysql>update students set tel=default where id=5;
mysql>update students set age=age+1;
mysql>update students set name="王五", age=18 where tel="13388888888";
```

### 删除表中的数据
```sql
mysql>delete from students where id=2;
mysql>delete from students where age<18;
mysql>delete from students;                 # 删除表中所有数据
```

## 创建后表的修改

### 添加列
```sql
mysql>alter table tablename add colname char(60);       # datatype为char(60)
mysql>alter table students add birthday date after age; # 在age后加入birthday
```
### 修改列
```sql
mysql>alter table tablename change colname newcolname datatype;
mysql>alter table students change tel telphone char(13) default "-";
mysql>alter table students change name name char(16) not null;  # name列数据类型改为char(16)
```

### 删除列
```sql
mysql>alter table tablename drop colname
mysql>alter table students drop birthday;
```

### 重命名表
```sql
mysql>alter table tablename rename newtablename;
mysql>alter table students rename workers;
```

### 删除整张表
```sql
mysql>drop table tablename;
mysql>drop table workers;
```

### 删除事个数据库
```sql
mysql>drop database dbname;
mysql>drop database samp_db;
```

