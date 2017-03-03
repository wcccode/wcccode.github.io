---
layout: post
title: extract log in specified range time
category: linux
tags: [linux]
---

# 提取某段时间内的日志

```

#!/bin/bash

# This is simple extract from log file in specified range time
# eg: ./extract.sh test.log "10:00" "11:00"


if [ $# -lt 2 ]; then
   echo "file name and filter are required"
   exit 2
fi

GET_START_ROW_NUM=`cat -n $1|grep $2|tail -n 1|awk '{print $1}'`
START_ROW_NUM=$GET_START_ROW_NUM
END_ROW_NUM=0i
COUNT=0
FILE_NAME="$1[$2]"
RESULT_STR="extract log from row num $START_ROW_NUM "


echo -e '\n******************** start ***********************\n'

if [ "$3" ]; then 

   GET_END_ROW_NUM=`cat -n $1|grep $3|tail -n 1|awk '{print $1}'` 
   END_ROW_NUM=$GET_END_ROW_NUM
   COUNT=$((END_ROW_NUM-START_ROW_NUM))
   FILE_NAME="$FILE_NAME[$3]"
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
