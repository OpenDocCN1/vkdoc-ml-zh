# 17. 使用 Scikit-Learn 与 PySpark 构建流水线

在本章中，我们将探讨 Scikit-Learn 和 PySpark 中的流水线技术。通过利用流水线的强大功能，数据科学家可以自动化和标准化建模工作流程中的各个步骤。这有助于构建稳健且可扩展的模型，增强模型的可解释性，并促进额外预处理步骤和特征工程技术的集成。

为了说明流水线如何简化建模流程并提高效率，我们以模拟数据集为例，实现回归流水线。我们生成反映回归问题的模拟数据，其中包含一组输入特征和一个连续目标变量。通过使用流水线技术，我们演示了如何在 Scikit-Learn 和 PySpark 中对数据进行预处理、应用特征缩放以及构建回归模型。虽然我们使用回归来演示流水线方法，但该技术可迁移至任何其他算法，无论是回归任务还是分类任务。

首先，我们介绍流水线的概念，并讨论其在机器学习背景下的重要性。我们将探讨流水线如何使我们能够将各种数据预处理和建模步骤封装并排序成一个连贯的工作流程。通过以这种方式构建分析，我们可以确保可重复性和可维护性，并轻松尝试不同的预处理和建模技术组合。

接着，我们探索 Scikit-Learn 中的流水线功能。我们演示如何构建一个流水线，其中包含使用 `StandardScaler` 进行特征缩放的步骤，以及使用 `LinearRegression` 类进行回归的步骤。我们将逐步讲解如何将流水线拟合到训练数据、对测试数据进行预测以及评估模型性能。

在本章的最后部分，我们将重点转向 PySpark，使计划从 Scikit-Learn 迁移的数据科学家能够受益于强大的分布式数据处理框架。PySpark 为处理大规模数据集提供了可扩展且高效的解决方案。我们将说明如何使用 PySpark 的 MLlib 库构建回归流水线。我们的流水线包含用于组装输入特征的 `VectorAssembler`、用于特征缩放的 `StandardScaler` 以及用于回归建模的 `LinearRegression` 类。我们将探讨将流水线拟合到训练数据、对测试数据进行预测以及评估模型性能的过程。

在本章结束时，读者将深入了解如何利用流水线技术在这两个广泛使用的框架中简化机器学习工作流程。

## 流水线的重要性

在机器学习领域，管理和组织数据预处理与建模的各个阶段可能是一项复杂的任务。它通常涉及多个步骤，例如数据清洗、特征缩放、特征工程和模型拟合。跟踪这些步骤并确保其正确排序可能具有挑战性，尤其是在尝试不同的技术组合或处理大型数据集时。

这正是流水线概念发挥作用的地方。流水线是一种强大的工具，它允许我们将这些预处理和建模步骤封装并排序成一个连贯且精简的工作流程。通过使用流水线来构建分析，我们可以获得几个关键优势。

一个关键优势是封装预处理和建模步骤。流水线提供了一个框架，用于封装机器学习过程中的各个步骤。每个步骤，例如特征缩放或特征工程，都可以定义为流水线内的一个独立组件。这种模块化方法增强了代码的组织性和可读性。此外，它使我们能够轻松修改或替换特定步骤，而不会影响流水线的其余部分。通过封装这些步骤，我们可以保持清晰的关注点分离，并确保每个步骤有效地执行其指定任务。

第二个优势是无缝的工作流程和可重复性。流水线使我们能够为机器学习定义一个清晰且结构化的流程。每个步骤按顺序执行，确保数据从一个阶段无缝流向另一个阶段。这确保了可重复性，因为整个工作流程可以轻松地在新数据集上复制或与同事共享。通过提供定义明确的结构，流水线减少了因手动干预或临时数据处理而可能出现的错误和不一致的可能性。

第三个优势是实验性和灵活性。通过将预处理和建模步骤封装在流水线内，我们可以轻松修改和尝试不同的技术组合。例如，我们可以在不同的特征缩放方法之间切换，或者尝试替代的特征工程方法，而只需进行最少的代码更改。这种灵活性使我们能够高效地探索各种途径并优化回归模型。

最后，流水线增强了可维护性。随着项目的发展，维护和更新机器学习模型可能变得越来越具有挑战性。借助流水线，我们可以通过拥有一个封装整个工作流程的单一代码库来确保可维护性。这使我们能够进行修改或添加新的预处理或建模步骤，而不会破坏整体结构。流水线还有助于代码重用，因为各个步骤可以在多个机器学习项目之间共享。通过采用模块化和可维护的方法，流水线帮助我们有效管理机器学习项目的复杂性。

