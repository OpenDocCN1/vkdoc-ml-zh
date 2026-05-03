# 加载包
library(randomForest)
library(mlbench)
library(caret)
```

在本例中，我们提出了一个用于评估的自定义函数。`randomForest` 的算法被继承用于分类问题。这是一种创建自定义搜索函数的高级方法。

![../images/416805_2_En_8_Chapter/416805_2_En_8_Fig7_HTML.jpg](img/416805_2_En_8_Fig7_HTML.jpg)

图 8-7

跨交叉验证样本和参数 mtry 的准确率

```
