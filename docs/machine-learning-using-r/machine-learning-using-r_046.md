# 打印 ntree=20 的结果
print(fit_20)
```

以下是使用随机森林算法中 20 棵树的结果。

*   模型 1：树的大小 = 50

```
Call:
randomForest(formula = factor(ProductChoice) ~ MembershipPoints
+      CustomerAge + PurchaseTenure + CustomerPropensity
+ LastPurchaseDuration,  data = Data, importance = TRUE, ntree = 20)
Type of random forest: classification
Number of trees: 20
No. of variables tried at each split: 2
OOB estimate of  error rate: 64.27%
Confusion matrix:
1    2    3   4 class.error
1 550 1035  495 104   0.7481685
2 730 1927 1051 199   0.5067827
3 449 1300 1005 165   0.6557040
4 149  450  300  91   0.9080808
```

以下是使用随机森林算法中 50 棵树的结果。

```
fit_50 <-randomForest(factor(ProductChoice) ~MembershipPoints +CustomerAge +PurchaseTenure +CustomerPropensity +LastPurchaseDuration,
data=Data,
importance=TRUE,
ntree=50)
