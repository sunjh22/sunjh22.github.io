---
title:  "李东风R教程学习笔记"
date:   2022-05-07
categories: [R]
tags: [LearningNotes]
render_with_liquid: false
---
[教程地址](https://www.math.pku.edu.cn/teachers/lidf/docs/Rbook/html/_Rbook/index.html)

## 安装R

指定镜像网站
```R
options(repos=c(CRAN="https://mirror.tuna.tsinghua.edu.cn/CRAN/"))
install.packages("sos")
```

安装包时指定镜像网站和安装路径
```R
install.packages("sos", repos="", lib="")
```

## 保存数据
```R
# save all objects in environment
save.image("to/path/all_data.Rdata")
load("to/path/all_data.Rdata")

# save several objects
save(data1, data2, "to/path/two_objects.Rdata")
load("to/path/two_objects.Rdata")

# save single object, both save() and saveRDS() work, but readRDS() could set a new name to object
saveRDS(data1, "to/path/single_object.Rdata")
new_data_load <- readRDS("to/path/single_object.Rdata")
```

## 数据类型与相应计算
R是“动态类型”语言， 赋值实际上是“绑定”（binding）， 即将一个变量名与一个存储地址联系在一起， 同一个存储地址可以有多个变量名与其联系

### 数值型向量及其运算
`numeric()`函数可以用来初始化一个指定元素个数而元素都等于零的数值型向量， 如`numeric(10)`会生成元素为10个零的向量

`vector("list", 10)`函数可以用来初始化一个有10个元素的空列表

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

排序函数: sort(x)返回排序结果。 rev(x)返回把各元素排列次序反转后的结果。 order(x)返回排序用的下标 `gtools::mixedsort(c("v10", "v7", "v2"))`可以按字符中的数字进行排序

统计函数: `sum`(求和), `mean`(求平均值), `var`(求样本方差), `sd`(求样本标准差), `min`(求最小值), `max`(求最大值), `range`(求最小值和最大值), `prod`求所有元素的乘积, `cumsum`和`cumprod`计算累加和累乘积

生成规则序列的函数： `seq`, `rep`

### 逻辑型向量及其运算
函数`match(x, y)`起到和`x %in% y`运算类似的作用，返回x中的每个元素在y中首次出现的下标，找不到时取NA

用`xor(x, y)`表示`x`与`y`的异或运算，即值不相等时为真值，相等时为假值，有缺失值参加运算时为缺失值

`&&`和`||`分别为短路的标量逻辑与和短路的标量逻辑或，仅对两个标量进行运算，如果有向量也仅使用第一个元素。 一般用在if语句、while语句中，且只要第一个比较已经决定最终结果就不计算第二个比较

函数`which()`返回真值对应的所有下标

函数`identical(x,y)`比较两个R对象`x`与`y`的内容是否完全相同，结果只会取标量TRUE与FALSE两种

函数`duplicated()`返回每个元素是否为重复值的结果

用函数`unique()`可以返回去掉重复值的结果

### 字符型数据及其处理
一些常用函数
`paste(sep=" ", collapse="")`, `paste0()`

`toupper()`, `tolower()`

`substr(x, start, stop)`

`strsplit(x, string, fixed=FALSE)`

`as.numeric()`, `as.character()`, `sprintf()`格式化

`gsub(pattern, replace, x)`可以替换字符串中的子串

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

类型升档:在用`c()`函数合并若干元素时，如果元素基本类型不同，将统一转换成最复杂的一个，复杂程度从简单到复杂依次为：`logical < integer < double < character`

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

lubridate提供了`%--%`运算符构造一个时间区间。 时间区间可以求交集、并集等

生成时间区间， 也可以用`lubridate::interval(start, end)`函数

把一个R日期时间值用`as.POSIXlt()`转换为`POSIXlt`类型， 就可以用列表元素方法取出其组成的年、月、日、时、分、秒等数值


## R因子类型

### factor
因子的`levels`属性可以看成是一个映射，把整数值1,2,映射成这些水平值，因子在保存时会保存成1,2,等与水平值对应的编号

为了对因子执行字符型操作（如取子串），保险的做法是先用as.character()函数强制转换为字符型
```R
factor(x, levels = sort(unique(x), na.last = TRUE), 
       labels, exclude = NA, ordered = FALSE)
```

### cut()
连续取值的变量，可以用`cut()`函数将其分段，转换成因子。 使用`breaks`参数指定分点，最小分点要小于数据的最小值，最大分点要大于等于数据的最大值，默认使用左开右闭区间分组

可以指定`breaks`为一个正整数，表示将数据范围略扩大后进行**等间距分组**

为了实现**各组个数比较平均**的分组，可以利用`quantile()`函数计算分位数作为分组

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
# 也可以把已经存在的元素修改为NULL值而不是删除此元素
li["b"] <- list(NULL)
```

用`as.list()`把一个其它类型的对象转换成列表； 用`unlist()`函数把列表转换成基本向量，注意这个函数会解压所有的列表元素到**一个**向量中

## R矩阵和数组
- 对矩阵`A`，`diag(A)`访问`A`的主对角线元素组成的向量。 另外，若`x`为正整数值标量，`diag(x)`返回`x`阶单位阵； 若`x`为长度大于1的向量， `diag(x)`返回以x的元素为主对角线元素的对角矩阵

- 若x是向量，`cbind(x)`把x变成列向量，即列数为1的矩阵，`rbind(x)`把x变成行向量

- 对两个同形状的矩阵， 用`*`表示两个矩阵对应元素相乘(注意这不是线性代数中的矩阵乘法)， 用`/`表示两个矩阵对应元素相除

- 用`%*%`表示矩阵乘法而不是用`*`表示， 注意矩阵乘法要求左边的矩阵的列数等于右边的矩阵的行数

- 设`x`, `y`是两个向量， 计算向量内积， 可以用`sum(x*y)`表示

用`solve(A)`求`A`的逆矩阵

用`solve(A,b)`求解线性方程组Ax = b 中的x

### apply()函数
`apply(A, 2, FUN)`, 把矩阵或者数据框A的每一列分别输入到函数FUN中，得到对应于每一列的结果

`apply(A, 1, FUN)`, 把矩阵或者数据框A的每一行分别输入到函数FUN中，得到与每一行对应的结果

### 多维数组
```R
# 三维数组ara可以看成是4个2X3矩阵
ara <- array(1:24, dim=c(2,3,4))
```
## 数据框
在R markdown文件中，可以将数据框保存的表格显示为富文本格式，方法是使用`knitr::kable()`函数

`split(x, f)`函数可以将向量或者数据框`x`按照`f`拆分，`f`可以是一组因子向量也可以是数据框中的一个变量，返回一个列表

### tibble
从tibble取出的一列结果仍是tibble而不是向量， 为了提取一列为向量应使用双方括号格式或`$`格式


# 编程模块

## R输入输出
`cat()`函数可以把字符串、变量、表达式连接起来显示,有`file=`和`append=TRUE`两个参数

read_csv()函数：参数`skip=`, `locale=locale()`,`col_names=`,`col_types`.

`read_table2()`读入用空格作为间隔的文本文件，同一行的两个数据项之间可以用一个或多个空格分隔，不需要空格个数相同，也不需要上下对齐

## 程序控制结构
- 分支结构：if-else, ifelse(), dplyr::case_when()相当于ifelse的多分支推广，在mutate中需要根据条件定义一个新的变量的时候很有用
- 循环结构：for, 如果需要对某个向量x按照下标循环，获得所有下标序列的标准写法是`seq_along(x)`
- while()和repeat
```R
# 用泰勒展开逼近计算e的值
e0 <- exp(1.0)
s <- 1.0
x <- 1
k <- 0
repeat{
  k <- k+1
  x <- x/k
  s <- s + x
  
  if(x < .Machine$double.eps) break
}
err <- s - e0
cat("k=", k, " s=", s, " e=", e0, " 误差=", err, "\n")
```

如果对向量每个元素遍历并保存结果，应在循环之前先将结果变量产生等长的存储，在循环内为已经分配好存储空间的输出向量的元素赋值。为了产生长度为n的数值型向量，用`numeric(n)`； 为了产生长度为n的列表，用`vector("list", n)`

if和while中用到的条件必须是标量值，而且必须为TRUE or FALSE

### 管道控制
R从4.1.0版本开始提供了一个`|>`运算符实现管道控制

## 函数
函数名 <- function(形式参数表) 函数体，*函数也是一个对象*

从R 4.1.0版本开始，简单的R函数还可以写成`\(x)` 表达式的形式, 用`\(x)`代替`function(x)`关键字

函数体的最后一个表达式是函数返回值, 为了返回多个变量值，将这些变量打包为一个列表返回即可

函数定义中的自变量叫做形式参数或形参(formal arguments)。 函数调用时，形式参数得到实际值，叫做实参(actual arguments)

`formals()`可以查看函数的形式参数表

R中在递归调用时，最好用 `Recall()` 代表调用自身，这样保证函数即使被改名（在R中函数是一个对象，改名后仍然有效）递归调用仍指向原来定义

R中的无名函数类似于Python中的lambda匿名函数，只要`function(形式参数)`就可以

`do.call(fun, list)`函数是用`list`中的所有元素作为`fun`的参数执行，因为`list`这个数据结构包容万物，所以这个函数在某些时候很有用，比如按行合并一个列表中的所有元素时，还可以用来优雅的处理函数的可选参数，具体讲解和例子可以参考[这篇优秀的推送](https://mp.weixin.qq.com/s/eqFz_ateDkCuChNDNPbRhw)

### 变量作用域
`ls(pattern="^tmp[.]")`显示所有以`tmp.`开头的变量

在函数内部如果要修改全局变量的值，用 `<<-`代替`<-`进行赋值

## R程序效率

### 向量化编程
尽量用R中内置的向量化的函数

### 减少显式循环
`replicate()`函数可以用来执行某段程序若干次,一个例子：
```R
set.seed(1)
replicate(6, {
  x <- rnorm(5, 0, 1); 
  c(mean(x), sd(x)) })
```

### 避免制作副本
- 事先分配好用来保存结果的数据结构，在每次循环中填入相应结果
- 在循环内修改数据框的值会制作数据框副本，修改列表元素不会制作副本

### R的计算函数

#### 数学函数
#### 概括函数
- `cumsum`和`cumprod`计算累计，得到和输入等长的向量结果
- `diff`计算前后两项的差分
- `quantile`计算样本分位数, `cor`计算相关系数
- `colSums`, `colMeans`, `rowSums`, `rowMeans`对矩阵的每列或每行计算总和或者平均值

#### 最值
- `pmax`(x1,x2,...)对若干个等长向量计算对应元素的最大值

#### 排序
- `sort`返回排序结果
- `order`返回排序用的下标序列
- `unique()`返回去掉重复元素的结果，`duplicated()`对每个元素用一个逻辑值表示是否与前面某个元素重复
- `rev`反转序列
- `rank`计算秩统计量

### 一元定积分
`integrate(f, lower, upper)`对一元函数f计算从lower到upper的定积分

### 用filter函数作迭代
向量自身迭代

## 函数进阶
- 任何成分都是R的对象
- 任何活动都是调用函数

句法作用域(lexical scoping)， 即函数运行中需要使用某个变量时， 从其定义时的环境向外层逐层查找， 而不是在调用时的环境中查找

动态查找(dynamic lookup)，函数运行时在找到某个变量对应的存储空间后， 会使用该变量的当前值，而不是函数定义的时候该变量的历史值

辅助嵌套函数：只能在定义它的函数内运行， 不能被直接调用， 可以看成是函数内的私有函数

懒惰求值：懒惰求值使得缺省值在初次访问时， 是在函数内的环境（局部变量作用域）内求值的， 不是在其调用处的环境内求值

将每一个小问题编写成一个单独的函数，编写一系列的测试函数， 对每个函数进行测试，最好能有自动化的测试策略

### 程序调试
在函数中加browser()可以进行断点调试，在调试命令行
输入变量名查看变量值；
用`n`命令或者换行键逐句运行；
用`s`命令跟踪进调用的函数内部逐句运行；
用`f`命令快速执行到循环末尾或者函数末尾；
用`c`命令恢复正常运行，不再跟踪；
用`Q`命令强行终止程序运行。

条件断点: 用browser()函数与R的if结构配合可以制作条件断点

`stop()`、`warning()`、`message()`，停止，警告和提示

函数`stopifnot`可以指定自变量的若干个条件， 当自变量不符合条件时自动出错停止。类似于Python中的assert

### 泛函（functionals）
使用purrr包中的`map`函数及其变种进行各种泛函分析。
`map`函数要求的输入格式是`map(data, function)`, map总是返回列表

`map`变种及其性质
- `map_lgl`：返回逻辑向量
- `map_int`：返回整型向量
- `map_dbl`: 返回双精度浮点型向量(double类型)
- `map_chr`: 返回字符型向量
- `modify`: 输出的数据类型与输入的数据类型一致，可用于对数据框特定列进行修改，如`modify(dataframe, ~ if(is.numeric(.x) .x-median(.x) else .x)`，可以先判断是否是数值型列，然后每一项减去中位数，也可以用`modify`的变种`modify_if()`写作`modify_if(dataframe, is.numeric, ~ .x-median(.x))`
- `map2`: 支持两个输入自变量，输出也是一个列表，若提前已知输出类型，可用相应变种指定输出类型
- `walk`: 狭义上讲，这个函数不产生输出也即不返回数据，适用于一些作图和保存数据到文件的情况，如`d.class %>% split(d.class, d.class[['sex']]) %>% walk(paste0("class", names(.), '.csv'), ~ write.csv(.x, file=.y))`，这条命令首先将一个数据框按某一列分成两个，然后将分开的数据框分别写到两个文件中
- `imap`: 如果x有元素名，`imap(x, f)`相当于`map2(x, names(x), f)`； 如果x没有元素名，`imap(x, f)`相当于`map2(x, seq_along(x), f)`
- `pmap`: 支持多个输入自变量
- `...`： 形参, 一个例子理解一下：`map_dbl(d.class[,3:5], mean, trim=0.10)`中的`trim=0.10`就是map函数的`...`形参

`purrr::reduce`函数: 可以用来对输入列表或向量的元素逐次的用给定的函数进行合并运算，类似于用`sum`和`prod`进行累加和累乘
`purrr::accumulate`函数: 就像`reduce`函数是`sum`的推广，`accumulate`函数就像是`cumsum`的推广

示性函数：返回逻辑向量的函数，示性函数可以作为泛函的输入，purrr包中以示性函数为输入的泛函有
- `keep(.x, .p)`: 保留满足`.p`条件的元素
- `discard`: 与`keep`相反

基本R中的泛函： `apply`类函数
- `apply(x, 2, fun)`: 输入矩阵或数据框，返回矩阵或数据框
- `lapply(x, fun, ...)`: 输入列表，返回列表，`...`表示可以添加参数到`fun`中
- `sapply()`: 优先尝试返回向量或矩阵，不可行时才返回列表
- `mapply()`: 可以支持多个输入自变量，类似于`map2`

函数工厂是指返回函数的函数


## 取颜色
```R
library(RColorBrewer)
display.brewer.all()
display.brewer.pal(n = 8, name = 'Dark2')
brewer.pal(n = 8, name = "Dark2")
# "#1B9E77" "#D95F02" "#7570B3" "#E7298A" "#66A61E" "#E6AB02" "#A6761D" "#666666"
colorRampPalette(brewer.pal(8,"Dark2"))(9)
# "#1B9E77" "#C16610" "#8D6B86" "#BC4399" "#A66753" "#96A713" "#D59D08" "#9D7426" "#666666"
# 通过这种方法可以构建新的颜色板然后从其中取任意数量的颜色
```

## 批量读入文件，合并到一个数据框中
```R
# 这里以读入三个read depth文件为例
listfiles <- dir(path = 'depth', pattern = paste0('^', 'Sample_', samplename), full.names = T)
listdepth <- vector('list', length = 3)
for (i in seq_along(listdepth)) {
  listdepth[[i]] <- read.delim(listfiles[[i]], col.names = c('ref','position','depth'), header = F)
}
avgDepth <- do.call(rbind, listdepth)
```

`Ctrl+Alt+Shift+R`插入函数文档注释 <https://zhuanlan.zhihu.com/p/150568854>




  