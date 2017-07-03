---
layout: post
title: extract log in specified range time
category: linux
tags: [linux]
---

# 提取某段时间内的日志

## 使用场景

有时候需要提取指定时间段的日志信息，同时文件比较大不能直接打开，此时可以通过脚本来提取所需的信息，如：

```
......

此处省略N行

......
16:09:04,973 INFO  [stdout] (StdoutThread) *********
16:09:04,973 INFO  [stdout] (StdoutThread) *********
16:09:04,973 INFO  [stdout] (StdoutThread) *********
16:09:04,973 INFO  [stdout] (StdoutThread) *********
16:09:04,973 INFO  [stdout] (StdoutThread) *********
16:10:04,973 INFO  [stdout] (StdoutThread) *********
16:10:04,973 INFO  [stdout] (StdoutThread) *********
16:10:04,973 INFO  [stdout] (StdoutThread) *********
16:10:04,973 INFO  [stdout] (StdoutThread) *********
16:10:04,973 INFO  [stdout] (StdoutThread) *********
......

此处省略N行

......
17:10:04,973 INFO  [stdout] (StdoutThread) *********
17:10:04,973 INFO  [stdout] (StdoutThread) *********
17:10:04,973 INFO  [stdout] (StdoutThread) *********
17:10:04,973 INFO  [stdout] (StdoutThread) *********
17:10:04,973 INFO  [stdout] (StdoutThread) *********
17:11:04,973 INFO  [stdout] (StdoutThread) *********
17:11:04,973 INFO  [stdout] (StdoutThread) *********
17:11:04,973 INFO  [stdout] (StdoutThread) *********
17:11:04,973 INFO  [stdout] (StdoutThread) *********
17:11:04,973 INFO  [stdout] (StdoutThread) *********
......

```

需要获取16:10到17:10的数据需要怎么处理呢？

首先日志格式要求第一部分是时间，通过使用cat、grep、head、tail、awk来获取到开始、结束时间的行号，并计算出总共行数。最后通过cat、head、tail来获取指定范围的行。

## 添加extract.sh，内容如下

```

#!/bin/bash

# This is simple extract from log file in specified range time
# eg: ./extract.sh test.log "^10:00" "^11:00"


if [ $# -lt 2 ]; then
   echo "file name and filter are required"
   exit 2
fi

GET_START_ROW_NUM=`cat $1|grep $2 -n|head -n 1|awk -F ':' '{print $1}'`
START_ROW_NUM=$GET_START_ROW_NUM
END_ROW_NUM=0i
COUNT=0
FILE_NAME="$1.$2"
FILE_NAME="${FILE_NAME//\^/}"
FILE_NAME="${FILE_NAME//\:/}"
RESULT_STR="extract log from row num $START_ROW_NUM "


echo -e '\n******************** start ***********************\n'

if [ "$3" ]; then 

   GET_END_ROW_NUM=`cat $1|grep $3 -n|tail -n 1|awk -F ':' '{print $1}'` 
   END_ROW_NUM=$GET_END_ROW_NUM
   COUNT=$((END_ROW_NUM-START_ROW_NUM))
   FILE_NAME="${FILE_NAME//\^/}"
   FILE_NAME="${FILE_NAME//\:/}"
   RESULT_STR="$RESULT_STR to $END_ROW_NUM count=$COUNT"
   ## extract log in specified range time then write to file
   cat -n $1|tail -n +$START_ROW_NUM|head -n $COUNT > $FILE_NAME

else
   ## extract log in specified start time then write to file
   cat -n $1|tail -n +$START_ROW_NUM > $FILE_NAME

fi

echo "$RESULT_STR write to file $FILE_NAME"

echo -e '\n******************** end **************************\n'

```

## 使用方式

```
./extract.sh filename start_time [end_time]
#时间格式支持正则表达式
```

获取文件中16:10到17:10的数据

- 原数据

```
[root@user mylog]# cat test.log
......
此处省略N行
.....
16:09:04,973 INFO  [stdout] (StdoutThread) *********
16:09:04,973 INFO  [stdout] (StdoutThread) *********
16:09:04,973 INFO  [stdout] (StdoutThread) *********
16:09:04,973 INFO  [stdout] (StdoutThread) *********
16:09:04,973 INFO  [stdout] (StdoutThread) *********
16:10:04,973 INFO  [stdout] (StdoutThread) *********
16:10:04,973 INFO  [stdout] (StdoutThread) *********
16:10:04,973 INFO  [stdout] (StdoutThread) *********
16:10:04,973 INFO  [stdout] (StdoutThread) *********
16:10:04,973 INFO  [stdout] (StdoutThread) *********
......
此处省略N行
.....
17:10:04,973 INFO  [stdout] (StdoutThread) *********
17:10:04,973 INFO  [stdout] (StdoutThread) *********
17:10:04,973 INFO  [stdout] (StdoutThread) *********
17:10:04,973 INFO  [stdout] (StdoutThread) *********
17:10:04,973 INFO  [stdout] (StdoutThread) *********
17:11:04,973 INFO  [stdout] (StdoutThread) *********
17:11:04,973 INFO  [stdout] (StdoutThread) *********
17:11:04,973 INFO  [stdout] (StdoutThread) *********
17:11:04,973 INFO  [stdout] (StdoutThread) *********
17:11:04,973 INFO  [stdout] (StdoutThread) *********
......

```

- 执行提取命令

```
[root@user mylog]# ./extract.sh test.log "^16:10" "^17:10"

******************** start ***********************

extract log from row num 9  to 21 count=12 write to file test.log.1610.1710

******************** end **************************
```

- 结果数据

```
[root@user mylog]# cat test.log.1610.1710 
     9  16:10:04,973 INFO  [stdout] (StdoutThread) *********
    10  16:10:04,973 INFO  [stdout] (StdoutThread) *********
    11  16:10:04,973 INFO  [stdout] (StdoutThread) *********
    12  16:10:04,973 INFO  [stdout] (StdoutThread) *********
    13  16:10:04,973 INFO  [stdout] (StdoutThread) *********
    14  ......
    15  此处省略N行
    16 ......
    17  17:10:04,973 INFO  [stdout] (StdoutThread) *********
    18  17:10:04,973 INFO  [stdout] (StdoutThread) *********
    19  17:10:04,973 INFO  [stdout] (StdoutThread) *********
    20  17:10:04,973 INFO  [stdout] (StdoutThread) *********

```