在本章的后续部分，我们将探讨在两个广泛使用的框架（Scikit-Learn 和 PySpark）中实现流水线。我们将深入探讨构建流水线的细节，并演示它们在回归分析中的实际应用。通过清晰的示例和逐步解释，我们将强调使用流水线来简化建模流程并提高机器学习工作流程效率的好处。

### 使用 Scikit-Learn 构建流水线

Scikit-Learn 充分利用了流水线（Pipeline）概念的优势。通过使用 `Pipeline` 类，你可以无缝地连接转换器和模型，将整个过程视为一个单一的 Scikit-Learn 模型。这种方法简化了工作流程，并实现了高效且直观的数据转换和建模。

该过程的第一步是导入必要的库：

**步骤 1：导入必要的库**

以下代码导入了流水线过程所需的几个函数和类：

```
[In]: from sklearn.model_selection import train_test_split
[In]: from sklearn.pipeline import Pipeline
[In]: from sklearn.preprocessing import StandardScaler
[In]: from sklearn.linear_model import LinearRegression
[In]: from sklearn.metrics import mean_squared_error
[In]: import numpy as np
```

以下是每个函数和类的作用说明：

*   `train_test_split:` 此函数用于将数据集拆分为训练子集和测试子集。它会将数据随机分成两部分：一部分用于训练回归模型，另一部分用于评估其性能。

*   `Pipeline:` 此类提供了一种便捷的方式，可以将多个数据处理步骤链接到一个单一对象中。它允许我们将一系列转换器（数据预处理步骤）和一个估计器（机器学习模型）定义为一个整体单元。

*   `StandardScaler`: 此类用于通过减去均值并除以标准差来缩放每个特征，从而得到一个均值为 0、标准差为 1 的转换后特征。

*   `LinearRegression:` 此类代表一个线性回归模型，该模型用于建模一个因变量与一个或多个自变量之间的关系。

*   `mean_squared_error:` 此函数是一个常见的评估指标，用于衡量回归问题中预测值与实际值之间的平均平方差。它提供了一种评估回归模型性能的方法。

*   `numpy as np:` 这是 Python 中的一个计算库，为大型多维数组提供支持，并提供一组用于操作这些数组的数学函数。我们用它来生成模拟数据集。

**步骤 2：生成数据**

```
[In]: np.random.seed(0)
[In]: num_samples = 1000
[In]: X = np.random.rand(num_samples, 2) * 10
[In]: y = np.random.rand(num_samples) * 20 - 10
```

在此步骤中，我们生成一些随机数据来说明流水线过程。第一行设置了 NumPy 中随机数生成器的种子值。通过设置特定的种子（例如 0），我们可以确保每次运行代码时都会生成相同的随机数序列。

第二行将值 1000 赋给变量 `num_samples`。这表示我们要生成的样本（数据点）数量。

第三行使用 `rand` 函数生成一个介于 0 和 1 之间的随机数的二维数组（X）。X 的形状是 `(num_samples, 2)`，表示将有 `num_samples` 行和 2 列。将随机数乘以 10 可将范围从 [0, 1) 缩放至 [0, 10)。

最后一行使用 `rand` 函数生成一个介于 0 和 1 之间的随机数的一维数组（y）。y 的形状是 `(num_samples)`，表示它将有 `num_samples` 个元素。将随机数乘以 20 可将范围扩展至 [0, 20)，再减去 10 可将范围移至 [-10, 10)。

我们可以通过以下方式查看 X 和 y 的前五行：

X 的前五行：

```
[In]: print(X[0:5])
[Out]:
[[5.48813504 7.15189366]
[6.02763376 5.44883183]
[4.23654799 6.45894113]
[4.37587211 8.91773001]
[9.63662761 3.83441519]]
```

y 的前五个元素：

```
[In]: print(y[0:5])
[Out]: [ 6.23036941 -0.47832028  0.4631198  -4.98958827  2.10086034]
```

在生成上述输出的代码中，`X[0:5]` 这一行对 X 变量进行切片，以检索从索引 0 到索引 4（含）的行，这些行对应 X 的前五行。然后打印出这些行，显示 X 的前五行。类似地，`y[0:5]` 对 y 变量进行切片，以检索从索引 0 到索引 4（含）的元素，这些元素对应 y 的前五个元素。然后打印出这些元素，显示 y 的前五个元素。

