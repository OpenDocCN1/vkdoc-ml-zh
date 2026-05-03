# 将数据缩放到 0-1 范围
scaler = None
if scale:
    scaler = MinMaxScaler(feature_range=(0, 1))
    train = scaler.fit_transform(train)
    test = scaler.transform(test)

