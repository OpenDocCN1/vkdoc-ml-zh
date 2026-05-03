# 顺序执行
system.time(rf_credit_model <-randomForest(credit_train[-17],
credit_train$default,
ntree =1000))
user  system elapsed
1.8     0.0     1.8
```

#### 10.1.3.4 构建模型：并行

在代码的并行版本中，我们不直接使用 `ntree` = 1000 参数（意味着构建 1000 棵决策树）的随机森林模型，而是使用带有 `%dopar%` 的 `foreach` 函数，这样我们可以将构建 1000 棵决策树的过程拆分为四个进程。每个部分使用 `randomForest` 函数构建 250 棵决策树。

```
