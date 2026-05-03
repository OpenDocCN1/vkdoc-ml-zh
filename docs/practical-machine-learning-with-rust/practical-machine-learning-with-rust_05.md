# 5. 自然语言处理

在前面的章节中，我们研究了几种不同的机器学习算法，然后学习了如何为这些算法处理数据。那些数据集大多是表格形式的，各个值要么是数值型，要么是分类型。通常，这些数据集不需要太多处理，因为计算机擅长处理标准化和结构化的数据。然而，当尝试将这些机器学习技术应用于人类语言时，会带来一系列全新的挑战，因为语言并不精确，并且在不同的上下文中具有不同的含义。甚至当我们从一种语言切换到另一种语言时，基本结构也会发生变化。因此，在创建智能应用时，语言需要特殊考虑，这被归入自然语言处理（NLP）领域。

在本章中，我们将研究 NLP 中的不同问题陈述，并理解用于解决这些特定问题的技术。首先，我们将了解句子分类。然后，我们将看到如何对文本语料库执行命名实体识别。最后，我们将学习如何创建一个意图推理引擎来支持一个良好的聊天机器人。这些问题将使用具有代表性的数据集进行描述。



## 5.1 句子分类

为了在 Rust 中实现句子分类，我们将使用 fastText 库。FastText 是 Facebook 开发的一个库，用于高效学习词向量表示和句子分类。其前提是利用浅层神经网络构建分布式和分布式的词向量。首先，让我们看看如何对语料库进行句子分类。

为了使用 fastText 模型，我们将像前几章那样，使用 `cargo` 创建一个名为 `fasttext-model` 的包。这将在 chapter5 文件夹中创建 `fasttext-model` 目录（列表 5-1）。

```
$ cd chapter5 && cargo new fasttext-model --bin
$
列表 5-1
创建 fasttext-model 包
```

为了理解如何实现分类，我们需要处理一个数据集。一个有趣的分类数据集是恐怖作者数据集。^(²¹) 该数据集包含来自公有领域小说作品的文本，由三位著名作者撰写：埃德加·爱伦·坡、H.P. 洛夫克拉夫特和玛丽·雪莱。下载数据并将文件解压到 `fasttext-model` 目录下的 `data` 文件夹中。现在，我们应该在文件夹中拥有训练文件（列表 5-2）。

```
$ head -n2 data/train.csv
"id","text","author"
"id26305","This process, however, afforded me
no means of ascertaining the dimensions of my
dungeon; as I might make its circuit, and return
to the point whence I set out, without being
aware of the fact; so perfectly uniform seemed the wall.","EAP"
$
列表 5-2
窥探恐怖作者数据
```

数据有三个字段，因此我们将创建一个反映该数据的结构体（列表 5-3）。

```
#[derive(Debug, Deserialize)]
pub struct SpookyAuthor {
id: String, text: String, author: String
}
列表 5-3
chapter5/fasttext-model/src/main.rs
```

现在，在继续之前，让我们讨论一下创建简单 fastText 模型时将使用的依赖项。我们有常用的 `csv`、`serde` 和 `serde_derive` 来解析 CSV 文件并将每条记录反序列化到上述结构体中。我们有 `rand` 来打乱数据。Rust 包 `stopwords`、`rust-stemmers` 和 `vtext` 将用于规范化和分词语料库。然后，我们将使用 `fasttext` crate 来创建 `fastText` 分类模型，以及 `itertools` crate 来提供一些辅助函数。因此，我们应该在 Cargo 文件中看到依赖项，如列表 5-4 所示。

```
[package]
name = "fasttext-model"
version = "0.1.0"
edition = "2018"
[dependencies]
csv = "1.0.7"
serde = "1"
serde_derive = "1"
rand = "0.6.5"
fasttext = "0.4.1"
stopwords = "0.1.0"
vtext = "0.1.0-alpha.1"
rust-stemmers = "1.1.0"
itertools = "0.8.0"
列表 5-4
chapter5/fasttext-model/Cargo.toml
```

