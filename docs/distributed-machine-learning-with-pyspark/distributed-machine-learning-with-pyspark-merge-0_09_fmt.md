# 9. 使用 Scikit-Learn 和 PySpark 进行随机森林分类

在本章中，我们继续探讨基于树的监督学习分类方法，具体来说是随机森林。我们将使用前一章中使用的相同数据集，构建、训练并评估一个随机森林分类器，以对鸢尾花的种类进行分类。之前我们强调过，决策树是擅长分类任务的强大机器学习算法。尽管如此，它们容易过拟合，尤其是当树生长得过深时。为了解决这个问题，集成方法（如随机森林）已变得流行。这些方法结合多个决策树来提升分类性能。通过执行本章提供的代码，我们可以将准确率与决策树获得的结果进行比较。这有助于确定随机森林在多大程度上提升了基于树的模型的性能。

与前一章一样，我们将使用两个库：Scikit-Learn 和 PySpark。通过比较它们的 Python 代码，我们将突出它们的相似之处，从而促进从 Scikit-Learn 到 PySpark 的过渡，并利用 PySpark 分布式计算能力的优势。

我们在前一章中探讨了鸢尾花数据集的特征。总结来说，该数据集包含 150 行和 6 列，涵盖四个特征（花萼长度、花萼宽度、花瓣长度和花瓣宽度）、一个以数值形式（`target`）和文本形式（`target_names`）表示的目标变量，并且没有空值。

## 随机森林分类

随机森林分类器基于多个决策树的组合来对鸢尾花的种类进行分类。随机森林中的每个决策树都在数据的不同子集上进行训练，这些子集通过一种称为自助聚合或装袋的过程创建。在训练期间，随机森林中的每个决策树都会接触到特征的随机子集和训练样本的随机子集。这种随机性在决策树之间引入了多样性，从而减少了过拟合并提升了模型的性能。

在对新的鸢尾花进行预测时，随机森林中的每个决策树都会根据花的特征独立预测其种类。最终预测结果通过森林中所有决策树的个别预测进行多数投票或平均来确定。

Scikit-Learn 和 PySpark 都提供了用于构建、训练和评估随机森林算法的工具。首先，让我们在分别使用这两个库创建模型之前，先比较一下这些工具。

### Scikit-Learn 与 PySpark 在随机森林上的相似之处

下表展示了在构建随机森林分类器时，Scikit-Learn 所使用的类与函数，以及它们在 PySpark 中的对应项：

| 任务 | Scikit-Learn | PySpark |
| --- | --- | --- |
| 数据准备 |   |   |
| 数据拆分 | `train_test_split` | `randomSplit` |
| 特征缩放 | `StandardScaler` | `StandardScaler` |
| 类别编码 | `OneHotEncoder` | `OneHotEncoder` |
| 缺失值处理 | `SimpleImputer` | `Imputer` |
| 特征向量化 | 不适用 | `VectorAssembler` |
| 模型训练 |   |   |
| 模型类 | `RandomForestClassifier` | `RandomForestClassifier` |
| 训练 | `fit()` | `fit()` |
| 模型评估 |   |   |
| 准确率 | `accuracy_score` | `MultiClassificationEvaluator` |
| 精确率 | `precision_score` |   |
| 召回率 | `recall_score` |   |
| F1 分数 | `f1_score` |   |
| 预测 |   |   |
| 生成预测 | `predict` | `transform` |

这些对比表明，Scikit-Learn 和 PySpark 都遵循相同的数据准备、模型训练、模型评估和预测的建模步骤。有趣的是，在某些情况下，甚至函数或类的名称都是相似的。例如 `StandardScaler`（用于通过移除均值并缩放至单位方差来标准化特征）、`OneHotEncoder`（用于将类别数据转换为独热编码特征）、`RandomForestClassifier`（代表随机森林算法）以及 `fit()` 方法（用于训练分类器）。

在接下来的小节中，我们将把这些建模步骤转化为 Scikit-Learn 和 PySpark 代码。需要注意的是，我们使用默认超参数构建模型，这意味着两个平台的结果可能会有所不同。我们将在第 16 章演示如何微调随机森林模型的超参数。

### 使用 Scikit-Learn 实现随机森林

我们首先使用 Scikit-Learn 来演示如何构建、训练和评估一个随机森林模型，其目的是根据鸢尾花的特征测量值来预测其种类。我们首先导入必要的库，然后定义关键函数，以确保随机森林分类的直观执行。

#### 步骤 1. 导入必要的库

