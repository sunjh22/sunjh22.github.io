---
title:  "Common useful linux tricks"
date:   2022-04-26
categories: [Linux]
render_with_liquid: false
---

Some useful bioinformatics tricks that I always forgot when in need.

## sort a bed file according to chromosome and start position
First three columns are chromosome, start and end
	
```bash
$ sort -Vk 1 -k 2,3n demo.bed
```
	
## compare two files, show comman lines
	
```bash
$ comm -12 file1 file2
```

## Convert text between uppercase and lowercase
[A good reference](https://www.networkworld.com/article/3529409/converting-between-uppercase-and-lowercase-on-the-linux-command-line.html)

Converting all letters

```bash
$ echo "Hello There" | tr [:lower:] [:upper:]
$ echo "Hello There" | tr a-z A-Z
$ echo "Hello There" | awk '{print toupper($0)}'
$ echo $dept | sed 's/[a-z]/\U&/g'
```

Capitalizing first letters only

```bash
$ echo design \& engineering| sed -e "s/\b\(.\)/\u\1/g"
```

Making sure only first letters are uppercase
		
```bash
$ echo -n "design & engineering" | python3 -c "import sys; print(sys.stdin.read().title())"
```

## Zombie process
[处理僵尸进程](https://www.cnblogs.com/mayhh/p/9968428.html)
要想杀死僵尸进程，一是杀掉僵尸进程本身，二是杀掉僵尸进程的父进程
```bash
$ ps -A -o stat,ppid,pid,cmd | grep -e '^[Zz]' | awk '{print $2}' | xargs kill -9
```

[linux top进程状态D](https://www.cnblogs.com/muahao/p/6838005.html)
今天跑snakemake的流程的时候比前几次多用了8个核（总共用了24个），提交任务后发现所有的任务状态都变成了D，但是snakemake没有报错。上网找了状态D的形成原因和解决办法；

D代表"uninterrunptable sleep"，一般是由于进程在长时间等待IO而没有获得响应导致的，处理方法一是可以让进程的IO得到满足，二只有reboot系统

但是奇怪的是，我没有做任何操作，过了一会之后所有任务的状态又变成了R，可以完全正常跑下去了，不知道这次是不是由于一次性分配太多核的原因

查看所有D状态的进程
```bash
$ ps axwf -eo pid,stat | grep D
```
