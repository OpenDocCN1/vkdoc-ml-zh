# 保存预测类别列表（正面、负面）
classes = list(lr_pipeline.classes_)
```

我们的模型基于 `norm_train_reviews` 构建，该数据集包含我们在之前所有分析中使用的标准化训练评论。现在分类流水线已经准备就绪，你可以通过 pickle 或 joblib 保存分类器和特征对象来实际部署模型，这与我们在第 5 章“模型部署”一节中讨论的方法类似。假设我们的流水线已投入生产，如何将其用于新的电影评论呢？让我们尝试预测两条新样本评论的情感（这些评论未用于模型训练）。

```
In [3]: lr_pipeline.predict(['the lord of the rings is an excellent movie',
...:                      'i hated the recent movie on tv, it was so bad'])
Out[3]: array(['positive', 'negative'], dtype=object)
```

我们的分类流水线正确预测了这两条评论的情感！这是一个良好的开端，但如何解读模型的预测结果呢？一种常见的方法是使用模型预测的类别概率作为置信度的衡量标准。你可以使用以下代码获取样本评论的预测概率。

```
In [4]: pd.DataFrame(lr_pipeline.predict_proba(['the lord of the rings is an excellent movie',
...:                      'i hated the recent movie on tv, it was so bad']), columns=classes)
Out[4]:
negative  positive
0  0.169653  0.830347
1  0.730814  0.269186
```

因此，我们可以说第一条电影评论有 83% 的预测置信度或概率为正面情感，而第二条电影评论有 73% 的概率为负面情感。现在让我们更进一步，不再使用玩具示例，而是对 `test_reviews` 数据集中的真实评论运行相同的分析（我们将使用包含标准化文本评论的 `norm_test_reviews`）。除了预测概率，我们还将使用 `skater` 框架来轻松解读模型决策，这与我们在第 5 章“模型解读”一节中所做的工作类似。首先需要从 `skater` 包中加载以下依赖项。我们还定义了一个辅助函数，该函数接受文档索引、语料库、其响应预测以及一个解释器对象，帮助我们进行模型解读分析。

```
from skater.core.local_interpretation.lime.lime_text import LimeTextExplainer
explainer = LimeTextExplainer(class_names=classes)