以下代码导入了必要的函数和类，包括用于加载鸢尾花数据集的 `load_iris` 函数、用于将数据拆分为训练集和测试集的 `train_test_split` 函数、用于构建随机森林模型的 `RandomForestClassifier` 类，以及用于评估预测准确率的 `accuracy_score` 函数：

```
[In]: from sklearn.datasets import load_iris
[In]: from sklearn.model_selection import train_test_split
[In]: from sklearn.ensemble import RandomForestClassifier
[In]: from sklearn.metrics import accuracy_score
```

#### 步骤 2. `load_iris_dataset()` 函数

此函数加载并返回鸢尾花数据集的特征 (X) 和标签 (y)。它使用 Scikit-Learn 的 `load_iris()` 函数获取数据集，并将特征赋值给 X，标签赋值给 y：

```
[In]: def load_iris_dataset():
        iris = load_iris()
        X = iris.data
        y = iris.target
        return X, y
```

#### 步骤 3. `build_random_forest()` 函数

此函数使用特征 (X) 和标签 (y) 构建并返回一个随机森林分类器。它初始化一个 `RandomForestClassifier` 对象，将其赋值给 `rf_classifier`，并使用 `fit()` 方法将分类器拟合到训练数据上：

```
[In]: def build_random_forest(X, y):
        rf_classifier = RandomForestClassifier()
        rf_classifier.fit(X, y)
        return rf_classifier
```

#### 步骤 4. `evaluate_model()` 函数

此函数评估并返回随机森林分类器 (`rf_classifier`) 在测试特征 (`X_test`) 和标签 (`y_test`) 上的准确率分数。它使用分类器的 `predict()` 方法预测测试数据的标签，将预测标签与真实标签进行比较，并使用 `accuracy_score()` 函数计算准确率分数：

```
[In]: def evaluate_model(rf_classifier, X_test, y_test):
        y_pred = rf_classifier.predict(X_test)
        accuracy = accuracy_score(y_test, y_pred)
        return accuracy
```

#### 步骤 5. 使用函数

现在，我们可以使用实际的鸢尾花数据集来调用上述函数：

##### 5.1. 加载鸢尾花数据集

```
[In]: X, y = load_iris_dataset()
```

##### 5.2. 将数据集拆分为训练集和测试集

```
[In]: X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)
```

##### 5.3. 构建随机森林模型

```
[In]: rf_classifier = build_random_forest(X_train, y_train)
```

##### 5.4. 评估模型

```
[In]: accuracy = evaluate_model(rf_classifier, X_test, y_test)
```

##### 5.5. 打印准确率

```
[In]: print("Accuracy:", accuracy)
[Out]: 1.00
```

输出显示模型准确预测了每一种情况，达到了 100% 的准确率。前五行数据的实际值与预测值对比展示了预测值与实际值的完美匹配：

| 预测值 | 实际值 |
| --- | --- |
| 1 | 1 |
| 0 | 0 |
| 2 | 2 |
| 1 | 1 |
| 1 | 1 |

```
[In]: y_pred = rf_classifier.predict(X_test)
[In]: print("Predicted\tActual")
[In]: for i in range(5):
    print(f"{y_pred[i]}\t\t{y_test[i]}")
[Out]: 
```

100% 的准确率表明，Scikit-Learn 随机森林算法的表现与前一章中使用的决策树算法一样好，后者也达到了 100% 的准确率。鉴于 100% 是可达成的最高准确率，我们不能期望两种模型类型之间有所改进。然而，对于 PySpark 来说情况并非如此，我们将在下一小节中看到。

最后，我们可以使用 Scikit-Learn 中的 `feature_importances_` 属性来识别模型预测的主要驱动因素。运行以下代码可以发现，花瓣的长度和宽度是模型预测的主要贡献因素：

```python
# 获取特征重要性及其对应的名称
feature_importances = rf_classifier.feature_importances_
feature_names = load_iris().feature_names

# 创建 (特征名称, 重要性) 元组列表
feature_importance_tuples = [(feature_name, importance) for feature_name, importance in zip(feature_names, feature_importances)]

# 按重要性降序排序列表
sorted_feature_importance_tuples = sorted(feature_importance_tuples, key=lambda x: x[1], reverse=True)

# 打印排序后的特征重要性
for feature_name, importance in sorted_feature_importance_tuples:
    print(f"{feature_name}: {importance}")
```

输出结果为：
```
petal length (cm): 0.46
petal width (cm): 0.42
sepal length (cm): 0.09
sepal width (cm): 0.03
```

