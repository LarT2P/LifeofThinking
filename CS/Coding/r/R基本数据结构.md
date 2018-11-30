---
title: R基本数据结构
date: 2018-02-15 21:46:06
categories: 
    - 编程与生活
tags: 
    - R
---

> 主站：https://lartpang.github.io/

之前一阵子，在EDX上学习了R语言的一门基础课程，这里做个总结。这门课程主要侧重于R的数据结构的介绍，当然也介绍了它的基本的绘图手段。

工作空间相关
============

    ls()

    ## character(0)

    rm(a)

    ## Warning in rm(a): 找不到对象'a'

    ls()

    ## character(0)

基本数据类型
============

1.  logical
    -   TRUE/FALSE/NA/T/F(推荐使用完整形式)/某些时候的0与非0
2.  numeric
    -   integer is numeric
    -   numeric not always integer
3.  character

Other atomic types:

-   double: higher precision
-   complex: complex numbers
-   raw: store raw bytes

> `is.*()`返回括号内内容是否是`*`对应类型。

    # logical
    TRUE

    ## [1] TRUE

    class(TRUE)

    ## [1] "logical"

    FALSE

    ## [1] FALSE

    class(NA)

    ## [1] "logical"

    T

    ## [1] TRUE

    F

    ## [1] FALSE

    # numeric
    2

    ## [1] 2

    class(2)

    ## [1] "numeric"

    2.5

    ## [1] 2.5

    class(2.5)

    ## [1] "numeric"

    2L

    ## [1] 2

    class(2L) 

    ## [1] "integer"

    is.numeric(2)

    ## [1] TRUE

    is.numeric(2L)

    ## [1] TRUE

    #integer is numeric 
    #numeric not always integer
    is.integer(2)

    ## [1] FALSE

    is.integer(2L)

    ## [1] TRUE

    # character
     "I love data science!"

    ## [1] "I love data science!"

     class("I love data science!")

    ## [1] "character"

强制转换
--------

> `as.*()`返回括号内内容转化为`*`对应类型后的结果，有些情况无法转换。

    as.numeric(TRUE)

    ## [1] 1

    as.numeric(FALSE)

    ## [1] 0

    as.character(4)

    ## [1] "4"

    as.numeric("4.5")

    ## [1] 4.5

    as.integer("4.5")

    ## [1] 4

    as.numeric("Hello")

    ## Warning: 强制改变过程中产生了NA

    ## [1] NA

向量 Vector
===========

-   Sequence of data elements
-   **Same basic type**
    -   Automatic coercion if necessary
-   character, numeric, logical
-   Single value = Vector

创建 `c()`或者利用`:`
---------------------

    # c()
    drawn_suits <- c("hearts", "spades", "diamonds",  
                       "diamonds", "spades")
    drawn_suits

    ## [1] "hearts"   "spades"   "diamonds" "diamonds" "spades"

    is.vector(drawn_suits)

    ## [1] TRUE

    # :
    1:5

    ## [1] 1 2 3 4 5

    is.vector(1:5)

    ## [1] TRUE

命名 `names()`
--------------

    remain <- c(11, 12, 11, 13)
    suits <- c("spades", "hearts", "diamonds", "clubs")
    names(remain) <- suits
    remain 

    ##   spades   hearts diamonds    clubs 
    ##       11       12       11       13

    #or
    remain <- c(spades = 11, hearts = 12,  
                  diamonds = 11, clubs = 13)
    remain

    ##   spades   hearts diamonds    clubs 
    ##       11       12       11       13

    #or
    remain <- c("spades" = 11, "hearts" = 12,  
                  "diamonds" = 11, "clubs" = 13)
    remain

    ##   spades   hearts diamonds    clubs 
    ##       11       12       11       13

单值仍为向量
------------

    my_apples <- 5 
    my_oranges <- "six" 
    is.vector(my_apples)

    ## [1] TRUE

    is.vector(my_oranges)

    ## [1] TRUE

    length(my_apples)

    ## [1] 1

    length(my_oranges)

    ## [1] 1

