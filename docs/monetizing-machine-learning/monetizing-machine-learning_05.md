# 将数据拆分为训练集和测试集
from sklearn.model_selection import train_test_split
X_train, X_test, y_train, y_test = train_test_split(bike_df_model_ready[features],
bike_df_model_ready[['cnt']], test_size=0.3, random_state=42)
代码清单 2-8
将数据集拆分为训练集和测试集的代码片段
```

“**train_test_split()**”函数将使用一个种子将数据拆分为两个随机数据集。无论何时测试不同的方法，并希望确保始终使用相同的拆分以进行公平比较，设置“**random_state**”种子参数都是一个好主意。“**test_size**”参数设置测试集的大小。这里我们将其设置为 0.3，即 30%，因此它将随机化数据，并将 70% 的数据分配给训练集，30% 分配给测试集（代码清单 2-9）。

```
from sklearn import linear_model
model_lr = linear_model.LinearRegression()
