# 准备训练方案
control <-trainControl(method="repeatedcv", number=10, repeats=3, search="random")
