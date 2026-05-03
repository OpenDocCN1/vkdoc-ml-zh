# 选取记录样本
Data <-data[sample(nrow(data),size=10000),]
```

以下是使用随机森林算法中 20 棵树的结果。

```
fit_20 <-randomForest(factor(ProductChoice) ~MembershipPoints +CustomerAge +PurchaseTenure +CustomerPropensity +LastPurchaseDuration,
data=Data,
importance=TRUE,
ntree=20)