像往常一样，为不同的 crate 使用最新版本。一旦这些 crate 被添加到依赖项中，我们应该能够在 `main.rs` 文件中导入相关模块（列表 5-5）。

```
extern crate serde;
#[macro_use]
extern crate serde_derive;
use std::io;
use std::vec::Vec;
use std::error::Error;
use std::io::Write;
use std::fs::File;
use std::collections::HashSet;
use csv;
use rand::thread_rng;
use rand::seq::SliceRandom;
use fasttext::{FastText, Args, ModelName, LossName};
use stopwords::{Spark, Language, Stopwords};
use itertools::Itertools;
use vtext::tokenize::VTextTokenizer;
use rust_stemmers::{Algorithm, Stemmer};
列表 5-5
chapter5/fasttext-model/src/main.rs
```

我们将在代码中使用一些常量，因此可以在列表 5-6 中定义它们。

```
const TRAIN_FILE: &str = "data.train";
const TEST_FILE: &str = "data.test";
const MODEL: &str = "model.bin";
列表 5-6
chapter5/fasttext-model/src/main.rs
```

FastText 要求训练文件采用特定格式。标签必须以 `__label__` 关键字为前缀，然后使用空格作为分隔符；一旦特定句子或文档的所有标签完成，句子其余部分紧随其后。例如，如果 l1 和 l2 是句子“this is a sentence”的两个标签，那么 fastText 期望其格式如列表 5-7 所示。

```
__label__l1 __label__l2 this is a sentence
列表 5-7
fastText 格式示例
```

不同的文档通过换行符区分。因此，我们需要将当前格式转换为 fastText 格式。为此，我们首先需要将 `train.csv` 文件读取到我们的结构体中。思路是，一旦原始数据被反序列化到结构体中，我们应该能够将所需的更改作为结构体的方法来实现。与第 2 章类似，我们将 CSV 文件中的行读取到 `SpookyAuthor` 结构体的向量中，并打乱该向量以便后续更好地分类（列表 5-8）。

```
fn main() -> Result> {
let mut rdr = csv::Reader::from_reader(io::stdin());
let mut data = Vec::new();
for result in rdr.deserialize() {
let r: SpookyAuthor = result?;
data.push(r); // 所有数据都被推送到这个向量中。
}
data.shuffle(&mut thread_rng()); // 我们随机打乱数据
let test_size: f32 = 0.2; // 测试集大小为 20%
let test_size: f32 = data.len() as f32 * test_size;
let test_size = test_size.round() as usize;
let (test_data, train_data) = data.split_at(test_size);
// 其余代码...
列表 5-8
chapter5/fasttext-model/src/main.rs
```

由于我们需要用 `__label__` 关键字作为标签的前缀，我们将为 `SpookyAuthor` 实现一个 `into_labels` 方法（列表 5-9）。

```
impl SpookyAuthor {
// 其他方法..
fn into_labels(&self) -> String {
match self.author.as_str() {
"EAP" => String::from("__label__EAP"),
"HPL" => String::from("__label__HPL"),
"MWS" => String::from("__label__MWS"),
l => panic!("
无法解析目标。\
传入了其他目标。{:?}", l),
}
}
// 其他方法..
}
列表 5-9
chapter5/fasttext-model/src/main.rs
```

现在，为了训练一个好的 fastText 模型，建议对原始文本进行一些文本预处理。文本预处理主要是为了实现文本规范化，这意味着转换文本的特定区域，使文本更有利于机器学习。本节展示的技术并非详尽无遗，也非万无一失，但在各种上下文中被普遍采用。请根据您的具体上下文使用所有文本规范化技术，以达到最佳效果。

我们可以将所有文本转换为小写。这主要适用于主要使用拉丁字母书写的日耳曼语言。英语就是一个典型的例子。在这些语言中，小写和大写之间存在差异，并且在计算设备处理过程中它们被视为不同。因此，将文本转换为小写可能很重要，这样在训练期间的学习过程中，相似的文本就不会被区别对待。但是，在将所有文本转换为小写时应小心，因为首字母缩略词和枚举在转换为小写后可能会完全失去其含义。在 Rust 中，字符串上的 `to_lowercase` 方法会将所有字符转换为小写（列表 5-10）。

