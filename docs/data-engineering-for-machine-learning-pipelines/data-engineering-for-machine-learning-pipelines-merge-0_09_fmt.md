# 8. 使用 `DaskML` 构建机器学习工程管道

## 引言

机器学习是人工智能和计算机科学的一个分支。机器学习模型是一个可以从数据中学习以做出决策的智能系统。各种类型的组织都通过在其流程中利用机器学习模型获得了显著价值。训练机器学习模型是使用算法完成的，这可能需要大量的计算。随着数据集在数据点的数量和复杂性方面不断增长，可扩展的机器学习解决方案备受追捧。在本章中，我们将了解 `Dask-ML`，这是一个在分布式计算环境中运行 `ML` 算法并与现有现代数据科学库良好集成的库。

在本章结束时，您将学习到：
*   典型机器学习管道的工作流程
*   `Dask-ML`
*   `Dask` 与当前机器学习库的集成
*   设置与安装
*   使用 `Dask-ML` 进行数据预处理
*   `Dask-ML` 中的交叉验证、超参数调优和统计插补
*   使用 `Dask-ML` 的机器学习管道

## 机器学习数据管道工作流程

用于开发机器学习模型的数据工程任务涉及一系列相互连接的步骤，这些步骤构成了应用程序开发和部署的有组织的工作流程。以下阶段为您构建和实施机器学习解决方案提供了一个大纲。请注意，这些步骤本质上是迭代的；它们通常需要在适当的时候重新审视某个步骤。这些步骤绝不是短暂的或线性的。让我们更详细地探讨这些步骤。

![图 8-1：机器学习的数据工程任务](img/615530_1_En_8_Fig1_HTML.jpg)

**图 8-1** 机器学习的数据工程任务（模型重新训练、模型监控、模型部署、数据源获取、数据探索、数据清洗、数据整理、超参数调优、模型评估、模型训练、数据拆分、特征选择和数据集成）。

> **注意** 这代表了我从该领域的经验中得出的最佳实践。我发现根据这些阶段对项目任务进行分类是有效的。这种方法帮助我管理了复杂的项目，并且相信遵循这种方法对您的工作有益。

## 数据工程任务详解

### 数据源获取

数据源获取是从各种来源确定和收集数据的过程。在此之前，甚至需要确定各种来源以及新数据生成的频率。数据源还可能涉及各种数据类型，包括但不限于文本、视频数据、音频数据、图片等。传入的数据量、新数据生成的频率以及处理所需的各种数据类型为数据源获取和摄取工具及策略奠定了基础。

### 数据探索

**数据探索** 就是了解数据集。数据集有其自身的性质和特征，这取决于数据点的性质、类型和上下文。数据探索包括理解每个数据点的分布、理解一个或多个数据点之间的相关性、查看各种模式、识别是否存在缺失值，并对数据集的准确性、完整性和一致性（即使是近似的）进行初步评估。数据探索有助于识别关于数据点的关键见解，最小化潜在的偏差结果（如果一个或多个数据点代表同一事物；例如，花瓣长度以毫米、厘米等单位表示），最重要的是，减少导致无果而终的盲目数据分析工作。

### 数据清洗

数据清洗是关注数据集的卫生。简而言之，干净的数据有助于有效的数据分析。数据清洗包括清理数据点中可能对数据集产生负面影响并可能导致偏差的错误和异常值。数据清洗还涉及处理之前“发现”的缺失值，决定是插补缺失值还是完全删除该行。数据清洗还可能涉及处理数据重复，这很可能是删除它。干净的数据可以带来准确的分析和准确的回归模型，并使判断更可靠。

### 数据整理

**数据整理** 是一个持续转换数据以使其能够用于分析和模型构建目的的过程。最常见的数据整理技术通常被称为数据标准化，即将数据转换为可以在共同尺度上测量的值范围。其他数据整理工具包括特征缩放和变量编码，其中数据被归一化，分类变量被转换为特定格式的数值，以便数学模型可以轻松分析。虽然模型构建过程中的每一步都同等重要，但数据整理直接关系到提高机器学习模型的准确性。

### 数据集成

**数据集成** 是将来自一个或多个数据源的一个或多个数据集组合成一个能够提供全面视图的单一数据集的想法。数据集成还将涉及进一步识别和删除因组合多个数据源而可能出现的重复项。数据集成涉及模式对齐，即来自一个或多个源的数据是兼容且对齐的。数据集成还涉及数据质量，即开发验证和检查以确保从多个源集成数据后数据质量良好。数据集成有助于扩展整个项目的范围。数据集成还有助于提高数据集的准确性和完整性。

### 特征工程

一旦我们有了数据集，我们就可以进入下一步，即特征工程。此时，数据集可能包含多个数据点。除了数据集中的现有数据点之外，**特征工程** 涉及基于现有数据点创建新的数据点。一些最简单的数据点包括计算一个人的年龄（出生时或特定日期）、将华氏度转换为摄氏度，或基于特定距离度量计算两点之间的距离，仅举几例。有时特征工程可能涉及复杂的计算，如傅里叶变换或其他计算成本高昂的复杂方法。其思想是提出与模型要预测或分类的数据点具有更高相关性的新数据点。

### 特征选择

既然您已经拥有了项目所需的数据集，并基于现有数据集的数据点设计了新特征，那么是时候选择哪些数据点进入模型了。对于给定的机器学习模型，有多种选择特征的方法。像卡方检验这样的测试有助于确定分类变量之间的关系，而像 `Fisher` 分数这样的指标有助于为各种数据点生成排名。有一些方法，如前向选择或后向选择，它们迭代地生成各种特征组合和相应的准确率分数；然而，随着数据和特征超出实验室实验的范围，它们可能会变得昂贵。其他技术包括使用主成分分析和自编码器等工具进行降维，这些工具将数据编码为较低维度进行处理，然后解码回原始形状以进行输出。

### 数据拆分

**数据拆分** 是将整个数据集分割成更小的块以用于训练机器学习模型的过程。根据训练策略的不同，数据拆分的方法可能会有所不同。最常见的数据拆分方法是保留训练集、验证集和测试集。训练集通常用于让模型从数据中学习，验证集主要用于超参数调优目的，而测试集用于评估具有选定超参数的模型的性能。还有 `k` 折交叉验证，其中数据集被分成 `k` 个块，并通过保留 `k – 1` 个块作为训练集，剩余的一个作为测试集进行迭代训练。甚至可以更进一步，利用像分层抽样这样的高级统计方法来获得具有相似特征和分布的分区。

## 机器学习模型构建与部署

### 模型选择

因此，我们对数据集有了很好的理解，执行了各种操作，从多个来源进行了集成，生成了特征，并查看了数据分布。下一步是开始考虑选择合适的机器学习模型。这也是决定基于进入模型的变量来讲述目标变量故事的重要性的好时机。在预测客户流失的情况下，基于少数变量（如人口统计、偏好等）来讲述故事可能是相关的，而在图像分类的情况下，人们更关注图片是猫还是狗，而不太关心图片是如何变成这样或那样的。在监督学习的情况下，通常从逻辑回归作为起点开始，然后转向集成方法，如自助法或堆叠法。

> **注意** **自助法** 基本上是获取数据集的几个样本，构建机器学习模型，并通过投票或计算其均值来组合结果。**堆叠法** 是在同一总体上训练多个机器学习模型，然后基于这几个模型训练一个元模型并获取结果。

### 模型训练

根据数据的特征以及我们试图使用机器学习模型解决的问题的性质和上下文，训练策略和算法会有所不同。常见的训练策略是监督学习和无监督学习。监督学习是使用带标签的数据集训练模型的过程，其中数据点具有特定的名称，并且通常选择与决策变量相关性较高的数据点进行训练。常见的例子包括回归模型和分类模型。

**无监督学习** 是使用未标记数据集进行训练的过程。一个很好的例子是聚类算法，它根据地理邻近性等特征对相似的数据点进行分组。还存在其他几种策略；例如，增量学习，即随着新数据的可用而持续训练模型。还有集成建模，它涉及组合多个机器学习模型以提高模型的预测性能。

### 模型评估

一旦我们训练了一个机器学习模型，下一步就是模型验证。有时仅测试可能就足够了，而在其他情况下，则需要测试和验证。其思想是通过对一组全新的数据做出决策来评估模型的准确性，并通过查看精确度指标（如 `R 平方`、`F1 分数`、精确率、召回率等）来统计评估其准确性。

### 超参数调优

超参数是控制训练过程的参数。这些超参数在模型训练阶段之前设置。超参数是在数学上指导算法的技术参数。在一个 `n` 维向量空间中，这些超参数决定了搜索最佳值的方向以及每次迭代在该方向上走多远。

想象一个场景，您被要求在一个大型足球场上找到一个足球。您可能必须向各个方向行走，并在每个方向上决定走多少步，然后环顾四周，看看是否能在您的区域内找到足球。这基本上就是训练算法内部的工作方式。**超参数调优** 是为机器学习模型寻找最优参数的过程。想象一下，如果我说：“向左转 `45` 度，走 `10` 步，向右转，走 `5` 步。你会找到足球。”想象一个能指导您找出这些步骤的过程。这就是超参数调优。

### 最终测试

在构建机器学习模型的过程中，我们执行了各种检查和验证，直到为我们的机器学习模型选择最佳超参数的过程。最终测试基本上是在一个保留的测试集上测试最终选定的模型。此过程的存在是为了在模型部署到生产环境时获得现实的期望。此步骤背后的主要目标是了解该模型在暴露于新数据集时可能的表现。一旦我们测试了最终模型，就要建立对模型优缺点的理解，并记录结果以供将来参考和维护审计跟踪。

### 模型部署

一旦我们有了训练好的模型，模型通过了测试集的验证，并且超参数得到了调优，我们现在就有了一个可以部署的机器学习模型。部署的概念会随着业务和手头问题陈述的性质和上下文而变化。概念上的定义是，部署意味着在日常场景中使用模型，利用模型来为更好的决策提供信息。

### 模型监控

一旦模型部署，就需要频繁地以一定的时间间隔监控模型，以查看模型是否表现适当。模型也需要经常进行维护。维护活动可能涉及各种策略，从简单的步骤（如增量训练或稍微调整系数）到添加/修改特征，甚至完全改造模型。这将是下一步的重点。

## 模型重新训练

模型重新训练是机器学习模型过程中的一个关键步骤。此步骤涉及获取自上次以来收集的新数据，并执行各种数据整理和特征工程过程。根据生成的数据集，将决定增量训练是否足够，或者是否需要完全改造模型。根据适当的训练策略，重新训练模型，然后进行测试和验证。然后将模型重新部署到生产环境。此过程随后重复进行，以保持模型的准确性和最新性。

## Dask-ML 与其他 ML 库的集成

Dask 的机器学习库通过使用 Dask 并与 `scikit-learn` 等其他流行的机器学习库协作，提供可扩展的机器学习解决方案。`Dask-ML` 将并行计算扩展到机器学习算法的能力使得计算更快，并使使用大量数据训练模型成为可能。在本章讨论 `Dask-ML` 的特性和能力时，让我们简要了解一下 `Dask-ML` 当前集成的各种流行机器学习库。

### scikit-learn

`scikit-learn` 是 Python 中一个流行的机器学习和数据处理库。`scikit-learn` 库被学术界和工业界广泛采用，用于构建机器学习应用程序。该库支持许多监督和无监督学习算法，并且还具有用于特征工程、模型选择和交叉验证策略的预构建方法。

`scikit-learn` 中的许多算法都是通过指定 `joblib` 参数来实现并行执行的。这个 `joblib` 参数为某些机器学习算法提供了基于线程和基于进程的并行性。`Dask-ML` 库可以将这些基于 `joblib` 的算法扩展到在给定集群中的多台机器上执行。

### XGBoost

`XGBoost` 是机器学习领域中另一个用于梯度提升树的流行库。`梯度提升树` 是一种机器学习技术，用于连续和分类决策变量（分别是回归和分类）。它属于集成学习范式，其中多个决策树模型最小化误差并组合起来形成最终的预测模型。`Dask` 与 `XGBoost` 模型集成良好，`Dask` 将 `XGBoost` 的主进程设置到 `Dask` 调度器上，并将 `XGBoost` 的工作进程设置到工作节点上。然后它将 `Dask` 数据帧移动到 `XGBoost` 并让其训练。当 `XGBoost` 完成任务时，`Dask` 将清理 `XGBoost` 的基础设施并恢复到其原始状态。

### PyTorch

`PyTorch` 是一个开源机器学习库，主要用于自然语言处理、计算机视觉和神经网络等模型。`PyTorch` 支持张量计算，其中张量是 GPU 高效的多维数组，专为高性能计算任务而设计。`PyTorch` 通过一个名为 `Skorch` 的库与 `scikit-learn` 很好地集成。这个 `Skorch` 库为 `PyTorch` 方法提供了一个 `scikit-learn` 包装器。这意味着经过 `Skorch` 包装的 `PyTorch` 模型可以与 `Dask-ML` API 一起使用。

### 其他库

`Dask-ML` 还支持 `Keras` 和 `TensorFlow` 库。`Keras` 是一个用于开发深度学习模型的开源 Python 库。`TensorFlow` 是一个端到端的机器学习库，由 `Google` 开发。`scikit-learn` 包有一个针对 `Keras` 的 API 包装器，这使得 `Dask-ML` 可以与 `Keras` 模型一起使用。

## Dask-ML 设置与安装

您可以从 `PyPI` 获取最新的 wheel：

```bash
pip install dask-ml
```

您也可以使用此命令安装 `XGBoost` 和 `Dask-XGBoost`：

```bash
pip install dask-ml[xgboost]
```

您还可以安装所有依赖项：

```bash
pip install "dask-ml[complete]"
```

与我们之前讨论的类似，`Dask` 将数据集分区成多个较小的块，并将这些块分配给给定计算资源中的各个工作节点。机器学习模型在这些工作节点上并行训练，并在训练后聚合结果。`Dask-ML` 提供多个任务的并行处理，包括数据预处理任务（如编码）和估计器（如主成分分析）。此外，`Dask-ML` 还提供了有效的交叉验证方法和超参数调优方法，这些方法将使用给定计算环境中的所有工作节点来执行计算以获得更好的参数。让我们讨论这些特性。

## Dask-ML 数据预处理

`Dask-ML` 提供了各种数据预处理技术，可以在给定集群中的多个核心或多台机器上执行大规模数据处理。这些方法可以进一步整合到 `scikit-learn` 的管道方法中。

### RobustScaler()

鲁棒缩放器方法使数据能够抵抗异常值。它采用两步方法：中心化和缩放，即减去中位数并将数据点除以四分位距。

让我们通过一个示例来看：

```python
import dask.dataframe as dd
from dask_ml.preprocessing import RobustScaler
from sklearn.datasets import load_iris

iris = load_iris()
X = iris.data
df = dd.from_array(X)
RScaler = RobustScaler() \
    .fit_transform(df) \
    .compute()
print(RScaler.head())
```

将产生

```
Dask DataFrame Structure:
                  0        1        2        3
npartitions=1
0              float64  float64  float64  float64
149                ...      ...      ...      ...
Dask Name: from_array, 1 graph layer
```

### MinMaxScaler()

`MinMaxScaler` 是 `Dask-ML` 预处理方法提供的另一个有用的缩放函数。对于给定列中的每个值，`MinMaxScaler` 减去该特征的最小值并除以范围。`MinMaxScaler` 背后的思想是将所有值都置于 `[0,1]` 范围内。

需要注意的是，如果该列有负值，那么它将尝试将数据拟合到 `[–1,1]` 范围内。当您尝试使用 `Tanh` 或 `Sigmoid` 激活函数拟合神经网络时，`MinMaxScaler` 是一个很好的选择。请注意，`MinMaxScaler` 可能不适用于异常值。要么移除异常值，要么改用 `RobustScaler` 函数。

让我们看一个例子：

```python
import dask.dataframe as dd
from dask_ml.preprocessing import MinMaxScaler
from sklearn.datasets import load_iris

# 从 sklearn 数据集库中加载数据集
iris = load_iris()
X = iris.data

# 初始化 Dask 数据框
df = dd.from_array(X)
MMScaler = MinMaxScaler() \
    .fit_transform(df) \
    .compute()
print(MMScaler.head())
```

将返回

```
          0         1         2         3
0  0.222222  0.625000  0.067797  0.041667
1  0.166667  0.416667  0.067797  0.041667
2  0.111111  0.500000  0.050847  0.041667
3  0.083333  0.458333  0.084746  0.041667
4  0.194444  0.666667  0.067797  0.041667
```

## 独热编码

独热编码是一种技术，它将包含 `n` 个类别的分类变量转换为 `n` 个列，其中每个类别在值存在时开启，在值不存在时关闭。与直接分配数值标签相比，这可以被视为一个更好的选择；数值标签可能会产生偏差，因为算法可能会根据其呈现的值进行解释，而不是将其视为分类变量。让我们看一个例子：

## 交叉验证
正如我们在前一节中所看到的，交叉验证是一种机器学习技术，有助于评估最近训练的机器学习模型；通过交叉验证方法，您可以了解模型对非训练数据的适应程度。该方法通常将数据集划分为“k”个不同的数据块。模型被训练“k”次，每次训练时，k 个数据块中的一个作为测试集，其余所有数据块用于训练/验证。这个过程重复 k 次，每次迭代中都有一个独特的数据块作为测试集。

例如，假设我们将一个数据集划分为五个部分，分别称为`d1`、`d2`、`d3`、`d4`和`d5`：
*   在第一次迭代中，`d1`作为测试集，`d2`、`d3`、`d4`和`d5`作为训练集。
*   在第二次迭代中，`d2`作为测试集，`d1`、`d3`、`d4`和`d5`作为训练集。
*   在第三次迭代中，`d3`作为测试集，`d1`、`d2`、`d4`和`d5`作为训练集。

依此类推……在每次迭代中，收集性能指标，在所有“k”次迭代结束时，汇总性能指标，并用于评估模型的性能。我们在这里看到的是 k 折交叉验证。

让我们看一个使用 `Dask-ML` 执行五折交叉验证的例子：
```python
import dask.array as da
from dask_ml.model_selection import KFold
from dask_ml.datasets import make_regression
from dask_ml.linear_model import LinearRegression
from statistics import mean

X, y = make_regression(
    n_samples=200, # 选择观测数量
    n_features=5, # 特征数量
    random_state=0, # 随机种子
    chunks=20
) # 要划分的数据块数量
model = LinearRegression()

# Dask 的 KFold 方法将数据分割成 k 个连续的数据子集
# 这里我们指定 k 为 5，因此是 5 折交叉验证
kf = KFold(n_splits=5)
train_scores: list[int] = []
test_scores: list[int] = []

for i, j in kf.split(X):
    X_train, X_test = X[i], X[j]
    y_train, y_test = y[i], y[j]
    model.fit(
        X_train,
        y_train
    )
    train_score = model.score(
        X_train,
        y_train
    )
    test_score = model.score(
        X_test,
        y_test
    )
    train_scores.append(train_score)
    test_scores.append(test_score)

print("平均训练得分:", mean(train_scores))
print("平均测试得分:", mean(train_scores))
```
您可能希望在前面的代码中加入自己的示例，并尝试使用十折交叉验证，然后检查输出结果，以此作为练习。

`Dask-ML` 还提供了一种方法来分割一个或多个 `Dask` 数组。让我们看看下面的代码：
```python
import dask.array as da
from dask_ml.datasets import make_regression
from dask_ml.model_selection import train_test_split

X, y = make_regression(
    n_samples=200,
    n_features=5,
    random_state=0,
    chunks=20
)

# 注意 Dask ML 库中方法的使用方式与 Sklearn 类似
X_train, X_test, y_train, y_test = train_test_split(X, y)
print(X)
```
会返回
```
dask.array
```
分割后
```
print(X_train)
```
会返回
```
dask.array
print(X_train.compute()[:5])
```
结果可能如下所示
```
array(
[[ 0.77523133, -0.92051055, -1.62842789, -0.11035289,  0.68137204],
[ 0.02295746,  1.05580299, -0.24068297,  0.33288785,  2.14109094],
[-0.40522438, -0.59160913,  0.0704159 , -1.35938371, -0.70304657],
[-1.0048465 ,  0.02732673,  0.02413585,  0.48857595, -1.22351207],
[-1.64604251,  1.44954215, -0.9111602 , -2.27627772, -0.02227428]]
)
```

## 使用 Dask-ML 进行超参数调优
### 超参数
是控制模型训练过程的参数。在开始训练模型之前，我们需要为算法提供这些参数，以便其迭代地搜索最优解。如果我们为超参数输入的值稍有偏差（可以这么说），那么模型可能无法收敛到最优解。因此，为超参数寻找合适的值是机器学习建模中的一个关键部分。

当你期望得到一个可用于生产环境的优秀模型时，计算给定问题的最佳超参数可能需要花费数小时。在扩展超参数搜索工作时，我们确实会遇到挑战。我们可能遇到的最常见问题是计算约束和内存约束。

内存约束是指模型训练所需的数据量过大，无法装入可用的物理内存。即使数据能够装入内存（通过增加更多物理内存），我们可能还会遇到另一个问题，即计算约束，即计算任务需要很长时间才能返回结果（即使数据能装入内存）。通常，当算法计算成本更高时，就会发生这种情况。

内存约束和计算约束是互斥的。当作为训练集提供的数据不构成上述任一问题（内存或计算约束）时，我们可以使用网格搜索和随机搜索方法。

### 网格搜索
你需要定义一个值的网格（一个值矩阵）。网格搜索会穷举搜索所有超参数组合。它会使用交叉验证方法评估网格内每种可能的超参数组合的性能。虽然它会确定性地搜索并为你的模型提供可能的最佳超参数，但随着模型规模的增长，其计算成本会变得很高，并且很大程度上取决于你所定义的网格。

### 随机搜索
你需要为每个超参数定义其分布。随机搜索从预定义的分布中随机采样超参数值。同样，此方法会使用交叉验证来评估模型性能。虽然这听起来是一种更高效的方法，但统计分布可能无法帮助找到这些超参数的全局最优值。

当你面临内存约束，但计算机足以执行模型训练时，你可以使用增量搜索方法。

### 增量搜索
增量搜索从预定义的超参数值开始，并根据每次迭代的性能进行调整。当增量调整可行时，这是一种高效的方法；它甚至可能收敛到超参数的最优值。但这种搜索策略需要一定的工作量，并且要求超参数是连续的。

当计算和内存都受到约束，并且为你的机器学习模型获取良好的超参数面临挑战时，你可以选择探索用于超参数调优的 Hyperband 算法。

我的理解是，`Hyperband` 算法本质上是一种网格搜索，但如果某个组合收敛所需时间过长，该算法会动态地切断分配给它的计算和内存资源，并将资源集中在那些能更快产生超参数结果的组合上。它在每次迭代中都有更激进的早停标准。一旦所有预定义的迭代完成，就会返回那些以最少计算资源收敛的超参数。

让我们看一个例子，我们创建一个随机梯度下降分类器，并分别将超参数初始化为对数均匀分布和均匀分布。`Hyperband` 算法会计算超参数，你可以在最后看到结果。代码如下所示：

# 使用 Dask-ML 进行统计插补

```python
from dask.distributed import Client
from dask_ml.datasets import make_classification
from dask_ml.model_selection import train_test_split
from sklearn.linear_model import SGDClassifier
from scipy.stats import uniform, loguniform
from dask_ml.model_selection import HyperbandSearchCV
client = Client()
X, y = make_classification(
chunks=10,
random_state=0
)
X_train, X_test, y_train, y_test = train_test_split(X, y)
clf = SGDClassifier(
tol=1e-3,
penalty='elasticnet', # 正则化项
random_state=0
)
params = {
'alpha': loguniform(1e-2, 1e0),
'l1_ratio': uniform(0, 1)
}
search = HyperbandSearchCV(
clf,
params,
max_iter=80,
random_state=0
)
search.fit(
X_train,
y_train,
classes=[0, 1]
)
print(search.best_params_)
print(search.best_score_)
print(search.score(X_test, y_test))
```

会输出

```
HyperbandSearchCV(
estimator=SGDClassifier(penalty='elasticnet', random_state=0),
parameters={
'alpha': ,
'l1_ratio': 
},
random_state=0
)
print(search.best_params_)
{
'alpha': 0.057240478677855075,
'l1_ratio': 0.40506608504946273
}
print(search.best_score_)
0.6
print(search.score(X_test, y_test))
0.8
```

插补是用适当的值替换缺失数据的过程，以便你可以保留大部分数据集。Dask-ML 提供了一个名为 `SimpleImputer` 的方法，我们将用它来插补缺失值。此外，我们还将使用逻辑回归来对另一个值进行插补。让我们更详细地了解这些内容。

首先，我们将为我们的应用导入必要的类和函数：

```python
import dask.dataframe as dd
import pandas as pd
from dask_ml.preprocessing import OneHotEncoder
from dask_ml.impute import SimpleImputer
from dask_ml.model_selection import train_test_split
from dask_ml.linear_model import LogisticRegression
from dask_ml.metrics import accuracy_score
```

现在，让我们创建一个样本数据集，以便使用 Dask-ML 的方法。我们将其保持为一个简单的 n 维数组，可以转换为数据框用于下游处理：

```python
data = {
'color':
['red', 'orange', 'green', None, 'yellow', 'green'],
'weight':
[150, 180, 200, 160, None, 220],
'taste':
['sweet', 'sweet', 'sour', 'sweet', 'sweet', 'sour'],
'fruit':
['apple', 'orange', 'apple', 'apple', 'orange', 'melon']
}
```

现在，让我们为上述数据创建包含两个分区的 Dask 数据框：

```python
df = dd.from_pandas(
pd.DataFrame(data),
npartitions=2
)
```

让我们初始化 `SimpleImputer`。你可以指定用于填充缺失值的策略。对于分类变量，最常见的方法是填充出现频率最高的类别。对于连续变量，你可以选择统计平均值来填充缺失值：

```python
imputer1 = SimpleImputer(strategy='most_frequent')
df_fit = imputer1.fit(df)
df[['color']] = imputer1.transform(df[['color']])
```

让我们对水果的重量进行插补：

```python
imputer2 = SimpleImputer(strategy='mean')
ddf[["weight"]] = imputer2.fit_transform(df[['weight']])
print(ddf.compute())
```

会输出

```
color  weight  taste   fruit
0     red   150.0  sweet   apple
1  orange   180.0  sweet  orange
2   green   200.0   sour   apple
3   green   160.0  sweet   apple
4  yellow   182.0  sweet  orange
5   green   220.0   sour   melon
```

注意，第一个插补器将出现频率最高的类别分配给了分类变量。第二个插补器将列的统计平均值分配给了缺失值。巧合的是，缺失的橙色水果的统计平均值接近为橙色找到的值。

让我们看看 Dask-ML 中的另一个机器学习模型，我们将线性回归模型拟合到一组数据上。线性过程主要关注预测连续变量的值。例如，根据给定时间的库存水平预测特定商品的需求，就很适合使用线性回归。让我们详细看看代码。

首先，让我们导入所有必要的类和函数：

```python
import dask.dataframe as dd
import numpy as np
import pandas as pd
from dask_ml.linear_model import LinearRegression
from dask_ml.datasets import make_regression
from dask_ml.model_selection import train_test_split
```

让我们为机器学习模型生成一些样本数据：

```python
number_of_samples = 1000
number_of_features = 4
X, y = make_regression(
n_samples= number_of_samples,
n_features= number_of_features,
noise=0.1,
chunks=4
)
y = np.exp(y / 200).astype(int)

# 将数据拆分为训练集和测试集
X_train, X_test, y_train, y_test = train_test_split(
X,
y,
test_size=0.2
)

# 初始化并拟合线性回归模型
linear_model = LinearRegression()
linear_model.fit(
X_train,
y_train
)

# 在测试集上评估模型得分
score = linear_model.score(
X_test,
y_test
)
print("模型得分:", score)
将输出
0.9999
print(linear_model.get_params())
{'C': 1.0, 'class_weight': None, 'dual': False, 'fit_intercept': True,
'intercept_scaling': 1.0, 'max_iter': 100,
'multi_class': 'ovr', 'n_jobs': 1, 'penalty': 'l2', 'random_state': None, 'solver': 'admm',
'solver_kwargs': None, 'tol': 0.0001, 'verbose': 0, 'warm_start': False}
```

## 结论

利用分布式计算在基于 Python 的生态系统中运行机器学习算法的理念，可被视为应用研究领域取得的一个重要里程碑。`Dask-ML` 与 `scikit-learn` 及其他主流库实现了良好集成。到目前为止，我们探讨了使用 `Dask-ML` 进行的各种预处理和调优应用。通过掌握 `Dask-ML` 并能在生产环境中加以运用，组织能够获取高价值洞察并实现显著的成本节约，从而在数据驱动决策方面将边界推向新的高度。

# 使用 Apache Kafka 构建实时数据管道

## 引言

到目前为止，我们探讨了批处理管道。在本章中，我们将研究实时流处理管道以及一个功能极其丰富的库。我们将讨论 Apache Kafka。Apache Kafka 是一个分布式、容错的流式传输和消息传递平台。Kafka 有助于构建事件流管道，这些管道能够实时捕获新数据的创建和现有数据的修改，并将其适当地路由到各种下游消费用途。在本章中，我们将了解 Kafka、其架构，以及 Kafka 如何帮助构建实时数据管道。

## 到本章结束时，你将学到

*   分布式计算的基础知识及其与实时数据管道的联系
*   Apache Kafka 及其核心组件
*   Kafka 的安装与配置
*   Kafka 架构以及编写生产者和消费者
*   `ksqlDB`

## 分布式计算简介

现在是简要介绍分布式计算演进的恰当时机。传统上，计算有助于开发软件应用程序，以及利用数据进行繁重的计算编程。在这里，数据存储在关系表中，而应用程序和编程则存储在一台生产计算机上，该计算机与终端用户计算机相比可能更大。随着计算应用的普及，特定时间点生成的数据量也随之增加。此外，数据的类型和生成速度也在不断上升。鉴于对更强计算能力的需求，分布式系统和分布式计算的概念应运而生。

Apache Hadoop 作为一个成功的分布式系统项目，其采用率不断上升；其商业衍生合作伙伴对分布式系统处理的需求巨大。其他一些工具和框架补充了 Hadoop，以应对分布式数据处理中的挑战和机遇。“大数据”一词由此演变而来。计算领域采用的每一种趋势和最佳实践都受到了挑战，新的框架和理念随之涌现。例如，关系型 SQL 见证了 NoSQL 的采用，这反过来又迫使关系型 SQL 供应商开发和优化更多功能。

大数据演进中的一个方向被称为事件处理或流式数据。事件处理背后的理念是，在事件发生时捕获数据，并将其作为事件写入数据存储。关系数据模型和数据捕获方法侧重于将数据作为实体进行捕获。实体可以被视为名词；它可以是一个人、一个组织、一个项目、一个实验、一辆车，甚至是一个灯泡（根据实体的定义）。另一方面，流式数据处理基本上是在给定时间间隔内发生的动作或事件。它们可以表示任何事物。

事件数据处理或流式数据处理是以大规模方式生成的数据。与来自例如销售点系统或某天生产的产品数量等应用的关系数据相比，这些数据以更高的速度到达系统。在流式数据处理领域演进出的一个著名项目是 Kafka。Apache Kafka 起源于 LinkedIn，它帮助解决了 LinkedIn 的数据移动问题。在本章中，我们将讨论 Apache Kafka 以及流式数据处理背后的理念，并进一步深入 Kafka 的开发。

## Kafka 简介

Apache Kafka 是一个分布式、可扩展、容错、弹性且安全的流式传输和消息传递平台。它使用 Java 和 Scala 编写，用于构建实时数据管道和流式管道。事件流管道有助于从数据库、设备传感器（如物联网设备等）以及其他系统等来源实时（或近实时）捕获数据。这些捕获的数据存储在数据库中，用于进一步分析、数据处理以及路由到其他下游软件应用程序。根据官方文档，Apache Kafka 提供三个核心功能：

*   实时处理发生的事件数据流
*   存储这些事件数据流
*   读写事件数据流，并持续从其他系统导入数据或向其他系统导出数据

如前所述，Kafka 是一个分布式、可扩展、弹性、容错且安全的平台。让我们学习一些软件架构术语：

`分布式`

指能够在一台或多台计算机上运行的能力；该软件会提供一个抽象层，该抽象层位于一台或多台商用计算机之上，使得抽象层模拟成一台单一的计算机，其资源（计算、内存、存储）可根据需要共享和利用。

`可扩展`

指系统处理不断增加的工作量的能力，以及系统适应其使用量增长的能力。传统计算机可能不被视为可扩展；它们受限于主板上的核心数、总存储量和内存。当系统进行“纵向扩展”时，意味着向系统添加更多资源；而“纵向缩减”则意味着系统资源被最小化。

`弹性`

指系统动态分配硬件资源以满足不断增长的计算需求的能力。这是根据计算资源需求自动进行纵向扩展或缩减的能力。可能存在某些高峰时段需要更多计算资源，也可能存在其他情况，系统只需最少资源即可运行。弹性也意味着优化和高效的资源利用。

`容错`

指系统在发生故障时持续运行的能力。容错通常通过复制的概念来实现。在分布式环境下的数据处理中，被分区成碎片的数据通常会被复制；这意味着每个数据子集在系统中会有多个副本。这些副本会分布在整个集群中。因此，当包含某个碎片副本的一个节点发生故障时，系统会自动在另一个节点上定位同一碎片的其他副本。

`安全`

指数据、系统和资源受到保护，防止未经授权的访问、损坏以及来自外部实体的攻击。一个安全的系统可能包含访问控制、双因素认证、传输中和静态时的加密（磁盘加密和在安全隧道中传输数据），以及定期的安全更新，以保护系统免受可能存在的潜在威胁和其他漏洞的影响。

> **注意**
>
> `静态加密`
>
> 静态加密指的是磁盘加密的概念。磁盘加密确保当未经授权的实体访问磁盘并尝试读取时，无法访问数据。数据被加密，需要“密钥”才能解密。
>
> `传输中加密`
>
> 传输中加密指的是对传输中的数据进行加密的概念。传输层加密是指在数据通过特定网络传输时保护数据的安全。有一些网络协议（行业批准的指令）实现了传输中加密，其中一些是 HTTPS、TLS 和 SSL 协议。因此，当数据在传输过程中被截获时，信息是无意义的（需要某种密钥来解密）。

Kafka 支持三种主要的消息系统架构，即流式应用程序、发布-订阅模型和消息队列应用程序。作为参考，最基本的消息系统包含一个发送者、一个接收者和一条或多条消息。尽管显而易见，但发送者向接收者发送一条或多条消息，接收者会接收发送者发送的消息。还有消息代理，它们充当各种消息系统架构之间的中间层，执行验证和路由。

让我们讨论一下消息队列系统架构。在这种架构中，消息被保存在队列中等待处理。处理过程通过以异步方式消费这些消息开始。每条消息仅由一个消费者消费。一旦消息被消费，该消息就会从队列中移除。

现在，让我们讨论一下发布-订阅消息系统。Pub–sub 分别代表发布者和订阅者。在这种架构中，消息被“发布”到所谓的主题中。然后，该主题可以被另一个称为“订阅者”的程序读取。这些订阅者可以从主题接收消息。你可以有多个订阅者从主题接收消息；因此，这些消息会被广播给多个订阅者。

## Kafka 架构

Kafka 可以部署在虚拟机、云环境、容器甚至裸机设备上。Kafka 在其分布式系统中包含服务器和客户端。Kafka 服务器主动提供代理和 Kafka Connect 服务。代理构成存储层，而 Kafka Connect 提供作为事件流的持续数据导入或导出。Kafka 客户端为你提供库和 API，用于编写能够处理事件流、并行运行、按需扩展且具有容错能力的分布式应用程序和微服务。让我们看看 Kafka 的一些组件。

### 事件

事件简单来说就是在某个时间点正在发生或已经发生的事情。当然，每天我们的世界中都会发生许多事件；在 Kafka 的上下文中，它特指与用例相关的事件。例如，在银行中，同一时间会发生许多事件——客户取款、客户存款等。事件的构成可能包含与该事件相关的键、值、时间戳和其他元数据，例如：

```
键: John
值: John 取款 100 美元现金
时间戳: 2023 年 7 月 14 日，上午 10:10:33
```

### 主题

主题是事件被组织并安全存储的地方。这个概念类似于文件和文件夹。文件夹是存储一个或多个文件的地方；类似地，一个或多个事件存储在主题中。在前面的例子中，John 取款 100 美元被视为一个事件。如果类似的事件被分组，那么它将被称作“现金取款”主题。事件可以根据需要多次读取，并且在消费后不会被删除。你可以定义这些事件的保留时长。这些保留期可以针对不同主题进行修改。

### 分区

分区是将大数据块分割成多个较小数据块的过程，这有助于读取和访问。主题是被分区的。任何给定的主题都会被分区，并分布在多个存储桶中。具有相同事件键（在前面的例子中，John 是一个事件键）的事件将被写入同一个分区。这些分区也可以被复制，这样在某个分区发生故障时，总会有另一个包含相同主题和事件的分区。复制是在主题分区级别执行的。例如，如果复制因子为 3，并且你有一个给定主题的四个分区，那么将会有 12 个不同的分区（即每个主题的每个分区有三个副本）。

### `Broker`（代理）

Kafka 中的代理是 Kafka 服务器提供的存储服务。上述主题分区分布在多个代理上。这样，当一个代理发生故障时，Kafka 会定位到包含相似主题分区的另一个代理并继续处理。代理之间相互通信以进行数据复制，并努力确保整个 Kafka 集群中数据的一致性。

### `Replication`（复制）

# Kafka 基础架构

Kafka 会自动复制主题分区，每个分区会根据 Kafka 管理员设置的复制因子存储在多个代理上。这是为了实现容错，这样即使集群发生故障，消息仍然保持可用。主分区被称为领导者副本，主题分区的副本则存储在称为追随者副本的地方。每个被复制的分区都有一个领导者和 n-1 个追随者副本。当您读写数据时，始终是与领导者副本进行交互。领导者和追随者协同工作来完成复制。整个过程是自动化的，并在后台进行。

## Producers（生产者）

生产者是 Kafka 客户端的一部分，负责将事件发布到 Kafka 主题。通常，Kafka 生产者会向主题发送一条消息。这些消息会根据某种机制（例如，键哈希）进行分区。为了成功将消息写入主题，生产者必须指定一个确认级别。Kafka 生产者的例子包括 Web 服务器、物联网设备、监控和日志记录应用程序等。Kafka 可以使用 `Java` 内置的序列化器或使用 `Apache Avro`、`Protobuf` 等对传入的数据进行序列化。

### 注意

键哈希是确定键到分区映射的过程。

您使用 Kafka 生产者连接到集群，生产者需要配置参数，包括集群中代理的地址等。

## Consumers（消费者）

一旦生产者开始向 Kafka 集群发送事件，我们需要将这些事件取回到系统的其他部分。消费者就是订阅（读写）这些事件的组件。他们从生产者那里读取这些事件，并针对该事件生成响应。请注意，消费者只读取主题，而不会以其他方式“消费”；这些主题在 Kafka 集群中仍然可用。来自生产者的数据需要被准确地反序列化才能被消费。消费者从最旧到最新读取主题。

## Schema Registry（模式注册表）

现在我们已经有了生产者和消费者，生产者将开始向主题发布消息，而消费者将开始订阅生产者发布的这些相同消息。只有当消费者识别并理解生产者发布的数据结构时，它们才会开始消费。模式注册表维护了一个数据库，其中包含生产者已发布到主题的所有模式。模式注册表是一个独立于 Kafka 代理的进程。通过使用模式注册表，您可以确保数据完整性以及其他诸多好处。

### 使用 Avro

Avro 是一种用于结构化数据的序列化器。

### 使用 Protobuf

Protobuf 代表 `Protocol Buffers`。它是一种序列化结构化数据的方式，并带有自己的模式定义语言。Protobuf 由 `Google` 开发；与 `JSON` 格式相比，它是基于文本且可读的。Protobuf 是基于二进制的、语言无关的，并且在速度方面效率很高。

## Kafka Connect

Kafka Connect 是 Kafka 提供的一种数据集成机制，用于连接和集成各种企业系统。Kafka Connect 通过 `API` 提供必要的方法和函数，用于连接各种关系型数据库、NoSQL 数据库、文件系统等。您可以使用 Kafka 连接来自这些系统的源数据，将其发布为主题，并从 Kafka 生产者消费主题，然后将其加载到这些不同的数据库和文件系统中。Kafka Connect 可以在独立模式下运行以进行开发和测试，也可以在分布式模式下运行以进行大规模生产部署。

## Kafka Streams 和 ksqlDB

Kafka Streams 是一种支持流式数据管道概念的服务。流式数据管道是在数据生成或事件发生时处理数据的管道。流式数据管道不一定需要提取数据；相反，它们只是从给定系统持续不断的数据流中接收数据。最容易理解的方式是天气预报系统。这些系统持续不断地从卫星接收大气数据。因此，这里没有“提取”发生。相反，它们被配置为接收数据。

Kafka Streams 具有可扩展性和容错性，可以部署在主流操作系统、裸机设备、容器等中。Kafka Streams 主要服务于 `Java` 编程语言，使其能够编写流式管道，并利用流处理功能丰富现有的 `Java` 应用程序。对于其他编程语言，我们有另一个名为 `ksqlDB` 的服务，它支持编写 Kafka 流功能。

`ksqlDB` 是一个与 Kafka 集成的数据库，使您能够构建流式数据管道，类似于构建传统数据应用程序。更具体地说，`ksqlDB` 是一个计算层，它使用类似 `SQL` 的语法与存储层（即 Kafka）进行交互。您可以使用 `Web`、命令行和 `REST API` 与 `ksql` 进行交互。要使用 `Python REST API` 使用 `ksqlDB`，您可以安装

```
pip install ksql
```

您可以在 `ksqlDB` 中创建一个新表或流，并将关系表或其他文件系统中的现有数据流式传输到主题中，例如：

```
CREATE TABLE campaign(
AdvertiserID FOREIGN KEY referencing table,
CampaignID INT PRIMARY KEY,
CampaignName STRING,
StartDate DATE,
EndDate DATE,
Budget FLOAT
)
WITH (
Kafka_topic = "Campaign",
Partitions = 3,
Value_format = "Protobuf"
);
```

如果您有现有的 Kafka 主题，您可以使用 `ksqlDB` 创建一个流或表，然后开始将数据流式传输到 `ksqlDB`：

```
CREATE STREAM ads
WITH (
kafka_topic="campaign",
value_format= "protobuf"
);
```

就像关系型 `SQL` 一样，您可以执行查找、连接、过滤、转换和聚合操作，以从数据中获取实时分析洞察。

## Kafka Admin Client（Kafka 管理客户端）

Kafka 管理客户端允许您管理 Kafka 集群上的资源。

以下是 Kafka 架构的基本图示：

![](img/615530_1_En_9_Fig1_HTML.jpg)

Kafka 架构。它包括诸如 Kafka 生产者、主题、Kafka 消费者等主题，并分为 Kafka 分区和消费者组。Kafka 分区引出副本，副本分为领导者副本和追随者副本。代理引出 Kafka 集群和副本。

**图 9-1** Kafka 架构

## 安装与开发环境搭建

Kafka 发行版主要有三种类型。第一种是 `Apache Kafka` 发行版，它是开源的，并采用 `Apache` 许可证。第二种是 Kafka 的商业发行版，由供应商 `Confluent` 公司提供。最后一种是基于云的发行版，由 `AWS` 等主要云计算供应商提供。在生产环境中，你很可能使用安全且管理完善的 Kafka 发行版，因此探索 `Confluent` 公司提供的商业发行版是合理的。此外，他们还提供了大量文档，这非常有帮助。

首先，访问 `Confluent` 的 Kafka 网站 [`www.confluent.io/get-started/`](http://www.confluent.io/get-started/)，并按照说明设置一个新账户。系统会提供选择云供应商来托管你的 Kafka 集群。从学习者的角度来看，选择哪个供应商来托管 Kafka 集群可能并不那么重要。但是，如果你的组织正在采用特定的云计算平台，那么选择相同的供应商来托管你的 Kafka 集群可能会有所帮助。在这里，我们将以学习为目的，注册一个个人用户。首先，在 `Confluent` 网站上注册。点击“Environments”（环境），然后继续创建集群。

界面如下所示：

![](img/615530_1_En_9_Fig2_HTML.jpg)

一张网页截图，标题为 Confluent。页面有一个导航栏，包含“Home”（首页）、“Environments”（环境）、“Data Portal”（数据门户）、“Stream Processing”（流处理）和“Cluster Links”（集群链接）选项。页面主要内容是对 Kafka 集群的描述。左下角的“create cluster”（创建集群）按钮被高亮显示。

**图 9-2**

Confluent Kafka 环境的主屏幕

选择合适的集群类型。出于学习目的，基本选项应该就足够了。界面如下所示：

![](img/615530_1_En_9_Fig3_HTML.jpg)

一张网页截图，标题为“create cluster”（创建集群）。它展示了三个部分，分别名为“basic”（基础版）、“standard”（标准版）和“enterprise”（企业版）。每个部分都列出了功能及其限制。

**图 9-3**

选择具有适当带宽的集群

你将可以选择指定集群的托管位置。请随意选择你喜欢的云服务提供商以及离你最近的区域。你会在底部看到预估费用。你的界面可能如下所示：

![](img/615530_1_En_9_Fig4_HTML.jpg)

一张网页截图，标题为“Create Cluster”（创建集群）。它包括四个步骤：集群类型、区域、付款以及审核和启动。选项有 `AWS`、`Google Cloud` 和 `Microsoft Azure`。其中 `Google Cloud` 选项被选中。区域设置为洛杉矶，并选择了单区域可用性。“continue”（继续）按钮被选中。

**图 9-4**

选择合适的区域

接下来会进入付款页面。如果你是第一次注册 Confluent，可能会获得免费额度；因此你可以跳过付款，直接启动集群。

## 集群界面

你的集群界面可能如下所示：

![图 9-5: Confluent Kafka 中的集群仪表盘](img/615530_1_En_9_Fig5_HTML.jpg)

一张窗口屏幕截图，标题为“cluster_0”。菜单包含以下选项：“Cluster Overview”（集群概览）、“Networking”（网络）、“API Keys”（API 密钥）、“Cluster Settings”（集群设置）、“Stream Lineage”（流谱系）、“Stream Designer”（流设计器）、“Topics”（主题）、`ksqlDB`和“Connectors”（连接器）。屏幕右侧展示了每秒生产字节数随时间变化的图表。

## 创建主题与 API 密钥

一旦你的 Kafka 集群准备就绪，就可以在该集群中创建一个主题（`topic`）。主题是一个仅追加的日志寄存器，用于记录消息。

此时，导航到 Kafka 仪表盘中的集群，找到“Connect to your systems”（连接到你的系统）部分，选择 Python 编程语言，然后创建一个集群 API 密钥和一个模式注册表 API 密钥。

界面如下所示：

![图 9-6: Kafka 集群的 API 密钥和密码](img/615530_1_En_9_Fig6_HTML.jpg)

一张窗口屏幕截图，标题为“Create an API key”（创建 API 密钥）。有一个文本框，标签分别为“Key”（密钥）、“Secret”（密码）和“Description”（描述）。左下角的“download and continue”（下载并继续）按钮被高亮显示。

为了开发和沙盒测试，在你的开发环境中创建一个 `config.py` 文件，并存储以下内容：

```python
config = {
'bootstrap.servers': '',
'security.protocol': 'SASL_SSL',
'sasl.mechanisms': 'PLAIN',
'sasl.username': '',
'sasl.password': ''}
sr_config = {
'url': '',
'basic.auth.user.info':':'
}
```

确保将端点、密钥和密码替换为在此过程中获得的相应数据。这对于你的本地 Kafka 代码与云中的 Kafka 集群进行交互至关重要。

## 设置本地开发环境

现在，让我们在你的本地工作站上设置 Kafka 开发环境。我们将从回顾如何创建虚拟环境开始：

```bash
pip3 install venv
python3 -m venv kafkadev
source kafkadev/bin/activate
```

如果你使用的是 MS Windows 操作系统，请使用以下命令激活你的 Python 虚拟环境：

```bash
kafkadev\Scripts\activate
```

要停用此环境，请转到命令行并输入：

```bash
deactivate
```

现在，让我们从命令行设置 Kafka 开发环境：

```bash
pip3 install confluent-kafka config
```

## 创建 Kafka 发布者-订阅者程序

现在，让我们创建一个简单的 Kafka 发布者-订阅者程序。首先，创建一个工作目录：

```bash
mkdir Kafka-dev
cd Kafka-dev
```

创建一个名为`config.py`的新 Python 脚本。将之前的配置复制到该脚本中，并注释掉我们程序不需要的模式注册表部分。

它看起来像这样：

```python
# python kafka 集群配置
config = {
'bootstrap.servers':
'abcdef-4r47.us-north2.gcp.confluent.cloud:9092',
'security.protocol':
'SASL_SSL',
'sasl.mechanisms':
'PLAIN',
'sasl.username':
'MG2334H787GH097FRT',
'sasl.password':
'vYGUYTGuyGVJHGVUYtt76&*^YUG+ArDmnoHxENzv**o#$%^ijkjkn+buNjCb0W5E'
}
"""
sr_config = {
'url': '',
'basic.auth.user.info':':'
}
"""
```

创建一个名为 `producer.py` 的新文件，并输入以下内容：

```python
from confluent_kafka import Producer
from config import config
p = Producer(config)
def callback(error, event):
if error:
print(f"生产者主题 {event.topic()} 的事件 {event.key} 发送失败")
else:
val = event.value().decode('utf-8')
print(f"值已发送到分区 {event.partition}")
greetings = ['alice','bob','jenny','rhonda','steve','miller']
for g in greetings:
p.produce("hello_topic",
f"hello {g}",
g,
on_delivery=callback
)
p.flush()
```

现在让我们检查一下代码。我们首先导入必要的库，即 Confluent 分发的 `kafka` 和 `config`。然后，我们使用配置实例化生产者类。生产者类最重要的方法是 `produce` 和 `flush`。

`produce` 方法包含：

*   要发送消息的主题（包含一个字符串）

*   消息本身（有效载荷）

*   消息键

在前面的示例中，我们还有一个 `on_delivery` 参数，它接受一个回调函数作为参数。此回调函数包含在消息成功投递到 Kafka 集群中的相应主题时打印的信息。

当你运行此代码时：

```bash
python3 producer.py
```

程序将连接到云中的 Kafka 集群，定位到相应的主题，并将消息写入该主题。从程序的角度来看，它将打印以下内容：

```
value is sent to the partition
value is sent to the partition
value is sent to the partition
value is sent to the partition
value is sent to the partition
value is sent to the partition
```

## 创建 Kafka 消费者

现在，让我们创建一个 Kafka 消费者，它将从 Kafka 集群中的主题读取消息。请注意，Kafka 消费者仅尝试读取消息，不会以任何方式更改其形式或执行除读取之外的操作。这些消息将继续保留在 Kafka 集群的主题中。

创建一个名为 `consumer.py` 的新 Python 脚本，并输入以下代码：

```python
from config import config
from confluent_kafka import Consumer
def consumer_config():
config['group.id'] = 'hello_group'
config['auto.offset.reset'] = 'earliest'
config['enable.auto.commit'] = False # type: ignore
consumer_config()
subscriber = Consumer(config)
subscriber.subscribe(["hello_topic"])
while True:
event = subscriber.poll(1.0)
if event is None:
continue
if event.error():
print(event.error())
val = event.value().decode('utf8')
partition = event.partition()
print(f'Received: {val} from partition {partition}')
subscriber.close()
```

我们再次从导入必要的类开始。除了 `config.py` 提供的配置外，我们还需要指定消费者组 ID、是否从最早或最新的消息开始消费等。以 `subscriber` 为名实例化一个消费者类，并将其订阅以消费来自给定主题的消息。最后，订阅者将在一个无限 while 循环中运行以输出消息，除非用户通过按键组合中断。作为消费者程序，其思路是从主题中读取消息，并在可能的情况下将其打印到控制台屏幕。

## 使用 Schema Registry 的 Kafka 应用程序

如您所见，生产者和消费者独立运行。这两个程序在运行前互不了解，并且在运行时也不会直接交互。这种程序设计方法构成了松耦合架构。虽然这是一种良好的程序设计方法，但生产者和消费者必须就数据结构达成一致，以确保数据完整性，这一点至关重要。在 Kafka 中，这可以通过 Schema Registry 实现。生产者会在将数据发送到 Kafka 集群之前将其序列化为字节。另一方面，消费者在读取 Kafka 集群主题中的消息时，需要将字节反序列化为数据。

让我们看一个例子。这里我们有学生数据，我们分别在生产者和订阅者脚本中使用 `JSONSerializer` 来序列化和反序列化数据。

让我们创建一个 `publisher.py`。内容如下：

```
from confluent_kafka import Producer
from confluent_kafka.serialization import (SerializationContext,
MessageField)
from confluent_kafka.schema_registry import SchemaRegistryClient
from confluent_kafka.schema_registry.json_schema import JSONSerializer
from config import (config,
sr_config)
class Student(object):
def __init__(self, student_id, student_name, course):
self.student_id = student_id
self.student_name = student_name
self.course = course
schema_str = """{
"$schema": "https://json-schema.org/draft/2020-12/schema",
"title": "Student",
"description": "Student course data",
"type": "object",
"properties": {
"student_id": {
"description": "id of the student",
"type": "number"
},
"student_name": {
"description": "name of the student",
"type": "string"
},
"course": {
"description": "course of the student",
"type": "string"
}
}
}
"""
def student_to_dict(stud, ctx):
return {
"student_id": stud.student_id,
"student_name": stud.student_name,
"course": stud.course
}
student_data = [
Student(55052,"Johndoe","Chem101" ),
Student(55053,"TestStudent","Physics"),
Student(55054,"JaneDoe","Biology")
]
def delivery_report(err, event):
if err is not None:
print(f'读取 {event.key().decode("utf8")} 时投递失败: {err}')
else:
print(f'学生数据 {event.key().decode("utf8")} 已生成到 {event.topic()}')
if __name__ == '__main__':
topic = 'student_data'
schema_registry_client = SchemaRegistryClient(sr_config)
json_serializer = JSONSerializer(schema_str,
schema_registry_client,
student_to_dict)
producer = Producer(config)
for i in student_data:
producer.produce(topic=topic,
key=str(i.student_id),
value=json_serializer(i,
SerializationContext(
topic,
MessageField.VALUE
)),
on_delivery=delivery_report)
producer.flush()
```

我们首先加载此发布者脚本所需的各种类和方法。我们还加载了一个数据类和一个 JSON 模式，用于演示学生数据示例。我们定义了一个将学生数据转换为字典的函数。程序的其余部分您应该很熟悉，因为我们实例化了生产者类、Schema Registry 和 JSON 序列化器，为生产者提供了序列化数据所需的各种参数，并遍历列表以将数据写入 Kafka 集群中的主题。

JSON 序列化器接受字符串格式的模式和模式实例。`student_to_dict` 函数将 Python 对象（本例中是一个列表）转换为字典。`ctx` 参数提供与序列化相关的元数据。

运行此程序时：

```
python3 publisher.py
```

我们将得到：

```
Student reading for 55054 produced to student_data
Student reading for 55052 produced to student_data
Student reading for 55053 produced to student_data
```

现在让我们关注此项目的消费者部分。创建一个名为 `consumer.py` 的文件，并将以下代码加载到文件中：

```python
from confluent_kafka import Consumer
from confluent_kafka.serialization import SerializationContext, MessageField
from confluent_kafka.schema_registry.json_schema import JSONDeserializer
from config import config

class Student(object):
    def __init__(self, student_id, student_name, course):
        self.student_id = student_id
        self.student_name = student_name
        self.course = course

schema_str = """{
    "$schema": "https://json-schema.org/draft/2020-12/schema",
    "title": "Student",
    "description": "Student course data",
    "type": "object",
    "properties": {
        "student_id": {
            "description": "id of the student",
            "type": "number"
        },
        "student_name": {
            "description": "name of the student",
            "type": "string"
        },
        "course": {
            "description": "course of the student",
            "type": "string"
        }
    }
}"""

def set_consumer_configs():
    config['group.id'] = 'student_group'
    config['auto.offset.reset'] = 'earliest'

def dict_to_student(dict, ctx):
    return Student(dict['student_id'], dict['student_name'], dict['course'])

if __name__ == '__main__':
    topic = 'student_data'
    print(type(schema_str))
    json_deserializer = JSONDeserializer(schema_str, from_dict=dict_to_student)
    set_consumer_configs()
    consumer = Consumer(config)
    consumer.subscribe([topic])
    while True:
        try:
            event = consumer.poll(1.0)
            if event is None:
                continue
            student = json_deserializer(event.value(),
                                        SerializationContext(topic, MessageField.VALUE))
            if student is not None:
                print(f'Student with id {student.student_id} is {student.student_name}.')
        except KeyboardInterrupt:
            break
    consumer.close()
```

同样，程序将所有必需的类和方法加载到文件中，然后是学生数据模式的数据类和定义模式字符串的 JSON 配置。然后，我们在现有配置的基础上添加所需的消费者配置，以及一个将字典转换为列表的函数。从这里开始，我们实例化消费者类，指定特定主题，实例化 JSON 反序列化器，并创建一个循环，该循环将打印学生数据，除非被键盘按键中断。

当你运行此程序时：

```bash
python3 consumer.py
```

它将输出：

```
Student with id 55054 is JaneDoe.
Student with id 55053 is TestStudent.
Student with id 55052 is Johndoe.
```

## Protobuf 序列化器

正如我们之前所见，Protobuf 代表 Protocol Buffers。Protobuf 由 Google 开发，在序列化数据的速度和大小方面被认为更高效。要使用 Protobuf 序列化器，您需要遵循以下提到的指南：

* 首先，在您的模式定义文件中定义您的 Protobuf 模式。您需要定义模式并以`.proto`文件格式保存文件。这些 proto 模式文件非常易于人类阅读，并且与 JSON 文档相比，包含的信息更加简化。

* 其次，将`.proto`文件编译成您的编程语言，在我们的例子中是 Python 脚本。如前所述，Protobuf 与语言无关，支持多种编程语言。

* 最后，您必须将生成的文件导入到您的主代码中。在我们的例子中，我们将获得一个 Python 文件。这个 Python 文件需要导入到我们的主发布者或订阅者程序中。

以下是在`.proto`文件中定义模式的示例：

```protobuf
/*
Protobuf person model

*/
syntax = "proto3";
package com.mykafka.personmodel;
message Person {
  string name = 1;
  int32 age = 2;
  float temperature = 3;
  bool needsParkingPass = 4;
  optional string email = 5;
}
```

如您所见，与 JSON 相比，该模式更易于阅读。您可以定义数据类型和字段名称，还可以通过使用关键字`optional`来指定特定项目是否需要填写。每行末尾分配的数字是字段编号。

要进一步研究，您可以访问 [`https://protobuf.dev`](https://protobuf.dev)。如果您在工作站上安装 protoc 时遇到问题，我建议您从 git 仓库获取二进制文件，并将适合操作系统的可执行文件复制到一个单独的文件夹中。然后，打开一个终端，在该文件夹中使用以下命令。

```bash
./protoc --python_out=.
```

将 proto 文件保存在同一文件夹中至关重要。您将获得一个 Python 脚本，其模型名称后带有`pb2`后缀，如下所示：

`myschemafile_pb2.py`

该文件将被导入到我们的 Kafka 代码中。让我们看一个使用 Protobuf 序列化器的示例。这里我们将使用之前看到的同一个学生模型。Protobuf 模型可能如下所示：

```protobuf
/*
Protobuf student model

student model to interact with confluence kafka cluster
*/
syntax = "proto3";
package com.kafka.studentmodel;
message student {
    int32  student_id = 1;
    string  student_name = 2;
    string  course = 3;
}
message student_data {
    repeated student student_records = 1;
}
```

`repeated`是 Protobuf 语法，表示`student_data`可以包含多个`student`条目。

现在，让我们使用 Protobuf 编译器编译这个 proto 模型：

```bash
./protoc --python_out=. model.proto
```

这将为我们生成：

`model_pb2.py`

让我们检查一下这个生成的 Python 文件的内容：

```python
# -*- coding: utf-8 -*-
# 由 protocol buffer 编译器生成。请勿编辑！
# source: model.proto
# Protobuf Python 版本: 4.25.2
"""生成的 protocol buffer 代码。"""

from google.protobuf import descriptor as _descriptor
from google.protobuf import descriptor_pool as _descriptor_pool
from google.protobuf import symbol_database as _symbol_database
from google.protobuf.internal import builder as _builder

# @@protoc_insertion_point(imports)

_sym_db = _symbol_database.Default()
```

# Kafka 学生数据生产者与消费者示例

以下是生成的 Protobuf 描述符代码：

```python
DESCRIPTOR = _descriptor_pool.Default().AddSerializedFile(b'\n\x0bmodel.proto\x12\x16\x63om.kafka.studentmodel\"C\n\x07student\x12\x12\n\nstudent_id\x18\x01 \x01(\x05\x12\x14\n\x0cstudent_name\x18\x02 \x01(\t\x12\x0e\n\x06\x63ourse\x18\x03 \x01(\t\"H\n\x0cstudent_data\x12\x38\n\x0fstudent_records\x18\x01 \x03(\x0b\x32\x1f.com.kafka.studentmodel.studentb\x06proto3')

_globals = globals()

_builder.BuildMessageAndEnumDescriptors(DESCRIPTOR, _globals)

_builder.BuildTopDescriptorsAndMessages(DESCRIPTOR, 'model_pb2', _globals)

if _descriptor._USE_C_DESCRIPTORS == False:

DESCRIPTOR._options = None

_globals['_STUDENT']._serialized_start=39

_globals['_STUDENT']._serialized_end=106

_globals['_STUDENT_DATA']._serialized_start=108

_globals['_STUDENT_DATA']._serialized_end=180

# @@protoc_insertion_point(module_scope)
```

## 生产者脚本

现在，让我们看看我们的生产者脚本：

```python
from confluent_kafka import Producer

import model_pb2

from config import config

class student:

def __init__(self, student_id, student_name, course):

self.student_id = student_id

self.student_name = student_name

self.course = course

def callback(error, msg):

if error:

print(f"消息投递失败，原因：{error}")

else:

print(f"{msg.topic()} 的消息已投递至分区 {msg.partition()}")

producer = Producer(config)

student_data = model_pb2.student_data()

data = [

student(55052,"Johndoe","Chem101" ),

student(55053,"TestStudent","Physics"),

student(55054,"JaneDoe","Biology")

]

for i in data:

student_record = student_data.student_records.add()

student_record.student_id = i.student_id

student_record.student_name = i.student_name

student_record.course = i.course

serialized_data = student_data.SerializeToString()

# 发送序列化数据

producer.produce('student_data', serialized_data, callback=callback)

producer.flush()
```

我们导入了必要的类和方法，包括由 Protobuf 编译器生成的 Python 文件。然后，我们创建了一个学生数据类，以及一个回调函数，该函数会在向 Kafka 集群中的主题发布消息成功或失败时，将消息输出到控制台。接着，我们实例化了一个生产者类和学生数据。随后，我们通过迭代添加了测试数据集。序列化后，数据被发布到 Kafka 集群中的 `student_data` 主题。

`SerializeToString()` 方法由基于 Python 的 Protobuf 库提供。它是一种将 Protobuf 直接序列化为二进制字符串的方法。但它与 Schema Registry 的集成效果不佳。

### 安装 Protobuf 库

以下是安装 Protobuf 库的方法：

```
pip3 install protobuf
```

## 消费者脚本

现在，让我们看看这个 Kafka 项目的消费者或订阅者部分：

```python
from confluent_kafka import Consumer

import model_pb2

from config import config, sr_config

def set_consumer_configs():

config['group.id'] = 'student_group'

config['auto.offset.reset'] = 'earliest'

set_consumer_configs()

consumer = Consumer(config)

consumer.subscribe(['student_data'])

while True:

try:

event = consumer.poll(1.0)

if event is None:

continue

if event.error():

print(f"在 {event.error()} 处发生错误")

continue

student_data = model_pb2.student_data()

student_data.ParseFromString(event.value())

for record in student_data.student_records:

print(f"{record.student_id} 号学生 {record.student_name} 已注册课程 {record.course}")

except KeyboardInterrupt:

break

finally:

consumer.close()
```

# 流处理

到目前为止，我们已经了解了 Kafka 如何发布和订阅事件。我们还看到 Kafka 将事件存储在 Kafka 集群中。在本节中，我们将探讨 Kafka 如何处理和分析存储在 Kafka 集群中的这些事件。这就是我们在 Kafka 中所说的流处理。让我们先定义一下事件。它就是在某个时间点发生的事情。它可以是任何东西，从学生在在线系统中注册课程，到电视观众在真人秀节目中为他们最喜欢的表演者投票。事件本质上是不可变的提交日志，只允许追加操作。你无法撤销一个事件；以学生为例，你可以退出已注册的课程，但请记住，这将成为另一个事件。

流处理可以通过两种方法执行：一种是 Kafka Streams，另一种是 kSQL。

`Kafka Streams` 是一个 Java 库，为流处理应用程序提供了各种方法；而 `kSQL` 是一个模块，允许使用 SQL 方言构建流处理管道。`ksqlDB` 是一个专门为存储、处理和分析来自流处理应用程序的事件而构建的数据库。

`ksqlDB` 不是关系型数据库，也完全不能替代关系型数据库。`ksqlDB` 中没有索引的概念。`ksqlDB` 中的表与关系型数据库管理系统中的表不同。`ksqlDB` 的表更像是一个物化视图，而不是一个表。

流也可以被定义为一组事件，你可以从中派生出一个表，例如，一家公司的股票在给定时间间隔内在证券交易所上市/交易的价格。一段时间内的股票价格序列就是一个流。同一个流可以被视为一个表，用于描述该公司的最新股票价格。

## 有状态处理 vs. 无状态处理

在 `ksqlDB` 中可以执行两种类型的流处理，即：有状态处理和无状态处理。Kafka 中的无状态处理是指处理事件时无需依赖与先前消息相关的信息。由于无状态处理不需要了解过去发生或将来会发生的任何消息，因此处理起来更简单、更快速。以下是一些示例：

*   对值进行转换，不涉及任何关于过去或未来消息的知识，例如，将摄氏度转换为华氏度
*   基于某个条件过滤值，该条件不需要了解其他消息，例如，过滤出温度大于 35 摄氏度的数据
*   创建分支，根据应用于消息的条件将输入流分支到多个输出流

另一方面，有状态处理需要关于先前消息的信息。通常，它涉及执行需要一个或多个状态的操作。常见的应用包括在给定的窗口（基于时间的窗口或基于计数的窗口）内准备聚合。以下是一些示例：

*   在时间窗口内执行聚合，例如，获取给定时间间隔内的总数、总和、平均值和中位数
*   基于消息之间的关系执行连接操作，例如，连接来自多个流的数据

一个流处理应用通常涉及从一条或多条流中**摄取**数据，在适用时进行转换，并将其加载到存储中。一个普通的流处理应用可能包含许多活动部件，从数据摄取和执行转换（这可能意味着数据被写入临时区域，因此这又是另一个存储）到写入存储。`ksqlDB` 简化了整个流处理应用，并将其缩减为仅两个服务：执行计算的 `ksqlDB` 和构成存储的 Kafka。在 `ksqlDB` 中，你可以利用 Kafka 将数据从其他系统流式传输到 Kafka，以及从 Kafka 传输到其他下游系统。

有必要重申一下，`ksqlDB` 是独立于 Apache Kafka 的软件包。因此，我们将继续使用我们现有的集群，并可能创建新的流。

### 搭建 ksqlDB 集群

让我们尝试搭建一个 `ksqlDB` 集群，以便编写流式数据管道。首先，登录 Confluent Cloud，选择合适的环境，选择正确的集群，然后点击 ksqlDB。

![img/615530_1_En_9_Fig7_HTML.jpg](img/615530_1_En_9_Fig7_HTML.jpg)

*图 9-7: Confluent Kafka 中的 ksqlDB*

你可以选择自行创建集群，或从教程开始。目标是搭建一个开发实例，以便开始使用流式数据处理管道。你可以选择为你的 `ksqlDB` 集群启用全局访问，并确保为模式注册表授予适当的权限。

![img/615530_1_En_9_Fig8_HTML.jpg](img/615530_1_En_9_Fig8_HTML.jpg)

*图 9-8: 创建新的 ksqlDB 集群*

你可以选择最低的 Confluent 流处理单元，并选择启动集群。效果如下：

![img/615530_1_En_9_Fig9_HTML.jpg](img/615530_1_En_9_Fig9_HTML.jpg)

*图 9-9: 命名和调整 ksqlDB 集群大小*

请等待几分钟，让您的集群完成配置。当状态从“正在配置”变为“运行中”时，您就知道集群已就绪。

### 安装 Confluent CLI

一旦 `ksqlDB` 启动并运行，我们可以尝试为 Confluent 和 `ksqlDB` 设置命令行界面。

最简单直接的方法是启动一个包含 Confluent 平台的 Docker 容器。您可以访问相应页面，复制 Docker Compose 文件，然后运行这些命令来启动并运行。但是，在终端中安装这些软件包会更好，因为我们还将亲身体验安装一个没有可执行文件的软件包。让我们开始研究如何在 Debian 操作系统中安装 Confluent CLI。

第一步是安装先决条件：

```bash
sudo apt update
sudo apt install -y software-properties-common curl gnupg
```

让我们检查一下是否已安装 Java；观察以下命令的输出：

```bash
java --version
```

如果未安装 Java，请继续执行以下操作：

```bash
sudo apt install -y default-jdk
```

让我们导入 `ksqlDB` 软件包的公钥。这是为了验证软件包的真实性，是称为加密签名和验证的安全机制的一部分：

> **注意**
> 在加密签名和验证的世界中，软件包使用私钥进行数字签名。开发人员使用公开可用的公钥（我们正在导入的）来验证此签名。

```bash
wget -qO - https://packages.confluent.io/confluent-cli/deb/archive.key | sudo apt-key add
```

让我们将 `ksqlDB` 添加到我们的 apt 仓库并运行更新：

```bash
sudo add-apt-repository "deb https://packages.confluent.io/confluent-cli/deb stable main"
sudo apt update
```

让我们安装 CLI 软件包：

```bash
sudo apt install confluent-cli
```

就我而言，我还在本地安装了 Confluent 平台。这意味着要下载源代码，设置环境变量和路径，并检查安装是否成功。

让我们获取源代码并将 tarball 解压到您首选的目标位置：

```bash
curl -O https://packages.confluent.io/archive/7.5/confluent-7.5.3.tar.gz
tar xzvf confluent-7.5.3.tar.gz
```

复制文件路径以设置环境变量和路径：

```bash
export CONFLUENT_HOME=
export PATH=$PATH:$CONFLUENT_HOME/bin
```

在终端中输入以下帮助命令以检查安装是否成功：

```bash
confluent --help
```

![img/615530_1_En_9_Fig10_HTML.jpg](img/615530_1_En_9_Fig10_HTML.jpg)

*图 9-10: Confluent 命令行界面的帮助屏幕*

如果您看到类似于此屏幕的内容，则说明您已成功安装 Confluent 平台。

### 连接 ksqlDB 集群

现在是时候设置环境，以便您可以与云中的 `ksqlDB` 集群进行交互了。在仪表板中，单击 ksqlDB 并选择 CLI 说明。您将看到有关登录 Confluent Cloud CLI 并将上下文设置到您的环境和集群的说明：

```bash
confluent login
confluent environment use env-0jvd89
confluent kafka cluster use lkc-555vmz
```

### 创建 API 密钥与访问 ksqlDB

为您的 ksqlDB 集群创建 API 密钥和密钥：
```
confluent api-key create --resource lksqlc-8ggjyq
```
您将得到类似以下的内容：
![](img/615530_1_En_9_Fig11_HTML.jpg)
一张屏幕截图，显示 ksqlDB 集群 API 和密钥。虚线表格有 2 列和 2 行。行标题是 API 密钥和 API 密钥。

**图 9-11**
ksqlDB 集群 API 密钥和密钥

现在，输入以下内容以便您可以访问 ksqlDB 命令行界面；不要忘记输入正确的密钥和密钥：
```
$CONFLUENT_HOME/bin/ksql -u <your-key> -p <your-secret> https://pksqldb-pgkn0m.us-east-2.aws.confluent.cloud:443
```
效果如下：
![](img/615530_1_En_9_Fig12_HTML.jpg)
一个代码编辑器中的文本计算机屏幕。底部的详细文本代表版权信息、警告和服务器状态。

**图 9-12**
命令行界面上的 ksqlDB

### 创建流并进行基本查询

现在让我们从一些基本的流应用程序开始。流将模式与底层的 Kafka 主题关联起来。让我们首先在 Kafka 集群中创建一个主题。访问集群的仪表板并单击“主题”。效果如下：
![](img/615530_1_En_9_Fig13_HTML.jpg)
一个窗口屏幕截图，显示 Kafka 集群中的新主题。主题集群概览包括网络、API 密钥、集群设置、流谱系、流设计器、主题、ksqlDB、连接器和客户端。

**图 9-13**
在 Kafka 集群中创建新主题

选择“添加主题”并将主题命名为“students”。其他字段保留原样，然后选择“使用默认值创建”。效果如下：
![](img/615530_1_En_9_Fig14_HTML.jpg)
一张标题为“新主题”的窗口屏幕截图。表单有两个文本字段，主题名称 students 和分区 6。右下角，选择了“使用默认值创建”按钮。

**图 9-14**
为新主题命名并指定分区

选择为消息值创建模式的选项。在生产环境中，为消息键创建模式的选项有助于实现键结构的一致性以及路由到合适的分区（具有相同键的消息会被发送到同一个分区）。
![](img/615530_1_En_9_Fig15_HTML.jpg)
网页截图，标题为“您的主题已成功创建”。它代表了数据契约的定义。在左下角，选择了“为消息值创建模式”。在右下角，选择了“创建模式”按钮。

**图 9-15**
为主题创建模式

点击“创建模式”并粘贴以下内容：
```
syntax = "proto3";

message students {
  int32  id = 1;
  string  name = 2;
  string  course = 3;
}
```
![](img/615530_1_En_9_Fig16_HTML.jpg)
窗口屏幕截图，代表正在创建新模式。在左侧，选择了“网络”、“API 密钥”、“集群设置”和“ksqlDB”。在右上角，选择了“运行查询”按钮。

**图 9-16**
创建新模式

还可以通过 ksqlDB 命令行界面创建流。让我们详细看看这种方法。使用 CLI，我们将创建一个名为 `students` 的新流。找到 ksqlDB 中的“编辑器”选项卡，并输入以下代码来创建一个新的 `students` 流。确保将 `auto.offset.reset` 属性设置为 `earliest`。
操作如下：
```
set 'auto.offset.reset' = 'earliest';

CREATE STREAM students (
    id INTEGER KEY,
    name string,
    course string)
  WITH (kafka_topic = 'students',
    PARTITIONS=1,
    value_format='JSON');
```
现在，向流中插入数据：
```
INSERT INTO students (id, name, course) VALUES (5, 'John', 'Physics');
INSERT INTO students (id, name, course) VALUES (6, 'Joe', 'Math');
INSERT INTO students (id, name, course) VALUES (7, 'Jane', 'Physics');
INSERT INTO students (id, name, course) VALUES (8, 'Jan', 'Physics');
INSERT INTO students (id, name, course) VALUES (10, 'Alice', 'Math');
INSERT INTO students (id, name, course) VALUES (12, 'Al', 'Chemistry');
INSERT INTO students (id, name, course) VALUES (4, 'Moe', 'Chemistry');
```
现在我们可以尝试对这个流运行一个查询。这里有一个示例查询供您入门：
```
SELECT * FROM STUDENTS WHERE COURSE IN ('Math','Physics');
```
![](img/615530_1_En_9_Fig17_HTML.jpg)
窗口屏幕截图，代表一个新查询。它表示从 students 表中选择 course 在（Math, Physics）中的记录，并添加了查询属性，同时“运行查询”按钮被选中。

**图 9-17**
执行新查询

请注意，您可以在浏览器中的仪表板上查看结果。让我们尝试通过命令行界面访问 Kafka 集群。登录我们之前安装的 Confluent Cloud，并使用相同的命令访问 ksqlDB。
在 ksqlDB 提示符下，输入以下查询：
```
ksql> select * from students where course = 'Physics' EMIT CHANGES;
```
您将看到以下结果：
![](img/615530_1_En_9_Fig18_HTML.jpg)
一个包含三列（ID、NAME、COURSE）的表格。该表显示了四行数据。数据为 5、7、8 和 16。名称包括 John、Jane、Jan 以及从浏览器输入的内容。

**图 9-18**
查询结果

让我们导航到浏览器上的仪表板，插入以下查询：
```
insert into students (id, name, course) values (16, 'EnteredFromBrowser', 'Physics');
```
点击“运行查询”。注意您保存 SQL 结果的终端。您很可能会看到以下内容：
![](img/615530_1_En_9_Fig19_HTML.jpg)
一个包含三列（ID、NAME、COURSE）的表格。该表显示了四行数据。第一行代表 ID 5、NAME John、COURSE Physics。第二行显示 ID 7、NAME Jane、COURSE Physics。第三行显示 ID 8、NAME Jan、COURSE Physics。

**图 9-19**
查询结果

我们刚刚见证了这些所谓的查询并非完全像关系数据库查询，而是流。一旦我们将数据插入流中，相同的数据就会出现在我们持续的流输出中。

### Kafka Connect

我们在之前的讨论中看到，Kafka Connect 如何帮助我们可靠地在 Apache Kafka 和其他数据库系统之间进行扩展和数据流传输。Kafka Connect 提供了一种称为连接器的工具，它可以与其他数据系统连接，以启用和协调数据流。有两种类型的连接器，即源连接器和汇连接器。源连接器从数据系统中提取数据，将其流式传输到 Kafka 主题，而汇连接器则将数据从 Kafka 主题传送到数据系统。
Kafka Connect 服务可以以独立模式安装，也可以为生产目的以分布式模式部署。Kafka Connect 是流式数据/ETL（提取、转换、加载）管道的一个重要方面，尤其是在架构中包含 Kafka 时。您可以通过 ksqlDB 使用 Kafka Connect 服务连接到外部数据系统。请在此处查看 Oracle 数据库示例连接器；括号内包含值的字段需要填入相应的数据库凭据：
```
CREATE SOURCE CONNECTOR IF NOT EXISTS CustomerDB WITH (
    'connector.class'          = 'OracleDatabaseSource',
    'kafka.api.key'            = '<your-key>',
    'kafka.api.secret'         = '<your-secret>',
    'connection.host'          = '<your-host>',
    'connection.port'          = '1521',
    'connection.user'          = '<your-user>',
```

# Apache Kafka 流式数据管道

## 最佳实践

如您所见，我们使用商业发行版 Confluent 演示了 Kafka 流式数据管道。在撰写本章时，供应商提供了数百美元的云积分，可用于获取他们的服务。这样，任何想要初步了解 Kafka 的人都可以利用他们的服务来运行示例集群。同时，注意后台未使用的 Kafka 集群也是一个好习惯。以下是一些可用于清理未使用 Kafka 资源的命令：

```sql
DROP STREAM IF EXISTS customers;
```

您还可以异步删除支持该表或流的相应主题：

```sql
DROP TABLE IF EXISTS t_customers DELETE TOPIC;
```

您也可以移除 Kafka 连接器：

```sql
DROP CONNECTOR IF EXISTS customerDB;
```

## 结论

可以肯定地说，Apache Kafka 代表了我们在构建实时数据管道和流分析解决方案方面的一种范式转变。其架构具有可扩展性、分布式和容错性，为构建和管理能够处理现代数据流的复杂数据管道提供了可靠的基础。我们讨论了 Kafka 架构及其各种组件，以及它们如何协同工作。我们研究了发布者-订阅者模型以及主题如何存储在数据库中。然后，我们研究了使用 ksqlDB 的流式数据管道，以及如何分析数据以获得业务洞察。希望您从本章学到的技能和知识能够帮助您构建实时数据管道。

# 10. 使用 FastAPI 进行机器学习工程和数据 REST API 开发

## 引言

在数据工程和机器学习建模这个复杂的领域中，存在多个动态环节（正如你目前所见），提供数据服务并部署机器学习模型以供使用至关重要。在本章中，我们将讨论 FastAPI。FastAPI 是一个 Python 库，主要用于支持 Web 应用程序开发和微服务开发。我们将着眼于 FastAPI，其唯一目的是利用该库来提供数据服务并将机器学习模型作为服务交付。尽管 Django 在基于 Python 的应用程序开发领域赢得了众多青睐，但 FastAPI 简单、强大且功能非常强大。对于机器学习工程师或数据工程师来说，理解应用程序编程接口的概念非常重要。

本章结束时，你将学习到

*   Web 服务和 API 的基础知识、各种类型的 API 及其概念
*   FastAPI 及其设置过程
*   Pydantic 与 FastAPI 的集成，实现强大的数据验证
*   FastAPI 依赖注入的概念与实现
*   数据库集成，包括 SQL、NoSQL 和对象关系映射方法
*   使用 FastAPI 构建 RESTful 数据 API
*   使用 FastAPI 进行机器学习 API 的开发与部署

## Web 服务与 API 简介

企业倾向于通过提供有价值的数据产品来为其内部或外部客户创造价值。数据产品由数据工程师通过识别各种数据源、执行各种操作，并将来自这些多个来源的数据进行组合而构建（操作顺序不一定相同）。然后，这些数据资产被共享给各种内部和外部客户。企业和 IT 系统共享数据并相互通信的有效方式之一就是 API 的概念。API 代表应用程序编程接口。API 由各种公司和组织构建，以允许不同的用户群体利用其服务。一个提供数据即服务（DaaS）的组织的好例子是 OpenWeather API。

### OpenWeather API

OpenWeather API 是由 OpenWeather 提供的一项数据服务，该公司通过 REST API 调用向个人和组织提供天气数据。OpenWeather API 使工程师能够以编程方式访问广泛的天气数据。其中一些数据包括全球各个地点的当前天气数据、预报和历史数据。他们为当前天气数据、预报天气数据、每日汇总、历史数据等提供了各种 API 端点。你可以在以下网站注册 OpenWeather API 以获取访问密钥。通过使用该访问密钥，你可以获取各种数据：
[`https://openweathermap.org/api`](https://openweathermap.org/api)

### API 的类型

并非所有 API 都以相同的方式构建，它们服务的用户群体也不尽相同。最常见的 API 类别是开放 API。这些 API 由各种组织提供，并且可以免费访问和使用。工程师可以不受任何限制地访问和使用这些服务。另一类 API 是内部 API。这些 API 同样提供服务；但是，它们对组织内部是私有的。它们不对外公开。相反，它们仅由给定组织内的各个团队访问和利用，以获取数据和功能。内部 API 还有另一种变体，称为合作伙伴 API。这些 API 同样具有一定程度的机密性；但是，它们可能服务于该组织及其部分合作伙伴组织。
除了不同的类别及其服务目的之外，还有多种访问 API 的方法；其中一些如下所示。

#### SOAP API

SOAP 代表简单对象访问协议。SOAP API 已经存在很长时间，并用于企业级应用程序。SOAP 主要用于在两个应用程序之间交换结构化数据。SOAP 使用 HTTP 或 SMTP 来传输消息。它们使用 XML 模板进行消息格式化。

#### REST API

REST 代表表述性状态转移。REST 是一种用于构建轻量级和可扩展 API 的架构风格。它们利用 HTTP 方法进行通信。一些 HTTP 方法包括 GET、PUT、POST 等。REST API 以 JSON 或 XML 格式返回数据。这些 API 通常用于基于 Web 的应用程序和移动应用程序。

#### GraphQL API

GraphQL 代表图形查询语言。顾名思义，GraphQL 使用基于查询的请求来获取数据，确保只获取所需的数据。GraphQL 的重点在于避免过度获取。虽然这些信息对你来说可能有点难以理解（取决于你的背景），但最好知道存在这样的 API。

#### Webhooks

Webhooks 是另一种类型的 API，可实现系统之间的实时通信。与 REST API 类似，Webhooks 也使用 HTTP 请求进行通信。

### API 的典型流程

API 充当软件应用程序之间的中间人，使它们能够更顺畅地相互交互。通信可以在两个软件应用程序之间进行，也可以是工程师寻求从主机获取服务。
这一切始于其中一方（我们称之为客户端）通过 API 向主机发送请求。API 携带消息并将其提交给主机。主机将处理该消息，准备必要的程序，并提供所请求的信息。
现在，主机（我们称之为服务器）响应客户端。响应中包含针对相应请求的信息。这种交互或通信（无论你更喜欢哪种称呼）是通过数据交换发生的。这些数据以某种方式结构化，以便 API 和服务器能够理解。这通常被称为数据格式。最常见的数据格式是 JSON 和 XML。这些数据格式确保所有三方——客户端、API 和服务器——能够理解彼此之间的交互。

### 端点

如前所述，API 是中介；它们充当两方之间的桥梁。API 提供了一种机制，供客户端发起与服务器的对话。其中一种机制称为 URL 或 URI，即统一资源定位符或统一资源标识符。它包含服务器的地址，以便将请求发送到正确的地址。

让我们看下面的例子：

*   简·多伊，
*   会计部，
*   ABC 公司，
*   美国某镇

假设有人向这个地址发送一封实体邮件，那么它将到达相应的城镇，到达相关的组织（在此例中是假设的），到达相应的部门，并到达该部门中指定的收件人。
REST API 端点类似于刚才提到的人的地址。

URL 或 URI 的结构如下：

```text
https://developer.MyFavoriteMovieTheatre.com/api/movie-theatre/showtimes?movie_id=5&date=2023-06-06
```

'connection.password'      = '',

'db.name'                  = '',

'table.whitelist'          = 'CUSTOMERS',

'timestamp.column.name'    = 'created_at',

'output.data.format'       = 'JSON',

'db.timezone'              = 'UCT',

'tasks.max'                = '1'

);

在此处
`https://developer.MyFavoriteMovieTheatre.com` 是 API 的基础 URL。
`/api/movie-theatre/showtimes` 表示用于检索放映时间的路径参数。
`movie_id=5` 和 `date=2023-06-06` 表示查询参数。它们允许你指定想要观看的电影和日期。`movie_id` 参数对应某一天上映的电影。
整个 URL 被称为端点。端点就像之前提到的物理地址一样，是地址。通过指定正确的基础 URL、正确的路径和适当的查询参数，电影院服务器将能够处理你的请求并检索响应。

API 通常需要身份验证。这样做是为了确保只有经过授权的用户或应用程序才能访问其功能。有多种方式可以对 API 进行身份验证。一些常见的方式包括使用 API 密钥、令牌和其他身份验证机制。

API 将数据从源端移动到目标端，并提供了一种更简单的方式来摄取数据。

## API 开发流程

要构建一个 API，我们首先要定义 API 需求，例如它可能需要访问的数据、它将执行的操作以及它将产生的结果等等。一旦定义了需求，就可以着手构建数据模型、方法、端点，并定义请求、身份验证、授权等的响应数据格式。根据需求实现 API，在此过程中设置所需的服务器及其基础设施，实现代码和依赖项（如果有），以及其他相关任务。在实现阶段或实现后阶段，对特性和功能进行充分的测试可能是一个好步骤。最后一步是将 API 部署到生产服务器；性能和用法监控可能有助于解决任何可能需要改进的地方。

## REST API

如前所述，REST 是表述性状态传递的缩写。它是符合 REST 架构标准的 Web 服务类型之一。REST 架构标准利用给定网络上的客户端-服务器通信。REST 架构是无状态的，这意味着服务器不会为来自客户端的请求维护会话信息。相反，请求消息本身将包含足够的信息供服务器处理该请求。

RESTful 服务监听 HTTP 方法并从服务器检索响应。HTTP 代表超文本传输协议。HTTP 方法的作用是告诉服务器需要执行哪个操作来为请求检索响应。HTTP 方法分为两类：安全 HTTP 方法和幂等 HTTP 方法。安全 HTTP 方法无论被调用多少次，总是返回相同或相似的响应。安全 HTTP 方法不会更改服务器上的数据。幂等方法可能能够更改服务器上的数据。一些更常见的 HTTP 方法（或动词）包括：

*   `GET`：从服务器读取数据，不以任何方式更改或修改资源。

    以下是一个简单的 GET 请求，用于请求某家多厅影院的所有电影：

    ```
    GET /multiplexapi/movies/
    ```

*   `HEAD`：类似于 GET，仅检索头部数据而不包含内容。

    以下是一个 HEAD 请求，用于查看电影的最后更新时间：

    ```
    HEAD /multiplexapi/theatreslist
    ```

*   `POST`：触发一个动作或可用于创建新数据；会在服务器上进行更改。

    以下是为观影者创建新预订的示例：

    ```
    POST /multiplexapi/bookings
    ```

*   `PUT`：用新数据替换当前数据；可视为执行更新操作。

    以下是更新电影放映时间的示例：

    ```
    PUT /multiplex/movies/SampleMovie { “showtime”: “5:55PM” }
    ```

*   `PATCH`：类似于 PUT，但部分替换或更新当前数据为新数据。

    以下是更新在第三影厅放映的“SampleMovie”剩余座位数的示例：

    ```
    PATCH /multiplex/threatre/3 { “seats_available” : 5 }
    ```

*   `OPTIONS`：用于获取给定 URL 到服务器的一组允许的 HTTP 方法。

    以下是检查各种预订选项的示例：

    ```
    OPTIONS /multiplexapi/bookings
    ```

*   `CONNECT`：用于建立到服务器的隧道的方法。

    以下是建立到多厅影院的 TCP 连接的示例：

    ```
    CONNECT app.multiplexapi.com:3690
    ```

### HTTP 状态码

当服务器收到来自客户端的请求时，它会处理并准备一个响应。HTTP 状态码是与响应一起接收的代码。以下是客户端收到的一些常见状态码：

表 10-1
一些常见的 HTTP 状态码及其描述

| HTTP 状态码 | 描述 |
| :--- | :--- |
| 200 OK | 请求成功，服务器返回了请求的数据。 |
| 201 Created | 请求成功，作为结果创建了新资源。 |
| 204 No Content | 请求成功，但无需返回任何内容（通常用于 DELETE 操作）。 |
| 301 Permanent Redirect | 请求的页面已永久移动到新的 URL。 |
| 400 Bad Request | 由于客户端错误，服务器无法处理请求。 |
| 401 Unauthorized | 服务器需要用户身份验证（有效凭据）才能处理请求。 |
| 403 Forbidden | 服务器理解你的请求，但拒绝授权，尽管提供了有效凭据（可能权限不足）。 |
| 404 Not Found | 请求的内容在服务器上找不到。 |
| 500 Internal Server Error | 由于某些意外问题，服务器似乎无法找到请求的内容。 |
| 503 Service Unavailable | 服务器目前无法处理请求。 |

这些 Web 服务通过 API 向外界公开其数据或服务。

## Python 中的 API 实现

要使用 Python 构建 API，有相当多的包可用。有 Flask，这是一个以构建简单、易于设置和较小型的 API 而闻名的 Python 库。流行的还有 Django 和 FastAPI。Django 已经存在很长时间，功能更多，是比 Flask 更好的选择。在本章中，我们将深入探讨 FastAPI。

### FastAPI

FastAPI 相对于其他 Python 库提供了一些优势；首先，FastAPI 通过 `asyncio` 支持 Python 的异步编程。FastAPI 自动生成基于 Open API 标准的文档，允许 API 的用户浏览文档并测试各种端点。最重要的是，顾名思义，FastAPI 性能高，比许多其他 Python 库更快。

#### 设置与安装

以下是在你的机器上设置 FastAPI 的方法：

```
pip install fastapi
```

FastAPI 不附带 Web 服务器；让我们也安装一个 Web 服务器：

```
pip install "uvicorn[standard]"
```

假设你正在使用虚拟开发环境，我也鼓励你考虑这个命令：

```
pip install "fastapi[all]"
```

这将安装 FastAPI、Pydantic 和 Starlette Python 库使用的所有可选依赖项，其中包括 “uvicorn” Web 服务器和异步客户端 “http”。

现在让我们考虑一个使用 FastAPI 的简单 Hello World 应用程序。如下所示：

```python
from fastapi import FastAPI, APIRouter

app = FastAPI()

api_router = APIRouter()

@api_router.get("/")
def root() -> dict:
    return {"msg": "Hello, World!"}

app.include_router(api_router)
```

#### 核心概念

FastAPI 基于 ASGI 标准和 Starlette 构建。FastAPI 利用 ASGI 服务器来实现其异步能力。ASGI 代表异步服务器网关接口。ASGI 是一个相对较新的 Web 标准，用于服务器与客户端进行异步通信。FastAPI 支持 Hypercorn 和 Uvicorn Web 服务器，因为它们与 ASGI 标准兼容。另一方面，Starlette 是一个 ASGI 标准框架，有助于实现 FastAPI 的核心功能。Starlette 提供了核心的路由基础设施，处理请求和响应、数据库集成以及许多其他被 FastAPI 利用的功能。在 Starlette 和 ASGI 标准之上，FastAPI 原生提供了数据验证、文档生成等功能，因此工程师可以专注于事物的业务逻辑。

#### 路径参数和查询参数

如你所知，路径参数是端点（endpoint）的组成部分，而查询参数是可选的类似过滤器的参数。让我们看一个例子；这是一个 URL：

```

# FastAPI 路径与查询参数

## URL 结构解析

以下是用于亚马逊商品页面的示例 URL：
`https://www.amazon.com/Sweetcrispy-Ergonomic-Computer-Comfortable-Armrests/dp/B0C61DLQNH/ref=mp_s_a_1_5?crid=1CE83JNY3DEDI&keywords=chair&qid=1703648878&refinements=p_36%3A1253524011&rnid=386465011&s=home-garden&sprefix=cha%2Caps%2C478&sr=1-5`

其结构如下：
- 基础 URL 是 `https://www.amazon.com/`。
- 路径参数是：
    - `/Sweetcrispy-Ergonomic-Computer-Comfortable-Armrests`
    - `/dp`
    - `/B0C61DLQNH`
    - `/ref=mp_s_a_1_5`
- 查询参数是（位于 `?` 之后）：
    - `crid=1CE83JNY3DEDI`
    - `keywords=chair`
    - `qid=1703648878`
    - `refinements=p_36%3A1253524011`
    - `rnid=386465011`
    - `s=home-garden`
    - `sprefix=cha%2Caps%2C478`
    - `sr=1-5`

## FastAPI 基础

当你编写一个函数时，可以将变量传递给该函数，这些变量可以被调用。如下所示：
```python
from fastapi import FastAPI, APIRouter
```

让我们创建一个示例学生和课程数据的列表：
```python
Students = [
    {
        "Student_id": 1,
        "Student_name": "StudentPerson1",
        "Course": "BIO 101",
        "Department": "Biology"
    },
    {
        "Student_id": 2,
        "Student_name": "StudentPersonTwo",
        "Course": "CHEM 201",
        "Department": "Chemistry"
    },
    {
        "Student_id": 3,
        "Student_name": "StudentPerson3",
        "Course": "MATH 102",
        "Department": "Mathematics"
    },
]
```

让我们实例化 `FastAPI`（使用合适的标题）和 `APIRouter` 对象：
```python
app = FastAPI(title="Student/Course Data")
api_router = APIRouter()
```

之前定义的主函数在结构上没有太大变化。让我们添加一个名为 `get_student` 的新函数来检索学生记录。注意使用了包含变量的装饰器。该变量或参数的值将在运行时传递：
```python
@api_router.get("/")
def hello() -> dict:
    return {"msg": "Welcome to the root page"}

@api_router.get("/student/{Student_id}")
def get_student(Student_id: int) -> dict:
    for s in Students:
        if s["Student_id"] == Student_id:
            result = s
            return result

app.include_router(api_router)
```

让我们实时运行这段代码。运行方式如下：
```bash
uvicorn main3:app —reload
```

将返回以下堆栈跟踪：

![图 10-1 执行一个简单的 FastAPI 应用](img/615530_1_En_10_Fig1_HTML.jpg)
*图 10-1 执行一个简单的 FastAPI 应用。命令提示符正在运行 uvicorn 命令以启动一个 FastAPI Web 应用程序。重新加载器进程启动，随后是服务器进程。应用程序启动成功完成，服务器已准备好处理请求。*

现在，让我们打开浏览器并输入地址 `http://127.0.0.1:8000/student/3:`

![图 10-2 在浏览器中查看 FastAPI 应用](img/615530_1_En_10_Fig2_HTML.jpg)
*图 10-2 在浏览器中查看 FastAPI 应用。网页浏览器显示来自 FastAPI 应用程序的 JSON 响应。该响应包含关于 ID 为 3 的学生信息，包括其姓名、课程和院系。*

> **注意**：你甚至可以指定路径参数的数据类型。在前面的例子中，`get_student()` 函数有一个路径参数，该路径参数的数据类型也被定义了。

如果你输入了代码中未定义的数据类型，`FastAPI` 会抛出一个 HTTP 错误，如下所示：

![图 10-3 FastAPI 抛出错误](img/615530_1_En_10_Fig3_HTML.jpg)
*图 10-3 FastAPI 抛出错误。错误信息提示提供的学生 ID "three" 不是有效的整数。*

`FastAPI` 期望接收的 `student_id` 是 `3`（整数），而不是 `"three"`（字符串）。

## 查询参数

除了定义路径参数，你还可以在函数内部定义查询参数。回想一下我们之前关于亚马逊 URL 的例子。查询参数位于 URL 的末尾，用于过滤或向端点提供附加信息。查询参数在问号（`?`）之后指定。多个查询参数用与号（`&`）分隔。

示例如下：
```python
from fastapi import FastAPI

app = FastAPI()

Students = [
    {
        "Student_id": 1,
        "Student_name": "StudentPerson1",
        "Course": "BIO 101",
        "Department": "Biology"
    },
    {
        "Student_id": 2,
        "Student_name": "StudentPersonTwo",
        "Course": "CHEM 201",
        "Department": "Chemistry"
    },
    {
        "Student_id": 3,
        "Student_name": "StudentPerson3",
        "Course": "MATH 102",
        "Department": "Mathematics"
    },
]

@app.get("/students/")
def read_item(skip: int = 0, limit: int = 10):
    return Students[skip : skip + limit]
```

这个示例会执行两个过滤操作，即跳过一定数量的记录并限制结果的数量。URL 如下所示：
`http://127.0.0.1:8000/items/?skip=1&limit=2`
查询参数是 `skip` 和 `limit`；我们打算跳过第一条记录，并将总结果限制为仅两条记录。因此输出结果如下所示：

![图 10-4 在 URL 地址中定义查询参数](img/615530_1_En_10_Fig4_HTML.jpg)
*图 10-4 在 URL 地址中定义查询参数。网页浏览器显示 FastAPI 应用程序的 JSON 响应。该响应包含两名学生的信息，包括他们的 ID、姓名、课程和院系。这些学生是通过分页参数 `skip=1` 和 `limit=2` 检索到的。*

## Pydantic 集成

正如我们在前一章所见，`Pydantic` 是一个简单而强大的数据验证工具。`FastAPI` 与 `Pydantic` 集成得很好。

让我们看一个 `Pydantic` 集成的示例：
```python
from fastapi import FastAPI, APIRouter
from pydantic import BaseModel
from typing import Sequence

app = FastAPI()
api_router = APIRouter()

Students = [
    {
        "Student_id": 1,
        "Student_name": "StudentPerson1",
        "Course": "BIO 101",
        "Department": "Biology"
    },
    {
        "Student_id": 2,
        "Student_name": "StudentPersonTwo",
        "Course": "CHEM 201",
        "Department": "Chemistry"
    },
    {
        "Student_id": 3,
        "Student_name": "StudentPerson3",
        "Course": "MATH 102",
        "Department": "Mathematics"
    },
]

class Student(BaseModel):
    Student_id: int
    Student_name: str
    Course: str
    Department: str

class retrieveStudent(BaseModel):
```

## 代码示例

该代码使用了与之前实例类似的示例。`get` 方法通过 `Student_id` 检索学生。`post` 方法将创建一个新学生及其所需的参数。请注意，此示例仅用于说明目的。数据（即发布的数据）不会持久化到磁盘上。

## 响应模型

在我们定义的 `get` 和 `post` 函数中，可以看到使用了 `response_model` 属性。`response_model` 会将给定的数据转换为 Pydantic 格式，同时仍然返回函数指定要返回的类型。`response_model` 实际上并不会改变函数的返回类型。`response_model` 参数告诉 FastAPI 将返回的数据转换为 Pydantic 对象，并在需要的地方执行必要的验证。

你可以使用 FastAPI 装饰器方法的 `response_model` 参数。其中一些方法包括：

```python
app.get()
app.post()
app.delete()
```

……等等。
还可以选择将 `response_model` 设置为 `None`。这将使 FastAPI 跳过执行 `response_model` 本应执行的验证或过滤步骤。通过将响应模型指定为 `None`，你仍然可以保留返回类型注解，并继续从你的 IDE 或类型检查器那里获得类型检查支持。

## 注意

此外，FastAPI 提供基于 Open API 标准的文档。在任何时候，如果你希望在开发中访问它，只需在你的端点后加上 `docs`。例如，`http://127.0.0.1:8000/docs` 将检索到以下文档页面（图 10-5）。

![img/615530_1_En_10_Fig5_HTML.jpg](img/615530_1_En_10_Fig5_HTML.jpg)

一个网页浏览器读取 FastAPI 应用程序 Swagger UI 文档的截图。该文档显示了可用的 API 端点、它们的 HTTP 方法以及它们期望的参数和响应。

图 10-5
基于 Open API 标准的 FastAPI 文档

在该窗口中，请展开任意 HTTP 方法以尝试其功能。以下是 "获取学生" 的界面：

![img/615530_1_En_10_Fig6_HTML.jpg](img/615530_1_En_10_Fig6_HTML.jpg)

一个 Swagger UI 界面读取 FastAPI 应用程序中 GET API 端点详细信息的截图。该端点根据学生 ID 检索学生。它需要一个类型为整数的路径参数 `Student_id`。响应是一个包含学生信息的 JSON 对象。

图 10-6
此应用的 GET 请求文档

当你点击 "Try it out" 时，文本框将变为可编辑状态，因此你可以输入一个给定的请求，让服务器处理并返回响应。服务器还会为该请求生成一个 Curl 命令、相应请求的端点、状态响应消息以及响应体。界面如下所示：

![img/615530_1_En_10_Fig7_HTML.jpg](img/615530_1_En_10_Fig7_HTML.jpg)

一个 Swagger UI 界面读取 FastAPI 应用程序中 GET API 端点详细信息的截图。该端点根据学生 ID 检索学生。用户输入了学生 ID 3 并点击了执行按钮。响应体显示了该学生的信息。

图 10-7
FastAPI 上的 GET 请求示例

## FastAPI 中的依赖注入

当你编写数据处理管道时，可能会遇到一个或一组功能，它们本质上是相同的代码，但应用于多个数据管道。最简单、最快速的例子是连接到特定的数据库。可能存在 "n" 个数据管道都需要连接到某个特定数据库的情况。显然，所述数据管道依赖于与这个特定数据库的连接。为你的代码提供这种数据库连接依赖关系的过程称为依赖注入。

### 注意

注入本质上就是在这里提供功能。不是将功能的代码写在数据处理管道内部，而是将代码写在外部，然后注入到数据处理管道中。

FastAPI 有一个简单直观的依赖注入系统。`Depends` 方法为 FastAPI 提供了依赖注入功能。让我们看下面的例子：

```python
from fastapi import FastAPI, Depends
from databases import Database
import sqlalchemy

app = FastAPI()
database = Database("Your database credentials here")

def dbConnect():
    return database

@app.get("/users")
def get_users(db=Depends(dbConnect)):
    conn = db
    users = conn.fetch_all("SELECT * from users")
    return {"users": users}
```

在上述示例中，我们设计了一个简单的 API，其中包含一个 GET 请求，该请求在被调用时会检索“users”。为了让 GET 方法能够检索并返回该信息，它需要与数据库建立连接。你可能已经注意到，数据库连接被定义为一个独立的函数。然后，该数据库连接通过 FastAPI 的 `Depends` 方法作为参数传递给 `get_users` 函数。这样，`get_users` 函数就会调用 `dbConnect()` 函数，该函数会建立与数据库的连接。

## FastAPI 与数据库的集成

FastAPI 可以与任何关系型数据库集成。与数据库交互的方式有多种。让我们先来看一种简单直接的关系型数据库交互方式。为了进行说明，我们将以在 Debian 工作站上使用名为 MySQL 的开源关系型数据库为例。让我们执行以下命令：

```bash
sudo apt update
sudo apt install mysql-server
```

检查 MySQL 是否已正确安装：

```bash
mysqld —version
```

让我们以 root 身份登录：

```bash
mysql -u root -p
```

输入相应的密码后，你将看到 mysql 提示符：

```
mysql>
```

让我们创建一个新的数据库用户：

```sql
create user 'newscott'@'localhost' identified with caching_sha2_password by 'Tiger@#@!!!9999';
```

要列出所有数据库，请使用

```sql
show databases;
```

要选择一个数据库，请使用

```sql
use your-database-name;
```

要创建一个数据库，请使用

```sql
create database your-database-name;
```

让我们创建一个数据库，在该数据库中创建一个表，并向该表中插入数据：

```sql
create database stu;
Use stu;
CREATE TABLE students (
    id INT(11) NOT NULL AUTO_INCREMENT,
    name VARCHAR(100) NOT NULL,
    course VARCHAR(100) NOT NULL,
    PRIMARY KEY (id)
);
```

以下命令也很有用：

```sql
show tables;
```

将输出

```
+---------------+
| Tables_in_stu |
+---------------+
| students      |
+---------------+
```

# FastAPI 与 MySQL 数据库交互示例

本文档将演示如何使用 FastAPI 与 MySQL 数据库进行交互，涵盖从数据库设置、基本 CRUD 操作到使用 ORM 的高级用法。

## 数据库设置与初始化

### 创建表与查看数据

首先，我们向 `students` 表插入一些测试数据。

```sql
insert into students (id, name, course) values (1, "test", "tescourse");
insert into students (id, name, course) values (2, "JohnDoe", "Physics101");
insert into students (id, name, course) values (3, "Joe", "Chemistry101");
```

让我们查看该表：

```sql
select * from stu.students;
```

查询结果如下：

```
+----+---------+--------------+
| id | name    | course       |
+----+---------+--------------+
|  1 | test    | tescourse    |
|  2 | JohnDoe | Physics101   |
|  3 | Joe     | Chemistry101 |
+----+---------+--------------+
```

### 用户权限设置

让我们为新创建的数据库用户授予权限：

```sql
grant all privileges stu.* to 'newscott'@'localhost';
flush privileges;
```

> **注意**：这些命令仅用于开发目的的简单数据库设置。在设置生产环境数据库实例时，你需要使用 `mysql_secure_installation` 并遵循 DBA 的最佳实践。

## FastAPI 应用实现

现在，创建一个名为 `db1.py` 的新 Python 脚本，并输入以下代码：

```python
from fastapi import FastAPI
from mysql import connector

dbapp = FastAPI()
database_creds = connector.connect(
    host="localhost",
    user="newscott",
    password="Tiger@#@!!!9999",
    database="stu",
    auth_plugin='mysql_native_password'
)

# GET 请求，列出所有学生
@dbapp.get("/AllStudents")
def get_students() -> dict:
    cursor = database_creds.cursor()
    cursor.execute("Select * from students")
    result = cursor.fetchall()
    return { "students" : result }

# GET 请求，根据学生 ID 获取姓名
@dbapp.get("/student/{id}")
def get_student(id: int) -> dict:
    cursor = database_creds.cursor()
    cursor.execute(f"Select name from student where id = {id}")
    result = cursor.fetchone()
    return { "student" : result }

# GET 请求，根据学生姓名获取课程
@dbapp.get("/stucourse/{name}")
def stucourse(name: str) -> dict:
    cursor = database_creds.cursor()
    cursor.execute(f"Select course from student where name = {name}")
    result = cursor.fetchall()
    return { "student" : result }

# POST 请求，插入新学生
@dbapp.post("/newstudent")
def newstudent(name: str, course: str) -> dict:
    cursor = database_creds.cursor()
    sql = "INSERT INTO student (name, course) VALUES (%s, %s)"
    val = (name, course)
    cursor.execute(sql, val)
    database_creds.commit()
    return {"message": "student added successfully"}
```

这是一个非常直接的示例，演示了 FastAPI 如何与 MySQL 数据库实例交互。我们创建了带有特定 SQL 查询的函数，使用适当的 FastAPI 方法（`@dbapp.get`， `@dbapp.post`）对函数进行了装饰，并在需要的地方传递了参数。

## 对象关系映射 (ORM)

对象关系映射是一种编程概念，用于在面向对象编程代码和关系型数据库之间建立桥梁。它将代码中的对象“映射”到关系型数据库对象。最常见的情况是，你在代码中创建一个类，该类代表关系型数据库中的一个表。表的字段名称及其相应类型由类的属性表示。最后，这些对象的每个实例都代表关系型数据库中表的一行。

ORM 库简化了 Python 脚本与关系型数据库之间的交互。使用 ORM 的优势在于，它旨在消除任何 SQL 注入攻击的可能性。

一些更常用的 ORM 库包括 Peewee、SQLAlchemy 和 Django ORM。目前，采用最广泛或使用最多的 ORM 库是 SQLAlchemy。

### SQLAlchemy

SQLAlchemy 由两个主要模块组成：核心模块和 ORM 模块。核心模块提供了一种 SQL 抽象，能够实现与各种数据库应用程序编程接口（DBAPI）的顺畅交互。核心模块不需要采用 ORM 即可连接和与数据库交互。ORM 模块构建在核心模块之上，为 SQLAlchemy 提供对象关系映射功能。

#### 引擎

要使用 SQLAlchemy 连接任何数据库，你需要熟悉引擎和会话这两个概念。为了连接数据库，你需要创建一个引擎。引擎是 SQLAlchemy 应用程序尝试连接并与数据库通信的起点。`create_engine` 对象提供了一种创建新数据库连接的方法。

以下是连接 PostgreSQL 数据库的示例：

```python
from sqlalchemy import URL
from sqlalchemy import create_engine

databaseURL = URL.create(
    "mysql+mysqlconnect",
    username="your_username_here",
    password="your_password_here",
    host="localhost",
    database="testdatabase",
)
engine = create_engine(databaseURL)
```

#### 会话

另一个概念是会话。顾名思义，会话在建立连接后，会与数据库建立“对话”。会话为你的所有查询和事务（提交、回滚等）创建工作空间。如果你正在构建一个较大的应用程序，建议使用 `sessionmaker` 类来创建会话。

以下是关于如何使用 `sessionmaker` 创建新会话的示例：

```python
from sqlalchemy import URL
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker

databaseURL = URL.create(
    "mysql+mysqlconnect",
    username="your_username_here",
    password="your_password_here",
    host="localhost",
    database="testdatabase",
)
engine = create_engine(databaseURL)
sampleSession = sessionmaker(autoflush=False, bind=engine)
# 此处进行查询和事务处理
sampleSession.close()
```

参数 `bind` 是将引擎与会话关联的一种方式。参数 `autoflush` 会在调用查询以向数据库发送 SQL 语句时，或在使用会话类的某些方法时，自动刷新或提交数据库事务。

#### 查询 API

SQLAlchemy 还提供了一个查询 API，支持执行各种操作以辅助数据检索和操作。这些方法包括但不限于排序、过滤、连接、从表中获取数据等。

让我们看一些 SQL 到 ORM 的示例。假设我们在关系数据库中有一个名为 `students` 的表。

SQL 查询：

```sql
Select * from Students
```

将转换为 SQLAlchemy ORM 查询：

```python
Students.select()
```

让我们看一个稍微详细的查询，我们选择检索在 2024 年秋季选修了 Chemistry 101 课程的学生...

# SQL 查询转换

```sql
Select
*
from
students
where
term = "Fall2024" and
course = "Chemistry101" and
plan = "something"
fetch first 25 rows only
```

将转换为

```python
students \
.select() \
.filter( \
students.has( \
term="Fall2024", \
course="Chemistry101", \
plan="something" \
)
).fetchmany(25)
```

# Alembic

此外，了解数据库迁移和`Alembic`库也很重要。数据库迁移并不一定意味着在数据库之间移动大量数据。相反，数据库迁移是关于创建和维护与应用程序相关的当前及不断演进的模式。`Alembic`是一个Python库，用于帮助在`SQLAlchemy`应用程序中管理和维护这些不断演进的模式。

# 构建 REST 数据 API

让我们利用`FastAPI`和`SQLAlchemy`构建一个RESTful数据API，以数据即服务（DaaS）的形式交付数据。数据即服务是一种商业概念和模型，描述按需交付高质量数据，无论提供者和消费者在地理上相隔多远。在此示例中，我们还将整合数据验证库`Pydantic`，以说明在构建RESTful数据API时如何验证数据并将其发送到下游。我们利用RESTful架构来确保无状态性、可扩展性和通过Web交付数据的简洁性。

首先，让我们看看如何使用`SQLAlchemy`初始化数据库连接：

```python
from sqlalchemy import create_engine
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.orm import sessionmaker
SQLAlchemy_DB_URL = "mysql+mysqlconnector://newscott:Tiger$$$!!!9999@localhost/stu"
engine = create_engine(SQLAlchemy_DB_URL)
SessionLocal = sessionmaker(autocommit=False,
autoflush=False,
bind=engine)
Base = declarative_base()
```

接下来，我们将定义与数据库中相应表对应的类：

```python
from sqlalchemy import Column, Integer, String
from database import Base
class Student(Base):
__tablename__ = "students"
id = Column(Integer, primary_key=True)
name = Column(String, unique=True, index=False)
course = Column(String, unique=False, index=True)
```

随后将使用`Pydantic`库创建一个数据验证类。代码如下所示：

```python
import pydantic as pyd
class NewStudent(pyd.BaseModel):
id: int
name: str
course: str
```

现在让我们开发各种将成为API功能的函数。在此情况下，我们可以获取所有学生的列表、按姓名或ID获取学生详情，或创建新学生并提交事务：

```python
from sqlalchemy.orm import Session
import model, pydanticSchema
def get_AllStudents(db: Session, offset: int = 0, limit: int = 10):
return db.query(model.Student).offset(offset).limit(limit).all()
def get_student_by_name(db: Session, name: str):
return db.query(model.Student).filter(model.Student.name == name).first()
def get_student_by_id(db: Session, id: int):
return db.query(model.Student).filter(model.Student.id == id).first()
def new_student(db: Session, student: pydanticSchema.NewStudent):
#id = 随机数生成
db_user = model.Student(id = id, name = model.Student.name, email=model.Student.email)
db.add(db_user)
db.commit()
db.refresh(db_user)
```

当我们拥有所有功能和组件后，现在来创建`FastAPI`应用程序：

```python
from fastapi import Depends, FastAPI, HTTPException
from sqlalchemy.orm import Session
from database import SessionLocal, engine
import model
from crud import get_AllStudents, get_student_by_name, get_student_by_id, new_student
from pydanticSchema import NewStudent
model.Base.metadata.create_all(bind=engine)
app = FastAPI()
```

## 依赖项

```python
def get_db():
db = SessionLocal()
try:
yield db
finally:
db.close()
```

```python
@app.get("/student/{name}")
def get_stud_by_name(student: NewStudent,
                     db: Session = Depends(get_db)):
try:
byname = get_student_by_name(db, name=student.name)
return byname
except Exception:
raise HTTPException(status_code=400)
```

```python
@app.get("/students/")
def get_allstudents(db: Session = Depends(get_db)):
try:
allstudents = get_AllStudents(db)
return allstudents
except Exception:
raise HTTPException(status_code=400)
```

```python
@app.get("/student/{id}")
def get_stud_by_id(student: NewStudent,
                   db: Session = Depends(get_db)):
try:
by_id = get_student_by_id(db, id=student.id)
return by_id
except Exception:
raise HTTPException(status_code=400)
```

```python
@app.post("/students/")
def new_student(student: NewStudent,
                db: Session = Depends(get_db)):
student_id = get_student_by_id(db, id=student.id)
if student_id:
raise HTTPException(status_code=400)
return new_student(db, student=student)
```

# 功能与代码准备就绪后，我们来测试一下 RESTful 数据 API 应用。

只需运行：

```bash
uvicorn main:app —reload
```

打开浏览器标签页，输入`http://127.0.0.1:8000/students/`即可获取所有学生信息。效果如下：

![](img/615530_1_En_10_Fig8_HTML.jpg)

浏览器读取FastAPI应用返回的JSON响应的截图。响应中包含三名学生的信息，包括姓名、课程和ID。

**图 10-8** 该FastAPI应用的GET请求示意图

## FastAPI中的中间件

**中间件**是位于提供方和消费方之间的一小段函数或程序集。它同时处理请求和响应流程。在每个请求被发送到服务器处理之前，中间件可以配置为在发送到服务器之前执行特定操作。同样，它也可以接收响应，执行某些操作，然后将响应发送给消费方。中间件应用的常见示例包括执行身份验证，以及记录请求和响应的元数据日志。中间件还有助于限制来自特定消费方的请求数量。

让我们看一个简单的中间件实现示例。假设我们部署了一个向公众开放的机器学习服务。我们希望实现一个速率限制中间件，将来自特定消费方的请求数量限制为每小时25次。顺便提一下，这是一个很好的`async`/`await`实现示例。

## 使用FastAPI的机器学习API端点

让我们考虑一个简单的预测（分类）花卉种类的示例。我们将获取鸢尾花数据集。鸢尾花数据集包含三种鸢尾花，每种有50个样本，广泛用于测试机器学习方法（来源：[`https://archive.ics.uci.edu/dataset/53/iris`](https://archive.ics.uci.edu/dataset/53/iris)）。

以下是一个基于该数据集的简单模型。我们首先构建一个简单的机器学习模型。我们使用R. A. Fisher于1936年发表的研究论文《分类问题中多重测量的应用》中使用的鸢尾花数据集。该数据集采用知识共享许可，可在加州大学欧文分校维护的机器学习库中找到。该数据集包含鸢尾属植物中三种花卉的萼片和花瓣（花的部分）的测量值以及相应物种的名称。

我们将植物测量值提取为因变量，将植物名称提取为自变量，以便根据一组测量值识别鸢尾植物的类型。该模型存储为pickle文件，以便我们可以在FastAPI应用程序中导入它：

> **注意**
>
> 请注意，重点在于为我们的机器学习应用演示FastAPI的实现。

```python
import pandas as pd
from sklearn.model_selection import train_test_split
from sklearn.linear_model import LogisticRegression
from sklearn.metrics import accuracy_score, confusion_matrix
import pickle
import numpy as np

df = pd.read_csv("//iris.csv")
x = df.iloc[:,:4]
y = df.iloc[:,4]
x_train, x_test, y_train, y_test = train_test_split(x, y, random_state=0)

model = LogisticRegression()
model.fit(x_train, y_train)
y_pred = model.predict(x_test)

# print(confusion_matrix(y_test, y_pred))
# print(accuracy_score(y_test, y_pred)*100)

export_model = pickle.dump(model, open('irismodel1.pkl', 'wb'))

input_data_reshaped = np.asarray(input_data).reshape(1, -1)
prediction = model1.predict(input_data_reshaped)
```

一旦我们有了模型pickle文件，就可以继续在FastAPI中构建我们的应用程序，并添加一个自定义中间件，该中间件在请求达到一定数量后限制对API的访问。我们首先导入必要的类和方法，然后实例化`FastAPI`，加载模型，并创建一个字典来存储IP地址和访问次数。接着我们定义中间件函数，在该函数中从请求类获取IP地址，并为每个IP地址增加访问次数。中间件的编码确保如果在给定小时内访问次数超过5次，则会抛出异常：

> **注意**
>
> 理解中间件代码仅用于演示目的非常重要。我还建议研究一个名为“SlowAPI”的Python库来实现速率限制器。此外，也建议使用像`Redis`这样的内存存储。

```python
from pydantic import BaseModel
import pickle
import json
from fastapi import FastAPI, Request
from datetime import datetime, timedelta

app = FastAPI()
model = pickle.load(open('irismodel1.pkl', 'rb'))

# 为 IP 地址创建新的注册表
registry: dict[str, (int, datetime)] = {}

@app.middleware("http")
async def rateThreshold(request: Request, call_next):
    ip_address = request.client.host
    rightnow = datetime.now()
    if ip_address in registry:
        hits, timestamp = registry[ip_address]
        if rightnow - timestamp > timedelta(hours=1):
            hits = 0
    else:
        hits = 0
    hits += 1
    if hits > 5:
        raise Exception("too many requests")
    registry[ip_address] = (hits, rightnow)
    response = await call_next(request)
    return response

class ModelInput(BaseModel):
    SLength: float
    SWidth: float
    PLength: float
    PWidth: float

@app.post("/predict/")
def predict(modelparam: ModelInput):
    modelparameters = json.loads(modelparam.json())
    sl = modelparameters['SLength']
    sw = modelparameters['SWidth']
    pl = modelparameters['PLength']
    pw = modelparameters['PWidth']
    predictionclass = model.predict([[sl, sw, pl, pw]])
    return predictionclass[0]
```

一旦我们有了应用程序，就可以通过以下方式启动：

```
uvicorn MLPipeline:app —reload
```

这将从终端启动应用程序。

要访问或测试API，建议打开一个浏览器标签页，并输入FastAPI文档链接：

`http://127.0.0.1:8000/docs#/default/predict_predict__post`

或者直接输入：

`http://127.0.0.1:8000/docs`

将会显示：

![](img/615530_1_En_10_Fig9_HTML.jpg)

一张Swagger UI界面的截图，显示了FastAPI应用程序中一个POST API端点的详细信息。该端点用于进行预测。请求体需要一个具有特定键和值的JSON对象。

**图 10-9** 机器学习模型API的POST请求

您可以开始输入值，让系统对鸢尾花植物进行分类（预测）。效果如下：

![](img/615530_1_En_10_Fig10_HTML.jpg)

一张Swagger UI界面的截图，显示了POST API端点的详细信息。该端点用于进行预测。用户已输入一个JSON请求体，其中包含`SLength`、`SWidth`、`PLength`和`PWidth`的值。响应体将预测结果显示为Iris-versicolor。

**图 10-10** FastAPI计算输入参数的预测结果并返回响应体

### 结论

# API（应用程序编程接口）

API（应用程序编程接口）是消费数据分析和机器学习服务日益增长的方式之一。基于 API 的开发和部署本身就是一个广阔的领域；对于机器学习工程师或数据工程师来说，设置和构建一个 API 是很好的实践。在本章中，我们探讨了 API 的概念和架构。我们更详细地研究了 `FastAPI` 架构，并查看了各种示例。我们转向了数据库集成，构建了一个数据服务 REST API。我们还研究了如何将机器学习模型部署为 REST API。您不仅掌握了 `FastAPI` 的核心概念，还获得了一套弥合复杂工程与服务交付之间差距的技能。总的来说，我希望这能极大地帮助您理解如何使用 `FastAPI` 来设计和部署数据管道。

# 11. 工作流管理与编排入门

## 引言

组织越来越多地利用数据，创建更多的数据团队，并执行各种数据项目。除了构建和部署产品与服务外，公司还越来越多地创建更多的数据团队，并执行从数据发现到获取价值，再到确保最佳安全和隐私数据实践的各种数据项目。

数据项目可以基于 IT 产品、IT 服务、共享服务以及组织中的其他任务和活动。根据数据项目的性质和类型，数据管道可能会变得复杂和精密。有效管理这些复杂任务，确保准确编排和管理工作流以产生预期结果，这一点至关重要。

每个数据项目都需要透明度和可观测性，以了解项目做什么、如何运作、从哪些系统获取数据以及向哪些系统提供数据。每个数据作业或数据管道都足够重要，以至于在作业失败时，需要重新启动作业并对导致失败的任何系统进行故障排除。随着大数据革命，组织越来越多地寻求利用各种机器生成的数据。随着数据管道数量的增加，管理它们又增加了一层复杂性。

在本章结束时，您将学习到：

*   工作流管理的基础知识及其对工程数据管道的重要性
*   工作流编排的概念及其如何优化数据管道操作
*   `cron` 调度器及其在任务自动化中的作用
*   创建、监控和管理 `cron` 作业及 `cron` 日志记录
*   `cron` 日志记录以及调度和编排 `cron` 作业的实际应用

## 工作流编排简介

### 工作流

工作流编排在 IT 和数据革命之前就已经存在了很长时间。工作流本身可以意味着一组按特定顺序执行以生产产品的任务或活动。每个任务都有一组特定的输入、处理和输出。给定任务的输出作为后续任务的输入。反之，给定任务的输入包含前一个任务的输出。这样，任务必须按照特定顺序遵循一组特定的步骤。

在任何给定的数据工程或机器学习作业的上下文中，工作流基本上是一组数据管道，包含需要按特定顺序执行的任务和活动。给定管道的输入依赖于前一个管道的输出。

为了进一步说明这一点，让我们以 Acme 面包公司为例，这是一家虚构的面包制造厂。是的，这是对《乐一通》的引用。假设制作面包的基本过程包括以下需要按特定顺序执行的步骤：

1.  **原料混合**：将面团、酵母、水和盐按特定、测量的比例在大型搅拌机中混合在一起。
2.  **发酵**：将揉好的混合物分成小份，放置在室温下，使面团变得更蓬松并膨胀（变大）。
3.  **烘烤**：在受控的温度和压力参数下，在烤箱中烘烤面团，并将湿度控制在适当水平，以获得所需的面包稠度。
4.  **质量控制**：确保生产的面包均匀一致，符合质量标准。
5.  **切片和包装**：将制作好的面包切片，用纸袋包装，并印上烘烤时间和安全食用期限。
6.  **分销**：将用纸袋包装好的面包再次装入箱子，并装载到各种卡车上。然后，这些卡车将面包箱运送到包括杂货店和餐馆在内的各个目的地。

这可能看起来像这样：

![一个装配线工作流的图示，包括原料混合、发酵、烘烤、质量控制、切片和包装以及分销。](img/615530_1_En_11_Fig1_HTML.jpg)

图 11-1 装配线工作流示例

这是工作流最基本的例子。在前面列出的步骤中，不能互换给定的步骤。这些步骤必须完全按照相同的顺序执行。此外，给定步骤的输出构成下一步的输入。因此，下一步的输出成为后续步骤的输入，依此类推。

### ETL 和 ELT 数据管道工作流

烘焙一条面包的过程涉及一组需要按特定顺序执行的步骤。类似地，在数据工程或机器学习项目的上下文中，存在一组需要按特定顺序执行的函数/作业/任务。编写这些数据管道的方法有很多种；其中一种最常见和众所周知的类型称为 ETL 工作流。ETL 代表提取、转换和加载。在这种类型的工作流中，数据被提取并放置在临时区域，应用必要的转换，然后将最终的工作产品传输到另一个系统，用于下游处理和使用。

一个简单的例子是工资处理数据管道。假设工资数据从像 `PeopleSoft` 这样的 ERP 系统中提取，其他费用数据从其他源系统中提取；然后，这些数据在临时区域中被清理、整理并整合成一个统一的数据集。下一阶段是转换阶段，在此阶段计算净工资、税金和扣除额等关键指标。然后，生成的数据集被加载到工资系统中，用于支付处理和报告。这可能看起来像这样：

![一个流程图，包括：1. 提取，包括 ERP 软件、数据库拉取和源数据库；2. 转换，包括数据转换区域、集成区域、验证和测试；3. 加载，包括目标系统和后处理任务。](img/615530_1_En_11_Fig2_HTML.jpg)

图 11-2 工资处理管道的示例 ETL 架构

ETL 的一个近期变体被称为 ELT。ELT 代表提取、加载和转换。在这种情况下，来自各种来源的数据被提取并加载到目标系统中。然后，直接在数据上应用各种转换和业务规则。在 ELT 工作流中，可能有也可能没有临时层。ELT 工作流在云数据存储的情况下更为常见。当处理多样化的数据类型时，ELT 更合适。大多数云提供商提供无服务器数据湖/仓库解决方案来服务 ELT 管道，使其具有成本效益且易于维护。

让我们举一个 ELT 数据管道可能是什么样子的例子。假设关系数据库数据来自一个关于各种产品的库存系统，以及仓库日志传感器读数的 JSON 数据，以及其他一些数据。以频繁的时间间隔获取这些数据将构成提取阶段。对于加载阶段，想象我们有一个 `Google BigQuery`，我们将所有数据转储到其中。转换阶段是数据被清理、整理，并要么集成到统一数据集中，要么加载到维度表中以供进一步分析。

这可能看起来像这样：

![一个流程图，包括：1. 提取，包括开始、MongoDB 和关系数据库，从 Mongo 和关系数据库提取；2. 转换，包括目标受众、统一数据集、在 Google BigQuery 内转换以及数据集的目的；3. 加载，包括加载数据到 BigQuery。](img/615530_1_En_11_Fig3_HTML.jpg)

图 11-3 库存数据处理管道的示例 ELT 架构

### 工作流配置

工作流配置是另一个需要理解的关键概念。工作流配置基本上是一组驱动工作流过程的配置参数。工作流配置对于成功交付产品或解决方案（在此例中是面包）至关重要且关键。

在这个例子中，工作流配置可能涉及以下方面：

1.  **原料混合**：每种原料的确切数量，并且测量使用相同的标度。
2.  **发酵**：发酵室的受控温度，涂抹在发酵板上的确切油量，以及一个自动计时器，一旦发酵完成的小时数达到，就会发出警报。
3.  **烘烤**：温度传感器的校准，需要在烤箱中停留的总时间，以及在此期间烤箱的确切温度（前 10 分钟，170°C；第二个 10 分钟，200°C；冷却 10 分钟，150°C）。
4.  **质量控制**：每生产 1,000 条面包，缺陷数量应减少到 3 条以下，等等。
5.  **切片和包装**：切片机应配置为特定尺寸，以便面包切割均匀；包装纸应清洁且无缺陷。
6.  **分销**：各种类型的箱子以及它们能装多少条面包；卡车内的温度；交货时间应在 x 小时内。

前面的工作流配置有助于组织简化生产流程，改进和建立均匀性和一致性，规划采购和人力需求，以及优化资源利用。这种级别的详细配置有助于理解生产优质面包所需遵循的步骤。

类似地，在数据工程或机器学习管道的情况下，也存在工作流配置参数。其中一些可能如下：

1.  如果作业被安排定期运行，那么该作业可能需要最新或新鲜的数据；因此，最近一次源系统刷新的时间和日期至关重要。
2.  组织开始采用数据治理最佳实践。其中一种实践是创建一个服务帐户或专门的用户帐户（取决于数据车间的大小、性质和复杂性）。必须配置适当的凭据才能访问正确的数据，这对于构建更好的数据产品至关重要。
3.  一个数据管道可能包含多个转换和业务规则。这些转换本质上是应用于从系统获取的数据的函数。这种转换逻辑和业务规则也是工作流配置。
4.  在尝试将最终数据产品加载到适当的系统时，可能存在强制将数据加载到模式中的约束。模式只是一个定义了所有字段及其数据类型的表结构。模式也可以是另一个工作流配置参数。

## 工作流编排

工作流编排指的是对整个过程的集中化管理和控制。工作流编排涉及规划、调度和控制生产过程的每个步骤及其各自的工作流配置参数，以确保所有任务都在定义的控件内并按适当顺序执行。工作流编排优化了资源利用，有助于规划原材料的采购，并在开发的每个产品中建立高质量和一致性，最终实现组织的无缝运作。在此上下文中，工作流编排将指：

1.  定义整个工作流：定义过程的每个步骤，并内置配置参数。
2.  调度：将调度已定义的工作流，其中一个过程将在装配链中的过程完成后启动。
3.  一个中央控制室，监督从原料混合到将面包包装到箱子中的整个车间，叉车操作员会将箱子放到卡车上。
4.  自动化：计算机指定并控制每个工业设备（如烤箱、发酵室等）的配置参数。

# Cron 作业调度器简介

## 工作流编排

- **错误处理和路由**：如果装配链中的机器未能处理其任务，工作流编排系统将为特定设备实施重试（自动重启机器），并触发某些事件以避免瓶颈。
- **与外部系统集成**：工作流编排跟踪已发货的箱子数量、已生产的面包数量、哪个市场集群收到了多少箱子、消耗的原材料单位数量、机器开启的小时数以及其他此类数据，并将它们提供给外部系统。

工作流编排使工厂车间能够以最小化错误、高效和可预测的方式运行。

类似地，在数据管道的上下文中，一些工作流编排可以看作如下：

1.  定义整个工作流，其中数据工程管道的每个步骤都配置了适当的参数以成功执行。
2.  如果任务无法连接到源系统或目标系统，则有一种机制可以在几秒钟后自动重新执行函数，以期连接到该系统。
3.  内置或良好集成的机密管理，以便开发或生产数据管道利用适当的凭据来执行作业。
4.  自动记录作业执行的事件，这些事件以后可用于数据分析，特别是识别某些类型事件中的任何模式。
5.  集中式监控和可观测性，以便在一个界面下查看、管理、监控和控制所有数据管道。

> **注意**
> 数据管道与制造车间中的任务或过程非常相似。数据管道将有一个输入（原始数据被获取的地方）、一个转换层（执行各种操作并整合业务逻辑）和一个输出（最终产品被生产出来并发送到下一个流程的地方）。数据管道之所以被称为数据管道，是因为一个过程的输入基本上是另一个过程的输出，而该给定作业的输出又成为另一个数据过程的输入。通常，数据管道本质上是瞬态的。

## 概念

cron 调度器的核心是所谓的 cron 守护进程。该进程在系统启动时在后台运行。cron 守护进程执行在名为 `crontab` 的文件中提到的命令。有两个文件指定用户及其对 cron 调度器环境的访问权限。

这些文件可能位于：

```
/etc/cron.allow
/etc/cron.deny
/etc/cron.d/cron.allow
/etc/cron.d/cron.deny
```

> **注意**
> 对于 Ubuntu 和 Debian 操作系统，`cron.allow` 和 `cron.deny` 文件不存在，这意味着默认情况下，Ubuntu 和 Debian 操作系统的所有用户都可以访问 cron 调度器。

cron 调度器环境的最基本单元是 cron 作业。一个 cron 作业代表一个被调度执行的任务或作业。一个 cron 作业是 `crontab` 中的一行，代表一个 shell 命令的一次执行。

## Crontab 文件

Crontab 是 `cron table` 的缩写。Crontab 是一个维护用户 `crontab` 文件的实用程序。`crontab` 文件是为给定的 Linux 用户自动生成的。无论是否有任务或作业被调度运行，cron 守护进程都会运行这个 `crontab` 文件。`crontab` 文件可以被视为一个注册表，其中包含已调度作业的列表。`crontab` 文件属于 `root` 或超级用户。您可以成为超级用户来创建 `crontab` 文件或编辑现有的 `crontab` 文件。`crontab` 文件的默认位置是：

```
/var/spool/cron/crontabs/
```

要创建一个新的 `crontab` 文件，请在命令行中使用 `crontab -e`。效果如下：

![img/615530_1_En_11_Fig4_HTML.jpg](img/615530_1_En_11_Fig4_HTML.jpg)

一个软件窗口，带有用于创建新 `crontab` 文件的命令行。

图 11-4 创建新的 `crontab` 文件

您将看到以下模板，用于编写您自己的 cron 作业：

![img/615530_1_En_11_Fig5_HTML.jpg](img/615530_1_En_11_Fig5_HTML.jpg)

一个名为 GNU Nano 6.2 的软件窗口，带有用于向 `crontab` 文件写入任务的命令行。

图 11-5 向 `crontab` 写入任务

要列出所有当前的 `crontab` 文件，您可以尝试以下命令：

```
ls -l /var/spool/cron/crontabs/
```

让我们看一下 `crontab` 文件的语法。

`crontab` 文件中的每一行代表一个给定 cron 作业的条目。一个 `crontab` 文件条目包含六个字段，由空格分隔。格式如下：

```
minute hour day-of-month month weekday command
```

*   `minute` 列表示作业应该运行的具体分钟。它只能包含数值。值可以是 0 到 59 之间的任何数字。
*   `hour` 列表示作业应该运行的小时，只接受数值。值可以是 0 到 23 之间的任何数字。
*   `day-of-month` 列表示给定日历月中的某一天。如您所知，月份包含 28、29、30 或 31 天，具体取决于月份以及是否是闰年。您可以指定 1 到 31 之间的数值。
*   `month` 列表示一个日历月，可以取 1 到 12 之间的数值。这些数值分别代表从一月到十二月的日历月。
*   `weekday` 列表示一周中的某一天，从星期日到星期六。它可以取 0 到 6 之间的任何值，其中 0 代表星期日，6 代表星期六。
*   `shell command` 是发出需要运行的命令的地方。

> **注意**
> 在 `crontab` 文件中，我们有一个星号操作符。`*` 操作符可以应用于 `crontab` 条目的前五列。当在相应的列中提及 `*` 时，意味着它可以在给定允许范围内的所有间隔上运行。以下是一个示例：
> `15 6 * * * /user/parallels/bin/helloworld`
> 上述 `crontab` 条目将每天早上 6:15 运行 `hello world` 脚本。具体来说，该脚本将在每个月的每一天、每个月以及每个工作日运行。

## Cron 日志记录

cron 守护进程在每次系统启动、执行新作业、解释命令时遇到问题或在作业执行期间遇到错误时，都会写入一条日志条目。不幸的是，日志条目不包含错误的来源。如果您使用的是基于 Debian 的操作系统（Ubuntu 等），那么您可以在此处找到日志：

```
/var/log/syslog
```

对于基于 Fedora 的发行版，日志位于：

```
/var/log/cron
```

您可以通过以下命令获取最近的 cron 日志条目：

```
grep CRON /var/log/syslog | tail -100
```

Cron 中的日志记录存储了许多关键信息。日志条目以作业启动时的日期时间戳开头，然后是工作站的主机名、该工作站的用户帐户以及执行的命令。以下是一个日志条目的示例：

```
Jan 29 15:55:07 linux CRON[1257]: (parallels) CMD (/home/parallels/helloworld.py)
```

在前面的示例中，日志条目以日期时间戳开头，然后是主机名（本例中为 `linux`），接着是主机上的用户（本例中为 `parallels`），最后是执行的命令（本例中为执行名为 `helloworld.py` 的 Python 脚本）。

## Cron 作业用法

在本节中，我们将看一些编写 cron 作业的简单示例。让我们从编写一个简单的 shell 脚本开始，并使用简单的 cron 作业脚本对其进行调度。

我们将创建一个名为 `cronexample.sh` 的 shell 脚本。在终端中输入以下内容：

```
nano cronexample.sh
```

并在 `nano` 编辑器中输入以下代码：

```bash
#!/bin/bash
echo "Hello from the world of Cron scheduling"
echo "Execution at: $(date)"
```

它应该看起来像这样：

![img/615530_1_En_11_Fig6_HTML.jpg](img/615530_1_En_11_Fig6_HTML.jpg)

一个名为 GNU Nano 6.2 的软件窗口，带有用于编写简单 shell 脚本的命令行。

图 11-6 编写一个简单的 shell 脚本

保存文件并退出编辑器。让我们为此文件提供执行权限：

```
chmod +x cronexample.sh
```

现在进入 `crontab` 以调度此 shell 脚本的执行：

```
crontab -u parallels -e
```

所有这些看起来像这样：

![img/615530_1_En_11_Fig7_HTML.jpg](img/615530_1_En_11_Fig7_HTML.jpg)

一个名为 GNU Nano 6.2 的软件窗口，带有用于调度先前编写的 shell 脚本执行的命令行。

图 11-7 调度先前编写的 shell 脚本的执行

我们得到的是：

```
* * * * * /home/parallels/Documents/cronexample.sh
```

这一行脚本将每分钟、每天、每周、每月执行该命令。

现在让我们通过另一个示例引入另一个操作符：

# Cron 调度与 Apache Airflow 工作流编排

我们有一个 `/` 操作符，称为步长值。在这种情况下，我们为分钟字段合并了步长值。这个示例意味着无论小时、天、月、日期等如何，每六分钟运行一次以下脚本。
```
*/6 * * * * /home/parallels/Documents/cronexample.sh
```

`/` 步长操作符也可以扩展到其他字段，从而实现更复杂的应用。

以下是一个示例：
```
*/24 */4 * * * /home/parallels/Documents/cronexample.sh
```
这将使 cron 调度器在每个第 4 小时的第 24 分钟运行此脚本。以下是一个示例调度：
```
first execution at 2024-02-09 12:00:00
followed by execution at 2024-02-09 12:24:00
followed by execution at 2024-02-09 12:48:00
followed by execution at 2024-02-09 16:00:00
followed by execution at 2024-02-09 16:24:00
```

让我们看看为给定字段提供一个列表。以下是一个示例：
```
*/30 */18 * * */1,4,7 /home/parallels/Documents/cronexample.sh
```
在前面的示例中，我们在每个第 18 小时的每半小时，在星期一、星期四和星期日运行 `cronexample.sh`。以下是调度可能的样子：
```
first execution at 2024-02-09 18:00:00
followed by execution at 2024-02-09 18:30:00
followed by execution at 2024-02-10 00:00:00
followed by execution at 2024-02-10 00:30:00
followed by execution at 2024-02-10 18:00:00
```
第一次执行在 18:00 开始，随后在同一小时内间隔 30 分钟。后续执行在第二天中午进行，提供了 18 小时的时间窗口。

> 注意：对于任何给定的调度，时钟从 12:00:00 开始。

我们也可以在 crontab 中创建一个范围。以下是一个示例：
```
0 22 * */6 1-5 /home/parallels/Documents/cronexample.sh
```
在此示例中，我们已将此脚本调度为在晚上 10 点运行，每周一至周五，每六个月运行一次。

以下是调度可能的样子：
```
first execution at 2024-07-01 22:00:00
followed by execution at 2024-07-02 22:00:00
followed by execution at 2024-07-03 22:00:00
followed by execution at 2024-07-04 22:00:00
followed by execution at 2024-07-05 22:00:00
```

现在，让我们尝试组合范围操作符和步长值操作符：
```
36 9-17/2 * * * /home/parallels/Documents/cronexample.sh
```
在此示例中，我们已将脚本调度为在给定小时的第 36 分钟运行，在上午 9 点到下午 5 点之间每 2 小时运行一次。

效果如下：
```
first execution at 2024-02-09 13:36:00
followed by execution at 2024-02-09 15:36:00
followed by execution at 2024-02-09 17:36:00
followed by execution at 2024-02-10 09:36:00
followed by execution at 2024-02-10 11:36:00
```

让我们看一个使用单词字符串而不是数字的示例。以下是一个示例：
```
0 9-17/3 */12 JAN,MAR,MAY,JUL,OCT,NOV,DEC MON /home/parallels/Documents/cronexample.sh
```
这个 crontab 脚本将在每个月的第 12 天执行 shell 脚本，如果该天恰好是一月、三月、五月、七月、十月、十一月和十二月的星期一，并且在执行脚本的那天，它将在上午 9 点到下午 5 点之间每三个小时执行一次。

以下是一个示例调度：
```
first execution at 2024-03-25 09:00:00
followed by execution at 2024-03-25 12:00:00
followed by execution at 2024-03-25 15:00:00
followed by execution at 2024-05-13 09:00:00
followed by execution at 2024-05-13 12:00:00
```

让我们再看几个操作符：
```
*/15 3 L 6-9 FRI#4 /home/parallels/Documents/cronexample.sh
```
注意使用了字符 `L` 和井号 `#`。`L` 字符表示给定字段的最后一个值。`#` 哈希操作符表示该月的第 n 天。在这种情况下，脚本被调度为每 15 分钟执行一次，在凌晨 3 点到 3:59 之间，在该月的最后一天执行，如果该最后一天恰好是该月的第四个星期五，并且在六月、八月和九月这些月份中。

## Cron 调度器应用

在建立基础设施和工程数据系统的世界中，有许多应用涉及使用 cron 调度器。让我们详细看看其中一些应用。

### 数据库备份
数据库备份是创建组织主文件和事务表副本的过程。这是一项关键操作，因为数据是最有价值的资产，并且日常业务和运营都需要数据。根据备份的类型和频率，存在各种类型的数据库备份方法。有差异备份、增量备份和完整备份。

让我们看一个简单的示例，其中调度了一个执行数据库备份操作的 shell 脚本：
```
0 2 * * * /home/parallels/mysql_backup.sh
```
此脚本将每天凌晨 2 点运行，执行 shell 脚本文件中提到的指令。

### 数据处理
许多组织都在使用各种生成数据的系统。组织内的数据团队通常会每天生成报告或执行某些数据的文件传输。这些在特定源系统中出现的数据处理任务可以使用 cron 调度器实用程序自动化。

让我们看看调度一个执行数据处理或每天生成报告的批处理作业：
```
45 3 * * * /home/parallels/Documents/data_processing.sh
```
上述 cron 调度器将每天凌晨 3:45 处理批处理操作，从而准备报告或执行文件传输到另一个位置。

### 电子邮件通知
电子邮件通知是向一个或多个人发送特定消息的过程。在数据系统和工程领域，可以设置接收关于已处理任务状态（备份数据库、准备数据集、数据挖掘等）的电子邮件通知。存在多个库用于设置电子邮件服务器配置并将输出消息发送到适当的电子邮件。

让我们看一个简单的示例：
```
45 7 * * MON-FRI /home/parallels/email_notification.sh
```
上述命令将在周一至周五每天早上 7:45 发送关于各种任务的电子邮件通知。

## Cron 替代方案

cron 作业调度器有几个替代方案；其中一些替代方案仍然基于早期版本的 cron 调度器实用程序。但有一个在管理员社区中被广泛使用和尊重的替代方案，称为 `systemd timer`。Systemd 是一个软件套件，提供各种组件来管理 Linux 操作系统中的系统进程。`systemd timer` 是 systemd 的组件之一，提供作业调度机制。作业可以被调度在特定的时间间隔运行，或者基于某个事件运行。

## 结论

工作流管理和编排是数据工程中的基本组成部分。在本章中，我们研究了 crontab 和 cron 调度，以及如何使用它们来编排复杂的作业和自动化日常任务。看似简单的概念，却是自动化重复性任务的强大调度工具。您在本章中学到的基础知识和关键概念，在您研究现代数据编排平台（我们将在接下来的几章中看到）时可能会对您有所帮助。高效的工作流管理、调度和编排是释放数据基础设施全部潜力的关键。

## 12. 使用 Apache Airflow 编排数据工程管道

### 简介

Apache Airflow 是一个开源的工作流编排平台。它使工程师能够将有向无环图（DAG）形式的工作流进行定义、调度和编排。Airflow 支持与流行的数据存储和处理系统进行广泛集成，适用于从 ETL（提取、转换、加载）流程到复杂的机器学习管道的各种用例。凭借其可扩展和可扩展的架构、出色的图形用户界面以及丰富的插件和扩展集合，Airflow 被广泛用于提高生产力并确保数据的可靠性和质量。我们将深入探讨 Apache Airflow 的架构、各种组件以及使其成为强大工作流编排器的关键概念。

在本章结束时，您将学习到：

# Apache Airflow 简介

*   Apache Airflow 的基础知识及其架构
*   在开发环境中设置、安装和配置 Airflow
*   关键概念，如 DAG、操作符和宏
*   使用 Xcom 进行任务间通信，以及使用参数和变量进行灵活的工作流配置
*   使用触发器和任务流控制复杂工作流

Apache Airflow 是一个开源的工作流管理和编排平台。Airflow 起源于大约十年前（2014 年）的 Airbnb。创建 Airflow 是为了以编程方式调度和编排工作流。

## 设置与安装

以下是我们如何开始使用 Apache Airflow。我正在使用的版本是 Apache Airflow 2.8.1。让我们从创建一个新的虚拟环境开始：

```
python -m venv airflow-dev
```

以下是在 Linux 中激活虚拟环境的方法：

```
source airflow-dev/bin/activate
```

如果您使用的是 MS Windows，请使用：

```
airflow-dev/Scripts/activate
```

在您的开发工作区中的某个位置创建一个新文件夹：

```
mkdir airflow_sandbox
```

现在，复制文件夹路径并设置 `AIRFLOW_HOME` 环境变量。确保在系统中安装 Apache Airflow 之前执行此操作：

```
export AIRFLOW_HOME=/home/parallels/development/airflow_sandbox
```

以下说明摘自 Apache Airflow 网站。我们将使用以下命令获取 Python 版本：

```
PYTHON_VERSION="$(python --version | cut -d " " -f 2 | cut -d "." -f 1-2)"
```

如前所述，我们希望安装 Apache Airflow 版本 2.8.1。以下是执行此步骤的命令：

```
pip install "apache-airflow==2.8.1" \
—-constraint "https://raw.githubusercontent.com/apache/airflow/constraints-2.8.1/constraints-${PYTHON_VERSION}.txt"
```

此命令将开始安装 Apache Airflow 及其依赖项。效果如下：

![](img/615530_1_En_12_Fig1_HTML.jpg)

带有安装 Apache Airflow 及其依赖项的命令行的文本图形。

图 12-1 Pip 安装 Apache Airflow

现在，让我们初始化后端数据库，创建一个管理员用户，并通过输入以下内容启动所有组件：

```
airflow standalone
```

![](img/615530_1_En_12_Fig2_HTML.jpg)

带有初始化后端数据库、创建管理员用户和启动所有组件的命令行的文本图形。

图 12-2 初始化 Apache Airflow 的后端配置

完成此步骤后，让我们通过以下命令初始化 Apache Airflow Web 服务器：

```
airflow webserver —D
```

此命令将启动 Airflow Web 服务器作为后台进程。效果如下：

![](img/615530_1_En_12_Fig3_HTML.jpg)

带有启动 Airflow Web 服务器作为后台进程的命令行的文本图形。

图 12-3 Apache Airflow 初始化

一旦 Web 服务器运行起来，让我们尝试运行调度器。以下命令将启动 Airflow 调度器：

```
airflow scheduler -D
```

您可以打开浏览器并输入以下 URL 来访问 Apache Airflow 图形用户界面。URL 是：`http://localhost:8080`

对您来说可能如下所示：

![](img/615530_1_En_12_Fig4_HTML.jpg)

一个 Airflow 窗口的截图，收集用户名和密码等登录信息。下方有一个名为“登录”的按钮。

图 12-4 Apache Airflow GUI

当您运行 `airflow standalone` 命令时，它应该已经创建了一个管理员帐户，密码存储在 `AIRFLOW_HOME` 位置内的一个文本文件中。从文本文件 `standalone_admin_password.txt` 中获取密码。效果如下：

![](img/615530_1_En_12_Fig5_HTML.jpg)

带有从文本文件获取密码的命令行的文本图形。

图 12-5 从文本文件获取用户凭据

登录成功后，您可能会看到以下包含预构建作业的屏幕：

![](img/615530_1_En_12_Fig6_HTML.jpg)

一个 Airflow 窗口的截图，菜单项 DAG 已打开。下方有五个条目，包含所有者、运行次数、调度、上次运行和下次运行的详细信息。

图 12-6 Apache Airflow 主屏幕

## Airflow 架构

Airflow 为您提供了一个定义、调度和编排作业及工作流的平台。如前所述，工作流可以是一系列任务，其中给定任务的输出作为后续任务的输入。在 Apache Airflow 中，工作流由有向无环图表示。有向无环图是一个数学术语，表示由边连接的一系列节点，在其结构中任何地方都不形成闭环。在 Apache Airflow 的上下文中，有向无环图表示一系列相互连接和依赖但绝不会形成循环的任务。它们简称为 DAG。

一个 DAG 工作流将任务表示为节点；数据源和依赖关系表示为边。DAG 表示任务应执行的顺序。任务本身包含描述要做什么的程序化指令。DAG 的概念是 Apache Airflow 功能的核心。除此之外，还有一些核心组件对于 Apache Airflow 的功能至关重要。让我们详细看看这些核心特性，从我们已经熟悉的组件开始。

### Web 服务器

Web 服务器组件提供 Apache Airflow 软件的用户界面功能。正如我们在上一节中看到的，Web 服务器作为守护进程运行。安装显示为本地机器/单机独立进程。您可以以分布式进程部署 Apache Airflow，其中 Web 服务器可以拥有自己的实例并具有更高的安全参数。Web 服务器为开发人员提供了一个用户界面，用于查看当前已调度的各种作业、它们被配置运行的调度、上次运行的时间以及给定作业工作流中包含的各种任务。

### 数据库

Apache Airflow 的数据库组件包含各种作业的元数据。其中一些元数据信息包括任务或工作流的上次运行、先前运行的状态等。具有现代用户界面的 Web 服务器从此数据库中检索信息。Apache Airflow 使用 SQLAlchemy 作为其默认数据存储。对于生产实例，建议设置不同的数据库后端，如 Postgres 或 MySQL。后端数据库设置涉及为 Airflow 实例创建一个单独的数据库、一个单独的数据库用户、向 Airflow 用户授予 Airflow 数据库的所有权限，并将新创建的 Airflow 用户添加到数据库的访问控制列表中。最后，初始化新的后端数据库，并在 Airflow 配置文件中的连接字符串中使用适当的数据库驱动程序（稍后我们将对此进行更多介绍）。

### 执行器

执行器是运行给定工作流中任务的方式。执行器实际上是调度器组件的一部分。它带有几个可插拔的执行器。这些执行器提供了运行工作流的不同机制。有两种主要类型的可插拔执行器，即本地执行器和远程执行器。顾名思义，本地执行器在您的工作站（本地）上运行任务，而远程执行器则在多个工作节点环境（分布式环境）中运行这些任务。例如，`LocalExecutor` 在您的本地工作站上运行，并支持并行性和超线程。另一个值得一提的 `KubernetesExecutor`，它在 Kubernetes 集群中的单独 Pod 中运行每个任务。Pod 类似于容器，而 Kubernetes 是一个容器编排平台。您必须设置一个 Kubernetes 集群才能使用 `KubernetesExecutor`。

### 调度器

这是 Apache Airflow 架构中最重要的组件。调度器组件监控所有工作流以及每个工作流中的所有任务。一旦任务的所有底层依赖项完成，调度器就会触发任务的执行。调度器是一个需要手动启动的独立服务，正如我们在“设置与安装”部分中看到的。Apache Airflow 支持同时运行多个调度器组件以提高性能。调度器组件利用执行器来执行准备就绪的任务。

### 配置文件

Airflow 配置文件不是 Apache Airflow 架构的一部分；但是，它控制着工作方式和功能。Apache Airflow 带有一个选项，可以通过 `airflow.cfg` 文件微调各种选项。当您在您的工作站上安装 Apache Airflow 时，会创建此文件。每个可配置的属性都提供了描述和默认配置选项。您可以使用记事本或文本编辑器简单地打开 Airflow 配置文件。您也可以通过输入以下命令从命令行获取选项列表：

```
airflow config list
```

配置在命令行上的显示方式如下：

![](img/615530_1_En_12_Fig7_HTML.jpg)

带有显示配置的命令行的文本图形。

图 12-7

## Apache Airflow 配置

如果您想更改存放 DAG 的位置，那么您可以在此处指定文件夹的位置：

```
[core]
dags_folder = /user/parallels//
```

如果您想指定或更改 Airflow 打算使用的执行器，那么您可以在此处进行：

```
[core]
executor = LocalExecutor
```

如果您希望将默认的 `sqlite` 后端更改为 `MySQL` 或 `Postgres` 数据库实例，那么您可以将数据库字符串从 `sqlite` 驱动程序和凭据更改为 `mysqldriver` 或 `psycopg2` 驱动程序以及相应的凭据。您可以在此处替换数据库字符串：

```
[database]
sql_alchemy_conn = sqlite:////
```

如果您希望获得对 Apache Airflow 开发或沙箱实例的可见性，并启用查看配置的能力，则将以下参数设置为 `True`：

```
[webserver]
expose_config=False
```

一旦您进行了必要的更改，重新启动 Airflow 服务器至关重要。一个良好的生产实践是在 `systemd` 服务中创建一个服务，以便启动、停止和重新启动 Airflow 服务器。您可以在 Apache Airflow 的 GitHub 存储库中找到 `systemd` 服务配置文件。但是，对于开发实例，更容易的方法是识别进程 ID（也称为 PID），尝试终止该进程，然后使用以下命令启动 Apache Airflow 服务器：

```
airflow webserver -p 8080 -D
```

注意

`-p` 指的是指定端口，而 `-D` 是将服务器作为守护进程（通常称为后台进程）运行。

此外，您可以使用终端中的以下命令获取环境中加载的所有当前 DAG 的列表：

```
airflow dags list
```

## 一个简单的示例

让我们看一个 Apache Airflow 的简单示例，并在以下部分中检查各种组件。以下是一个简单的入门示例：

```python
from datetime import datetime
from airflow import DAG
from airflow.operators.python import PythonOperator

def say_hello():
    print("Hello, World!")

def print_time():
    now = datetime.today()
    print(now)
    return now

first_airflow_dag = DAG(
    'hello_world',
    description='A simple DAG that prints Hello, World!',
    schedule=None,
)

hello_task = PythonOperator(
    task_id='say_hello_task',
    python_callable=say_hello,
    dag=first_airflow_dag,
)

display_time = PythonOperator(
    task_id='print_time',
    python_callable=print_time,
    dag=first_airflow_dag
)

hello_task >> display_time
```

我们首先导入所需的类和方法。此代码中有两个函数：第一个函数将打印一个字符串，另一个函数将显示当前日期和时间。然后，我们定义 `DAG`，在其中指定各种详细信息，如名称、描述和调度。这些 DAG 也可以接受更复杂的配置。然后，我们为每个函数定义任务，并将它们与我们的 DAG 关联起来。最后，我们定义要执行的任务的顺序。虽然这不会在命令行中打印任何内容，但这有助于理解使用 Apache Airflow 进行工作流定义的各种部分。要让 DAG 显示在您的用户界面上，请确保将 Python 脚本放在适当的文件夹中。

注意

您必须将 Python 脚本放在 `airflow.cfg` 配置文件中引用的文件夹中，才能查看和监控您的 DAG。确保您的 `dags_folder` 属性指向包含您需要运行的脚本的适当文件夹。此外，找到名为 `expose_config` 的配置参数，并将该参数设置为 `True`。这将暴露 Web 服务器中的所有配置参数。在开发或沙箱实例中，这些可能有助于故障排除。

## Airflow DAG

有向无环图，或简称为 DAG，是工作流管理和编排系统（在本例中是 Apache Airflow）的核心。一个典型的 DAG 将包含通过各种数据库连接和依赖关系相互连接的各种任务，这样图中任何地方都不会形成循环。

以下是一个 DAG 的简单示例：

连接到数据库 ➤ 提取表 ➤ 执行转换 ➤ 加载数据集 ➤ 关闭数据库 ➤ 电子邮件通知

![](img/615530_1_En_12_Fig8_HTML.jpg)

一个 Airflow 窗口的截图，菜单项 Grid 被选中。一个名为 hello world 的 DAG 已打开，显示其详细信息、图形、甘特图和代码。

图 12-8

在 Apache Airflow GUI 中查看 DAG

这些 DAG 将定义和控制这些任务如何执行；但是，它们没有关于这些任务实际做什么的信息。在某种程度上，这些 DAG 是任务无关的。

在数据管道中声明 DAG 有三种方法。最基本和熟悉的方法是使用构造函数并将 DAG 传递给您计划使用的操作符。效果如下：

```python
my_test_airflow_dag = DAG(
    dag_id = 'hello_world',
    description='A simple DAG that prints Hello, World!',
    start_date=datetime.datetime(2024,2,5)
    schedule="@weekly",
)
EmptyOperator(task_id="my_task", dag="my_test_airflow_dag")
```

您也可以使用 Python 上下文管理器声明 DAG，并在上下文中添加操作符：

```python
with my_test_airflow_dag(
    dag_id = 'hello_world',
    description='A simple DAG that prints Hello, World!',
    start_date=datetime.datetime(2024,2,5)
    schedule="@weekly",
):
    PythonOperator(task_id="hello_world_task")
```

最后，您可以定义一个函数，在该函数中指定所有操作符；然后您可以使用装饰器将该函数转换为 DAG 生成器。效果如下：

```python
@dag(
    dag_id = 'hello_world',
    description='A simple DAG that prints Hello, World!',
    start_date=datetime.datetime(2024,2,5)
    schedule="@weekly",
)
def some_random_function():
    EmptyOperator(task_id="hello_world_task")
    PythonOperator(task_id="print_current_date")

some_random_function()
```

每当您运行一个 DAG 时，Apache Airflow 将创建一个新的 DAG 实例并运行该实例。这些被称为 DAG 运行。类似地，在运行时也会创建 DAG 内定义的任务的新实例。这些被称为任务实例。

### 任务

DAG 需要任务来运行。任务是任何给定 DAG 的核心。它们可以被视为工作单元。在运行期间，您将创建一个新的任务实例，即任务实例。这些任务实例有开始日期、结束日期和状态。任务具有状态，代表生命周期的阶段。以下是一个示例生命周期：

```
None ➤ Scheduled ➤ Queued ➤ Running ➤ Success
```

有三种类型的任务，即操作符、传感器和任务流。如前一节所述，任务使用关系相互连接。在此上下文中，任务之间的依赖关系表示为关系。以下是如何在 Apache Airflow 中表示关系的示例。假设我们有三个任务，即 `extract`、`transform` 和 `load`。`extract` 任务的输出作为 `transform` 任务的输入，`transform` 任务的输出作为 `load` 任务的输入。以下是这些任务在关系中的表示方式：

```
extract_data >> transform_data >> load_data
Task1 >> Task2 >> Task3
```

### 操作符

Apache Airflow 中的操作符代表执行特定任务的模板。Apache Airflow 拥有大量已经开发好的操作符列表。一些操作符适用于特定的云环境、数据库等。让我们看一个利用 `bash` 操作符下载平面文件并将其保存在特定目录中的简单示例。

以下是相同的代码：

## Apache Airflow 核心概念

每次您使用 Apache Airflow 调度和编排数据管道时，都必须重新初始化数据库。您可以使用以下命令执行此操作：

```
airflow db init
```

此命令将从文件夹中获取 DAG，您可以在用户界面中看到列出的 DAG。效果如下：

![](img/615530_1_En_12_Fig9_HTML.jpg)

一个 Airflow 窗口的截图，菜单项 DAGs 已打开。有一个名为 Extract task 的条目，包含所有者、运行次数、调度、上次运行、下次运行和最近任务的详细信息。

图 12-9 Apache Airflow 中活动的 DAG 列表

### 传感器

传感器也是一种操作符，但它执行一个非常独特的功能。传感器可用于创建一个事件，等待某事发生。一旦事件发生，它将触发下游作业。如果事件没有发生，它将持续等待。传感器通常用于等待文件到达文件系统、监控外部 API 和数据库，或者只是等待不同任务的完成。以下是一些常用的传感器及其在数据管道工作流中的使用方式：

*   `ExternalTaskSensor`：等待不同 DAG 中的外部任务完成。
*   `SQLSensor`：执行 SQL 查询，并等待直到查询返回结果。
*   `FileSensor`：等待文件或文档出现在给定的文件夹或文件位置。
*   `TimeSensor`：等待某个时间点。

让我们看一个传感器的简单示例。我们有一个文件传感器，它会持续等待，直到文件在指定时间内到达文件夹/文件系统。每当文件到达相应的文件夹位置时，DAG 将运行下游任务，该任务会回显文件已到达。在此上下文中，假设文件将由另一个进程放置在相应的文件夹中：

```
from datetime import datetime
from airflow.decorators import dag
from airflow.sensors.filesystem import FileSensor
from airflow.operators.bash import BashOperator

@dag(
dag_id = "file_sensor_example",
start_date=datetime(2024,2,3)
)
def wait_for_file():
    FileSensor(
        task_id="wait_for_file",
        filepath="/Users//airflow/testfile.txt",
        mode="poke",
        timeout=300,
        poke_interval=25,
    )

@dag(
dag_id = "file_sensor_example",
start_date=datetime(2024,2,3)
)
def file_notification():
    BashOperator(
        task_id="file_notification_task",
        bash_command='echo "file is available here"'
    )

wait_for_file >> file_notification
```

### 任务流

正如您在之前的章节中所看到的，在开发数据管道时，您必须开始规划任务和 DAG。如果您有主要用纯 Python 代码编写而不使用 Airflow 操作符的数据管道，那么任务流装饰器将有助于创建 DAG，而无需编写额外的操作符代码：

```
from airflow.decorators import dag, task

@dag(schedule="@daily", start_date=(2024,2,2))
def taskflow_example():
    @task(task_id="extract", retries=5)
    def extract():
        """
        Extract data from the data source.
        If there is a connection issue, attempt to
        establish the connection again
        """
        return dataset

    @task(task_id="transform")
    def transform(dataset):
        """
        Perform the necessary transformation for
        the extracted dataset
        """
        return processed_dataset

    @task(task_id="loaddata", retries=5)
    def load(processed_dataset):
        """
        Obtain the dataset and transport it to file system.
        Retry the task if there is an issue with connection.
        """
        pass

    extract_data = extract()
    transform_data = transform(dataset)
    load_data = load(processed_dataset)

taskflow()
```

在此示例中，我们有三个函数，每个函数执行一个单独的活动。我们有一个任务装饰器，它会使这些函数成为一个任务，而父函数已用 DAG 装饰。

### Xcom

默认情况下，任务会孤立地执行或运行它们被提供的指令，并且不与其他任务或进程通信。Xcom 是 Apache Airflow 独有的概念，支持任务之间的交叉通信。使用 Xcom，您可以从一个任务实例获取信息或值，并在另一个任务实例中使用它。虽然也可以通过将值导出到文件或数据库，然后读取文件或数据库来获取值，从而以编程方式实现这一点，但 Xcom 功能是原生的，并且可以被认为是更安全的。

从开发的角度来看，交叉通信可以通过两个步骤实现。首先，您为任务创建一个 Xcom，然后从您创建它的地方拉取值。就创建 Xcom 而言，您可以简单地返回一个函数的值，或者您可以使用 `xcom_push` 方法。效果如下：

```python
@task
def some_function(value):
    result = some_other_function(*args)
    value.xcom_push(key="Result_Func1", value=result)
```

`xcom_pull` 方法将尝试使用 `task_id` 和键来获取值。以下是一个示例：

```python
def another_function(value):
    value_from_some_function = value.xcom_pull(key="Result_Func1", task_id="some_function")
    pass
```

### 钩子

Apache Airflow 通过钩子的概念，让您可以轻松地连接到外部数据平台和系统。Airflow 中的钩子让您可以连接到外部系统，而无需编写代码手动连接到这些系统。钩子还内嵌了可选的“重试”逻辑。Airflow 提供了许多内置钩子，并且还有一个开发者社区正在为各种应用程序开发自定义钩子。让我们看一个简单的说明。

让我们看看以下连接到数据库的代码：

```python
import psycopg2

def querydb():
    conn_params = {
        "host": localhost,
        "database": testDB1,
        "port": 5432,
        "user": "username",
        "password": "password"
    }
    try:
        conn = psycopg2.connect(**conn_params)
        cur = conn.cursor()
        sql = "select * from table1"
        cur.execute(sql)
        rows = cur.fetchall()
        for row in rows:
            print(row)
    except:
        print("error connecting to database")
```

现在让我们使用 Airflow 钩子连接到同一个数据库。代码可能如下所示：

```
from airflow.hooks.postgres_hook import PostgresHook
def querydb():
    dbhook = PostgresHook(postgres_conn_id='Postgres_TestDB1')
    sql = "select * from table1"
    rows = dbhook.get_records(sql)
    for row in rows:
        print(row)
```

注意在连接和检索行时使用了 `Postgres` 数据库钩子；此处不需要指定数据库连接参数。

### 重要说明
在 Airflow 中使用钩子之前，您必须先在 Airflow 中单独设置外部数据系统的凭据。
您可以访问 Airflow UI ➤ Admin ➤ Connections ➤ New Connection。一旦您配置了外部数据系统，您就可以获得一个连接 ID，可以在钩子中使用它。

## 变量
Apache Airflow 变量是可以在 Airflow DAG 中定义和使用的键值对。这些变量的作用域是全局的，可以从任何任务中访问。DAG 中的任务可以查询这些变量。您也可以在模板中使用这些变量。以下是如何在 Apache Airflow 中设置变量。一个很好的应用是存储一些在项目期间保持静态的数据库或访问凭据。当使用 Airflow 变量存储 API 密钥、密码和其他敏感信息时，Airflow 会自动屏蔽这些变量。此外，Airflow 还会加密这些变量。因此，建议使用变量来存储敏感信息，而不是其他可能缺乏屏蔽和加密功能的选项。

您可以通过管道、命令行界面甚至用户界面（Web 服务器）以编程方式创建 Apache Airflow 变量。以下是在命令行界面中设置 Airflow 变量的示例：
```
airflow variables set server_ip 192.0.0.1
airflow variables set -j aws_creds '{"AWS_SECRET": "your_secret_here"}'
```

您也可以从 Apache Airflow Web 服务器设置或批量上传 JSON。要开始，请导航到 Web 服务器中的 Admin 菜单，然后单击 Variables：
![](img/615530_1_En_12_Fig10_HTML.jpg)
一个 Airflow 窗口的截图，菜单项包括 DAGs、Cluster activity、Datasets、Security、Browse、Admin 和 Docs。未找到任何记录。
图 12-10 在 Apache Airflow 中创建变量

您可以单击 `+` 按钮创建一个新变量。它将呈现一个新屏幕，您可以在其中输入键、值和描述。效果如下：
![](img/615530_1_En_12_Fig11_HTML.jpg)
一个 Airflow 窗口的截图，用于添加变量信息，如键、值和描述。底部有一个保存按钮。
图 12-11 填充变量的键、值和描述

您可以使用以下语法检索这些变量：
```
var.value.
```

以下是一个示例说明：
```
from airflow.models import Variable
def get_aws_parameters():
    aws_access_key = Variable.get("aws_key",default_var=None)["aws_access_key"]
    aws_secret_key = Variable.get("aws_key",default_var=None)["aws_secret_key"]
    PythonOperator(
        task_id="get_aws_parameters",
        python_callable=get_aws_parameters,
    )
```

## 参数
`Params` 是 `parameters` 的缩写。与变量类似，参数是您可以在运行时传递给 Airflow DAG 运行或任务实例的参数。不同之处在于，传递给参数的值不会被加密，因此不应将其用于存储敏感信息。您可以在 DAG 级别或单个任务级别传递参数。任务级别的参数优先于 DAG 级别的参数。

让我们看看在任务级别传递参数：
```
BashOperator(
    task_id="an_example_task",
    bash_command="echo {{ params.my_param }}",
    params={"my_param": "this is an example parameter"},
)
```

以下是另一个在 DAG 级别传递参数的说明：
```
from datetime import datetime
from airflow.decorators import dag, task
@dag(
    start_date=datetime(2024, 2, 2),
    schedule=None,
    catchup=False,
    params={
        "Person": "JohnDoe",
        "Value": 10
    },
)
def demo_dag():
    @task
    def demo_task(**context):
        print(context["params"]["Person"])
        print(context["params"]["Value"] + 10)
        print_all_params()
demo_dag()
```

与 Apache Airflow 中的变量相比，参数更加本地化。如果您想在每次运行时为任务传递不同的值，参数将是一个不错的选择。

## 模板
在 Apache Airflow 中，模板化的概念利用了一个名为 `Jinja` 的 Python 模板框架，它允许您在运行时将数据传递到任务实例中。这些 Jinja 模板使您能够在运行时填充值。此模板化的语法如下：
```
{{ your-value-here }}
```

模板化最常见的应用是获取 Apache Airflow 中的执行信息。这些包括正在运行的任务实例的日期、执行日期以及当前 DAG 的开始和结束日期间隔。这些模板在 Apache Airflow 中最常用的操作符中得到支持。您还可以通过添加过滤器或函数来扩展这些模板的功能。以下是如何在 Airflow 中使用模板：
```
BashOperator(
    task_id="begin and end date interval"
    bash_command='echo "the DAG started at {{ data_interval_start }} and finished at {{ data_interval_end }}"'
)
```

## 宏
Apache Airflow 中的宏使您能够注入预定义的函数，以便在模板中使用。它们位于模板中的 `macros` 命名空间下。目前，Airflow 宏支持来自 Python 库的方法，即 `time`、`datetime`、`uuid`、`random` 和 `dateutil`。以下是一个使用宏的示例：
```
from datetime import datetime
BashOperator(
    task_id="display_current_datetime",
    bash_command='echo "It is currently {{ macros.datetime.now() }}"'
)
```

在前面的示例中，我们使用 `macros` 命名空间调用了 Python 库中的一个方法。如果您尝试在没有 `macros` 命名空间的情况下调用它，该命令将不会返回日期时间。

## 控制 DAG 工作流
通常，生产应用程序或数据管道会变得更加复杂，任务流可能比我们看到的示例更复杂。可能需要在生产数据管道的各个阶段设置决策循环。典型的场景可能看起来像根据给定上游任务的结果，执行下游任务的子集或仅执行管道下游的某些部分。其他场景可能看起来像根据条件跳过整个下游任务。因此，不仅需要调度和编排 DAG 数据管道，还需要控制这些 DAG 的条件执行逻辑。在 Apache Airflow 中，这被称为分支。

您可以通过两种不同的概念来控制 DAG 的执行：分支和触发。在分支中，您利用操作符来定义条件执行。在本节中，我们将介绍两个常用的操作符：`BranchPythonOperator` 和 `ShortCircuitOperator`。

`BranchPythonOperator` 是 Apache Airflow 中的一个操作符，它允许您评估某个条件（或决策循环），并决定您希望执行下游任务的哪个子集。它的工作方式是，操作符会评估来自任务的条件，并返回一个任务 ID 或任务 ID 列表以在下游执行。以下是 `BranchPythonOperator` 的示例说明：
```
def decision_loop(result):
    if value > 0.5:
        return ['third_task', 'fifth_task']
    return ['fourth_task']
branching = BranchPythonOperator(
    task_id='branch',
    python_callable=decision_loop,
)
```

# 高级工作流编排工具

## Airflow 新语法与任务装饰器

此示例使用了较旧的语法，您可以在其中创建相应操作符类的实例，而最新的语法则使用一个装饰器来处理相应操作符的实例化和操作。

让我们看一个使用新语法的示例：

```python
from airflow.decorators import dag, task
from datetime import datetime
from random import randint

@dag(
    start_date=datetime(2024, 2, 2),
    schedule=None,
    catchup=False
)
def branch():
    @task
    def cur_task():
        return randint(1,100)

    @task.branch
    def decision_loop(value_from_cur_task):
        if ((value_from_cur_task > 0) and (value_from_cur_task < 40)):
            return 'hello_world'
        elif ((value_from_cur_task >= 40) and (value_from_cur_task <= 80)):
            return 'other_task'
        else:
            return 'i_am_next'

    @task
    def i_am_next():
        pass

    @task
    def next_to_that():
        pass

    @task
    def other_task():
        pass

    @task
    def hello_world():
        pass

    value_from_cur_task = cur_task()
    decision_loop(value_from_cur_task)
```

此示例使用了任务装饰器。根据给定任务生成的值，检查一个条件，并根据评估结果返回相应的任务 ID，这将决定运行哪个下游任务。在代码中，`@task.branch` 是 `BranchPythonOperator` 的装饰器版本。

## ShortCircuitOperator

另一个可用于控制工作流的操作符称为 `ShortCircuitOperator`。如果 `ShortCircuitOperator` 满足给定条件，它将跳过所有下游任务。让我们看一个示例：

```python
from airflow.decorators import task, dag
from airflow.models.baseoperator import chain
from datetime import datetime
from random import getrandbits

@dag(
    start_date=datetime(2024, 2, 2),
    schedule='@daily',
    catchup=False,
)
def sc1():
    @task
    def begin():
        return bool(getrandbits(1))

    @task.short_circuit
    def decision_loop(value):
        return value

    @task
    def success():
        print('Not skipping the execution')

    chain(decision_loop(begin()), success())

sc1()
```

在此示例中，我们有一个简单的函数，它返回一个随机的 true 或 false，并将其提供给决策循环函数。如果随机函数返回 true，则执行下游任务 `success()`；如果随机函数未返回 true，则跳过下游任务。因此，每次运行此 DAG 时，您都会获得不同的结果。

## 触发器

正如我们在前面的章节中讨论的，一个给定的任务只有在所有前置任务成功完成后才会执行。我们已经看到了根据条件控制执行的方法。让我们想象一个场景，其中有 `n` 个任务线性调度，一个任务在其前置任务成功完成后开始执行。

假设我们有一个需求：如果一个任务失败，则忽略所有其他后续任务，并且必须只运行结论任务。您不能短路剩余的工作流，因为那样会跳过包括结论任务在内的所有任务。这就是触发器概念的有用之处。

您可以通过向任务添加 `trigger_rule` 参数，在任务本身内部配置触发器。让我们看一个示例：

```python
from airflow.decorators import dag, task
from airflow.utils.trigger_rule import TriggerRule
from datetime import datetime
import random

@dag(
    start_date=datetime(2024,2,2),
    dag_id="trigger_illustration",
    schedule="@daily"
)
def dag_trigger():
    @task(task_id="begin")
    def begin():
        print("Task execution begins")

    @task(task_id="random_task")
    def random_task():
        if random.choice([True,False]):
            raise ValueError("Task 2nd in queue unsuccessful")
        else:
            print("Task 2nd in queue executed")

    @task(
        task_id="task3",
        trigger_rule=TriggerRule.NONE_FAILED_MIN_ONE_SUCCESS
    )
    def task3():
        print("Task 3rd in queue executed")

    @task(
        task_id="task4",
        trigger_rule=TriggerRule.NONE_FAILED_MIN_ONE_SUCCESS
    )
    def task4():
        print("Task 4th in queue executed")

    @task(
        task_id="conclusion_task",
        trigger_rule=TriggerRule.ONE_FAILED
    )
    def conclusion_task():
        print("One of the tasks unsuccessful")

    begin() >> random_task() >> [task3(), task4()] >> conclusion_task()

dag_trigger()
```

在前面的示例中，我们有五个任务，其中一个任务被配置为执行或不执行，在此期间必须执行结论任务。通过指定各种触发规则，我们能够在任务未执行的情况下跳过下游任务并执行结论任务。

## 结论

正如我们在本章中所探讨的，Apache Airflow 是一个强大的工作流编排工具，也是编排数据工程和机器学习作业的良好解决方案。Apache Airflow 已经存在了近十年，支持工作流编排并提供高级功能和自定义选项。所有云供应商似乎都提供 Apache Airflow 即服务，这表明它在行业中被广泛采用。我们研究了如何设置 Airflow、Airflow 系统的架构、组件以及它们如何协同工作。然后，我们研究了 DAG、钩子、操作符、变量、宏、参数、触发器和其他概念，以及如何利用它们来编排数据管道。在下一章中，我们将介绍另一个工作流编排工具，它更加强大，并且使您能够无需 DAG 即可工作。工作流编排工具的选择取决于项目需求、当前基础设施和团队。

# 13. 使用 Prefect 编排数据工程管道

## 简介

之前我们研究了 Apache Airflow，这是最广泛采用的工作流编排解决方案。Apache Airflow 得到了社区的广泛采用和支持。在本章中，我们将介绍另一个名为 Prefect 的工作流编排解决方案。我们将研究其架构以及如何在没有 DAG 的情况下创建工作流。不过，您仍然可以在 Prefect 中定义 DAG。Prefect 的特性、组件和典型工作流看起来与我们之前看到的 Apache Airflow 不同。至于哪个工具是更好的选择，这取决于许多因素，如团队、需求、基础设施等。

在本章结束时，您将学习到：

*   Prefect 工作流编排的基础知识和关键概念
*   设置、安装和配置 Prefect 开发环境
*   Prefect 的核心概念，如流程、任务和结果
*   工件、变量和持久化结果
*   状态、块和状态更改钩子
*   任务运行器

## Prefect 简介

`Prefect` 是第二代开源工作流编排平台，旨在帮助自动化、编排、执行和管理各种基于 Python 的任务和流程。`Prefect` 可以在运行时生成动态工作流。`Prefect` 的学习曲线平缓，这意味着使用 `Prefect` 进行工作流所需的时间相对较少。使用 `Prefect`，可以将 Python 函数转换为可被编排、监控和观察的工作单元。`Prefect` 提供的一些突出特性是：

**调度**：调度作业或任务通常通过 cron 调度或使用专用的编排服务来完成。使用 `Prefect`，可以在简约的 Web UI 中创建、修改和查看调度。`Prefect` 提供 cron 调度、间隔调度和用于更复杂事件调度的重复规则调度。

# Prefect 核心概念与安装

## 核心概念

**重试**：数据工程师经常编写代码，以便在失败时重试某个操作。这通常用于需要连接到数据库的情况，作为定期运行的数据集成/工程管道的一部分。使用 Prefect，可以简单地使用 Python 装饰器来指定重试次数，从而消除了对多行代码及其相关可维护性的需求。

**日志记录**：日志记录是在数据管道执行期间发生事件时注册或记录信息的过程。日志记录可以包括高级信息，如注册连接到数据库的状态，以及低级信息，如捕获执行期间某个时间点的变量值。`Prefect` 会自动记录各种操作的日志，而无需使用 Prefect 执行额外的配置。

**缓存**：缓存是一种将数据副本存储在特定位置（通常称为缓存）的方法。其思想是高效地检索这些项目，以便在其他作业中重用。使用 Prefect，可以缓存任务的结果并将其持久化到指定位置。

**异步**：`Async` 是 asynchronous 的缩写，是一种在主流程之外执行任务的方法。使用 Prefect，可以异步运行任务，这意味着 Prefect 可以在等待另一个任务完成时启动另一个任务，而不会停止程序的执行。

**通知**：通知是数据管道在数据管道执行之前、期间或之后发送的关于事件或更新的消息。`Prefect Cloud` 通过提供各种警报机制（如电子邮件、短信以及 Slack、MS Teams 等平台）来启用关于数据管道的通知。

**可观测性**：`Prefect Cloud` 提供了一个类似仪表板的用户界面，用户可以在其中观察和监控各种已调度、正在运行或已完成的任务和流程。

在传统的数据栈中，必须编写多行代码，并且在某些情况下，需要利用另一个工具或软件来实现这些功能。与类似的编排平台相比，`Prefect` 能够开箱即用地提供这些功能，这被认为是有吸引力的。

`Prefect` 有两个不同的版本，即 `Prefect Core` 和 `Prefect Cloud`。`Prefect Cloud` 是 `Prefect Technologies, Inc.` 的商业产品，带有一个类似仪表板的用户界面，用户可以在其中观察和监控工作流，以及一些其他功能，如基于角色的访问控制和与其他工具和技术的集成等。

`Prefect Core` 是一个提供核心功能的开源框架。您可以在自己的基础设施内运行 `Prefect Core`。`Prefect Core` 是一个 Python 包，提供 Prefect 的核心组件。您可以从 Python 包安装程序中获取 Prefect。

## 设置与安装

要在您的工作站上设置 Prefect，让我们在您的系统中创建一个新的虚拟环境：

```
sudo apt update / brew update
python3 -m venv prefect
source prefect/bin/activate
```

如果您使用的是 MS Windows 操作系统，请使用以下命令：

```
prefect/Scripts/activate
```

接下来，在您的系统中安装 Prefect：

```
pip install -U prefect
```

让我们通过运行以下命令来验证安装过程：

```
prefect version
```

终端将打印类似这样的内容：

![](img/615530_1_En_13_Fig1_HTML.jpg)

一个终端输出的截图，显示已安装的 Prefect 版本 2.14.21，API 版本 0.8.4，Python 版本 3.11.5，以及系统详细信息，包括 Linux 架构、SQLite 数据库版本 3.41.2 和 Git 提交哈希。

图 13-1 检查已安装的 Prefect 版本

## Prefect Server

`Prefect Server` 是一个开源后端，有助于监控和执行 Prefect 流程。`Prefect Server` 是 `Prefect Core` 包的一部分。`Prefect Server` 在其用户界面中提供以下选项：

*   流程运行
*   流程
*   部署
*   工作池
*   块
*   变量
*   通知
*   任务运行并发
*   工件

以下是如何设置本地服务器：

```
prefect server start
```

如果 Web 服务器成功启动，您将看到以下屏幕：

![](img/615530_1_En_13_Fig2_HTML.jpg)

一个终端输出，指示各种包（包括 Prefect 2.14.21）已成功安装。提供了启动 Prefect 服务器并使用本地 API URL `http://127.0.0.1:4200` 进行配置的说明，以及 API 文档和仪表板的链接。

图 13-2 启动 Prefect 服务器

导航到浏览器并输入此 URL 以访问用户界面：

`http://127.0.0.1:4200`

![](img/615530_1_En_13_Fig3_HTML.jpg)

Prefect 仪表板在 localhost 127.0.0.1:4200 的截图，侧面板突出了 Dashboard 选项。主区域提示用户运行一个流程以开始，并带有一个标有“开始”的按钮。底部有一条消息提到了 `Prefect Cloud` 的扩展选项。

图 13-3 Web 浏览器中的 Prefect 用户界面

从软件设计的角度来看，似乎有三个主要组件：

*   `Prefect` 核心，提供核心的 Prefect 功能和工作流编排引擎
*   用于 `Prefect` 元数据的后端数据库，默认附带 sqlite，并且可以配置为 Postgres 数据库
*   用于观察和监控工作流、机密管理和其他编排相关功能的 Web 服务器

您可以通过发出以下命令来重置后端 `Prefect` 数据库：

```
prefect server database reset -y
```

`Prefect` 是一个开源的工作流编排系统，它管理组织的数据管道，并提供各种开箱即用的功能，使所有数据作业和管道具有一致性。此外，Prefect 提供了整个组织数据管道的集中视图，包括当前运行的状态和运行历史。Prefect 还提供了一个机密管理系统，可以在其中存储安全的登录凭据和其他关键凭据，与组织当前的工具集集成，并提供自定义集成。

## Prefect 开发

### 流程

流程是 `Prefect` 开发中最基本的单元。流程是工作流逻辑的容器，允许用户管理和理解其工作流的状态。它在 Python 中表示为一个单一函数。要将 Python 函数转换为 Prefect 流程，需要向该函数添加以下装饰器：

```
@flow
```

添加此装饰器后，函数的行为会发生变化，呈现以下特性：

*   流程状态的变化会报告给 API，从而实现对流程执行的实时观察。
*   可以验证输入参数，从而实现数据完整性和一致性。
*   可以在失败时执行重试（需要作为参数提及）。
*   可以强制执行超时，以防止意外的长时间运行的工作流。

流程可以包含对其他流程或任务的调用，`Prefect` 将其称为“子流程”。

### 流程运行

流程运行代表流程执行的单个实例。可以通过简单地运行包含该流程的 Python 脚本来创建流程运行。也可以通过设置 `Prefect Cloud` 或自托管 `Prefect Server` 上的部署来启动流程运行。如果流程运行包含任务或子流程，则 `Prefect` 将跟踪每个子流程运行与父流程运行的关系。

以下是一个基本 `Prefect` 流程的示例：

```
from prefect import flow

@flow()
def hello_world():
    print("Hello World of Prefect!")

hello_world()
```

让我们看一个利用可以传递给装饰器的各种参数的 `Prefect` 流程的说明：

```
from prefect import flow

@flow(name = "MyFirstFlow",
      description = "This flow prints Hello World",
      retries = 3,
      retry_delay_seconds = 10,
      flow_run_name = "Hello World Flow",
      timeout_seconds = 300
      )
def hello_world():
    print("Hello World of Prefect!")

hello_world()
```

除了流程的名称和描述，我们还可以配置重试。`retries` 参数是在流程运行失败时重试执行的次数。您还可以通过设置 `retry_delay_seconds` 参数来指定失败后重试流程前等待的秒数。此外，还有 `timeout_operator`，如果流程超过指定的运行时间，它会将流程标记为失败。

要执行此 Python 程序，只需从终端执行该程序。执行后，您将看到类似于以下所示的日志跟踪：

![](img/615530_1_En_13_Fig4_HTML.jpg)

一个终端输出，指示使用 Prefect 执行 Python 脚本 `prefect_1.py`。名为 elated turkey 的流程运行已成功创建并完成，并显示消息 "Hello World of Prefect!"。流程运行完成状态显示为 completed。

图 13-4 执行 Prefect 流程

# Prefect 流程与任务详解

## 界面

如您所见，Prefect 已执行了一个流程实例，并且此流程运行已被赋予一个名称。您还可以看到流程以 `completed` 状态结束。让我们看看用户界面：

```
![](img/615530_1_En_13_Fig5_HTML.jpg)

Prefect 流程运行仪表板的截图。提供了流程、部署、工作池和标签的过滤器，以及过去一周的数据范围和一个状态字段。底部显示了一个标题为“my first flow hello world flow”的流程运行，时间戳为 2024 年 2 月 15 日。

图 13-5
```

在用户界面中观察 Prefect 流程

回想一下，我们分别为流程和流程实例分配了名称 `MyFirstFlow` 和 `Hello World Flow`。当您单击流程运行名称时，可以获得相同的日志跟踪和一些其他详细信息。效果如下：

```
![](img/615530_1_En_13_Fig6_HTML.jpg)

Prefect 流程运行仪表板的截图，显示 1 个标题为“My first flow hello world flow”的流程运行。该流程标记为已完成，时间戳为 2024 年 2 月 15 日。日志选项卡已突出显示，并选择了级别“全部”和排序“从旧到新”。信息选项显示“finished in state completed”。

图 13-6
```

Prefect 流程的日志跟踪

虽然流程是 Prefect 中工作流的基本概念，但一个流程可以包含一个或多个任务。

让我们看看任务的概念。

## 任务

任务是 Prefect 中代表单个离散工作单元的函数。完全有可能仅使用流程来创建工作流，其中过程被封装为带有流程装饰器的 Python 函数。然而，任务允许将工作流逻辑分解为更小的包，这些包可以在各种其他流程和子流程中重用。任务类似于函数，它们接受输入、执行函数并返回输出。任务可以在定义流程的同一个 Python 文件中定义。任务也可以在一个模块中定义，并可以被导入以在流程定义中使用。所有任务都必须从流程内部调用，这一点至关重要。

任务从上游依赖项接收元数据，包括在执行之前依赖项的状态，无论是否从它们接收数据输入。这使开发人员能够使任务在另一个任务完成之前等待。在 Prefect 中，任务的使用是可选的。您可以将所有代码写在一个巨大的函数中，并为该函数使用 `@flow` 装饰器。Prefect 可以执行该函数。但是，将代码组织成尽可能多的小任务可能有助于获得运行时状态的可见性，并有助于调试代码。如果没有任务，如果一个作业在一个流程中运行并且任何一行代码失败，那么整个流程将失败。如果使用任务将流程分解为多个任务，那么调试起来会相对容易。

每个 Prefect 工作流必须包含一个主要的、入口点的 `@flow` 函数。从该流程函数中，可以调用许多任务、子流程和其他 Python 函数。默认情况下，任务不能在其他任务中调用；在从其他任务调用任务时，必须使用特殊的语法：

```python
from prefect import flow, task

@task
def task1():
    print("I am task1")

@task
def task2():
    task1.fn()
    print("I am task2")

@flow
def flow1():
    task1()
    task2()

flow1()
```

在前面的示例中，我们看到了两个合并到一个流程中的任务。虽然 Prefect 不允许从其他任务触发任务，但有一个选项可以通过使用 `fn()` 方法直接调用任务的函数。您可以看到函数 `task1` 已在 `task2()` 函数内部被调用。这将是该函数的一次性执行，并且当从任务调用此函数时，您无法利用重试等 Prefect 功能。

让我们看看这个程序的输出。

当您从终端运行此代码时，您可能会收到类似于以下的日志跟踪：

```
![](img/615530_1_En_13_Fig7_HTML.jpg)

一个终端输出，指示执行了一个名为 charcoal silkworm 的 Prefect 流程，包含 2 个任务：task1 和 task2，两者都已创建并成功执行。流程运行和任务运行以 completed 状态结束，任务立即执行。

图 13-7
```

在命令行中观察 Prefect 流程的输出

此 Prefect 流程有两个任务，即 `task1` 和 `task2`，其中 `task2` 直接调用 `task1` 的函数。因此，您将看到 `task1()` 的执行和 `task2()` 的执行，其中包括 `task1` 的打印函数。

任务提供参数以进行进一步自定义。其中一些列在下面。以下是一个包含可选参数的任务示例：

```python
from prefect import flow, task

@task(
    name="Task1",
    description="This is the task1 returns a string",
    tags=[test],
    retries=2,
    retry_delay_seconds=10
)
def task1():
    print("I am task1")

@task(
    name="Task2",
    description="This is the task2 returns a string and calls the task1 function directly",
    tags=[test],
    retries=2,
    retry_delay_seconds=10
)
def task2():
    task1.fn()
    print("I am task2")

@flow(
    name="MainFlow",
    description="Main flow with tasks included",
    retries=1
)
def flow1():
    task1()
    task2()

flow1()
```

除了提供任务的名称和描述，您还可以指定在任务执行失败时重试特定任务的次数，以及重试前等待的持续时间。还有一个布尔参数，用于指定是否将任务运行结果持久化到存储中。

## 结果

结果非常直接。它们代表流程或任务返回的数据。如前所述，任务或流程基本上是可能返回值的 Python 函数。

以下是一个如何返回结果的简单示例：

```python
from prefect import flow, task

@task
def my_task():
    return 1

@flow
def my_flow():
    task_result = my_task()
    return task_result + 1

result = my_flow()
assert result == 2
```

在前面的示例中，您可以看到任务（带有任务装饰器的函数）的输出被保存到一个变量中。流程的输出也被保存到一个变量中。虽然此程序能够将任务和流程的结果保存到变量中，但该程序无法持久化它们。

## 持久化结果

Prefect 不会存储结果，除非它们有助于 Prefect 减少读写结果存储的开销。结果被持久化到一个存储位置，Prefect 存储对结果的引用。

以下功能依赖于结果的持久化：

- 任务缓存键
- 流程运行重试
- 禁用内存中缓存

要在 Prefect 中持久化结果，您需要启用结果持久化参数，指定存储位置，并且还可以选择将输出序列化为 JSON 或使用 pickle 序列化器。根据程序中使用的参数，结果可以持久化到给定任务、所有任务或一个流程。考虑以下示例：

```python
from prefect import flow, task

@task
def task1(a: int, b: int) -> int:
    c = a + b
    return c

@flow(retries=2)
def flow():
    ans = task1(2,3)
    print(ans)

flow()
```

在此示例中，流程启用了 `retries` 参数，这将要求所有任务的结果都被持久化。但是，流程重试不需要流程本身被持久化。让我们尝试运行此程序：

```
![](img/615530_1_En_13_Fig8_HTML.jpg)

一个终端输出，指示执行了一个名为 jovial sponge 的 Prefect 流程，包含一个任务 task1。该任务已立即执行并以 completed 状态结束，流程在命令行中持久化了其结果。

图 13-8
```

在命令行中观察 Prefect 流程的输出持久化

我们可以观察到程序成功执行。当您在 Prefect 服务器中检查结果时，您可能会看到结果被持久化了，而流程没有。效果如下：

```
![](img/615530_1_En_13_Fig9_HTML.jpg)

一个 Prefect 仪表板，带有流程运行选项卡。类型为 list 的未持久化结果，以及 task1-0 运行的结果（类型为 int）被持久化到特定文件路径。

图 13-9
```

结果持久化在存储中

您也可以手动切换结果持久化。切换持久化将覆盖 Prefect 可能自行做出的任何操作。以下是一个示例：

# Prefect 持久化与工件

## 任务和流程的持久化设置

在以下代码示例中，任务的 `persist_result` 已设置为 `False`。这意味着该任务永远不会持久化。任何依赖于持久化的功能都可能无法工作并导致错误。但是，对于流程，`persist_result` 已设置为 `True`。即使某个功能不需要，流程也会持久化结果。

```python
from prefect import flow, task

@task(persist_result=False)
def my_task(a: int, b: int) -> int:
    c = a * b
    return c

@flow(persist_result=True)
def my_flow() -> int:
    result = my_task(5,6)
    return result

my_flow()
```

让我们运行此代码：

![终端输出，指示执行了一个名为 asparagus panda 的 Prefect 流程](img/615530_1_En_13_Fig10_HTML.jpg)

*图 13-10*

### 切换持久化

代码运行没有错误。回想一下，我们要求任务不持久化结果，而要求流程持久化结果。让我们看看 Prefect 服务器中的流程运行输出：

![Prefect 流程运行仪表板](img/615530_1_En_13_Fig11_HTML.jpg)

*图 13-11*

在这里，我们看到虽然任务没有持久化结果，但流程将结果存储在了本地存储中。

进一步检查，该文件的序列化输出如下：

```json
{
    "serializer": {
        "type": "pickle",
        "picklelib": "cloudpickle",
        "picklelib_version": "2.2.1"
    },
    "data": "gAVLHi4=\n",
    "prefect_version": "2.14.21"
}
```

您可以选择指定一个特定的位置来存储结果。

## Prefect 中的工件

在 Prefect 中，工件指的是持久化的输出，如链接、报告、表格或文件。它们存储在 Prefect Cloud 或 Prefect Server 实例上，并在用户界面中呈现。工件支持轻松跟踪和监控流程随时间产生和更新的对象。已发布的工件可以与任务运行、任务名称或流程运行相关联。工件提供了显示表格、Markdown 报告和指向各种外部数据的链接的能力。

工件可以帮助管理与团队或组织共享信息，提供有用的见解和上下文。工件的一些常见用例是：

- **文档**：文档是 Prefect 工件最常见的用途。可以发布关于特定流程的各种报告和文档，其中可能包括示例数据，以便与团队共享信息并帮助跟踪工作进度。还可以随时间跟踪工件，以帮助查看数据的进展和更新。为此，工件必须共享相同的键。
- **数据质量报告**：工件可以帮助在任务期间发布数据质量检查。在训练大型机器学习模型的情况下，可以利用工件发布性能图表。通过为多个工件指定相同的键，还可以跟踪非常具体的内容，例如数据管道中的异常。
- **调试**：使用工件，可以发布关于结果写入时间和位置的各种信息。还可以添加指向存储位置的外部或内部链接。

## 链接工件

可以通过调用 `create_link_artifact()` 方法来创建工件。每个 `create_link_artifact()` 方法都会生成一个不同的工件，并具有以下参数。

以下是一个包含链接的工件的简单示例：

```python
from prefect import flow
from prefect.artifacts import create_link_artifact

@flow
def my_flow():
    create_link_artifact(
        key="artifcatkey1",
        link="https://www.google.com/",
        link_text="Google",
        description="Here is a search engine"
    )

if __name__ == "__main__":
    my_flow()
```

当我们运行此代码时，它将为我们生成一个工件。让我们尝试找到它。在您的 Prefect 服务器中，单击 `Flow Runs` 并导航到 `Artifacts` 列：

![Prefect 仪表板，显示流程运行和工件](img/615530_1_En_13_Fig12_HTML.jpg)

*图 13-12*

当您单击工件时，它可能会像这样呈现链接工件：

![Prefect 仪表板，显示链接工件键 `artifcatkey1`](img/615530_1_En_13_Fig13_HTML.jpg)

*图 13-13*

## Markdown 工件

Markdown 是一种轻量级标记语言，可用于向纯文本文档添加格式元素。需要将 Markdown 语法添加到各种文本中，以指示哪些单词或短语看起来不同以及如何不同。要创建 Markdown 工件，需要使用 `create_markdown_artifact()` 函数：

```python
from prefect import flow, task
from prefect.artifacts import create_markdown_artifact

@task(name="Manufacturing Report")
def markdown_task():
    markdown_report = f"""
# 制造报告

## 摘要
在菠萝电脑公司，我们在过去一个季度制造了以下产品。

## 各部门数据管道
| 产品         | 数量   |
|:--------------|-------:|
| 便携式电脑   | 60000  |
| 平板电脑     | 70000  |
| 手机         | 80000  |

## 结论
这些数字非常鼓舞人心，体现了我们勤奋且足智多谋的制造团队的实力。
    """
    create_markdown_artifact(
        key="manufacturing-report-1a",
        markdown=markdown_report,
        description="季度制造报告",
    )

@flow()
def my_flow():
    markdown_task()

if __name__ == "__main__":
    my_flow()
```

运行上述 Python 代码后，我们可以在同一位置访问该 Markdown 制品。该制品的外观如下：

![季度制造报告制品输出](img/615530_1_En_13_Fig14_HTML.jpg)

*图 13-14*

## 表格制品

可以通过使用 `create_table_artifact()` 函数来创建表格制品。示例如下：

```python
from prefect.artifacts import create_table_artifact
from prefect import flow

@flow
def my_fn():
    highest_churn_possibility = [
        {'customer_id':'12345', 'name': 'John Doe', 'churn_probability': 0.85 },
        {'customer_id':'56789', 'name': 'Jane Doe', 'churn_probability': 0.65 }
    ]

    create_table_artifact(
        key="table-artifact-001",
        table=highest_churn_possibility,
        description= "## 请今天就联系这些客户！"
    )

if __name__ == "__main__":
    my_fn()
```

运行此程序后，我们可以在 Prefect 服务器内的类似位置找到该制品。
其外观可能如下：

![](img/615530_1_En_13_Fig15_HTML.jpg)

一个完美的仪表盘展示了表格制品 `001`，显示了客户详情及其流失概率：John Doe 为 0.85，Jane Doe 为 0.65。仪表盘建议今天就联系这些客户。

图 13-15
Prefect 服务器中的制品

## 在终端中列出制品

此外，你可以在命令行中列出所有制品。通过输入`prefect artifact ls`，我们可以列出所有制品，如下所示：

![](img/615530_1_En_13_Fig16_HTML.jpg)

一个终端输出列出了所有制品，包含 ID、键、类型和更新列。ID 为 `42a86bd0bfcb4de1aae5129532f0ea43`，类型为 `markdown`。ID 为 `3efb0a6ae21541dcb7f9222360c378e9`，类型为 `markdown`。ID 为 `811ca93da2c9424ea8316b29839b6ad6`，类型为 `table`。每个制品都显示了各自的最后更新时间。

图 13-16
在终端中列出所有制品

## 检查制品内容

此外，你可以使用终端中的制品键来检查这些制品的内容。命令是`prefect artifact inspect <artifact-key>`。

![](img/615530_1_En_13_Fig17_HTML.jpg)

检查表格制品 `001`，其 ID 为 `811ca93da2c9424ea8316b29839b6ad6`。详细信息包括创建和更新时间戳、敦促联系客户的描述，以及客户流失概率数据。

图 13-17
使用制品键检查特定制品

## Prefect 中的状态

状态基本上是指系统中某个对象在特定时间点的状态或状况。在 Prefect 中，状态包含关于特定任务运行或流程运行状态的信息。提及流程或任务的状态，指的是流程运行或任务运行的状态。例如，如果一个流程旨在成功、完成或正在运行，那么它直接指的是流程运行的状态。Prefect 状态描述了程序在其执行生命周期中的当前位置。Prefect 中有终端状态和非终端状态。

以下是各种状态及其描述：

表 13-1
各种状态及其描述表

| 状态 | 类型 | 描述 |
| --- | --- | --- |
| `SCHEDULED` | 非终端 | 运行将在未来的特定时间开始。 |
| `PENDING` | 非终端 | 运行已提交，但正在等待满足必要的先决条件。 |
| `RUNNING` | 非终端 | 运行代码当前正在执行。 |
| `PAUSED` | 非终端 | 运行代码已停止执行，直到收到手动批准才能继续。 |
| `CANCELING` | 非终端 | 代码运行的基础设施正在被清理。 |
| `COMPLETED` | 终端 | 运行成功完成。 |
| `FAILED` | 终端 | 由于代码问题，运行未完成，且没有剩余的重新尝试次数。 |
| `CRASHED` | 终端 | 由于基础设施问题，运行未完成。 |

在 Prefect 中，流程对象的任务可以返回数据对象、Prefect 状态对象或 Prefect futures（数据和状态对象的组合）。让我们看一个关于数据对象的示例：

```python
from prefect import flow, task

@task
def add_one(x):
    return x + 1

@flow
def my_flow():
    result = add_one(1)  # 返回 int
```

状态对象是一个 Prefect 对象，指示给定流程运行或任务运行的状态。你可以通过将`return_state`参数设置为 `True` 来返回一个 Prefect 状态。示例如下：

```python
from prefect import flow, task

@task
def square(x):
    return x * x

@flow
def my_flow():
    state = square(5, return_state=True)
    result = state.result()
    return result

my_flow()
```

Prefect future 是一个 Prefect 对象，同时包含数据和状态。你可以通过在函数后添加`.submit()`来返回一个 Prefect future：

```python
from prefect import flow, task

@task
def square(x):
    return x * x

@flow
def my_flow():
    state = square.submit(5)
    data = state.result()
    pstate = state.wait()
    return data, pstate

print(my_flow())
```

返回值决定了流程的最终状态。

## 状态变更钩子

状态变更钩子是一种工作流控制机制概念。当流程或任务改变其状态或发生状态转换时，状态变更钩子允许你根据给定任务或流程中的状态变更类型来定义响应动作。对于任务，你可以在状态变为完成或失败时定义响应动作。对于流程，你可以在其状态变为完成、取消、崩溃或失败时定义响应动作。

让我们看一个示例：

```python
from prefect import flow, task

def success_hook(task, task_run, state):
    print("任务运行成功")

def failure_hook(task, task_run, state):
    print("任务运行失败")

def conclusive_hook(task, task_run, state):
    print("无论任务运行成功还是失败，此钩子都会运行。")

def flow_cancellation_hook(flow, flow_run, state):
    print(f"流程 {flow.name} 已取消")

def flow_crashed_hook(flow, flow_run, state):
    print("流程已崩溃")

def flow_completion_hook(flow, flow_run, state):
    print("流程已完成执行")

@task(
    on_completion=[success_hook, conclusive_hook],
    on_failure=[failure_hook, conclusive_hook]
)
def my_task():
    print("此任务成功")

@flow(
    on_completion=[flow_completion_hook],
    on_cancellation=[flow_cancellation_hook, conclusive_hook],
    on_crashed=[flow_crashed_hook]
)
def my_flow():
    my_task()

my_flow()
```

从前面的代码中可以看到，对于给定的状态转换，我们指定了不止一个状态变更钩子（以列表形式）。此外，你可以为多个任务和流程的相同状态转换指定相同的状态钩子。这样，当你拥有多个任务和流程时，你可以轻松地根据任务和流程中的状态变更来控制工作流。

## 块（Blocks）

块是 Prefect 的重要特性之一。块有助于存储配置和身份验证信息，并提供与其他系统交互的接口。
使用 Prefect 块，可以安全地存储身份验证信息、从环境变量中提取值，以及存储各种源系统、下游系统、通信渠道和其他系统的配置信息。利用块，可以查询数据库中的数据、从 AWS S3 等云数据存储中上传或下载数据、向 Microsoft Teams 频道发送消息，以及访问 GitHub 等源代码管理工具。块在 Prefect Server 和 Prefect Cloud 中均可使用。
需要注意的是，块用于存储在运行时不会更改或修改的配置。在块中定义的内容在流程运行期间不可更改。可以使用 Prefect UI 创建块以及修改现有块。
以下是其在 Prefect UI 上的显示方式：

![图 13-18 Prefect 服务器中的块页面](img/615530_1_En_13_Fig18_HTML.jpg)

一个 Prefect 仪表盘显示了一些块，并提示添加一个块以开始使用。块安全地存储用于管理与外部系统连接的凭据和配置。“添加块”按钮被高亮显示，其旁边有一个“查看文档”选项。

Prefect 为众多云数据存储和其他外部系统提供了块。也可以在 Prefect 中创建自定义块。Prefect 的自定义块基于 Pydantic 库中的 `BaseModel` 类构建。虽然所有块在存储前都会被加密，但也可以使用 Pydantic 的 `SecretStr` 字段类型来混淆字段。
让我们创建一个新块。在此处，点击“添加块”；你将看到一个包含各种块的列表。以下是创建自定义 AWS S3 块的示例。S3 是亚马逊云服务提供的一种对象存储云服务。要创建一个新的 S3 块，只需在搜索栏中输入“s3”，即可获得 S3 块。在块中输入所需详细信息并保存。效果如下所示：

![图 13-19 创建一个新的 AWS 块](img/615530_1_En_13_Fig19_HTML.jpg)

一个 Prefect 仪表盘显示了一个 AWS S3 存储桶块，并带有提示“将此代码片段粘贴到你的流程中以使用此块”、一个用于帮助的问号图标，以及一个“查看文档”选项。存储桶详细信息包括存储桶路径 `b pro dev bucket 1`、AWS 访问密钥 ID 和 AWS 秘密访问密钥。

也可以使用以下命令注册自定义块，以便在 Prefect UI 中使用它们：

```
prefect block register --file my_minio_block.py
```

## Prefect 变量

在 Prefect 中，变量是命名的、可变的字符串值。变量可以随时创建或修改，并且可以被缓存以便更快地检索。变量可用于存储非敏感性质的信息，例如配置信息。
需要注意的是，Prefect 变量未加密。要存储凭据等敏感信息，应使用块，而不是变量。
可以通过 Prefect 用户界面、Prefect API 和命令行界面来创建、读取、写入和删除变量。要使用命令行界面访问 Prefect 变量，你需要执行以下操作：

列出所有 Prefect 变量

```
prefect variable ls
```

检查名为 `prefect_var_1` 的 Prefect 变量

```
prefect variable inspect prefect_var_1
```

删除名为 `prefect_var_1` 的 Prefect 变量

```
prefect variable delete prefect_var_1
```

要通过用户界面创建 Prefect 变量，请转到 Prefect 服务器，选择“变量”，然后点击“新建变量”。
对你来说，效果可能如下所示：

![图 13-20 在 Prefect 服务器中创建一个新变量](img/615530_1_En_13_Fig20_HTML.jpg)

一个 Prefect 仪表盘显示了一个“新建变量”对话框，其中包含名称、值和标签字段，以及“取消”和“创建”按钮。“名称”字段被高亮显示，“创建”按钮也被高亮显示。

以下是使用 Prefect API 访问 Prefect 变量的方法：

```python
from prefect import variables

VariableFromPrefect = variables.get('prefect_var_1')

print(VariableFromPrefect)

# 包含在 prefect_var_1 中的值
```

### YAML 文件中的变量

可以在项目 YAML 文件中引用变量。在 Prefect 中引用 YAML 文件的语法是 `{{ variable name }}`。以下是 Prefect 文档提供的一个示例，其中传递了一个变量来为部署拉取步骤中的 git 仓库指定分支：

```yaml
pull:
  - prefect.deployments.steps.git_clone:
      repository: https://github.com/PrefectHQ/hello-projects.git
      branch: "{{ prefect.variables.deployment_branch }}"
```

上述变量将在运行时进行评估。也可以在不直接更新部署的情况下更改或修改变量。

## 任务运行器

在 Prefect 中，任务可以以不同的方式运行。它们是执行任务的并发、并行和分布式方式。执行任务时无需指定任务运行器。任务简单地作为 Python 函数执行，并为该函数产生适当的返回值。通过使用默认设置，当在流程中调用任务时，Prefect 会顺序执行该函数。
顺序执行是一种方法，其中函数或任务一个接一个地执行，只有在前一个任务或函数完成后才执行下一个。这是最简单的任务执行模型，它按特定顺序一次执行一个任务。Prefect 提供了一个内置的任务运行器，称为 `SequentialTaskRunner`。

这个 `SequentialTaskRunner` 同时适用于 Python 中的同步和异步函数。以下是一个示例：

```python
from prefect import flow, task
from prefect.task_runners import SequentialTaskRunner

@task
def task1():
    print("I am task1")

@task
def task2():
    task1.fn()
    print("I am task2")

# 使用 submit 可以创建 Prefect Future，有助于返回任务的状态和结果
@flow (task_runner=SequentialTaskRunner())
def flow1():
    task1.submit()
    task2.submit()

if __name__ == "__main__":
    flow1()
```

Prefect 还提供了 `ConcurrentTaskRunner`，这是另一个内置的任务运行器。

`ConcurrentTaskRunner` 以并发方式执行任务，其中多个函数或任务可以启动、运行和完成，而无需等待另一个进程完成。一个任务的执行不会阻塞流程中另一个任务的执行。以下是一个示例：

```python
from prefect import flow, task
from prefect.task_runners import ConcurrentTaskRunner
import time

@task
def stop_at_floor(floor):
    print(f"elevator moving to floor {floor}")
    time.sleep(floor)
    print(f"elevator stops on floor {floor}")

@flow(task_runner=ConcurrentTaskRunner())
def elevator():
    for floor in range(10, 0, -1):
        stop_at_floor.submit(floor)

if __name__ == "__main__":
    elevator()
```

此外，Prefect 还附带 `DaskTaskRunner`，这是一个并行任务运行器。它需要安装 Python 包 `prefect-dask`。`DaskTaskRunner` 将任务提交给分布式调度器。对于任何给定的流程运行，都会创建一个临时的 Dask 集群。以下是一个示例：

```python
import dask
from prefect import flow, task
from prefect_dask.task_runners import DaskTaskRunner

@task
def show(x):
    print(x)

# 创建一个带有一些资源注解的 `LocalCluster`
```

# 注解在 dask 中是抽象的，并非从你的系统推断而来。

## 这里，我们声明我们的系统每个工作进程有 1 个 GPU 和 1 个进程可用

```python
@flow(
    task_runner=DaskTaskRunner(
        cluster_kwargs={"n_workers": 1, "resources": {"GPU": 1, "process": 1}}
    )
)
def my_flow():
    with dask.annotate(resources={'GPU': 1}):
        future = show(0)  # 此任务在工作进程上需要 1 个 GPU 资源

    with dask.annotate(resources={'process': 1}):
        # 这些任务每个在工作进程上需要 1 个进程；因为我们
        # 指定了我们的集群每个工作进程有 1 个进程和 1 个工作进程，
        # 这些任务将按顺序执行
        future = show(1)
        future = show(2)
        future = show(3)

if __name__ == "__main__":
    my_flow()
```

# 结论

在本章中，我们了解了`Prefect`，这是另一个开源工作流管理与编排工具。`Prefect`提供了一种灵活且便捷的工作流编排方法，解决了工程团队面临的许多挑战。`Prefect`是Python原生的，其流程和任务的概念，以及仅使用装饰器即可创建工作流的想法非常便捷。到目前为止，我们了解了工作流编排器的组件，随后进行了设置和安装。我们深入理解了流程和流程实例、任务和任务实例。我们进一步探讨了结果、结果持久化以及各种产物。我们还讨论了状态、状态变更钩子和变量。我们了解了用于机密管理的块；此外，还可以与`HashiCorp Vault`集成以进行机密管理。值得指出的是，工作流编排解决方案的选择可能取决于多个因素（当前基础设施、团队的专业知识、项目需求等）。总的来说，通过学习`Airflow`和`Prefect`，你已掌握了更高级的技能，能够构建可扩展且更高效的数据管道。

# 14. 大数据与云计算入门

## 引言

过去十年间，整个技术领域以及数据处理的面貌发生了显著变化。仅在过去十年中，云计算的演进就彻底改变了在云端构建数据管道的概念。本章将作为使用主流云技术构建工程数据管道的入门或序言。我们将重点关注云计算的早期采用者，即亚马逊、谷歌和微软，以及它们的云计算栈。在本章中，我们将讨论云计算是如何打包和交付的，以及一些技术及其基本原理。尽管目前其中许多技术已经实现了自动化，但理解这些概念仍然至关重要。

### 到本章结束时，你将学到
*   云工程的基本网络概念
*   大数据处理与`Hadoop`生态系统的基础
*   `Hadoop`和`Spark`的采用以及云计算采用率的增长
*   云计算部署模型
*   云计算的架构与原则
*   云计算服务模型
*   云计算提供的一些服务

## 云计算的背景

我们所说的远程计算，其最早的形式始于客户端-服务器计算。客户端-服务器计算涉及两台个人计算机，其中一台计算机请求服务，另一台计算机用所请求的信息进行响应。传统上，访问互联网或电子邮件遵循的是客户端-服务器交互模式。此外还有对等计算，其中连接网络中的所有计算机都是相同的，并且相互交互。还有各种协议支持从一台计算机访问另一台计算机。随着操作系统和图形界面的发展，访问远程计算机的整个图形用户界面并执行各种操作成为可能。随着互联网和万维网的兴起并加速发展，公司开始服务于更广泛的受众群体。这些公司需要能够扩展的更好基础设施来服务其客户。从这里开始云计算之旅可能是稳妥的。让我们来看几个术语：

*   资本支出：资本支出是指为获取实物设备而产生的费用，这些设备能为公司带来利益。这通常被称为`CAPEX`。产生资本支出的一个典型例子是为公司IT基础设施购买刀片服务器。
*   运营支出：运营支出是指公司在日常运营中为产生收入而发生的费用。这些通常被称为`OPEX`。例如，租用办公空间、支付电费、各种设备的维护和修理费用等。

## 云计算的网络概念

理解网络在IT基础设施中扮演着重要角色至关重要。让我们回顾一些可能对你的学习之旅有所帮助的网络概念和术语。

### IP 地址
IP地址代表互联网协议地址。IP地址是服务器在互联网上被识别的方式。IP版本4将IP地址定义为一个32位数字。然而，IP版本6的采用正在增加，它将地址定义为一个128位数字。

### DNS
DNS代表域名系统。它可以被看作是一个地址簿，但仅适用于互联网。无需记住复杂的IP地址数字和字符，你可以将其视为一串单词，一个易于记忆的域名，可用于访问网站。DNS是一个数据库，其中IP地址与域名相互映射。

### 端口
端口是逻辑实体，充当服务器的门户。要进入服务器，你不仅需要拥有正确的IP地址，还需要正确的端口。端口号范围从0到65535。可以将其想象成一个拥有65,535个登机口的机场。要登机，你必须到达正确的机场并通过正确的登机口才能登上你的航班。例如，端口1521由Oracle数据库使用，端口22由SSH使用，等等。

### 防火墙
防火墙就像一个门禁系统，只允许特定的IP地址访问。可以将其更多地想象成一个仅限受邀者参加的发布活动，只允许白名单中的成员进入。

### 虚拟私有云
虚拟私有云是云计算供应商提供的虚拟化网络环境。它可以被看作是你自己的公共云环境中的一个小的门控空间，在这里你可以获得隔离并控制你的云资源。这与虚拟专用网络不同，后者通过私有网络提供安全加密的连接，以便你可以从远程位置访问私有服务。

### 虚拟化
虚拟化是运行操作系统虚拟实例的过程，其硬件与操作系统隔离。因此，计算机硬件可能具有特定的规格。通过采用虚拟化，我们能够仅配置裸机硬件所拥有的全部能力的一个子集，因此我们能够在同一台计算机内配置多个操作系统。一个位于计算机硬件和操作系统之间的虚拟机监控程序软件管理着为每个系统分配多少硬件资源。虚拟机监控程序启动的操作系统被称为虚拟机。

## 大数据简介

组织看到了从数据中获取洞察以做出更好决策的价值。在过去十年中，数据开始大规模增长。技术系统面临着挑战和需求，以适应这种增长并仍然能够从中获取洞察。正是在这里，大数据的概念被构想出来。大数据技术的创建基本上是为了满足大规模可扩展系统的需求，这些系统能够进行并行处理并提供洞察。

### Hadoop
大数据领域最广泛接受的系统是`Hadoop`。`Hadoop`是一个由多种工具和技术组成的生态系统，支持利用大数据进行处理并提供洞察。自21世纪初首次发布后，`Hadoop`便开源，并得到了全球各地开发者的贡献。`Hadoop`的根源在于分布式计算。`Hadoop`使得构建一个运行在大量消费级机器上的分布式计算平台成为可能。`Hadoop`生态系统由一个包含名称节点和一个或多个工作节点的集群组成，其中名称节点负责分配和协调任务，而工作节点则执行分配给它们的任务。

`Hadoop`生态系统拥有自己的文件系统，称为`Hadoop`分布式文件系统（`HDFS`）。`HDFS`支持`MapReduce`范式，该范式能够对大数据进行并行计算。`Hadoop`允许使用一种名为`Pig Latin`的脚本语言快速简便地编写`MapReduce`作业。`Pig Latin`有助于编写复杂的数据转换，并在大数据集的探索性数据分析中非常有用。此外，`Hadoop`还支持`Hive`，这是一个构建在`Hadoop`文件系统之上的数据仓库解决方案。`Hive`工具提供了一种类似SQL的接口，用于查询存储在`Hadoop`文件系统中的数据。`Hive`会将这种类似SQL的语法转换为`MapReduce`作业，并在数据上实现分布式计算。

### Spark
大数据系统演进过程中最大的飞跃来自`Apache Spark`。`Apache Spark`是一个支持大数据处理并具备内存计算能力的分析引擎。执行内存计算的想法使得`Apache Spark`比基于磁盘的`MapReduce`处理快100倍。此外，`Spark`支持多种编程语言，如`Java`、`Scala`、`Python`和`R`，提供全面的分析能力，包括SQL处理、实时流处理、机器学习流水线开发以及图处理。

`Spark`在塑造和演进现代数据平台（如`Delta Lake`架构、数据湖仓等）方面发挥了重要作用。`Delta Lake`是一个运行在数据湖之上的开源存储层，它基本上构建在`Spark`之上；`Delta Lake`提供了多种能力，例如在数据湖上实现ACID事务，以及集成流式处理和批处理数据。

大数据的发展导致对大数据系统及其紧密相关的工具和技术的需求增加，这无疑促进了云计算技术的更广泛采用。随着组织意识到从海量数据中获取分析结果以获得竞争情报和其他洞察，迁移到云端成为一个相对更容易的选择。云计算系统，无论供应商是谁，都能提供大规模的计算、内存和存储。配置和设置一个数据处理集群要快得多；更棒的是，云计算供应商只按你使用其资源的时间收费。这被称为无服务器计算，你将在后续的章节中看到更多相关内容。

## 云计算简介

云计算是租用和消费位于远程的计算机资源的概念。云计算是一个商业术语，也可以被视为一个流行词。云计算使组织能够摆脱资本支出，专注于运营支出。组织无需购买多台刀片服务器和机架服务器来搭建自己的IT基础设施，而是可以在云中配置相同的环境，并根据其消费水平支付运营费用。

其中一个最大且明显的优势是搭建基本基础设施的速度。在传统模式下，需要采购计算机，并配置存储、内存和网络，还需要有合适的办公空间来容纳这些物理基础设施。所有这些可能花费的时间比预期的要长。而使用云计算，配置各种计算、存储、内存以及与IT基础设施相关的其他组件所需时间不会超过一小时。这为组织提供了显著的产品上市时间优势。

其次，你可以通过云计算走向全球。你可以轻松扩展你的应用，有时云计算供应商会动态添加计算资源以满足需求，这使你能够进入新的市场。如前所述，你不再需要投资于专用的物理服务器，并花费数周时间进行设置和启动运行。

安全性是云计算的另一个主要优势。据我所知，主要的云供应商确实提供了各种隐私和安全工具及方法，例如访问控制、静态和传输中加密、自带加密密钥（让你掌控安全）以及多因素授权等等。不过，你仍然需要安全专家。然而，你可以获得所有这些与云供应商产品良好集成的服务，而无需单独采购每一项，这是采用云计算的一个优势。

## 云计算部署模型

根据用例和客户所在行业的性质，云计算可以通过多种方式进行部署。让我们详细了解一下。

### 公有云
公有云是客户部署云计算最常见的方法。它也被认为是最经济的选择。在公有云中，服务通过互联网连接交付。因此，客户只需要一个安全的工作站和足够强大的互联网带宽，就能无缝地访问云计算供应商提供的服务。它也是最容易设置的，创建一个账户并开始使用云服务可能只需要几分钟时间。

### 私有云
私有云是指组织客户对其IT基础设施拥有专有权利和控制权。这类似于运行传统的IT基础设施。你可以让第三方云供应商提供和管理专有的IT基础设施，或者也可以是组织自己部署并管理其专有云。你仍然可以利用更新的技术、自动化、虚拟化等，并享受云计算的好处。然而，与公有云产品相比，成本可能更高。

### 混合云
顾名思义，它是私有云和公有云概念的混合体。在混合云中，一些资源托管在本地，而另一些资源托管在公有云中。这种情况更常见于组织将某些应用保留在本地，而将其他某些应用迁移到云端。因此，组织可以在将数据保留在自己本地的同时，在云中采购和使用某些服务。

### 社区云
社区云是公有云的一种变体，其区别在于IT基础设施由特定社区或行业内的多个组织共享。它可由内部管理，也可由第三方供应商外部管理。社区云是一种协作模式。与公有云相比，它提供了更好的安全性。例如银行客户和医疗客户，他们需要满足各自监管机构制定的特定法规和合规要求。

### 政府云
政府云专为政府机构和实体打造；它是社区云的一个子变体，区别在于它旨在满足特定的合规与监管要求。这要求云计算供应商提供额外的安全层和策略，以符合政府实体可能拥有的特定要求。

### 多云
多云是指一个组织利用多个云供应商来托管其应用程序和基础设施。其优势在于你不会被单一云供应商绑定，因此可以轻松地在不同供应商之间迁移。从成本角度来看，这也可能是有利的。一个云供应商的报价可能比另一个云供应商的报价更低。其缺点是运营复杂性较高。

## 云架构概念

让我们回顾一些可能直接或间接涉及工程和机器学习项目数据管道设计的概念。你可能见过或读过软件架构的七个基本“-ility”或十个“-ility”。在本节中，我们将探讨在设计数据工程或机器学习项目的数据架构时需要牢记的类似概念。过去几年中，数据的体量、真实性和多样性逐渐增长，这正是讨论以下概念的动机。

### 可扩展性
每个数据管道都旨在处理和/或传输数据。其消耗的计算资源量与被处理的数据量成正比。随着数据管道处理更多数据，整个管道会变得过载。可扩展性是指处理因使用量增加而产生的压力，同时确保数据管道平稳、一致地有效执行其功能的过程。你需要大致了解管道能处理的最大工作负载，以及是否可能遇到内存泄漏或堆栈溢出。可扩展性可通过两种方法实现，即水平扩展和垂直扩展。水平扩展是向集群添加更多计算节点，以处理增加的处理流量。垂直扩展是增加给定计算节点的容量以满足流量需求。

### 弹性
弹性是指适应给定数据管道工作负载变化的能力。弹性是动态调整底层计算资源以满足数据处理需求，并减少计算资源利用率的概念。这与可扩展性不同，因为可扩展性侧重于在高峰消费期间进行扩展，而弹性则指数据处理系统根据需求动态扩展或收缩以最小化资源利用率的弹性特性。一个很好的例子是云供应商提供的无服务器计算。无服务器意味着你无需为底层计算付费，而只需为执行时间内完成的工作单元付费。此外，弹性还指在运行时添加或动态配置计算和存储的功能。

### 高可用性
高可用性定义为数据处理系统在无需干预的情况下，在指定时间内持续可用的能力。其理念是消除单点故障，并在需要数据处理时随时可用。高可用性可通过给定时间点内的正常运行时间百分比来衡量。即使在系统维护或底层计算环境发生故障的情况下，使数据处理系统保持可用的能力就是高可用性的理念。

### 容错性
容错性是指数据处理系统在底层计算环境实际发生故障时仍可用的理念。虽然它们可以说是同一枚硬币的两面，但这与高可用性的概念略有不同。两者都旨在使系统无中断地可用。实现容错性的方法是在不同区域添加运行相同数据处理系统的计算节点。例如，如果你的数据处理管道在一个地理区域提供服务，你可以在另一个地理区域再部署一个执行相同任务的系统实例。这样，如果一个区域发生故障，另一个区域可以继续提供服务，直到主区域恢复服务。许多分布式计算集群都有主节点管理通信、工作节点执行任务的概念。容错性就是拥有随时可用的备用主节点的理念。

### 灾难恢复
灾难是指宏观层面的灾难，例如导致日常服务中断的地震和洪水，以及其他与人类相关的事件，如疾病、大流行病或流行病，或其他导致服务长时间中断的事件。它们可能是有意的或无意的。灾难恢复是指在灾难事件发生后，恢复数据管理系统、数据处理和机器学习系统，并恢复其功能的概念。其理念是为数据管理、处理和机器学习系统建立一个备用站点。在此背景下，灾难恢复是云计算供应商将其作为一项服务提供的能力，以便企业能够照常运营。

### 缓存
缓存严格来说并非架构原则，但它是架构设计的一个重要方面。缓存是一种数据存储概念，将计算过程中常用的数据存储在一个地方。其理念是重用先前计算或处理过的数据，这样我们就不必一次又一次地运行相同的计算。缓存数据通常存储在`RAM`（随机存取存储器）中。与使用计算和存储再次执行相同操作相比，缓存从`RAM`中获取结果，从而提高了速度。从CPU架构的角度来看，可能还有其他几个缓存层，这超出了云数据架构的讨论范围。内存中缓存数据有几种类型；其中一些是`API`响应缓存、内容分发网络缓存等。我们的重点是数据库缓存。当你有一个数据库时，可能会有一些查询代价高昂；即使经过优化，也需要大量的资源消耗。通过从缓存而非源数据库获取查询结果，我们可以最大限度地减少延迟，并提高数据处理系统的整体性能。

## 云计算供应商

目前行业内有三大云计算供应商：亚马逊、谷歌和微软。亚马逊早在2006年就开始提供亚马逊云服务（Amazon Web Services），将云计算带入大众视野。谷歌推出了名为谷歌云平台（Google Cloud Platform）的云计算平台。2010年，微软推出了名为`Azure`的云计算平台。除了这三大云供应商外，还有几家其他公司提供云计算服务，其中包括甲骨文（Oracle）、DigitalOcean、IBM，以及一些服务于特定国家的区域性供应商。

## 云服务模型

一旦我们确定了要部署的云计算模型类型，就可以着手选择适当的服务模型。云计算供应商提供其服务的方式大致可分为三大类，我们将对此进行更详细的讨论。

![img/615530_1_En_14_Fig1_HTML.jpg](img/615530_1_En_14_Fig1_HTML.jpg)

一个三层金字塔。顶层标注为“软件即服务”。中间层标注为“平台即服务”。底层标注为“基础设施即服务”。图中展示了每一层的应用。

图 14-1：描述云服务模型的金字塔

### 基础设施即服务
这是指云计算供应商将计算基础设施作为服务来提供。其理念是云供应商为你提供服务，你可以用它来运行应用程序、执行计算等。要运行应用程序，你需要基础设施。基础设施包括计算机硬件，如处理器、内存、操作系统、网络、存储等。通常，云供应商会为你提供服务器、存储、网络以及一个带有操作系统的虚拟机。由你决定安装哪些软件来执行所需的任务。此模型适用于希望搭建和管理基础设施的管理员。基础设施即服务的例子包括微软`Azure`、亚马逊云服务和谷歌云平台等。

### 平台即服务
平台即服务是指云计算供应商为你提供开发应用程序或使用应用程序所需的所有基础设施和环境。想象一下，你可以租用一个配置好内存和存储的多核处理器，一个已安装所有更新和补丁的操作系统，以及已安装并管理好的软件开发工具，这样你就可以直接开始编写代码或部署代码。平台即服务对于希望从头开始开发某些东西而又无需担心管理工作的开发人员来说非常有用。平台即服务的例子包括Salesforce CRM、Tableau仪表板，或基于订阅的数据库即服务（例如`AWS RDS`上的`Postgres`实例）。

### 软件即服务
软件即服务是你每天随处可见的东西。你通过输入域名地址、用户ID和密码来查看谷歌提供的免费电子邮件账户，或者使用Instagram或Facebook等社交媒体提供商，这些都是免费使用的。基本上，这些公司为你提供了一切，包括硬件、操作系统、内存、存储和软件应用程序（Gmail、Dropbox等）。作为最终用户，你可以访问他们的服务，尽情使用，或许下次还会再来。软件即服务的例子可能包括Zoom视频会议、Microsoft Office 365（Word、Excel、PowerPoint）、Dropbox在线存储等。

## 云计算服务

### 身份与访问管理
身份与访问管理是一种业务流程，旨在让正确的用户在正确的时间访问正确的数据集。访问管理能够保障数据资产的安全，并控制不同用户对各类数据资产的访问。这是一个持续的过程，确保只有经过授权的用户和服务才能访问数据资产。亚马逊提供`AWS`身份与访问管理（`AWS Identity and Access Management`）和`AWS Lake Formation`服务，用于管理对其各种资源（包括数据湖环境）的访问（包括细粒度访问控制策略）。微软提供`Azure Active Directory`和基于角色的访问控制服务，以实现对各种服务的访问控制。谷歌提供`Cloud IAM`服务，以对其资源和数据资产进行细粒度的访问控制。

### 计算
计算指的是计算处理能力（如主板）、内存、存储以及与之连接的网络。这是能提供的最基本的云服务。2006年，亚马逊启动了其亚马逊云服务，并将`EC2`作为其最早提供的服务之一。你可以选择不同类型的微处理器（可能提供不同数量的核心）、所需的物理内存大小以及所需的网络连接类型。

### 存储
云存储基本上是将你的数据（文件、备份、图片、视频、音频等）存储在可通过网络访问的第三方存储提供商那里。云存储提供安全且经济高效的存储管理。根据客户的需求和用例，云存储提供多种类型的存储产品。这些存储系统可以在需要时自动扩展，并且可以高可用地通过网络进行访问。

#### 对象存储
对象存储是一种特殊类型的文件系统存储，可用于存储任何类型的文件。对象存储将数据作为对象进行管理，这与将数据作为文件进行管理的文件系统不同。每个数据都是一个对象，并附带元数据和一个与该单一数据关联的唯一标识符。对象存储用于多种应用，包括冷数据存储（数据访问频率低）和数据归档。对象存储的例子包括`Red Hat Ceph`、`MinIO`、`Cloudflare R2`、`Azure Blob Storage`（如果你在微软生态系统中）等。

#### 数据库
最常见的服务之一就是关系型数据库管理系统。云供应商以多种选项提供数据库即服务。你可以选择从云供应商那里租用一个带有高速网络的计算空间，然后自行安装数据库；或者，你也可以通过几次点击，租用一个预装了最新版本数据库的服务。如果你选择完全配置好的方案，供应商可能会提供一些数据库管理和维护任务的支持。使用数据库，你可以进行自动扩展、以编程方式交互、构建数据管道，此外还可以加载、查询和生成SQL报表。

#### NoSQL
除了关系型数据库，云计算供应商还提供`NoSQL`数据库解决方案。`NoSQL`代表“不仅仅是SQL”（Not Only SQL），意味着这些数据库管理系统旨在处理各种数据模型和数据结构。其理念是，`NoSQL`数据库系统能够补充并高效存储那些可能不适合千篇一律的关系型数据模型的数据。然而，`NoSQL`永远无法取代传统的关系型数据库系统、它们通过多种范式所强加的数据完整性，以及关系型数据库在事务系统中提供的有效性。`NoSQL`系统允许在无需预先定义模式的情况下插入数据。

#### 写入时模式 vs. 读取时模式
SQL与`NoSQL`数据库系统的区别在于，关系型数据库管理系统会对传入的数据强制施加一个模式。如果传入的数据不符合模式标准，SQL数据库系统将拒绝接收。另一方面，`NoSQL`数据库系统是无模式的；数据的结构是从`NoSQL`数据库中读取数据时才被解释的。这就是为什么传统上，`RDBMS`使用“写入时模式”，而`NoSQL`数据库系统使用“读取时模式”。

> **注意**
> `NoSQL`数据库系统通常是无模式的；然而，一些`NoSQL`数据库系统提供了模式强制选项，这有助于数据一致性。各种类型的`NoSQL`数据系统（文档型、键值型、图型、列型）处理模式的方式往往不同。`MongoDB`确实允许进行模式验证（这有点像进入了SQL领域）；少数`RDBMS`已经开始提供对JSON数据的支持，而`PostgreSQL`甚至允许你对非结构化数据编写复杂查询。至少可以说，SQL和`NoSQL`之间的界限正变得模糊；然而，理解`NoSQL`对JSON数据和非结构化数据有更好的支持和更快的查询引擎，而`RDBMS`在事务性和结构化关系数据方面仍然占据优势，这是很好的。

`NoSQL`自2010年代初就已存在。如今，各大云厂商似乎都提供了各种`NoSQL`产品。让我们逐一简要了解。

##### 文档数据库
文档数据库是专为管理半结构化数据而设计的数据系统。一个很好的例子是JSON数据，其中数据被捕获在JSON文档中。这些文档数据库具有灵活的数据模型，并且在加载之前无需定义。即使JSON文档可能具有复杂的结构，并且每个文档内部存在嵌套结构，只要它们来自单一来源，它们将始终保持一致。因此，文档数据库会适应这些文档的传入结构。一个著名的文档数据库例子是`MongoDB`。它是一个快速、轻量、易于使用的数据库，附带一种查询语言，可用于将JSON文档视为关系数据并在其之上编写查询。

##### 列导向数据库
另一种最古老的`NoSQL`数据库系统形式是列导向数据库。请注意，列导向数据库与列存储数据库不同。列存储仍然是用于OLAP处理和数据仓库目的的关系型数据库系统。它们在生成复杂分析报告方面极其快速和高效。列导向数据库是`NoSQL`系统，其模式随传入数据而演变。列存储数据库大多具有在加载数据之前定义好的维度数据模型。例如，`Postgres`是一个列存储数据库；`Google BigQuery`也是如此。列导向数据库也被称为宽列数据库。在这种数据库中，给定数据集的列可以随时添加。`Google Bigtable`和`Apache Cassandra`是宽列数据库的两个优秀服务产品。微软的`Cosmos DB`和亚马逊的`DynamoDB`同样是宽列数据库系统中具有竞争力的服务产品。

##### 键值存储
键值存储是`NoSQL`数据库系统的一种类型，其中数据以键值对集合的形式存储（与Python中的字典数据类型非常相似）。数据模型非常简单直接，其中键是标识符，可用于检索相应的值。因此，这些系统在检索时速度极快，因为它不需要扫描几个表并匹配某些过滤器和条件。很好的例子包括`Amazon DynamoDB`、`Redis`、`Azure Cosmos DB`等。

##### 图数据库
图数据库是另一种类型的`NoSQL`数据库，其底层数据模型由节点和边组成。节点是包含各种信息（如人物、姓名或其他离散对象）的实体。边是节点之间的连接，描述了连接两个节点之间的关系。让我们通过一个图形化示例来看两个实体如何共享关系，以及实体之间可能存在哪些类型的关系。

![img/615530_1_En_14_Fig2_HTML.jpg](img/615530_1_En_14_Fig2_HTML.jpg)

一个关于两个节点（标记为节点A和节点B）之间关系的示意图。节点A是节点B的朋友，在节点B工作，或者与节点B关系最密切。

图 14-2：两个节点之间的关系示例

一些优秀的图数据库例子包括`Neo4j`、`Amazon Neptune`、`Azure Cosmos DB`等。

##### 时序数据库
时序数据库是`NoSQL`数据库系统的另一种变体，其数据模型基本上是按时间戳索引的数据值。这种类型的数据存在于各种行业中——例如，交易时间内股票的价格，或使用物联网传感器监测温度。这些数据库被设计为写入密集型，同时在检索时间间隔内的聚合指标、基于时间范围的报告等方面非常高效。此类数据库的优秀例子包括`Google Bigtable`、`Azure Time Series Insights`和`Amazon Timestream`等。

##### 向量数据库
向量数据库是`NoSQL`数据库系统中较新的类型，它们被设计用于存储、索引和快速搜索向量数据。给定一个查询向量，向量数据库会执行快速搜索，以检索与给定向量最相似的向量。这也被称为相似性搜索，在图像识别和其他人工智能环境中非常有效。在这里，向量只是一个数字数组；根据上下文，这些数组可能带有特定含义。一旦给定一个向量，就可以指定依赖哪种度量方式来获取与之相似的最近邻向量。相似性度量可以是欧几里得距离、Tanimoto距离或曼哈顿距离。所有这些向量都会被索引，以实现更快的相似性搜索和检索。

#### 数据仓库
数据仓库是用于分析处理的列式关系型数据库管理系统。这些数据存储利用维度数据模型和列存储来更快地检索数据并生成分析洞察。各大云厂商提供数据仓库软件、访问控制和一些开箱即用的关键功能，因此你可以轻松地搭建你的数据仓库实例。配置数据仓库并使其上线所需的时间“相对”较快，但并非指数级快。亚马逊的`AWS Redshift`、谷歌的`BigQuery`和`Azure`的`Synapse Analytics`是关系型数据仓库环境的优秀例子。

#### 数据湖
数据湖是用于存储结构化、半结构化和非结构化数据的集中式存储库。数据湖环境支持类似于数据仓库的数据存储，但无需定义预定义的模式。你可以从各种来源摄取多种类型的数据，并可以使用各种分析工具（如`Spark`）或分布式计算环境（如`Hadoop`）与数据交互和查询。如果你拥有大数据，或者正在尝试集成关系型和非结构化数据以创建分析数据集，那么数据湖环境是理想的选择。数据湖是开发机器学习管道时值得考虑的一个好选项。数据湖环境的优秀例子包括亚马逊的`Lake Formation`、`Azure Data Lake Storage Gen2`和谷歌的`Bigtable`。

#### 数据仓库与数据湖
数据仓库存储结构化数据，并按照已定义的架构加载处理后的数据。而数据湖则存储非结构化或半结构化数据，无需强制定义架构。数据科学家和机器学习工程师使用数据湖来运行各种类型的**高级分析**，而数据仓库则面向数据分析师和开发人员。数据仓库继续提供其一直以来所创造的价值。搭建数据仓库环境可能成本略高，并且其价值可能需要通过有效的实践才能随着时间推移显现出来。数据湖相对成本较低，因为现代云服务提供了可用作数据湖的托管对象存储解决方案；然而，数据湖需要根据数据的增长率投入更多的处理能力。例如，一家医疗保健公司的数据仓库可能被设计用于以结构化格式（如维度模型）检索患者信息。而该公司的数据湖可能包含结构化的患者数据、医学影像、各种检测报告、患者健康检测视频等。

#### 实时/流式处理服务
正如我们所看到的，大多数业务和应用程序每天都会随着用户的使用而产生大量**实时数据**。如果能够快速分析这些数据，以识别给定时间段内的趋势或正在发生的事件，这其中蕴含着巨大机遇。这就是流式ETL管道的用武之地。可以利用`Apache Kafka`来设计生产者和消费者，并使用`Apache Flink`来创建流式数据分析管道。Amazon提供了`AWS MSK`（`Apache Kafka`的托管流式服务）和`AWS Kinesis`（提供`Apache Flink`功能）。Google也通过`Google Cloud`提供`Kafka`和`Flink`，以及`Google Pub/Sub`服务。Microsoft则以`Azure Stream Analytics`的名义提供这些服务。

#### 无服务器函数
**无服务器**意味着你可以选择按需消费服务，并仅为已使用的资源付费。在主流云平台中，无服务器意味着可以运行与一个或多个组件交互的函数，并设置任务来运行，而无需预置底层计算实例。Amazon提供名为`AWS Lambda`的无服务器计算服务，`Google Cloud Platform`提供`Cloud Functions`，Microsoft提供`Azure Functions`——这三者都提供无服务器计算服务。

#### 数据集成服务
数据集成是将来自不同来源的数据汇集起来，为企业提供价值和洞察，以便他们能够做出更快、更好的决策的过程。数据集成可以整合各种类型的数据，并能够生成简单的报告和运行复杂的分析。最常见的数据集成方法是ETL管道的概念，它代表提取、转换和加载任务；此外还有ELT的概念，即先将数据加载到现代数据平台中，然后再对其进行转换。`Google Cloud`提供`Cloud Data Fusion`作为数据集成服务，而Amazon提供`AWS Glue`，Microsoft提供`Azure Data Factory`。所有这些服务都由各自的云供应商完全托管。

#### 持续集成服务
**持续集成 (CI)** 是一种软件开发策略，它自动化地将来自团队不同成员的代码开发和更改集成到主构建中。当开发人员编写新的更改和开发内容时，CI过程会将代码运行到自动化测试用例中，在需要时执行代码审查，并将其集成到主构建中。这也被称为**持续开发 (CD)**。术语“CI/CD”与此过程同义。Amazon提供`AWS CodePipeline`，Microsoft提供`Azure Pipelines`，Google提供`GCP Cloud Build`。除了持续集成，这些工具还支持持续部署。持续部署是指软件自动部署到生产环境的过程。

#### 容器化
**容器**模拟虚拟机；它们托管应用程序或服务。与虚拟机不同，它们消耗的资源要少得多。与无服务器的概念相比，容器化使应用程序或服务能够始终保持可用。`Docker`是著名的容器软件，它有助于以容器镜像的形式将应用程序容器化。容器镜像是一种可执行软件，包含独立运行应用程序所需的一切，从代码、依赖项、系统工具和库等。**容器注册表**是存储和分发一个或多个容器镜像的地方。Amazon提供`AWS Elastic Container Registry`，Microsoft提供`Azure Container Registry`，Google提供`Container Registry`作为容器注册表服务。

#### 数据治理
**数据治理**是工具、流程和实践的结合，通过这些，数据在其整个生命周期内得到安全且合规的管理。在每个组织中建立数据治理实践至关重要，因为数据已成为组织拥有的最有价值的资产。像Amazon、Google和Microsoft这样的主要云供应商都提供各种可用于建立和运行数据治理实践的服务。让我们在这里更详细地了解其中一些服务。

##### 数据目录
**数据目录**是一个集中式存储库，用于存储和管理云中数据的元数据。它也是一个系统，用户可以在其中发现关于各种数据源的数据，并了解如何使用这些数据源。Amazon提供`AWS Glue Data Catalog`，这是一个用于存储和管理数据的集中式存储库。Microsoft提供`Azure Data Catalog`，这是一项完全托管的云数据目录服务，充当Microsoft Azure中各种数据资产的知识中心。`Google Cloud Platform`提供`Google Cloud Data Catalog`，这是一项可扩展的数据目录和元数据管理服务，用于发现云中的所有数据源。

##### 合规性与数据保护
**合规性**是遵守相应机构制定的规则和法规的过程。**数据保护**是识别和保护组织当前可能拥有的个人身份信息的过程。例如，在医疗保健行业，有`HIPAA`合规性要求，以确保个人身份信息免受欺诈和盗窃。Amazon提供`AWS Artifact`，用于生成审计和合规性报告，这些报告对各种监管机构和政策很有帮助。Microsoft提供`Azure Policy`，它提供一份可深入分析的报告，用于评估合规性和强制执行标准。Google提供`Security Command Center`，用于帮助监控合规性及其他安全标准。

##### 数据生命周期管理
数据生命周期管理是一个建立策略和流程的过程，用于在整个生命周期内管理数据。从新数据生成到数据进入长期归档，数据会根据不同标准被划分为不同阶段。数据会经历根据这些标准定义的各个阶段。微软、谷歌和亚马逊在其对象存储中提供了生命周期管理存储策略，分别是`Azure Blob`存储、`Google Cloud Storage`和`AWS S3`。这些策略能够根据已定义的条件，自动将对象存储中的数据对象移动到不同的存储类别。你可以定义条件，基于这些条件自动删除这些数据对象。

#### 机器学习
所有主要的云服务商都提供机器学习平台即服务，你可以在其上构建机器学习模型、训练这些模型，并能够与其他云服务连接以进行部署或报告结果。亚马逊提供`AWS SageMaker`，微软提供`Azure ML`，谷歌提供`Vertex AI`。这些服务涵盖了机器学习模型的完整生命周期，并且可以选择自行编写自定义机器学习模型，或使用低代码来构建机器学习模型。

## 结论

云计算已经彻底改变了技术格局。其提供的灵活性和经济高效的服务采购方式，使工程团队能够交付更多价值。我们已经看到大数据的兴起如何为各种工具和技术的演进铺平了道路。与十年前相比，你现在可以在相对更短的时间内配置一个`Hadoop`集群。然而，了解我们是如何演进的，对于理解我们当前拥有的技术和数据系统的复杂程度至关重要。尽管组织可能出于各种原因选择上云或不选择上云，但显而易见的是，云计算供应商提供了易用性以及最新的技术、工具和流程，以便从数据中获取洞察。

# 15. 使用 Amazon Web Services 构建工程数据管道

## 引言

云计算是一种为开发和部署数据及软件服务获取必要IT基础设施的概念。通过提供计算能力、内存、存储以及安装在物理机器上的各种软件，组织可以比以前更快地进入状态。这种方式消除了组织购买物理设备的资本支出，因为云计算提供商为其服务提供了按需付费的定价模式。在本章中，我们将详细探讨`Amazon Web Services`。亚马逊是云计算的早期采用者之一，也是最早为商业提供IT基础设施服务的少数公司之一，早在2006年就已开始。当时云计算被称为Web服务。如今，亚马逊提供超过200种云计算产品，包括但不限于计算、存储、分析、开发者工具等。让我们详细看看在开发数据和机器学习管道背景下`AWS`提供的服务。`AWS`将其托管数据中心的国家分为三个区域，也称为分区。它们是标准区域、中国区域和美国GovCloud区域。`AWS`建议选择地理上靠近实际所在地的区域，以最大限度地减少延迟。当你开始探索`Amazon Web Services`的旅程时，我恳请你注意管理云资源，尤其是在你使用完毕后。在传统基础设施的情况下，你可以在使用完毕后关闭计算机。然而，在云基础设施的情况下，你通常采购的是基于按需付费模式的计划，即使资源未被使用，你也会被计费。注销`AWS`并不意味着关闭你已采购的服务。这对于虚拟机、数据库以及任何其他计算密集型资源尤其重要。请务必养成检查活动资源并关闭这些特定资源的习惯。如果你不再计划使用某项服务，你可能希望将其删除。这样，你只需为你积极使用的服务付费，从而控制成本。我还要感谢`AWS`为新用户提供免费额度，并积极支持新学习者降低他们的成本。

### 到本章结束时，你将学习到
*   `Amazon Web Services`和`AWS`管理控制台的基础知识
*   如何设置`AWS`账户并配置各种参数
*   `AWS`的对象存储、数据库和数据仓库解决方案
*   数据处理和分析工具
*   如何使用`AWS SageMaker`构建、训练和部署机器学习模型

## AWS 控制台概览

通过使用`AWS`管理控制台，可以访问各种云服务。它是一个简单的图形用户界面，能够以更简单的定价访问托管在190多个国家/地区的200多种`AWS`服务。

### 设置 AWS 账户

要注册新账户，请访问[`​aws.​amazon.​com/​`](https://aws.amazon.com/) 并点击“创建AWS账户”。进入以下屏幕后，按照提示输入必要信息以创建账户：

![img/615530_1_En_15_Fig1_HTML.jpg](img/615530_1_En_15_Fig1_HTML.jpg)

`AWS`注册页面截图，显示了探索免费套餐产品以及创建或登录`AWS`账户的选项。

图 15-1：`AWS`控制台

虽然可以直接使用根账户来配置和访问服务，但我建议你创建一个管理员账户来配置和访问`AWS`服务。为此，让我们使用`AWS Organizations`服务。`AWS Organizations`是一项账户管理服务，可以管理多个账户。其流程是，在你的组织（根账户）内，你需要设置组织单元。组织单元基本上就是组织内的各个部门。接着，你需要创建服务控制策略，并将其分配给各个组织单元中的不同账户。为了便于说明，我们将创建两个组织单元，分别命名为“dev”和“prod”。然后，我们将邀请团队成员加入我们的组织单元。

> **注意**
> 要模拟团队成员，可以选择创建临时电子邮件地址并将其添加到你的组织单元中。

完成注册并登录后，你将看到`AWS`管理控制台页面。界面如下所示：

![img/615530_1_En_15_Fig2_HTML.jpg](img/615530_1_En_15_Fig2_HTML.jpg)

`AWS`控制台首页截图。用户位于欧洲区域。没有显示最近访问过的服务。用户可以探索常用服务，如`EC2`、`S3`、`RDS`或`Lambda`。

图 15-2：`AWS`控制台首页

将光标导航到搜索区域，输入“`AWS Organizations`”，然后点击“创建新组织”。你将看到以下界面：

![img/615530_1_En_15_Fig3_HTML.jpg](img/615530_1_En_15_Fig3_HTML.jpg)

`AWS Organizations`界面截图，显示了组织的账户列表，包括根账户和一个名为`Dev sandbox`的组织单元。

图 15-3：在`AWS Organizations`服务下创建新组织

下一步是创建一个组织单元。你会注意到屏幕中央显示的组织结构。请确保选中“Root”，然后点击“操作”，接着选择“创建组织单元”。

![img/615530_1_En_15_Fig4_HTML.jpg](img/615530_1_En_15_Fig4_HTML.jpg)

`AWS`账户窗口截图。在“组织结构”下，已选中“Root”选项。右侧的组织单元下，“创建新项”选项被高亮显示。

图 15-4：创建新的组织单元

接下来是创建服务控制策略。服务控制策略是一种组织策略，用于管理组织内的权限。这些策略允许对组织内的成员进行受控访问。现在，我们将通过点击相应的按钮来启用服务控制策略，如下所示：

![img/615530_1_En_15_Fig5_HTML.jpg](img/615530_1_En_15_Fig5_HTML.jpg)

`AWS`服务控制策略窗口截图，用于启用对组织内账户权限的集中管理。这有助于确保符合访问控制指南。

图 15-5：启用服务控制策略

现在我们已经启用了服务控制策略，接下来为我们的组织创建一个新策略，点击“创建策略”：

![img/615530_1_En_15_Fig6_HTML.jpg](img/615530_1_En_15_Fig6_HTML.jpg)

`AWS`服务控制策略窗口截图。它显示了用于管理`AWS`账户内权限的服务控制策略的相关信息。该页面包含一个可用策略列表，其中包括一个名为`full AWS access`的策略，该策略允许访问所有操作。

图 15-6：创建新的服务控制策略

你将可以选择众多`AWS`服务中的一项，并为该策略选择适当的权限以及资源。选择好合适的策略后，点击“保存更改”。`AWS`将根据我们在此处的选择自动创建一个JSON文件。策略创建过程如下所示：

![img/615530_1_En_15_Fig7_HTML.jpg](img/615530_1_En_15_Fig7_HTML.jpg)

网页截图，左侧显示一个JSON代码块，右侧显示一个表单。JSON代码块定义了一个托管区块链资源的策略文档。右侧的表单允许用户编辑策略语句、添加操作、选择服务以及筛选服务。

图 15-7：配置新的服务控制策略

创建好这些策略后，就可以将策略附加到账户了。为此，请转到主策略页面，选择合适的策略，点击“操作”，然后选择“附加策略”，将策略附加到相应的组织单元。操作如下所示：

![img/615530_1_En_15_Fig8_HTML.jpg](img/615530_1_En_15_Fig8_HTML.jpg)

`AWS Organizations`界面截图，显示了组织单元和账户的层级结构。用户正在将`Dev`策略附加到组织内的一个或多个目标。组织结构包括一个根、`Dev`和`Prod`。

图 15-8：将新的服务控制策略附加到`AWS`组织单元

一旦相应的策略被附加，你可以登录用户的账户来检查策略是否已生效。现在访问管理控制台，查找“IAM Identity Center”。请确保你使用的是创建`AWS`组织的账户登录。`AWS IAM Identity Center`在`AWS`内提供单点登录（`SSO`）服务。你需要启用`AWS IAM Identity Center`。页面如下所示：

![img/615530_1_En_15_Fig9_HTML.jpg](img/615530_1_En_15_Fig9_HTML.jpg)

`IAM Identity Center`窗口截图。它是一个用于管理员工对多个`AWS`账户和云应用程序访问权限的工具。右侧显示了一个启用它的选项。

图 15-9：启用`IAM Identity Center`

> **注意**
> 单点登录服务允许仅使用一个登录凭据即可集中进行身份验证和授权，以访问和使用众多服务、程序和软件。通常称为`SSO`，你只需登录一次，即可获得访问各种服务的权限。`SSO`被认为是安全的，可审计的（因为它们提供日志记录），并且确实有助于组织满足法规遵从性要求。著名的例子包括`SAML`、`OAuth`、`Okta`等。

启用身份中心后，你需要创建权限集。权限集是用于创建身份和访问管理策略的模板。权限集允许向组织内的组和用户授予对各种服务的访问权限。操作如下所示：

![img/615530_1_En_15_Fig10_HTML.jpg](img/615530_1_En_15_Fig10_HTML.jpg)

`AWS`控制台中的`IAM Identity Center`截图。它显示了一个空的权限集列表。有一个用于创建新权限集的按钮。该界面还包括用于管理实例、用户、组、设置、多账户权限、应用程序分配和应用程序的部分。

图 15-10：`IAM Identity Center`首页

现在我们将创建一个新的权限集。让我们尝试使用一个由Amazon预定义的权限集。为了便于说明，我们选择了“DatabaseAdministrator”权限集。操作如下所示：

![img/615530_1_En_15_Fig11_HTML.jpg](img/615530_1_En_15_Fig11_HTML.jpg)

权限集类型窗口截图。在顶部，“类型”下选择了“预定义权限集”选项。在底部，“预定义权限集的策略”下选择了“数据库管理员”选项。

图 15-11：选择预定义的权限集

这将允许你输入关于该权限集的更多详细信息。此步骤成功后，你将看到以下界面：

![img/615530_1_En_15_Fig12_HTML.jpg](img/615530_1_En_15_Fig12_HTML.jpg)

`IAM`身份中心页面截图展示了用于管理实例的权限集。页面包含仪表盘、用户、组、设置、`AWS`账户、权限集、应用程序等部分。主区域列出了权限集及其名称、描述，以及创建和删除等操作。

图 15-12：创建新的权限集

现在我们将创建一个用户。思路是创建用户，并为不同用户分配不同的权限集。你也可以将用户分配到组，并为组分配权限集。不过这是一个可选步骤。具体操作如下：

![img/615530_1_En_15_Fig13_HTML.jpg](img/615530_1_En_15_Fig13_HTML.jpg)

`AWS`控制台截图。其中包含一个表单，用于将用户添加到权限集，分为两个步骤：1. 选择用户和组；2. 选择权限集。提交选项位于右下角。

图 15-13：在`IAM`身份中心内创建新用户

现在，我们需要将用户、权限集和组织连接起来；重新访问`AWS Organizations`，选择相应的组织，然后选择用户并为该用户分配权限集。`AWS Organizations`的界面如下：

![img/615530_1_En_15_Fig14_HTML.jpg](img/615530_1_En_15_Fig14_HTML.jpg)

`IAM`身份中心用户管理页面截图显示了一个名为`John Doe underscore d b admin`的用户，显示名称为`John Doe`，状态为已启用。该用户列在用户部分，并提供了添加或删除用户、查找用户以及管理多账户权限的选项。

图 15-14：连接新用户、权限集和组织

点击“分配用户或组”，然后选择我们创建的用户。点击“下一步”后，为相应组织中的该用户选择权限集。点击“提交”，等待几秒钟，让`AWS`为该组织中的该用户创建权限集。最终界面如下：

![img/615530_1_En_15_Fig15_HTML.jpg](img/615530_1_En_15_Fig15_HTML.jpg)

`AWS`账户窗口截图。`root`、`Dev`和`Prod`的文件位于组织架构下。右上角是“分配用户或组”的选项。

图 15-15：将用户分配到组织

完成上述步骤后，在`AWS IAM`身份中心点击“仪表盘”，获取该用户的`AWS`访问门户。界面如下：

![img/615530_1_En_15_Fig16_HTML.jpg](img/615530_1_En_15_Fig16_HTML.jpg)

`IAM`身份中心设置摘要截图，显示了实例名称、区域、组织ID、`AWS`访问门户URL和颁发者URL。

图 15-16：获取相应用户的访问门户

你可以使用`AWS`访问门户URL登录。请使用我们通过`AWS IAM`身份中心设置的用户凭证，而不是root凭证。建议将此链接加入书签。

## 安装 AWS CLI

`AWS CLI`是命令行界面，适用于Windows、Linux和MacOS等主流操作系统。详细说明请参考`AWS`文档网站。设置好`AWS`组织后，接下来需要配置所谓的“单点登录”解决方案。

要配置命令行界面，使用`AWS SDK`或REST API连接到`S3`存储桶，我们需要设置安全凭证以获取访问密钥和秘密密钥。要创建访问密钥和密码，只需访问“身份和访问管理”门户，找到“安全凭证”部分，并按照说明创建访问密钥和秘密密钥。这些密钥将显示为一长串数字和字符。

要配置命令行界面，请输入：

```
```

# AWS 配置与核心服务介绍

## AWS 命令行配置
运行以下命令开始配置：
```
aws configure
```
系统会要求你输入以下内容：
```
AWS Access Key ID [None]:
AWS Secret Access Key [None]:
Default region name [None]:
Default output format [None]:
```
并提供所需的凭证以继续。

配置完成后，你可以执行诸如列出存储桶、在存储桶之间移动对象等命令。以下是一个列出 S3 服务中所有存储桶的命令：
```
aws s3 ls
```
以下是一个列出指定存储桶中所有文件的命令：
```
aws s3 ls s3://<bucket-name>
```

## AWS S3
AWS S3 代表简单安全存储（Simple Secure Storage），是一种对象存储服务。S3 允许保留非结构化数据。S3 被认为针对“多次读取、一次写入”的使用场景进行了优化。与将数据存储为文件的文件系统不同，S3 以对象的形式存储数据。它是存储照片、视频和类似内容的绝佳选择。AWS 提供了编程接口，允许应用程序对存储在 S3 对象存储上的数据执行创建、更新、读取和删除操作。

Amazon 在其 S3 服务中提供了多种存储类型。Amazon S3 Standard 适用于读取量大的数据，是追求高吞吐量和低延迟的应用程序的理想选择。Amazon S3 IA（低频访问）适用于访问和读取频率不高的数据；理想的应用场景包括业务恢复和灾难规划、长期备份等。Amazon S3 Glacier 适用于可能永远不会被访问但需要保留的数据；应用场景包括生命周期结束的数据、为监管和合规目的而保留的备份等。

### 注意
让我们学习几个术语。在云对象存储的上下文中，我们有两个术语用于描述访问模式和存储需求。这两个术语分别是“热数据”和“冷数据”。
**热数据** 是一个或多个用户同时频繁访问或使用的数据。这些数据需要随时可用，并且需求持续不断。
**冷数据** 很少被访问；数据包括定期备份和历史数据。提供经济高效的存储（而非像热数据那样优化性能）可能是明智之举。

AWS 支持全局存储桶系统。要创建全局存储桶，必须在给定分区内使用唯一的名称。还可以选择创建可供所有人访问的公共存储桶。以下是创建 AWS S3 存储桶的方法：

![图 15-17 AWS S3 服务主页](img/615530_1_En_15_Fig17_HTML.jpg)
Amazon S3 截图，展示了该服务从任何位置存储和检索任意数量数据的能力。左下角有一个“创建存储桶”按钮。

点击“创建存储桶”，你将进入存储桶创建页面。虽然最好保留默认选项，但以下是一些你可以考虑选择以最适合你的选项：
*   **AWS 区域：** 选择地理上靠近你所在位置的区域。
*   **存储桶类型：** 如果你是 AWS 新手，最好选择通用存储桶。
*   **存储桶名称：** 除了遵循 AWS 的存储桶命名指南外，还应采用包含组织代码、部门代码、项目类型以及它是开发沙箱还是生产存储桶的命名约定；当你需要筛选多个存储桶时，这将大有帮助。

界面如下：

![图 15-18 创建新存储桶](img/615530_1_En_15_Fig18_HTML.jpg)
Amazon S3 控制台截图显示了一个通用存储桶列表。用户当前正在查看名为 `bprdevbucket1` 的存储桶，位于美国东部区域。该存储桶及其内容均不公开。

### 上传文件
以下是一个使用 AWS 命令行界面在指定存储桶中上传对象（文件）的示例：
```
aws s3api put-object \
    --bucket <bucket-name> \
    --body /Users/me/<local-file-name> \
    --key destination-file-name
```
以下是使用 AWS SDK 上传文件的方法：
```
import boto3
import configparser

config = configparser.ConfigParser()
path = '/Users/pk/Documents/de/aws/s3/.aws/credentials.cfg'
config.read(path)
aws_access_key_id = config.get('aws','access_key')
aws_secret_access_key = config.get('aws','access_secret')
aws_region = 'us-east-1'

session = boto3.Session(
    aws_access_key_id=aws_access_key_id,
    aws_secret_access_key=aws_secret_access_key,
    region_name=aws_region
)

# create a S3 client
s3_client = session.client('s3')
file_to_upload = '/Users/pk/Documents/abcd.csv'
bucket_name = 'bpro-dev-bucket-1'

try:
    s3_client.upload_file(
        file_to_upload,
        bucket_name,
        'uploaded_abcd.csv'
    )
    print(f"The file {file_to_upload} has been uploaded successfully \
in {bucket_name} S3 bucket")
except Exception as e:
    print(f"Exception {e} has occurred while uploading \
{file_to_upload}")
```
在上述示例中，我们将 AWS 凭证存储在一个配置文件中，并从该文件中调用所需的访问密钥和访问密钥。一种良好的安全实践是创建一个隐藏文件夹来存储凭证。隐藏文件夹可以提供一定程度的安全性，但请记住，任何拥有开发环境 root 访问权限的人都可以看到这些凭证。
当你使用像 `git` 这样的版本控制系统时，默认情况下，将文件添加到仓库时不会包含隐藏文件夹。在你的 `git` 仓库中拥有一个 `.gitignore` 文件也非常重要，该文件用于指定要忽略哪些文件。

以下是一个非常基础的 `.gitignore` 文件：
```
# 忽略敏感或机密信息（例如，凭证）
secretsconfig.json
topsecretpasswords.txt
credentials.cfg
```
不要忘记将 `.gitignore` 文件放在目录的根目录下，并提交该文件，这样其他 fork 该项目的人也能受益。请确保根据 `.gitignore` 忽略某些文件不会以任何方式影响代码。

## AWS 数据系统
AWS 为用户提供了多种数据系统选项。在本节中，你将了解最常见或最广泛采用的服务。

### Amazon RDS
RDS 代表关系数据库服务。顾名思义，Amazon RDS 提供关系数据库，并支持多种数据库引擎选项。其中一些包括但不限于 `MySQL`、`PostgreSQL`、`Oracle`、`MS SQL Server` 等。AWS RDS 易于在几分钟内设置，易于操作，并且可以在必要时进行扩展。AWS 将管理常见的数据库管理任务，因此你无需担心维护问题。
对于那些希望将数据库从本地环境迁移到云端的人来说，AWS RDS 是一个主要选择。通过为迁移选择合适的数据库引擎，可以在关系数据库系统传输过程中实现更快的速度和更高的可靠性。

让我们来看看如何创建一个新的数据库实例。你可以通过在 AWS 控制台中搜索“RDS”，打开相应页面，然后选择“创建数据库”来启动该过程。你可以选择通过自定义可用性、安全性、维护等各个选项来创建新数据库，也可以根据你的用例选择 AWS 推荐的配置。
你还可以指定此实例的性质：是需要一个高可用性的生产数据库实例，还是一个性能有限的开发沙箱即可满足需求。以下是可能呈现的样子：

![图 15-19 在 AWS RDS 中创建新的数据库实例](img/615530_1_En_15_Fig19_HTML.jpg)
Amazon RDS 配置页面的截图。顶部选择了“简易创建”选项。中间部分，在引擎类型下选择了 Aurora 选项。底部，在数据库实例大小下选择了“开发/测试”选项。

此外，请确保在安全配置中添加一条入站网络规则，允许所有传入 IP 通过 TCP/IP 进行通信。你可以在 `VPC` 选项卡下找到此选项。以下是可能呈现的样子：

![图 15-20 允许所有传入 IP 通过 TCP/IP 进行通信（仅用于学习和开发目的）](img/615530_1_En_15_Fig20_HTML.jpg)
安全组规则配置界面的截图。它有一个包含五列两行的表格。列标题如下：安全组规则 ID、类型、协议、端口范围和源。左下角是“添加规则”选项。

以下是一个连接到 AWS RDS 并将数据上传到表的 Python 应用程序：
```
import csv
import psycopg2
import configparser

# CONFIG
config = configparser.ConfigParser()
```

# Amazon Redshift

Redshift 是一个大规模可扩展的云数据仓库。它允许您加载 PB 级的数据，执行复杂的分析查询以使用 SQL 检索数据，并连接商业智能应用程序以呈现报告和仪表板。当数据仓库环境处于空闲状态时，您无需付费，因为只有在您通过传递查询或加载数据与 Redshift 积极交互时才会产生费用。Amazon Redshift 基于开放标准的 PostgreSQL；但是，它已被定制以支持在线分析处理应用程序和其他读取密集型应用程序。

Amazon Redshift 是一个类似列存储的数据仓库，具有可扩展性，并由 AWS 完全管理。以列存储方式存储数据可减少 Redshift 上的输入输出操作次数，并优化分析查询的性能。此外，您还可以指定 Redshift 中的数据被压缩，从而进一步减少存储需求，甚至优化查询。当您对压缩数据执行查询时，数据会被解压缩并运行。

Amazon Redshift 实例以集群形式创建。一个集群有一个领导节点和多个计算节点。领导节点管理通信，包括与客户端应用程序和计算节点的通信。对于用户提供的分析查询，领导节点会创建一个执行计划，并列出返回结果所需的执行步骤。领导节点编译查询并将一部分执行任务分配给计算节点。领导节点会将查询分配给包含查询中提到的底层表的计算节点。

计算节点本身被划分为多个切片。每个切片在计算节点内分配一部分物理内存和存储空间。因此，当领导节点将工作分配给计算节点时，工作实际上会到达给定计算节点内的给定切片。然后，计算节点会在集群内并行处理任务。一个集群可以包含一个或多个数据库，数据存储在计算节点中。

## 创建工作组和集群

让我们尝试设置一个新的 Amazon Redshift 集群。首先，我们必须创建一个工作组。Redshift 中的工作组是一组计算资源，还包括 VPC 配置、子网组、安全组和一个端点。让我们访问 AWS 管理控制台并搜索 Redshift。进入主页面后，我们选择创建一个工作组。您将被引导至一个页面，您可以在其中指定工作组的 RPU。RPU 是 Redshift 处理单元，是可用计算资源的度量单位。您还可以选择 VPC 配置和子网组。

![](img/615530_1_En_15_Fig21_HTML.jpg)

图 15-21 在 Amazon Redshift 中创建工作组

指定了各种参数后，您可能希望导航到预置集群仪表板以创建一个新集群。创建新集群时，您可以指定集群的名称、希望在集群上拥有的节点数量，以及希望为数据仓库集群获得的计算类型、RAM 和存储。

![](img/615530_1_En_15_Fig22_HTML.jpg)

图 15-22 在 Amazon Redshift 中创建新集群

您还可以在创建 Redshift 集群时指定数据库凭据。如果您希望开始使用示例数据查询 Redshift 集群，可以单击“加载示例数据”。此外，您还可以选择创建一个新角色或将现有的 IAM 角色与其集群关联。

![](img/615530_1_En_15_Fig23_HTML.jpg)

图 15-23 为 Redshift 集群配置数据库凭据

选择其他选项后，您就可以创建新的 Redshift 集群了。单击“创建新集群”后，您可能需要等待几分钟才能创建好集群。集群准备就绪并加载了示例数据后，您可能希望单击集群仪表板页面上的查询编辑器。我们可以选择查询编辑器版本 2，它的界面更简洁。

![](img/615530_1_En_15_Fig24_HTML.jpg)

图 15-24 具有新界面的 Redshift 查询编辑器 2

您可以从查询编辑器编写查询、访问各种数据库，还可以探索 AWS Glue Data Catalog。此外，您可以在查询编辑器中针对 Redshift 集群中的数据创建视图、存储过程和函数。在左上角，您可以选择使用图形用户界面创建新数据库或表，或直接从您的某个 S3 存储桶加载数据。

## 示例：从 S3 加载数据

让我们看看如何使用 Python 将数据从 S3 存储桶加载到 Redshift。以下是一个示例代码，它从 S3 存储桶中获取一个平面文件并将其加载到 Redshift 数据库中：

```python
import boto3
import configparser

config = configparser.ConfigParser()
path = '/Users//credentials.cfg'
config.read(path)

aws_access_key_id = config.get('aws','access_key')
aws_secret_access_key = config.get('aws','access_secret')
aws_region = 'us-east-1'

s3_bucket_name = 'your-bucket'
s3_key = 'key'
table_to_be_copied = 'nba_shots.csv'

redshift_cluster_identifier = config.get('redshift','cluster_id')
redshift_database = config.get('redshift','r_dbname')
redshift_user = config.get('redshift','r_username')
redshift_password = config.get('redshift','r_password')
redshift_copy_role_arn = config.get('redshift','redshift_arn')

s3 = boto3.client(
    's3',
    aws_access_key_id=aws_access_key_id,
    aws_secret_access_key=aws_secret_access_key,
    region_name=aws_region
)

redshift = boto3.client(
    'redshift',
    # ... (后续代码)
)
```

# 数据导入示例

以下代码展示了如何读取配置文件并将 CSV 数据导入数据库。

```python
# reading the configuration file
config.read('aws_rds_credentials.cfg')

dbname = config.get("rds", "database_name")
user = config.get("rds", "username")
password = config.get("rds", "password")
host = config.get("rds", "endpoint")
port = config.get("rds", "port")

# Connect to your RDS instance
conn = psycopg2.connect(
    dbname=dbname,
    user=user,
    password=password,
    host=host,
    port=port
)
cur = conn.cursor()

# CSV 文件路径
csv_file_path = "/Users//iris.csv"
try:
    with open(csv_file_path, 'r') as f:
        reader = csv.reader(f)
        next(reader)  # 跳过表头行
        for row in reader:
            cur.execute(
                "INSERT INTO users (id, name, age) VALUES (%s, %s, %s)",
                row
            )
        conn.commit()
        print("数据上传成功")
except:
    print("将数据加载到 AWS RDS 时出错")
finally:
    cur.close()
    conn.close()
```

```python
aws_access_key_id=`aws_access_key_id`,
aws_secret_access_key=`aws_secret_access_key`,
region_name=`aws_region`
)

`copy_query` = f"""
COPY `{table_to_be_copied}`
FROM 's3://`{s3_bucket_name}`/`{s3_key}`'
IAM_ROLE '`{redshift_copy_role_arn}`'
FORMAT AS CSV;
"""

def `execute_redshift_query`(`query`):
    try:
        `redshift_data` = `boto3`.client(
            'redshift-data',
            aws_access_key_id=`aws_access_key_id`,
            aws_secret_access_key=`aws_secret_access_key`,
            region_name=`aws_region`
        )
        `response` = `redshift_data`.execute_statement(
            ClusterIdentifier=`redshift_cluster_identifier`,
            Database=`redshift_database`,
            DbUser=`redshift_user`,
            Sql=`query`,
            WithEvent=True
        )
        return `response`
    except Exception as e:
        print(f"执行 Redshift 查询时出错: {e}")
        return None

`response` = `execute_redshift_query`(`copy_query`)
print(`response`)
```

# AWS 数据湖核心服务

## Amazon Athena

Amazon Athena 是一项交互式查询服务，允许您直接使用标准 SQL 语法分析对象存储 AWS S3 中的数据。使用 Amazon Athena，您还可以选择使用 Apache Spark 直接在 S3 存储桶上运行数据分析。无需设置底层的 Spark 基础设施或数据库连接驱动程序。Amazon 会为您处理所有这些事宜。Amazon Athena 是一项无服务器服务，因此您只需按使用量付费，即按请求数量付费。

由于您能够直接查询对象存储中的存储桶，您现在可以分析存储桶内的非结构化、半结构化和结构化数据。数据可以是任何格式，包括但不限于 CSV、JSON、Parquet 和 Apache ORC。所有这些都可以使用 Athena 进行分析。通过使用 Amazon Athena，您可以查询 S3 中的各种数据；因此，您可以将 S3 用作数据湖，而 Athena 则作为数据湖的查询层。查询将自动并行化并执行，提供原生更快的性能。

要开始使用 Amazon Athena，请在 AWS 管理控制台中搜索 Athena，并选择查询 S3 存储桶数据的选项（SQL 或 Spark）。以下是您可能看到的界面：

![img/615530_1_En_15_Fig25_HTML.jpg](img/615530_1_En_15_Fig25_HTML.jpg)
*图 15-25：AWS Athena 主页。它显示了 Athena 主页，提供了使用 Trino SQL 查询数据或使用 PySpark 和 Spark SQL 分析数据的选项。*

您还可以通过与 Jupyter Notebook 交互，在 S3 存储桶中的对象上使用 Spark SQL。以下是您可能看到的界面：

![img/615530_1_En_15_Fig26_HTML.jpg](img/615530_1_En_15_Fig26_HTML.jpg)
*图 15-26：AWS Athena — Jupyter Notebook 工作区。显示了一个包含 Python 和 SQL 代码示例的笔记本编辑器。Python 代码打印了 Hello World，SQL 查询从指定的存储桶中选择数据。该界面还包括用于运行代码、创建新笔记本和探索数据源的按钮。*

## Amazon Glue

Amazon Glue 是一项无服务器数据集成工具。您可以使用 Glue 发现数据源、集成数据，并为数据分析、机器学习和应用程序开发准备数据集。Amazon Glue 拥有 Glue Studio，它允许您以可视化方式准备数据集成作业，您可以将组件拖放到工作区中。设计好作业后，您可以直接从 Glue 环境中运行和监控这些作业。您可以根据计划时间或事件触发作业。

## AWS Lake Formation

AWS Lake Formation 是一项数据湖服务。数据湖是结构化和非结构化数据可以共存、查询和分析的地方。它简化了数据湖的构建和管理。与需要多年的项目不同，AWS Lake Formation 可以让您更快地投入生产。AWS Lake Formation 提供了一个数据编目工具，用于捕获各种数据集的元数据，以便您可以有效地搜索和检索给定的数据集。

AWS Lake Formation 将数据存储在 S3 存储桶中，无论其是结构化还是非结构化数据。使用 Lake Formation 创建数据湖的关键方面之一是列出各种数据源，无论其数据类型如何，以及将应用于数据湖的安全和访问策略。

让我们看一个将 Lake Formation 与 Athena 和 Glue 结合使用的简单示例。

### 创建 S3 存储桶

首先，让我们在对象存储中创建一个新的存储桶：

![img/615530_1_En_15_Fig27_HTML.jpg](img/615530_1_En_15_Fig27_HTML.jpg)
*图 15-27：为 Lake Formation 项目创建新的 S3 存储桶。显示了 Amazon S3 存储桶创建表单，通用选项已选中。*

让我们创建两个文件夹，一个用于源数据，另一个用于在数据湖存储桶中运行基于 Glue 的报告。以下是您可能看到的界面：

![img/615530_1_En_15_Fig28_HTML.jpg](img/615530_1_En_15_Fig28_HTML.jpg)
*图 15-28：在对象存储中创建文件夹。显示了 Amazon S3 存储桶 `oly-data-lake` 的截图，其中包含两个对象，并列出了用于管理对象的选项。*

### 配置 Lake Formation 数据库

现在让我们配置数据湖。我们将导航到 AWS 管理控制台并搜索 Lake Formation。我们将在 AWS Lake Formation 中创建一个数据库，并从 S3 存储桶配置我们的数据源文件夹。创建一个数据库，指定一个名称，并为数据库选择数据源位置。以下是您可能看到的界面：

![img/615530_1_En_15_Fig29_HTML.jpg](img/615530_1_En_15_Fig29_HTML.jpg)
*图 15-29：在 Lake Formation 中创建新数据库。用户正在创建一个名为 `data-lake` 的数据库，其数据源位置为 S3 路径 `s3://oly-data-lake/data-sources`。*

### 创建 IAM 角色与配置 Glue

现在让我们配置 AWS Glue，以便我们可以自动编目数据源存储桶中的表。在此之前，我们需要在 AWS 中创建一个 IAM 角色，AWS Glue 将使用该角色来创建爬网程序。AWS Glue 中的爬网程序是一个可以从多个源抓取或获取数据的程序。

首先，让我们在 AWS 管理控制台中查找 IAM，导航到该服务，然后单击“角色”以创建一个角色。以下是您可能看到的界面：

![img/615530_1_En_15_Fig30_HTML.jpg](img/615530_1_En_15_Fig30_HTML.jpg)
*图 15-30：在 IAM 中创建新角色。显示了 IAM 角色页面，角色列表已列出。*

对于受信任的实体类型，选择“AWS 服务”，对于用例，单击下拉菜单并选择“Glue”。以下是您可能看到的界面：

![img/615530_1_En_15_Fig31_HTML.jpg](img/615530_1_En_15_Fig31_HTML.jpg)
*图 15-31：在 IAM 中选择受信任的实体类型。显示了选择 `glue` 作为用例的界面。*

以下是此 IAM 角色所需的权限；选择一个合适的名称并创建 IAM 角色：

![img/615530_1_En_15_Fig32_HTML.jpg](img/615530_1_En_15_Fig32_HTML.jpg)
*图 15-32：为新 IAM 角色添加权限。显示了步骤 2 “添加权限”页面和步骤 3 “添加标签”页面。*

现在，我们将授予这个新创建的角色在我们的 Lake Formation 中创建目录的权限。导航到 AWS Lake Formation，选择我们的数据湖，并从“操作”中授予权限。以下是您可能看到的界面：

![img/615530_1_En_15_Fig33_HTML.jpg](img/615530_1_En_15_Fig33_HTML.jpg)
*图 15-33：在 Lake Formation 中为新创建的角色授予权限。显示了数据库列表和“授予”操作。*

选择新创建的 IAM 角色，并在命名的数据目录资源下选择数据湖。在数据库权限中，也提供创建表访问选项。

现在让我们找到 AWS Glue 服务。您可以在 AWS 管理控制台中搜索 Glue，或者单击 AWS Lake Formation 中的爬网程序选项直接导航到 AWS Glue 服务。

![img/615530_1_En_15_Fig34_HTML.jpg](img/615530_1_En_15_Fig34_HTML.jpg)
*图 15-34：在权限页面中选择 IAM 用户。显示了“授予数据湖权限”窗口，选择了 IAM 用户和角色及命名的数据目录资源。*

### 创建与运行爬网程序

我们将创建一个新的爬网程序，在其中我们将指定数据湖存储桶中的 `data-sources` 文件夹作为输入，分配新创建的 `dl-glue` IAM 角色，并为目标数据库选择 `data-lake` 数据库。

以下是创建爬网程序的方法：

![img/615530_1_En_15_Fig35_HTML.jpg](img/615530_1_En_15_Fig35_HTML.jpg)
*图 15-35：使用指定的源和 IAM 角色创建新爬网程序。显示了创建爬网程序的步骤：选择数据源和分类器、配置安全设置、设置输出和调度。*

创建爬网程序后（可能需要几分钟），您可以在 AWS Glue 中看到您新创建的爬网程序。

选择爬网程序并单击“运行”。您可能需要再等几分钟让爬网程序运行。

在此过程中，AWS Glue 爬网程序将推断来自数据源文件夹的输入数据集的结构，并将元数据存储在数据目录中。以下是您可能看到的界面：

![img/615530_1_En_15_Fig36_HTML.jpg](img/615530_1_En_15_Fig36_HTML.jpg)
*图 15-36：在 AWS Glue 中初始化新爬网程序。显示了爬网程序窗口及其运行状态表格。*

### 使用 Athena 查询数据

到目前为止，我们已经成功设置了 AWS Lake Formation，将数据源指向 AWS S3 存储桶，使用 AWS IAM 创建了必要的服务账户，配置并运行了 AWS Glue 爬网程序，并填充了数据目录。我们现在将使用 AWS Athena 查询数据以提取洞察。

访问 AWS 管理控制台以导航到 AWS Athena 服务。选择使用 Trino SQL 查询数据的选项，然后单击“启动查询编辑器”。以下是您可能看到的界面：

![img/615530_1_En_15_Fig37_HTML.jpg](img/615530_1_En_15_Fig37_HTML.jpg)
*图 15-37：AWS Athena 查询编辑器。显示了空白查询区域，数据源设置为 `AwsDataCatalog`，数据库设置为 `data-lake`。列出了两个表：`athlete_events_csv` 和 `noc_regions_csv`。*

现在让我们设置一个输出文件夹，用于存储查询结果。在顶部，单击“设置”选项，然后单击“管理设置”。单击“浏览 S3”选项，导航到数据湖 S3 存储桶并选择“Output datasets”选项。保存设置。

现在，我们准备好在 AWS Athena 中编写查询并从数据集中提取洞察。让我们编写一个简单的查询：

```sql
```

# 使用 AWS Athena 查询数据湖

## SQL 查询示例

```sql
select
    distinct year, season, city
from
    events_csv
where
    year < 2020
order by year desc
```

以下是它在编辑器中的样子：

![一个 Amazon Athena 查询编辑器的截图，显示了一个 SQL 查询，用于从 data-lake 数据库的 events_csv 表中选择不同的年份、季节和城市，过滤 2020 年之前的年份，并按年份降序排列结果。](img/615530_1_En_15_Fig38_HTML.jpg)

图 15-38 使用 AWS Athena 查询数据湖

该查询将产生以下结果：

![一个结果窗口的截图。它有一个包含四列六行的表格。列标题如下：#、year、season 和 city。复制和下载结果的选项在右上角。](img/615530_1_En_15_Fig39_HTML.jpg)

图 15-39 在 AWS Athena 中检索结果

正如我们之前提到的，此查询的结果已存储在我们之前指定的 S3 存储桶下的文件夹中。让我们导航到该文件夹查看查询结果。您将看到一个按日期、月份和年份创建的复杂文件夹结构层次。找到 CSV 文件类型并单击“使用 S3 Select 查询”。您将看到一个已填充的简单查询，因此只需选择运行查询即可获得结果。以下是您可能看到的界面：

![一个 SQL 查询结果的截图，显示了来自 s3 对象表的数据。查询成功返回了 183 条记录，耗时 2278 毫秒。结果格式化为三列：year、season 和 city。](img/615530_1_En_15_Fig40_HTML.jpg)

图 15-40 S3 存储桶内文件夹中的查询结果

## AWS SageMaker

Amazon SageMaker 是 AWS 提供的一项完全托管的机器学习服务。它使您能够大规模地构建、训练、测试和部署机器学习模型。您还可以选择开发无代码或低代码的机器学习模型。AWS SageMaker 支持行业领先的机器学习工具包，如 `scikit-learn`、`TensorFlow`、`PyTorch`、`Hugging Face` 等。

AWS SageMaker 有几种变体。AWS SageMaker Studio 带有一个功能齐全的 ML 集成开发环境。它附带一个 Jupyter notebook，让您可以控制模型构建的每一步。AWS SageMaker Studio Lab 是 AWS SageMaker Studio 的一个用户友好、简化版本，但硬件支持有限。您将获得 16 GB 的物理内存和 15 GB 的存储空间，并且无法扩展，而 SageMaker Studio 允许您指定更大的计算能力和内存。还有 SageMaker Canvas，这是一个用于开发预测模型的低代码环境。

### 设置 SageMaker 域

要设置 AWS SageMaker Studio，请转到服务页面，并选择“设置 SageMaker 域”选项。以下是您可能看到的界面：

![一个 Amazon SageMaker 配置页面的截图。系统提示用户创建一个角色并配置 SageMaker 域。在“配置 SageMaker 域”下选择了“快速设置”选项。](img/615530_1_En_15_Fig41_HTML.jpg)

图 15-41 设置 SageMaker 域

一旦为您创建了 SageMaker 域，您就可以从该域启动 SageMaker Studio。以下是您可能看到的界面：

![一个 Amazon SageMaker 域详细信息页面的截图，显示了用户配置文件、管理员配置和域设置。用户当前正在查看“用户配置文件”选项卡，该选项卡列出了域的创建和修改日期。](img/615530_1_En_15_Fig42_HTML.jpg)

图 15-42 启动 AWS SageMaker Studio

以下是 SageMaker 可能呈现给您的样子：

![一个 SageMaker Studio 窗口的截图。它显示了主页，其中包含启动工作流、管理应用程序和空间以及查看入门材料的选项。有 Jupyter Lab、R Studio 和 Canvas 部分，以及代码编辑器和 Studio 配置选项。](img/615530_1_En_15_Fig43_HTML.jpg)

图 15-43 AWS SageMaker 主页

### 构建和部署机器学习模型

让我们尝试使用 Amazon SageMaker 构建和部署一个机器学习模型。我们将构建一个简单的机器学习模型，将其托管在一个实例上，创建一个可用于与模型交互的 API，并创建一个可以访问该端点的 Lambda 函数。

首先，让我们打开 JupyterLab 并选择新的 JupyterLab 空间。您将为 JupyterLab 实例提供一个名称，然后继续选择底层硬件。我的建议是使用实例 `ml.t3.medium`，因为当您开始使用 SageMaker 时，可以获得 250 小时的免费计算时间。“映像”选项基本上是一个为机器学习任务适当配置的 Docker 镜像。

例如，设置可能如下所示：

![一个 SageMaker Studio 窗口的截图。它显示了一个名为 Second 的 Jupyter Lab 空间。该空间当前已停止，使用 ml.t3.medium 实例和 SageMaker Distribution 1.6。有一个空间设置部分，包括存储和生命周期配置选项。](img/615530_1_En_15_Fig44_HTML.jpg)

图 15-44 在 AWS SageMaker 上创建和配置新的 Jupyter 空间

对于我们的简单示例，我们将选择泰坦尼克号数据集，其结果是船撞上冰山时乘客幸存的可能性。出于说明目的，我们选择了一个更简单的数据集，并删除了一些列。我们正在导入必要的库，预处理数据集，运行 SageMaker 的线性学习器模型，并创建一个可以托管在另一个实例上的端点。我们使用 Amazon SageMaker 的线性方法来获取模型。

以下是代码：

```python
import sagemaker
from sagemaker.sklearn.model import SKLearnModel
from sagemaker import get_execution_role
import numpy as np
import pandas as pd

df = pd.read_csv("titanic.csv")
df = df.dropna(
    subset=["Age"]
)
df = df.drop(
    ['Name','Sex','Ticket','Cabin','Embarked'],
    axis=1
)
df.to_csv("titanic_subset.csv")

rawdata = np.genfromtxt(
    "titanic_subset.csv",
    delimiter=',',
    skip_header=1
)

train = rawdata[:int(len(rawdata) * 0.8)]
test = rawdata[int(len(rawdata) * 0.8):]

Xtr = train[:, :-1]
Ytr = train[:, -1]
Xts = test[:, :-1]
Yts = test[:, -1]

linear = sagemaker.LinearLearner(
    role = get_execution_role(),
    instance_count = 1,
    instance_type = 'ml.m5.large',
    predictor_type='regressor',
    sagemaker_session=sagemaker.Session()
)

train_data_records = linear.record_set(
    Xtr.astype(np.float32),
    labels=Ytr.astype(np.float32),
    channel='train'
)

linear.fit(train_data_records)

predictor = linear.deploy(
    initial_instance_count=1,
    instance_type='ml.t3.medium'
)
```

让我们在`JupyterLab`笔记本上运行此代码，并记下`SageMaker`生成的端点。我们将把这个端点托管在另一个`SageMaker`实例上。然后，我们将有一个`Lambda`函数来调用托管的API。`Lambda`函数通常具有关联的特定`IAM`角色，以便与其他服务协同工作。

让我们创建一个新的`IAM`角色，用于与机器学习模型交互。让我们在`AWS`管理控制台中找到`IAM`，在左侧找到“身份和访问管理”选项，单击“角色”；创建一个新角色，并为`Lambda`、`SageMaker`和`CloudWatch`服务分配完全访问权限。以下是您可能看到的界面：

![图 15-45 为ML创建新的IAM角色](img/615530_1_En_15_Fig45_HTML.jpg)

一个选项卡的截图。在左侧窗格中，在“身份和访问管理”下选择了“角色”选项。在右侧，它在“权限策略”下有一个包含两列三行的表格。列标题如下：策略名称和类型。

现在让我们创建一个`Lambda`函数。导航到`AWS`管理控制台并搜索“Lambda”服务。选择创建函数，选择“从头开始创作”选项，选择`Python`作为运行时配置，并保持架构选择不变。以下是`AWS Lambda`中“创建函数”页面的样子：

![图 15-46 在AWS Lambda中创建新的Lambda函数](img/615530_1_En_15_Fig46_HTML.jpg)

一个`AWS`控制台中`Lambda`函数创建页面的截图。系统提示用户输入函数名称，选择运行时、架构和权限。在运行时和架构下分别选择了`Python 3.10`和`x86_64`。

确保通过使用现有角色来更改默认执行角色，并从下拉选项中选择您创建的`IAM`角色。`AWS Lambda`函数附带起始代码，其中`Lambda`函数有两个参数，一个接受触发函数的事件，另一个包含运行时参数。以下是`AWS Lambda`中仪表板的样子：

![图 15-47 AWS Lambda仪表板页面](img/615530_1_En_15_Fig47_HTML.jpg)

一个`Lambda`窗口的截图。从左侧窗格中选择了“函数”选项。在右侧，它在“函数”下有一个包含四列两行的表格。列标题如下：函数名称、运行时、架构和上次修改时间。

我们解析JSON字符串以获取将发送到`SageMaker`进行预测的实际数据。然后，我们请求`SageMaker`端点并返回响应。随后再次对响应进行JSON解析并返回预测分数。

以下是`Lambda`函数：

```python
import json, os, boto3

endpoint = os.environ['endpoint']

runtime = boto3.client('runtime.sagemaker')

def lambda_handler(event, context):
    event_data = json.loads(
        json.dumps(event)
    )
    testdata = event_data['data']
    response = runtime.invoke_endpoint(
        EndpointName=endpoint,
        ContentType='text/csv',
        Body=testdata
    )
    result = json.loads(
        response['Body'] \
            .read() \
            .decode()
    )
    return int(result['predictions'][0]['score'])
```

此外，您可能希望通过导航到“配置”选项并选择“环境变量”来为此 Lambda 函数设置环境变量。以下是您可能看到的界面：

![](img/615530_1_En_15_Fig48_HTML.jpg)

一个选项卡的截图。从选项栏中选择了“配置”选项。从左侧窗格中选择了“环境变量”选项。在右侧，它有一个包含两列一行的表格。列标题是键和值。

图 15-48
为 Lambda 函数设置环境变量

现在让我们部署此函数。从 AWS 管理控制台，导航到 API Gateway 服务并选择创建 API。选择构建一个 REST API，它将为您提供对请求和响应的更多控制。输入新 API 的名称，保持端点类型不变，然后选择创建 API。
以下是您可能看到的界面：

![](img/615530_1_En_15_Fig49_HTML.jpg)

一个 AWS API Gateway 控制台的截图，显示了“创建 REST API”页面。系统提示用户选择是创建新 API、克隆现有 API、从 OpenAPI 定义导入还是使用示例 API。API 名称、描述和端点类型字段也可见。

图 15-49
为我们的模型创建 REST API 端点

由于我们将向 API 发送数据以获取预测分数，我们将创建一个 POST 方法，该方法将数据点发布到机器学习 API 并返回预测分数。因此，选择创建方法，选择 POST 作为方法类型，并选择 Lambda 函数作为集成方法。
请从“方法类型”下拉菜单中选择 POST 方法，而不是 PUT 方法。
以下是您可能看到的界面：

![](img/615530_1_En_15_Fig50_HTML.jpg)

一个“创建方法”窗口的截图。用户正在配置一个 HTTP 方法类型为 PUT 的新方法。集成选项包括 Lambda 函数、HTTP、模拟、AWS 服务和 VPC 链接。选择了 Lambda 函数选项。

图 15-50
创建 POST 方法

创建此 POST 方法后，您可以测试 API 功能以查看其是否按预期工作。在 API Gateway 上，导航到“测试”选项，在“请求正文”部分输入要测试的数据，然后单击“测试”。您将获得响应代码和 API 返回的响应。
一切正常后，我们可以继续部署可公开访问的 API。在右上角，选择“部署 API”选项，为“阶段”下拉菜单选择“新阶段”，并输入阶段名称。您可以提及开发、测试、生产或 Alpha、Beta 等。
以下是您可能看到的界面：

![](img/615530_1_En_15_Fig51_HTML.jpg)

一个用于部署 API 的表单截图。系统要求用户选择部署阶段，输入阶段名称，并可选择添加部署描述。当前选择的阶段是“新阶段”，阶段名称是“prod”。

图 15-51
部署 API 以使其可公开访问

# 结论

到目前为止，我希望您对 Amazon Web Services 有了全面的了解，重点关注其数据工程和机器学习能力。我们涵盖了广泛的服务，从简单的对象存储到在云上托管您自己的机器学习模型。我们创建了用于安全访问服务的 IAM 角色；使用 RDS 建立了数据库，使用 Amazon Redshift 建立了数据仓库，使用 Lake Formation 建立了数据湖，使用 AWS SageMaker 建立了机器学习模型；并且还获得了使用 AWS Lambda、AWS Athena 和 AWS Glue 的经验。
这里提供的示例可能看起来使用了相对宽松的 IAM 角色权限。这仅用于说明目的。在生产环境中，您应该遵循最小权限原则，仅授予特定任务所需的特定权限。总的来说，我们涵盖的主题可能对您的项目有所帮助。

# 16. 使用 Google Cloud Platform 构建数据管道

## 引言
在本章中，我们将研究 Google Cloud Platform，它是云计算的早期采用者之一。我们将探索 GCP 的各种服务，重点关注数据工程和机器学习能力。我们将从理解 Google Cloud Platform 的核心概念和查看关键服务开始。然后详细研究 Google Cloud Platform 提供的各种数据系统服务。最后，我们将研究 Google Vertex AI，这是一个完全托管的机器学习平台，用于在一个服务内构建、训练和部署机器学习模型。

在本章结束时，您将学习

-   Google Cloud Platform 的基础知识
-   如何设置 Google CLI 来管理资源
-   Google 的对象存储和用于虚拟机及数据系统的计算引擎
-   Google 的数据处理工具 Dataproc，用于运行 Spark 和 Hadoop 工作负载
-   使用 Google Vertex AI 开发、训练和部署机器学习模型

当您开始探索 Google Cloud Platform 的旅程时，请您务必注意管理云资源，尤其是在您使用完它们之后。在传统基础设施的情况下，您可以在使用完后关闭计算机。然而，在云基础设施的情况下，您通常购买的是基于按需付费模式的计划，即使资源未被使用，您也需要为其付费。注销 GCP 并不意味着关闭您已购买的服务。这对于虚拟机、数据库和任何其他计算密集型资源尤其重要。请务必养成检查活动资源并关闭这些特定资源的习惯。如果您不再计划使用某项服务，可以将其删除。这样，您只需为积极使用的服务付费，从而控制成本。我还要感谢 GCP 为新用户提供免费额度，并积极支持新学习者降低他们的成本。

## Google Cloud Platform

Google Cloud Platform 是 Google 提供的一套云计算服务。Google 早在 2008 年就开始提供云计算，当时他们提供了 App Engine，这是一个用于托管和管理 Web 应用程序的平台，其数据中心完全由 Google 管理。Google 的数据中心遍布全球。它们被指定为区域和可用区。每个区域包含一组可用区。这些可用区彼此隔离。Google Cloud 提供各种服务，这些服务可以是硬件或软件产品或资源。
Google Cloud 有项目的概念。如果您尝试使用 Google Cloud 服务，那么它必须是一个项目的一部分。Google 将项目定义为一个由设置、权限和其他描述您应用程序的元数据组成的实体。每次创建新项目时，Google Cloud 都会为您提供一个项目名称、项目 ID 和项目编号。您可以创建多个项目，并关联到同一个账户进行计费。
在 AWS 中，我们看到了资源层次结构的概念，其中我们有一个根账户，使我们能够通过 AWS Organizations 创建组织单位，并从中创建 AWS 账户，并在这些账户内配置各种服务。在 GCP 中，层次结构始于一个组织资源，它类似于 AWS 根账户。
组织资源控制计费、访问控制列表和管理各种资源。一个组织节点可以有一个或多个文件夹，每个文件夹代表组织内的某种子组织或部门。每个文件夹可以有一个或多个项目。项目代表组织中的最低层次。任何给定的项目都是可以配置虚拟机或存储实例的地方。
出于说明目的，让我们将文件夹设置为 GCP 设置的一部分。您首先需要一个 Google 帐户。我建议您首先创建一个沙箱文件夹，并在其中创建您的项目。当您获得进展后，可以设置一个开发文件夹，并在那里测试您的用例。Google Cloud 可以通过图形用户界面、命令行界面以及提供 API 来管理资源和服务的客户端库进行访问和交互。

## 设置 GCP 账户

让我们从访问 `https://cloud.google.com` 开始。
单击“登录”并按照屏幕上的说明创建新的 Google 帐户或登录现有的 Google 帐户。根据需要输入详细信息，然后继续开始。我们想要进入 Google Cloud 控制台页面（类似于 AWS 管理控制台）。
以下是 Google 控制台页面的可能样子：

![](img/615530_1_En_16_Fig1_HTML.jpg)

一个 Google Cloud Platform 的截图。仪表板页面左侧有项目信息，中间有与 API 相关的信息，右侧有 Google Cloud Platform 状态和与监控相关的链接。

图 16-1
Google Cloud 控制台页面

## Google Cloud Storage

Google 的 Cloud Storage 是一项对象存储服务。Cloud Storage 将数据存储为唯一的对象或 blob。与关系数据库管理系统不同，您无需定义模式即可使用 Cloud Storage。您可以存储数据文件、图像、视频、音频等。这些文件被称为对象。这些对象被放置在称为存储桶的容器中。这些对象可以通过唯一键以 URL 的形式引用。Cloud Storage 有多种数据存储类型和应用；您可以首先存储您网站的内容、以关系表形式收集数据的数据文件、定期存档、您在网站上托管的视频和采访、文档等。这些文件被视为原子性的，这意味着如果您尝试访问一个对象，您访问的是整个对象。
就数据安全而言，您可以利用 Google IAM 和 Cloud Storage 的访问控制列表机制进行身份验证和授权。在 Cloud Storage 中，单个对象也有自己的访问控制列表。对象存储服务的名称必须是唯一的，因为 Cloud Storage 具有全局命名空间。一旦您使用某个名称创建了对象服务，以后就无法修改该名称。但是，您可以重命名存储桶内的对象。
Google 的 Cloud Storage 提供四种不同的存储类别，即：标准存储、近线存储、冷线存储和归档存储。标准存储选项适用于频繁访问且仅存储短时间的数据。近线存储适用于存储访问频率较低、可用性稍低的数据，数据至少存储 30 天。冷线存储的可用性较低，适用于存储不常访问的数据，数据至少存储 90 天。归档存储适用于灾难恢复和长期备份，这些数据访问频率不高，并且服务的可用性可能不如其他三种存储类别。
Cloud Storage 提供对象生命周期管理配置，您可以在其中为给定的存储桶定义和设置条件。例如，您可以根据某个时间阈值更改对象的存储类别。您可以指定某个对象或存储桶应保留的天数。您可以启用对象版本控制，这样同一对象存在多个变体。但是，这会显著增加存储大小和产生的成本，因此建议谨慎使用。

## Google Cloud CLI

Google Cloud 有一个命令行界面，可用于创建和管理 Google Cloud 资源。根据操作系统的不同，安装方法可能有所不同。以下是在 Ubuntu/Debian 机器上进行设置的一些说明：

1.  首先，运行更新命令并安装您的系统可能需要的任何依赖项：

1. 安装必要的依赖包：
   ```
   sudo apt update
   sudo apt-get install apt-transport-https ca-certificates gnupg curl
   ```

2. 导入 Google Cloud CLI 公钥，并将 GCloud CLI 发行版添加为软件包源：
   ```
   curl https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo gpg --dearmor -o /usr/share/keyrings/cloud.google.gpg
   echo "deb [signed-by=/usr/share/keyrings/cloud.google.gpg] https://packages.cloud.google.com/apt cloud-sdk main" | sudo tee -a /etc/apt/sources.list.d/google-cloud-sdk.list
   ```

3. 再次运行更新命令并安装 GCloud CLI：
   ```
   sudo apt-get update && sudo apt-get install google-cloud-cli
   ```

4. 运行 Google Cloud CLI 开始使用：
   ```
   gcloud init
   ```

您需要在浏览器中进行身份验证，输入您的凭据并允许 Google Cloud SDK 访问您的帐户。以下是您可能看到的界面：

![Google Cloud SDK 身份验证页面](img/615530_1_En_16_Fig2_HTML.jpg)

一个 Google Cloud 身份验证页面的截图。左侧显示消息“Google Cloud SDK 想要访问您的 Google 帐户”。右侧显示允许 Google Cloud 的权限。底部有“取消”和“允许”按钮。

图 16-2  
Google Cloud SDK 身份验证页面

如果您使用的是 MS Windows 操作系统，请打开 PowerShell 选项卡并输入以下内容：
```
(New-Object Net.WebClient).DownloadFile("https://dl.google.com/dl/cloudsdk/channels/rapid/GoogleCloudSDKInstaller.exe", "$env:Temp\GoogleCloudSDKInstaller.exe")
& $env:Temp\GoogleCloudSDKInstaller.exe
```

按照 Google 安装程序提供的提示进行操作。打开 Google Cloud CLI 并输入以下内容以开始使用 GCP CLI：
```
gcloud init
```

您已准备好从命令行使用 Google Cloud。

## Google Compute Engine

Google 提供按需虚拟机以满足各种企业应用程序的需求。您可以使用 Compute Engine 虚拟机来建立数据库服务器、构建应用程序、托管网站以及执行各种其他任务。Google 提供多种类型的计算引擎：

*   通用引擎，适用于日常任务
*   存储优化型，工作负载主要涉及存储且消耗较少计算资源
*   计算优化型，优先考虑性能，具有多核和更高的时钟性能
*   内存优化型，每个核心提供更多内存，适用于内存密集型工作负载
*   加速器优化型，适用于 GPU 密集型工作负载、大规模并行 CUDA 工作负载和高性能计算目的

让我们首先启用 Compute Engine API。导航到 Google Cloud 控制台并查找虚拟机选项或 Google Compute 选项；我们需要启用 API 服务以供使用。
以下是您可能看到的界面：

![](img/615530_1_En_16_Fig3_HTML.jpg)

一个裁剪后的 Google Cloud 页面的截图。页面上列出的产品有：创建虚拟机、训练和托管 ML 模型、创建数据库、创建存储桶、运行容器化应用和创建容器化应用。

图 16-3 在 Google Cloud 中创建虚拟机

单击“创建虚拟机”，这将引导我们进入以下页面，您将在其中看到启用 Google Compute Engine API 的选项。
单击“启用”以启用计算 API。

![](img/615530_1_En_16_Fig4_HTML.jpg)

一个裁剪后的 Google Cloud 页面的截图。页面标题为“Compute Engine API”，下方有一个链接“Google Enterprise API”，左侧是其图标。底部是“启用”按钮。

图 16-4 启用 Compute Engine API

启用后，您将能够管理该 API。它看起来像这样：

![](img/615530_1_En_16_Fig5_HTML.jpg)

一个裁剪后的 Google Cloud 页面的截图。页面标题为“Compute Engine API”，下方有一个链接“Google Enterprise API”，左侧是其图标。底部是“管理”按钮。右下角指示了一条已勾选的消息“API 已启用”。

图 16-5 Compute Engine API 已启用

启用 Compute Engine API 后，您可以继续创建新的虚拟机。
现在，我们将创建一个 Linux 虚拟机。
我们将首先创建一个新的计算实例，选择合适的 Linux 发行版和版本，并通过允许传入的 HTTP 流量来启用访问。
首先，让我们访问左侧菜单，找到“Compute Engine”选项，单击“VM 实例”并创建一个新实例。您将看到一个页面，您可以在其中指定实例的名称、位置和类型。您还可以选择指定预打包的机器类型（具有特定数量的核心和内存），或者自定义您的配置。
根据您做出的选择，Google 会向您显示每月可能产生的费用。以下是您可能看到的界面：

![](img/615530_1_En_16_Fig6_HTML.jpg)

一个 Google Cloud 中 VM 实例页面的截图。名称、区域和可用区文本框已填写，机器配置为通用，机器类型选择了 E2。右侧指示了每月估算费用 $28.65。下方是“创建”按钮。

图 16-6 在 Google Cloud 中配置新的虚拟机

您还可以在同一页面中指定访问控制、防火墙规则和其他安全配置。选择好所需的配置后，您可以单击“创建”。您也可以选择最低配置，只使用最基本的设置。以下是仪表板可能的样子：

![](img/615530_1_En_16_Fig7_HTML.jpg)

一个 Google Cloud 中 VM 实例页面的截图。“实例”选项卡使用一个包含 5 列 4 行的表格列出了已创建的 VM 实例。列标题是：状态、名称、可用区、内部 IP 和连接。顶部是“创建实例”按钮。

图 16-7 Google Cloud 中所有 VM 实例的列表

请等待几分钟让实例创建完成。创建完成后，Google 将提供一个浏览器内的 SSH 会话。以下是您可能看到的界面：

![](img/615530_1_En_16_Fig8_HTML.jpg)

一个 Ubuntu 22.04.3 控制台的截图。它有一条欢迎消息，包含文档、管理和支持的链接，系统信息，可用更新和版权信息。控制台等待用户输入。

图 16-8 通过 SSH 访问您的 VM 实例

## Cloud SQL

Cloud SQL 是 Google 提供的一项完全托管的关系数据库服务。Cloud SQL 完全由提供商 Google 管理，这意味着它不需要系统维护、管理和运营支持。以下是您可能看到的界面：

![](img/615530_1_En_16_Fig9_HTML.jpg)

一个 Google Cloud 页面的截图。该页面包含有关 Cloud SQL 的信息。一个标有“使用您的免费额度创建实例”的按钮和一个名为“迁移数据”的可点击选项位于 Cloud SQL 欢迎消息的底部。

图 16-9 Google Cloud SQL 主页

Google 会自动执行备份、将补丁应用于数据库，并根据需要扩展存储容量。不过，需要定义标准和设置等。
Google 提供了为您的数据库实例选择数据库驱动程序的选项。当前选项是 Postgres、MySQL 和 MS SQL Server。当您使用选择的数据库驱动程序创建新的 Cloud SQL 实例时，Google 默认会在单个可用区中配置该实例。让我们访问 Google 控制台，选择 Cloud SQL 服务，并创建一个新数据库。

![](img/615530_1_En_16_Fig10_HTML.jpg)

一个“创建 MySQL 实例”页面的截图。选择的预设是 Sandbox，选择的区域是 us-central1 (Iowa)，对于可用区选项，选择了“单可用区”。底部是“创建实例”和“取消”按钮。

图 16-10 在 Google Cloud SQL 中创建新的数据库实例

正如我们之前讨论的，Cloud SQL 将为您提供选择数据库引擎的选项。选择数据库引擎后，继续提供计算实例名称、root 用户密码、预设选择 Sandbox、靠近您位置的区域以及单可用区，然后选择创建实例。
请等待几分钟让实例创建完成。创建完成后，屏幕将如下所示：

![](img/615530_1_En_16_Fig11_HTML.jpg)

一个 Google Cloud SQL 实例页面的截图。“概览”选项卡有一个实例名称，即 instance-20240213-083922，其下方是 CPU 利用率图表。编辑、导入、导出、重启、停止和删除实例的选项位于页面顶部。

图 16-11 Google Cloud SQL 中的活动数据库实例

配置好 Cloud SQL 服务后，您可以继续创建数据库，并启用对 Cloud SQL 实例的 shell 访问等。让我们在此处创建一个数据库。转到您的实例，在左侧菜单中选择“数据库”，然后单击“创建数据库”。提供数据库名称，将字符集保留为 UTF-8，然后单击“创建”。

以下是一个简单的应用程序，用于以编程方式将数据从您的本地工作站加载到您新创建的 Cloud SQL 数据库：

```python
from google.cloud.sql.connector import Connector

import sqlalchemy

import csv

import configparser

config = configparser.ConfigParser()

path = '/Users//credentials.cfg'

config.read(path)
```

# 初始化 Connector 对象

```python
connector = Connector()
```

# 返回数据库连接的函数

```python
def getconn():
    conn = connector.connect(
        config.get('gcp','instance_connection_name'),
        "pymysql",
        user=config.get('gcp','database_user'),
        password=config.get('gcp','database_password'),
        db=config.get('gcp','database')
    )
    return conn

pool = sqlalchemy.create_engine(
    "mysql+pymysql://",
    creator=getconn,
)

filepath = "/Users//gcp/polars_dataset.csv"
with pool.connect() as db_conn:
    db_conn.execute(' \
        CREATE TABLE onedata( \
            firstname varchar(200), \
            lastname varchar(200), \
            gender varchar(10), \
            birthdate varchar(100), \
            type varchar(20), \
            state varchar(100), \
            occupation varchar(100) \
        ) \
    ')
    with open(filepath, mode='r',encoding='utf-8') as csv_file:
        csv_reader = csv.DictReader(csv_file)
        for row in csv_reader:
            insert_query = 'INSERT INTO onedata ( \
                firstname, \
                lastname, \
                gender, \
                birthdate, \
                type, \
                state, \
                occupation \
            ) \
            VALUES (%s, %s, %s, %s, %s, %s, %s)'

            # 从 CSV 行中提取值的元组
            data_tuple = (row['firstname'],
                          row['lastname'],
                          row['gender'],
                          row['birthdate'],
                          row['type'],
                          row['state'],
                          row['occupation']
                         )

            # 执行查询
            db_conn.execute(insert_query, data_tuple)
    db_conn.commit()
    result = db_conn.execute("SELECT * from onedata").fetchall()
    for row in result:
        print(row)
    #db_conn.execute("DROP TABLE onedata")
```

在上述应用中，我们连接到 Google Cloud SQL，执行一个 DDL 脚本来创建表，然后迭代读取一个平面文件并将行插入到 Cloud SQL 中，最后提交事务。以下是数据在 Cloud SQL Studio 中的显示效果：

![img](img/615530_1_En_16_Fig12_HTML.jpg)

一张裁剪后的 Google Cloud SQL 页面截图。页面上有一条 SQL 查询语句：`select * from onedata`。运行按钮已被选中。结果下方显示了一个包含 7 列（如名字、姓氏、州、职业等）和超过 9 行数据的表格。

**图 16-12** 在 Google Cloud SQL 中创建并加载表

## Google Bigtable

Bigtable 是 Google Cloud 提供的一种 NoSQL 服务。这种 NoSQL 数据存储专为高吞吐量传入数据和低延迟数据库应用而设计。Bigtable 用于需要快速获取数据的应用场景，例如股票交易、实时游戏和流媒体平台。Bigtable 是一种反范式化数据库，不支持表之间的连接操作。

> **注意**
>
> 低延迟数据库是一种数据库管理系统，能够提供非常快的响应时间，例如毫秒级别。

Google Bigtable 是一个键值存储，不支持二级索引（键除外），并且列的顺序不必固定。你仍然可以将数据存储在表中，并且在一个给定的 Bigtable 实例中可以包含多个表。在没有模式的情况下，你可以尝试将结构（或模式）相似的数据存储在同一个表中。此外，所有操作在行级别都是原子性的。我认为，当一个操作向 Bigtable 写入多行但失败时，可能会出现某些行已写入而某些行未写入的情况。

在设计 Bigtable 行键时，阐明数据将如何被查询以及如何检索数据非常重要。你可以在行键中嵌入多个由分隔符分隔的标识符。在 Bigtable 中，行按行键的字典序排序，因此设计一个以通用值开头、以最细粒度值结尾的行键可能是有益的。

Google Bigtable 将表定义为由行键索引的值的逻辑组织。让我们创建一个 Bigtable 实例。导航到 GCP 控制台并输入“Bigtable”。找到该服务后，选择创建一个实例。

你也可以在工作站上安装适用于 Python 的 Google Bigtable 客户端。根据文档（[`https://cloud.google.com/python/docs/reference/bigtable/latest`](https://cloud.google.com/python/docs/reference/bigtable/latest)），安装方法如下：

```
pip install google-cloud-bigtable
```

你总是需要创建一个 Bigtable 实例。一个实例通常包含一个或多个位于不同时区的集群。每个集群包含一个或多个计算节点。这些计算节点负责管理你的数据并执行其他任务。要创建实例，你必须指定存储类型，可以选择硬盘或 SSD。

以下是创建实例时的界面示例：

![img](img/615530_1_En_16_Fig13_HTML.jpg)

一张截图显示了配置第一个集群的设置。前面的步骤（命名实例和选择存储类型）已完成勾选。在“配置第一个集群”下，集群 ID、区域和可用区字段已填写。节点扩缩模式选择了“手动分配”。

**图 16-13** 创建新的 Google Bigtable 实例

让我们尝试将 JSON 文档上传到 Google Bigtable。

以下是 JSON 文档的结构示例：

```json
{
  "id": 2,
  "first_name": "Rube",
  "last_name": "Mackett",
  "email": "rmackett1@japanpost.jp",
  "gender": "Male",
  "ip_address": "153.211.80.48"
}
```

以下是将 JSON 加载到 Google Bigtable 的示例应用：

## 加载数据到 Bigtable

```python
from google.cloud import bigtable
from google.cloud.bigtable import column_family
import json
import configparser

config = configparser.ConfigParser()
path = '/Users/pk/Documents/de/gcp/credentials.cfg'

# 配置信息
project_id = config.get('bigtable','project_id')
instance_id = config.get('bigtable','instance_id')
table_id = config.get('bigtable','table_id')
json_file_path = "/Users/pk/Downloads/randomdata.json"

# 认证并连接到 Bigtable
client = bigtable.Client(project=project_id, admin=True)
instance = client.instance(instance_id)
table = instance.table(table_id)

max_versions_rule = column_family.MaxVersionsGCRule(2)
column_family_id = "cf1"
column_families = {column_family_id: max_versions_rule}

if not table.exists():
    table.create(column_families=column_families)

column_family_id = "cf1"

with open(json_file_path, 'r') as f:
    records = json.load(f)

for record in records:
    row_key = "row-key-{}".format(record["id"]).encode()
    row = table.direct_row(row_key)
    for key, value in record.items():
        row.set_cell(column_family_id,
                     key,
                     str(value).encode(),
                     timestamp=None)
```

## 提交

`row.commit()`

`print("数据已成功加载到 Bigtable。")`

您可以通过运行一个简单的查询在 `Bigtable Studio` 中查看输出。示例如下：

![](img/615530_1_En_16_Fig14_HTML.jpg)

一张裁剪后的 Google Cloud SQL 页面截图。编辑器 1 页面包含一个 SQL 查询 `select star from one data`，并且运行按钮已被选中。在结果下方，显示了一个包含 7 列和超过 9 行数据的表格。

图 16-14 在 Google Bigtable 中加载 JSON 数据集

一个示例凭证文件可能如下所示：

```
[bigtable]
project_id = "在此处填写项目 ID"
instance_id = "在此处填写实例 ID"
table_id = "在此处填写表 ID"
```

# Google BigQuery

## 概述

Google BigQuery 是 Google 提供的一种全托管分析型数据仓库解决方案。由于它是全托管服务，因此无需设置、配置或维护底层基础设施。BigQuery 的存储空间会自动配置，并在数据量增加时自动扩展。除了支持高吞吐量的读取，BigQuery 还支持高吞吐量的流式数据摄入。在 Google BigQuery 中，存储和计算是分离的。存储和计算都可以按需独立扩展。

## 存储与数据模型

Google BigQuery 是一个列式存储数据库，数据会自动跨多个可用区复制，以防止因机器故障或区域故障导致数据丢失。在 BigQuery 中，数据主要以表格格式存储。表格数据包括标准表、表克隆、物化视图和快照。标准表格数据是一种具有模式（`Schema`）的表数据，每个列都有指定的数据类型。表克隆是标准表的轻量级副本，仅存储表克隆与基表之间的差异（因此非常轻量）。

表快照是表在特定时间点的副本，您可以选择从特定时间点恢复指定表。最后一种是物化视图，其中查询结果被预先计算、缓存并定期更新。在 Google BigQuery 中，查询的缓存结果存储在临时表中。Google 不会对缓存的查询结果收费。

## 安全、管理与质量

Google BigQuery 允许您通过访问控制、数据管理和数据质量模块，在整个数据生命周期内管理数据的安全性和质量。您可以管理和控制 BigQuery 用户的访问权限，以及他们对表、视图、快照等资源的访问级别。此外，还提供了控制和管理对特定行和列访问的条款，从而实现对敏感数据的细粒度访问。您可以选择通过获取审计日志来获取详细的用户配置文件和系统活动信息。

此外，您可以通过对 `PII` 字段应用数据脱敏来保护 BigQuery 表和视图中的个人身份信息。这将保护数据集免遭意外数据泄露。BigQuery 会自动加密所有数据，无论是传输中还是静态存储的数据。您还可以选择通过在数据集上运行统计指标（平均值、中位数、唯一值等）来检查数据质量。您还可以根据预定义规则验证数据，检查数据质量并排查数据问题。

## 快速入门

要开始使用 Google BigQuery，我们需要在 Google Cloud 中找到该服务并启用它。该过程如下所示：

![图 16-15：启用 Google BigQuery API](img/615530_1_En_16_Fig15_HTML.jpg)
*一张截图。页面包含 `BigQuery API` 的信息，其图标位于左侧。启用按钮位于下方。概览、文档和相关产品等选项卡位于底部。*

点击“启用”后，您可能需要等待几分钟，直到系统自动将您重定向到 Google BigQuery。无需配置任何基础设施、计算资源甚至存储空间。当您开始使用 BigQuery 时，将根据您的使用情况，按存储和计算用量计费。
您的界面可能如下所示：

![图 16-16：Google BigQuery 主控制台](img/615530_1_En_16_Fig16_HTML.jpg)
*一张 Google BigQuery 控制台的截图。在侧边面板中，选择了 `BigQuery Studio` 选项。在右侧，资源管理器选项卡显示了资源和摘要。在最右侧，显示了一个未命名的代码面板以及包含 4 列和 8 行数据的查询结果。*

# Google Dataproc

## 概述

Google Dataproc 是一个全托管服务，为您提供 `Hadoop` 集群或 `Spark` 集群，以便您在分布式环境中运行大数据处理，从而利用大数据工具的优势。`Dataproc` 服务还与 BigQuery 等其他多种 Google Cloud 服务良好集成，使您能够构建完整的数据平台。
要开始使用 Google Dataproc，请从云控制台启用 API：

![图 16-17：启用 Cloud Dataproc API](img/615530_1_En_16_Fig17_HTML.jpg)
*一张裁剪后的截图。它包含 `Cloud Dataproc API` 的信息，其图标位于左侧。文本内容为“在 Google Cloud 平台上管理基于 `Hadoop` 的集群和作业”。下方是“启用”和“试用此 API”按钮，以及一个指向外部链接的图标。*

Google Dataproc 的 `Spark` 集群采用无服务器模式，这意味着您只需为实际消耗的资源付费。`Hadoop` 集群的成本也相对较低。

## 集群特性与类型

Google Dataproc 设置新集群所需的时间非常短。它还具备自动缩放功能，可根据内存使用情况自动扩展或缩减。
您可以使用 Google Dataproc 设置三种类型的集群：单节点集群、标准集群和高可用性集群。单节点集群由一个主节点和零个工作节点组成。标准集群由一个主节点和“n”个工作节点组成，而高可用性集群则由三个主节点和“n”个工作节点组成。单节点集群不支持自动缩放功能。

## 创建集群（通过 `Compute Engine`）

让我们在 Google Dataproc 中使用 `Compute Engine` 创建一个简单的集群。首先，点击“创建集群”并选择 `Compute Engine` 选项：

![图 16-18：为 Dataproc 集群选择基础设施](img/615530_1_En_16_Fig18_HTML.jpg)
*一张裁剪后的截图。标题为“创建 `Dataproc` 集群”。下方有两个选项：在 `Compute Engine` 上创建集群和在 `GKE` 上创建集群，每个选项都附带一个“创建”按钮。“取消”按钮位于右下角。*

在我们的示例中，我们将创建一个包含 1 个主节点和 0 个工作节点的单节点集群。让我们提供集群名称、区域、集群类型，并为 `Compute Engine` 选择磁盘映像。界面如下所示：

![图 16-19：创建新的 Dataproc 集群](img/615530_1_En_16_Fig19_HTML.jpg)
*一张 Google Dataproc 的截图。在左侧面板中，选择了“集群”选项。在中央，选择了“设置集群”选项。“创建”按钮位于下方。在右侧，显示了包含值的名称和位置字段。下方是一个用于选择集群类型的面板，其中“单节点”单选按钮已被选中。*

向下滚动时，请确保勾选“启用组件网关”复选框以及“`Jupyter Notebook`”选项。您可以根据需要选择其他选项。界面如下所示：

![图 16-20：为新的 Dataproc 集群指定组件](img/615530_1_En_16_Fig20_HTML.jpg)
*一张 Google Dataproc 在 `Compute Engine` 上创建集群的截图。在左侧，选择了“设置集群”选项。“创建”按钮位于下方。在右侧，“启用组件网关”复选框已被勾选。下方是一列包含 9 个可选组件的复选框，其中“`Jupyter Notebook`”组件已被勾选。*

现在，点击“配置节点”，您可以在其中指定要使用的计算类型。如果您正在为开发环境创建集群，那么性能稍弱的计算资源可能也能让您舒适地工作。
在这种情况下，您可能希望选择通用计算类型，并在看到“`N1` 系列”选项时选择它。您也可以为工作节点选择类似的选项。
界面效果如下所示：

![图 16-21：为 Dataproc 集群配置节点](img/615530_1_En_16_Fig21_HTML.jpg)
*计算引擎上 Google Dataproc 集群页面的截图。左侧，“配置节点”选项已被选中。“创建”按钮在其下方。在右侧的管理节点面板下，“通用目的”选项卡已被勾选，随后是系列“`N1`”和机器类型，并显示了相应的值。下方是关于 CPU 平台和 GPU 的数据。*

## 创建集群（通过命令行）

您也可以通过 Google Cloud 命令行界面创建集群。

相应的命令如下：

```
gcloud dataproc clusters create my-cluster \
  --enable-component-gateway \
  --region us-central1 \
  --no-address \
  --master-machine-type n1-standard-2 \
  --master-boot-disk-type pd-balanced \
  --master-boot-disk-size 50 \
  --num-workers 2 \
  --worker-machine-type n1-standard-2 \
  --worker-boot-disk-type pd-balanced \
  --worker-boot-disk-size 50 \
  --image-version 2.2-debian12 \
  --optional-components JUPYTER \
  --project {your-project-id}
```

# 集群仪表板与 PySpark 管道

集群创建完成后，您应该可以在仪表板中看到它。界面效果如下所示：

![](img/615530_1_En_16_Fig22_HTML.jpg)

**图 16-22** Google Dataproc 中活跃集群的仪表板

## 使用 Vertex AI Workbench 创建 Spark 管道

让我们创建一个 Spark 数据管道，并使用 Cloud Dataproc 来执行该数据管道。我们将使用 Vertex AI Workbench 服务来创建一个 Jupyter 笔记本。

### Google Vertex AI Workbench

Google Vertex AI Workbench 服务是一个完全托管的、基于 Jupyter Notebook 的开发环境。Google Vertex AI 还与 GitHub 集成。让我们导航到 GCP 控制台，并分别启用 Vertex AI API 和 Notebook API。然后，导航到 Google Vertex AI Workbench 并创建一个笔记本。您将看到在 Dataproc 集群上创建 PySpark 实例的选项。界面效果如下所示：

![](img/615530_1_En_16_Fig23_HTML.jpg)

**图 16-23** Google Vertex AI Workbench 的仪表板

Vertex AI Workbench 与 Cloud Storage 和 BigQuery 服务集成良好。它预装了各种机器学习库，并支持 PyTorch 和 TensorFlow 框架。您可以选择仅使用 CPU 的实例或仅使用 GPU 的实例。

以下是一个示例代码，您可以在 Google Cloud Storage 数据上运行：

```python
from pyspark.sql import SparkSession

bucket = "your-bucket"

filepath = "gs://your-bucket/polars_dataset.csv"

spark = SparkSession.builder\
    .appName("PySpark-my-cluster")\
    .config("spark.jars", "gs://spark-lib/bigquery/spark-bigquery-latest.jar")\
    .getOrCreate()

df = spark.read.csv(filepath, header=True, inferSchema=True)

df.show(3)

occ_report = df.crosstab(col1="state",col2="occupation")

occ_report.show(5)
```

> `spark.jars` 文件用于 Spark 与 Google BigQuery 进行读写操作。您可以在 Google Dataproc 服务中获取合适的 Spark jars 文件。访问仪表板，导航到 Dataproc 中的“提交作业”页面。提供适当的信息并点击“提交”以获取 jars 文件。

以下是在 Jupyter 笔记本上执行此代码的效果：

![](img/615530_1_En_16_Fig24_HTML.jpg)

**图 16-24** 在 Google Vertex AI Workbench 上运行 PySpark 代码

## Google Vertex AI 与 AutoML

Google 的 Vertex AI 是一个完全托管的机器学习平台，可让您训练和部署机器学习模型。一旦您训练好模型，就可以将其部署在 Vertex 上，使模型使用者能够获得旨在为企业创造价值的实时智能和洞察。这个机器学习平台拥有众多功能，并且与 AWS 和 Microsoft 提供的其他领先机器学习平台高度可比。

让我们从头开始构建一个自定义机器学习模型，这次我们将使用 AutoML 功能。AutoML 代表自动化机器学习，是自动化机器学习模型的过程，包括预处理、特征工程、模型选择和超参数调优。

Google Vertex AI 提供两种用于构建机器学习模型的开发环境：Vertex AI Workbench 和 Colab 笔记本。我们在上一节中已经了解了 Vertex AI Workbench，并利用该笔记本构建了机器学习模型。这些工作台环境通过虚拟机提供，并且可自定义，同时支持 GPU。

Google Colab 笔记本是一种托管笔记本环境，可让您与他人协作，同时 Google 为您管理底层基础设施。您可以选择自定义底层计算和配置运行时。在 Google Colab 中，表格文件的上传大小不能超过 20 兆字节，而在 Vertex AI Workbench 中，您可以处理大小高达 100 兆字节的表格数据集。

让我们来看看如何使用 Google Vertex AI 构建机器学习模型并将模型作为端点托管。我们使用鸢尾花数据集来解决一个多类分类问题。以下是供您参考的机器学习代码。我鼓励您将此代码作为起点，并尝试其他分类器：

```python
import pandas as pd
from xgboost import XGBClassifier
from sklearn.model_selection import train_test_split
from sklearn.metrics import accuracy_score
from sklearn.preprocessing import LabelEncoder
import pickle

training_dataset = pd.read_csv('iris.csv')
dataset = training_dataset.values
# split data into X and y
X = dataset[:,0:4]
Y = dataset[:,4]

label_encoder = LabelEncoder()
label_encoder = label_encoder.fit(Y)
label_encoded_y = label_encoder.transform(Y)

seed = 7
test_size = 0.33

X_train, X_test, y_train, y_test = train_test_split(
    X,
    label_encoded_y,
    test_size=test_size,
    random_state=seed
)

xgmodel = XGBClassifier()
xgmodel.fit(
    X_train,
    y_train
)

print(xgmodel)

# make predictions for test data
predictions = xgmodel.predict(X_test)

# 评估预测结果
accuracy = accuracy_score(
    y_test,
    predictions
)
print("准确率: %.2f%%" % (accuracy * 100.0))

filename = "model.pkl"
pickle.dump(xgmodel, open(filename, 'wb'))
```

# 在 Google Vertex AI 上部署 Pickle 文件格式的机器学习模型

## Pickle 文件简介

Pickle 文件是二进制文件，用于序列化 Python 对象，例如字典、列表等。将 Python 对象转换为字节流的过程称为 pickling 或序列化。反之亦然。你可以反序列化或解封一个 pickle 文件。由于 pickle 文件是 Python 特有的，因此被认为是安全的。在 Python 生态系统中，Pickling 常用于机器学习建模。

一旦我们有了 pickle 文件格式的模型，就可以将此模型添加到 Google Vertex AI 模型注册表中。要开始使用，请从 Google 控制台访问 Vertex AI，导航到部署，然后点击模型注册表。界面可能如下所示：

![](img/615530_1_En_16_Fig25_HTML.jpg)

一张 Google Vertex AI 的截图。左侧面板显示了工具、笔记本和 Vertex AI Studio 选项。右侧的模型注册表选项卡显示了字段、区域，其中包含一个条目 us central 1, Iowa，以及一个包含 5 列 0 行数据的表格。顶部有创建、导入、刷新和学习选项。

图 16-25 Google Vertex AI 的模型注册表页面

## 导入模型

你可以交互式地创建一个新的机器学习模型，或者导入你生成的 pickle 文件。在这种情况下，我们将以 pickle 文件的形式导入我们的模型。你可以选择模型的区域。

![](img/615530_1_En_16_Fig26_HTML.jpg)

一张 Google Vertex AI 的截图。左侧面板标题为“导入模型”，列出了 3 个选项。选项 1，名称和区域已被选中。在右侧，选中了“作为新模型导入”的单选按钮。下方是名称、描述和区域的字段。名称和区域已有值。

图 16-26 导入模型、命名并指定区域

你可以为此模型指定名称、描述和区域。你可以选择将其作为新模型导入。如果你碰巧重新训练了同一个模型（使用鸢尾花数据集）并生成了另一个模型 pickle 文件，那么你可以将其作为现有模型的较新版本导入。我们使用 `XGBoost` 模型框架训练了模型。如果你使用了 `scikit-learn` 或 `TensorFlow` 作为模型框架，请从下拉框中选择相应的模型框架和模型框架版本。对于模型工件位置，请提供云存储文件夹位置，可以通过可视化浏览或直接输入 URI。

![](img/615530_1_En_16_Fig27_HTML.jpg)

一张 Google Vertex AI 的截图。从左侧面板选择了“在线预测”选项。在右侧的“在线预测”标题下，选中了“端点”选项卡。区域文本字段中已输入 us central 1, Iowa。底部有创建和刷新按钮。

图 16-27 导入模型的进一步设置

对于模型工件位置，请仅指定文件夹，而不是确切的模型路径。该列会自动填充 `gs://` 前缀，因此请注意云存储路径。

## 创建端点

一旦你在模型注册表中部署了机器学习模型 pickle 文件，就可以创建端点并将其公开以供使用。让我们导航到 Vertex AI 中的“部署和使用”选项卡，然后点击“在线预测”。界面可能如下所示：

![](img/615530_1_En_16_Fig28_HTML.jpg)

一张 Google Vertex AI 的截图。左侧面板标题为“导入模型”，列出了 3 个选项。选项 2，模型设置已被选中。在右侧，选中了“将模型工件导入新的预构建容器”的单选按钮。下方是模型框架、版本和位置字段，并带有值。

图 16-28 为我们的 ML 模型创建端点

在线预测的概念仅在你希望通过向 HTTP 端点提交请求来使用机器学习模型时适用。让我们将鸢尾花机器学习模型部署为端点。你可以选择托管此端点的区域，然后点击“创建新端点”。

![](img/615530_1_En_16_Fig29_HTML.jpg)

一张 Google Vertex AI 的截图。左侧面板标题为“部署到端点”，列出了 3 个选项，其中“定义你的端点”选项被选中。在右侧，选中了“创建新端点”的单选按钮，随后是一个用于输入端点名称的文本框。在“访问”下，选中了“标准”单选按钮。

图 16-29 定义端点并指定区域

点击创建端点后，为端点提供一个名称，并单独记下该名称。要访问端点，我们将在此处选择标准选项。

## 部署模型设置

流量拆分基本上是指定应将多少传入流量路由到此端点。出于说明目的，我们可以保留该值不变。如果我们为机器学习模型托管多个端点，则可以在以后更改此参数。

此外，你可以为此端点指定计算节点的数量。同时，你还可以指定在流量较高时希望拥有的最大计算节点数。你还可以指定具有特定计算能力和物理内存的自定义机器；也可以选择专用机器，例如 CPU 密集型、内存密集型等。

界面可能如下所示：

![](img/615530_1_En_16_Fig30_HTML.jpg)

一张 Google Vertex AI 的截图。左侧面板标题为“部署到端点”，列出了 3 个选项，其中“模型设置”选项被选中。在右侧，在“新模型”标题下，流量拆分的字段输入为 100，最小计算节点数的字段输入为 1。高级缩放选项下有一个机器类型字段。

图 16-30 部署到端点的模型设置

对于其余选项，你可以保留原样并选择部署模型。该过程可能需要几分钟。模型部署后，你应该能够在部署仪表板中看到该条目。

## 已部署模型与测试

界面可能如下所示：

![](img/615530_1_En_16_Fig31_HTML.jpg)

一张 Google Vertex AI 的截图。从左侧面板选择了“模型注册表”。右侧的模型注册表选项卡有一个组合框，区域为 us central 1, Iowa，以及一个包含 5 列 1 行数据的表格。顶部有创建和刷新选项。

图 16-31 可在 Google Vertex AI 仪表板中看到的已部署模型

你可以通过各种形式测试端点。Postman 客户端是最简单的。以下是使用 Google Cloud 命令行界面的示例：

```
gcloud ai endpoints predict 2954719796345700352 \
    --region us-central1 \
    --json-request request.json
```

```
request.json` 文件将包含样本数据。

## 结论

到目前为止，我们能够了解 Google Cloud Platform，重点关注数据工程和机器学习项目。我们探索了从 `Cloud Storage` 到 `BigQuery` 的广泛服务。GCP 通过 `Compute Engine API` 提供灵活的计算选项，以及 `Bigtable` 和 `Cloud SQL` 等托管数据库服务。我们了解了用于运行分布式计算工作负载的 `Dataproc` 和 `BigQuery`，以及用于开发机器学习模型的 `Vertex AI Workbench`。随后是 Google `Vertex AI`，我们了解了如何构建自定义机器学习模型，将其部署到模型注册表，并将其公开为用于实时预测的端点。随着 GCP 的发展并不断添加更多服务，他们持续提供托管服务，以减少数据和机器学习工程师的运营开销，使他们能够构建稳定可靠的数据解决方案。

# 17. 使用 Microsoft Azure 构建工程数据管道

## 简介

Azure 云
计算是微软提供的一项完全托管的计算服务。Azure 的概念最早于 2008 年的一次会议上提出，微软于 2010 年推出了 `Windows Azure` 服务，后更名为 `Microsoft Azure`。目前，它通过各种部署模式提供一套全面的计算、数据库、分析及其他服务。微软在全球拥有数据中心，服务于各行各业。在本章中，我们将重点介绍 Azure 的一些关键组件和服务，特别是与数据工程和机器学习相关的部分。

学完本章，你将了解：

*   如何设置 `Microsoft Azure` 账户以及 `Azure` 的资源层级结构
*   微软名为 `Azure Blob Storage` 的对象存储及其概念与设置
*   名为 `Azure SQL` 的关系数据库服务及其概念与安装
*   `Cosmos DB`（一种 NoSQL 数据库）及其架构与概念
*   完全托管的数据仓库环境
*   无服务器函数与数据集成服务
*   `Azure Machine Learning Studio` 及其概念

在你开始探索 `Microsoft Azure` 平台之际，我恳请你务必注意管理云资源，尤其是在使用完毕后。对于传统基础设施，你可以在使用完毕后关闭计算机。然而，对于云基础设施，由于你通常采用按需付费模式，即使资源未被使用，也会产生费用。注销 `Azure` 并不意味着关闭你已购买的服务。这一点对于虚拟机、数据库以及其他计算密集型资源尤为重要。请务必养成检查活跃资源并关闭这些特定资源的习惯。如果你不再计划使用某项服务，可以将其删除。这样，你只需为实际使用的服务付费，从而控制成本。同时，我也要感谢 `Azure` 为新用户提供免费额度，并积极支持新学习者降低使用成本。

## Azure 简介

`Azure` 是微软的云计算平台，提供多项与谷歌和亚马逊云服务相媲美的服务。`Azure` 的服务对于那些重度依赖微软技术的组织尤其具有吸引力。
微软在数据管理领域享有长期声誉。微软是构建 ETL 工具的早期采用者之一，这些工具在促进不同数据源之间的数据移动方面发挥了重要作用。要开始使用 `Microsoft Azure`，你需要先访问微软网站注册一个 `Microsoft` 账户，然后才能尝试获取 `Azure` 的访问权限。拥有 `Microsoft` 账户后，你可以注册免费的 `Azure` 服务。与 `AWS` 和 `Google Cloud` 类似，你需要向微软提供各种信息才能获得 `Azure` 服务的访问权限。

让我们熟悉一下 `Microsoft Azure` 的资源层级结构。这有助于配置和管理特定账户（尤其是组织账户）所提供服务和负载的配置。最高层级称为管理组。管理组负责管理特定账户对各种服务的利用率。管理组使组织能够对管理组内的所有用户应用各种策略，并对一个或多个 `Azure` 服务产品应用访问控制。从根管理组，你可以创建一个或多个管理组，这些管理组可能对应一个业务组或某种计费模式。只需从 `Azure` 仪表板中搜索“管理组”一词即可进入该部分，你可以为你的组织创建一个管理组。你可以将管理组命名为你的组织、组织内的部门或特定项目名称。

如果你经常访问某个服务产品页面，那么将该页面固定到仪表板可能会更方便。为此，只需单击管理组标题旁边的图钉状图标即可。效果如下所示：

![](img/615530_1_En_17_Fig1_HTML.jpg)

一个网页截图，标题为仪表板，管理组名称为 mg 1。页面有一个搜索栏，以及一系列选项，包括概述、订阅、资源组、资源、活动日志和访问控制。还有创建、添加订阅和刷新按钮。在“基本信息”下方有一个按名称或 ID 搜索的搜索栏。

图 17-1
`Azure` 管理组仪表板

在给定的管理组中，你可以拥有一个或多个订阅。你可以在“管理组”部分看到订阅选项。订阅是一种方案，它允许一个或多个用户访问一个或多个 `Azure` 服务产品。例如，你可以为特定团队在管理组中设置两个订阅，即开发订阅和生产订阅。你可能允许生产订阅拥有更多资源，并对开发订阅施加某些限制。另一个例子是为组织提供的每个产品或服务创建一个订阅，以便能够计算成本。

在任何给定的订阅中，我们有一个称为资源组的概念。这是一个基于项目的资源分组，或一个或多个 `Azure` 服务的分组。例如，如果你希望建立一个数据仓库项目，并且需要数据库、数据仓库、数据集成服务、机密管理、数据建模工具服务以及日志记录和监控服务，那么你可以创建一个包含这些服务的资源组。这在治理和设置访问策略的背景下非常有益。

最后，最低层的抽象在 `Azure` 中称为资源。这些是 `Azure` 提供的各种服务。它可以是一个数据库或一个你需要付费使用的工具。资源是任何由网络、存储或计算实体组成的东西。例如，一个使用计算能力将数据从源移动到目标的无服务器数据集成服务就是资源的一个例子。

从数据产品和服务角度来看，让我们探讨一下存储系统、数据库服务以及数据摄取和集成服务等。

## `Azure Blob` 存储

如前几节所述，对象存储是一种通用的存储系统，并且已成为云供应商成功的产品。`Azure Blob` 存储是一种对象存储，是与 `AWS` 提供的 `S3` 和 `Google Cloud` 提供的 `Bigtable` 相媲美的解决方案。`Azure Blob` 存储是一个高度可扩展且安全的存储平台，可以存储结构化和非结构化数据。`Azure Blob` 存储提供基于策略和角色的访问控制以及数据生命周期管理。

> **注意**
>
> “`Blob`”是一个数据库术语，是二进制大对象的缩写。`Blob` 可以将任何类型的数据存储为二进制对象。它非常适合存储图像、音频、视频或 PDF 内容。不过，这些 `blob` 数据是不可索引的。还有 `clob`，它是字符大对象的缩写，用于存储大量文本数据，如文档、大型代码片段等。

要创建新的 `blob` 存储，请从主仪表板中选择“存储”并创建一个新的存储。提供实例名称并选择一个就近的区域。此外，选择标准性能和经济型复制。你可能会看到其他选项，例如选择客户管理的加密密钥等其他至关重要的选项。微软会花一点时间运行验证来检查你的选择，然后才会启用“创建”按钮。创建完成后，等待几分钟，让资源在你的帐户中部署。部署完成后，界面如下所示：

![](img/615530_1_En_17_Fig2_HTML.jpg)

一张截图显示了 `Microsoft Azure` 部署页面，标题为“用于示例用法的测试 blob”。它包括一个搜索框和一系列选项。这些选项包括概述、输入、输出、模板，其中“概述”被高亮显示。一条带有勾选标记的消息显示：“你的部署已完成”。底部有一个“转到资源”按钮。

图 17-2 部署一个新的 `Azure blob` 存储

你现在有了一个配置好供你使用的存储帐户。现在，让我们通过单击“上传”并选择“新建”来创建一个容器。界面如下所示：

![](img/615530_1_En_17_Fig3_HTML.jpg)

一张 `Microsoft Azure` 门户的截图。页面标题为“上传 blob”。有一个带有向上箭头的云图标。图标下方的文字显示：“将文件拖放到此处或浏览文件”。有一个复选框，标签为“如果文件已存在则覆盖”。在高级选项下，“将 `.vhd` 文件作为页 `blob` 上传”已被选中。

图 17-3 在 `Azure Blob` 存储中上传数据

现在，让我们使用 `Python` 以编程方式将一个平面文件上传到 `Azure Blob` 存储。首先，我们需要为 `Python` 编程语言安装 `Azure Blob` 存储的软件开发工具包。打开一个终端并启动一个虚拟环境会话或 `conda` 环境会话：

```
python3 -m venv azure
source azure/bin/activate
```

如果你使用的是 `MS Windows`，请使用

```
azure/Scripts/activate
pip install azure-storage-blob azure-identity
```

为了让我们能够以编程方式与云存储服务交互，我们分别需要连接字符串、容器名称和本地文件路径。我们将设置需要上传的文件的连接路径，并分别定义容器名称和连接字符串。然后，我们将分别初始化 `blob` 客户端和容器客户端。容器是 `blob` 存储服务中对象的逻辑分组。你也可以强制执行容器级别的访问控制。一旦我们初始化了 `blob` 客户端，我们就可以从那里上传文件了。

以下是整个过程的代码示例：

```python
# 此处应包含原始的代码示例内容
```

与 AWS 和 Google Cloud 类似，Microsoft Azure 提供了多种存储层级，你可以根据访问数据的频率将数据组织在特定的层级中。目前，Azure 提供热层、冷层、冷层和存档层用于存储。热层是为最频繁的访问（读取或写入）而设计的存储层级。热层是所有层级中最昂贵的。冷层适用于不常访问的数据；但是，数据至少需要存储 30 天。冷层与冷层的概念类似，只是数据至少需要存储 90 天。存档层的最短存储期限为 180 天，也称为长期备份。

## Azure SQL

`Azure SQL` 是一种托管数据库服务，可以通过三种不同的方式获得。回想一下我们关于基础设施即服务、平台即服务和软件即服务的讨论。
你可以在 Azure 虚拟机上部署 `Azure SQL`。Azure 为你提供了预装了 `SQL Server` 的操作系统镜像。你可以选择你喜欢的镜像，但请记住，你将负责操作系统的升级和补丁。你可以选择为虚拟机优化内存或优化存储。该服务名为“`SQL 数据库`”，这种设置类型属于基础设施即服务。
你也可以使用 `Azure 托管数据库实例`，其中完全托管的 `Azure SQL 数据库`服务会自动处理大部分软件更新、安全更新和补丁。在这里，我们只需选择合适的数据库版本，而无需担心底层操作系统。你仍然可以配置底层计算（并可以选择向上或向下扩展当前分配给托管实例的虚拟核心数量）。这被称为平台即服务模型。
另一种选择是使用名为“`SQL Server`”的服务，它作为 SaaS（软件即服务）运行。在这种选择中，你无需担心底层基础设施。微软将管理应用程序和底层操作系统。你可以选择无服务器模型，只需为你消耗的资源付费。Azure 根据你使用 `SQL Server` 的秒数向你收费。你可以为底层基础设施选择各种层级，并且可以灵活地向上或向下扩展计算资源。

### 创建一个 Azure SQL 数据库

让我们创建一个 `Azure SQL 数据库`。在创建新 `SQL 数据库`的过程中，我们还将创建一个 `Azure SQL 逻辑服务器`。`Azure SQL 逻辑服务器`就像是 `SQL 数据库`的父资源。使用 `Azure SQL 逻辑服务器`，你可以启用基于角色的访问控制、登录、威胁防护和防火墙等功能。它还提供了访问数据库的端点。
让我们搜索“`SQL 数据库`”并选择创建一个新的数据库实例。你需要提及订阅和资源组详细信息。你还需要输入数据库的名称并选择一个服务器。服务器选项用于 `Azure SQL 逻辑服务器`。如果当前没有服务器，你可以创建一个新的。界面如下所示：

![图 17-4](img/615530_1_En_17_Fig4_HTML.jpg)
一个标记为“创建 SQL 数据库”的对话框。表单包含订阅、资源组、数据库名称、服务器、是否使用 SQL 弹性池、工作负载环境以及计算和存储等字段。它包含“查看 + 创建”和“下一步：网络”按钮。

你还可以选择是否使用弹性 `SQL 池`。弹性 `SQL 池`基本上是为数据库提供的底层计算、存储和 IO，这些资源也可以与其他数据库实例共享。如果你创建了“n”个单一数据库实例，那么你将拥有“n”个底层计算和其他资源。如果你使用弹性 `SQL 池`，那么你可以拥有少于“n”个的计算和存储资源，并在“n”个数据库实例之间“共享”它们。
除了 `SQL 弹性池`选项之外，我们还可以指定我们正在创建的此实例是用于开发还是生产目的。`SQL 弹性池`按 `eDTU` 计费。`eDTU` 是一种货币单位，代表一定数量的计算、存储和 IO 资源。你可能希望预先购买一定数量的 `eDTU`，并为数据库设置它们在给定时间内可以消耗的此类单位数量的限制。

### 创建 Azure SQL 逻辑服务器

在创建新的 `SQL 数据库`之前，我们可能需要创建一个新的 `Azure SQL 逻辑服务器`。让我们单击“创建 `SQL 数据库`服务器”，这将提供创建新逻辑服务器的页面。界面如下所示：

![图 17-5](img/615530_1_En_17_Fig5_HTML.jpg)
一个标记为“创建 SQL 数据库服务器”的对话框。表单包含服务器名称、位置、身份验证方法、服务器管理员登录名、密码和确认密码等字段。服务器名称为 `my-d-b-server-7`。位置为美国东部。身份验证方法为 `SQL 身份验证`。底部有一个“确定”按钮。

你可以为逻辑服务器指定名称和位置；此外，你还可以指定身份验证方法。提供信息后，你可以单击“确定”为你的 `SQL 数据库`创建一个新的逻辑服务器。对于我们的实例，我们选择创建一个 `SQL 数据库`的单一实例。因此，我们选择不使用弹性 `SQL 池`。

### 选择计算和存储

逻辑服务器准备就绪后，你可以选择 `SQL 数据库`的计算和存储。在这里，你可以为你的 `SQL 数据库`选择底层硬件；你可能希望探索 Azure 提供的各种硬件及其定价，这些信息将立即计算并提供给你。此外，你还可以选择计算层级，是想要预分配的计算资源，还是选择无服务器模式，在这种模式下，你只需为你消耗的资源付费。
界面如下所示：

![图 17-6](img/615530_1_En_17_Fig6_HTML.jpg)
一个标记为“服务和计算层级”的网页截图。服务层级字段输入为“通用”。面板有一个标记为“最大 v 核心数”为 4、“最小 v 核心数”为 0.15 的滑块条。右侧有一个标记为“SQL”的文本框，带有成本摘要。

在选择备份存储冗余时，你可以为开发目的选择本地冗余备份存储或区域冗余选项。你可以保留其他选项不变，然后选择创建数据库。

### 示例 Python 应用程序

以下是一个示例 Python 应用程序，它将连接到数据库、创建表并向该表插入行。如果你尚未安装 `pyodbc` 包，请在终端中执行以下命令：

```
pip install -u pyodbc
```

```python
import pyodbc
from dotenv import load_dotenv
import os

server = os.getenv("server")
database = os.getenv("database")
username = os.getenv("username")
password = os.getenv("password")
driver = os.getenv("driver")

try:
    cnxn = pyodbc.connect('DRIVER=' + driver +
                           ';SERVER=' + server +
                           ';DATABASE=' + database +
                           ';UID=' + username +
                           ';PWD=' + password)
    cursor = cnxn.cursor()
    print('Connection established')

    try:
        query1 = "CREATE TABLE SampleTable \
                  (Id INT PRIMARY KEY, \
                   Name NVARCHAR(100));"
        cursor.execute(query1)
        cnxn.commit()
        print("Table created")

        try:
            query2 = "INSERT INTO \
                      SampleTable (Id, Name) \
                      VALUES (1, 'John'); \
                      INSERT INTO \
                      SampleTable (Id, Name) \
                      VALUES (2, 'Jane'); \
                      INSERT INTO "
```

```python
from azure.storage.blob import BlobServiceClient
import os

# 连接字符串、容器名称和要上传的文件的路径
connection_string = 'DefaultEndpointsProtocol=https;\
AccountName=testblobforsampleusage;\
AccountKey=YjsfMmriMCRCn5Po/DdzCQAiidxPsfgfsdgs45SFGS$%SGDFFADSFGSR$#$AGDFGHDFGHcpjKd6AINtVWMiO9+AStc9nKVA==;\
EndpointSuffix=core.windows.net'
container_name = "firstcontainer"
local_file_path = "/Users/username/hello.csv"

# 初始化 blob 服务并创建容器客户端
blob_service_client = BlobServiceClient.from_connection_string(connection_string)
container_client = blob_service_client.get_container_client(container_name)

# 这里我只使用本地文件路径中的文件名
blob_name = os.path.basename(local_file_path)

# 获取 blob 客户端以访问上传方法
blob_client = container_client.get_blob_client(blob_name)

# 上传 blob
with open(local_file_path, "rb") as data:
    blob_client.upload_blob(data)
```

# Azure Cosmos DB

## 示例代码：数据库操作

```
SampleTable (Id, Name) \

VALUES (3, 'Alice');"

cursor.execute(query2)

cnxn.commit()

print("Rows inserted")

except:

print("unable to insert rows into the table")

cursor.close()

cnxn.close()

except:

print("unable to create the table")

except:

print('Cannot connect to SQL server')
```

## 环境配置文件

环境文件的内容可能如下所示：

```
server = "your-azure-database-server-instance"

database = "your-database"

username = "your-username"

password = "password"

driver = "driver"
```

## Azure Cosmos DB 简介

Azure Cosmos DB 是 Microsoft 提供的一种托管 NoSQL 数据库服务。它是一种多功能服务产品，可以提供 SQL 和 NoSQL 数据库开发，包括关系数据模型、文档存储、图形数据库和向量数据库。Cosmos DB 提供多种 API，每种 API 模拟不同的数据库引擎。这些包括关系 SQL、用于文档数据库的 MongoDB、用于列式数据库的 Cassandra DB、用于图形数据库的 Gremlin 以及用于键值数据库的 Table。从某种意义上说，Cosmos DB 是一种多模型数据库服务，你可以根据数据库应用程序的需求使用合适的 API。在数据库迁移的情况下，你可以选择与你打算迁移的当前关系或 NoSQL 数据库相对应的 API。

你可以通过两种方式使用 Cosmos DB，即预配吞吐量和无服务器模型。顾名思义，在预配吞吐量中，你可以预配你期望数据库提供的吞吐量，同时定义你希望为数据库应用程序获得的性能级别。这里的度量单位称为请求单位。另一种使用 Cosmos DB 的方法是通过无服务器模型。

无服务器模型仅根据它接收和处理的请求数量向你收费。就计费而言，预配吞吐量将根据已预配但未消耗的请求单位数量计算账单，而在无服务器的情况下，计费是根据在一小时内通过执行数据库操作消耗的请求单位数量计算的。

### 关于“请求单位”

请求单位是衡量你如何使用服务的一种方式。你使用 Cosmos DB 执行的每个操作都会消耗一定量的计算、存储和物理内存。请求单位是根据此类消耗计算的。例如，发送到 Cosmos DB 的查询将花费一定数量的请求单位来检索结果。获取结果所需的确切请求单位数量可能取决于表的大小、查询编译时间和行数等参数。

## 使用 Cosmos DB 实例

让我们看看如何使用 MongoDB API 创建一个 Cosmos DB 实例。一旦你选择了 API，该实例的 API 将保持不变。如果你希望使用另一个 API，你可能希望使用相应的 API 创建另一个实例。在我们的例子中，我们选择 MongoDB API。如果你熟悉 MongoDB，那么其用法将与 Cosmos DB 非常相似。

### 选择 API

以下是在 Cosmos DB 中选择 API 的位置：

![图 17-7：为你的 Azure Cosmos DB 实例选择合适的 API](img/615530_1_En_17_Fig7_HTML.jpg)

一个标记为“创建 Azure Cosmos DB 帐户”的网页截图。页面标题为“哪个 API 最适合你的工作负载”。它包括 `Azure Cosmos DB for NoSQL`、`Azure Cosmos DB for NoSQL`、`PostgreSQL`、`MongoDB`、`Apache Cassandra`、`Table` 和 `Apache Gremlin`。每个都包含一个“创建”按钮。

### 选择资源类型

接下来，系统会提示你为实例选择资源。有两个类别：一个基于请求单位，另一个基于虚拟核心。这两个选项都由 Azure 完全管理，并且随着工作负载的增加而扩展。让我们选择虚拟核心选项，这样我们可以选择底层硬件。

界面如下所示：

![图 17-8：为 Cosmos DB 选择硬件资源](img/615530_1_En_17_Fig8_HTML.jpg)

一个标记为“创建 Azure Cosmos DB 帐户 - 选择架构”的网页截图。页面标题为“哪种类型的资源”。它包括 2 个文本框，一个标记为“请求单位数据库帐户”，另一个标记为“vCore 集群”。每个框都有一个标记为“创建”的按钮。

### 配置实例

选择虚拟核心选项后，你将进入配置 Cosmos DB 的页面。在这里，你需要指定订阅或计费帐户以及适当的资源组。资源组是共享相似策略和权限的东西。此外，选择管理员用户名和密码、集群的名称和位置以及其他详细信息。

![图 17-9：为 MongoDB 集群创建新的 Cosmos DB 实例](img/615530_1_En_17_Fig9_HTML.jpg)

一个标记为“创建 Azure Cosmos DB for MongoDB 集群”的对话框。表单包含订阅、资源组、集群名称、免费层、位置、集群层级、MongoDB 版本、管理员用户名、密码和确认密码等字段。有“查看 + 创建”、“上一步”和“下一步：网络”按钮，以及一个“反馈”链接。

### 配置底层集群

你可以选择为底层集群使用免费层。让我们看看为此 Cosmos DB 实例配置底层集群。在配置下，你将拥有各种开发和生产级别的硬件实例，你可以在其中自定义计算、存储和物理内存。此外，你还可以选择高可用性和数据库分片。

MongoDB 中的数据库分片是一种将数据分布在给定集群内的多个工作节点之间的方法。你还可以创建数据的副本，从而在读取/写入操作中获得速度。例如，假设一个包含特定分片的节点每秒可以处理“n”个操作；通过引入包含相同数据的“m”个分片，你可能能够每秒处理“m × n”个操作。

集群配置可能如下所示：

![图 17-10：为 MongoDB 集群配置 Cosmos DB 的硬件](img/615530_1_En_17_Fig10_HTML.jpg)

一个标记为“创建 Azure Cosmos DB for MongoDB 集群”的网页截图。页面包含集群层级、存储、高可用性和分片等字段。它包括一个标记为“成本摘要”的文本框，底部有一个“保存”按钮。

### 配置网络和防火墙

你还可以在网络选项中指定防火墙规则和连接方法。界面如下所示：

![图 17-11：Cosmos DB 的防火墙规则规范](img/615530_1_En_17_Fig11_HTML.jpg)

一个包含网络连接设置的网页截图。页面列出了加密连接和防火墙规则。加密连接在连接方法下选择了“公共访问”选项。防火墙规则包括规则名称、起始 IP 地址和结束 IP 地址字段。

## 连接并上传数据

你可以保留网络设置和其他设置不变，然后单击“创建”。Azure 可能需要几分钟来预配新集群。让我们看看如何连接并将 JSON 文档上传到 Cosmos DB：

```
from azure.cosmos import CosmosClient

import config

import json

url = config.settings['host']

key = config.settings['master_key']

database_name = config.settings['database_id']

container_name = config.settings['container_id']

path_to_json = "/Users/pk/Documents/mock_data.json"

def connect():

    try:

        client = CosmosClient(url,credential=key)

        print("Established connection with the client")

        try:

            database = client.get_database_client(database_name)

            print("Database obtained")

            try:

                container = database.get_container_client(container_name)

                print("Container obtained")

            except:

                print(f"unable to select the container {container_name}")

        except:

            print(f"unable to select the database, {database_name}")

    except:

        print("Unable to connect to the database")

    return container

def upsert(container):

    with open(path_to_json, "rb") as file:

        json_doc = json.load(file)

    for js in json_doc:

        upload_json_doc = container.upsert_item(js)

        print(f"id uploaded is : {js['id']}")
```

```python
def `primaryfunc`():

    `c_name` = `connect`()

    `upsert`(`c_name`)

`primaryfunc`()
```

## Azure Synapse Analytics

到目前为止，我们已经研究了对象存储、关系数据库解决方案和 NoSQL 数据库解决方案。Azure Synapse Analytics 是 Microsoft 提供的一种现代存储解决方案，它能够将关系型、非结构化和半结构化数据存储到单个存储解决方案中。一旦数据进入 Synapse Analytics，就可以轻松使用各种计算层（如 Hadoop、Spark 或 Databricks）来查询数据并生成分析见解。此外，Synapse 数据湖提供了将数据以 `Parquet` 格式保存的选项，从而减少数据占用的空间。

你可以通过两种形式使用 Synapse Analytics，即专用 SQL 池和无服务器 SQL 池。专用 SQL 池以前称为 Azure SQL 数据仓库。专用 SQL 池是在实例创建期间预配的计算资源集合。与 Azure SQL 和 Cosmos DB 类似，这些实例的大小由称为数据仓库单位的单位来衡量。数据仓库单位代表一定数量的计算、内存和 IO 资源。

顾名思义，无服务器 SQL 池是无服务器的。无需配置底层基础设施，也无需进行集群维护。你只需为运行的数据处理付费，因此无需为底层基础设施付费。你可以在几分钟内启动并运行查询数据。此服务在你的数据湖之上创建一个查询层，你可以在数据之上运行 `T-SQL` 查询。

从架构的角度来看，Synapse Analytics 将计算与存储分离。你可以独立于数据来扩展计算。在内部，Synapse SQL 使用基于节点的架构，该架构由控制节点和计算节点组成。提交的 `T-SQL` 代码被路由到控制节点。控制节点使用分布式查询引擎来优化查询以进行并行处理，并将任务分配给计算节点以并行执行查询。

让我们创建一个 Azure Synapse Analytics 实例。我们首先导航到 Azure 控制台并搜索 Synapse Analytics。当 Synapse Analytics 服务页面为我们呈现时，我们将选择创建一个新的 Synapse 工作区。我们首先指定订阅组、资源组和托管资源组。托管资源组是 Azure 为 Synapse 工作区创建的补充资源所在的位置。

界面如下所示：

![](img/615530_1_En_17_Fig12_HTML.jpg)

一个标题为“创建 Synapse 工作区”的网页截图。表单包含订阅、资源组、托管资源组等字段。在工作区详细信息下，它列出了工作区名称、区域、`Data Lake Storage Gen2`、帐户名称和文件系统名称。底部有一个已勾选的“分配我自己”复选框。

图 17-12 创建 Azure Synapse 工作区

我们还必须定义工作区的名称和位置；此外，我们必须指定当前的数据湖或创建一个新的数据湖，这是配置工作区的一部分。输入基本信息后，让我们在安全设置中指定一个本地用户。

一旦 Synapse Analytics 启动并运行，你就可以在仪表板中查看它。界面如下所示：

![](img/615530_1_En_17_Fig13_HTML.jpg)

一个包含 s1 sandbox dl storage account 选项的菜单截图。选项包括概述、活动日志、标签、诊断并解决问题、访问控制、数据迁移、事件、存储浏览器和数据存储。在基本信息下，它列出了资源组、位置、订阅、磁盘状态以及更多带有值的项目。

图 17-13 Azure Synapse 工作区的仪表板

## Azure Data Factory

Azure Data Factory 是一种完全托管的数据集成服务。该服务以无服务器方式提供，因为你无需为底层硬件付费。你可以通过可视化方式或编程方式设计数据集成管道。它拥有多个内置连接器，可实现更顺畅的集成；它还与 git 良好集成，因此你可以在适用的情况下推送代码更改。Azure Data Factory 允许你创建自定义事件触发器，当触发某个事件时，这些触发器会自动执行某些操作。

![](img/615530_1_En_17_Fig14_HTML.jpg)

一个包含“基本信息”、“安全性”、“网络”、“标签”、“查看 + 创建”菜单的截图。在“安全性”下，它突出显示了身份验证，并包括身份验证方法、`SQL Server` 管理员登录名、`SQL` 密码和确认密码字段。

图 17-14 Azure 身份验证

使用 Azure Data Factory，可以在从一个系统加载数据到另一个系统时压缩数据，从而消耗更少的带宽。要开始使用 Azure Data Factory，请访问链接 [`https://adf.azure.com/en/`](https://adf.azure.com/en/) 或在控制台中搜索 Azure Data Factory 找到相同的链接。将打开一个新选项卡，你可以在其中为新的数据工厂指定名称并关联你的订阅帐户。

界面如下所示：

![](img/615530_1_En_17_Fig15_HTML.jpg)

一个标题为“欢迎使用 Azure Data Factory”的对话框截图。页面有一个标题为“创建新数据工厂”的表单。表单包括名称、位置和订阅字段。底部还有一个“创建”按钮。

图 17-15 创建 Azure Data Factory 页面

提交信息后，你可能需要等待几分钟，让 Azure 为你准备 Azure Data Factory 服务。完成后，界面可能如下所示：

![](img/615530_1_En_17_Fig16_HTML.jpg)

一个标记为“Data Factory”的截图。左侧列出了 4 个带有图标和文本的选项。在“我的数据工厂”下，它列出了 4 个带有描述的图标。它包括“摄取”、“复制数据”、“编排”、“无代码数据管道”、“转换数据”、“使用数据流”、“配置 SIS”、“在云中管理包”。

图 17-16 Azure Data Factory 主页

在左侧，你将看到五个选项，其中“主页”是主页面，“创作”是你的数据集成 IDE，“监视”是你编排和观察作业运行的地方，“管理”是你控制 git 集成和其他设置的地方，“学习中心”是你获取 Azure Data Factory 文档和学习支持的地方。

![](img/615530_1_En_17_Fig17_HTML.jpg)

一个计算机屏幕截图，列出了包含“主页”、“创作”、“监视”、“管理”、“学习中心”选项的菜单。在“管理”下，Data Factory 包含“常规”、“连接”、“源代码管理”和“创作”菜单。在“连接”下，“链接服务”被突出显示，底部有一个“创建链接服务”按钮。

图 17-17 Azure Data Factory 控制台

在使用 Azure Data Factory 设计数据集成作业时，你可以创作一个数据集成作业，内部称为管道，它是一组逻辑上排列好要执行的活动。管道内的活动执行单个任务。你可以执行各种活动，这些活动可以连接源和目标数据系统、传输数据以及执行各种转换。

你必须定义一个数据系统连接才能连接到数据系统。要定义数据系统连接信息，请导航到“管理”页面，然后单击“连接”选项卡下的“链接服务”。

让我们尝试在“链接服务”中创建或添加 Azure Blob 存储作为新连接。你可以单击“新建连接”，在数据存储中搜索“`Azure Blob`”，然后单击“继续”。你可以为连接输入名称和描述，然后在“帐户选择方法”下选择 Azure 订阅；选择你的相应订阅和容器名称。

你可以测试连接，如果返回成功，则说明你已成功将 Azure Blob 存储添加到 Azure Data Factory 中。界面如下所示：

![](img/615530_1_En_17_Fig18_HTML.jpg)

一个标记为“新建链接服务”的对话框，列出了一个表单。表单包含名称、描述、通过集成运行时连接、身份验证类型、帐户选择方法、`Azure` 订阅和存储帐户名称等字段。表单包含“创建”、“返回”、“测试连接”和“取消”按钮。

图 17-18 在 Azure Data Factory 中创建新的链接服务

为了熟悉 Azure Data Factory，让我们看看如何将数据从 Azure Blob 存储复制到 Azure SQL 数据库。我们将从源获取一个逗号分隔的数据文件，并将其上传到 Azure Blob 存储。然后，我们将使用 Azure Data Factory 启动从 Blob 存储到 Azure SQL 数据库的复制管道。首先，导航到主页，单击“摄取”选项，然后选择“内置复制任务”。

界面如下所示：

![](img/615530_1_En_17_Fig19_HTML.jpg)

一个标题为“复制数据工具”的网页截图。页面包含一个选项列表，包括“属性”、“源”、“目标”、“设置”和“查看并完成”。在右侧，有“属性”步骤的描述，包括两个选项：“内置复制任务”和“元数据驱动的复制任务”。底部有“下一步”按钮。

图 17-19 Azure Data Factory 的“复制数据”工具

我们将在下一页指定源数据系统，即 Azure Blob 存储。从“源类型”下拉菜单中，选择“Azure Blob 存储”，然后从“连接”下拉菜单中，选择你的 Blob 存储。如果你之前使用了“链接服务”选项，则此选项应该可用。如果没有，过程是相同的。

一旦你的 Blob 存储被链接，你可以浏览对象存储以选择合适的平面文件或包含平面文件的文件夹。如果你计划从一个文件夹上传多个平面文件，请选择“递归”选项。系统将识别所选的平面文件类型，并自动填充文件格式设置。

界面如下所示：

![](img/615530_1_En_17_Fig20_HTML.jpg)

一个标题为“复制数据工具”的页面截图。左侧列出了“属性”、“源”、“数据集”、“配置”、“目标”、“设置”和“查看并完成”。右侧列出了“源数据存储”。源数据存储包括“源类型”、“连接”、“编辑”、“新建连接”、“选项”。底部有“下一步”按钮。

图 17-20 在 Azure Data Factory 中指定源数据存储

现在，让我们指定目标数据系统。你可以选择 Azure SQL 数据库并指定连接，为此你可能希望创建一个新的链接服务，或选择可能已经创建好的链接服务。由于这是一个关系数据库，我们确实可以选择在目标数据库上创建表架构，并将平面文件直接填充到数据库中。如果没有，我们可以选择自动创建数据表并填充此数据表。

界面如下所示：

![](img/615530_1_En_17_Fig21_HTML.jpg)

一个标题为“数据复制工具”的页面截图。左侧列出了选项“属性”、“源”、“目标”、“数据集”。屏幕右侧显示“目标数据存储”。它包括“目标类型”、“连接”、“源”和“目标”字段。底部有“下一步”按钮。

图 17-21 在 Azure Data Factory 中指定目标数据存储

完成此步骤后，让我们在“设置”下命名此数据管道。

界面如下所示：

![](img/615530_1_En_17_Fig22_HTML.jpg)

一个标题为“数据复制工具”的页面截图。左侧列出了选项“属性”、“源”、“目标”、“设置”。屏幕右侧显示“设置”。它包括“任务名称”、“任务描述”、“数据一致性验证”和“容错”字段。

图 17-22 添加此数据管道的名称和描述

成功完成所有这些步骤后，你将看到一个查看屏幕，其中列出了此数据管道的所有参数。如果有任何错误，或者你发现需要更改的内容，你可以编辑相应的字段。如果一切看起来正常，那么你可以单击“下一步”。Azure Data Factory 将对此整个数据管道运行验证。一旦验证过程成功完成，你可能会看到以下屏幕：

![](img/615530_1_En_17_Fig23_HTML.jpg)

一个标题为“数据复制工具”的页面截图。左侧列出了选项“属性”、“源”、“目标”、“设置”、“查看并完成”、“查看”和“部署”。右侧将 Azure Blob 存储链接到 Azure SQL 数据库。它列出了部署步骤，状态为“成功”。底部有“完成”按钮。

图 17-23 数据管道的验证和执行

你可以选择单击“完成”，这将带你回到主页。在 Azure Data Factory 的仪表板上，单击“监视”页面，该页面将列出你存储库中的当前数据管道。你可以单击“重新运行”来运行数据管道。当作业成功完成时，你可能会看到类似以下内容：

![](img/615530_1_En_17_Fig24_HTML.jpg)

一个页面截图，左侧有一个选项列表。它包括“仪表板”、“运行”和“管道运行”。在“管道运行”下，它包括“所有管道运行”、“测试复制-活动运行”。“活动运行”包括活动名称、活动状态和活动类型。

图 17-24 在 Azure Data Factory 中监视和重新运行数据管道

## Azure Functions

Azure Functions 是一种无服务器计算选项，它允许执行执行特定任务的函数，而无需担心底层硬件。Azure 会自动为这些函数提供更新的服务器来执行，并在需要时进行扩展。这些计算资源在运行时动态分配并自动缩放。你只需为你执行的代码（执行持续时间和使用的计算资源）付费。你可以使用 Azure Functions 构建 API、响应另一个服务上发生的事件等。

Azure Functions 的亮点是触发器和绑定的使用。使用触发器和绑定，你可以创建与其他服务交互的函数。触发器基本上是启动函数执行的事件或条件。在 Azure Functions 中，每个函数只能有一个触发器。例如，你可以有一个具有 HTTP 触发器的 Azure 函数，该触发器接收请求并触发函数提供响应；你可以有一个服务触发器，当事件发生时（例如在 Blob 存储中上传了新文件，甚至是一个简单的计时器，按特定计划设置函数执行）激活 Azure 函数。

Azure Functions 中的绑定提供了一种连接到各种数据源的方法；因此，你可以与服务交互（写入和读取数据）。在给定的 Azure 函数中，你可以有多个绑定。Azure Functions 支持两种类型的绑定，即输入和输出绑定。输入绑定可能包括从数据存储系统（如 Blob 存储、数据库、文件系统等）读取数据，输出绑定可能是将数据发送到另一个服务。Azure Functions 实际上支持在给定函数中使用多个输入和输出绑定。

要创建新的 Azure 函数，请导航到 Azure 仪表板，并搜索“`Function App`”。

界面如下所示：

![](img/615530_1_En_17_Fig25_HTML.jpg)

一个标记为“`Function App`”的网页截图。菜单包括“创建”、“管理视图”、“刷新”、“导出到 `CSV`”、“打开查询”和“分配标签”选项。下面的表格包括“名称”、“状态”、“位置”、“定价层”和“应用服务计划”列。表格为空。

图 17-25 Function App——Azure Functions

进入 Azure Functions 后，创建一个新的函数应用。你必须输入订阅详细信息；你还将定义实例详细信息，包括函数应用的名称、运行时堆栈、环境和操作系统。

你还可以定义如何选择托管应用。最具成本效益的选项是无服务器；但是，你也可以选择重用来自其他 Azure 服务的计算资源。

界面如下所示：

![](img/615530_1_En_17_Fig26_HTML.jpg)

一个标题为“创建函数应用”的对话框截图。表单包含订阅、资源组等字段。在“实例详细信息”下，它列出了函数应用名称、部署类型、运行时堆栈、版本、区域和操作系统。操作系统按钮已选中“Linux”。底部有“查看 + 创建”按钮。

图 17-26 在 Azure Functions 中创建新的函数应用

创建函数应用实例后，你可以在其中创建 Azure 函数。让我们探索如何使用核心工具包在本地开发 Azure 函数。

根据操作系统的不同，你可能希望参考这些说明来安装核心工具。

链接如下： [`https://learn.microsoft.com/en-us/azure/azure-functions/functions-run-local?tabs=macos%2Cisolated-process%2Cnode-v4%2Cpython-v2%2Chttp-trigger%2Ccontainer-apps&pivots=programming-language-python`](https://learn.microsoft.com/en-us/azure/azure-functions/functions-run-local?tabs=macos%2Cisolated-process%2Cnode-v4%2Cpython-v2%2Chttp-trigger%2Ccontainer-apps&pivots=programming-language-python)

要检查安装是否完成，你可以打开终端并输入“`func`”，它将显示与 Azure 函数相关的输出。

要在本地创建新的 Azure 函数，请输入

```
func init
```

`func init myfunction --worker-runtime python --model V2`

它将使用适当的 JSON 元素设置整个项目结构。界面如下所示：

![](img/615530_1_En_17_Fig27_HTML.jpg)

一个页面截图。左侧包括 A F U N C、test function、v s code、gitignore、function app、host j son、local settings 和 requirements。右侧包括 func init test function、python，包含 6 个步骤：Writing requirements、function app、host、local settings users。

图 17-27 测试 Azure Functions 中核心工具的安装

左侧的文件夹结构由 Azure Functions 核心工具实用程序创建。你可以导航到 `function_app.py` 并继续编写你的 Azure 函数。以下是一个示例 Azure 函数：

```python
import azure.functions as func
import datetime
import json
import logging

app = func.FunctionApp()

@app.function_name('helloworld')
@app.route(route="route66")
def helloworld(requests: func.HttpRequest) -> func.HttpResponse:
    logging.info('received request for helloworld function')
    user = req.params.get('username')
    if user:
        message = f"Hello, {user}"
    else:
        message = "welcome to Azure functions"
    return func.HttpResponse(
        message,
        status_code=200
    )
```

准备好 Azure 函数后，你可以使用命令行中的 `func start` 运行该函数。界面如下所示：

![](img/615530_1_En_17_Fig28_HTML.jpg)

一个 CLI 截图。它包括使用的 Python 版本、Azure Functions 核心工具版本和函数运行时版本。它包括 2 个函数：hello world 和 test function，以及它们对应的 URL。

图 17-28 在 Azure Functions 中执行函数

当你导航到相应的 URL 时，你可以查看函数的输出。如果你希望将代码发布为 Azure 函数（目前，该函数是在本地开发和部署的），那么你可以使用终端中的以下命令：

```
func azure functionapp publish
```

# Azure Machine Learning

Azure Machine Learning 是一种完全托管的机器学习服务，可帮助开发代码、训练机器学习模型、执行、部署和管理已部署的机器学习模型。Azure Machine Learning 服务提供无服务器计算。Azure Machine Learning 提供的主要服务是 Azure ML Studio 和用于机器学习的 Python SDK。使用 Azure ML Studio，你可以使用 Jupyter Notebook 编写自己的代码，并使用 Azure ML 服务执行相同的代码。Azure 还提供了一个低代码选项，使你能够拖放 ML 数据资产和组件来设计、训练和部署机器学习模型。
要开始使用 Azure Machine Learning，请导航到管理控制台并搜索“Azure machine learning”。你将在市场选项中找到 Azure Machine Learning。选择该服务并创建一个新的机器学习工作区。为你的订阅和工作区提供适当的详细信息。界面如下所示：

![](img/615530_1_En_17_Fig29_HTML.jpg)
*图 17-29 在 Azure Machine Learning 中创建机器学习工作区*
一个标题为“Azure Machine Learning”的对话框截图。表单包含订阅、资源组、名称、区域、存储帐户、密钥保管库和 Application Insights 等字段。每个字段都有一个下拉菜单，其中包含可供选择的选项。它有“查看 + 创建”、“上一步”和“下一步”按钮。

工作区选项用于启用 Azure Machine Learning 提供的所有资源的集中视图。当你预配新的 Azure Machine Learning 工作区时，Azure 会自动创建一组专用于 Azure Machine Learning 的服务。这些是存储帐户、密钥保管库、容器注册表和 Insights。存储帐户是为 Azure Machine Learning 数据资产、日志和报告（如果有）新创建的存储空间。还有一个创建 Blob 的选项。密钥保管库是一种机密管理资源，用于安全存储机器学习工作区中使用的敏感信息。Insights 将在监控机器学习项目期间提供操作见解和诊断信息。容器注册表处理用于机器学习模型、开发环境等的 Docker 容器。创建并可用服务后，界面如下所示：

![](img/615530_1_En_17_Fig30_HTML.jpg)
*图 17-30 Azure ML 服务主页*
一个页面截图。左侧列出了“所有工作区”、“主页”、“模型目录”、“创作”和“资产”。右侧列出了 3 个快捷方式：“创建笔记本”、“添加计算”。在快捷方式下方，包括“计算实例”。

## Azure ML 数据资产

以前在 Azure ML Studio 中称为数据集，数据资产就像是 Azure Machine Learning 中的一个存储库，你可以在其中创建对位于其他位置的数据集的引用。你可以从 Azure 数据存储、公共 URL 和 Azure 开放数据集创建数据集引用。由于数据仍保留在其位置，因此你不会产生存储成本。数据资产中的这些数据集是延迟评估的，这有助于高效使用计算。你可以存储在数据资产中的数据集有两种类型，即 `FileDataSet` 和 `TabularDataSet`。
`FileDataSet` 是对位于其他位置的一个或多个数据集的引用。源数据集可以是任何格式，`FileDataSet` 创建可在机器学习工作流中使用的引用。如果你引用的数据集已经清理完毕并准备好在机器学习管道中使用，那么 `FileDataSet` 是一个不错的选择。
`TabularDataSet` 是另一种类型的数据集，它以其原生格式解析源数据集（一个或多个数据集）并将其转换为表格格式。然后可以将此表格格式数据集导入 Jupyter notebook 或 Spark 环境。`TabularDataSet` 可以解析 JSON 文件、Parquet 文件、SQL 查询的结果以及简单的逗号分隔文件。
以下是如何从 Azure ML Studio 创建这两种数据集类型中的任何一种。你可以单击 Azure ML Studio 中“资产”下的“数据”选项。界面如下所示：

![](img/615530_1_En_17_Fig31_HTML.jpg)
*图 17-31 Azure ML Studio 中的数据资产*
一个页面截图。左侧列出了“所有工作区”、“主页”、“模型目录”、“创作”和“资产”。右侧列出了“数据”。在“数据”下，它列出了“数据资产”、“数据存储”、“数据集监视器”和“数据导入”。它突出了“创建”按钮。中央面板中有一个文件夹图标。

进入此页面后，单击“创建”。你可以指定数据集的名称和描述，并从“类型”下拉菜单中选择“文件”或“表格”。界面如下所示：

![](img/615530_1_En_17_Fig32_HTML.jpg)
*图 17-32 在 Azure ML Studio 中指定数据资产*
一个包含数据类型和数据源的对话框截图。右侧有名称、描述和类型字段。类型字段有一个下拉菜单，其中包含“表格”、“文件”、“文件夹”和“表”选项。已选择“表格”。

## Azure ML 作业

Azure Machine Learning 基本上是一组在底层集群上运行的机器学习任务。你可以构建一个 ML 训练作业，它可能包含一组训练机器学习模型的任务；你也可以构建一个扫描作业，它可能包含一组使用多个超参数执行机器学习模型的任务，并选择最能最小化成本函数的最佳模型。这些作业还会生成日志条目，Azure ML Studio 可以跟踪每个 ML 作业的指标。
要开始开发机器学习模型，你需要一个机器学习工作区和一个计算实例。在前面的部分中，我们已经创建了一个工作区。要创建新的计算实例，你可以选择“管理”下的“计算”选项，或者只需单击仪表板上的“添加计算实例”。界面如下所示：

![](img/615530_1_En_17_Fig33_HTML.jpg)
*图 17-33 在 Azure ML Studio 中添加新的计算实例*
一个页面截图。左侧列出了“数据”和“计算”等选项。右侧列出了 3 个快捷方式：“创建笔记本”、“添加计算”。在快捷方式下方，包括“计算实例”。

你可以为计算实例指定名称，并选择是需要基于 CPU 的实例还是基于 GPU 的实例。实例类型的选择将基于你打算执行的机器学习任务的性质。GPU 始终与并行性和高性能计算相关联，因此，如果你的机器学习模型利用并行性（通过 `Dask` 或 `Ray`），你可以选择此选项。某些应用程序，如训练具有多个层的深度学习模型或计算机视觉任务（图像处理），非常适合使用 GPU。某些机器学习应用程序，如文本建模、大型模型或构建推荐引擎，其底层任务受益于并行性，可以通过 CPU 和 GPU 架构进行训练。对于表格数据，基于 CPU 的计算可能就足够了。
选择虚拟机后，你可以保留其他选项不变，然后继续创建计算实例。界面如下所示：

![](img/615530_1_En_17_Fig34_HTML.jpg)
*图 17-34 在 Azure ML Studio 中创建新的计算实例*
一个页面截图，包括“所需设置”、“计划”、“安全性”、“应用程序”、“标签”和“查看”。在“配置所需设置”下，它有“计算名称”、“虚拟机类型”、“虚拟机大小”以及一个包含“名称”、“类别”、“可用配额”和“成本”的表格。底部有“查看 + 创建”按钮。

创建计算实例后，你可以开始利用该计算资源来开发你的机器学习模型。你可以选择 Jupyter Notebook 选项或基于 Web 的 VS Code 编辑器。界面如下所示：

![](img/615530_1_En_17_Fig35_HTML.jpg)
*图 17-35 活动 ML 计算实例的图示*
一个页面截图。左侧列出了“创作”和“资产”下的选项。右侧在“计算实例”标题下，包括“添加计算实例”和“`ml compute one`”，状态为“正在运行”，VM 大小为“标准”，以及“应用程序”。右侧的一个弹出窗口列出了“VS Code Web 预览”、“VS Code 桌面”、“终端”和“Notebook”。

让我们构建一个分类器模型，该模型根据给定的观察集来分类一个人是否患有糖尿病。根据你的方便，你可以选择使用 Jupyter notebook 或访问 Visual Studio Code。我们的目标是使用 Azure ML Studio 训练模型，并使用 Azure Functions 部署模型。
以下是模型代码：

```python
import pandas as pd
import numpy as np
import pickle
from sklearn.ensemble import RandomForestRegressor
from sklearn.model_selection import train_test_split

df = pd.read_csv("diabetes.tsv",sep="\t")
#print("Top records of the dataset")
#print(df.head())
#print("Analytics on dataset")
```

# 数据预处理与模型训练

```python
print(df.describe())

df = df.drop_duplicates()

df = df.fillna(0)
```

获取与 `Y` 相关性最小的数据点

```python
eliminate = np.argmin(df.corr()["Y"].values)
```

删除最不重要的特征

```python
final_df = df.drop(columns=df.columns[eliminate])
```

随机森林模型参数

```python
params = {
    "max_depth": 4,
    "n_estimators": 250,
    "min_samples_leaf": 4
}

x = final_df.drop(["Y"], axis=1)
y = final_df["Y"]

X_train, X_test, y_train, y_test = train_test_split(x, y, test_size=0.2)

model = RandomForestRegressor(**params)

trained_model = model.fit(X_train, y_train)

pred = trained_model.predict(X_test)

file = 'model.pkl'

pickle.dump(model, open(file, 'wb'))
```

## 部署准备

你可以将此代码作为起点，构建自己的特征，并尝试其他分类器。导出 pickle 文件后，我们可以继续创建新的 Azure 函数来部署模型。如果你之前已经按照“Azure Functions”部分的内容操作过，那么请导航到终端并创建一个名为 `diabetes` 的新 Azure 函数。

操作示例如下：

```
func init diabetes --worker-runtime python --model v2
```

我们将使用 Azure Functions 将模型部署为无服务器 API，并利用 HTTP 触发器。现在，切换到 `diabetes` 文件夹并打开 `function_app.py` 文件。此函数目前仅在本地延迟部署。接下来，我们将此函数部署到 Azure 函数应用中。

## 函数部署

以下是部署模型的代码：

```python
import azure.functions as func
import pickle
import json
import logging

app = func.FunctionApp()

classifier = ['Diabetic', 'Not-diabetic']

@app.function_name('pima-indian-diabetes-model')
@app.route(route="pidm", auth_level=func.AuthLevel.ANONYMOUS)
def main(requests: func.HttpRequest) -> func.HttpResponse:
    filepath = 'model.pkl'
    with open(filepath, 'rb') as file:
        model = pickle.load(file)
    data = requests.get_json()["data"]
    y_pred = model.predict(data)[0]
    pred_class = classifier[y_pred]
    logging.info(f"For the data points: {data}, the prediction is, {pred_class}")
    response = {"class": pred_class}
    return func.HttpResponse(json.dumps(response))
```

## 结论

到目前为止，我们已经探索了从对象存储到高级分析、NoSQL 解决方案、使用 Azure 数据工厂进行数据集成、Azure Functions 以及 Azure ML Studio 等一系列广泛的服务。Azure Functions 展示了如何利用无服务器计算来执行代码。从创建对象存储解决方案到部署机器学习模型，我们结合代码详细研究了这些服务。虽然在一章中难以涵盖所有云计算产品，但我希望我们探讨的这些理念、服务和实践实现，能为你踏上云计算之旅奠定坚实的基础。