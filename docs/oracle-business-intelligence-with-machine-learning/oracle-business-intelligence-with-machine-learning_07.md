# 将数据拆分为训练集和测试集
ind <- sample(2, nrow(winedata), replace = TRUE, prob = c(0.7, 0.3))
train_set <- winedata[ind == 1, ]
test_set <- winedata[ind == 2, ]
