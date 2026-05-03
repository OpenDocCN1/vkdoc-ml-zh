# 将数据拆分为训练集和测试集
[In]: train_data, test_data = spark_df.randomSplit([0.8, 0.2], seed=42)
