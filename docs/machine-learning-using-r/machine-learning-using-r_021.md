# store as  data frame
MarketBasketRules_df  {bread and cake}",..: 189 106 163 174 60 116 115 118 16 120 ...
$ support   : num  0.203 0.226 0.223 0.398 0.201 ...
$ confidence: num  0.835 0.811 0.804 0.8 0.815 ...
$ lift      : num  1.15 1.12 1.11 1.11 1.13 ...
```



#### 6.10.2.2 Eclat 算法

Eclat 是另一种用于关联规则挖掘的算法。该算法使用简单的交集运算进行等价类聚类，并采用自底向上的格遍历。以下两篇参考文献详细介绍了该算法：

1.  模型构建

    ```
    library(arules)
    With support = 0.2
    MarketBasketRules_Eclat<-eclat(MarketBasket, parameter =list(support =0.2, minlen =2))
    parameter specification:
    tidLists support minlen maxlen            target   ext
    FALSE     0.2      2     10 frequent itemsets FALSE
    algorithmic control:
    sparse sort verbose
    7   -2    TRUE
    writing  ... [531 set(s)] done [0.00s].
    Creating S4 object  ... done [0.00s].
    With support = 0.1
    MarketBasketRules_Eclat<-eclat(MarketBasket, parameter =list(supp =0.1, maxlen =15))
    parameter specification:
    tidLists support minlen maxlen            target   ext
    FALSE     0.1      1     15 frequent itemsets FALSE
    algorithmic control:
    sparse sort verbose
    7   -2    TRUE
    writing  ... [7503 set(s)] done [0.00s].
    Creating S4 object  ... done [0.00s].
    Observe the increase in the number of rules by decreasing the support. Experiment with the support value to see how the rules are changing.
    ```

2.  模型摘要

*   Mohammed J. Zaki, Srinivasan Parthasarathy, Mitsunori Ogihara, and Wei Li, 1997, “New Algorithms for Fast Discovery of Association Rules,” Technical Report 651, Computer Science Department, University of Rochester, Rochester, NY 14627.

*   Christian Borgelt (2003), “Efficient Implementations of Apriori and Eclat,” Workshop of Frequent Item Set Mining Implementations (FIMI), Melbourne, FL, USA.

支持度最高的前五条规则：

```
{bread and cake,milk cream} 0.5079331
{bread and cake,fruit} 0.5053249
{bread and cake,vegetables} 0.4994566
{fruit,vegetables} 0.4796783
{baking needs,bread and cake} 0.4762008
summary(MarketBasketRules_Eclat) # the model with support = 0.1
set of 531 itemsets
most frequent items:
bread and cake     vegetables          fruit   baking needs   frozen foods
196            137            136            130            122
(Other)

element (itemset/transaction) length distribution:sizes
2   3   4   5
187 260  81   3
Min. 1st Qu.  Median    Mean 3rd Qu.    Max.
2.000   2.000   3.000   2.812   3.000   5.000
summary of quality measures:
support
Min.   :0.2002
1st Qu.:0.2118
Median :0.2378
Mean   :0.2539
3rd Qu.:0.2768
Max.   :0.5079
includes transaction ID lists: FALSE
mining info:
data ntransactions support
MarketBasket          4601     0.2
```

按支持度对杂货规则进行排序：

```
inspect(sort(MarketBasketRules_Eclat, by ="support")[1:5])
items              support
1 {bread and cake,
milk cream}     0.5079331
2 {bread and cake,
fruit}          0.5053249
3 {bread and cake,
vegetables}     0.4994566
4 {fruit,
vegetables}     0.4796783
5 {baking needs,
bread and cake} 0.4762008
