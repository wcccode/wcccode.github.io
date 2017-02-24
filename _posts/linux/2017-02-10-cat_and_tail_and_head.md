---
layout: post
title: cat/tail/head
category: linux
tags: [linux]
---

# cat、tail、head命令

cat -n 文件路径\|tail -n +5\|head -n 6   // 显示 5 ～ 10 行的内容， 包括5 和10

tail -n +iLinNum   // 从ILinNum开始显示到结束的内容

tail -n iLinNum     // 显示最后 iLinNum 行的内容

head -n iLinNum // 显示开头 iLinNum 行的内容

head -n +iLinNum // 同 head -n iLinNum

## head
默认是显示开头前10行。

head /etc/passwd

显示开头前k行。

head -k /etc/passwd
head -n k /etc/passwd（注意和下面的有-的区别）
head -n +k /etc/passwd

除最后k行外，显示剩余全部内容。

head -n -k /etc/passwd

## tail

语法 tail [-n -k ]... [FILE]...

例子
默认是显示末尾10行。

tail /etc/passwd

显示末尾k行。

tail -k /etc/passwd
tail -n k /etc/passwd（注意和下面的有+的区别）
tail -n -k /etc/passwd

从开头第k行开始输出。

tail -n +k /etc/passwd


## head和tail组合用法举例

//从第11行开始显示，但不包括最后3行

 head -n -3 /etc/passwd \|tail -n +11

//显示前20行，但从第11行开始

head -n 20 /etc/passwd \|tail -n +11 

//显示除最后3行以外的所有行，但只显示最后10行

 head -n -3 /etc/passwd \|tail -n 10 

//显示前20行中的后10行

head -n 20 /etc/passwd \|tail -n 10 

//从第11行开始显示，但只显示前10行

tail -n +11 /etc/passwd \|head -n 10 

//从第11行开始显示，但不包括最后3行

tail -n +11 /etc/passwd \|head -n -3

//显示最后13行中的前10行

tail -n 13 /etc/passwd \|head -n 10 

//显示最后13行中除末尾的3行以外的前10行

tail -n 13 /etc/passwd \|head -n -3
