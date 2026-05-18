# 18. 使用 Scikit-Learn 和 PySpark 在生产环境中部署模型

在本书的最后一章中，我们将探讨使用 Scikit-Learn 和 PySpark 部署机器学习模型的实际操作。模型部署是指让机器学习模型能够在生产环境中使用，从而基于真实世界的数据进行预测或执行任务的过程。它涉及将训练好的机器学习模型集成到系统或应用程序中，以便向最终用户或其他系统提供预测结果。

模型部署是机器学习生命周期中的关键步骤，其重要性体现在多个方面。首先，机器学习模型只有在真实环境中用于预测或自动化任务时才能体现其价值。部署确保模型得到实际应用，从而可以节省成本并提高效率。此外，部署使组织能够扩展其机器学习模型的使用范围，以服务大量用户或处理海量数据。在生产环境中，模型可以实时或批量处理数据，使其适用于各种应用场景。

随后，已部署的模型可以自动化原本需要人工干预或专业知识的决策过程，从而实现更快、更一致的决策。更重要的是，模型可以集成到现有工作流程中，成为现有流程的无缝组成部分。这种集成对于在特定业务环境中利用模型的能力至关重要，例如自动化评估贷款或信用卡申请，以根据用户的信用度和财务历史判断其是否符合资格。

此外，部署允许在生产环境中监控模型的性能，包括跟踪指标、检测模型漂移等问题，并根据需要更新模型以确保其持续有效性。成功部署和利用机器学习模型的组织可以通过做出数据驱动的决策、改善客户体验并在行业中保持领先地位来获得竞争优势。

模型部署之所以重要的另一个关键原因是，已部署的模型可以被包括数据科学家、分析师、开发人员和业务用户在内的各种利益相关者访问，从而促进跨职能协作和知识共享。

本章将演示如何使用 Scikit-Learn 和 PySpark 进行模型部署。但读者应该知道，还有其他在生产环境中部署模型的方法，尽管这些方法不在本章的讨论范围内。一种常见的方法是将算法部署为 API 服务，允许其他应用程序和系统向模型发送 HTTP 请求以获取预测结果。像 Flask、Django 和 FastAPI 这样的框架通常用于构建 API 端点。另一种选择是容器化。Docker 容器用于将机器学习模型及其依赖项打包成可移植的单元。Kubernetes 和其他容器编排工具支持对容器化模型进行扩展和管理。

在复杂的工作流程中，组织使用 Apache Airflow 等工具以协调的方式编排多个模型和数据管道的部署。同样，在面向客户的应用程序中，组织将机器学习模型集成到用户界面中，使最终用户能够与模型的预测或建议进行交互并从中受益。对于离线或批处理，组织将模型部署在 Apache Hadoop 或 Apache Spark 等分布式计算平台上，这些平台可以处理大规模数据处理和模型执行。此外，像 TensorFlow Serving 和 MLflow 这样的专用模型服务框架专为在生产环境中部署和管理机器学习模型而设计，通常包含版本控制、监控和扩展功能。

最后，还有基于云的部署选项，云提供商提供托管的机器学习服务（例如 AWS SageMaker、Azure Machine Learning、Google AI Platform），这些服务简化了在其基础设施上部署模型的过程，通常包括自动扩展和监控功能。

为了演示使用 Scikit-Learn 和 PySpark 进行模型部署，我们将利用一个多层感知器（MLP）并使用鸢尾花数据集。在下一节中，我们将概述模型部署所涉及的关键步骤。随后，我们将深入探讨在 Scikit-Learn 中部署模型的过程。在本章的最后一节，我们将把重点转向 PySpark，使正在考虑从 Scikit-Learn 过渡的数据科学家能够利用这个强大的分布式数据处理框架所提供的优势。

## 模型部署的步骤

在 Scikit-Learn 和 PySpark 中，将模型部署到生产环境通常涉及三个主要步骤：模型训练与评估、模型持久化以及部署。

步骤 1：模型训练与评估

首先，您需要使用提供的算法（例如 Scikit-Learn 中的 `MLPClassifier` 和 PySpark 中用于神经网络的 `MultilayerPerceptronClassifier`）来训练和评估您的模型。这包括使用可用的模块预处理数据，将其拆分为训练集和测试集，并使用准确率等评估指标来评估模型性能。

