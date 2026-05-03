# 使用 spark.glm 拟合一个 "gaussian" 族的广义线性模型
gaussianGLM |t|)
(Intercept)            -80034    32619       -2.4536   0.014387
StoreArea              58.172    9.8507      5.9054    5.4833e-09
StreetHouseFront       136.98    80.828      1.6947    0.090578
BasementArea           23.623    3.7224      6.3461    3.9629e-10
LawnArea               0.77459   0.19875     3.8973    0.0001066
Rating                 35402     1519.4      23.3      0
SaleType_NewHouse      -12979    31904       -0.40681  0.68427
SaleType_FirstResale   10117     32497       0.31132   0.75565
SaleType_SecondResale  -24563    32480       -0.75626  0.44975
SaleType_ThirdResale   -22562    34847       -0.64748  0.51754
SaleType_FourthResale  -32205    36778       -0.87567  0.38151
(Dispersion parameter for gaussian family taken to be 2012650630)
Null deviance: 4.9599e+12  on 711  degrees of freedom
Residual deviance: 1.4109e+12  on 701  degrees of freedom
AIC: 17286
Number of Fisher Scoring iterations: 1
```

### 10.3.6 预测测试数据

在最后一步，你现在可以使用上一步构建的 ML 模型来预测测试数据集上的房价。请参考第 6 章了解该模型的评估标准。

```
#对 gaussianModel 进行预测
gaussianPredictions <-predict(gaussianGLM, gaussianDF_test)
names(gaussianPredictions) <-c('HOUSE_ID','HousePrice','StoreArea','StreetHouseFront','BasementArea','LawnArea','Rating','SaleType','ActualPrice','PredictedPrice')
gaussianPredictions$PredictedPrice <-round(gaussianPredictions$PredictedPrice,2.0)
showDF(gaussianPredictions[,9:10])
+-----------+--------------+
|ActualPrice|PredictedPrice|
+-----------+--------------+
|   139400.0|      128582.0|
|   157000.0|      202101.0|
|   178000.0|      164765.0|
|   120000.0|       50425.0|
|   130000.0|      155841.0|
|   582933.0|      333450.0|
|   309000.0|      255584.0|
|   176000.0|      192695.0|
|   125000.0|      132784.0|
|   130000.0|      140085.0|
|   169990.0|      183082.0|
|   213000.0|      222965.0|
|   144000.0|      122123.0|
|   118500.0|      158940.0|
|   138000.0|      116004.0|
|   437154.0|      346572.0|
|   230000.0|      261396.0|
|    82000.0|       61949.0|
|    85000.0|      119914.0|
|   214900.0|      218930.0|
+-----------+--------------+
only showing top 20 rows
```



### 10.3.7 停止 SparkR 会话

最后，当任务完成时，执行以下代码以释放为此进程持有的所有资源，例如 CPU 和内存。

```
sparkR.stop()
```

在此代码运行期间，您可以在浏览器中打开 `http://localhost:4040/jobs/` 并查看 Spark 作业的进度。对于执行此代码时自动生成的每个作业，您可以查看 DAG 可视化，了解 Spark 引擎实际如何执行该作业。

为了理解如何通过可视化来了解您的应用程序在 Spark 集群上的实际运行情况，请阅读 databricks 的这篇博文：

[`https://databricks.com/blog/2015/06/22/understanding-your-spark-application-through-visualization.html`](https://databricks.com/blog/2015/06/22/understanding-your-spark-application-through-visualization.html)

## 10.4 使用 H2O 在 R 中进行机器学习

在本书即将结束机器学习之旅之际，我们想为 R 用户介绍另一个强大的平台，名为 H2O。我们之前讨论了一些强大的机器学习技术，如深度学习、文本分析、集成方法等。这些技术无法在单台机器上执行，需要高性能计算。

R 是一种流行的语言，并且能够出色地适应不同平台，它提供了将自身集成到强大的高性能计算环境中的选项。在前面的章节中，我们展示了一些示例，例如 Microsoft Cognitive Services、Spark 和其他 Apache 产品。在最后一节中，我们将介绍 H2O，这是一个用于大数据分析的开源高性能集群。

H2O 由 H2O.ai（前身为 Oxdata）开发和维护，这是一家成立于 2011 年的初创公司。H2O 被宣传为“用于大数据科学的开源内存预测引擎”。它提供了令人印象深刻的一系列机器学习算法。H2O R 包提供了用于构建 GLM、GBM、K-means、朴素贝叶斯、主成分分析、随机森林和深度学习（多层神经网络模型）的函数。

H2O 是一个 Java 虚拟机，经过优化，可在集群上对分布式、并行的机器学习算法进行“内存中”处理。“集群”是一种软件结构，可以在您的笔记本电脑、服务器上启动，也可以在由真实机器（包括构成 Hadoop 集群的计算机）组成的集群的多个节点上启动。根据最新文档，H2O 软件可以在传统操作系统上运行，例如 Microsoft Windows（7 或更高版本）、MacOS X（10.9 或更高版本）和 Linux（Ubuntu 12.04；RHEL/CentOS 6 或更高版本）。它也可以在大型数据系统上运行，特别是 Apache Hadoop 分布式文件系统（HDFS），并且可以在几种流行的虚拟机上使用，例如 Cloudera（5.1 或更高版本）、MapR（3.0 或更高版本）和 Hortonworks（HDP 2.1 或更高版本）。它还可以在云计算环境中运行，例如使用 Amazon EC2、Google Compute Engine 和 Microsoft Azure。H2O Sparkling Water 软件在 Apache Spark 上获得了 databricks 认证。

对于 R，H2O 包可在 CRAN 上获取。在继续 H2O 的演示之前，我们建议您访问以下 URL，这些 URL 包含一些文档齐全的资料：

*   H2O 包的完整文档：[`https://cran.r-project.org/web/packages/h2o/h2o.pdf`](https://cran.r-project.org/web/packages/h2o/h2o.pdf)

*   另一份关于 H2O 的文档可在 h2o.ai 上找到：[`http://docs.h2o.ai/h2o/latest-stable/h2o-docs/index.html`](http://docs.h2o.ai/h2o/latest-stable/h2o-docs/index.html)

*   更多针对 H2O 的机器学习算法实现：[`https://github.com/h2oai/h2o-3/tree/master/h2o-r/demos`](https://github.com/h2oai/h2o-3/tree/master/h2o-r/demos)

*   H2O 的安装：此处提供了用户友好且易于遵循的安装说明：`http://h2o-release.s3.` [`amazonaws.com/h2o/master/1735/docs-website/Ruser/Rinstall.html`](http://amazonaws.com/h2o/master/1735/docs-website/Ruser/Rinstall.html)

*   关于使用 H2O 在 R 中进行高性能机器学习的演示文稿：[`http://www.stat.berkeley.edu/~ledell/docs/h2o_hpccon_oct2015.pdf`](http://www.stat.berkeley.edu/%257Eledell/docs/h2o_hpccon_oct2015.pdf)

### 10.4.1 安装包

完成先决条件的安装后，以下代码将获取适用于 R 的最新 H2O 版本并将其安装在本地系统中。

注意：

*   在尝试此代码之前，建议具备良好的互联网连接。所有计算（在高度优化的 Java 代码中）都在 H2O 集群中执行，并由来自 R 的 REST 调用启动。

*   建议不要在本地机器上使用大量数据试验这些代码（在本地机器上运行以下代码中显示的演示是安全的）。

```
