# 评估准确率
[In]: evaluator = MulticlassClassificationEvaluator(labelCol="species", predictionCol="prediction", metricName="accuracy")
[In]: accuracy = evaluator.evaluate(predictions)
[In]: print("Accuracy:", accuracy)
