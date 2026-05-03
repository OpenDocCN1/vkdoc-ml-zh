# 选择最佳调优配置
dataset <-Data
```

这里，我们设置 `trainControl` 函数，其 `method="repeatedCV"`，表示使用重复交叉验证，并在 `tunegrid` 上进行参数调优。

```