**步骤 3：将数据拆分为训练集和测试集**

以下代码使用 `train_test_split` 函数将数据集拆分为训练子集和测试子集：

```
[In]: X_train, X_test, y_train, y_test = train_test_split(X, y,
test_size=0.2, random_state=0)
```

在这段代码中，X 代表输入特征或自变量，y 代表相应的目标变量或因变量。`test_size=0.2` 参数指定 20% 的数据将保留用于测试，其余 80% 将用于训练机器学习模型。`random_state=0` 参数设置随机种子以确保可重复性。变量 `X_train`、`X_test`、`y_train`、`y_test` 代表拆分后得到的数据子集。`X_train` 和 `y_train` 将包含训练数据，而 `X_test` 和 `y_test` 将包含测试数据。

**步骤 4：定义流水线阶段**

这是流水线过程中的一个重要步骤。在以下代码中，定义了机器学习流水线的两个阶段（我们可以定义任意数量的阶段）：

```
[In]: scaler = StandardScaler()
[In]: regression = LinearRegression()
```

第一行创建了 `StandardScaler` 类的一个实例，并将其赋给变量 `scaler`。这是一个用于标准化数值特征的数据预处理步骤。它通过减去均值并除以标准差来缩放特征，从而得到具有零均值和单位方差的转换后特征。

第二行创建了 `LinearRegression` 类的一个实例，并将其赋给变量 `regression`。这个类是我们的机器学习模型，用于线性回归，其目标是通过将线性方程拟合到观测数据来建模因变量与自变量之间的关系。

在典型的流水线中，如下一步所示，这些阶段会使用 `Pipeline` 类组合在一起，其中每个阶段被定义为一个由名称（可选）和相应对象组成的元组。

**步骤 5：创建流水线**

在以下代码中，使用 `Pipeline` 类创建了一个流水线：

```
[In]: pipeline = Pipeline(steps=[('scaler', scaler), ('regression',
regression)])
```

这行代码创建了 `Pipeline` 类的一个实例，并将其赋给变量 `pipeline`。此类允许我们将一系列数据处理步骤和一个估计器定义为一个整体单元。

`Pipeline` 构造函数的 `steps` 参数接受一个元组列表，其中每个元组代表流水线中的一个步骤。在本例中，该列表包含两个元组：

1.  `('scaler', scaler)`：这为步骤定义了名称 `'scaler'`，并将 `scaler` 对象分配给它。这对应于流水线中的 `StandardScaler` 阶段，负责缩放输入特征。

2.  `('regression', regression)`：这为步骤定义了名称 `regression`，并将 `regression` 对象分配给它。这对应于流水线中的 `LinearRegression` 阶段，代表线性回归模型。

需要注意的是，列表中元组的顺序决定了步骤的执行顺序。

**步骤 6：将流水线拟合到训练数据**

在此步骤中，我们将之前定义的流水线拟合到训练数据：

```
[In]: pipeline.fit(X_train, y_train)
```

这行代码在管道对象上调用了 `fit` 方法，该方法将管道拟合到训练数据上。`X_train` 和 `y_train` 变量代表用于拟合管道的训练数据。`X_train` 包含输入特征，`y_train` 包含对应的目标变量或标签。当我们在管道上调用 `fit` 时，它会应用 `StandardScaler` 来缩放特征，然后将 `LinearRegression` 模型拟合到转换后的数据上。

拟合过程涉及根据提供的训练数据估计线性回归模型的系数。这一步使管道能够从训练数据中学习，并为模型进行预测做好准备。

**步骤 7：打印系数**

此步骤从拟合后的管道回归模型中提取系数和截距：

```
[In]: coefficients = pipeline.named_steps['regression'].coef_
[In]: intercept = pipeline.named_steps['regression'].intercept_
[In]: print("Coefficients:", coefficients)
[In]: print("Intercept:", intercept)
[Out]:
Coefficients: [0.1555 -0.0093]
Intercept: 0.0938
```

第一行代码检索作为管道一部分的回归模型的系数。管道的 `named_steps` 属性允许通过特定名称访问各个步骤。在此例中，`regression` 是分配给 `LinearRegression` 步骤的名称。通过在 `LinearRegression` 对象上使用 `.coef_`，我们获得了模型的权重或参数。

第二行代码检索回归模型的截距（或偏置）项。与上一行类似，它使用名称 `regression` 访问 `LinearRegression` 步骤，并通过 `.intercept_` 检索截距值。