### 使用 PySpark 实现随机森林

在本小节中，我们将开发与上一小节提供的 Scikit-Learn 代码等效的 PySpark 代码。建模步骤大致相同：数据准备、模型训练、模型评估和预测。与 Scikit-Learn 类似，我们通过导入必要的库来开始模型构建过程：

#### 步骤 1. 导入必要的库

```python
from sklearn.datasets import load_iris
import pandas as pd
from pyspark.sql import SparkSession
from pyspark.ml.feature import VectorAssembler
from pyspark.ml.classification import RandomForestClassifier
from pyspark.ml.evaluation import MulticlassClassificationEvaluator
```

#### 步骤 2. `convert_to_spark_df()` 函数

为了能够使用 PySpark，我们需要将包含鸢尾花数据集的 Pandas DataFrame 转换为 PySpark DataFrame。`convert_to_spark_df()` 函数接收一个 Pandas DataFrame（`pandas_df`）和一个 SparkSession（`spark`）作为输入。它使用 SparkSession 的 `createDataFrame()` 方法将 Pandas DataFrame 转换为 Spark DataFrame。然后返回生成的 Spark DataFrame：

```python
def convert_to_spark_df(pandas_df, spark):
    return spark.createDataFrame(pandas_df)
```

#### 步骤 3. `build_random_forest()` 函数

`build_random_forest()` 函数接收一个 Spark DataFrame（`data`）作为输入。它创建一个 `RandomForestClassifier()` 对象 `rf`，并将标签列设置为 `label`，特征列设置为 `features`。然后，该函数使用 `fit` 方法将随机森林模型拟合到提供的数据上，从而得到一个训练好的模型。最后，返回训练好的模型：

```python
def build_random_forest(data):
    rf = RandomForestClassifier(labelCol="label",
                                featuresCol="features")
    model = rf.fit(data)
    return model
```

#### 步骤 4. `evaluate_model()` 函数

`evaluate_model()` 函数接收训练好的随机森林模型（`model`）和一个 Spark DataFrame（`data`）作为输入。它使用训练好的模型通过 `transform()` 方法对提供的数据进行预测。预测结果存储在新的 DataFrame `predictions` 中。然后，该函数创建一个 `MulticlassClassificationEvaluator` 对象 `evaluator`，并将标签列设置为 `label`，预测列设置为 `prediction`，评估指标设置为 `accuracy`。它使用评估器的 `evaluate()` 方法评估预测的准确率，从而得到一个准确率分数。该函数返回准确率分数和预测结果 DataFrame：

```python
def evaluate_model(model, data):
    predictions = model.transform(data)
    evaluator = MulticlassClassificationEvaluator(labelCol="label",
                                                  predictionCol="prediction",
                                                  metricName="accuracy")
    accuracy = evaluator.evaluate(predictions)
    return accuracy, predictions
```

#### 步骤 5. 使用函数

现在我们可以调用前面的函数，以获取准确率指标来评估模型性能：

**5.1. 加载鸢尾花数据集**

```python
iris = load_iris()
```

**5.2. 访问特征数据（X）和目标标签（y）**

```python
X = iris.data
y = iris.target
```

**5.3. 定义列名**

```python
feature_names = iris.feature_names
target_names = iris.target_names
```

**5.4. 创建包含列名的 DataFrame**

```python
df = pd.DataFrame(X, columns=feature_names)
df['label'] = y
```

**5.5. 创建 SparkSession**

```python
spark = SparkSession.builder.appName("IrisRandomForest").getOrCreate()
```

**5.6. 将 Pandas DataFrame 转换为 PySpark DataFrame**

```python
data = convert_to_spark_df(df, spark)
```

**5.7. 准备特征向量**

```python
assembler = VectorAssembler(inputCols=data.columns[:-1], outputCol="features")
transformed_data = assembler.transform(data)
```

**5.8. 将数据集拆分为训练集和测试集**

```python
train_data, test_data = transformed_data.randomSplit([0.8, 0.2], seed=42)
```

**5.9. 构建随机森林模型**

```python
model = build_random_forest(train_data)
```

**5.10. 评估模型并获取预测结果**

```python
accuracy, predictions = evaluate_model(model, test_data)
```

此时，我们已经可以比较一部分预测数据和实际数据，以了解模型的表现：

