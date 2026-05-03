# stack using random forest
set.seed(917)
stack.rf <-caretStack(models, method="rf", metric="Accuracy", trControl=stackControl)
print(stack.rf)
A rf ensemble of 2 base models: lda, rpart, glm, knn, svmRadial
Ensemble results:
Random Forest
30000 samples
5 predictor
2 classes: 'A', 'B'
No pre-processing
Resampling: Cross-Validated (10 fold, repeated 3 times)
Summary of sample sizes: 27000, 26999, 27000, 27000, 27000, 27001, ...
Resampling results across tuning parameters :
mtry  Accuracy   Kappa
2     0.6372440  0.1944063
3     0.6356217  0.1927612
5     0.6335549  0.1885745
Accuracy was used to select the optimal model using  the largest value.
The final value used for the model was mtry = 2.
```

使用`randomForest`，我们得到了接近 63.7%的准确率，这与`glm`的准确率相近，但略低一些。因此，对于这个实验，使用`glm`进行堆叠效果最好。再次强调，某些方法之间的相关性很高，因此将它们添加到堆叠中不会有利于模型准确率的提升。

## 8.7 高级主题：机器学习模型的贝叶斯优化

在机器学习中，超参数调优扮演着重要角色。数据科学家现在在将最终模型投入生产之前，会关注参数的调优。因此，简要介绍一种最重要的优化技术——贝叶斯优化——是很重要的。Yachen Yan 最近发布了一个用于 R 语言贝叶斯优化的新包。我们将展示如何将此包应用于房价数据。

贝叶斯优化是一种为黑箱函数（模型评估指标作为超参数的函数）寻找全局最优点的方法，且无需导数。Jonas Mockus 的工作在学术界广受好评；关于此主题的全面介绍可参见《贝叶斯方法在全局优化中的应用：理论与应用》，Jonas Mockus，Kluwer Academic (2013)。

对于这个例子，我们将首先通过随机调优获得一组初始超参数。这将为我们生成在广泛范围内分布的多个值。这里我们创建了 20 个随机参数。该示例的灵感来源于辉瑞公司总监 Max Kuhn 在 revolutions 上发表的文章。文章可通过[`http://blog.revolutionanalytics.com/2016/06/bayesian-optimization-of-machine-learning-models.html`](http://blog.revolutionanalytics.com/2016/06/bayesian-optimization-of-machine-learning-models.html)访问。

```
setwd("C:/Personal/Machine Learning/Chapter 8/");
library(caret)
library(randomForest)
library(class)
library(ipred)
library(GPfit)