步骤 2：模型持久化

训练完成后，将训练好的模型保存到磁盘以供将来部署至关重要。在 Scikit-Learn 中，您可以使用 `joblib` 库（或 pickle）来高效地序列化 Python 对象，包括训练好的模型。通过使用 `joblib.dump()` 函数，您可以将模型保存为文件，以便稍后加载用于部署。同样，PySpark 提供了 ML 持久化 API，允许您使用 `save()` 方法保存模型配置和训练好的权重。

步骤 3：模型部署

最后一步涉及使用已保存的模型。在 Scikit-Learn 中，您可以使用 `joblib` 库和 `joblib.load()` 函数将保存的模型文件加载到内存中。这会加载模型，使其准备好对新数据进行预测。同样，在 PySpark 中，可以通过使用 ML 持久化 API 加载已保存的模型来进行部署。然后，您可以通过调用 `transform()` 方法使用加载的模型来转换新数据。

通过遵循这些步骤和技术，您可以有效地使用 Scikit-Learn 和 PySpark 部署您的机器学习模型，确保它们为生产环境做好准备。

## 部署多层感知器（MLP）

在以下各节中，我们将提供使用 Scikit-Learn 和 PySpark 在生产环境中部署模型的实际示例。为了演示，我们将使用鸢尾花数据集训练、评估和部署一个多层感知器（MLP）。我们已经熟悉这个数据集，因为在第 15 章中我们曾用它来训练和评估 k-means 聚类算法。需要注意的是，这里讨论的关于 MLP 分类的原理也适用于其他算法，无论它们是用于回归任务还是分类任务。

### 使用 Scikit-Learn 进行部署

在本小节中，我们将使用 Scikit-Learn 在鸢尾花数据集上构建、训练、评估、持久化并部署一个 MLP 分类器。第一步是导入所需的库。

**步骤 1：导入必要的库**

以下函数和类使我们能够访问并利用 Scikit-Learn 库中的必要功能，以训练、评估、保存和部署 MLP 分类器：

```
[In]: import numpy as np
[In]: from sklearn.datasets import load_iris
[In]: from sklearn.neural_network import MLPClassifier
[In]: from sklearn.model_selection import train_test_split
[In]: from sklearn.metrics import accuracy_score
[In]: import joblib
```

以下是每个库的功能说明：

*   `numpy:` 该库将用于生成一些新预测所需的数据。

*   `load_iris`: 此函数用于加载鸢尾花数据集。

*   `MLPClassifier:` 这是一个用于实现多层感知机（MLP）的类——MLP 是一种用于分类任务的人工神经网络。

*   `train_test_split:` 这是一个实用函数，用于将数据集拆分为训练子集和测试子集，以便评估机器学习模型的性能。

*   `accuracy_score:` 这是一个用于计算准确率指标的函数，通过将分类模型预测结果与测试数据的真实标签进行比较，来评估模型预测的准确性。

*   `joblib:` 该模块提供了保存和加载 Python 对象（包括训练好的机器学习模型）的工具。它常用于模型持久化，使我们能够将训练好的模型保存到磁盘，并在之后加载以进行部署或进一步使用。

**步骤 2：加载鸢尾花数据**

```
[In]: iris = load_iris()
[In]: X = iris.data
[In]: y = iris.target
```

在此步骤中，我们使用 Scikit-Learn 内置的 `load_iris()` 函数加载鸢尾花数据集。特征被赋值给变量 X，而对应的目标值被赋值给变量 y。

**步骤 3：将数据拆分为训练集和测试集**

```
[In]: X_train, X_test, y_train, y_test = train_test_split(X, y,
test_size=0.2, random_state=42)
```

这行代码使用 `train_test_split` 函数将数据拆分为训练集和测试集。X 和 y 分别是我们要拆分的特征集和标签集。`test_size=0.2` 参数指定 20% 的数据将用于测试集，而 80% 将用于训练集。`random_state=42` 参数确保了结果的可复现性。通过使用相同的种子值，每次运行代码时都会得到相同的数据拆分结果。训练集和测试集分别被赋值给变量 X_train、X_test、y_train 和 y_test。

**步骤 4：创建 MLP 分类器**

