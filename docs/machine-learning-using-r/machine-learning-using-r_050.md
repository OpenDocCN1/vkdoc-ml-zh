# 加载数据集
dataset <-Data
metric <- "Accuracy"
```

这里，我们设置 `trainControl` 函数，其中 `method="repeatedCV"` 表示使用重复交叉验证，搜索 `method = "grid"` 表示在 `tunegrid` 定义的网格中搜索。

```
