# 训练模型列表
methodList <-c('knn','rpart')
models <-caretList(choice ~MembershipPoints +CustomerAge +PurchaseTenure +CustomerPropensity +LastPurchaseDuration, data=dataset, trControl=train_control, methodList=methodList)
