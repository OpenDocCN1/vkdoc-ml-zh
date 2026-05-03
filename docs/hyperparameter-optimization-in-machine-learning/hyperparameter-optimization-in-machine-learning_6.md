# 加载数据集并将其分割为训练集和测试集。

```
tpot = TPOTClassifier(generations=5, population_size=50, verbosity=2)
tpot.fit(X_train, y_train)
```

我们训练 TPOT 五个世代；默认是 100 个世代。这个框架的接口与 scikit-learn 非常相似。

瞧！我在训练好的模型上对测试集达到了 100% 的准确率。

为了让事情更简单，我们可以将选定的流水线导出到一个 Python 脚本中，然后可以轻松部署。

```
tpot.export('tpot_iris_pipeline.py')
```

上面的代码行自动生成了以下 `tpot_iris_pipeline.py` 文件：

```
import numpy as np
import pandas as pd
from sklearn.model_selection import train_test_split
from sklearn.neural_network import MLPClassifier
# NOTE: Make sure that the outcome column is labeled 'target' in the data # file
tpot_data = pd.read_csv('PATH/TO/DATA/FILE', sep="COLUMN_SEPARATOR",
dtype=np.float64)
features = tpot_data.drop('target', axis=1)
training_features, testing_features, training_target, testing_target = \
train_test_split(features, tpot_data['target'],
random_state=None)
# Average CV score on the training set was: 0.9913043478260869
exported_pipeline = MLPClassifier(alpha=0.1, learning_rate_init=0.1)
exported_pipeline.fit(training_features, training_target)
results = exported_pipeline.predict(testing_features)
```

我们优化的时间越长，得到的结果就越好。

还有许多其他的 AutoML 库，例如 auto-sklearn、H2O AutoML 和 AutoKeras。AutoML 不仅有利于数据科学家加速流程，也让没有编程知识的人能够使用机器学习。AutoML 在人工智能的未来无疑占有重要地位。

正如你在本章中所见，超参数优化在 AutoML 中扮演着巨大的角色，使我们能够在最短的时间内生成最佳的机器学习模型。

作为本书的结尾，我祝愿你在使用更高级的方法（如贝叶斯优化和剪枝算法，而不是手动调优或网格搜索）优化超参数时取得成功。优化愉快！

脚注 1   2   3  索引 A 激活函数 自动化机器学习（AutoML） 构建模块 定义 TPOT B 反向传播 贝叶斯优化 超参数 SMBO 树结构帕尔森估计器 C 连续变量 create_study() 函数 交叉验证函数 D Dask 分布式 网格搜索 超参数优化 随机搜索 Dask 并行集合 数据清洗与预处理 特征选择 F 检验 互信息 递归 缺失值 链式方程 删除行 均值/中位数 回归/分类 ML 算法 模型评估方法 非数值列 标签编码 独热编码 异常值检测 DBSCAN z-score 技术 决策树 基于密度的噪声应用空间聚类（DBSCAN） 离散变量 距离度量 动态任务调度 E 指数分布 F fmin() 函数 F 检验 全连接神经网络 隐藏层 PyTorch G 高斯分布 高斯过程（GP） 生成对抗网络（GAN） 遗传算法 图形处理器（GPU） grid_search() 函数 网格搜索 GridSearchCV 函数 H 高性能计算（HPC） Horovod Hyperas Hyperopt 定义 示例 hyperas 并行化试验 TPE 搜索空间 Sklearn TPE Hyperopt-sklearn 超参数优化 Dask 仪表盘 减半搜索 超带搜索 增量搜索 超参数 连续变量 决策树 离散变量 kd-tree KNN 神经网络 概率分布 参见概率分布函数 SVM 超参数调优库 I, J 增量函数 索引算法 迭代 *vs*. 准确率图 迭代 *vs*. 准确率分数 K K 近邻（KNN） L 标签编码 学习率 Log Softmax 函数 损失函数 M 机器学习（ML） 内存约束 MNIST 数据集 keras PyTorch 互信息（MI） N 神经网络 激活函数 批量大小 离散变量 损失函数 层数 节点数 优化器 O 独热编码 Optuna 算法 定义 分布式优化 示例 搜索空间 可视化 P, Q 参数 partition_dataset() 概率分布函数 指数 高斯 均匀 PyTorch 分布式 R 随机搜索 随机搜索 *vs*. 网格搜索 递归特征选择 强化学习算法 S Scikit-learn 库 基于序列模型的全局优化（SMBO） Slurm 命令 监督机器学习算法 支持向量机（SVM） T 三维曲线 时间/计算约束 基于树的流水线优化工具（TPOT） 树结构帕尔森估计器（TPE） U, V 均匀分布 无监督机器学习算法 W, X, Y, Z 权重 脚注 1   2   3   4