强制变换
--------

    drawn_ranks <- c(7, 4, "A", 10, "K", 3, 2, "Q")
    drawn_ranks

    ## [1] "7"  "4"  "A"  "10" "K"  "3"  "2"  "Q"

    class(drawn_ranks)

    ## [1] "character"

基本运算
--------

很自然的可以由单数的运算推广出来。

    # with number: +-*/
    earnings <- c(50, 100, 30)
    earnings * 3

    ## [1] 150 300  90

    earnings^2

    ## [1]  2500 10000   900

    # with vector: +-*/
    earnings <- c(50, 100, 30) 
    expenses <- c(30, 40, 80) 
    bank <- earnings - expenses
    ## sum() >
    sum(bank)

    ## [1] 30

    earnings > expenses

    ## [1]  TRUE  TRUE FALSE

    ## multiplication and division are done element-wise!
    earnings * c(1, 2, 3)

    ## [1]  50 200  90

子集
----

三种索引方式

-   序号（R从1开始）
-   名字 —— names()的利用
-   逻辑值

<!-- -->

    remain <- c(spades = 11, hearts = 12,  
                  diamonds = 11, clubs = 13)
    remain[1]

    ## spades 
    ##     11

    remain["spades"]

    ## spades 
    ##     11

    remain[c(4, 1)] # 此法可以用来交换或者抽取特定位置的元素

    ##  clubs spades 
    ##     13     11

    remain[c("clubs", "spades")]

    ##  clubs spades 
    ##     13     11

    # 逻辑值索引，短的会被自动循环使用
    remain[c(TRUE, FALSE)]

    ##   spades diamonds 
    ##       11       11

    remain[c(TRUE, FALSE, TRUE, FALSE)]

    ##   spades diamonds 
    ##       11       11

    # 负索引，“all but it”，返回除此之外的元素
    remain[-1]

    ##   hearts diamonds    clubs 
    ##       12       11       13

    remain[-c(1, 2)]

    ## diamonds    clubs 
    ##       11       13

    #remain[-"spades"] #can't work

矩阵 Matrix
===========

-   Vector: 1D array of data elements
-   Matrix: 2D array of data elements
-   Rows and columns
-   One atomic vector type

创建 `matrix()`
---------------

**默认按列填充**

    # 直接创建
    matrix(1:6, nrow = 2)

    ##      [,1] [,2] [,3]
    ## [1,]    1    3    5
    ## [2,]    2    4    6

    matrix(1:6, ncol = 3)

    ##      [,1] [,2] [,3]
    ## [1,]    1    3    5
    ## [2,]    2    4    6

    matrix(1:6, nrow = 2, byrow = TRUE)

    ##      [,1] [,2] [,3]
    ## [1,]    1    2    3
    ## [2,]    4    5    6

    # 循环创建
    matrix(1:3, nrow = 2, ncol = 3)

    ##      [,1] [,2] [,3]
    ## [1,]    1    3    2
    ## [2,]    2    1    3

    matrix(1:4, nrow = 2, ncol = 3)

    ## Warning in matrix(1:4, nrow = 2, ncol = 3): 数据长度[4]不是矩阵列数[3]的整
    ## 倍数

    ##      [,1] [,2] [,3]
    ## [1,]    1    3    1
    ## [2,]    2    4    2

    # 组合创建
    cbind(1:3, 1:3)

    ##      [,1] [,2]
    ## [1,]    1    1
    ## [2,]    2    2
    ## [3,]    3    3

    rbind(1:3, 1:3)

    ##      [,1] [,2] [,3]
    ## [1,]    1    2    3
    ## [2,]    1    2    3

    m <- matrix(1:6, byrow = TRUE, nrow = 2)
    rbind(m, 7:9)

    ##      [,1] [,2] [,3]
    ## [1,]    1    2    3
    ## [2,]    4    5    6
    ## [3,]    7    8    9

    cbind(m, c(10, 11))

    ##      [,1] [,2] [,3] [,4]
    ## [1,]    1    2    3   10
    ## [2,]    4    5    6   11