```
[In]: mlp = MLPClassifier(hidden_layer_sizes=(100, 50), max_iter=1000, random_state=42)
```

这行代码创建了一个 `MLPClassifier` 类的实例。`hidden_layer_sizes` 参数通过定义每个隐藏层中的神经元数量来指定神经网络的架构。在本例中，神经网络有两个隐藏层，第一个隐藏层有 100 个神经元，第二个隐藏层有 50 个神经元。

`max_iter` 参数决定了神经网络训练的最大迭代次数或轮数。在训练过程中，模型会根据训练数据迭代地更新权重以提高性能。在本例中，`max_iter` 参数将最大迭代次数设置为 1000。

最后，`random_state` 参数允许结果的可复现性。通过将 `random_state` 设置为 42，每次运行代码时都会生成相同的随机数序列。这确保了神经网络权重的初始化以及算法中的其他随机过程在不同运行中保持一致。

**步骤 5：训练分类器**

```
[In]: mlp.fit(X_train, y_train)
```

这行代码用于在训练数据上训练 MLP 分类器。`fit()` 方法使用提供的输入特征（X_train）和相应的目标标签（y_train）来训练模型。

在训练过程中，MLP 分类器学习输入特征与目标标签之间的关系。它根据训练数据迭代地调整神经网络的权重和偏置，旨在最小化误差或损失函数。迭代次数由我们在创建 `MLPClassifier` 对象时指定的 `max_iter` 参数决定。执行 `fit()` 方法后，MLP 分类器即训练完成，并准备好对新的、未见过的数据进行预测。

**步骤 6：对测试数据进行预测**

```
[In]: y_pred = mlp.predict(X_test)
```

这行代码用于使用训练好的 MLP 分类器模型对测试数据进行预测。`predict()` 方法将训练好的模型应用于输入数据，并返回预测的类别标签。对于测试数据中的每个样本，它根据从训练数据中学到的模式和关联来预测相应的目标标签。得到的预测标签存储在 y_pred 变量中。

**步骤 7：评估准确率**

```
[In]: accuracy = accuracy_score(y_test, y_pred)
[In]: print("Accuracy:", accuracy)
[Out]: Accuracy: 1.0
```

这段代码用于评估 MLP 分类器在测试数据上预测的准确率。`accuracy_score()` 是一个实用函数，通过将预测标签（y_pred）与真实标签（y_test）进行比较，来计算分类模型预测的准确率。通过调用 `accuracy_score(y_test, y_pred)`，该函数将每个预测标签与其对应的真实标签进行比较，从而计算出预测的准确率。它返回一个介于 0 和 1 之间的值，表示正确预测标签的比例。

输出结果显示 MLP 分类器的准确率得分为 1.0，这意味着所有预测中 100% 是正确的。这表明模型已准确地对鸢尾花的每个品种进行了分类。

**步骤 8：保存训练好的模型以供部署**

在训练并评估了模型性能之后，我们现在应该能够进入持久化步骤：

```
[In]: joblib.dump(mlp, 'mlp_model.pkl')
```

这行代码使用 `joblib` 库，通过 `dump()` 函数将机器学习模型保存到名为 `mlp_model.pkl` 的文件中。`mlp` 参数代表机器学习模型对象。`mlp_model.pkl` 参数是序列化模型将要保存到的文件名。`.pkl` 扩展名通常用于存储使用 pickle 协议序列化的 Python 对象的文件。

**步骤 9：加载保存的模型**

此步骤涉及使用保存的模型。我们可以使用 `joblib` 库和 `joblib.load()` 函数将保存的模型文件加载到内存中。这将加载模型，使其准备好对新数据进行预测：

```
[In]: mlp = joblib.load('mlp_model.pkl')
```

这行代码使用 `joblib` 库中的 `load()` 函数从 `mlp_model.pkl` 文件中加载序列化的 MLP 模型，并将其赋值给变量 mlp。执行这行代码后，mlp 将包含反序列化后的模型对象，该对象可用于预测。

**步骤 10：生成新数据（模拟示例）**

```
[In]: np.random.seed(42)
[In]: X_new = np.random.rand(10, 4)
```

