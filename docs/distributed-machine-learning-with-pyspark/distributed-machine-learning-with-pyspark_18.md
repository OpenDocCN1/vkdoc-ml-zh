# a) 统计每个预测簇的出现次数
[In]: cluster_counts = {}
[In]: for label in labels:
if label in cluster_counts:
cluster_counts[label] += 1
else:
cluster_counts[label] = 1
