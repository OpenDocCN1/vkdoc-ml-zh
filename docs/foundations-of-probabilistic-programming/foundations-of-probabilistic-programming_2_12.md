## 11 机器学习库

在实验结束后才请来统计学家，可能无异于让他进行尸检：他或许能说出实验的死因。

——罗纳德·费希尔

本章内容

*   流行机器学习库概览
*   Scikit-learn 简介
*   机器学习流程概览

*机器学习* 包括让计算机利用数据找到解决问题的方法。相比之下，在传统编程中，程序员在代码中定义找到解决方案的方法，而不是解决方案本身。本章简要概述了一些用于机器学习的流行库。这些库实现了用于创建和训练机器学习模型的算法。这些模型根据问题的类型有不同的用途。例如，一些模型用于预测未来值，另一些模型用于将数据分类到组或类别中。

### 流行的机器学习库

四个最流行的机器学习库是 TensorFlow、Keras、PyTorch 和 Scikit-learn：

*   **TensorFlow：** 谷歌开发了这个强大的库供内部使用。它用于使用深度学习解决问题。这涉及到定义转换数据的层，并在解决方案拟合数据时对这些层进行调整。
*   **Keras：** 这个开源库旨在与 TensorFlow 配合使用，现在已包含在 TensorFlow 库中（参见 [`www.tensorflow.org/guide`](https://www.tensorflow.org/guide)）。
*   **PyTorch：** 这是 Facebook 对达到生产水平的机器学习库的贡献。它基于 Torch 库，该库利用 GPU 解决深度学习问题（参见 [`pytorch.org/docs/stable/index.html`](https://pytorch.org/docs/stable/index.html)）。
*   **Scikit-learn：** 这个用于入门机器学习的流行库构建在 NumPy 和 SciPy 之上。它包含了大多数传统算法的类。你将在下一节中了解更多关于 Scikit-learn 的信息。

### 机器学习的工作原理

机器学习算法可以分为两种类型：无监督学习和监督学习。无监督学习涉及在没有预先存在的结果进行测试的情况下发现关于数据的见解。这通常意味着根据数据的特征识别模式，而无需数据科学家的任何输入。监督学习涉及使用已知数据来训练和测试模型。通常，训练监督模型的步骤如下：

1.   转换数据。
2.   分离出测试数据。
3.   训练模型。
4.   测试准确性。

Scikit-learn 提供了简化这些步骤中每一步的工具，如下节所述。



#### 变换

对于某些算法，在训练模型之前对数据进行变换是有利的。例如，你可能希望将连续变量（如年龄）转换为离散类别（如年龄段）。Scikit-learn 包含多种类型的变换器，包括用于清洗、特征提取、降维和扩维的变换器。这些变换器以类的形式呈现，通常使用 `.fit()` 方法来确定变换，并使用 `.transform()` 方法通过该变换来修改数据。清单 11.1 使用了 `MinMaxScaler` 变换器，它可以将数值缩放到一个定义的范围内——默认情况下是 0 到 1 之间。

清单 11.1 **使用 `MinMaxScaler` 进行变换**

点击此处查看代码图片

```
import numpy as np

from sklearn.preprocessing import MinMaxScaler

data = np.array([[100, 34, 4], 

                  90,  2,  0],

                  78, -12, 16],

                  23,  45, 4]]) # 数据范围从 -12 到 100 的数组

data

array([[100,  34,   4],

      [ 90,   2,   0],

      [ 78, -12,  16],

      [ 23,  45,   4]])

minMax = MinMaxScaler()          # 创建一个变换器对象 

scaler = minMax.fit(data)        # 将变换器拟合到数据上 

scaler.transform(data)           # 缩放到 0 到 1 之间的范围

array([[1\.       ,  0.80701754,  0.25      ],

      [0.87012987,  0.24561404,  0\.        ],

      [0.71428571,  0\.        ,  1\.        ],

      [0\.        ,  1\.        ,  0.25      ]])
```

有时你可能希望在拟合变换器之前分离数据。这样做时，变换器的设置将不会受到测试数据的影响。拟合和变换需要分开的方法；很容易对训练数据进行拟合，并使用该拟合结果来变换测试数据。

#### 拆分测试数据和训练数据

训练模型时需要避免的一个重要陷阱是过拟合，当模型能完美预测用于训练它的数据，但对新数据的预测能力很差时，就会发生这种情况。从最简单的意义上讲，避免过拟合的方法是不使用训练模型的数据来测试模型。Scikit-learn 提供了辅助方法来简化数据拆分。

在查看数据拆分的示例之前，你可以加载一个简单的示例。与其他许多数据科学库一样，Scikit-learn 自带了一些样本数据集。清单 11.2 加载了 `iris` 数据集。请注意，`.load_iris()` 函数加载了两个 NumPy 数组数据：第一个是源数据（将用于进行预测的特征），第二个是要预测的目标特征。对于 `iris` 数据集，源数据有 150 个样本，每个样本包含 4 个特征，以及 150 个代表鸢尾花类型的目标值。

清单 11.2 **加载样本数据集**

点击此处查看代码图片

```
from sklearn import datasets # 加载样本数据集 

source, target = datasets.load_iris(return_X_y=True) # 加载源数据和目标数据 

print(type(source))

<class 'numpy.ndarray'>

print(source.shape)

(150, 4)

print(type(target))

<class 'numpy.ndarray'>

print(target.shape)

 (150,)
```

清单 11.3 使用 Scikit-learn 函数 `train_test_split()` 将库自带的 `iris` 数据集拆分为训练数据集和测试数据集。你可以看到样本被拆分，其中 112 个在训练集中，38 个在测试集中。

清单 11.3 **拆分数据集**

点击此处查看代码图片

```
from sklearn.model_selection import train_test_split

train_s, test_s, train_t, test_t = train_test_split(source, target)

train_s.shape

(112, 4)

train_t.shape

(112,)

test_s.shape

(38, 4)

test_t.shape

(38,)
```

#### 训练与测试

Scikit-learn 提供了许多代表各种机器学习算法的类。这些类被称为*估计器*。许多估计器可以在实例化时通过参数进行调整。每个估计器都有一个 `.fit()` 方法，用于训练模型。大多数 `.fit()` 方法接受两个参数。第一个是某种训练数据，称为*样本*。第二个是这些样本的结果或目标。这两个参数都应该是类似数组的对象，例如 NumPy 数组。训练完成后，模型可以使用其 `.predict()` 方法来预测结果。可以使用该方法所在模块中的函数来检查此预测的准确性。

清单 11.4 展示了一个使用 `KNeighborsClassifier` 估计器的简单示例。K-近邻是一种基于特征间距离对样本进行分组的算法。它通过将新样本与距离其最近的现有样本进行比较来进行预测。你可以通过选择与新样本进行比较的邻居数量来调整算法。模型训练完成后，你可以使用测试数据进行预测，并检查这些预测的准确性。

清单 11.4 **训练模型**

点击此处查看代码图片

```
from sklearn.neighbors import KNeighborsClassifier   # 导入估计器类 

from sklearn import metrics   # 导入 metrics 模块以测试准确性 

knn = KNeighborsClassifier(n_neighbors=3)   # 创建 3-邻居估计器

knn.fit(train_s, train_t)   # 使用训练数据训练模型

test_prediction = knn.predict(test_s)   # 从源数据进行预测

metrics.accuracy_score(test_t, test_prediction)   # 与测试数据对比的准确性 

0.8947368421052632
```

### 了解更多关于 Scikit-learn 的信息

本章仅触及了 Scikit-learn 功能的皮毛。其他重要特性包括用于交叉验证的工具（交叉验证会将数据集多次拆分以避免在测试数据上过拟合），以及管道（管道将变换器、估计器和交叉验证整合在一起）。如果你想了解更多关于 Scikit-learn 的信息，可以在 [`scikit-learn.org/stable/`](https://scikit-learn.org/stable/) 找到很棒的教程。

### 总结

用于创建机器学习模型的许多算法都在主要的 Python 机器学习库中有所体现。TensorFlow 是由 Google 创建的深度学习库。PyTorch 是由 Facebook 在 Torch 基础上构建的库。Scikit-learn 是一个用于入门机器学习的流行库。它拥有执行创建和分析模型所涉及步骤的模块和函数。

### 问题

1.   在训练监督估计器的哪个步骤中，Scikit-learn 变换器会很有用？

2.   为什么在机器学习中分离训练数据和测试数据很重要？

3.   在变换数据并训练模型之后，下一步应该做什么？

