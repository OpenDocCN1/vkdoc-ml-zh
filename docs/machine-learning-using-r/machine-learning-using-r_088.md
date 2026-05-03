# 装袋算法示例
control <-trainControl(method="repeatedcv", number=10, repeats=3)
metric <- "Accuracy"
```

以下代码片段拟合了一个装袋树模型。

```
