# 平滑序列
if roll_mean_window:
    timeseries = timeseries.rolling(roll_mean_window).mean().dropna()