这段代码使用 NumPy 的随机模块生成新数据。其目的是使用这些新数据来演示如何使用加载的模型。第一行代码为随机数生成器设置了随机种子。将种子设置为 42 可确保生成的随机数具有可复现性。

第二行创建了一个包含随机数的 NumPy 数组（X_new）。`rand()` 函数从 0 到 1 之间的均匀分布中生成随机数。参数 (10, 4) 指定了要创建的数组形状，其中 10 代表行数，4 代表列数。

步骤 11：对新数据进行预测

```
[In]: y_new_pred = mlp.predict(X_new)
[In]: print("Predictions:", y_new_pred)
[Ou]: Predictions: [1 1 1 0 1 1 0 1 1 1]
```

在最后一步中，我们首先使用 mlp 模型的 `predict()` 方法对新数据集 X_new 进行预测。`predict()` 方法接收输入数据，并基于训练好的模型返回预测值。预测结果存储在变量 y_new_pred 中。然后，我们使用 `print()` 函数打印模型生成的预测结果。

### PySpark

我们现在将焦点转向 PySpark，以展示如何使用 Spark ML 训练和评估一个 MLP 分类器，持久化模型，然后加载模型对新数据进行预测。

第一步是导入必要的库：

步骤 1：导入必要的库

```
[In]: from pyspark.sql import SparkSession
[In]: from pyspark.ml.classification import
MultilayerPerceptronClassificationModel,
MultilayerPerceptronClassifier
[In]: from pyspark.ml.feature import VectorAssembler
[In]: from pyspark.ml.evaluation import
MulticlassClassificationEvaluator
[In]: import numpy as np
```

这段代码导入了在 PySpark 中构建、训练、评估和部署 MLP 分类模型所需的类。以下是每个导入类的描述：

*   `SparkSession:` 这是 Spark 功能的入口点；它支持与 Spark API 的交互。

*   `MultilayerPerceptronClassifier:` 该类提供了 Spark 中 MLP 分类器的实现。

*   `MultilayerPerceptronClassificationModel:` 该类代表一个训练好的 MLP 分类模型。它包含神经网络学习到的权重和偏置，以及其他与模型相关的信息。

*   `VectorAssembler:` 该类用于将多个特征列合并成一个单一的向量列。

*   `MulticlassClassificationEvaluator:` 该类为多类分类模型提供评估指标，由于鸢尾花数据集包含三个品种（setosa、versicolor 和 virginica），使其成为一个多类数据集，因此该评估器在此场景下特别相关。

*   `numpy as np`: 该库将用于生成一些用于新预测的随机输入数据。

步骤 2：创建一个 SparkSession

```
[In]: spark = SparkSession.builder.getOrCreate()
```

这段代码创建了一个 Spark Session，以便与 Spark 进行交互。

步骤 3：创建 PySpark DataFrame

```
[In]: spark = SparkSession.builder.getOrCreate()
[In]: data = [(float(X[i, 0]), float(X[i, 1]), float(X[i, 2]), float(X[i, 3]), float(y[i])) for i in range(len(X))]
[In]: spark_df = spark.createDataFrame(data, ["sepal_length", "sepal_width", "petal_length", "petal_width", "species"])
```

在这一步中，我们首先初始化一个 SparkSession，它是利用 Spark 功能的入口点。然后，我们通过创建一个列表来准备数据，列表中的每个元素对应一朵鸢尾花的特征，包括花萼长度、花萼宽度、花瓣长度、花瓣宽度及其品种标签。这些数据被转换成适合 PySpark 的格式。最后，使用 `createDataFrame()` 方法，我们创建了一个名为 spark_df 的 PySpark DataFrame。

步骤 4：将数据拆分为训练集和测试集

```
[In]: train_data, test_data = spark_df.randomSplit([0.8, 0.2],
seed=42)
```

这行代码根据指定的拆分比例，将 Spark DataFrame (spark_df) 拆分为两个独立的 DataFrame，即 train_data 和 test_data。Spark DataFrame 提供的 `randomSplit()` 方法随机执行此拆分。它接受两个参数：

1.  [0.8, 0.2] 参数是一个列表，分别指定训练数据和测试数据的拆分比例。在这种情况下，80% 的数据将被分配给 train_data，20% 的数据将被分配给 test_data。

