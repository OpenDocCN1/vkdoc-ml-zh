# 构建逻辑回归模型
lr = LogisticRegression()
lr.fit(cv_train_features, train_sentiments)

