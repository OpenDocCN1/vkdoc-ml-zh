# 以第 0 个索引为基准进行归一化
if normalize:
    normalised_data = []
    for window in result:
        normalised_window = [((float(p) / float(window[0])) - 1) \
                             for p in window]
        normalised_data.append(normalised_window)
    result = normalised_data

