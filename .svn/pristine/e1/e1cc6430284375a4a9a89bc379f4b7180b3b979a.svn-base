# 学习笔记(Shell)

## 1、常用命令

### 1.1 开放端口

```shell
# 开放80端口：
firewall-cmd --add-port=80/tcp

# 开放8080-8085端口：
firewall-cmd --add-port=8080-8085/tcp
firewall-cmd --reload
```

### 1.2 杀死指定名称的进程

```shell
ps -ef | grep movie | grep -v grep |awk '{print $2}' | xargs kill -9
```

### 1.3 删除除了某个文件或文件夹外的所有文件

```shell
ls | grep -v nginx-1.17.0 | xargs rm -rf
```

### 1.4 crontab用法

```shell
# 查看crontab运行日志
cat /var/log/cron

# 每天02:00执行任务
0 2 * * * /bin/sh backup.sh
# 每天5:00和17:00执行任务
0 5,17 * * * /scripts/script.sh
# 每分钟执行一次任务
* * * * *  /scripts/script.sh
# 每周日17:00 执行任务
0 17 * * sun  /scripts/script.sh
# 每10min执行一次任务
*/10 * * * * /scripts/monitor.sh
# 在特定的某几个月执行任务(在一月、五月、八月每天每分钟执行任务)
* * * jan,may,aug * /script/script.sh
# 在特定的某几天执行任务(在每周五、周日的17点执行任务)
0 17 * * sun,fri /script/scripy.sh
# 每四个小时执行一个任务
0 */4 * * * /scripts/script.sh
# 每周一、周日4:00和17:00执行任务
0 4,17 * * sun,mon /scripts/script.sh
# 每个30秒执行一次任务
* * * * * sleep 30; /scripts/script.sh
# 多个任务在一条命令中配置
* * * * * /scripts/script.sh; /scripts/scrit2.sh
# 每年执行一次任务(@yearly 类似于“0 0 1 1 *”。它会在每年的第一分钟内执行，通常我们可以用这个发送新年的问候。)
@yearly /scripts/script.sh
# 系统重启时执行
@reboot /scripts/script.sh
# 将 Cron 结果重定向的特定的账户(默认情况下，cron 只会将结果详情发送给 cron 被制定的用户。如果需要发送给其他用户，可以通过如下的方式)
MAIL=bob
0 2 * * * /script/backup.sh
# 将所有的 cron 命令备份到文本文件当中
# 1、备份cron到文件中
crontab -l > cron-backup.txt
# 2、移除当前的cron
crontab -r
# 3、从text file中恢复
crontab cron-backup.txt
```

### 1.5 vim常用快捷键

- 快速移动光标：
  - 行首：`shift+6`
  - 行尾：`shift+4`

### 1.6 sed

- 删除文件第一行

  ```shell
  # 删除文件第一行，sed -i表示将改动直接写入到原文件里
  sed -i '1d'  txtfilename
  ```


### 1.7 date

```shell
date +"%Y-%m-%d %H:%M:%S"
date -d "+1second" +"%Y-%m-%d %H:%M:%S"
date -d "+1minute" +"%Y-%m-%d %H:%M:%S"
date -d "+1hour" +"%Y-%m-%d %H:%M:%S"
date -d "+1day" +"%Y-%m-%d %H:%M:%S"
date -d "+1month" +"%Y-%m-%d %H:%M:%S"
date -d "+2year" +"%Y-%m-%d %H:%M:%S"
date -d"+1day 2015-04-01" +%Y-%m-%d
```



## 2、示例脚本

### 2.1 启动爬虫

