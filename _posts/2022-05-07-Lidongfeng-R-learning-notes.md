---
title:  "李东风R教程学习笔记"
date:   2022-05-07
categories: [R]
tags: [LearingNotes]
render_with_liquid: false
---
[教程地址](https://www.math.pku.edu.cn/teachers/lidf/docs/Rbook/html/_Rbook/index.html)

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


## R数据类型的性质
`NULL`值表示不存在。 `NULL`长度为0，不能有任何属性值

`NA`是有类型的（integer、double、logical、character等), `NA`表示存在但是未知

类型升档:在用`c()`函数合并若干元素时，如果元素基本类型不同，将统一转换成最复杂的一个复杂程度从简单到复杂依次为：`logical < integer < double < character`

除了`NULL`以外，R的变量都可以看成是对象，都可以有属性。 常用属性有`names`, `dim`，`class`等

可以用`attributes()`读取对象`x`的所有属性

可以用`attr(x, "属性名")`的格式读取或定义`x`的属性

**取矩阵子集时如果结果仅有一列或一行， 除非用了`drop=FALSE`选项， 结果不再有`dim`属性， 退化成了普通向量。**

R具有一定的面向对象语言特征，其数据类型有一个class属性,函数`class()`可以返回变量类型的类属

`str()`函数可以显示对象的类型和主要结构及典型内容


## R日期时间
因为我们平时不怎么遇到时间相关的转化或计算问题，所以这部分的内容大概了解一下就可以了

R扩展包`lubridate`提供了多个方便函数， 可以更容易地生成、转换、管理日期型和日期时间型数据

`lubridate::ymd()`, `lubridate::mdy()`, `lubridate::dmy()`将字符型向量转换为日期型向量

`lubridate::make_date(year, month, day)`可以从三个数值构成日期向量

as.character()中可以用format选项指定显示格式

lubridate包中的`wday()`可以取出日期在一个星期内的序号，但是一个星期从星期天开始，星期天为1,星期一为2，星期六为7，例如`wday(as.POSIXct("2018-1-17 13:15:40"))`为`4`表示星期三

### 日期计算
在lubridate的支持下日期可以相减，可以进行加法、除法

lubridate的`dseconds()`, `dminutes()`, `dhours()`, `ddays()`, `dweeks()`, `dyears()`函数可以直接生成时间长度类型的数据

用`unclass()`函数将时间长度数据的类型转换为以秒为单位的普通数值

lubridate提供了`%--%`运算符构造一个时间期间。 时间区间可以求交集、并集等

生成时间区间， 也可以用`lubridate::interval(start, end)`函数

把一个R日期时间值用`as.POSIXlt()`转换为`POSIXlt`类型， 就可以用列表元素方法取出其组成的年、月、日、时、分、秒等数值

## R因子类型

### factor
因子的`levels`属性可以看成是一个映射， 把整数值1,2,映射成这些水平值， 因子在保存时会保存成整数值1,2,等与水平值对应的编号

为了对因子执行字符型操作（如取子串）， 保险的做法是先用as.character()函数强制转换为字符型
```R
factor(x, levels = sort(unique(x), na.last = TRUE), 
       labels, exclude = NA, ordered = FALSE)
```

### cut()
连续取值的变量，可以用`cut()`函数将其分段， 转换成因子。 使用`breaks`参数指定分点， 最小分点要小于数据的最小值， 最大分点要大于等于数据的最大值， 默认使用左开右闭区间分组

可以指定`breaks`为一个正整数， 表示将数据范围略扩大后进行**等间距分组**

为了实现**各组个数比较平均**的分组， 可以利用`quantile()`函数计算分位数作为分组

### forcats包的因子函数
`forcats::fct_reorder()`可以根据不同因子水平分成的组中另一数值型变量的统计量值排序
```R
set.seed(1)
fac <- sample(c("red", "green", "blue"), 30, replace=TRUE)
fac <- factor(fac, levels=c("red", "green", "blue"))
x <- round(100*(10+rt(30,2)))

fac2 <- forcats::fct_reorder(fac, x, sd)

# 将特定的一个或几个水平次序放到因子水平最前面
fac3 <- fct_relevel(fac, "blue")

# 修改每个水平的名称
fac4 <- fct_recode(fac, "红"="red", "绿"="green", "蓝"="blue")

# 合并原来的水平
fct_collapse()
```

## 列表类型
```R
# 把某个列表元素赋值为NULL就删掉这个元素
rec <- list(name="李明", age=30, scores=c(85, 76, 90))
rec[["age"]] <- NULL
# 在list()函数中允许定义元素为NULL，这样的元素是存在的
li <- list(a=120, b="F", c=NULL)
# 把已经存在的元素修改为NULL值而不是删除此元素
li["b"] <- list(NULL)
```

用`as.list()`把一个其它类型的对象转换成列表； 用u`nlist()`函数把列表转换成基本向量

## R矩阵和数组
- 对矩阵`A`，`diag(A)`访问`A`的主对角线元素组成的向量。 另外，若`x`为正整数值标量，`diag(x)`返回`x`阶单位阵； 若`x`为长度大于1的向量， `diag(x)`返回以x的元素为主对角线元素的对角矩阵

- 若x是向量，`cbind(x)`把x变成列向量， 即列数为1的矩阵， `rbind(x)`把x变成行向量

- 对两个同形状的矩阵， 用`*`表示两个矩阵对应元素相乘(注意这不是线性代数中的矩阵乘法)， 用`/`表示两个矩阵对应元素相除

- 用`%*%`表示矩阵乘法而不是用`*`表示， 注意矩阵乘法要求左边的矩阵的列数等于右边的矩阵的行数

- 用%*%表示矩阵乘法而不是用*表示， 注意矩阵乘法要求左边的矩阵的列数等于右边的矩阵的行数。注意矩阵乘法总是给出矩阵结果， 即使此矩阵已经退化为行向量、列向量甚至于退化为标量也是一样

- 设`x`, `y`是两个向量， 计算向量内积， 可以用`sum(x*y)`表示

- R向量支持外积运算， 记为`%o%`, 结果为矩阵。 `x %o% y`的第行第列元素等于`x[i]`乘以`y[j]`。这种运算还可以推广到x的每一元素与y的每一元素进行其它的某种运算， 而不限于乘积运算，可以用outer(x,y,f)完成， 其中f是某种运算，或者接受两个自变量的函数

用`solve(A)`求`A`的逆矩阵

用`solve(A,b)`求解线性方程组Ax = b 中的x

### apply()函数
`apply(A, 2, FUN)`,把矩阵A的每一列分别输入到函数FUN中， 得到对应于每一列的结果

`apply(A, 1, FUN)`,把矩阵A的每一行分别输入到函数FUN中， 得到与每一行对应的结果

### 多维数组
```R
# 三维数组ara可以看成是4个2X3矩阵
ara <- array(1:24, dim=c(2,3,4))
```

## 数据框
在R markdown文件中， 可以将数据框保存的表格显示为富文本格式， 方法是使用`knitr::kable()`函数

### tibble
从tibble取出的一列结果仍是tibble而不是向量， 为了提取一列为向量应使用双方括号格式或`$`格式