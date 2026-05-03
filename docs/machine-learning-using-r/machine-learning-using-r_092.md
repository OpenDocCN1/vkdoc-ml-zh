# 提升算法示例
control <-trainControl(method="repeatedcv", number=10, repeats=3)
metric <- "Accuracy"
```

这里，我们拟合了一个 C5.0 决策树模型。

![../images/416805_2_En_8_Chapter/416805_2_En_8_Fig14_HTML.jpg](img/416805_2_En_8_Fig14_HTML.jpg)

图 8-14

各提升迭代次数下的准确率：C5.0

```
