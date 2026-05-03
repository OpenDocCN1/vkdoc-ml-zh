# Scarcity in the data - More white space means, more scarcity
image(sample(MarketBasket, 100))
```

#### 6.10.2.1 Apriori

可以修改支持度和置信度，以便在此模型中提供灵活性。

1.  模型构建

    我们使用 `arules` 包中的 Apriori 函数来演示市场购物篮分析，其中 `support` 和 `confidence` 为常量值：

    ```
    library(arules)
    MarketBasketRules<-apriori(MarketBasket, parameter =list(support =0.2, confidence =0.8, minlen =2))
    MarketBasketRules
    Parameter specification:
    confidence minval smax arem  aval originalSupport support minlen maxlen
    0.8   0.1    1 none FALSE          TRUE    0.2       2     10
    target   ext
    rules FALSE
    Algorithmic control:
    filter tree heap memopt load sort verbose
    0.1 TRUE TRUE  FALSE TRUE    2    TRUE
    writing ... [190 rule(s)] done [0.00s].
    creating S4 object  ... done [0.00s].
    ```

2.  模型摘要

    前五条规则及其提升度：

    ```
    {beef,fruit} => {vegetables} - 1.291832
    {biscuits,bread and cake,frozen foods,vegetables} => {fruit} - 1.288442
    {biscuits,milk cream,vegetables} => {fruit} - 1.275601
    {bread and cake,fruit,sauces gravy pkle} =>{vegetables} - 1.273765
    {biscuits, bread and cake,vegetables} => {fruit} - 1.270252
    summary(MarketBasketRules)
    set of 190 rules
    rule length distribution (lhs + rhs):sizes
    2  3  4  5
    4 93 84  9
    Min. 1st Qu.  Median    Mean 3rd Qu.    Max.
    2.000   3.000   3.000   3.516   4.000   5.000
    summary of quality measures:
    support         confidence          lift
    Min.   :0.2002   Min.   :0.8003   Min.   :1.106
    1st Qu.:0.2082   1st Qu.:0.8164   1st Qu.:1.138
    Median :0.2260   Median :0.8299   Median :1.160
    Mean   :0.2394   Mean   :0.8327   Mean   :1.169
    3rd Qu.:0.2642   3rd Qu.:0.8479   3rd Qu.:1.187
    Max.   :0.3980   Max.   :0.8941   Max.   :1.292
    mining info:
    data ntransactions support confidence
    MarketBasket          4601     0.2        0.8
    ```

按提升度对杂货规则进行排序：

```
inspect(sort(MarketBasketRules, by ="lift")[1:5])
lhs                    rhs            support confidence     lift
1 {beef,
fruit}             => {vegetables} 0.2143012  0.8313659 1.291832
2 {biscuits,
bread and cake,
frozen foods,
vegetables}        => {fruit}      0.2019126  0.8294643 1.288442
3 {biscuits,
milk cream,
vegetables}        => {fruit}      0.2206042  0.8211974 1.275601
4 {bread and cake,
fruit,
sauces gravy pkle} => {vegetables} 0.2184308  0.8197390 1.273765
5 {biscuits,
bread and cake,
vegetables}        => {fruit}      0.2642904  0.8177539 1.270252
