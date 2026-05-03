# 通过训练测试拆分来更好地评估此模型
from sklearn.model_selection import train_test_split
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.33, random_state=42)
代码块 11-14
将数据拆分为训练集和测试集
```

现在让我们再次拟合模型，但这次仅使用训练数据。这在代码块 11-15 中完成。

```
lin_reg_2 = LinearRegression()
lin_reg_2.fit(X_train, y_train)
代码块 11-15
在训练数据上拟合模型
```

为了评估性能，我们使用估算出的模型（本例中为系数和线性模型公式）来预测测试集上的价格估算值。这在代码块 11-16 中完成。

```
pred_reg_2 = lin_reg_2.predict(X_test)
代码块 11-16
在测试集上进行预测
```

我们可以将这些预测值与测试集中已知的真实价格一起使用，来计算 R²分数。这在代码块 11-17 中完成。

```
from sklearn.metrics import r2_score
r2_score(y_test, pred_reg_2)
代码块 11-17
计算 R²分数
```

得到的 R²分数为 0.1007。虽然结果不算出色，但该分数表明模型具有一定的预测价值，并且比使用均值定价能提供更好的细分。

### 数据探索的迭代 2

在第二次迭代中，我们将把地理数据加入到探索和模型中。毕竟，在预订 Airbnb 时位置很重要，这很可能体现在价格上。让我们首先更详细地查看数据集中的这些特征。代码块 11-18 中的代码通过从几何列中提取数据，专门创建了纬度和经度变量。

```
