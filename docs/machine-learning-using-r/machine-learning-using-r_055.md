# Tune algorithm parameters using a manual grid search.
seed <-917;
dataset <-Data
```

此处，我们设置 `trainControl` 函数，其中 `method="repeatedCV"` 表示使用重复交叉验证，搜索 `method = "grid"` 表示在 `grid` 定义的网格中进行搜索。

```