```shell
#!bin/bash
# 说明
echo '本程序是启动爬虫程序'
echo '影视：腾讯视频(tencent)、优酷视频(youku)、爱奇艺视频(iqiyi)、最大资源(zuida)、酷云资源(kuyun)、永久资源(yongjiu)、ok资源(ok)、135资源(ziyuan135)、33uu资源(ziyuan33uu)'
echo '电视：好趣网(tv)'
echo '戏曲：戏曲屋(drama)'
echo '小品：小品屋(piece)、相声小品网(piece2)'
echo ''
echo 示例：
echo 启动全部爬虫：sh start_spiders.sh
echo 启动腾讯视频爬虫：sh start_spiders.sh tencent
echo 启动腾讯视频、优酷视频爬虫：sh start_spiders.sh tencent,youku
echo ''

# 程序
cd '/Users/weipeng/Personal/Projects/PocketFilm/Spider/PocketLifeSpider/PocketLifeSpider/shell'
current_path=$("pwd")
parent_current_path=$(dirname ${current_path})
echo ${parent_current_path}
cd ${parent_current_path}
string=$1
if [ ! $2 ]; then
	type="all"
else
	type=$2
fi
array=[]
if [ $1 == "all" ]; then
	string="tencent,youku,iqiyi,zuida,kuyun,yongjiu,ok,ziyuan135,ziyuan33uu"
elif [ $1 == 'daily' ]; then
	string="tv,drama,piece,piece2,piece3,piece4"
elif [ $1 == 'temp' ]; then
	string="tencent,youku,iqiyi"
fi
array=(${string//,/ })
if [ $type == 'all' ]; then
# 爬取全部数据
i=0
while :
do
	var=${array[$i]}
	if [ $i == 0 ]; then
	last=${var}
	fi
    #监控服务是是否存活，这里是通过监控端口来监控服务，这里也可以替换为其他服务
	pid=`ps -ef | grep "scrapy crawl ${last}" | grep -v grep |awk '{print $2}'`
	echo "scrapy crawl ${last}"
	echo $pid
	if [ ! $pid ]
	then
	# 启动服务
	echo ${last}' 运行完成，正在启动 '${var} 
	# 获取当前目录
	file=${parent_current_path}/documentations/logs/${var}-${type}.txt
	# -f 参数判断 $file 是否存在
	if [ ! -f ${file} ]; then
 	touch ${file}
	fi
	nohup /Library/Frameworks/Python.framework/Versions/3.7/bin/scrapy crawl ${var} > ${file} 2>&1 &
	echo ${var} ' 启动成功'
	i=$(($i+1))
	echo $i
	if [ $i == ${#array[@]} ]; then
	break
	fi
	last=$var
	fi
	sleep 10
done
else
# 爬取最新数据
while :
do
i=0
while :
do
	var=${array[$i]}
	if [ $i == 0 ]; then
	last=${var}
	fi
    #监控服务是是否存活，这里是通过监控端口来监控服务，这里也可以替换为其他服务
	pid=`ps -ef | grep "scrapy crawl ${last} -a target=latest" | grep -v grep |awk '{print $2}'`
	echo "scrapy crawl ${last} -a target=latest"
	echo $pid
	if [ ! $pid ]
	then
	# 启动服务
	echo ${last}' 运行完成，正在启动 '${var} 
	# 获取当前目录
	file=${parent_current_path}/documentations/logs/${var}-${type}.txt
	# -f 参数判断 $file 是否存在
	if [ ! -f ${file} ]; then
 	touch ${file}
	fi
	nohup /Library/Frameworks/Python.framework/Versions/3.7/bin/scrapy crawl ${var} -a target=latest > ${file} 2>&1 &
	echo ${var} ' 启动成功'
	i=$(($i+1))
	echo $i
	if [ $i == ${#array[@]} ]; then
	break
	fi
	last=$var
	fi
	sleep 10
done
sleep 600
done
fi
```

### 2.2 输出指定日期范围内的所有日期

```shell
#!/bin/bash
# FileName: alldateduringtwodays1.sh
# Description: Print all the date during the two days you inpute.
# Simple Usage: ./alldateduringtwodays1.sh 2017-04-01 2017-06-14 or ./alldateduringtwodays1.sh 20170401 20170614 [-]
# (c) 2017.6.15 vfhky https://typecodes.com/linux/alldateduringtwodays1.html
# https://github.com/vfhky/shell-tools/blob/master/datehandle/alldateduringtwodays1.sh
 
START_DAY=$(date -d "$1" +%s)
END_DAY=$(date -d "$2" +%s)
# The spliter bettwen year,month and day.
SPLITER=${3}
 
 
# Declare an array to store all the date during the two days you inpute.
declare -a DATE_ARRAY

# Get all date between two date.
function genAlldate
{
 START_DAY_TMP=${1}
 END_DAY_TMP=${2}
 SPLITER_TMP=${3}
 if [ ! ${SPLITER_TMP} ]; then
	SPLITER_TMP=""
 fi
 I_DATE_ARRAY_INDX=0
 
 # while [[ "${START_DAY}" -le "${END_DAY}" ]]; do
 while (( "${START_DAY_TMP}" <= "${END_DAY_TMP}" )); do
 cur_day=$(date -d @${START_DAY_TMP} +"%Y${SPLITER_TMP}%m${SPLITER_TMP}%d")
 DATE_ARRAY[${I_DATE_ARRAY_INDX}]=${cur_day}
 
 # We should use START_DAY_TMP other ${START_DAY_TMP} here.
 START_DAY_TMP=$((${START_DAY_TMP}+86400))
 ((I_DATE_ARRAY_INDX++))
 
 #sleep 1
 done
}
 
# Call the funciotn to generate date during the two days you inpute.
genAlldate "${START_DAY}" "${END_DAY}" "${SPLITER}"
 
 
# [Method 1] Traverse the array.
for SINGLE_DAY in ${DATE_ARRAY[@]};
do
 echo ${SINGLE_DAY}
done
 
# If you do not need this array any more,you can unset it.
# unset DATE_ARRAY
 
exit 0
```

### 2.3 监控Mongodb服务

