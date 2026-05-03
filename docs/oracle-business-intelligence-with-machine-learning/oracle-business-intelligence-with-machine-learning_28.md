# 这会返回上述预测以 "auc" 作为评估度量的性能。Auc 是 ROC 曲线下的面积。
auc1 <- performance(pr1, measure = "auc")
auc1 <- auc1@y.values[[1]]
auc1 # "auc" 越接近 1 或等于 1 表示模型拟合优度好，预测性能更佳
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
head(test_set2)
