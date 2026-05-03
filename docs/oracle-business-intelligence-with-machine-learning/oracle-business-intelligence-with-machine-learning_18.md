# 并返回一个类为 prediction 的对象。
pr1 <- prediction(test_set$p_to_buyPred, test_set$propensity_to_buy)
class(pr1) # 这会将类显示为 "prediction"