| 标签 | 预测值 |
|------|--------|
| 0    | 0      |
```

### 5.11 打印准确率

输出结果表明，对于这五个鸢尾花（Iris setosa）样本记录，模型成功地将每个预测值与实际标签匹配。然而，如下一个输出所示，整体准确率低于 100%，因为准确率指标是基于整个数据点样本计算的。

输出结果表明，在整个数据点样本上，随机森林模型达到了约 97% 的良好准确率。作为参考，在上一章中，决策树分类器达到了 94% 的较低准确率。因此，在本章引言中假设随机森林通过组合多个决策树来提高预测准确率，从而优于决策树算法，这一假设是准确的。

最后，我们可以利用 PySpark 中的 `featureImportances` 属性来确定对达到此准确率贡献最大的特征。

输出结果表明，花瓣宽度和长度对模型的预测贡献最大，因为它们具有最显著的重要性分数。

## 整合所有代码

在本章前面的部分中，我们提供了分别说明每个建模步骤的代码示例。然而，在本节中，我们的目标是将这些步骤中的所有相关代码整合到一个代码块中。这使得数据科学家能够将代码作为一个整体来执行。

### Scikit-Learn

```
from sklearn.datasets import load_iris
from sklearn.model_selection import train_test_split
from sklearn.ensemble import RandomForestClassifier
from sklearn.metrics import accuracy_score
def load_iris_dataset():
    """
    加载并返回鸢尾花数据集的特征和标签。
    返回:
    X (array-like): 鸢尾花数据集的特征（属性）。
    y (array-like): 鸢尾花数据集的目标标签。
    """
    iris = load_iris()
    X = iris.data
    y = iris.target
    return X, y
def build_random_forest(X, y):
    """
    使用提供的特征和标签构建并返回一个随机森林分类器。
    参数:
    X (array-like): 数据集的特征（属性）。
    y (array-like): 数据集的目标标签。
    返回:
    RandomForestClassifier: 一个训练好的随机森林分类器。
    """
    rf_classifier = RandomForestClassifier()
    rf_classifier.fit(X, y)
    return rf_classifier
def evaluate_model(rf_classifier, X_test, y_test):
    """
    评估提供的分类器在测试数据上的准确率并返回。
    参数:
    rf_classifier (RandomForestClassifier): 一个训练好的随机森林分类器。
    X_test (array-like): 测试特征（属性）。
    y_test (array-like): 测试数据的真实标签。
    返回:
    float: 分类器在测试数据上的准确率。
    """
    y_pred = rf_classifier.predict(X_test)
    accuracy = accuracy_score(y_test, y_pred)
    return accuracy

# 加载鸢尾花数据集
X, y = load_iris_dataset()

# 将数据集拆分为训练集和测试集
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)

# 构建随机森林模型
rf = build_random_forest(X_train, y_train)

# 评估模型
accuracy = evaluate_model(rf_classifier, X_test, y_test)

# 打印准确率
print("Accuracy:", accuracy)

# 打印前 5 行真实标签和预测标签
y_pred = rf_classifier.predict(X_test)
print("Predicted\tActual")
for i in range(5):
    print(f"{y_pred[i]}\t\t{y_test[i]}")
```

### PySpark

# 使用随机森林对鸢尾花数据进行分类

本章将演示如何使用 `scikit-learn`、`pandas` 和 `PySpark` 库，通过随机森林算法对经典的鸢尾花数据集进行分类。

## 导入必要的库

首先，我们需要导入所有必需的库和函数。

```python
from sklearn.datasets import load_iris
import pandas as pd
from pyspark.sql import SparkSession
from pyspark.ml.feature import VectorAssembler
from pyspark.ml.classification import RandomForestClassifier
from pyspark.ml.evaluation import MulticlassClassificationEvaluator
```

## 定义辅助函数

接下来，我们定义几个辅助函数来封装数据转换、模型构建、评估和打印的逻辑。

### 转换数据格式
`convert_to_spark_df` 函数用于将 `pandas` `DataFrame` 转换为 `Spark` `DataFrame`。
```python
def convert_to_spark_df(pandas_df, spark):
    """
    将 pandas DataFrame 转换为 Spark DataFrame。
    参数:
    pandas_df (pd.DataFrame): 要转换的 pandas DataFrame。
    spark (SparkSession): 用于创建 Spark DataFrame 的 SparkSession 对象。
    返回:
    DataFrame: 从提供的 pandas DataFrame 创建的 Spark DataFrame。
    """
    return spark.createDataFrame(pandas_df)
