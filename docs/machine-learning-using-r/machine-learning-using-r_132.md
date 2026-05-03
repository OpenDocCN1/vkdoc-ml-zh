# 接下来，我们下载、安装并初始化适用于 R 的 H2O 包。
install.packages("h2o", repos=(c("http://s3.amazonaws.com/h2o-release/h2o/rel-kahan/5/R", getOption("repos"))))
#或者您可以按如下方式从 CRAN 安装 h2o 包
install.packages("h2o")
```

### 10.4.2 初始化 H2O 集群

安装完成后，您可以通过调用 `init()` 函数来启动一个集群实例进行计算。

```
