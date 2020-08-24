# 学习笔记(Oracle)

## 1、to_date()和to_char()

- 转换的格式：

  表示year的：y 表示年的最后一位 yy 表示年的最后2位 yyy 表示年的最后3位 yyyy 用4位数表示年

  表示month的：mm 用2位数字表示月；mon 用简写形式 比如11月或者nov ；month 用全称 比如11月或者november

  表示day的：dd 表示当月第几天；ddd表示当年第几天；dy 当周第几天 简写 比如星期五或者fri；day当周第几天 全写

  比如星期五或者friday。

  表示hour的：hh 2位数表示小时 12进制； hh24 2位数表示小时 24小时

  表示minute的：mi 2位数表示分钟

  表示second的：ss 2位数表示秒 60进制

  表示季度的：q 一位数 表示季度 （1-4）

  另外还有ww 用来表示当年第几周 w用来表示当月第几周。

  24小时制下的时间范围：00：00：00-23：59：59

  12小时制下的时间范围：1：00：00-12：59：59

- 示例：

```sql
select to_char(sysdate,'yyyy-mm-dd hh24:mi:ss') from dual  //显示：2008-11-07 13:22:42
select to_date('2005-12-25,13:25:59','yyyy-mm-dd,hh24:mi:ss') from dual //显示：2005-12-25 13:25:59
```

