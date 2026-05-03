# 汇总结果
boosting_results <-resamples(list(c5.0=fit.c50, gbm=fit.gbm))
summary(boosting_results)
Call:
summary.resamples(object = boosting_results)
Models: c5.0, gbm
Number of resamples: 30
Accuracy
Min. 1st Qu. Median   Mean 3rd Qu.   Max. NA's
c5.0 0.376  0.3917 0.4008 0.4004  0.4088 0.4226    0
gbm  0.398  0.4112 0.4153 0.4158  0.4209 0.4286    0
Kappa
Min. 1st Qu.  Median    Mean 3rd Qu.    Max. NA's
c5.0 0.00000 0.02886 0.04001 0.03855 0.05701 0.07496    0
gbm  0.05248 0.07366 0.08241 0.08171 0.08875 0.10530    0
dotplot(boosting_results)
```

我们可以看到，C5.0 算法的最佳模型准确率为 40.5%，而 GBM 模型的准确率为 41.5%（见图 8-16）。梯度提升似乎能更好地拟合数据。

### 8.6.3 混合 KNN 与 Rpart

混合是一种集成方法，其中不同模型的输出以一定权重进行组合，并非所有模型输出都被同等对待。以下示例使用了两种技术进行混合：

*   `knn`

*   `rpart`

在本例中，我们将把 `knn` 和 `rpart` 方法作为模型的线性组合进行混合。这些模型将通过 `caretEmseble()` 函数进行组合。

`caretEnsemble` 是一个用于创建 Caret 模型集成的 R 包。该包的详细信息可访问 [`https://cran.r-project.org/web/packages/caretEnsemble/vignettes/caretEnsemble-intro.html`](https://cran.r-project.org/web/packages/caretEnsemble/vignettes/caretEnsemble-intro.html) 获取。

```
混合（模型的线性组合）