最后两条语句打印系数和截距。系数表示线性回归模型中分配给每个特征的权重，指示每个特征对预测结果的贡献。截距项表示当所有特征值为零时的基线预测值。

**步骤 8：在测试数据上进行预测**

以下代码使用拟合后的管道对测试数据进行预测：

```
[In]: y_pred = pipeline.predict(X_test)
```

这行代码使用拟合后的管道对象的 `predict` 方法对测试数据 `X_test` 进行预测。该方法将管道步骤中定义的转换应用于输入数据，然后使用训练好的模型生成预测。`X_test` 变量代表测试数据，其中包含我们想要进行预测的输入特征。

通过在管道上调用 `predict`，它会使用 `StandardScaler` 步骤进行缩放。然后，它使用训练好的 `LinearRegression` 模型基于缩放后的数据生成预测。得到的预测结果被赋值给变量 `y_pred`，这是一个包含与测试数据对应的目标变量预测值的数组或向量。

**步骤 9：打印前五个实际值与预测值**

在此步骤中，我们查看目标变量的前五个实际值及对应的预测值：

|     | 实际值 | 预测值 |
| --- | --- | --- |
| 0   | 7.59 | 0.03 |
| 1   | 8.62 | -0.07 |
| 2   | 6.43 | 0.02 |
| 3   | 7.07 | 0.12 |
| 4   | -1.04 | 0.19 |

```
[In]: import pandas as pd
[In]: data = {'Actual': y_test[:5], 'Predicted': y_pred[:5]}
[In]: df = pd.DataFrame(data)
[In]: print(df.head())
[Out]:
```

代码的第一行将 Pandas 导入为 pd。第二行创建了一个名为 `data` 的 Python 字典，其键代表我们想要在 DataFrame 中创建的列名。值是通过切片 `y_test` 和 `y_pred` 的前五个元素获得的，这些元素包含来自机器学习模型的实际值和预测值。在下一行代码中，通过将 `data` 字典作为参数传递给 `DataFrame()` 构造函数，创建了 Pandas DataFrame `df`。最后，`head()` 方法显示 DataFrame 的前几行。

从输出中我们可以看到，实际值和预测值之间存在很大差异，这表明模型的性能并不理想。对于当前任务来说，这没问题，因为我们使用的模拟数据纯粹是为了说明目的。

**步骤 10：评估模型**

在最后一步中，我们通过计算测试数据上的均方根误差（RMSE）来评估模型的性能：

```
[In]: rmse = np.sqrt(mean_squared_error(y_test, y_pred))
[In]: print("Root Mean Squared Error (RMSE) on test data:", rmse)
[Out]: Root Mean Squared Error (RMSE) on test data: 5.55
```

代码的第一行使用 `mean_squared_error` 函数计算 RMSE。该函数接受两个参数：实际目标值（`y_test`）和预测目标值（`y_pred`）。它计算两组值之间的均方误差。然后对均方误差应用 `np.sqrt` 函数以获得 RMSE。这是一个常用的指标，用于衡量预测值与实际值之间的平均差异。它表示平方差平均值的平方根。计算出的 RMSE 值被赋值给变量 `rmse`。

下一行打印在测试数据上计算出的 RMSE，约为 5.55。该值提供了模型准确性或拟合优度的度量。较低的 RMSE 表示更好的预测性能，因为它代表预测值与实际值之间的平均差异更小。

### 使用 PySpark 构建流水线

与 Scikit-Learn 类似，PySpark 也充分利用了流水线（Pipeline）这一概念。通过使用 `Pipeline` 类，我们可以轻松地将转换器和模型连接起来，将整个过程视为一个连贯的 PySpark 模型。这种方法简化了工作流程，使得数据转换和建模能够无缝、高效地进行。

与我们使用 Scikit-Learn 时类似，首先需要导入必要的 PySpark 库：

**步骤 1：导入必要的库**

流水线过程的第一步是从 PySpark 库中导入必要的类：

```
[In]: from pyspark.sql import SparkSession
[In]: from pyspark.ml.feature import VectorAssembler, StandardScaler
[In]: from pyspark.ml.regression import LinearRegression
[In]: from pyspark.ml import Pipeline
[In]: from pyspark.ml.evaluation import RegressionEvaluator
```

以下是这些类用途的描述：

*   `SparkSession:` 这是 Spark 功能的入口点。它允许我们创建 Spark 应用程序并以结构化的方式与数据进行交互。