```
let lc_text = text.to_lowercase();
列表 5-10
chapter5/fasttext-model/src/main.rs
```



传统计算设备通常将文档存储为字符序列。即便是 Rust，也会将随机文档存储为 `Vec<u8>`。但这种格式无法为机器学习算法提供任何信息。因此，文档需要被分解为**具有语言学意义的单元**。这一过程被称为分词。由于不同语言在意义组织方式上存在差异，我们需要为不同语言创建不同的分词库。通常，在英语中，意义存在于由空格分隔的单词中，因此相对而言，英语比中文或日语等语言更容易处理，因为这些语言的书写系统中可能没有空格来分隔“单词”或“标记”。即使在英语中，也需要小心处理；例如，“New Delhi”需要作为一个整体来理解其含义。在 Rust 中，如果我们尝试对英文句子进行分词，可以使用 `vtext` crate（代码清单 5-11）。

```
let tok = VTextTokenizer::new("en");
let tokens: Vec = tok.tokenize(lc_text.as_str()).collect();
代码清单 5-11
chapter5/fasttext-model/src/main.rs
```

有时，在不同语言中，单词会根据不同的语言规则发生形态变化：例如，句子的时态。例如，根据语境的不同，单词“am”、“are”和“is”与“be”是相同的。你可能会争辩说，这些时态差异可能对最终含义很重要，但事实可能并非如此。时态信息可能只会为核心理解增加噪声。词干提取试图消除这些歧义，并通过一些启发式方法实现，这些方法根据常见的前缀和后缀截断单词的末尾。在我们的英语案例中，我们可以使用 `rust-stemmers` crate。该库为一些用 snowball 语言编写的词干提取算法提供了 Rust 实现。^(²²) 在代码清单 5-12 中，我们将使用 `Stemmer` 结构体来解析来自代码清单 5-11 中 vtext 分词器的标记，然后获取词干提取后的标记。

```
let en_stemmer = Stemmer::create(
Algorithm::English);
let tokens: Vec = tokens.iter().map(
|x| en_stemmer.stem(x).into_owned())
.collect();
let mut tokens: Vec = tokens.iter().map(
|x| x.as_str()).collect();
代码清单 5-12
chapter5/fasttext-model/src/main.rs
```

最后，我们将继续移除停用词。停用词是常用的词语，本质上是语言结构，对句子的含义没有贡献。英语中常见的停用词示例包括“a”、“am”、“the”、“is”等。你正在处理的目标语言可能包含也可能不包含停用词。移除停用词可以使用 `stopwords` 包，或者你也可以直接存储一个停用词向量，然后从标记中过滤掉这些停用词（代码清单 5-13）。

```
let stops: HashSet = Spark::stopwords(Language::English)
.unwrap().iter().collect();
// 注意 tokens 被初始化为可变的
tokens.retain(|s| !stops.contains(s));
代码清单 5-13
chapter5/fasttext-model/src/main.rs
```

现在，我们可以连接这些标记并返回整个字符串（代码清单 5-14）。

```
tokens.iter().join(" ")
代码清单 5-14
chapter5/fasttext-model/src/main.rs
```

以上是可以实现的不同预处理步骤。这个规范化技术列表并非详尽无遗，应根据具体上下文来使用。例如，在金融场景中，“₹200”在规范化后可能应该变成“two hundred rupees”。现实世界中的文本非常复杂，甚至可能混合了不同的语言。在这种情况下，文本规范化需要更加复杂，并处理更多的边界情况，才能构建有效的机器学习模型。上述步骤，当整合到 `into_tokens` 方法中时，可以在代码清单 5-15 中看到。

