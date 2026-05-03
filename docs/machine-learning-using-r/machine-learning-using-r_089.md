# 装袋 CART
set.seed(917)
fit.treebag <-train(factor(ProductChoice) ~MembershipPoints +CustomerAge +PurchaseTenure +CustomerPropensity +LastPurchaseDuration, data=dataset, method="treebag", metric=metric, trControl=control)
Loading required package: plyr
Loading required package: e1071
```

以下代码片段拟合了一个随机森林模型。

```