*   `VectorAssembler:` 这是一个特征转换器，它将给定的列列表合并成一个单一的向量列。它接收一组输入列，并生成一个包含这些列值向量的新列。

*   `StandardScaler:` 这个类是一个特征转换器，通过减去均值并缩放到单位方差来标准化数值特征。它转换特征值，使其均值为零，标准差为一。

*   `LinearRegression:` 这是一种基于一个或多个预测变量来预测连续目标变量的算法。它根据输入数据拟合一个线性方程，其中假设目标变量是预测变量的线性组合。

*   `Pipeline:` 这是一个实用工具，允许我们将多个转换和估计器阶段链接在一起，形成一个工作流程。它有助于组织和执行对数据的一系列操作，例如特征工程、模型训练和评估。

*   `RegressionEvaluator:` 这是一个用于在 PySpark 中评估回归模型的类。它提供了各种指标和方法来评估回归模型的性能，例如均方根误差（RMSE）。

**步骤 2：创建 SparkSession**

```
[In]: spark = SparkSession.builder.getOrCreate()
```

这行代码创建了一个 Spark Session，使我们能够与 Spark 进行交互。

**步骤 3：生成模拟数据**

在此步骤中，我们生成模拟数据来演示 PySpark 中流水线的概念：

| id | feature1 | feature2 | label |
| --- | --- | --- | --- |
| 0 | 3.51 | 4.01 | -2.85 |
| 1 | 1.97 | 4.49 | 2.36 |
| 2 | 8.14 | 3.70 | -6.81 |
| 3 | 7.40 | 2.95 | 0.93 |
| 4 | 3.64 | 1.12 | -3.44 |

```
[In]: num_samples = 1000
[In]: simulated_data = spark.range(num_samples).selectExpr(
"id as id",
"(RAND() * 10) as feature1",
"(RAND() * 5) as feature2",
"(RAND() * 20 - 10) as label")
[Out]:
```

在上述代码中，我们首先将值 1000 赋给变量 num_samples，它表示我们要为模拟数据集生成的行数或样本数。然后，我们使用 `spark.range()` 函数创建了一个名为 simulated_data 的 DataFrame。这会生成一个包含单列 id 的 DataFrame，其中的数字从 0 到 num_samples-1。这作为模拟数据的索引列。

第二行中的 `selectExpr()` 方法用于选择和转换 DataFrame 中的列。它接受多个参数，每个参数指定一个要应用于列的转换表达式：

*   表达式 “(RAND() * 10) as feature1” 为每一行生成一个介于 0 和 10 之间的随机值，并将其分配给名为 feature1 的新列。`RAND()` 函数生成一个介于 0 和 1 之间的随机数，乘以 10 后将其范围缩放至 0 到 10。

*   表达式 “(RAND() * 5) as feature2” 为每一行生成一个介于 0 和 5 之间的随机值，并将其分配给名为 feature2 的新列。与上一个表达式类似，它使用 `RAND()` 函数生成一个介于 0 和 1 之间的随机数，并将其缩放至 0 到 5。

*   表达式 “(RAND() * 20 - 10) as label” 为每一行生成一个介于 -10 和 10 之间的随机值，并将其分配给名为 label 的新列。`RAND()` 函数生成一个介于 0 和 1 之间的随机数，通过乘以 20 再减去 10，其范围被缩放至 -10 到 10。

如输出所示，生成的 simulated_data DataFrame 有四列：id（由 `spark.range()` 生成）、feature1（0 到 10 之间的随机值）、feature2（0 到 5 之间的随机值）和 label（-10 到 10 之间的随机值）。

**步骤 4：将数据拆分为训练集和测试集**

```
[In]: train_ratio = 0.8
[In]: test_ratio = 1 - train_ratio
[In]: training_data, testing_data = simulated_data.randomSplit([train_ratio, test_ratio], seed=42)
```

在这段代码中，我们使用 `randomSplit()` 函数将 simulated_data DataFrame 拆分为训练集和测试集。第一行将值 0.8 赋给变量 train_ratio，表示 80% 的数据将用于训练。第二行通过从 1 中减去 train_ratio 来计算测试集的比例。由于训练和测试比例之和应等于 1，因此从 1 中减去 train_ratio 得到剩余部分，即测试比例。

最后一行执行将数据实际拆分为训练集和测试集的操作。`randomSplit()` 函数接受两个参数：一个用于训练集和测试集的比例数组，以及一个种子参数，以确保数据的随机拆分是可重现的。

**步骤 5：定义流水线阶段**

