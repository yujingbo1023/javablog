---
title: 05-linux系统常用命令
date: 2028-12-15
sticky: 1
sidebar: 'auto'
tags:
 - devops
categories:
 - devops
---



### 1，echo命令和输出重定向

作用：echo输出内容到屏幕和文件中。

```bash
语法结构:
    echo 字符串 回车    # 输出到屏幕
    echo 字符串 > file # 输出如到文件
    其他命令输出 >file  # 输出到文件中
    
案例1.输出malu字符串到malu.txt文件中，如果malu.txt 不存在则自动创建
[root@malu:~]# echo malu > malu.txt
[root@malu:~]# ll
total 4
-rw-r--r-- 1 root root 7 Jul  9 10:20 malu.txt
[root@malu:~]# cat malu.txt 
oldboy

案例2.默认> 先清空文件的内容在写入新的内容
[root@malu:~]# echo test > malu.txt
[root@malu:~]# cat malu.txt 
test

案例3.追加malu字符串到文件中
[root@malu:~]# echo malu >> malu.txt 
[root@malu:~]# cat malu.txt 
test
malu

案例4.ip a 结果输入到ip.txt文件中
[root@malu:~]# ip a > ip.txt
[root@malu:~]# cat ip.txt 	
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: ens33: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 00:0c:29:65:96:4a brd ff:ff:ff:ff:ff:ff
    inet 10.0.0.200/24 brd 10.0.0.255 scope global noprefixroute ens33
       valid_lft forever preferred_lft forever
    inet6 fe80::b4c3:3c61:51f7:2655/64 scope link noprefixroute 
       valid_lft forever preferred_lft forever
       
[root@malu:~]# ping -c1 -W1 www.baidu.com > ping.log
[root@malu:~]# cat ping.log 
PING www.a.shifen.com (110.242.68.3) 56(84) bytes of data.
64 bytes from 110.242.68.3 (110.242.68.3): icmp_seq=1 ttl=128 time=13.2 ms

--- www.a.shifen.com ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 13.224/13.224/13.224/0.000 ms

>  # 先清空，后写入  1> 			标准正确输出重定向  我只要正确的执行结果，错误的不要。
>> # 追加内容到文件的最后 1>>	      标准正确追加输出重定向 只要正确的执行结果
2> # 标准错误输出重定向   我只要错误的 不要正确的
2>># 标准错误追加输出重定向 我只要错误的 不要正确的

案例1.正确的执行结果定向到a.txt
正确: 命令正确，文件正确，啥都正确，结果也是正确的
  358  ll > a.txt
  359  cat a.txt 
  360  ip add
  361  ip add|grep 10.0.0.200
  362  ip add|grep 10.0.0.200 >> a.txt
  363  cat a.txt 
  364  history 
  
案例2.错误的执行结果
错误: 命令错，结果错。
[root@malu:~]# lllllllllll
-bash: lllllllllll: command not found
[root@malu:~]# lllllllllll > hehe.txt
-bash: lllllllllll: command not found
[root@malu:~]# ll aaaaaaaaaa.txt
ls: cannot access 'aaaaaaaaaa.txt': No such file or directory
[root@malu:~]# ll aaaaaaaaaa.txt > hehe.txt
ls: cannot access 'aaaaaaaaaa.txt': No such file or directory

[root@malu:~]# > hehe.txt   # 直接清空文件内容
[root@malu:~]# cat hehe.txt 

案例3.接收错误的结果 2> 2>>
[root@malu:~]# lll
-bash: lll: command not found
[root@malu:~]# lll 2>hehe.txt
[root@malu:~]# cat hehe.txt 
-bash: lll: command not found

[root@malu:~]# ping -c1 -W1 www.aaaaaaccc.com 2>> hehe.txt 
[root@malu:~]# cat hehe.txt 
ping: www.aaaaaaccc.com: Name or service not known
[root@malu:~]# ping -c1 -W1 www.aaaaaaccc.com 2>> hehe.txt 
[root@malu:~]# cat hehe.txt 
ping: www.aaaaaaccc.com: Name or service not known
ping: www.aaaaaaccc.com: Name or service not known

服务: 两个日志文件 ok.log  error.log
错误和正确的日志放在一起的。

案例4.同时接收正确和错误的结果
正确的结果写入到 ok.txt
错误的结果写入到 error.txt
[root@malu:~]# #ping -c1 -W1 www.aaaaaaccc.com >> ok.txt 2>> error.txt
[root@malu:~]# > ok.txt
[root@malu:~]# > error.txt
[root@malu:~]# ping -c1 -W1 www.aaaaaaccc.com >> ok.txt 2>> error.txt
[root@malu:~]# cat ok.txt
[root@malu:~]# cat error.txt 
ping: www.aaaaaaccc.com: Name or service not known
[root@malu:~]# ping -c1 -W1 www.aa.com >> ok.txt 2>> error.txt
[root@malu:~]# cat ok.txt 
PING www.aa.com (184.50.93.141) 56(84) bytes of data.
64 bytes from a184-50-93-141.deploy.static.akamaitechnologies.com (184.50.93.141): icmp_seq=1 ttl=128 time=45.2 ms

--- www.aa.com ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 45.198/45.198/45.198/0.000 ms

[root@malu:~]# cat error.txt 
ping: www.aaaaaaccc.com: Name or service not known


案例5.正确的和错误的都写入到ok.txt文件中
[root@malu:~]# ping -c1 -W1 www.aa.comaaaaaaaaaa >> ok.txt 2>> ok.txt
[root@malu:~]# cat ok.txt 
PING www.aa.com (184.50.93.141) 56(84) bytes of data.
64 bytes from a184-50-93-141.deploy.static.akamaitechnologies.com (184.50.93.141): icmp_seq=1 ttl=128 time=44.8 ms

--- www.aa.com ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 44.775/44.775/44.775/0.000 ms
ping: www.aa.comaaaaaaaaaa: Name or service not known

案例6.其他写法
第一种写法: 正确的结果输入到ok.txt,错误的结果和1一样。
[root@malu:~]# ping -c1 -W1 www.aa.comaaaaaaaaaa >> ok.txt 2>&1
第二种写入: & 正确和错误的都输出到ok.txt
[root@malu:~]# ping -c1 -W1 www.aa.com &>>ok.txt
第三种方法:
[root@malu:~]# ping -c1 -W1 www.aa.comaaaaaaaaaa >> ok.txt 2>> ok.txt
```



### 2，less命令和more命令

作用：一页一页查看文件 查看大文件

```bash
参数选项:
    -N 显示行号
    f 空格 往下翻页
    b 往上翻页
    1G 快速到首行
    g 快速到首行
    / 搜索内容
    n 查找下一个
    N 查找上一个
    q 退出
```



### 3，sort命令

作用：排序

```bash
语法结构：
    sort 文件
    cat file|sort 
参数选项：
    -n 按照数字正序排序
    -r 逆序排序
    -k 指定列数
    
案例1.对数字进行排序
[root@malu:~]# cat a.txt
123
222
1
34
456
5
6
7
8
9
10

语法结构: 
[root@malu:~]# sort  a.txt 
[root@malu:~]# cat a.txt |sort
默认按照第一列数字进行排序
[root@malu:~]# sort a.txt
1
10
123
222
34
456
5
6
7
8
9

字符串:
[root@malu:~]# echo {a..h}|xargs -n1	# 设置为1列输出
a
b
c
d
e
f
g
h
[root@malu:~]# echo {a..h}|xargs -n1 > b.txt
```





