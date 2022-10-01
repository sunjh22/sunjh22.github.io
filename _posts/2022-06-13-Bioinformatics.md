---
title:  "Random record before categorizing them"
date:   2022-06-13
render_with_liquid: false
---

## Sort

Sort a bed file according to chromosome and start position
	
```bash
$ sort -Vk 1 -k 2,3n demo.bed
```
	
## Comm

Compare two files, show comman lines
	
```bash
$ comm -12 file1 file2
```

## uppercase to lowercase

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

## 在网页上查看IGV的结果

全部运行在`10.20.57.27`服务器上

下载`node.js`二进制版本
```bash
wget https://nodejs.org/dist/v16.15.1/node-v16.15.1-linux-x64.tar.xz
tar -xf https://nodejs.org/dist/v16.15.1/node-v16.15.1-linux-x64.tar.xz
```

软链接`npm,npx,node`到`~/data3/bin/`，然后在tmux中开启一个服务器
```bash
tmux new -s web
npx http-server ./ -p 10086
```

用`igv_web.py`生成一个`index.html`。主要是借用`igv.js`在browser上直接展示IGV的结果
```bash
python ~/data3/bin/igv_web.py -r /data3/refs/hg38/analysisSet/hg38.analysisSet.fa -m level1_node0_cell0.bam -g /data3/refs/hg38/annotation/UCSC.hg38.ncbiRefSeq.gtf
```

要注意10086这个端口必须已经开放，需要root或sudo权限
```bash
sudo iptables -I INPUT -p tcp --dport 10086 -j ACCEPT
```

然后在`10.20.57.27:10086`上就可以访问IGV的展示结果了

## Mean-shift algorithm

[参考这篇文章](https://nicehuster.github.io/2019/08/05/shift/)

## t-test

- one-sample t-test: 比较一组样本(数量为n)的均值与一个预期的总体均值是否相等，null hypothesis就是相等，自由度为n-1
- two-sample t-test: 比较两组样本(数量分别为n1,n2)的均值是否相等，null hypothesis是相等，自由度为n1+n2-2，假设是两个样本都服从正态分布且方差相同
- Welch-test: 和two-sample t-test一样，都是比较两组样本的均值是否相等，但是没有两组样本方差相同的假设

## center, scale and normalize

- 中心化(center): 使样本的均值减去一个特定的值(通常为减到0或减去样本的中位数值)
- 标准化(scale): 使样本的均值为0，标准差为1，也即正态化。在单细胞中一般对基因做scale，目的是更好的在热图上展示不同基因在细胞中的表达差异
- 归一化(normalize): 使样本的值落在0-1之间(通过min-max normalization: x' = (x - X_min) / (X_max - X_min))，或-1到1之间(通过x' = (x - μ) / (X_max - X_min))

## remove duplicates

`samtools rmdup`: 比较适合单端reads去重，只考虑reads map的起始和终止位置，方向还有质量值，不考虑比对情况，结果会直接去除重复的reads
`gatk RemoveDuplicatesSpark`: 适合双端reads去重，考虑reads**5'端**比对到的位置，方向和碱基比对情况，结果只会标记重复的reads而不会去除

## bwa mem -M

supplementary reads指的是一条reads中的一部分map到参考区域一，另一部分map到参考区域二，较短的一部分的flag值是2048；

secondary reads指的是一条reads可以map到基因组上的不同位置，包含supplementary reads的情况，异常map的flag值是256；

用`baw mem -M`参数可以mark supplementary reads使其变为secondary reads，也即flag值从2048变为256，这样有利于利用这些reads做变异检测，否则这些reads会被丢弃

## Umap and Bismap mappability

Refer to this [site](https://bismap.hoffmanlab.org)

这个map的目的是标记整个基因组上highly mappable的位置，也即可以被特定长度的k-mer特异性map的位置。
方法是首先产生一个基因组上可能有的所有k-mer，然后将这些k-mer用bowtie2 map回基因组上，标记那些只map到基因组上一个位置的k-mer的起始位置。结果中1表示这个位置是uniquely mappable，0表示这个位置产生的k-mer可以map到基因组上的其他地方。

## Subsample reads from bam file

```bash
samtools view --subsample 0.01 in.bam > out.bam
```
## beffroni and BH (FDR) correction

beffroni校正是用P值直接除以总的test数

BH是先对P值进行排序，然后用P值除以test数再乘以P值的秩

可以参考[这篇文章](https://zhuanlan.zhihu.com/p/565427011)

## Bowtie2 alignment report

[详解Bowtie2的比对Summary](https://www.jianshu.com/p/eeed5186e90e)

> aligned **concordantly** 0 times，也即合理匹配0次，表示双端reads不能合理匹配上，在这种情况下，bowtie2一方面会报告这些reads中可以不合理匹配上的reads，另一方面会将双端reads拆分成单端reads继续align，并且报告单端reads可以匹配上的数量。 aligned **concordantly** exactly 1 times，表示合理匹配且只匹配到基因组上的一个位置的reads。 aligned **concordantly** >1 times，表示有些双端reads可以匹配到基因组上的多个位置。 overall alignment rate = (合理匹配一次readsx2 + 多重匹配readsx2 + 不合理匹配readsx2 + 单端匹配reads) / 总双端readsx2
{: .prompt-tip}