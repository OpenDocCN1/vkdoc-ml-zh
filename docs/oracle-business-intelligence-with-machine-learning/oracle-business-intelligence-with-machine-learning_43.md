# 创建一个新列 id，并将其分配给 test_data 数据集的每一行。Seq_len() 创建一个序列，长度等于 test_data 的行数。
> test_data$id  row.names(test_data)  head(test_data[,c(16,1,15)]) # 显示 test_data 中 id 为 1 到 6 的 6 行数据的第 16、1 和 15 列。
id class  origin
1  1     1 Origin1
2  2     1 Origin1
3  3     1 Origin1
4  4     1 Origin1
5  5     1 Origin1
6  6     1 Origin1
> origin_pred2  origin_pred2$id  head(origin_pred2) # 显示 origin_pred2 数据中 id 为 1 到 6 的 6 行数据。
Origin1 Origin2 Origin3 prediction  origin id
1   1.000   0.000   0.000    Origin1 Origin1  1
2   0.994   0.004   0.002    Origin1 Origin1  2
3   1.000   0.000   0.000    Origin1 Origin1  3
4   1.000   0.000   0.000    Origin1 Origin1  4
5   1.000   0.000   0.000    Origin1 Origin1  5
6   1.000   0.000   0.000    Origin1 Origin1  6
> row.names(origin_pred2)  head(test_data) # 显示添加了 'origin' 和 'id' 列后的 test_data 的 6 行数据。
class Alcohol Malic.acid  Ash Alcanility.of.ash Magnesium Total.phenols
1     1   14.37       1.95 2.50              16.8       113          3.85
2     1   14.20       1.76 2.45              15.2       112          3.27
3     1   14.06       2.15 2.61              17.6       121          2.60
4     1   13.64       3.10 2.56              15.2       116          2.70
5     1   14.06       1.63 2.28              16.0       126          3.00
6     1   12.85       1.60 2.52              17.8        95          2.48
Flavanoids Nonflavanoid.phenols Proanthocyanins Color.intensity    Hue
1       3.49                 0.24            2.18            7.80   0.86
2       3.39                 0.34            1.97            6.75   1.05
3       2.51                 0.31            1.25            5.05   1.06
4       3.03                 0.17            1.66            5.10   0.96
5       3.17                 0.24            2.10            5.65   1.09
6       2.37                 0.26            1.46            3.93   1.09
OD280.OD315.of.diluted.wines Proline  origin id
1                         3.45    1480 Origin1  1
2                         2.85    1450 Origin1  2
3                         3.58    1295 Origin1  3
4                         3.36     845 Origin1  4
5                         3.71     780 Origin1  5
6                         3.63    1015 Origin1  6
> head(origin_pred2) # 显示 origin_pred2 的 6 行数据以及 id 列。
Origin1 Origin2 Origin3 prediction  origin id
1   1.000   0.000   0.000    Origin1 Origin1  1
2   0.994   0.004   0.002    Origin1 Origin1  2
3   1.000   0.000   0.000    Origin1 Origin1  3
4   1.000   0.000   0.000    Origin1 Origin1  4
5   1.000   0.000   0.000    Origin1 Origin1  5
6   1.000   0.000   0.000    Origin1 Origin1  6
> head(origin_pred2[,c(6,5,4)]) # 显示 origin_pred2 中仅包含第 6、5 和 4 列的 6 行数据。
id  origin prediction
1  1 Origin1    Origin1
2  2 Origin1    Origin1
3  3 Origin1    Origin1
4  4 Origin1    Origin1
5  5 Origin1    Origin1
6  6 Origin1    Origin1
> df1  df2  class(df1)
[1] "data.frame"
> df1_new  head(df1_new) # 显示 df1_new 中排序后的 6 行数据。
id  origin class Alcohol Malic.acid  Ash Alcanility.of.ash Magnesium
1  1 Origin1     1   14.37       1.95 2.50              16.8       113
2  2 Origin1     1   14.20       1.76 2.45              15.2       112
3  3 Origin1     1   14.06       2.15 2.61              17.6       121
4  4 Origin1     1   13.64       3.10 2.56              15.2       116
5  5 Origin1     1   14.06       1.63 2.28              16.0       126
6  6 Origin1     1   12.85       1.60 2.52              17.8        95
Total.phenols Flavanoids Nonflavanoid.phenols Proanthocyanins Color.intensity
1          3.85       3.49              0.24            2.18            7.80
2          3.27       3.39              0.34            1.97            6.75
3          2.60       2.51              0.31            1.25            5.05
4          2.70       3.03              0.17            1.66            5.10
5          3.00       3.17              0.24            2.10            5.65
6          2.48       2.37              0.26            1.46            3.93
Hue OD280.OD315.of.diluted.wines Proline
1 0.86                         3.45    1480
2 1.05                         2.85    1450
3 1.06                         3.58    1295
4 0.96                         3.36     845
5 1.09                         3.71     780
6 1.09                         3.63    1015
> df2_new  head(df2_new) # 显示 df2_new 排序后数据框的 6 行数据。
Origin1 Origin2 Origin3 prediction  origin id
1   1.000   0.000   0.000    Origin1 Origin1  1
2   0.994   0.004   0.002    Origin1 Origin1  2
3   1.000   0.000   0.000    Origin1 Origin1  3
4   1.000   0.000   0.000    Origin1 Origin1  4
5   1.000   0.000   0.000    Origin1 Origin1  5
6   1.000   0.000   0.000    Origin1 Origin1  6
> nrow(df1_new) # 显示 df1_new 的行数。
[1] 413
> nrow(df2_new) # 显示 df2_new 的行数。
[1] 413
> data_set  nrow(data_set) # 显示 data_set 的行数。
[1] 413
> head(data_set) # 显示 data_set 的 6 行数据。
id  origin class Alcohol Malic.acid  Ash Alcanility.of.ash Magnesium
1  1 Origin1     1   14.37       1.95 2.50              16.8       113
2  2 Origin1     1   14.20       1.76 2.45              15.2       112
3  3 Origin1     1   14.06       2.15 2.61              17.6       121
4  4 Origin1     1   13.64       3.10 2.56              15.2       116
5  5 Origin1     1   14.06       1.63 2.28              16.0       126
6  6 Origin1     1   12.85       1.60 2.52              17.8        95
Total.phenols Flavanoids Nonflavanoid.phenols Proanthocyanins Color.intensity
1          3.85       3.49              0.24            2.18            7.80
2          3.27       3.39              0.34            1.97            6.75
3          2.60       2.51              0.31            1.25            5.05
4          2.70       3.03              0.17            1.66            5.10
5          3.00       3.17              0.24            2.10            5.65
6          2.48       2.37              0.26            1.46            3.93
Hue OD280.OD315.of.diluted.wines Proline Origin1 Origin2 Origin3 prediction
1 0.86                      3.45    1480   1.000   0.000   0.000    Origin1
2 1.05                      2.85    1450   0.994   0.004   0.002    Origin1
3 1.06                      3.58    1295   1.000   0.000   0.000    Origin1
4 0.96                      3.36     845   1.000   0.000   0.000    Origin1
5 1.09                      3.71     780   1.000   0.000   0.000    Origin1
6 1.09                      3.63    1015   1.000   0.000   0.000    Origin1
origin.1 id.1
1  Origin1    1
2  Origin1    2
3  Origin1    3
4  Origin1    4
5  Origin1    5
6  Origin1    6
> colnames(data_set)[20]  head(data_set)
id  origin class Alcohol Malic.acid  Ash Alcanility.of.ash Magnesium
1  1 Origin1     1   14.37       1.95 2.50              16.8       113
2  2 Origin1     1   14.20       1.76 2.45              15.2       112
3  3 Origin1     1   14.06       2.15 2.61              17.6       121
4  4 Origin1     1   13.64       3.10 2.56              15.2       116
5  5 Origin1     1   14.06       1.63 2.28              16.0       126
6  6 Origin1     1   12.85       1.60 2.52              17.8        95
Total.phenols Flavanoids Nonflavanoid.phenols Proanthocyanins Color.intensity
1          3.85       3.49              0.24            2.18            7.80
2          3.27       3.39              0.34            1.97            6.75
3          2.60       2.51              0.31            1.25            5.05
4          2.70       3.03              0.17            1.66            5.10
5          3.00       3.17              0.24            2.10            5.65
6          2.48       2.37              0.26            1.46            3.93
Hue OD280.OD315.of.diluted.wines Proline Origin1 Origin2 Origin3  Source
1 0.86                         3.45    1480   1.000   0.000   0.000 Origin1
2 1.05                         2.85    1450   0.994   0.004   0.002 Origin1
3 1.06                         3.58    1295   1.000   0.000   0.000 Origin1
4 0.96                         3.36     845   1.000   0.000   0.000 Origin1
5 1.09                         3.71     780   1.000   0.000   0.000 Origin1
6 1.09                         3.63    1015   1.000   0.000   0.000 Origin1
origin.1 id.1
1  Origin1    1
2  Origin1    2
3  Origin1    3
4  Origin1    4
5  Origin1    5
6  Origin1    6
> data_set$propensity_to_buy  class(data_set) # 显示最终 data_set 的类别。
[1] "data.frame"
> nrow(data_set) # 显示最终 data_set 的行数。
[1] 413
> head(data_set) # 显示最终 data_set 的 6 行数据。
id  origin class Alcohol Malic.acid  Ash Alcanility.of.ash Magnesium
1  1 Origin1     1   14.37       1.95 2.50              16.8       113
2  2 Origin1     1   14.20       1.76 2.45              15.2       112
3  3 Origin1     1   14.06       2.15 2.61              17.6       121
4  4 Origin1     1   13.64       3.10 2.56              15.2       116
5  5 Origin1     1   14.06       1.63 2.28              16.0       126
6  6 Origin1     1   12.85       1.60 2.52              17.8        95
Total.phenols Flavanoids Nonflavanoid.phenols Proanthocyanins Color.intensity
1          3.85       3.49              0.24            2.18            7.80
2          3.27       3.39              0.34            1.97            6.75
3          2.60       2.51              0.31            1.25            5.05
4          2.70       3.03              0.17            1.66            5.10
5          3.00       3.17              0.24            2.10            5.65
6          2.48       2.37              0.26            1.46            3.93
Hue OD280.OD315.of.diluted.wines Proline Origin1 Origin2 Origin3  Source
1 0.86                         3.45    1480   1.000   0.000   0.000 Origin1
2 1.05                         2.85    1450   0.994   0.004   0.002 Origin1
3 1.06                         3.58    1295   1.000   0.000   0.000 Origin1
4 0.96                         3.36     845   1.000   0.000   0.000 Origin1
5 1.09                         3.71     780   1.000   0.000   0.000 Origin1
6 1.09                         3.63    1015   1.000   0.000   0.000 Origin1
origin.1 id.1 propensity_to_buy
1  Origin1    1                 1
2  Origin1    2                 1
3  Origin1    3                 1
4  Origin1    4                 1
5  Origin1    5                 1
6  Origin1    6                 1
> write.csv(data_set, "Wineptobuy.csv", row.names=FALSE) # 将 data_set 中包含的数据写入工作目录下的一个新 .csv 文件中。
>
```


### 解决方案的第二部分

现在我们已经预测了葡萄酒产地，并创建了一个包含 `Source` 列的新数据集，接下来需要利用它来预测购买倾向。如本节开头所述，我们将使用逻辑回归机器学习算法，该算法通过应用广义线性模型来拟合数据。使用的预测变量是 `Source`，响应变量是 `propensity_to_buy`。清单 6-2 展示了相关代码。

```
library(ORE)
ore.connect("testr","orcl","localhost","testr")
library(OREmodels)
winedata  0.5,1,0)
misClasificError <- mean(test_set$p_to_buyPred != test_set$propensity_to_buy)
print(paste('Accuracy',1-misClasificError))
####
library(ROCR)
class(test_set$p_to_buyPred)
pr1 <- prediction(test_set$p_to_buyPred, test_set$propensity_to_buy)
class(pr1)
prf1 <- performance(pr1, measure = "tpr", x.measure = "fpr")
class(prf1)
pdf("plot_prf1.pdf")
plot(prf1, colorize = TRUE) # , text.adj = c(-0.2,1.7)
dev.off()
auc1 <- performance(pr1, measure = "auc")
auc1 <- auc1@y.values[[1]]
auc1
####
library(ROCR)
p <- predict(logitM, newdata= data.frame(test_set[,c(1:19)]), type="response")
class(p)
pr <- prediction(p, test_set$propensity_to_buy)
class(pr)
prf <- performance(pr, measure = "tpr", x.measure = "fpr")
class(prf)
plot(prf, colorize = TRUE) # , text.adj = c(-0.2,1.7)
auc <- performance(pr, measure = "auc")
auc <- auc@y.values[[1]]
auc
test_set2 <- data.frame(test_set[,c(1:19)])
test_set3 <- cbind(test_set2, predict(logitM, newdata=test_set2, type = "link", se = TRUE))
test_set3 <- within(test_set3, {
PredictedProb <- plogis(fit)
LL <- plogis(fit - (1.96 * se.fit))
UL <- plogis(fit + (1.96 * se.fit))
})
head(test_set3)
清单 6-2.
使用逻辑回归根据葡萄酒产地确定购买倾向
```

下一部分展示了 `ggplot2` 函数。关于 `ggplot` 的描述见第 5 章“图形输出分析：根据葡萄酒产地预测购买倾向”一节。

```
library(ggplot2)
pdf("test_set3_ribbon.pdf")
ggplot(test_set3, aes(x = Source, y = PredictedProb, group=PredictedProb)) +
geom_line(aes(colour = PredictedProb), size = 1) + geom_point() +
geom_ribbon(aes(ymin = LL, ymax = UL, fill = PredictedProb), alpha = 0.25)  +
scale_fill_gradient(low="red", high="green") +
ggtitle("Predicting Propensity to buy based on Wine Source") +
ylab("Predicted Probability - p_to_buyPred")
dev.off()
fillc_train <- train_set$Source # c("Origin1", "Origin2", "Origin3")
train_set <- cbind(train_set, fillc_train)
library(ggplot2)
pdf("WineOriginTrainingDataGLMPlot_test_bar.pdf")
ggplot(data=train_set, aes(x=Source, y=propensity_to_buy, fill = Source)) +
geom_bar(stat="identity", width=0.25) +
scale_fill_manual("legend", values = c("Origin1" = "green", "Origin2" = "orange", "Origin3" = "blue"))
dev.off()
fillc_test <- test_set$Source
test_set <- cbind(test_set, fillc_test)
library(ggplot2)
pdf("WineOriginTestDataGLMPlot_test_bar.pdf")
ggplot(data=test_set, aes(x=Source, y=p_to_buyPred, fill = Source)) +
geom_bar(stat="identity", width=0.25)  +
scale_fill_manual("legend", values = c("Origin1" = "green", "Origin2" = "orange", "Origin3" = "blue"))
dev.off()
library(ggplot2)
pdf("WineOriginTrainingDataGLMPlot_test_lineNpoint.pdf")
ggplot(data=train_set, aes(x=Source, y=propensity_to_buy, group=1)) +
geom_line(aes(colour = propensity_to_buy), size = 1) + geom_point() +
stat_smooth(method="glm", family="binomial", se=FALSE)
dev.off()
library(ggplot2)
pdf("WineOriginTestDataGLMPlot_test_lineNpoint.pdf")
ggplot(data=test_set, aes(x=Source, y=p_to_buyPred, group=1)) +
geom_line(aes(colour = p_to_buyPred), size = 1) + geom_point() +
stat_smooth(method="glm", family="binomial", se=FALSE)
dev.off()
```

以下是执行清单 6-2 中代码的输出结果：



```
> library(ORE) # 加载 ORE 库，这是 Oracle R Enterprise 执行的主要库
Loading required package: OREbase
Loading required package: OREcommon
Attaching package: 'OREbase'
The following objects are masked from 'package:base':
cbind, data.frame, eval, interaction, order, paste, pmax, pmin,
rbind, table
Loading required package: OREembed
Loading required package: OREstats
Loading required package: MASS
Loading required package: OREgraphics
Loading required package: OREeda
Loading required package: OREmodels
Loading required package: OREdm
Loading required package: lattice
Loading required package: OREpredict
Loading required package: ORExml
> ore.connect("testr","orcl","localhost","testr") # 使用 ORE 需要连接到 Oracle 数据库
> library(OREmodels) # 加载 OREmodels 库以调用 ORE 机器学习模型