命名
----

`rownames()`, `colnames()`

    m <- matrix(1:6, byrow = TRUE, nrow = 2)
    rownames(m) <- c("row1", "row2")
    m

    ##      [,1] [,2] [,3]
    ## row1    1    2    3
    ## row2    4    5    6

    colnames(m) <- c("col1", "col2", "col3")
    m

    ##      col1 col2 col3
    ## row1    1    2    3
    ## row2    4    5    6

    # 直接命名
    m <- matrix(1:6, byrow = TRUE, nrow = 2,  
                  dimnames = list(c("row1", "row2"),  
                                  c("col1", "col2", "col3"))) 
    m

    ##      col1 col2 col3
    ## row1    1    2    3
    ## row2    4    5    6

强制转换
--------

    num <- matrix(1:8, ncol = 2)
    num

    ##      [,1] [,2]
    ## [1,]    1    5
    ## [2,]    2    6
    ## [3,]    3    7
    ## [4,]    4    8

    char <- matrix(LETTERS[1:6], nrow = 4, ncol = 3)
    char

    ##      [,1] [,2] [,3]
    ## [1,] "A"  "E"  "C" 
    ## [2,] "B"  "F"  "D" 
    ## [3,] "C"  "A"  "E" 
    ## [4,] "D"  "B"  "F"

    num <- matrix(1:8, ncol = 2)
    char <- matrix(LETTERS[1:6], nrow = 4, ncol = 3)
    cbind(num, char)

    ##      [,1] [,2] [,3] [,4] [,5]
    ## [1,] "1"  "5"  "A"  "E"  "C" 
    ## [2,] "2"  "6"  "B"  "F"  "D" 
    ## [3,] "3"  "7"  "C"  "A"  "E" 
    ## [4,] "4"  "8"  "D"  "B"  "F"

子集运算
--------

    m <- matrix(sample(1:15, 12), nrow = 3)
    rownames(m) <- c("r1", "r2", "r3") 
    colnames(m) <- c("a", "b", "c", "d")
    m

    ##     a  b  c  d
    ## r1  7  5  6 10
    ## r2  3  9 12  8
    ## r3 15 13  2  4

    m[1,3]

    ## [1] 6

    m[3,] 

    ##  a  b  c  d 
    ## 15 13  2  4

    m[,3]

    ## r1 r2 r3 
    ##  6 12  2

    m[4] # 默认按列计数 

    ## [1] 5

    m[2, c(2, 3)]

    ##  b  c 
    ##  9 12

    m[c(1, 2), c(2, 3)]

    ##    b  c
    ## r1 5  6
    ## r2 9 12

    m[c(1, 3), c(1, 3, 4)]

    ##     a c  d
    ## r1  7 6 10
    ## r3 15 2  4

    m["r2","c"]

    ## [1] 12

    m[2,"c"]

    ## [1] 12

    m[3, c("c", "d")]

    ## c d 
    ## 2 4

    m[c(FALSE, FALSE, TRUE),  
        c(FALSE, TRUE, FALSE, TRUE)] 

    ##  b  d 
    ## 13  4

    m[c(FALSE, FALSE, TRUE),  
        c(FALSE, TRUE)]

    ##  b  d 
    ## 13  4

矩阵运算
--------

-   `colSums(), rowSums()`
-   Standard arithmetic possible
-   **Element-wise** computation

