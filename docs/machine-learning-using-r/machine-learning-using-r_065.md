# plot the effect of parameters on accuracy
plot(model)
```

图 8-4 中的自动网格搜索优化显示，最佳模型的参数为 size=22 和 k=21，对应的准确率为 0.3512。这与我们的手动网格搜索结果不同，手动网格搜索的最优参数为 size=5 和 k=3，准确率为 0.3581。



### 8.3.4 最优搜索

本节讨论最优搜索优化模型的细节。

*   *响应变量*：`ProductChoice`

*   *预测变量*：`MembershipPoints`、`CustomerAge`、`PurchaseTenure`、`CustomerPropensity` 和 `LastPurchaseDuration`

*   *误差计算*：交叉验证

*   *模型类型*：递归划分与回归树

观察我们在 `train` 函数的 `tuneGrid` 参数中使用的以下三个 `expand.grids`。

*   *手动搜索*：`expand.grid(.mtry=c(sqrt(ncol(dataset)-2)))`

*   *手动网格搜索*：`expand.grid(size=c(5,10,20,50), k=c(1,2,3,4,5))`

*   *最优搜索*：`expand.grid(.cp=seq(0,0.1,by=0.01))`

在最优搜索中，`expand.grid` 的参数更加精细，这意味着算法能够比其他方法更好地收敛到全局最优解。例如，通过修改 `seq` 函数中的 `by = 0.01` 以增加小数位数，可以进一步提高精细度。但请记住，提高精细度会增加计算开销。

```
