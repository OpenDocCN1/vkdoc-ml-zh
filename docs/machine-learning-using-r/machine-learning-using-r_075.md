# 绘制参数对准确率的影响
plot(model)
```

随机搜索算法通常在调优时更快、更高效。在本例中，图 8-6 中的图表显示，该算法能够以更少的迭代次数优化问题。此示例中使用了随机森林模型。随机森林通过随机搜索可以快速优化。这为调优随机森林模型节省了大量时间。



### 8.3.6 自定义搜索

自定义搜索算法提供了引导算法优化调优参数的高级方法。机器学习的高级用户可以创建自己的搜索算法来优化超参数。在本例中，我们展示了一种这样的搜索优化方法。

*响应变量：* `ProductChoice`

*预测变量：* `MembershipPoints`、`CustomerAge`、`PurchaseTenure`、`CustomerPropensity` 和 `LastPurchaseDuration`

*误差计算：* 交叉验证

*模型类型：* 自定义随机森林

```
setwd("C:/Personal/Machine Learning/Chapter 8/");
library(caret)
library(randomForest)
library(class)
