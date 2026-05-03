# 评估模型
pred_reg_3 = lin_reg_3.predict(X2_test)
print(r2_score(y_test, pred_reg_3))
代码块 11-23
模型迭代 2
```

该模型获得的 R²分数为 0.498。与第一次迭代（R²为 0.10）相比，这实际上是一个相当大的改进。这让我们有信心在下一节中继续测试非线性模型。

### 模型迭代 3

正如引言中所述，在后续迭代中我们可以使用许多替代模型。为了保持模型易于解释，我们将在这一步使用决策树。欢迎尝试 scikit-learn 库中更复杂的模型，它们像下面的示例一样易于集成到代码中。`DecisionTreeRegressor`的代码如代码块 11-24 所示。

```
from sklearn.tree import DecisionTreeRegressor
