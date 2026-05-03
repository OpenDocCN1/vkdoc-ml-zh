# 显示模型预测和实际情感
print("测试文档索引: {index}\n 实际情感: {actual}
\n 预测情感: {predicted}"
.format(index=doc_index, actual=prediction_labels[doc_index],
predicted=lr_pipeline.predict([norm_corpus[doc_index]])))