2.  seed=42 参数设置了随机种子。它确保了随机拆分过程的可重复性。当使用相同的种子时，如果多次应用随机拆分，将产生相同的训练-测试集划分。

步骤 5：将特征组装成一个单一的向量列

```
[In]: feature_cols = ["sepal_length", "sepal_width", "petal_length", "petal_width"]
[In]: assembler = VectorAssembler(inputCols=feature_cols, outputCol="features")
[In]: train_data = assembler.transform(train_data)
[In]: test_data = assembler.transform(test_data)
```

在此步骤中，使用 `VectorAssembler` 将特征组合成一个向量。第一行中，`feature_cols` 被定义为一个列表，包含我们想要纳入机器学习模型的特征列名称。这些列代表了鸢尾花的特征：花萼长度、花萼宽度、花瓣长度和花瓣宽度。

第二行创建了一个名为 `assembler` 的 `VectorAssembler` 对象。`VectorAssembler` 是一个 PySpark 特征转换器，用于将来自多个列的特征值向量组装成一个单一的向量列。在此例中，它被配置为将 `feature_cols` 中指定的列作为输入列，并生成一个名为 `features` 的新列，该列包含所选特征的向量表示。

最后两个命令使用 `VectorAssembler` 的 `transform` 方法将转换应用于训练数据和测试数据。此转换从 DataFrame（`train_data` 和 `test_data`）的每一行中提取指定的特征列，并将它们组合成一个名为 `features` 的单一向量列。此转换对于 PySpark 中的机器学习算法至关重要，因为它们期望输入特征采用向量格式。

步骤 6：创建 MLP 分类器

```
[In]: mlp = MultilayerPerceptronClassifier(labelCol="species", featuresCol="features", layers=[4, 100, 50, 3], seed=42, maxIter=500)
```

在这行代码中，使用 `MultilayerPerceptronClassifier` 类创建了多层感知机（MLP）分类模型。这个神经网络由多个相互连接的节点（神经元）层组成。

`labelCol="species"` 参数指定了 DataFrame 中包含分类标签或目标变量的列名。在此例中，列名为 `species`。`featuresCol="features"` 参数指定了 DataFrame 中包含分类器输入特征的列名。在此例中，列名为 `features`。

`layers=[4, 100, 50, 3]` 参数定义了多层感知机网络的架构。该列表指定了从输入层到输出层每一层的节点数。在此示例中，MLP 有 1 个包含 4 个节点（代表特征）的输入层，2 个分别包含 100 和 50 个节点的隐藏层，以及 1 个包含 3 个节点（代表鸢尾花种类）的输出层。

`seed=42` 参数为 MLP 分类器设置了随机种子。它通过将随机数生成器初始化为特定状态来确保结果的可复现性。

`maxIter=500` 参数决定了 MLP 分类器的最大迭代次数或训练轮数。它控制算法遍历训练数据以更新神经网络权重和偏置的次数。

步骤 7：训练分类器

```
[In]: mlp_model = mlp.fit(train_data)
```

在这单行代码中，MLP 分类器（`mlp`）正在训练数据（`train_data`）上进行训练。调用 `fit` 方法来启动训练过程并创建一个训练好的模型。`mlp` 是之前定义的多层感知机分类器实例。它封装了 MLP 网络的架构和设置。`fit(train_data)` 是启动训练过程的方法调用。它接收 `train_data` DataFrame 作为输入，其中包含带有相应特征和标签的带标签训练样本。`fit` 方法执行一个迭代优化过程，在此过程中，它使用训练样本更新 MLP 网络的权重和偏置。迭代次数由创建 MLP 分类器时指定的 `maxIter` 参数决定。

训练过程完成后，`fit` 方法返回一个训练好的 MLP 模型（`mlp_model`）。该模型代表了在训练数据中学到的模式和关系，可用于对新的、未见过的数据进行预测。

步骤 8：对测试数据进行预测

```
[In]: predictions = mlp_model.transform(test_data)
```

在这单行代码中，训练好的多层感知机模型（`mlp_model`）被用于对测试数据（`test_data`）进行预测。在 `mlp_model` 上调用 `transform` 方法，将训练好的模型应用于测试数据并生成预测。

