# 10. 使用 Pandas、Scikit-Learn 和 PySpark 进行支持向量机分类

本章探讨支持向量机（SVM），这是一种广泛使用的监督学习算法，以其在二分类任务中的有效性而闻名。SVM 旨在找到一个最优超平面（一个能将不同类别成员的对象分开的决策平面），该超平面能最大化不同类别数据点之间的间隔。超平面充当决策边界，每一侧对应一个类别。间隔表示超平面与每个类别最近点之间的垂直距离。间隔越大，表示分离效果越好；间隔越小，则表明决策边界不够理想。

SVM 的一个优势是能够处理线性可分和非线性可分的数据。对于非线性数据，SVM 利用“核技巧”将数据转换到更高维的空间，使得线性分离成为可能。这种转换涉及增加超出原始特征空间的维度或特征数量。通常，这是通过核函数实现的，该函数隐式地对数据应用非线性变换，而无需显式计算和存储变换后的特征向量。本质上，核技巧简化了数据转换，节省了内存和计算资源。

然而，SVM 高度依赖核函数及其相关参数的选择。选择不合适的核函数或错误配置参数可能导致次优结果。此外，训练 SVM，特别是使用非线性核或处理大型数据集时，计算量可能很大且耗时。随着数据集规模的增长，训练时间也会增加。由于可解释性有限，SVM 通常被视为“黑盒”算法。此外，虽然使用 SVM 进行二分类已经很成熟，但将其扩展到多分类可能具有挑战性。

在本章中，我们将构建、训练和评估一个 SVM 分类器，并用它来预测肿瘤是恶性还是良性。这基于威斯康星州乳腺癌（诊断）数据集，该数据集广泛用于探索和评估分类算法。它提供了一个真实场景，机器学习技术可以帮助根据可测量的特征区分良性和恶性肿瘤。

我们将使用两个库：Scikit-Learn 和 PySpark。通过比较它们的 Python 代码，我们旨在强调它们的相似性，使数据科学家更容易从 Scikit-Learn 过渡到 PySpark，并利用 PySpark 的分布式计算能力。此外，我们还将对 Pandas 和 PySpark 进行比较，以展示它们的相似性，从而突出 PySpark 在处理大规模数据项目方面的适用性。

## 数据集

癌症数据集，也称为威斯康星州乳腺癌（诊断）数据集，包含与乳腺癌细胞特征相关的各种特征的测量值。目标是基于这些特征预测肿瘤是恶性（癌性）还是良性（非癌性）。

该数据集提供了 30 个不同属性的信息，包括平均半径、平均纹理和平均平滑度。这些特征是根据乳腺肿块的细针抽吸（FNA）数字化图像计算得出的。每个实例都有一个对应的目标标签，指示类别：0 表示良性，1 表示恶性。

该数据集可在 UCI 机器学习网站上免费下载。以下是贡献者姓名、捐赠日期、数据集名称、网站名称以及可下载该数据集的 URL：

标题：威斯康星州乳腺癌（诊断）

来源：UCI 机器学习

