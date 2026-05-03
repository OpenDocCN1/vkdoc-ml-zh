# 分割数据框
credit_train <-credit[train_sample, ]
credit_test  <-credit[-train_sample, ]
```

#### 10.1.3.3 构建模型：串行

注意在创建的训练数据上以串行方式执行随机森林模型所需的时间。

```
在数据上训练模型
library(randomForest)