`predictions` DataFrame 现在包含了多层感知机模型对测试数据做出的预测。我们可以使用这个 DataFrame 来评估模型的性能。

步骤 9：评估准确率

```
[In]: evaluator = MulticlassClassificationEvaluator(labelCol="species", predictionCol="prediction", metricName="accuracy")
[In]: accuracy = evaluator.evaluate(predictions)
[In]: print("Accuracy:", accuracy)
[Out]: Accuracy: 1.0
```

在这段代码中，正在评估多层感知机模型对测试数据所做预测的准确率。使用 `MulticlassClassificationEvaluator` 来计算准确率指标，并打印结果。`evaluator` 是 `MulticlassClassificationEvaluator` 类的一个实例。它通过以下三个参数创建：

1.  `labelCol="species"` 参数指定了 DataFrame 中包含测试数据实际标签的列名。
2.  `predictionCol="prediction"` 参数指定了 DataFrame 中包含模型生成的预测标签的列名。
3.  `metricName="accuracy"` 参数指定了要计算的指标，在此例中为准确率。

在第二行代码中，`accuracy` 是一个变量，用于保存对预测结果评估准确率指标的结果。在 `evaluator` 实例上调用 `evaluate` 方法，并传入 `predictions` DataFrame。`evaluate` 方法将 `predictions` DataFrame 的 `prediction` 列中的预测标签与 `species` 列中的实际标签进行比较。它计算准确率，即测试数据中正确预测的标签数量占总实例数的比例。

最后，使用 `print` 语句打印准确率值。该值为 1.0 或 100%，表明模型对每种鸢尾花都做出了准确的预测。这意味着模型的预测与真实标签完全匹配，分类准确率达到 100%，这有力地表明了模型在测试数据上的性能。

步骤 10：保存训练好的模型以供部署

```
[In]: model_dir = "/dbfs/mlp_model"
[In]: mlp_model.write().overwrite().save(model_dir)
```

在这段代码中，训练好的多层感知机模型（`mlp_model`）被保存以供部署。在 `mlp_model` 上调用 `write()` 方法，随后调用 `overwrite()` 方法和 `save()` 方法。

第一行定义了训练好的模型将被保存的目录路径（`model_dir`）。在此示例中，使用 Databricks 将目录路径设置为 “/dbfs/mlp_model”。

在第二行中，在训练好的多层感知机模型（`mlp_model`）上调用 `write()` 方法来启动保存过程。`overwrite()` 方法确保如果指定目录路径中已存在模型，它将被正在保存的新模型覆盖。此步骤保证保存的模型是最新版本。

`save()` 方法将训练好的模型保存到指定的目录路径（`model_dir`）。训练好的模型被序列化并存储在指定位置以供将来使用。

步骤 11：加载保存的模型

```
[In]: loaded_model = MultilayerPerceptronClassificationModel.load(model_dir)
```

在这段代码中，加载一个保存好的多层感知机模型以供进一步使用。在 `MultilayerPerceptronClassificationModel` 类上调用 `load()` 方法，并将加载的模型赋值给变量 `loaded_model`。

该类表示 Apache Spark 的 MLlib 中训练好的多层感知器模型。通过调用该类的 `load()` 方法，可从指定目录路径（model_dir）加载模型。该方法会读取并反序列化已保存的模型文件，并构造一个新的 `MultilayerPerceptronClassificationModel` 类实例。

变量 model_dir 包含之前保存训练模型时的目录路径。变量 loaded_model 被赋值为加载后的多层感知器模型，现在可用于对新数据进行预测。

**步骤 12：生成新的（模拟）数据**

```
[In]: np.random.seed(42)
[In]: X_new = np.random.rand(10, 4)
```

要使用加载的多层感知器模型对新数据进行预测，我们首先需要生成这些数据。这通过前面展示的代码片段实现，该代码演示了如何使用 Python 的 NumPy 库生成随机数并创建一个包含随机特征的新数组。

代码的第一行设置了 NumPy 随机数生成器的随机种子。设置种子可以确保每次运行代码时生成的随机数相同。第二行生成一个名为 X_new 的新 NumPy 数组，其维度为 10`×`4。`rand` 函数用于生成 0 到 1 之间的随机数。数组中的每个元素代表一个随机特征值。第一个参数 10 表示数组中的行数或样本数。第二个参数 4 表示数组中的列数或特征数。因此，X_new 是一个包含随机特征值的 10`×`4 数组。

