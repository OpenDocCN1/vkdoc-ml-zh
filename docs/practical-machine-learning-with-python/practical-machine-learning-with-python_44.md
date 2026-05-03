# 输入形状 = 时间步长*特征数
model.add(LSTM(input_shape=input_shape,
units = hidden_units,
return_sequences=True
))



