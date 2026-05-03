# 将特征组装成单个向量列
[In]: feature_cols = ["sepal_length", "sepal_width", "petal_length", "petal_width"]
[In]: assembler = VectorAssembler(inputCols=feature_cols, outputCol="features")
[In]: train_data = assembler.transform(train_data)
[In]: test_data = assembler.transform(test_data)