**步骤 13：将 NumPy 数组转换为 Spark DataFrame**

```
[In]: new_data = spark.createDataFrame(X_new.tolist(), ["sepal_length", "sepal_width", "petal_length", "petal_width"])
```

这一行代码演示了如何使用 Spark 的 `createDataFrame` 方法从 NumPy 数组创建 Spark DataFrame。更准确地说，该代码从 NumPy 数组 X_new 创建了一个名为 new_data 的新 Spark DataFrame。`createDataFrame` 方法在 spark 对象上调用，该对象是使用 Spark 功能的入口点。

`tolist()` 方法将 NumPy 数组 X_new 转换为嵌套的 Python 列表。这种转换是必要的，因为 `createDataFrame` 期望输入与 Python 数据结构兼容。`["sepal_length", "sepal_width", "petal_length", "petal_width"]` 提供了 DataFrame 的列名。它指定了 new_data DataFrame 中的列名，对应于 X_new 数组中的四个特征。生成的 DataFrame new_data 将包含四列：sepal_length、sepal_width、petal_length 和 petal_width，其值来自 NumPy 数组中对应的元素。变量 new_data 保存了新创建的 Spark DataFrame。

**步骤 14：为新数据将特征组装成单个向量列**

```
[In]: new_data = assembler.transform(new_data)
```

在这段代码中，在 assembler 对象上调用了 `transform()` 方法，以对 new_data DataFrame 应用特征转换。该方法将 new_data 作为输入，并对其列应用指定的转换操作。生成的 DataFrame new_data 将增加一个名为 features 的列，其中包含组装好的特征向量。

**步骤 15：对新数据进行预测**

```
[In]: new_predictions = loaded_model.transform(new_data)
[In]: new_predictions.select("prediction").show()
[Out]:
+----------+
|prediction|
+----------+
|       1.0|
|       1.0|
|       1.0|
|       0.0|
|       0.0|
|       1.0|
|       0.0|
|       1.0|
|       1.0|
|       1.0|
+----------+
```

在步骤 15 中，使用加载的多层感知器模型（loaded_model）对新数据集进行预测。然后显示预测结果。更多细节如下文所述。

第一行代码将训练好的多层感知器模型（loaded_model）应用于 new_data_scaled DataFrame。在 loaded_model 对象上调用了 `transform()` 方法，并将 new_data 作为参数传入。该方法将模型应用于输入的 DataFrame，根据学习到的模型权重和架构进行预测。生成的 DataFrame new_predictions 将包含 new_data 中的原始列，以及一个名为 prediction 的新列，该列保存预测的标签或值。

第二行从 new_predictions DataFrame 中选择 prediction 列并显示其内容。在 new_predictions 上调用了 `select()` 方法，并将列名 prediction 作为参数。然后调用 `show()` 方法来显示选定的列。

## 整合所有步骤

在本节中，我们将前面步骤中的所有相关代码合并到一个代码块中。这将使读者能够在 Scikit-Learn 和 PySpark 中作为一个连贯的整体来执行代码。

### Scikit-Learn

```

# 导入必要的库
[In]: import numpy as np
[In]: from sklearn.datasets import load_iris
[In]: from sklearn.neural_network import MLPClassifier
[In]: from sklearn.model_selection import train_test_split
[In]: from sklearn.metrics import accuracy_score
[In]: import joblib

# 加载鸢尾花数据集
[In]: iris = load_iris()
[In]: X = iris.data  # 特征
[In]: y = iris.target  # 目标标签

# 将数据拆分为训练集和测试集
[In]: X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)

# 创建 MLP 分类器
[In]: mlp = MLPClassifier(hidden_layer_sizes=(100, 50), max_iter=1000, random_state=42)

# 训练分类器
[In]: mlp.fit(X_train, y_train)

# 对测试数据进行预测
[In]: y_pred = mlp.predict(X_test)

# 评估准确率
[In]: accuracy = accuracy_score(y_test, y_pred)
[In]: print("Accuracy:", accuracy)

# 保存训练好的模型以便部署
[In]: joblib.dump(mlp, 'mlp_model.pkl')

# 加载保存的模型
[In]: mlp = joblib.load('mlp_model.pkl')

# 生成新的（模拟）数据
[In]: np.random.seed(42)
[In]: X_new = np.random.rand(10, 4)  # 新特征（鸢尾花数据集有 4 个特征）

# 对新数据进行预测
[In]: y_new_pred = mlp.predict(X_new)
[In]: print("Predictions:", y_new_pred)
```

