# 随机森林
set.seed(917)
fit.rf <-train(factor(ProductChoice) ~MembershipPoints +CustomerAge +PurchaseTenure +CustomerPropensity +LastPurchaseDuration, data=dataset, method="rf", metric=metric, trControl=control)
```

以下代码使用 Caret 包中的`resamples()`函数汇总了这两种方法的装袋结果。

```
