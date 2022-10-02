---
title:  "Linux"
date:   2022-04-26
categories: [Linux]
render_with_liquid: false
---

## 在Linux服务器上用Python开一个服务器
```bash
python -m http.server --bind 10.20.57.27 10086
```
将该服务器运行在tmux中便可以常驻后台

## 运行服务器中的jupyter notebook
```bash
jupyter notebook --no-browser --port 10087 --ip=10.20.57.27
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
