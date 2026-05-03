# 权重：68 (48 个变量)
初始值 38816.242111
迭代 10 值 37672.163254
迭代 20 值 37574.198380
迭代 30 值 37413.360061
迭代 40 值 37327.695046
迭代 50 值 37263.280870
迭代 60 值 37261.603993
最终值 37261.599306
已收敛
显示模型统计摘要
mnl_model
调用:
multinom(formula = ProductChoice ~ MembershipPoints + IncomeClass +
CustomerPropensity + LastPurchaseDuration, data = train)
系数:
(Intercept) MembershipPoints IncomeClass1 IncomeClass2 IncomeClass3
2   11.682714      -0.03332131  -11.4405637   -11.314417   -11.307691
3   -1.967090       0.02730530    0.9855891     1.644233     2.224430
4   -1.618001      -0.12008110    1.5710959     1.692566     2.062924
IncomeClass4 IncomeClass5 IncomeClass6 IncomeClass7 IncomeClass8
2   -11.547647   -11.465621   -11.447368   -11.388917   -11.367926
3     2.023594     2.119750     2.201136     2.169300     2.241395
4     1.911509     2.062195     2.296741     2.249285     2.509872
IncomeClass9 CustomerPropensityLow CustomerPropensityMedium
2   -12.047828            -0.4106025               -0.2580652
3     1.997350            -0.8727976               -0.5184574
4     2.027252            -0.6549446               -0.5105506
CustomerPropensityUnknown CustomerPropensityVeryHigh
2                -0.5689626                  0.1774420
3                -1.1769285                  0.4646328
4                -1.1494067                  0.5660523
LastPurchaseDuration
2           0.04809274
3           0.05624992
4           0.08436483
残差偏差：74523.2
AIC：74619.2
预测概率
predicted_test <-as.data.frame(predict(mnl_model, newdata = test,
type="probs"))
显示预测概率
head(predicted_test)
1         2         3          4
1 0.3423453 0.2468372 0.2252361 0.18558132
2 0.2599605 0.2755778 0.2546863 0.20977542
3 0.4096704 0.2429370 0.2482094 0.09918326
4 0.2220821 0.2485851 0.3188838 0.21044894
5 0.4163053 0.2689046 0.1763766 0.13841355
6 0.4284514 0.2626000 0.1948703 0.11407836
基于最高概率进行预测
test_result <-apply(predicted_test,1,which.max)
table(test_result)
test_result
1    2    3     4
8928 1265 3879  5930
合并预测值与实际值至同一位置
result <-as.data.frame(cbind(test$ProductChoice,test_result))
colnames(result) <-c("实际类别", "预测类别")
head(result)
实际类别 预测类别
1            1               1
2            1               2
3            1               1
4            1               3
5            1               1
6            1               1
```

现在我们有了实际值与预测值的矩阵，接下来将创建分类矩阵。现在我们将计算分类矩阵的一些关键特征：

*   *案例数量*：测试集中案例的总数或行数（`n`）

*   *类别数量*：进行预测的类别总数（`nc`）

*   *正确分类数量*：分类矩阵对角线元素之和（`diag`）

*   *每个类别的实例数量*：实际值中所有案例的总和（`rowsums`）

*   *每个预测类别的实例数量*：预测值中所有案例的总和（`colsums`）

*   *实际值分布*：行总和除以总数

*   *预测值分布*：`colsums`的总和除以总数

```
创建分类矩阵
cmat <-as.matrix(table(实际值 = result$`实际类别`, 预测值 =
result$`预测类别`))
按顺序计算上述度量指标
n <-sum(cmat) ;
cat("案例数量  ", n);
案例数量   20002
nclass <-nrow(cmat);
cat("类别数量  ", nclass);
类别数量   4
correct_class <-diag(cmat);
cat("正确分类数量  ", correct_class);
正确分类数量   3175 395 1320 2020
rowsums <-apply(cmat, 1, sum);
cat("每个类别的实例数量  ", rowsums);
每个类别的实例数量   4998 4995 5035 4974
colsums <-apply(cmat, 2, sum);
cat("每个预测类别的实例数量  ", colsums);
每个预测类别的实例数量   8928 1265 3879 5930
actual_dist  <-rowsums  /n;
cat("实际值分布  ", actual_dist);
实际值分布   0.249875 0.249725 0.2517248 0.2486751
predict_dist <-colsums /n;
cat("预测值分布  ", predict_dist);
预测值分布   0.4463554 0.06324368 0.1939306 0.2964704
```

这些数量是根据分类矩阵计算得出的。建议您验证这些数字并深入理解这些量。以下是我们的分类器的分类矩阵和分类率：

```
打印分类矩阵 - 基于测试数据
print(cmat)
预测值
实际值    1    2    3    4
1 3175  312  609  902
2 2407  395  825 1368
3 1791  284 1320 1640
4 1555  274 1125 2020
打印分类率
classification_rate <-sum(correct_class)/n;
print(classification_rate)
[1] 0.3454655
```

该分类器的分类率较低。35%的分类率意味着模型在超过 50%的情况下对案例进行了错误分类。建模人员需要深入探究分类器性能低下的原因。原因可能包括预测概率、底层变量的解释力、不平衡类别的抽样，或者仅仅是选取最高概率的方法本身。

这里的模型性能帮助我们判断模型是否真正达到了我们的标准。我们真的能在实际环境中使用它吗？是什么导致了性能低下？这一步对于任何机器学习实践都至关重要。


### 7.6.2 敏感性与特异性

敏感性和特异性分别用于衡量模型在正类和负类上的表现。这些指标能够帮助你了解模型在正类和负类群体中的表现情况。结合分类矩阵，数学符号有助于阐明这些指标：

*   *敏感性*：测试将实际为`True`的类判定为`True`的概率。也称为真正例率（TPR），在模式识别中称为精确率。敏感性可通过分类矩阵计算（见图 7-7）。

    *敏感性，真正例率 = 正确识别的正例数 / 总正例数 = TP/(TP+FN)*

*   *特异性*：测试将实际为`False`的类判定为`False`的概率。也称为真负例率（TNR），在模式识别中称为召回率。特异性可通过分类矩阵计算（见图 7-7）。

    *特异性，真负例率 = 正确拒绝的负例数 / 总负例数 = TN/(TN+FP)*

敏感性和特异性是测试本身的特性，底层群体不会影响结果。对于一个好的模型，我们力求最大化 TPR 和 TNR，而受试者工作特征曲线（ROC）有助于实现这一目标。受试者工作曲线是敏感性与（1-特异性）之间的关系图，该曲线上的最高点提供了能最大化分类率的截断点。我们将在下一节讨论 ROC 曲线，并将其与优化敏感性和特异性联系起来。

### 注意

敏感性和特异性是按类别计算的。对于多类别问题，我们通常会对各类别的指标取平均值，从而为整个模型得到一个单一数值。为便于说明，我们将通过将类别合并为二分类问题来展示分析过程。鼓励读者将此概念扩展到完整模型。

```
分析针对 ProductChoice == 1 进行展示
Actual_Class <-ifelse(result$`Actual Class` ==1,"One","Rest");
Predicted_Class <-ifelse(result$`Predicted Class` ==1, "One", "Rest");
ss_analysis <-as.data.frame(cbind(Actual_Class,Predicted_Class));
为 ProductChoice == 1 创建分类矩阵
cmat_ProductChoice1 <-as.matrix(table(Actual = ss_analysis$Actual_Class,
Predicted = ss_analysis$Predicted_Class));
print(cmat_ProductChoice1)
Predicted
Actual  One Rest
One  3175 1823
Rest 5753 9251
classification_rate_ProductChoice1 <-sum(diag(cmat_ProductChoice1))/n;
cat("ProductChoice 1 的分类率为 ", classification_rate_
ProductChoice1)
ProductChoice 1 的分类率为   0.6212379
计算 TPR 和 TNR
TPR <-cmat_ProductChoice1[1,1]/(cmat_ProductChoice1[1,1] +cmat_
ProductChoice1[1,2]);
cat(" 敏感性或真正例率为 ", TPR);
敏感性或真正例率为  0.6352541
TNR <-cmat_ProductChoice1[2,2]/(cmat_ProductChoice1[2,1] +cmat_
ProductChoice1[2,2])
cat(" 特异性或真负例率为 ", TNR);
特异性或真负例率为  0.6165689
```

结果显示，对于`ProductChoice == 1`，我们的模型能够正确分类总计 63%的案例，其中，它能够从“One”群体中识别出 61%的“One”，并从“Rest”群体中识别出 62%的“Rest”。模型在从群体中预测“Rest”方面表现更好。

### 7.6.3 ROC 曲线下面积

受试者工作特征曲线（ROC）是一种图形化表示，展示了二分类器在分类阈值或截断点变化时的性能。如前一节所述，对于一个好的模型，我们希望最大化两个相互依赖的指标——TPR 和 TNR——而 ROC 曲线将展示这种关系。该曲线通过在不同截断点或阈值设置下绘制真正例率（TPR）与假正例率（FPR）的关系图来生成。

然而，由于我们使用的是多类分类器，我们并未使用截断点进行分类。鼓励读者针对其他 ProductChoices/类别，将多类模型重建为二分类模型（一类与其余类），然后使用 ROCR 包的内置函数。

这里我们展示 ROC 曲线和曲线下面积（AUC）值，假设模型只有两个类别：ProductChoice “One” 和 “Rest”。如果我们仅使用类别“One”/1 的概率，这将为我们提供一系列截断点尺度。请注意，在以下代码中，我们正在重新创建模型，将多类问题转化为二分类问题。本质上，概率尺度的多项分布以总和为 1 的方式在各类别间分配概率，而对于 ROC，我们需要某个类别的全范围概率来调整分类的阈值/截断点值。

为便于说明，我们将使用仅包含两个选择类别（0 或 1）的购买预测数据，定义如下：

*   如果客户从包含四种产品的目录中选择产品 1，则定义为 1；这构成我们的正类
*   如果客户选择除产品 1 以外的任何其他产品，则定义为 0；这构成我们的负类

我们根据此定义创建二元逻辑模型。

```