### PySpark

```
[In]: from sklearn.datasets import load_iris
[In]: from pyspark.sql import SparkSession
[In]: from pyspark.ml.classification import MultilayerPerceptronClassificationModel, MultilayerPerceptronClassifier
[In]: from pyspark.ml.feature import VectorAssembler
[In]: from pyspark.ml.evaluation import MulticlassClassificationEvaluator
[In]: import numpy as np

# 创建一个 SparkSession
[In]: spark = SparkSession.builder.getOrCreate()

# 加载鸢尾花数据集
[In]: iris = load_iris()
[In]: X = iris.data
[In]: y = iris.target

# 从鸢尾花数据集创建 PySpark DataFrame
[In]: data = [(float(X[i, 0]), float(X[i, 1]), float(X[i, 2]), float(X[i, 3]), float(y[i])) for i in range(len(X))]
[In]: spark_df = spark.createDataFrame(data, ["sepal_length", "sepal_width", "petal_length", "petal_width", "species"])

# 将数据拆分为训练集和测试集
[In]: train_data, test_data = spark_df.randomSplit([0.8, 0.2], seed=42)

# 将特征组装成单个向量列
[In]: feature_cols = ["sepal_length", "sepal_width", "petal_length", "petal_width"]
[In]: assembler = VectorAssembler(inputCols=feature_cols, outputCol="features")
[In]: train_data = assembler.transform(train_data)
[In]: test_data = assembler.transform(test_data)

# 创建 MLP 分类器
[In]: mlp = MultilayerPerceptronClassifier(labelCol="species", featuresCol="features", layers=[4, 100, 50, 3], seed=42, maxIter=500)

# 训练分类器
[In]: mlp_model = mlp.fit(train_data)

# 对测试数据进行预测
[In]: predictions = mlp_model.transform(test_data)

# 评估准确率
[In]: evaluator = MulticlassClassificationEvaluator(labelCol="species", predictionCol="prediction", metricName="accuracy")
[In]: accuracy = evaluator.evaluate(predictions)
[In]: print("Accuracy:", accuracy)

# 保存训练好的模型以便部署
[In]: model_dir = "/dbfs/mlp_model"
[In]: mlp_model.write().overwrite().save(model_dir)

# 加载已保存的模型
[In]: loaded_model = MultilayerPerceptronClassificationModel.load(model_dir)

# 生成新数据（模拟示例）
[In]: np.random.seed(42)
[In]: X_new = np.random.rand(10, 4)  # 新特征（鸢尾花数据集有 4 个特征）

# 为新数据创建 PySpark DataFrame
[In]: new_data = spark.createDataFrame(X_new.tolist(), ["sepal_length", "sepal_width", "petal_length", "petal_width"])

# 将特征组装成单个向量
[In]: new_data = assembler.transform(new_data)

# 对新数据进行预测
[In]: new_predictions = loaded_model.transform(new_data)

# 显示预测结果
[In]: new_predictions.select("prediction").show()
```

## 总结

在本书的最后一章中，我们探讨了使用 Scikit-Learn 和 PySpark 部署机器学习算法的实际应用。我们强调了模型部署在机器学习生命周期中的关键作用。我们得出结论：能够成功部署并利用机器学习模型的组织可以获得竞争优势。这种优势来源于做出数据驱动的决策、改善客户体验以及在行业中保持领先地位。

我们概述了模型部署所涉及的关键步骤（训练与评估、持久化和部署），并使用鸢尾花数据集将其应用于一个多层感知器（MLP）分类模型。虽然本章主要聚焦于 Scikit-Learn 和 PySpark，但我们也提供了其他部署方法的相关信息。例如，在复杂工作流中，组织会使用 Apache Airflow 以协调的方式编排多个模型和数据管道的部署。