在此步骤中，使用 PySpark ML 库定义机器学习工作流程的流水线阶段：

```
[In]: assembler = VectorAssembler(
inputCols=["feature1", "feature2"], outputCol="features")
[In]: scaler = StandardScaler(
inputCol="features", outputCol="scaledFeatures")
[In]: regression = LinearRegression(
featuresCol="ScaledFeatures", labelCol="label")
```

第一行创建了一个 `VectorAssembler` 对象，这是流水线中的一个转换阶段。该组装器将多个输入列（feature1 和 feature2）合并成一个单一的输出列（features）。`inputCols` 参数指定要组装成向量的输入列的名称。在这种情况下，它包括 feature1 和 feature2 列。`outputCol` 参数指定输出列的名称，该列将包含组装后的特征向量。

第二行创建了一个 `StandardScaler` 对象，用于标准化从前一阶段获得的特征向量列。它将特征向量列 features 作为输入（由 `inputCol` 参数指定），并创建一个名为 scaledFeatures 的新列，其中包含标准化后的特征值。缩放是通过减去每个特征的均值并除以标准差来执行的。

第三行创建了一个 `LinearRegression` 对象，这是流水线中的一个估计器阶段，用于构建线性回归模型。`featuresCol` 参数指定包含特征的输入列的名称。它与 `VectorAssembler` 产生的输出列名称（scaledFeatures）相匹配。`labelCol` 参数指定包含标签的输入列的名称。

通过定义这些流水线阶段，我们为机器学习流水线设置了数据转换和建模步骤。这些阶段可以与其他阶段（例如特征转换或附加模型）进一步组合，以在 PySpark 中构建一个用于数据预处理和建模的综合流水线。

**步骤 6：创建流水线**

```
[In]: pipeline_stages = [assembler, scaler, regression]
[In]: pipeline = Pipeline(stages=pipeline_stages)
```

在这段代码中，实际的流水线是通过 PySpark 的 `Pipeline` 类创建的。PySpark 使用与 Scikit-Learn 相同的名称和概念并非巧合，因为它正是从 Scikit-Learn 的流水线实现中汲取了灵感。

该流水线包含三个阶段：assembler（特征组装器）、scaler（缩放器）和 regression（回归器）。

*   assembler 阶段负责将输入特征组装成一个向量。它接收 feature1 和 feature2 这两列作为输入，并创建一个名为 features 的新列，该列包含特征的向量表示。

*   scaler 阶段是一个 `StandardScaler`，用于对上一阶段生成的特征向量列进行标准化。它接收特征向量列 features 作为输入，并创建一个名为 scaledFeatures 的新列，该列包含标准化后的特征值。

*   regression 阶段是一个 `LinearRegression` 估计器，它对缩放后的特征向量列执行线性回归。它接收缩放后的特征向量列 scaledFeatures 和标签列 label 作为输入，以训练线性回归模型。

代码中的 pipeline_stages 列表按执行顺序包含了各个阶段。`Pipeline` 对象以 pipeline_stages 作为输入创建，代表了整个流水线。

**步骤 7：将流水线拟合到训练数据**

```
[In]: pipeline_model = pipeline.fit(training_data)
```

在这一行代码中，使用 `fit()` 方法将流水线拟合到训练数据。拟合流水线意味着按顺序执行流水线的每个阶段，应用定义的转换，并在训练数据上训练指定的模型。

`fit()` 方法接收训练数据作为输入，并依次应用每个阶段的转换，从第一阶段（assembler）开始，到最后阶段（regression）结束。每个阶段的输出作为输入传递给流水线中的下一个阶段。在拟合过程中，流水线阶段中定义的转换被应用于训练数据，并且最后阶段（regression）中指定的模型使用转换后的数据进行训练。

调用 `fit()` 的结果是一个拟合后的流水线模型（pipeline_model），它封装了训练好的模型以及已应用的转换。这个拟合后的流水线模型可用于对新数据进行预测或评估模型性能。

**步骤 8：打印系数**

```
[In]: coefficients = pipeline_model.stages[-1].coefficients
[In]: intercept = pipeline_model.stages[-1].intercept
[In]: print("Coefficients:", coefficients)
[In]: print("Intercept:", intercept)
[Out]: Coefficients: [0.2150,-0.2897] Intercept: 0.1336
```

