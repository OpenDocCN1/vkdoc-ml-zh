# 从鸢尾花数据集创建 PySpark DataFrame
[In]: data = [(float(X[i, 0]), float(X[i, 1]), float(X[i, 2]), float(X[i, 3]), float(y[i])) for i in range(len(X))]
[In]: spark_df = spark.createDataFrame(data, ["sepal_length", "sepal_width", "petal_length", "petal_width", "species"])
