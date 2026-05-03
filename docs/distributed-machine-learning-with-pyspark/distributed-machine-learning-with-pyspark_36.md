# 创建 MLP 分类器
[In]: mlp = MultilayerPerceptronClassifier(labelCol="species", featuresCol="features", layers=[4, 100, 50, 3], seed=42, maxIter=500)