<!-- -->

    the_fellowship <- c(316, 556) 
    two_towers <- c(343, 584) 
    return_king <- c(378, 742) 
    lotr_matrix <- rbind(the_fellowship, two_towers, return_king) 
    colnames(lotr_matrix) <- c("US", "non-US") 
    rownames(lotr_matrix) <- c("Fellowship", "Two Towers",  
                                 "Return King") 
    lotr_matrix

    ##              US non-US
    ## Fellowship  316    556
    ## Two Towers  343    584
    ## Return King 378    742

    # 与数字 +-*/
    lotr_matrix / 1.12 

    ##                   US   non-US
    ## Fellowship  282.1429 496.4286
    ## Two Towers  306.2500 521.4286
    ## Return King 337.5000 662.5000

    lotr_matrix - 50

    ##              US non-US
    ## Fellowship  266    506
    ## Two Towers  293    534
    ## Return King 328    692

    # 与矩阵 +-*/ (这里不是线性代数中的矩阵计算)
    theater_cut <- matrix(c(50, 80, 100), nrow = 3, ncol = 2)
    theater_cut

    ##      [,1] [,2]
    ## [1,]   50   50
    ## [2,]   80   80
    ## [3,]  100  100

    lotr_matrix - theater_cut

    ##              US non-US
    ## Fellowship  266    506
    ## Two Towers  263    504
    ## Return King 278    642

    # 与向量
    lotr_matrix - c(50, 80, 100) #按列循环计算

    ##              US non-US
    ## Fellowship  266    506
    ## Two Towers  263    504
    ## Return King 278    642

因子 Factors
============

-   Factors for categorical variables
-   Limited number of different values
-   Belong to category

创建因子 `factor()`
-------------------

    blood <- c("B", "AB", "O", "A", "O", "O", "A", "B")
    blood

    ## [1] "B"  "AB" "O"  "A"  "O"  "O"  "A"  "B"

    blood_factor <- factor(blood) # 默认等级按照字母顺序定
    blood_factor

    ## [1] B  AB O  A  O  O  A  B 
    ## Levels: A AB B O

    str(blood_factor)

    ##  Factor w/ 4 levels "A","AB","B","O": 3 2 4 1 4 4 1 3

    # 自定义level
    blood_factor2 <- factor(blood, 
                              levels = c("O", "A", "B", "AB"))
    blood_factor2

    ## [1] B  AB O  A  O  O  A  B 
    ## Levels: O A B AB

    str(blood_factor2)

    ##  Factor w/ 4 levels "O","A","B","AB": 3 4 1 2 1 1 2 3

Rename factor levels
--------------------

    blood <- c("B", "AB", "O", "A", "O", "O", "A", "B")
    #1.1
    blood_factor <- factor(blood)
    levels(blood_factor) <- c("BT_A", "BT_AB", "BT_B", "BT_O")
    #1.2
    blood <- c("B", "AB", "O", "A", "O", "O", "A", "B") 
    blood_factor <- factor(blood) 
    factor(blood,  
             levels = c("O", "A", "B", "AB"),  
             labels = c("BT_O", "BT_A", "BT_B", "BT_AB"))

    ## [1] BT_B  BT_AB BT_O  BT_A  BT_O  BT_O  BT_A  BT_B 
    ## Levels: BT_O BT_A BT_B BT_AB

    #2
    factor(blood, labels = c("BT_A", "BT_AB", "BT_B", "BT_O"))

    ## [1] BT_B  BT_AB BT_O  BT_A  BT_O  BT_O  BT_A  BT_B 
    ## Levels: BT_A BT_AB BT_B BT_O

Ordered factor
--------------

    blood <- c("B", "AB", "O", "A", "O", "O", "A", "B")
    blood_factor <- factor(blood) 
    blood_factor[1] < blood_factor[2] 

    ## Warning in Ops.factor(blood_factor[1], blood_factor[2]): '<' not meaningful
    ## for factors

    ## [1] NA

    # 下面比较大小才是有意义的
    tshirt <- c("M", "L", "S", "S", "L", "M", "L", "M")
    tshirt_factor <- factor(tshirt, ordered = TRUE, 
                              levels = c("S", "M", "L"))
    tshirt_factor

    ## [1] M L S S L M L M
    ## Levels: S < M < L

    tshirt_factor[1] < tshirt_factor[2]

    ## [1] TRUE

