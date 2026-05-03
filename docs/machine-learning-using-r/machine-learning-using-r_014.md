# 将聚类分布与实际净值制成表格
table(clusterCut_2,Data_House_Worth$HouseNetWorth)
clusterCut_2 High Low Medium
1  104 135     51
2   26   0      0
clusterCut_3 <-cutree(clusters, 3)
