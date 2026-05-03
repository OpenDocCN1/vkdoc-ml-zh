# 使用训练集拟合逻辑回归模型
logitM <- glm(formula = propensity_to_buy ~ Source, family = binomial, data = train_set)
summary(logitM)
