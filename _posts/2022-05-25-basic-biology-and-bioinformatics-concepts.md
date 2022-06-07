---
title:  "Some basic biology and bioinformatics concepts"
date:   2022-05-25
tags: [Basic concepts]
render_with_liquid: false
---

How to understand the Bowtie2 alignment summary. [详解Bowtie2的比对Summary](https://www.jianshu.com/p/eeed5186e90e)

> aligned **concordantly** 0 times，也即合理匹配0次，表示双端reads不能合理匹配上，在这种情况下，bowtie2一方面会报告这些reads中可以不合理匹配上的reads，另一方面会将双端reads拆分成单端reads继续align，并且报告单端reads可以匹配上的数量。 aligned **concordantly** exactly 1 times，表示合理匹配且只匹配到基因组上的一个位置的reads。 aligned **concordantly** >1 times，表示有些双端reads可以匹配到基因组上的多个位置。 overall alignment rate = (合理匹配一次readsx2 + 多重匹配readsx2 + 不合理匹配readsx2 + 单端匹配reads) / 总双端readsx2
{: .prompt-tip}

How to understand the relationship between sense strand, mRNA, codon, UTR. [如何理解搞懂codon，cds，mRNA，sense strand，ATG，AUG之间的关系](https://www.jianshu.com/p/4f23aafaf7eb)

> DNA有双链，其中一条会被用作模板转录RNA，这条链叫模板链、非编码链和反义链(antisense strand)，另外一条链就叫非模板链，编码链和正义链(sense strand)，这一条链除了T/U的区别和mRNA中的碱基序列一致。一条DNA序列可以分为编码区和非编码区，编码区又分为内含子(intron)和外显子(exon)，经过剪切的成熟mRAN包含蛋白编码区cds(protein coding sequence)和UTR区(untranslated region)，在cds上会有一个翻译起始密码子也即AUG(Met/M)/GUG(Val/V)，会有一个翻译终止密码子也即UAA/UAG/UGA。

