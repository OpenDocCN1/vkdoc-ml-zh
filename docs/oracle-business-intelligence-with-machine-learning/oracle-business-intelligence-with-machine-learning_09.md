# 使用测试集进行预测
logitPredict <- predict(logitM, newdata = test_set, type = "response")
head(logitPredict)
