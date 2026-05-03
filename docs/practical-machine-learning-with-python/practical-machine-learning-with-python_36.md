# 创建窗口
result = []
for index in range(len(timeseries) - sequence_length):
    result.append(timeseries[index: index + sequence_length])