```shell
#!/bin/bash

#检查是否是root用户
if [ $(id -u) != "0" ]
then
        echo "Not the root user! Try using sudo command!"
        exit 1
fi
while :
do
    #监控服务是是否存活，这里是通过监控端口来监控服务，这里也可以替换为其他服务
	netstat -an | grep 127.0.0.1:27017
	if [ $? -ne 1 ]
	then
	    continue
	fi
	echo $(date +%T%n%F)" Restart mongodb Services " >> mongodb.log
	#重启服务
	sudo mongod --config=/usr/local/etc/mongod.conf
	sleep 30
done
```

### 2.4 将mongodb中的数据同步到elasticsearch

```shell
#!/bin/bash

# 将mongodb中的数据同步到elasticsearch
# 日期格式
DATE_FORMAT="+%Y%m%d"
# 起始日期
if [ ! ${1} ]; then
	START_DAY=`date -d "-1day" ${DATE_FORMAT}`
else
	START_DAY=`date -d "-1day ${1}" ${DATE_FORMAT}`
fi
# 截止日期
if [ ! ${2} ]; then
	END_DAY=${START_DAY}
else
	END_DAY=`date -d "-1day ${2}" ${DATE_FORMAT}`
fi
echo ${START_DAY}
echo ${END_DAY}
# python脚本路径
PYTHON_SCRIPT_PATH="/usr/local/projects/PocketFilm/Spider/PocketLifeSpider/PocketLifeSpider"

START_DAY_TMP=${START_DAY}
END_DAY_TMP=${END_DAY}
while (( "${START_DAY_TMP}" <= "${END_DAY_TMP}" )); do
 cur_day=`date -d ${START_DAY_TMP} ${DATE_FORMAT}`
 echo "Current date is ${cur_day}:"
 echo "Run mongo-to-es.py"
 /usr/local/python3/bin/python3 ${PYTHON_SCRIPT_PATH}/mongo-to-es.py ${cur_day}
 START_DAY_TMP=`date -d "+1day ${START_DAY_TMP}" ${DATE_FORMAT}`
done
```

### 2.5 将mongodb中的数据拉取到本地，然后导入到hive中

```shell
#!bin/bash
# 将mongodb中的数据拉取到本地，然后导入到hive中
# 日期格式
DATE_FORMAT="+%Y%m%d"
# 起始日期
if [ ! ${1} ]; then
	START_DAY=`date -d "-1day" ${DATE_FORMAT}`
else
	START_DAY=`date -d "-1day ${1}" ${DATE_FORMAT}`
fi
# 截止日期
if [ ! ${2} ]; then
	END_DAY=${START_DAY}
else
	END_DAY=`date -d "-1day ${2}" ${DATE_FORMAT}`
fi
echo ${START_DAY}
echo ${END_DAY}
# python脚本路径
PYTHON_SCRIPT_PATH="/usr/local/projects/PocketFilm/Spider/PocketLifeSpider/PocketLifeSpider"
EXPORT_PATH="/usr/local/projects/PocketFilm/files/documentations/mongodb/export"
HDFS_DOMAIN_PATH="/user/hive/warehouse/pocket.db"

START_DAY_TMP=${START_DAY}
END_DAY_TMP=${END_DAY}
while (( "${START_DAY_TMP}" <= "${END_DAY_TMP}" )); do
 cur_day=`date -d ${START_DAY_TMP} ${DATE_FORMAT}`
 echo "Current date is ${cur_day}:"
 echo "Run mongo-to-local.py"
 /usr/local/python3/bin/python3 ${PYTHON_SCRIPT_PATH}/mongo-to-local.py ${cur_day}
 echo "Add partition for hive"
 beeline -u jdbc:hive2://192.168.2.205:10000/pocket -e "alter table movie add if not exists partition(date_id=${cur_day})"
 beeline -u jdbc:hive2://192.168.2.205:10000/pocket -e "alter table tv add if not exists partition(date_id=${cur_day})"
 beeline -u jdbc:hive2://192.168.2.205:10000/pocket -e "alter table drama add if not exists partition(date_id=${cur_day})"
 beeline -u jdbc:hive2://192.168.2.205:10000/pocket -e "alter table piece add if not exists partition(date_id=${cur_day})"
 echo "Upload file to hdfs"
 hdfs dfs -put ${EXPORT_PATH}/${cur_day}/movie.csv ${HDFS_DOMAIN_PATH}/movie/date_id=${cur_day}
 hdfs dfs -put ${EXPORT_PATH}/${cur_day}/tv.csv ${HDFS_DOMAIN_PATH}/tv/date_id=${cur_day}
 hdfs dfs -put ${EXPORT_PATH}/${cur_day}/drama.csv ${HDFS_DOMAIN_PATH}/drama/date_id=${cur_day}
 hdfs dfs -put ${EXPORT_PATH}/${cur_day}/piece.csv ${HDFS_DOMAIN_PATH}/piece/date_id=${cur_day}
 START_DAY_TMP=`date -d "+1day ${START_DAY_TMP}" ${DATE_FORMAT}`
done
```

