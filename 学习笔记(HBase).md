# 学习笔记(HBase)

## 1、相关命令

### 1.1 建表

```sql
-- 语法
create '<table>', 'column_family'[, 'column_family', ...]
-- 示例
create 'test', 'f1', 'f2'
```

### 1.2 插入数据

```sql
-- 语法
put '<table>', '<row_key>', '<column_family:column_qualifier>', '<cell_value>'[, <timestamp>]
-- 示例
put 'test', 'r1', 'f1:c1', 'value1'
put 'test', 'r1', 'f1:c2', 'value1'
put 'test', 'r1', 'f1:c3', 'value1'
put 'test', 'r2', 'f1:c1', 'value1'
put 'test', 'r2', 'f1:c2', 'value1'
put 'test', 'r2', 'f1:c3', 'value1'
```

### 1.3 查询数据

```sql
-- 语法
-- 扫描全表
scan '<table>'
-- 示例
scan 'test'

-- 语法
-- 查询一个单元格
get '<table>', '<row_key>'[, {<property> => <value>, ...}]
--示例
get 'test', 'r1', {COLUMN => 'f1:c1'}
```

### 1.4 更新数据

```sql
-- 示例
put 'test', 'r1', 'f1:c1', 'update value'
```

### 1.5 删除数据

```sql
-- 语法
-- 删除单元格
delete '<table>', '<row_key>', '<column_family:column_qualifier>'
-- 示例
delete 'test', 'f1:c1'

-- 语法
-- 删除整行数据
deleteall '<table_name>', '<row_key>'
-- 示例
deleteall 'test', 'r1'
```

