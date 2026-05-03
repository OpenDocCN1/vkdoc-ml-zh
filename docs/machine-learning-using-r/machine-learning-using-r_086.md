# 创建包含 1 万条记录的样本
set.seed(917);
Data <-data[sample(nrow(data),size=10000),]
