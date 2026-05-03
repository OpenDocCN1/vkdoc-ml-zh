# 并行执行
system.time(
rf_credit_model_parallel <-foreach(nt =rep(250,4),
.combine = combine ,
.packages ='randomForest')
%dopar%
randomForest(
credit_train[-17],
credit_train$default,
ntree = nt))
user  system elapsed
0.33    0.09    1.73
```

#### 10.1.3.5 停止集群

停止所有集群并恢复串行执行。

```