```
impl SpookyAuthor {
pub fn into_tokens(&self) -> String {
// 将所有文本转换为小写
let lc_text = self.text.to_lowercase();
// 对单词进行分词
let tok = VTextTokenizer::new("en");
let tokens: Vec = tok.tokenize(
lc_text.as_str()).collect();
// 对单词进行词干提取
let en_stemmer = Stemmer::create(Algorithm::English);
let tokens: Vec = tokens.iter().map(
|x| en_stemmer.stem(x).into_owned()).collect();
let mut tokens: Vec = tokens.iter().map(
|x| x.as_str()).collect();
// 移除停用词
let stops: HashSet = Spark::stopwords(Language::English)
.unwrap().iter().collect();
tokens.retain(|s| !stops.contains(s));
// 连接标记并返回
tokens.iter().join(" ")
}
// 其余方法 ...
代码清单 5-15
chapter5/fasttext-model/src/main.rs
```

完成后，我们应该能够连接标签和字符串，以实现 fastText 格式。然后，我们可以将连接后的字符串写入一个文件，作为训练数据（代码清单 5-16）。

```
fn push_training_data_to_file(train_data: &[SpookyAuthor],
filename: &str) -> Result> {
let mut f = File::create(filename)?;
for item in train_data {
writeln!(f, "{} {}", item.into_labels(), item.into_tokens())?;
}
Ok(())
}
代码清单 5-16
chapter5/fasttext-model/src/main.rs
```

回想一下，在代码清单 5-8 中，我们将完整数据拆分成了 `train_data` 和 `test_data`。现在，我们可以将 `train_data` 传递给这个函数，以创建训练文件（代码清单 5-17）。

```
push_training_data_to_file(train_data.to_owned(),
TRAIN_FILE)?;
代码清单 5-17
chapter5/fasttext-model/src/main.rs
```

类似于训练文件，我们也可以创建测试文件。但是，对于测试文件，我们应该确保不添加标签。测试文件应仅包含标记（代码清单 5-18）。

```
fn push_test_data_to_file(test_data: &[SpookyAuthor],
filename: &str) -> Result> {
let mut f = File::create(filename)?;
for item in test_data {
writeln!(f, "{}", item.into_tokens())?;
}
Ok(())
}
fn main() {
// 数据加载代码 ...
push_test_data_to_file(test_data.to_owned(),
TEST_FILE)?;
// 其余代码 ...
}
代码清单 5-18
chapter5/fasttext-model/src/main.rs
```

现在，所有预处理和数据组织工作都已完成，我们可以继续进行训练过程。为此，我们将创建一个 `Args` 上下文，并将该上下文传递给 fastText 结构体进行训练。Args 上下文将包含输入文件；由于这是一个分类问题，我们需要将模型类型定义为监督学习，并且损失函数需要是 softmax。应该使用 softmax，因为对于多标签分类，使用 softmax 相当于预测标签的分布，因为 softmax 本质上会将它们转换为概率。（代码清单 5-19）。

```
fn main() -> Result> {
// 之前的代码 ...
let mut args = Args::new();
args.set_input(TRAIN_FILE);
args.set_model(ModelName::SUP);
args.set_loss(LossName::SOFTMAX);
let mut ft_model = FastText::new();
ft_model.train(&args).unwrap();
// 其余代码 ...
}
代码清单 5-19
chapter5/fasttext-model/src/main.rs
```



因此，一旦模型创建完成，我们就可以检查其准确率并保存模型。具体做法是：对 `test_data` 中的每个元素调用 fastText 模型的 `predict` 方法，创建向量 `preds`；再对 `test_data` 中的每个元素调用 `into_labels` 方法，创建另一个变量 `test_labels`。然后，在一个 for 循环中逐一比较这两个向量，如果相同，则变量 `correct_hits` 自增。对于 `preds` 中的所有值，变量 `hits` 都会自增，以获取总数值的计数。最后计算这两个值的比值，得到准确率参数（列表 5-20）。

