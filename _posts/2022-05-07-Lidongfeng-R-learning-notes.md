---
title:  "李东风R教程学习笔记"
date:   2022-05-07
categories: [R]
tags: [LearingNotes]
render_with_liquid: false
---

## 安装R

指定镜像网站
```R
options(repos=c(CRAN="https://mirror.tuna.tsinghua.edu.cn/CRAN/"))
install.packages("sos")
```

安装GitHub上的R包
```R
if(!require(devtools)) install.packages('devtools')
devtools::install_github("kjhealy/socviz")
```

安装Bioconductor上的R包
```R
options(repos=c(CRAN="https://mirror.tuna.tsinghua.edu.cn/CRAN/"))
if (!requireNamespace("BiocManager", quietly = TRUE)){
  install.packages("BiocManager")
  BiocManager::install()
}
options(BioC_mirror="https://mirrors.tuna.tsinghua.edu.cn/bioconductor")
BiocManager::install(c("Biostrings"))
```

更新扩展包
```R
options(repos=c(CRAN="http://mirror.tuna.tsinghua.edu.cn/CRAN/"))
update.packages(checkBuilt=TRUE, ask=FALSE)
```

## 数据类型与相应计算

R是“动态类型”语言， 赋值实际上是“绑定”（binding）， 即将一个变量名与一个存储地址联系在一起， 同一个存储地址可以有多个变量名与其联系

### 数值型向量及其运算
`numeric()`函数可以用来初始化一个指定元素个数而元素都等于零的数值型向量， 如`numeric(10)`会生成元素为10个零的向量

常用的数学函数:
- 舍入： `ceiling`, `floor`, `round`, `signif`, `trunc`, `zapsmall`
- 符号函数 `sign`
- 绝对值 `abs`
- 平方根 `sqrt`
- 对数与指数函数 `log`, `exp`, `log10`, `log2`
- 三角函数 `sin`, `cos`, `tan`
- 贝塔函数 `beta`
- 伽玛函数 `gamma`

如果自己编写的函数没有考虑向量化问题， 可以用Vectorize()函数将其转换成向量化版本

排序函数: sort(x)返回排序结果。 rev(x)返回把各元素排列次序反转后的结果。 order(x)返回排序用的下标

统计函数: `sum`(求和), `mean`(求平均值), `var`(求样本方差), `sd`(求样本标准差), `min`(求最小值), `max`(求最大值), `range`(求最小值和最大值), `prod`求所有元素的乘积, `cumsum`和`cumprod`计算累加和累乘积

生成规则序列的函数： `seq`, `rep`

### 逻辑型向量及其运算
函数`match(x, y)`起到和`x %in% y`运算类似的作用， 但是其返回结果不是找到与否， 而是对x的每个元素， 找到其在y中首次出现的下标，找不到时取缺失值

用`xor(x, y)`表示`x`与`y`的异或运算， 即值不相等时为真值，相等时为假值， 有缺失值参加运算时为缺失值

`&&`和`||`分别为短路的标量逻辑与和短路的标量逻辑或， 仅对两个标量进行运算，如果有向量也仅使用第一个元素。 一般用在if语句、while语句中， 且只要第一个比较已经决定最终结果就不计算第二个比较

函数`which()`返回真值对应的所有下标

函数`identical(x,y)`比较两个R对象`x`与`y`的内容是否完全相同， 结果只会取标量TRUE与FALSE两种

函数`duplicated()`返回每个元素是否为重复值的结果

用函数`unique()`可以返回去掉重复值的结果

### 字符型数据及其处理
一些常用函数
`paste(sep="", collapse="")`, `paste0()`

`toupper()`, `tolower()`

`substr(x, start, stop)`

`as.numeric()`, `as.character()`, `sprintf()`格式化

`gsub()`可以替换字符串中的子串

把多于一个空格替换成一个空格
```R
gsub('[[:space:]]+', ' ', 'a   cat  in a box', perl=TRUE)
```

## R向量下标和子集
`which.min`、`which.max`求最小值的下标和最大值的下标

用`unname(x)`返回去掉了元素名的`x`的副本， 用`names(x) <- NULL`可以去掉`x`的元素名

