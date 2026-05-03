# 分层训练测试集划分
from sklearn.model_selection import train_test_split
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.33, random_state=42, stratify=y)
代码块 10-14
分层训练-测试集划分
```

此步骤之后，你将得到四个数据集：X_train 和 y_train 是 X 和 y 中我们将用于训练的部分，而 X_test 和 y_test 将用于评估。

我们现在拥有了开始构建模型的所有要素。我们将要构建的第一个模型是逻辑回归。由于我们没有海量数据，可以排除使用随机森林、xgboost 等复杂模型，尽管如果在这个用例中有更多数据，它们完全可以替代逻辑回归。得益于 scikit-learn 易于使用的建模接口，替换一个模型为另一个模型非常容易，正如你将在本示例的剩余部分看到的那样。

代码块 10-15 中的代码首先初始化了一个逻辑回归模型，然后使用训练数据拟合该模型。



```