列表 List
=========

*Vector - Matrix - List*

-   Vector: 1D, same type
-   Matrix: 2D, same type
-   List:
    -   Different R objects
    -   No coercion
    -   Loss of some functionality

创建列表 `list()`
-----------------

    list("Rsome times", 190, 5)

    ## [[1]]
    ## [1] "Rsome times"
    ## 
    ## [[2]]
    ## [1] 190
    ## 
    ## [[3]]
    ## [1] 5

    song <- list("Rsome times", 190, 5)
    is.list(song)

    ## [1] TRUE

命名列表
--------

    #1
    song <- list("Rsome times", 190, 5) 
    names(song) <- c("title", "duration", "track")
    song

    ## $title
    ## [1] "Rsome times"
    ## 
    ## $duration
    ## [1] 190
    ## 
    ## $track
    ## [1] 5

    #2
    song <- list(title = "Rsome times",  
                   duration = 190,  
                   track = 5)
    song

    ## $title
    ## [1] "Rsome times"
    ## 
    ## $duration
    ## [1] 190
    ## 
    ## $track
    ## [1] 5

    str(song)

    ## List of 3
    ##  $ title   : chr "Rsome times"
    ##  $ duration: num 190
    ##  $ track   : num 5

列表嵌套
--------

    similar_song <- list(title = "R you on time?", 
                           duration = 230)
    song <- list(title = "Rsome times", 
                   duration = 190, track = 5,  
                   similar = similar_song)
    str(song)

    ## List of 4
    ##  $ title   : chr "Rsome times"
    ##  $ duration: num 190
    ##  $ track   : num 5
    ##  $ similar :List of 2
    ##   ..$ title   : chr "R you on time?"
    ##   ..$ duration: num 230

子集运算
--------

`[` versus `[[`

    similar_song <- list(title = "R you on time?", 
                           duration = 230) 
    song <- list(title = "Rsome times", 
                   duration = 190, track = 5, 
                   similar = similar_song) 
    str(song)

    ## List of 4
    ##  $ title   : chr "Rsome times"
    ##  $ duration: num 190
    ##  $ track   : num 5
    ##  $ similar :List of 2
    ##   ..$ title   : chr "R you on time?"
    ##   ..$ duration: num 230

    song[1]

    ## $title
    ## [1] "Rsome times"

    song[[1]]

    ## [1] "Rsome times"

    song[c(1, 3)] 

    ## $title
    ## [1] "Rsome times"
    ## 
    ## $track
    ## [1] 5

    #song[[c(1, 3)]] #can't work
    #song[[1]][[3]] #can't work
    song[["duration"]]

    ## [1] 190

    song["duration"]

    ## $duration
    ## [1] 190

    song[c(FALSE, TRUE, TRUE, FALSE)]

    ## $duration
    ## [1] 190
    ## 
    ## $track
    ## [1] 5

    #song[[c(FALSE, TRUE, TRUE, FALSE)]] # can't work
    #song[[F]][[T]][[T]][[F]] #also

    # list in list
    song[[4]][[1]]

    ## [1] "R you on time?"

    song[[c(4, 1)]]

    ## [1] "R you on time?"

    song[c("duration", "similar")] 

    ## $duration
    ## [1] 190
    ## 
    ## $similar
    ## $similar$title
    ## [1] "R you on time?"
    ## 
    ## $similar$duration
    ## [1] 230

