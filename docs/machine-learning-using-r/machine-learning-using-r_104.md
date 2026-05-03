# stack using glm
stackControl <-trainControl(method="repeatedcv", number=10, repeats=3, savePredictions=TRUE, classProbs=TRUE)
set.seed(917)
stack.glm <-caretStack(models, method="glm", metric="Accuracy", trControl=stackControl)
print(stack.glm)
A glm ensemble of 2 base models: lda, rpart, glm, knn, svmRadial
Ensemble results:
Generalized Linear Model
30000 samples
5 predictor
2 classes: 'A', 'B'
No pre-processing
Resampling: Cross-Validated (10 fold, repeated 3 times)
Summary of sample sizes: 27000, 26999, 27000, 27000, 27000, 27001, ...
Resampling results:
Accuracy   Kappa
0.6441887  0.1845648
```

使用`glm`进行堆叠得到了 64%的准确率。在下一节中，我们使用`randomForest`进行了相同的堆叠。

```
