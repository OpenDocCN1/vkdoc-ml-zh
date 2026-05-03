# 显示预测概率
print("\n 模型预测概率:")
for probs in zip(classes, lr_pipeline.predict_proba([norm_corpus[doc_index]])[0]):
print(probs)

