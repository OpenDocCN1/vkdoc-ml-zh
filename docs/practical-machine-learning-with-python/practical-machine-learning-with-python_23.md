# 在 GloVe 训练特征上训练 DNN 模型
batch_size = 100
glove_dnn.fit(train_glove_features, y_train, epochs=5, batch_size=batch_size,
shuffle=True, validation_split=0.1, verbose=1)

