# 对新数据进行预测
[In]: y_new_pred = mlp.predict(X_new)
[In]: print("Predictions:", y_new_pred)
```



### PySpark

```
[In]: from sklearn.datasets import load_iris
[In]: from pyspark.sql import SparkSession
[In]: from pyspark.ml.classification import MultilayerPerceptronClassificationModel, MultilayerPerceptronClassifier
[In]: from pyspark.ml.feature import VectorAssembler
[In]: from pyspark.ml.evaluation import MulticlassClassificationEvaluator
[In]: import numpy as np
