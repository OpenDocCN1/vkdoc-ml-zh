# 在训练集上训练模型
model_lr.fit(X_train, y_train)
代码清单 2-9
线性回归代码
```

我们声明一个“**LinearRegression()**”模型，然后调用“**fit()**”函数，使用训练数据和训练标签来训练模型。模型“**model_lr**”现在已训练完毕，可以用于预测（代码清单 2-10）。

```
输入：
predictions = model_lr.predict(X_test)
from sklearn.metrics import mean_squared_error
print("均方根误差: %.2f" % sqrt(mean_squared_error(y_test, predictions)))
输出：
均方根误差: 143.08
代码清单 2-10
预测并获取 RMSE 分数
```

最后，我们使用预留用于测试的 30% 的数据调用“**predict()**”函数，并将预测的标签输入“**mean_squared_error()**”函数，以获取均方根误差分数。我们得到的 RMSE 为 **143.08**，并将其作为我们的基础基准分数。这是我们在当前种子拆分（我们在 train_test_split 函数上应用的种子，以确保每次都能得到相同的拆分）以及到目前为止选择的所有特征下得到的结果。解释该分数的一种方式是，由于它与我们的结果变量处于同一量级，我们的模型预测偏差了 143 辆自行车。考虑到每小时平均自行车租赁需求约为 190 辆，我们的模型比简单地选取自行车租赁的总体平均值表现更好。但让我们看看是否能够改进这一点。

## 特征工程实验

让我们尝试几种不同的技术，包括多项式、非线性建模和利用时间序列，看看是否能获得更好的分数。



### 使用多项式进行建模

对一系列数值应用多项式变换，可以在线性回归中实现更好的分离效果。在 Python 的“**sklearn**”库中，这非常容易实现（代码清单 2-11）。

```
from sklearn.preprocessing import PolynomialFeatures
poly = PolynomialFeatures(2)
X_train = poly.fit_transform(X_train)
X_test = poly.fit_transform(X_test)
代码清单 2-11
创建多项式特征
```

现在，我们将所有特征转换为二次多项式（代码清单 2-12）。

```
输入：
print("将 PolynomialFeatures 设置为 2 次时的均方根误差: %.2f" % sqrt(mean_squared_error(y_test, predictions)))
输出：
将 PolynomialFeatures 设置为 2 次时的均方根误差: 122.96
代码清单 2-12
二次多项式
```

现在，将所有特征转换为三次多项式（代码清单 2-13）。

```
输入：
print("将 PolynomialFeatures 设置为 3 次时的均方根误差: %.2f" % sqrt(mean_squared_error(y_test, predictions)))
输出：
将 PolynomialFeatures 设置为 3 次时的均方根误差: 111.65
代码清单 2-13
三次多项式
```

现在，将所有特征转换为四次多项式（代码清单 2-14）。

```
输入：
print("将 PolynomialFeatures 设置为 4 次时的均方根误差: %.2f" % sqrt(mean_squared_error(y_test, predictions)))
输出：
将 PolynomialFeatures 设置为 4 次时的均方根误差: 114.84
代码清单 2-14
四次多项式
```

如您所见，使用 sklearn 的“**PolynomialFeatures()**”函数对数据集应用多项式变换极其简单。使用二次和三次多项式时，评分确实有所提升，但超过这个次数后评分会下降。

### 从分类数据创建虚拟特征

另一种值得尝试的方法是对分类数据进行虚拟化处理。这意味着为每个类别创建单独的列。以特征“**weathersit**”为例：它不是一个连续变量，而是一个任意类别。如果直接将其输入模型，模型会将其视为线性数值数据，但这在这种情况下并不合理；将“**雾与云**”加 1 并不等于“**雪**”。通过创建四个新列：“**晴**”、“**雾**”、“**雪**”和“**雨**”，并为每个列分配一个二进制的真/假值，模型在处理“**weathersit**”时效果会更好。

使用 Pandas 的“**get_dummies()**”函数可以轻松实现这一点。我们将代码抽象成一个函数，以便更容易地创建我们的 Web 应用程序（代码清单 2-15）。

```
def prepare_data_for_model(raw_dataframe,
target_columns,
drop_first = False,
make_na_col = True):