URL：[`https://archive.ics.uci.edu/dataset/17/breast+cancer+wisconsin+diagnostic`](https://archive.ics.uci.edu/dataset/17/breast%252Bcancer%252Bwisconsin%252Bdiagnostic)

贡献者：William Wolberg 等人

日期：1995 年

该数据集也可在 Scikit-Learn 中获得。以下代码演示了如何访问它：

步骤 1：导入必要的库

```
[In]: import pandas as pd
[In]: from sklearn.datasets import load_breast_cancer
```

步骤 2：加载威斯康星州乳腺癌数据集

```
[In]: data = load_breast_cancer()
```

步骤 3：为特征创建一个 Pandas DataFrame

```
[In]: pandas_df = pd.DataFrame(data.data, columns=data.feature_names)
```

步骤 4：将目标变量作为一列添加到 DataFrame 中

```
[In]: pandas_df['target'] = data.target
```

现在我们可以探索数据集了。首先，我们可以使用 Pandas 的 `shape` 属性来获取 DataFrame 的维度（行数和列数）：

```
[In]: pandas_df.shape
[Out]: (569, 31)
```

该数据集有 569 行和 31 列（30 个特征加上目标变量）。

我们可以使用 Pandas 的 `columns` 属性来访问 DataFrame 的列标签或名称：

```
[In]: print(pandas_df.columns)
[Out]:
Index(['mean radius', 'mean texture', 'mean perimeter', 'mean area', 'mean smoothness', 'mean compactness', 'mean concavity', 'mean concave points', 'mean symmetry', 'mean fractal dimension', 'radius error', 'texture error', 'perimeter error', 'area error', 'smoothness error', 'compactness error', 'concavity error', 'concave points error', 'symmetry error', 'fractal dimension error', 'worst radius', 'worst texture', 'worst perimeter', 'worst area', 'worst smoothness', 'worst compactness', 'worst concavity', 'worst concave points', 'worst symmetry', 'worst fractal dimension', 'target'], dtype='object')
```

接下来，我们可以选择并打印行和列的子集：

|   | mean radius | mean texture | mean perimeter | mean area |
|---|-------------|--------------|----------------|-----------|
| 0 | 17.99       | 10.38        | 122.8          | 1001      |
| 1 | 20.57       | 17.77        | 132.9          | 1326      |
| 2 | 19.69       | 21.25        | 130            | 1203      |
| 3 | 11.42       | 20.38        | 77.58          | 386.1     |
| 4 | 20.29       | 14.34        | 135.1          | 1297      |

```
[In]: selected_columns = pandas_df.iloc[0:5, 0:4]
[In]: print(selected_columns)
[Out]:
```

我们还可以输出一个包含目标列唯一值的数组：

```
[In]: pandas_df['target'].unique()
[Out]: [0 1]
```

输出表明目标变量是 0（肿瘤为良性）或 1（肿瘤为恶性或癌性）。

要使用 PySpark 进行探索性数据分析，我们首先需要创建一个 PySpark DataFrame。最简单的方法是使用 PySpark 的 `createDataFrame()` 方法将 pandas_df DataFrame 转换为 PySpark：

```
[In]: from pyspark.sql import SparkSession
[In]: spark = SparkSession.builder.getOrCreate()
[In]: spark_df = spark.createDataFrame(pandas_df)
```

PySpark 没有像 Pandas 那样的内置 `shape` 属性来获取 spark_df 的维度（行数和列数）。因此，我们需要执行两个操作：`count()` 和 `len()` 来分别返回行数和列数：

```
[In]: spark_df_shape = (spark_df.count(), len(spark_df.columns))
[In]: print(spark_df_shape)
[Out]: (569, 31)
```

与 Pandas 类似，我们可以使用 PySpark 的 `columns` 属性来打印 DataFrame 的列名：

```
[In]: print(spark_df.columns)
[Out]:
['mean radius', 'mean texture', 'mean perimeter', 'mean area', 'mean smoothness', 'mean compactness', 'mean concavity', 'mean concave points', 'mean symmetry', 'mean fractal dimension', 'radius error', 'texture error', 'perimeter error', 'area error', 'smoothness error', 'compactness error', 'concavity error', 'concave points error', 'symmetry error', 'fractal dimension error', 'worst radius', 'worst texture', 'worst perimeter', 'worst area', 'worst smoothness', 'worst compactness', 'worst concavity', 'worst concave points', 'worst symmetry', 'worst fractal dimension', 'target']
```

我们可以选择并打印行和列的子集：

| mean radius | mean texture | mean perimeter | mean area |
| --- | --- | --- | --- |
| 17.99 | 10.38 | 122.8 | 1001 |
| 20.57 | 17.77 | 132.9 | 1326 |
| 19.69 | 21.25 | 130 | 1203 |
| 11.42 | 20.38 | 77.58 | 386.1 |
| 20.29 | 14.34 | 135.1 | 1297 |

```
[In]: selected_columns = spark_df.columns[0:4]
[In]: spark_df.select(selected_columns).show(5)
[Out]:
```

最后，我们可以使用 `distinct()` 函数获取目标列的唯一值：

| target |
| --- |
| 0 |
| 1 |

```
[In]: spark_df.select('target').distinct().show()
[Out]:
```

目标变量有两个值：0 表示良性肿瘤，1 表示恶性肿瘤。

## 支持向量机分类

上一节进行的探索性数据分析很有帮助，因为它帮助我们了解了数据的结构和特征。

本节介绍如何使用 Scikit-Learn 和 PySpark 库实现一个 SVM 分类器，用于乳腺癌分类。代码使用了威斯康星州乳腺癌数据集，该数据集被加载并划分为训练集和测试集。然后使用 `StandardScaler` 对特征进行缩放，以确保整个数据集具有一致的尺度。在 SVM 中，特征缩放并非严格必需，但建议进行，因为它可以提高模型性能和收敛速度。SVM 试图找到最优超平面，以最大间隔将不同类别的数据点分开。

使用线性核的 SVM 模型在缩放后的训练数据上进行训练。训练好的模型用于对测试集进行预测。计算了各种评估指标，包括准确率、精确率、召回率、F1 分数以及受试者工作特征曲线下面积，以评估模型的性能。最后，打印出这些评估指标。

### 使用 Scikit-Learn 的线性 SVM

我们首先使用 Scikit-Learn 库构建、训练和评估线性 SVM 分类器。我们遵循以下步骤：

步骤 1：导入必要的库

```
[In]: from sklearn.datasets import load_breast_cancer
[In]: from sklearn.model_selection import train_test_split
[In]: from sklearn.svm import SVC
[In]: from sklearn.metrics import accuracy_score, precision_score, recall_score, f1_score, confusion_matrix, roc_auc_score
[In]: from sklearn.preprocessing import StandardScaler
```

这些导入提供了必要的函数和工具，用于加载威斯康星州乳腺癌数据集，将其划分为训练集和测试集，应用特征缩放，训练 SVM 模型，并使用各种指标评估其性能。

`load_breast_cancer` 函数用于加载威斯康星州乳腺癌数据集。`train_test_split` 函数用于将数据集划分为训练子集和测试子集，这对于评估 SVM 分类器的性能至关重要。`SVC` 类是实现分类任务的线性 SVM 算法。`accuracy_score`、`precision_score`、`recall_score`、`f1_score`、`confusion_matrix` 和 `roc_auc_score` 函数用于评估 SVM 模型的性能。最后，`StandardScaler` 类用于通过去除均值并缩放至单位方差来标准化特征。这个预处理步骤确保特征具有相似的尺度。

步骤 2：加载威斯康星州乳腺癌数据集

```
[In]: data = load_breast_cancer()
[In]: X = data.data
[In]: y = data.target
```

这段代码加载乳腺癌数据集，将输入特征分离到 X 中，并将相应的目标值分配给 y。

步骤 3：将数据划分为训练集和测试集

```
[In]: X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)
```

在这段代码中，`train_test_split()` 函数用于将数据划分为训练数据集和测试数据集。该函数将输入特征 X 和目标值 y 作为参数，并将它们分成四个独立的数据集：X_train、X_test、y_train 和 y_test。`test_size` 参数设置为 0.2，这意味着 20% 的数据将用于测试，其余 80% 将用于训练模型。`random_state` 参数设置为 42。该参数通过固定随机种子来确保可重复性。这意味着每次运行代码时，都会得到相同的训练集和测试集数据划分。

步骤 4：使用 StandardScaler 缩放特征

```
[In]: scaler = StandardScaler()
[In]: X_train_scaled = scaler.fit_transform(X_train)
[In]: X_test_scaled = scaler.transform(X_test)
```

在这段代码中，使用 Scikit-Learn 库的 `StandardScaler()` 类来标准化特征数据。首先，创建一个 `StandardScaler` 类的实例，并将其赋值给变量 scaler。接下来，在 scaler 对象上调用 `fit_transform()` 方法，并将 X_train 作为参数。该方法计算 X_train 数据集中每个特征的均值和标准差，然后应用变换来标准化数据。标准化后的特征数据被赋值给变量 X_train_scaled。

在标准化训练数据之后，在 scaler 对象上调用 `transform()` 方法，并将 X_test 作为参数。该方法使用从训练数据计算出的均值和标准差，对测试数据应用相同的变换。标准化后的测试数据被赋值给变量 X_test_scaled。

步骤 5：训练线性 SVM 模型

```
[In]: svm = SVC(kernel='linear', random_state=42)
[In]: svm.fit(X_train_scaled, y_train)
```

在这段代码中，创建了一个 SVM 分类器，并在标准化后的训练数据上对其进行训练。首先，从 Scikit-Learn 库中创建一个 SVC 类的实例，并将其赋值给变量 `svm`。SVC 类代表 SVM 分类器。`kernel` 参数设置为 `linear`，表示 SVM 将使用线性核函数。`random_state` 参数设置为 42，通过固定 SVM 的随机种子来确保结果的可复现性。

接下来，在 `svm` 对象上调用 `fit()` 方法，传入标准化后的训练数据 (`X_train_scaled`) 和对应的目标值 (`y_train`) 作为参数。该方法在提供的数据上训练 SVM 分类器。

**步骤 6：在测试集上进行预测**

```
[In]: y_pred = svm.predict(X_test_scaled)
```

在这段代码中，使用训练好的 SVM 分类器对标准化后的测试数据进行目标值预测。在 SVM 对象 (`svm`) 上调用 `predict()` 方法，并将标准化后的测试数据 (`X_test_scaled`) 作为参数传入。该方法将训练好的 SVM 模型应用于测试数据，并返回预测的目标值。

**步骤 7：计算评估指标**

```
[In]: accuracy = accuracy_score(y_test, y_pred)
[In]: precision = precision_score(y_test, y_pred)
[In]: recall = recall_score(y_test, y_pred)
[In]: f1 = f1_score(y_test, y_pred)
[In]: cm = confusion_matrix(y_test, y_pred)
[In]: auc = roc_auc_score(y_test, y_pred)
```

在这段代码中，计算了几个性能指标来评估 SVM 分类器 (`svm`) 在测试数据上的预测效果：

*   **准确率**：使用 `accuracy_score()` 函数计算预测的准确率。它将预测标签 (`y_pred`) 与真实标签 (`y_test`) 进行比较，并返回正确预测实例的比例。

*   **精确率**：使用 `precision_score()` 函数计算预测的精确率。精确率衡量在所有被预测为正类的实例（真正例 + 假正例）中，正确预测的正类实例（真正例）所占的比例。它反映了分类器避免假正例预测的能力。

*   **召回率**：使用 `recall_score()` 函数计算预测的召回率。召回率，也称为灵敏度或真正例率，衡量在所有实际的正类实例（真正例 + 假负例）中，被正确预测的正类实例（真正例）所占的比例。它反映了分类器识别正类实例的能力。

*   **F1 分数**：使用 `f1_score()` 函数计算 F1 分数，它是精确率和召回率的加权平均值。它提供了一个平衡精确率和召回率的单一指标，给出了一个整体的性能度量。

*   **混淆矩阵**：使用 `confusion_matrix()` 函数创建一个混淆矩阵，这是一个总结分类模型性能的表格。它显示了真正例、真负例、假正例和假负例预测的数量。

*   **ROC 曲线下面积 (AUC)**：使用 `roc_auc_score()` 函数计算接收者操作特征 (ROC) 曲线下的面积。ROC 曲线代表了真正例率与假正例率之间的权衡。AUC 值越高，表示分类性能越好。

**步骤 8：打印评估指标**

```
[In]: print("Area Under ROC:", auc)
[In]: print("Accuracy:", accuracy)
[In]: print("Precision:", precision)
[In]: print("Recall:", recall)
[In]: print("F1 Score:", f1)
[In]: print("Confusion Matrix:")
[In]: print(cm)
[Out]:
Area Under ROC: 0.96
Accuracy: 0.96
Precision: 0.97
Recall: 0.96
F1 Score: 0.96
Confusion Matrix:
[[41  2]
 [ 3 68]]
```

对该输出的解释如下：

*   **ROC 曲线下面积 (AUC)**：值约为 0.96，表明 SVM 分类器在区分正类和负类方面表现良好。AUC 越高，分类器正确分类实例的能力越强。

*   **准确率**：准确率为 0.96，表明 SVM 分类器正确预测了测试数据中约 96% 的实例的类别标签。

*   **精确率**：精确率得分为 0.97，表明 SVM 分类器在其分类为正类的所有实例中，真正例预测（正确预测的正类实例）的比例很高。这表明假正例率很低。

*   **召回率**：召回率得分为 0.96，表明 SVM 分类器正确识别了约 96% 的实际正类实例。其假负例率很低。

*   **F1 分数**：F1 分数为 0.96，将精确率和召回率结合成一个单一指标。它在精确率和召回率之间取得了平衡，表明 SVM 分类器整体性能良好。

*   **混淆矩阵**：混淆矩阵的格式如下：

[[真负例 假正例]

[假负例 真正例]]

在此例中，SVM 分类器预测了 41 个真负例、2 个假正例、3 个假负例和 68 个真正例。

### 使用 PySpark 实现线性 SVM

现在我们可以编写 PySpark 代码来构建、训练和评估一个随机森林分类器，类似于我们使用 Scikit-Learn 在相同癌症数据集上构建的模型。与在 Scikit-Learn 中一样，该模型使用默认超参数构建。由于两个平台使用不同的默认超参数，它们的结果可能会有所不同。在第 16 章中，我们将演示如何微调算法的超参数。

与 Scikit-Learn 类似，导入必要的库是我们的起点：

**步骤 1：导入必要的库和模块**

```
[In]: from pyspark.ml.feature import StandardScaler
[In]: from pyspark.ml.classification import LinearSVC
[In]: from pyspark.ml.evaluation import BinaryClassificationEvaluator
[In]: from pyspark.ml.linalg import Vectors
[In]: from pyspark.sql import functions as F
[In]: from pyspark.sql import SparkSession
```

通过导入这些库和模块，我们可以访问必要的功能和类，以便在 PySpark 框架内执行与数据预处理、机器学习算法、评估和数据操作相关的任务。

`StandardScaler` 类是一个转换器，将用于标准化我们癌症数据集中的特征。`LinearSVC`（线性支持向量分类器）是用于分类的机器学习算法。`BinaryClassificationEvaluator` 用于评估模型的性能。`Vectors` 模块提供了处理向量数据的数据结构，而 `F` 提供了一组可应用于 Spark DataFrame 或列以进行数据操作和转换的内置函数。

**步骤 2：创建 SparkSession**

```
[In]: spark = SparkSession.builder.getOrCreate()
```

**步骤 3：加载乳腺癌威斯康星数据集**

```
[In]: data = load_breast_cancer()
```

**步骤 4：将 NumPy 数组转换为列表**

```
[In]: X = [Vectors.dense(x) for x in data.data.tolist()]
[In]: y = data.target.tolist()
```

这两行代码将乳腺癌数据集中的特征数据和目标标签转换为适合训练 SVM 分类器的格式（X 和 y）。

**步骤 5：将列表转换为 Spark DataFrame**

```
[In]: spark_df = spark.createDataFrame(zip(X, y), ["features", "label"])
```

此代码根据特征数据 (X) 和目标标签 (y) 创建一个 Spark DataFrame。`zip(X, y)` 将特征数据 X 和目标标签 y 组合成一个元组列表，其中每个元组包含一个特征向量及其对应的标签。`createDataFrame()` 方法通过接受两个参数（压缩后的元组列表和列名 ["features", "label"]）来创建一个 Spark DataFrame (spark_df)。

**步骤 6：将数据拆分为训练集和测试集**

```
[In]: train_data, test_data = spark_df.randomSplit([0.8, 0.2], seed=42)
```

此代码将 spark_df 随机拆分为两个子集：训练集 (train_data) 和测试集 (test_data)。`randomSplit()` 方法根据提供的权重拆分数据：80% 分配给训练集 (train_data)，20% 分配给测试集 (test_data)。`seed=42` 设置随机种子，以确保每次运行代码时都能获得相同的拆分结果。

**步骤 7：使用 `StandardScaler` 缩放特征**

```
[In]: scaler = StandardScaler(inputCol="features", outputCol="scaledFeatures")
[In]: scaler_model = scaler.fit(train_data)
[In]: train_data_scaled = scaler_model.transform(train_data)
[In]: test_data_scaled = scaler_model.transform(test_data)
```

`StandardScaler` 缩放癌症数据集的特征。此过程有助于标准化特征并使它们处于相似的尺度。`inputCol="features"` 指定 DataFrame 中包含特征的输入列的名称，而 `outputCol="scaledFeatures"` 指定转换后的 DataFrame 中将包含缩放后特征的输出列的名称。转换后的 DataFrame 将有一个名为 "scaledFeatures" 的新列，用于存储缩放后的值。

下一行代码 `scaler_model = scaler.fit(train_data)` 将 `StandardScaler` 对象拟合到训练数据。它计算训练集中特征列的均值和标准差，这些将用于缩放特征。

最后，`train_data_scaled = scaler_model.transform(train_data)` 和 `test_data_scaled = scaler_model.transform(test_data)` 分别将缩放转换应用于训练数据和测试数据。它使用训练好的 scaler_model 来转换 train_data 和 test_data DataFrame 中的特征列。转换后的 DataFrame（train_data_scaled 和 test_data_scaled）将包含一个名为 "scaledFeatures" 的附加列，其中包含原始特征的缩放值。

**步骤 8：训练 SVM 模型**

```
[In]: svm = LinearSVC()
[In]: svm_model = svm.fit(train_data_scaled)
```

`LinearSVC` 是 PySpark 中的分类器算法，它实现了用于二分类的线性 SVM。它通过最大化类别之间的间隔来寻找最佳分离不同类别数据点的超平面。`svm` 是 `LinearSVC` 分类器的一个实例。

下一行代码 `svm_model = svm.fit(train_data_scaled)` 使用经过 `StandardScaler` 缩放后的训练数据来训练 `LinearSVC` 模型。`fit(train_data_scaled)` 是 `LinearSVC` 对象的一个方法，用于将模型拟合到训练数据。它接受训练数据 (train_data_scaled) 作为输入，并学习 SVM 模型的参数以最佳地拟合数据。拟合后的模型存储在 `svm_model` 变量中，稍后可用于对新的、未见过的数据进行预测。

**步骤 9：对测试集进行预测**

```
[In]: predictions = svm_model.transform(test_data_scaled)
```

此代码使用 PySpark 中训练好的 SVM 模型 (svm_model) 生成对测试数据的预测。`svm_model` 是通过将模型拟合到缩放后的训练数据而获得的训练好的 `LinearSVC` 模型。`transform(test_data_scaled)` 是训练好的模型的一个方法，它将学习到的模型应用于测试数据 (test_data_scaled) 以生成预测。`transform()` 方法的输出是一个 DataFrame (predictions)，其中包含测试数据的原始列以及表示预测标签和其他相关信息的附加列。

**步骤 10：评估模型**

为了评估线性 SVM 二分类器的性能，我们计算几个评估指标，从 ROC 曲线下面积开始：

*   **计算 AUC（曲线下面积）：**

```
    [In]: evaluator = BinaryClassificationEvaluator(labelCol="label", rawPredictionCol="rawPrediction")
    [In]: area_under_roc = evaluator.evaluate(predictions)
```

`BinaryClassificationEvaluator` 是 PySpark 中专门为二分类问题设计的评估器。这适用于我们的情况，因为我们正在预测肿瘤是良性的 (0) 还是恶性的 (1)。`labelCol="label"` 指定 predictions DataFrame 中包含测试数据真实标签的列名。`rawPredictionCol="rawPrediction"` 指定 predictions DataFrame 中包含模型生成的原始预测或分数的列名。

下一行代码 `area_under_roc = evaluator.evaluate(predictions)` 使用 `BinaryClassificationEvaluator` 计算接收者操作特征 (ROC) 曲线下面积 (AUC)。

*   **计算其他评估指标：**

    ```
    [In]: tp = predictions.filter("label = 1 AND prediction = 1").count()
    [In]: fp = predictions.filter("label = 0 AND prediction = 1").count()
    [In]: tn = predictions.filter("label = 0 AND prediction = 0").count()
    [In]: fn = predictions.filter("label = 1 AND prediction = 0").count()
    [In]: accuracy = (tp + tn) / (tp + tn + fp + fn)
    [In]: precision = tp / (tp + fp)
    [In]: recall = tp / (tp + fn)
    [In]: f1 = 2 * (precision * recall) / (precision + recall)
    [In]: confusion_matrix = [[tn, fp], [fn, tp]]
    ```

PySpark ML 中的 `BinaryClassificationEvaluator` 可以计算 AUC，但缺少用于计算准确率、精确率、召回率、F1 分数和混淆矩阵等其他指标的内置函数。因此，我们使用提供的代码手动计算这些指标。这段代码基于 SVM 模型在测试数据上的预测结果，计算各种额外的评估指标。让我们逐步分解：

*   `tp = predictions.filter("label = 1 AND prediction = 1").count()` 计算真正例（tp）预测的数量。它过滤预测 DataFrame，选择真实标签（label）为 1 且预测标签（prediction）也为 1 的行，然后统计这些行的数量。

*   `fp = predictions.filter("label = 0 AND prediction = 1").count()` 计算假正例（fp）预测的数量。它过滤预测 DataFrame，选择真实标签为 0 且预测标签为 1 的行，然后统计这些行的数量。

*   `tn = predictions.filter("label = 0 AND prediction = 0").count()` 计算真负例（tn）预测的数量。它过滤预测 DataFrame，选择真实标签为 0 且预测标签也为 0 的行，然后统计这些行的数量。

*   `fn = predictions.filter("label = 1 AND prediction = 0").count()` 计算假负例（fn）预测的数量。它过滤预测 DataFrame，选择真实标签为 1 且预测标签为 0 的行，然后统计这些行的数量。

这些计算使我们能够获得评估指标所需的四个值：真正例、假正例、真负例和假负例。

*   `accuracy = (tp + tn) / (tp + tn + fp + fn)` 计算模型的准确率。它将真正例和真负例之和除以总预测数，总预测数包括真正例、真负例、假正例和假负例。

*   `precision = tp / (tp + fp)` 计算模型的精确率。它将真正例除以真正例与假正例之和。精确率衡量在所有被预测为正例的实例中，正确预测的比例。

*   `recall = tp / (tp + fn)` 计算模型的召回率（也称为灵敏度或真正例率）。它将真正例除以真正例与假负例之和。召回率衡量在所有实际为正例的实例中，正确预测的比例。

*   `f1 = 2 * (precision * recall) / (precision + recall)` 计算 F1 分数，它是精确率和召回率的加权平均值。它提供了一个平衡精确率和召回率的单一指标。

*   `confusion_matrix = [[tn, fp], [fn, tp]]` 创建一个以列表形式呈现的混淆矩阵。第一个列表表示真负例（tn）和假正例（fp）的数量，第二个列表表示假负例（fn）和真正例（tp）的数量。

这些评估指标和混淆矩阵通过量化模型的准确率、精确率、召回率和 F1 分数，并根据真实标签和预测标签将预测结果分类，有助于评估 SVM 模型的性能。

步骤 11：打印评估指标

```
[In]: print("Area Under ROC:", area_under_roc)
[In]: print("Accuracy:", accuracy)
[In]: print("Precision:", precision)
[In]: print("Recall:", recall)
[In]: print("F1 Score:", f1)
[In]: print("Confusion Matrix:")
[In]: for row in confusion_matrix:
print(row)
[Out]:
Area Under ROC: 0.9976
Accuracy: 0.9603
Precision: 0.9605
Recall: 0.9733
F1 Score: 0.9669
Confusion Matrix:
[48, 3]
[2, 73]
```

该输出可以解释如下：

*   **ROC 曲线下面积（0.99）**：ROC 曲线下面积是衡量模型区分两个类别能力的指标。其取值范围为 0 到 1，值越高表示性能越好。在本例中，SVM 模型取得了非常高的 AUC，接近 1，表明其具有出色的区分能力。

*   **准确率（0.96）**：准确率表示模型所有预测中正确预测（包括真正例和真负例）的比例。0.96 的准确率表明模型能够正确预测测试数据中约 96% 的实例类别。

*   **精确率（0.96）**：精确率衡量在所有被预测为正例的实例中，正确预测的正例（真正例）的比例。0.96 的精确率表明模型预测为正例的实例中，有 96% 确实是正例。

*   **召回率（0.97）**：召回率，也称为灵敏度或真正例率，表示在所有实际为正例的实例中，正确预测的正例（真正例）的比例。0.97 的召回率表明模型能够正确识别 97% 的正例实例。

*   **F1 分数（0.97）**：F1 分数是精确率和召回率的加权平均值，提供了一个平衡两者的单一指标。其取值范围为 0 到 1，值越高表示精确率和召回率之间的平衡越好。0.97 的 F1 分数表明 SVM 模型在精确率和召回率之间取得了良好的平衡。

*   **混淆矩阵**：混淆矩阵以表格形式呈现模型的性能。它总结了模型在真正例、假正例、真负例和假负例方面的预测结果。模型的混淆矩阵如下：

[48, 3]

[2, 73]

左上角的值（48）表示真负例（TN）的数量，即正确预测为负例的实例数。右上角的值（3）表示假正例（FP）的数量，即错误预测为正例的实例数。左下角的值（2）表示假负例（FN）的数量，即错误预测为负例的实例数。右下角的值（73）表示真正例（TP）的数量，即正确预测为正例的实例数。

## 整合所有步骤

在本章前面的部分中，我们分别展示了每个建模步骤的代码示例。然而，在本节中，我们的目标是将所有这些步骤中的相关代码整合到一个代码块中。这使得读者能够将代码作为一个整体来执行。

### Scikit-Learn

步骤 1：导入必要的库

```
[In]: from sklearn.datasets import load_breast_cancer
[In]: from sklearn.model_selection import train_test_split
[In]: from sklearn.svm import SVC
[In]: from sklearn.metrics import accuracy_score, precision_score, [In]: recall_score, f1_score, confusion_matrix, roc_auc_score
[In]: from sklearn.preprocessing import StandardScaler
```

步骤 2：加载威斯康星州乳腺癌数据集

```
[In]: data = load_breast_cancer()
[In]: X = data.data
[In]: y = data.target
```

步骤 3：将数据拆分为训练集和测试集

```
[In]: X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)
```

步骤 4：使用 StandardScaler 缩放特征

```
[In]: scaler = StandardScaler()
[In]: X_train_scaled = scaler.fit_transform(X_train)
[In]: X_test_scaled = scaler.transform(X_test)
```

步骤 5：训练 SVM 模型

```
[In]: svm = SVC(kernel='linear', random_state=42)
[In]: svm.fit(X_train_scaled, y_train)
```

步骤 6：对测试集进行预测

```
[In]: y_pred = svm.predict(X_test_scaled)
```

步骤 7：计算评估指标

```
[In]: accuracy = accuracy_score(y_test, y_pred)
[In]: precision = precision_score(y_test, y_pred)
[In]: recall = recall_score(y_test, y_pred)
[In]: f1 = f1_score(y_test, y_pred)
[In]: cm = confusion_matrix(y_test, y_pred)
[In]: auc = roc_auc_score(y_test, y_pred)
```

步骤 8：打印评估指标

```
[In]: print("Area Under ROC:", auc)
[In]: print("Accuracy:", accuracy)
[In]: print("Precision:", precision)
[In]: print("Recall:", recall)
[In]: print("F1 Score:", f1)
[In]: print("Confusion Matrix:")
[In]: print(cm)
```

### PySpark

步骤 1：导入必要的库

```
[In]: from pyspark.ml.feature import StandardScaler
[In]: from pyspark.ml.classification import LinearSVC
[In]: from pyspark.ml.evaluation import BinaryClassificationEvaluator
[In]: from pyspark.ml.linalg import Vectors
[In]: from pyspark.sql import functions as F
[In]: from pyspark.sql import SparkSession
```

步骤 2：创建 SparkSession

```
[In]: spark = SparkSession.builder.getOrCreate()
```

步骤 3：加载威斯康星州乳腺癌数据集

```
[In]: data = load_breast_cancer()
```

步骤 4：将 NumPy 数组转换为列表

```
[In]: X = [Vectors.dense(x) for x in data.data.tolist()]
[In]: y = data.target.tolist()
```

步骤 5：将列表转换为 Spark DataFrames

```
[In]: spark_df = spark.createDataFrame(zip(X, y), ["features", "label"])
```

步骤 6：将数据拆分为训练集和测试集

```
[In]: train_data, test_data = spark_df.randomSplit([0.8, 0.2], seed=42)
```

步骤 7：使用 StandardScaler 缩放特征

```
[In]: scaler = StandardScaler(inputCol="features", outputCol="scaledFeatures")
[In]: scaler_model = scaler.fit(train_data)
[In]: train_data_scaled = scaler_model.transform(train_data)
[In]: test_data_scaled = scaler_model.transform(test_data)
```

步骤 8：训练 SVM 模型

```
[In]: svm = LinearSVC()
[In]: svm_model = svm.fit(train_data_scaled)
```

步骤 9：对测试集进行预测

```
[In]: predictions = svm_model.transform(test_data_scaled)
```

步骤 10：评估模型

```
[In]: evaluator = BinaryClassificationEvaluator(labelCol="label", rawPredictionCol="rawPrediction")
[In]: area_under_roc = evaluator.evaluate(predictions)
```

步骤 11：计算评估指标

```
[In]: tp = predictions.filter("label = 1 AND prediction = 1").count()
[In]: fp = predictions.filter("label = 0 AND prediction = 1").count()
[In]: tn = predictions.filter("label = 0 AND prediction = 0").count()
[In]: fn = predictions.filter("label = 1 AND prediction = 0").count()
[In]: accuracy = (tp + tn) / (tp + tn + fp + fn)
[In]: precision = tp / (tp + fp)
[In]: recall = tp / (tp + fn)
[In]: f1 = 2 * (precision * recall) / (precision + recall)
[In]: confusion_matrix = [[tn, fp], [fn, tp]]
```

步骤 12：打印评估指标

```
[In]: print("Area Under ROC:", area_under_roc)
[In]: print("Accuracy:", accuracy)
[In]: print("Precision:", precision)
[In]: print("Recall:", recall)
[In]: print("F1 Score:", f1)
[In]: print("Confusion Matrix:")
[In]: for row in confusion_matrix:
print(row)
```

## 总结

本章使用乳腺癌数据集介绍了支持向量机（SVM）。它使用了 Pandas、Scikit-Learn 和 PySpark 进行数据处理、探索和机器学习。本章讨论了 SVM 的优缺点，以及用于处理非线性可分数据的核技巧。我们构建、训练并评估了线性 SVM 分类器，用它来预测肿瘤是恶性还是良性。这非常合适，因为 SVM 擅长二分类，而乳腺癌数据由二元标签组成：0（肿瘤为良性）和 1（肿瘤为恶性）。该模型实现了高准确率。

在下一章中，我们将介绍另一种监督分类算法：朴素贝叶斯。朴素贝叶斯是一种基于贝叶斯定理的流行且简单的分类算法。它广泛应用于各种分类任务，包括文本分类、垃圾邮件检测和情感分析。

