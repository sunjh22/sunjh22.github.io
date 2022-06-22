---
title:  "Random record before categorizing them"
date:   2022-06-13
render_with_liquid: false
---

# 在网页上查看IGV的结果
全部运行在`10.20.57.27`服务器上

下载`node.js`二进制版本
	
	wget https://nodejs.org/dist/v16.15.1/node-v16.15.1-linux-x64.tar.xz
	tar -xf https://nodejs.org/dist/v16.15.1/node-v16.15.1-linux-x64.tar.xz

软链接`npm,npx,node`到`~/data3/bin/`，然后在tmux中开启一个服务器

	tmux new -s web
	npx http-server ./ -p 10086

用`igv_web.py`生成一个`index.html`。主要是借用`igv.js`在browser上直接展示IGV的结果

	python ~/data3/bin/igv_web.py -r /data3/refs/hg38/analysisSet/hg38.analysisSet.fa -m level1_node0_cell0.bam -g /data3/refs/hg38/annotation/UCSC.hg38.ncbiRefSeq.gtf

要注意10086这个端口必须已经开放，需要root或sudo权限

	sudo iptables -I INPUT -p tcp --dport 10086 -j ACCEPT

然后在`10.20.57.27:10086`上就可以访问IGV的展示结果了