在这段代码中，我们从拟合后的流水线模型（pipeline_model）中检索训练好的线性回归模型的系数和截距。由于线性回归模型是流水线的最后一个阶段，我们使用 `pipeline_model.stages[-1]` 来访问它。流水线模型的 `stages` 属性包含流水线中所有阶段的列表，而 `-1` 指向最后一个阶段。由于我们在线性回归模型中使用了两个特征（feature1 和 feature2），因此我们得到两个系数，其近似值分别为 0.22 和 -0.29。无论特征数量多少，都会产生截距（约 0.13）。这些结果与 Scikit-Learn 回归模型的结果不同，这表明两个平台之间存在实现上的差异。

**步骤 9：对测试数据进行预测**

```
[In]: predictions = pipeline_model.transform(testing_data)
```

在这一行代码中，我们使用训练好的流水线模型（pipeline_model）对测试数据（testing_data）进行预测。使用 pipeline_model 对测试数据应用 `transform` 方法。该方法按顺序将流水线的每个阶段应用于测试数据。它接收测试数据作为输入，并执行流水线阶段中定义的所有必要转换。

输出的 DataFrame predictions 包含测试数据中的原始列，以及 scaledFeatures 和 prediction 等附加列。prediction 列包含训练好的线性回归模型为测试数据中每一行生成的预测值。

**步骤 10：显示前五个实际值与预测值**

| label | prediction |
| --- | --- |
| 8.46 | -0.22 |
| -9.10 | -0.16 |
| -7.05 | -0.14 |
| 7.65 | -0.15 |
| 5.21 | -0.04 |

```
[In]: predictions.select("label", "prediction").show(5)
[Out]:
```

在这一步中，我们想查看 predictions DataFrame 中前五行的实际标签和对应的预测值。使用 `select` 方法选择 label 和 prediction 列。然后使用 `show()` 方法检索所选列的前五行。

如输出所示，两组值之间存在较大差异，表明模型存在预测误差。考虑到我们使用的是模拟数据集，这并不奇怪，因为我们的目的是说明流水线的概念。

**步骤 11：评估模型**

在最后一步中，我们通过计算测试数据上的均方根误差（RMSE）指标来评估模型的性能：

```
[In]: evaluator = RegressionEvaluator(labelCol="label",
predictionCol="prediction", metricName="rmse")
[In]: rmse = evaluator.evaluate(predictions)
[In]: print("Root Mean Squared Error (RMSE) on test data:", rmse)
[Out]: Root Mean Squared Error (RMSE) on test data: 5.88
```

在第一行代码中，我们创建了一个名为 evaluator 的 `RegressionEvaluator` 对象。我们分别使用 `labelCol` 和 `predictionCol` 参数指定标签列为 label，预测列为 prediction。我们还设置了 metricName 参数为 rmse，以表明我们要计算 RMSE。

在下一行中，我们使用 evaluator 对象的 `evaluate` 方法计算 RMSE。我们将包含实际标签和预测值的 predictions DataFrame 作为参数传递给 evaluate 方法。然后，RMSE 值被赋给变量 rmse。

最后，我们使用格式化字符串打印 RMSE 值（约 5.88），并附带一条描述性消息。输出显示了测试数据上的 RMSE，这指示了模型在实际值与预测值之间的平均差异方面的表现。较低的 RMSE 表示模型性能更好。

值得注意的是，PySpark 的 RMSE 值（5.88）与 Scikit-Learn 获得的值（5.55）不同。包括回归在内的机器学习算法在训练过程中涉及随机性，这可能导致不同运行之间产生略有差异的结果。此外，PySpark 和 Scikit-Learn 可能对其回归模型使用不同的优化技术，从而导致模型预测和 RMSE 值存在差异。

## 整合所有步骤

现在，让我们将前面步骤中的所有相关代码整合到一个代码块中。这将使读者能够在 Scikit-Learn 和 PySpark 中作为一个连贯的整体来执行代码。

### Scikit-Learn

步骤 1：导入必要的库

```
[In]: from sklearn.model_selection import train_test_split
[In]: from sklearn.pipeline import Pipeline
[In]: from sklearn.preprocessing import StandardScaler
[In]: from sklearn.linear_model import LinearRegression
[In]: from sklearn.metrics import mean_squared_error
[In]: import numpy as np
[In]: import pandas as pd
```

步骤 2：生成模拟数据

```
[In]: np.random.seed(0)
[In]: num_samples = 1000
[In]: X = np.random.rand(num_samples, 2) * 10
[In]: y = np.random.rand(num_samples) * 20 - 10
```

步骤 3：将数据拆分为训练集和测试集

