# 将预测概率转换为类别（1 或 0）
p_to_buyPred <- ifelse(logitPredict > 0.5, 1, 0) # 将概率量化为值 1 和 0
misClasificError <- mean(p_to_buyPred != test_set$propensity_to_buy)
print(paste('准确率', 1 - misClasificError))
