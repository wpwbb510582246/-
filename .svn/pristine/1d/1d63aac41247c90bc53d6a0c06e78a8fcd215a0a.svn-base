# 学习笔记(MongoDB)

## 1、常用命令

### 1.1 数据备份、恢复、导入、导出

- 备份

  ```shell
  cd ../db_backup
  rm -rf ./*
  mongodump --host 127.0.0.1 --port 27017
  ```

- 恢复

  ```shell
  db_name=$1
  mongorestore -h 127.0.0.1:27017 -d $db_name ../db_backup/dump/$db_name
  ```

- 导入

  ```shell
  mongoimport -d wcx2020 -c articles -file D:\mongodbBackup\mongosql.json --type json
  # -d ：数据库名
  # -c ：表名
  # -file ：输入的文件名
  # --type ： 输入的格式，默认为json
  ```

  

- 导出

  ```shell
  mongoexport -d zhilian -c jiangsu -o jiangsu.csv
  mongoexport -d myDB -c user -f _id,name,password,adress --type=csv -o ./user.csv
  mongoexport -v --host 192.168.2.205:27017 -d "pocket" -c "movie" --out movie.csv --fieldFile movie.txt --type csv
  # -d ：数据库名
  # -c ：表名
  # -o ：输出的文件名
  # --type ： 输出的格式，默认为json
  # -f ：输出的字段，如果-type为csv，则需要加上-f "字段名"
  # -q：过滤条件，json字符串
  ```

  