```
[In]: X_train, X_test, y_train, y_test = train_test_split(X, y,
test_size=0.2, random_state=0)
```

步骤 4：定义流水线阶段

```
[In]: scaler = StandardScaler()
[In]: regression = LinearRegression()
```

步骤 5：创建流水线

```
[In]: pipeline = Pipeline(steps=[('scaler', scaler), ('regression', regression)])
```

步骤 6：将流水线拟合到训练数据

```
[In]: pipeline.fit(X_train, y_train)
```

步骤 7：打印系数

```
[In]: coefficients = pipeline.named_steps['regression'].coef_
[In]: intercept = pipeline.named_steps['regression'].intercept_
[In]: print("Coefficients:", coefficients)
[In]: print("Intercept:", intercept)
```

步骤 8：对测试数据进行预测

```
[In]: y_pred = pipeline.predict(X_test)
```

步骤 9：打印前五个实际值与预测值

```
[In]: data = {'Actual': y_test[:5], 'Predicted': y_pred[:5]}
[In]: df = pd.DataFrame(data)
[In]: print(df.head())
```

步骤 10：评估模型

```
[In]: rmse = np.sqrt(mean_squared_error(y_test, y_pred))
[In]: print("Root Mean Squared Error (RMSE) on test data:", rmse)
```

### PySpark

步骤 1：导入必要的库

```
[In]: from pyspark.sql import SparkSession
[In]: from pyspark.ml.feature import VectorAssembler, StandardScaler
[In]: from pyspark.ml.regression import LinearRegression
[In]: from pyspark.ml import Pipeline
[In]: from pyspark.ml.evaluation import RegressionEvaluator
```

步骤 2：创建 SparkSession

```
[In]: spark = SparkSession.builder.getOrCreate()
```

步骤 3：生成模拟数据

```
[In]: num_samples = 1000
[In]: simulated_data = spark.range(num_samples).selectExpr(
"id as id",
"(RAND() * 10) as feature1",
"(RAND() * 5) as feature2",
"(RAND() * 20 - 10) as label")
```

步骤 4：将数据拆分为训练集和测试集

```
[In]: train_ratio = 0.8
[In]: test_ratio = 1 - train_ratio
[In]: training_data, testing_data =
simulated_data.randomSplit([train_ratio, test_ratio], seed=42)
```

步骤 5：定义流水线阶段

```
[In]: assembler = VectorAssembler(
inputCols=["feature1", "feature2"], outputCol="features")
[In]: scaler = StandardScaler(
inputCol="features", outputCol="scaledFeatures")
[In]: regression = LinearRegression(
featuresCol="scaledFeatures", labelCol="label")
```

步骤 6：创建流水线

```
[In]: pipeline_stages = [assembler, scaler, regression]
[In]: pipeline = Pipeline(stages=pipeline_stages)
```

步骤 7：将流水线拟合到训练数据

```
[In]: pipeline_model = pipeline.fit(training_data)
```

步骤 8：打印系数

```
[In]: coefficients = pipeline_model.stages[-1].coefficients
[In]: intercept = pipeline_model.stages[-1].intercept
[In]: print("Coefficients:", coefficients)
[In]: print("Intercept:", intercept)
```

步骤 9：对测试数据进行预测

```
[In]: predictions = pipeline_model.transform(testing_data)
```

步骤 10：评估模型

```
[In]: evaluator = RegressionEvaluator(labelCol="label",
predictionCol="prediction", metricName="rmse")
[In]: rmse = evaluator.evaluate(predictions)
[In]: print("Root Mean Squared Error (RMSE) on test data:", rmse)
```

步骤 11：显示前五个实际值与预测值

```
[In]: predictions.select("label", "prediction").show(5)
```

## 总结

在本章中，我们探讨了 Scikit-Learn 和 PySpark 中的流水线技术。通过利用流水线的强大功能，数据科学家可以自动化和标准化建模工作流程中的各个步骤。这有助于创建稳健且可扩展的模型，增强模型的可解释性，并促进额外预处理步骤和特征工程技术的集成。

我们演示了如何构建一个流水线，该流水线包含使用 `StandardScaler` 进行特征缩放的步骤，以及使用 `LinearRegression` 类进行回归的步骤。我们逐步讲解了将流水线拟合到训练数据、对测试数据进行预测以及评估模型性能的完整过程。

在下一章（也是本书的最后一章）中，我们将深入探讨使用 Scikit-Learn 和 PySpark 在生产环境中部署机器学习模型的实际方面，以此作为本书的总结。

