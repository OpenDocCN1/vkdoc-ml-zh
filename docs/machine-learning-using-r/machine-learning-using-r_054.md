# 结果的点图
dotplot(results)
```

你可以在图 8-2 中看到，不同参数值之间的准确率变化不大。这可能意味着我们的搜索不够全面，或者模型能够在少于 100 棵树的随机森林模型下学习到数据的大部分特征。此外，应该增加自变量的列表。



### 8.3.2 手动网格搜索

本节将讨论用于手动网格搜索优化的模型细节。

*   *响应变量*：`ProductChoice`

*   *预测变量*：`MembershipPoints`、`CustomerAge`、`PurchaseTenure`、`CustomerPropensity` 和 `LastPurchaseDuration`

*   *误差计算*：交叉验证

*   *模型类型*：学习向量量化（LVQ）

```
