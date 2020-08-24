# 学习笔记(Hive)

## 1、相关命令

### 1.1 连接Hive

```shell
beeline -u jdbc:hive2://192.168.2.205:10000/default
```

### 1.2 操作分区表

#### 1.2.1 创建分区表

```shell
# movie
CREATE TABLE movie (
s_id string,
acquisition_time string,
actors string,
description string,
directors string,
duration string,
id string,
language string,
name string,
nickname string,
region string,
release_date string,
score string,
sources string,
src string,
type string,
type2 string,
update_status string,
update_time string
)
PARTITIONED BY (date_id string)
row format delimited fields terminated by ','
stored as textfile
LOCATION '/user/hive/warehouse/pocket.db/movie';
```

### 1.3 msck

```sql
-- 当一个分区表先在hdfs上创建目录，上后再上传文件到hdfs指定分区目录时，hive不回识别该分区目录，需要使用msck来让hive创建相应分区，这样便可以从hive中查询到相应分区中的数据
-- msck只能用于外表，且只能在分区不存在时创建分区，而不能删除分区
msck repair table employees;
```