**\[\[ or \[ ?** + \[\[ to select list *element* + \[ results in
*sublist* + *\[\[ and $* to subset and extend lists

列表扩展
--------

这里引出了R中比较重要的一个符号`$`

    similar_song <- list(title = "R you on time?", 
                           duration = 230) 
    song <- list(title = "Rsome times", 
                   duration = 190, track = 5, 
                   similar = similar_song) 
    #$
    song$duration

    ## [1] 190

    #extending
    friends <- c("Kurt", "Florence",
                    "Patti", "Dave")
    song$sent <- friends #或者 song[["sent"]] <- friends
    song$similar$reason <- "too long"
    song

    ## $title
    ## [1] "Rsome times"
    ## 
    ## $duration
    ## [1] 190
    ## 
    ## $track
    ## [1] 5
    ## 
    ## $similar
    ## $similar$title
    ## [1] "R you on time?"
    ## 
    ## $similar$duration
    ## [1] 230
    ## 
    ## $similar$reason
    ## [1] "too long"
    ## 
    ## 
    ## $sent
    ## [1] "Kurt"     "Florence" "Patti"    "Dave"

数据框 Data Frame
=================

-   Observations 观测值
-   Variables 变量
-   Example: people
    -   each person = observation
    -   properties (name, age …) = variables
-   Rows = observations (persons)
-   Columns = variables (age, name, …)

不同的变量的观测值可以类型不同，但是变量自己的所有观测值类型一致。

多在导入数据时使用。

创建数据框
----------

    name <- c("Anne", "Pete", "Frank", "Julia", "Cath") 
    age <- c(28, 30, 21, 39, 35) 
    child <- c(FALSE, TRUE, TRUE, FALSE, TRUE) 
    df <- data.frame(name, age, child) 
    str(df)

    ## 'data.frame':    5 obs. of  3 variables:
    ##  $ name : Factor w/ 5 levels "Anne","Cath",..: 1 5 3 4 2
    ##  $ age  : num  28 30 21 39 35
    ##  $ child: logi  FALSE TRUE TRUE FALSE TRUE

命名数据框
----------

    name <- c("Anne", "Pete", "Frank", "Julia", "Cath") 
    age <- c(28, 30, 21, 39, 35) 
    child <- c(FALSE, TRUE, TRUE, FALSE, TRUE) 
    df <- data.frame(name, age, child)
    names(df) <- c("Name", "Age", "Child") 
    str(df)

    ## 'data.frame':    5 obs. of  3 variables:
    ##  $ Name : Factor w/ 5 levels "Anne","Cath",..: 1 5 3 4 2
    ##  $ Age  : num  28 30 21 39 35
    ##  $ Child: logi  FALSE TRUE TRUE FALSE TRUE

    df <- data.frame(Name = name, Age = age, Child = child) #also
    str(df)

    ## 'data.frame':    5 obs. of  3 variables:
    ##  $ Name : Factor w/ 5 levels "Anne","Cath",..: 1 5 3 4 2
    ##  $ Age  : num  28 30 21 39 35
    ##  $ Child: logi  FALSE TRUE TRUE FALSE TRUE

可见，这里的字符串向量，被自动转化为因子类型，所以可以设置参数来避免此隐含行为。

    name <- c("Anne", "Pete", "Frank", "Julia", "Cath") 
    age <- c(28, 30, 21, 39, 35) 
    child <- c(FALSE, TRUE, TRUE, FALSE, TRUE)
    df <- data.frame(name, age, child, 
                       stringsAsFactors = FALSE)
    str(df)

    ## 'data.frame':    5 obs. of  3 variables:
    ##  $ name : chr  "Anne" "Pete" "Frank" "Julia" ...
    ##  $ age  : num  28 30 21 39 35
    ##  $ child: logi  FALSE TRUE TRUE FALSE TRUE

子集运算
--------

Subset Data Frame \* Subsetting syntax from matrices and lists \* \[
from matrices \* \[\[ and $ from lists

    name <- c("Anne", "Pete", "Frank", "Julia", "Cath") 
    age <- c(28, 30, 21, 39, 35) 
    child <- c(FALSE, TRUE, TRUE, FALSE, TRUE) 
    people <- data.frame(name, age, child,  
                   stringsAsFactors = FALSE)

    # 类似矩阵的操作
    people[3,2] 

    ## [1] 21

    people[3,"age"]

    ## [1] 21

    people[,"age"]

    ## [1] 28 30 21 39 35

    people[3,] # 由于返回的是一个数据框，我的R notebook不显示数据框

    ##    name age child
    ## 3 Frank  21  TRUE

    people[c(3, 5), c("age", "child")] # 同上

    ##   age child
    ## 3  21  TRUE
    ## 5  35  TRUE

    # 类似列表的操作
    people$age

    ## [1] 28 30 21 39 35

    people[["age"]]

    ## [1] 28 30 21 39 35

    people[[2]]

    ## [1] 28 30 21 39 35

    ## 由于返回的是一个数据框，我的R notebook不显示数据框
    people["age"]

    ##   age
    ## 1  28
    ## 2  30
    ## 3  21
    ## 4  39
    ## 5  35

    people[2]

    ##   age
    ## 1  28
    ## 2  30
    ## 3  21
    ## 4  39
    ## 5  35

扩展数据框
----------

Extend Data Frame \* Add columns = add variables \* Add rows = add
observations

    name <- c("Anne", "Pete", "Frank", "Julia", "Cath") 
    age <- c(28, 30, 21, 39, 35) 
    child <- c(FALSE, TRUE, TRUE, FALSE, TRUE) 
    people <- data.frame(name, age, child,  
                   stringsAsFactors = FALSE)
    #Add column
    height <- c(163, 177, 163, 162, 157) 
    people$height <- height 
    str(people)

    ## 'data.frame':    5 obs. of  4 variables:
    ##  $ name  : chr  "Anne" "Pete" "Frank" "Julia" ...
    ##  $ age   : num  28 30 21 39 35
    ##  $ child : logi  FALSE TRUE TRUE FALSE TRUE
    ##  $ height: num  163 177 163 162 157

    ##also
    people[["height"]] <- height
    str(people)

    ## 'data.frame':    5 obs. of  4 variables:
    ##  $ name  : chr  "Anne" "Pete" "Frank" "Julia" ...
    ##  $ age   : num  28 30 21 39 35
    ##  $ child : logi  FALSE TRUE TRUE FALSE TRUE
    ##  $ height: num  163 177 163 162 157

    weight <- c(74, 63, 68, 55, 56) 
    cbind(people, weight)

    ##    name age child height weight
    ## 1  Anne  28 FALSE    163     74
    ## 2  Pete  30  TRUE    177     63
    ## 3 Frank  21  TRUE    163     68
    ## 4 Julia  39 FALSE    162     55
    ## 5  Cath  35  TRUE    157     56

    #Add row 这里要注意，有时候会出错
    tom <- data.frame("Tom", 37, FALSE, 183)
    #rbind(people, tom)
    #会报错：
    #Error : names do not match previous names
    tom <- data.frame(name = "Tom", age = 37, 
                child = FALSE, height = 183)
    rbind(people, tom) 

    ##    name age child height
    ## 1  Anne  28 FALSE    163
    ## 2  Pete  30  TRUE    177
    ## 3 Frank  21  TRUE    163
    ## 4 Julia  39 FALSE    162
    ## 5  Cath  35  TRUE    157
    ## 6   Tom  37 FALSE    183

排序
----

这里主要介绍了`sort()`与`order()`，其中，`order()`更适合用来为数据框调整顺序。

    str(people)

    ## 'data.frame':    5 obs. of  4 variables:
    ##  $ name  : chr  "Anne" "Pete" "Frank" "Julia" ...
    ##  $ age   : num  28 30 21 39 35
    ##  $ child : logi  FALSE TRUE TRUE FALSE TRUE
    ##  $ height: num  163 177 163 162 157

    #sort()直接对于向量元素进行了排序
    sort(people$age)

    ## [1] 21 28 30 35 39

    #order()会返回对应大小等级所实际在的位置
    ranks <- order(people$age) 
    ranks

    ## [1] 3 1 2 5 4

    people$age

    ## [1] 28 30 21 39 35

    people[ranks, ] #直接对行进行了排序

    ##    name age child height
    ## 3 Frank  21  TRUE    163
    ## 1  Anne  28 FALSE    163
    ## 2  Pete  30  TRUE    177
    ## 5  Cath  35  TRUE    157
    ## 4 Julia  39 FALSE    162

    #或者如下可以实现降序排序
    people[order(people$age, decreasing = TRUE), ] 

    ##    name age child height
    ## 4 Julia  39 FALSE    162
    ## 5  Cath  35  TRUE    157
    ## 2  Pete  30  TRUE    177
    ## 1  Anne  28 FALSE    163
    ## 3 Frank  21  TRUE    163

绘图 Graphics
=============

这里主要介绍了`graphics`包的`plot()`与`hist()`

`plot()`会根据不同的数据类型，而画出不同的图像

1.  plot() (categorical) 条形图 例如：`plot(countries$continent)`
2.  plot() (numerical) 散点图 例如：`plot(countries$population)`
3.  plot() (2x numerical) 散点图
    例如：`plot(countries$area, countries$population)`、
    `plot(log(countries$area), log(countries$population))`
4.  plot() (2x categorical) 某种条形图的变形
    例如：`plot(countries$continent, countries$religion)`

`hist()`可以绘制直方图 例如： `hist(africa$population)`、
`hist(africa$population, breaks = 10)`

Other graphics functions \* barplot() \* boxplot() \* pairs()

自定义绘图
----------

这里就是修改参数了。无需多讲。

这里，引出了函数`par()`，这是一个绘图的公共参数**列表**，里面存放着常用的一些绘图的公共属性，可以实现绘制多幅图形时，基本属性的一次性确定。

例如：

    par(col = "blue") 
    plot(mercury$temperature, mercury$pressure) 

常用的plot的属性有：

    plot(mercury$temperature, mercury$pressure, 
           xlab = "Temperature", 
           ylab = "Pressure", 
           main = "T vs P for Mercury", #标题
           type = "o", 
           col = "orange", 
           col.main = "darkgray", 
           cex.axis = 0.6, #cex系列属性表示缩放程度
           lty = 5, #Line Type
           pch = 4  #Plot Symbol
           )
           

多图绘制
--------

`mfrow`与`mfcol`参数可以在一个图形框里，用来放置多个图像，区别是，前者是将后面plot语句生成的图像按行填充，而后者是按列填充。

    #按行填充
    par(mfrow = c(2,2)) 
    plot(shop$ads, shop$sales) 
    plot(shop$comp, shop$sales) 
    plot(shop$inv, shop$sales) 
    plot(shop$size_dist, shop$sales)

    #按列填充
    par(mfcol = c(2,2)) 
    plot(shop$ads, shop$sales) 
    plot(shop$comp, shop$sales) 
    plot(shop$inv, shop$sales) 
    plot(shop$size_dist, shop$sales)

Reset the grid

    par(mfrow = c(1,1))

相较于这个，`layout()`函数设置的更为灵活。

    grid <- matrix(c(1, 1, 2, 3), nrow = 2, 
             ncol = 2, byrow = TRUE) 
    layout(grid)
    plot(shop$ads, shop$sales) #放在grid的1号位置
    plot(shop$comp, shop$sales) #放在grid的2号位置
    plot(shop$inv, shop$sales) #放在grid的3号位置

Reset the grid

    layout(1) 
    par(mfcol = c(1,1))

Reset all parameters

    old_par <- par() 
    par(col = "red") 
    plot(shop$ads, shop$sales) 
    par(old_par) 
    plot(shop$ads, shop$sales)

线性拟合
--------

引出函数`lm()` —— *linear
model*，\*\*lm(a~b)就是对a=k\*b+c进行线性拟合\*\*

    plot(shop$ads, shop$sales, 
          pch = 16, col = 2, 
          xlab = "advertisement", 
          ylab = "net sales")
    lm_sales <- lm(shop$sales ~ shop$ads)
    abline(coef(lm_sales), lwd = 2) #取模型系数，线宽为2，画直线