```
fn main() -> Result> {
// 之前的代码 ...
let preds = test_data.iter().map(
|x| ft_model.predict(x.text.as_str(), 1, 0.0));
let test_labels = test_data.iter().map(
|x| x.into_labels());
let mut hits = 0;
let mut correct_hits = 0;
let preds_clone = preds.clone();
for (predicted, actual) in preds.zip(test_labels) {
let predicted = predicted?;
// 只取第一个值。
let predicted = &predicted[0];
if predicted.clone().label == actual {
correct_hits += 1;
}
hits += 1;
}
assert_eq!(hits, preds_clone.len());
println!("accuracy={} ({}/{} correct)",
correct_hits as f32 / hits as f32,
correct_hits, preds_clone.len());
ft_model.save_model(MODEL)?;
Ok(())
}
列表 5-20
chapter5/fasttext-model/src/main.rs
```

现在，我们应该能够运行模型并训练 fastText 分类器了（列表 5-21）。

```
$ cd chapter5/fasttext-model
$ cargo run < data/train.csv
Compiling fasttext-model v0.1.0 (chapter5/fasttext-model)
Finished dev [unoptimized + debuginfo] target(s) in 1.42s
Running `target/debug/fasttext-model`
Read 0M words
Number of words:  5560
Number of labels: 3
Progress: 100.0% words/sec/thread:  134810 lr:  0.000000 loss:  1.079427 ETA:   0h 0m
accuracy=0.48263535 (1890/3916 correct)
$
列表 5-21
fasttext-model 训练
```

并且，我们应该能在目录中看到保存的模型文件（列表 5-22）。

```
$ ls -ltr model.bin
$
-rw-r--r--  1 joydeepbhattacharjee  staff  802313179 Aug 20 09:37 model.bin
列表 5-22
训练好的 fastText 模型
```

由于此模型是在 `fastText` 代码库中构建的，列表 5-22 中生成的 `model.bin` 文件符合通用的 `fastText` 规范，也可以被其他 `fastText` 应用程序使用。例如，如果我们使用原始的 `fastText` 应用程序加载该模型，它也能正常工作。参见列表 5-23。

```
$ ./fasttext predict ../model.bin -
I love ghosts.
__label__EAP
^C
$
列表 5-23
使用官方二进制文件进行 fastText 预测

要编译并创建上述 fasttext 二进制文件，请遵循 fasttext 网页上的说明：[`https://github.com/facebookresearch/fastText#building-fasttext-using-make-preferred`](https://github.com/facebookresearch/fastText%2523building-fasttext-using-make-preferred)`。

## 5.2 命名实体识别

自然语言处理的核心应用领域之一是命名实体识别（NER），它将文本中出现的实体分类到预定义的类别中。这些类别取决于上下文和具体问题。例如，一家旅游机构可能对城市和日期感兴趣。你可能会争辩说这可以用正则表达式实现，但如果你是一家成长中的公司，扩展此类操作会非常困难。此外，采用 NER 方法可以为内容增加丰富的语义知识，并有助于理解任何给定文本的主题。

用于 NER 任务的流行算法之一是条件随机场（CRF）。CRF 本质上是利用先前标签的上下文信息进行分类的分类器，从而增加标签做出良好预测所需的信息量。

为了有效执行 CRF，输入文本需要被正确分块。文本分块将每个句子划分为句法相关的词块。例如，句子“Rust is great for machine learning”可以如下划分：

*   [NP Rust][VP is][NP great][PP for][NP machine learning]

在这个例子中，NP 代表名词短语，VP 代表动词短语，PP 代表介词短语。此任务被形式化为一个序列标注任务，其中文本中的一系列标记被分配一系列标签。为了表示一个块，使用了 IOB2 标注法。块的开始由 B-标签表示，块内部由 I-标签表示，其他部分定义为 O。分块主要是一项手动任务，有一些流行的标注工具可以让用户操作起来稍微简单一些。

*   GATE - 通用架构与文本工程，已有 15 年以上历史，免费且开源。
*   Anafora - 这是一个免费且开源的、基于 Web 的原始文本标注工具。
*   brat - Brat 快速标注工具是一个用于协作文本标注的在线环境。
*   tagtog – 这是一个需要付费的专有工具。
*   prodigy - 这是一个由主动学习驱动的标注工具，需要付费。
*   LightTag - LightTag 是一个面向团队的托管式管理文本标注工具，需要付费。^(²³)