```

### 构建随机森林模型
`build_random_forest` 函数用于构建并训练一个随机森林分类器。
```python
def build_random_forest(data):
    """
    构建并训练一个随机森林分类器。
    参数:
    data (DataFrame): 包含训练数据的 Spark DataFrame。
    返回:
    RandomForestClassificationModel: 训练好的随机森林模型。
    """
    rf = RandomForestClassifier(labelCol="label",
                                featuresCol="features")
    model = rf.fit(data)
    return model
```

### 评估模型性能
`evaluate_model` 函数用于评估模型在给定数据上的准确率。
```python
def evaluate_model(model, data):
    """
    评估随机森林分类器在提供数据上的性能。
    参数:
    model (RandomForestClassificationModel): 训练好的随机森林模型。
    data (DataFrame): 包含待评估数据的 Spark DataFrame。
    返回:
    tuple: 包含准确率分数和带有预测结果的 DataFrame 的元组。
    """
    predictions = model.transform(data)
    evaluator = MulticlassClassificationEvaluator(labelCol="label",
                                                  predictionCol="prediction",
                                                  metricName="accuracy")
    accuracy = evaluator.evaluate(predictions)
    return accuracy, predictions
```

### 打印对比结果
`print_actual_vs_predicted` 函数用于从预测结果 `DataFrame` 中打印真实标签与预测标签的对比。
```python
def print_actual_vs_predicted(predictions, n_rows=5):
    """
    从预测结果的 DataFrame 中打印真实标签与预测标签的对比。
    参数:
    predictions (DataFrame): 包含真实标签和预测标签的 DataFrame。
    n_rows (int): 要显示的行数。默认为 5。
    返回:
    None
    """
    results = predictions.select("label", "prediction").limit(n_rows).toPandas()
    print(results)
```

## 加载与准备数据

现在，我们开始处理数据。

### 加载鸢尾花数据集
我们使用 `scikit-learn` 内置的函数加载数据。
```python
# 加载鸢尾花数据集
iris = load_iris()

# 访问特征数据 (X) 和目标标签 (y)
X = iris.data
y = iris.target

# 定义列名
feature_names = iris.feature_names
target_names = iris.target_names
```

### 创建 DataFrame
我们将数据整合到一个 `pandas` `DataFrame` 中。
```python
# 创建一个带有列名的 DataFrame
df = pd.DataFrame(X, columns=feature_names)
df['label'] = y
```

### 初始化 Spark 会话并转换数据
我们需要一个 `SparkSession` 来使用 `PySpark`，然后将 `pandas` `DataFrame` 转换为 `Spark` `DataFrame`。
```python
# 创建一个 SparkSession
spark = SparkSession.builder.appName("IrisRandomForest").getOrCreate()

# 将 Pandas DataFrame 转换为 PySpark DataFrame
data = convert_to_spark_df(df, spark)
```

### 特征工程与数据集拆分
我们使用 `VectorAssembler` 将特征列合并为一个向量列，然后将数据集拆分为训练集和测试集。
```python
# 准备特征向量
assembler = VectorAssembler(inputCols=data.columns[:-1], outputCol="features")
transformed_data = assembler.transform(data)

# 将数据集拆分为训练集和测试集
train_data, test_data = transformed_data.randomSplit([0.8, 0.2], seed=42)
```

## 模型构建与评估

### 构建并训练模型
我们调用之前定义的 `build_random_forest` 函数在训练集上训练模型。
```python
# 构建随机森林模型
model = build_random_forest(train_data)
```

### 评估并输出结果
我们使用训练好的模型在测试集上进行预测和评估，然后打印结果。
```python
# 评估模型并获取预测结果
accuracy, predictions = evaluate_model(model, test_data)

# 打印准确率
print("Accuracy:", accuracy)

# 打印前 5 行真实标签与预测标签的对比
print_actual_vs_predicted(predictions, n_rows=5)
```

## 总结

本章介绍了使用随机森林算法对鸢尾花数据进行分类。它使用了 `Pandas`、`Scikit-Learn` 和 `PySpark` 库进行数据预处理和模型构建。本章表明，尽管语法可能不同，但 `Scikit-Learn` 和 `PySpark` 在建模步骤上是一致的。与逻辑回归和决策树分类章节类似，本章演示了以模块化格式构建分类模型。代码被组织成函数，以便直观地执行和跟进。

本章还强调了随机森林相对于决策树的优势，特别是在减少过拟合方面。我们观察到，与决策树分类器相比，随机森林确实改进了分类效果。在下一章中，我们将继续讨论分类，并介绍一种新算法：支持向量机。