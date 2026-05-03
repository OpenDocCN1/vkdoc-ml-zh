# 为新数据创建 PySpark DataFrame
[In]: new_data = spark.createDataFrame(X_new.tolist(), ["sepal_length", "sepal_width", "petal_length", "petal_width"])
