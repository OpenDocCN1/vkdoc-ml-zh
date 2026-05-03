# 定义训练控制参数
train_control <-trainControl(method="cv", number=4, savePredictions=TRUE, classProbs=TRUE)
