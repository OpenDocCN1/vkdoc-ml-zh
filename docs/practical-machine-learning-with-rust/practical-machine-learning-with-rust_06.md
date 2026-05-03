# 以下是新的且对 NER 有用的模块
use crfsuite::{Model, Attribute, CrfError};
use crfsuite::{Trainer, Algorithm, GraphicalModel};
清单 5-27
chapter5/crfsuite-model/src/main.rs
```

与我们之前看到的类似，我们现在可以创建一个函数，从标准输入读取数据，并将其存储在我们拥有的数据结构向量中，在本例中为 `Vec<NER>`。请参见清单 5-28。

```
fn get_data() -> Result, Box> {
let mut rdr = csv::Reader::from_reader(io::stdin());
let mut data = Vec::new();
for result in rdr.deserialize() {
let r: NER = result?;
data.push(r);
}
data.shuffle(&mut thread_rng());
Ok(data)
}
清单 5-28
chapter5/crfsuite-model/src/main.rs
```

既然我们已经有了数据，就应该能够将数据拆分为训练集和测试集，以便我们能够检查模型在样本外数据上的准确率。这与我们在其他模块和章节中看到的代码相同（清单 5-29）。

```
fn split_test_train(data: &[NER],
test_size: f32)
-> (Vec, Vec) {
let test_size: f32 = data.len() as f32 ∗ test_size;
let test_size = test_size.round() as usize;
let (test_data, train_data) = data.split_at(test_size);
(test_data.to_vec(), train_data.to_vec())
}
清单 5-29
chapter5/crfsuite-model/src/main.rs
```

现在到了有趣的部分。给定数据，我们需要将数据中的每个条目提取到 `crf_suite` crate 提供的 `Attributes` 向量中，以便训练模块能够在数据集上进行训练。这个 `Attribute` 将包含令牌及其值，即该令牌在序列中的权重。在清单 5-30 中，目标词的权重为 1.0，下一个词的权重为 0.5，但你可以调整这些权重，看看哪个能带来更好的结果。标签可以按预期使用 `Vec<String>`。

```
fn create_xseq_yseq(
data: &[NER])
-> (Vec>, Vec) {
let mut xseq = vec![];
let mut yseq = vec![];
for item in data {
let seq = vec![Attribute::new(item.lemma.clone(), 1.0),
Attribute::new(item.next_lemma.clone(), 0.5)]; // 主词的权重更高。
xseq.push(seq);
yseq.push(item.tag.clone());
}
(xseq, yseq)
}
清单 5-30
chapter5/crfsuite-model/src/main.rs
```

我们现在可以创建一个函数，根据 x 序列和 y 序列进行模型预测。在这种情况下，训练完成后，模型会保存到由 `model_name` 指定的文件中（清单 5-31）。

```
fn crfmodel_training(xseq: Vec>,
yseq: Vec,
model_name: &str)
-> Result> {
let mut trainer = Trainer::new(true); // 开启详细输出
trainer.select(Algorithm::AROW, GraphicalModel::CRF1D)?;
trainer.append(&xseq, &yseq, 0i32)?;
trainer.train(model_name, -1i32)?; // 使用所有实例进行训练。
Ok(())
}
清单 5-31
chapter5/crfsuite-model/src/main.rs
```

在清单 5-31 中，我们使用了自适应正则化权重向量（AROW）。我们可以使用的不同算法如下：

*   使用 L-BFGS 方法的梯度下降：这是一种寻找目标函数局部最小值的方法，利用了目标函数值及其梯度。

*   带 L2 正则化项的随机梯度下降：它将随机梯度下降与损失函数相结合，损失函数中增加了权重的平方项。这应该能避免模型对数据过拟合。

*   平均感知机：这是一种二元分类方法，使用了标准感知机算法的平均权重和偏置。

*   被动攻击算法：如果数据来自不同的样本，该算法会缓慢地遗忘旧分布。如果分类正确，则表现为“被动”；如果分类错误，则表现为“攻击性”，即权重发生变化。

*   自适应正则化权重向量：该算法结合了大间隔训练、置信度加权以及处理不可分数据的能力。AROW 在每次看到新实例时都会对预测函数进行自适应正则化，使其在存在标签噪声的情况下表现尤为出色。

训练完成后，我们可以从文件中加载模型并对其进行预测（清单 5-32）。

```
fn model_prediction(xtest: Vec>,
model_name: &str)
-> Result, Box>{
let model = Model::from_file(model_name)?;
let mut tagger = model.tagger()?;
let preds = tagger.tag(&xtest)?;
Ok(preds)
}
清单 5-32
chapter5/crfsuite-model/src/main.rs
```

我们可以使用这个 `model_prediction` 函数，看看我们的模型在测试数据上的表现如何。为此，我们需要使用准确率函数。这里的准确率函数与我们在前几章中看到的相同。请参见清单 5-33。



```
fn check_accuracy(preds: &[String], actual: &[String]) {
let mut hits = 0;
let mut correct_hits = 0;
for (predicted, actual) in preds.iter().zip(actual) {
if actual != "O" { // 不考虑其他类别，因为会虚增准确率
if predicted == actual && actual != "O" {
correct_hits += 1;
}
hits += 1;
}
}
println!("accuracy={} ({}/{} correct)",
correct_hits as f32 / hits as f32,
correct_hits,
hits);
}
代码清单 5-33
chapter5/crfsuite-model/src/main.rs
```

请注意其中的区别：我们不考虑实际值为“O”的情况。这是因为该标签表示“其他”，而大多数标签都属于其他类别。因此，如果将其纳入准确率计算，会虚增准确率，使结果优于实际水平。更通用的 CRF 模型准确率评估方法是使用精确率、召回率和 F1 分数，这些指标同样可以在 Rust 中以类似方式实现。

现在我们已经拥有了所有相关函数，可以在主函数中整合它们（代码清单 5-34）。

```
fn main() {
let data = get_data().unwrap();
let (test_data, train_data) = split_test_train(
&data, 0.2);
let (xseq_train, yseq_train) = create_xseq_yseq(
&train_data);
let (xseq_test, yseq_test) = create_xseq_yseq(
&test_data);
crfmodel_training(xseq_train,
yseq_train,
"rustml.crfsuite").unwrap();
let preds = model_prediction(xseq_test,
"rustml.crfsuite").unwrap();
check_accuracy(&preds, &yseq_test);
}
代码清单 5-34
chapter5/crfsuite-model/src/main.rs
```

运行代码清单 5-34 中的代码，输出应类似于代码清单 5-35 所示。

```
$ cd chapter5/crfsuite-model
$ cargo run < data/ner.csv
Finished dev [unoptimized + debuginfo] target(s) in 0.03s
Running `target/debug/crfsuite-model`
Feature generation
type: CRF1d
feature.minfreq: 0.000000
feature.possible_states: 0
feature.possible_transitions: 0
0....1....2....3....4....5....6....7....8....9....10
Number of features: 3137
Seconds required: 0.013
Adaptive Regularization of Weights (AROW)
variance: 1.000000
gamma: 1.000000
max_iterations: 100
epsilon: 0.000000
***** Iteration #1 *****
Loss: 1210.000000
Feature norm: 0.526780
Seconds required for this iteration: 0.012
**** Intermediate iterations
***** Iteration #100 *****
Loss: 369.217403
Feature norm: 296.558922
Seconds required for this iteration: 0.012
Total seconds required for training: 1.226
Storing the model
Number of active features: 3116 (3137)
Number of active attributes: 2067 (2088)
Number of active labels: 17 (17)
Writing labels
Writing attributes
Writing feature references for transitions
Writing feature references for attributes
Seconds required: 0.014
accuracy=0.5551948 (171/308 correct)
$
代码清单 5-35
crfsuite-model 输出
```

模型应已创建在目录中（代码清单 5-36）。

```
$ ls -ltr rustml.crfsuite
-rw-r--r--  1 joydeepbhattacharjee  staff  168196 Aug 20 09:10 rustml.crfsuite
代码清单 5-36
模型输出文件
```

如代码清单 5-35 所示，准确率相当低。这是因为为了简化，我们只使用了三个特征。尝试使用更多特征，看看准确率是否会提升。

命名实体识别是自然语言处理中重要且有趣的一类问题，而 `crfsuite` 是用于创建 NER 的 CRF 模型的优秀 crate。

在本节中，我们探讨了如何在 Rust 中使用 `crfsuite` 创建 NER 的 CRF 模型。我们还研究了如何对输入数据集进行预处理，使其适用于 CRF 套件模型。最后，我们介绍了一种评估 CRF 模型准确率的方法。

## 5.3 聊天机器人与自然语言理解（NLU）

虽然聊天机器人是一个复杂的主题，涉及生产系统中的大量集成和工程工作，但其核心驱动力之一是底层系统具备一定程度的自然语言理解能力。NLU 用于确定用户意图、从话语中提取信息，并与用户进行对话以执行和完成任务。

**意图识别** 大多数意图是简单的离散任务，例如“查找产品”、“转账”、“预订航班”，通常由动词和名词组合描述。这类意图会启动与用户的对话，以获取更多信息、从远程系统获取和更新数据，并向用户反馈进度。

意图识别的目标是将用户话语与其正确对应的任务或问题匹配起来。在 Rust 中，可以使用 `snips-nlu-lib` crate 对用户话语进行意图解析。但需要注意的是，该库仅可用于推理部分，训练仍需在 Python 中完成。

在继续之前，请确保你的机器已安装 clang。对于 Ubuntu 机器，可以使用代码清单 5-37 所示的命令安装 clang。

```
$ sudo apt update
$ sudo apt install clang
