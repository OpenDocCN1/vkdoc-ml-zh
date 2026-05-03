# 将聚类分布与实际净值制成表格
table(clusterCut_3,Data_House_Worth$HouseNetWorth)
clusterCut_3 High Low Medium
1    0 122      1
2  104  13     50
3   26   0      0
clusterCut_4 <-cutree(clusters, 4)
