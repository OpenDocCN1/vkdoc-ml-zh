# 选择最佳调优配置
dataset <-Data
```

这里，我们设置 `trainControl` 函数，其 `method="repeatedCV"`，表示使用重复交叉验证，并将预测变量搜索设置为 `random`。

![../images/416805_2_En_8_Chapter/416805_2_En_8_Fig6_HTML.jpg](img/416805_2_En_8_Fig6_HTML.jpg)

图 8-6

跨交叉验证集和随机选择预测变量的准确率

```