另一方面，我们将使用一个已经为我们标注好的数据集。可以通过以下链接从 kaggle 网站下载：[`https://www.kaggle.com/abhinavwalia95/entity-annotated-corpus`](https://www.kaggle.com/abhinavwalia95/entity-annotated-corpus)。下载 `ner.csv` 文件并将其保存在 `data` 文件夹中。我们可以看到数据包含许多字段，这对于 NER 数据集中应有的字段类型是一个很好的指导。让我们来看看这些字段。

*   lemma - 句子中标记的词元
*   next-lemma - 句子中下一个标记的词元
*   next-next-lemma - 句子中当前标记后第 2 个位置标记的词元
*   next-next-pos - 句子中当前标记后第 2 个位置标记的词性标注
*   next-next-shape - 句子中当前标记后第 2 个位置标记的形状
*   next-next-word - 句子中当前标记后第 2 个位置的标记
*   next-pos - 下一个（+1 位置）标记的词性标注
*   next-shape - 下一个（+1 位置）标记的形状
*   next-word - 下一个（+1 位置）标记
*   pos - 当前标记的词性标注
*   prev-iob - 前一个标记的 IOB 标注
*   prev-lemma - 前一个标记的词元
*   prev-pos - 前一个标记的词性标注
*   prev-prev-iob - 句子中当前标记前第 2 个位置标记的 IOB 标注
*   prev-prev-lemma - 句子中当前标记前第 2 个位置标记的词元
*   prev-prev-pos - 句子中当前标记前第 2 个位置标记的词性标注
*   prev-prev-shape - 句子中当前标记前第 2 个位置标记的形状
*   prev-prev-word - 句子中当前标记前第 2 个位置的标记
*   prev-shape - 前一个（当前标记前 -1 位置）标记的形状



*   `prev-word` 前一个词（当前令牌的前一个位置）

*   `sentence_idx` 句子索引（具有相同索引的令牌属于同一句子）

*   `shape` 令牌在句子中的形状

*   `word` 通常被称为令牌

*   `tag` 当前令牌的 IOB 标注

不过，在我们的示例中，我们只会使用词元（lemma）和下一个词元（next-lemma）来了解如何使用 `crf-suite` 创建 NER 模型。但在生产环境中，你应该使用上述所有特征以及你能集成的所有其他特征。

现在，为了创建 NER 的代码，我们将使用 `cargo` 创建一个二进制包 `crfsuite-model`（清单 5-24）。

```
$ cargo new crfsuite-model --bin
$
清单 5-24
创建 crfsuite-model 包
```

这将在 `crfsuite-model` 目录中创建 `src/main.rs` 和 `Cargo.toml` 文件（清单 5-25）。

```
#[derive(Debug, Deserialize, Clone)]
pub struct NER {
lemma: String,
#[serde(rename = "next-lemma")]
next_lemma: String,
word: String,
tag: String
}
清单 5-25
chapter5/crfsuite-model/src/main.rs
```

请注意，在清单 5-25 中，我们使用了 `serde` 的重命名功能，将 NER 结构体中的 `next_lemma` 识别为 CSV 文件中的 `next-lemma` 列。

在继续之前，让我们讨论一下创建模型所需的依赖项。`csv` crate 用于解析 CSV 文件，`serde` 和 `serde-derive` 也会提供帮助。`rand` crate 用于生成随机变量，而 `crfsuite` 是主要的 crate，用于在代码中实现 CRF 功能。请参见清单 5-26。

```
[dependencies]
csv = "1.0.7"
serde = "1"
serde_derive = "1"
rand = "0.6.5"
crfsuite = "0.2.6"
清单 5-26
chapter5/crfsuite-model/Cargo.toml
```

一旦这些内容写入 toml 文件，我们就应该能够在主模块中导入相关模块（清单 5-27）。

```
extern crate serde;
#[macro_use]
extern crate serde_derive;
use std::io;
use std::vec::Vec;
use std::error::Error;
use csv;
use rand;
use rand::thread_rng;
use rand::seq::SliceRandom;
