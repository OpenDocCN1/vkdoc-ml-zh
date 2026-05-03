# coding: utf-8
from crfsuite_model import CRFSuiteModel
model = CRFSuiteModel("model.crfsuite")
res = model.fit("data/ner.csv")
print(res)
代码清单 8-11
示例训练文件
```

现在，我们应该能够运行训练和预测的 Python 代码（代码清单 8-13）。

```
$ cd chapter8
$ python3 −m venv venv
$ source venv/bin/ activate
( venv ) $ pip install -e .
( venv ) $ python crfsuite_model_training.py
Feature generation
type: CRF1d
feature . minfreq : 0.000000
feature . possible_states : 0
feature . possible_transitions : 0
0 .... 1 .... 2 .... 3 .... 4 .... 5 .... 6 .... 7 .... 8 .... 9 .... 10
Number of features : 3136
Seconds required : 0.014
Adaptive Regularization of Weights (AROW)
variance : 1.000000
gamma: 1.000000
max_iterations : 100
epsilon : 0.000000
***** Iteration #1 *****
Loss : 1214.000000
Feature norm : 0.526245
Seconds required for this iteration : 0.014
***** Other iterations *****
***** Iteration #100 *****
Loss : 455.980399
Feature norm : 329.397943
Seconds required for this iteration : 0.012
Total seconds required for training : 1.257
Storing the model
Number of active features : 3115 (3136)
Number of active attributes : 2065 (2086)
Number of active labels : 17 (17)
Writing labels
Writing attributes
Writing feature references for transitions
Writing feature references for attributes
Seconds required : 0.015
accuracy =0.5263158 (160/304 correct)
model fit done
$
代码清单 8-13
运行训练代码
```

如果我们现在检查目录，应该会创建一个名为“model.crfsuite”的 crfsuite 模型文件（代码清单 8-14）。

```
$ l s
Cargo . lock      crfsuite_model.egg−info       data
requirements−dev.txt    venv
Cargo . toml      crfsuite_model_prediction.py  model.crfsuite
setup . py
MANIFEST. in      crfsuite_model_training.py    pip−wheel−meta
data src
crfsuite_model    crfsuite_model_training1.py   pyproject.toml
target
$
代码清单 8-14
检查模型文件
```

现在模型已创建，我们应该能够运行预测代码（代码清单 8-15）。

```
( vevn ) $ python crfsuite_model_prediction . py
[ 'O' , 'O' , 'O' , 'O' , 'O' , 'O' , 'B−geo' , 'O', 'O' ]
$
代码清单 8-15
运行预测代码
```

类似地，我们可以从本书中选取其他示例，并创建可在 Python 中调用的 API。

### 8.1.2 Java

与我们之前看到的 Python 示例类似，我们可以集成 Rust 函数并在 Java 类中调用它们。概念是相似的，我们需要在 C 接口中暴露 Rust 函数，并在 Java 中定义引用这些 C 接口的本地方法。这是通过 `jni` crate 完成的。让我们以第 2 章的 xgboost 示例为例，并将其增强为一个 Java 应用程序。

与我们之前在 Python 部分看到的一样，任何 FFI 项目都会在两种语言中都有一些代码。我们将看到作为 Java 端和 Rust 端之间桥梁所需的最少代码量。Java 要求所有本地方法都遵循 Java 本地接口（JNI）标准。因此，我们需要从 Java 定义函数签名，然后编写符合该签名的 Rust 代码。步骤如下：

*   编写功能代码；
*   编写 Java 类；
*   编写 Rust 接口，该接口封装 Rust 功能并提供在 Java 中访问这些功能的途径。

**Rust 功能** 为了实现 Rust 函数，我们将复制并粘贴之前在 xgboost 中的代码。请注意，之前的代码是一个二进制可执行文件。因此，我们需要将包转换为库，以便能够在 Rust 中调用这些函数（代码清单 8-16）。

```
$ cd chapter8
$ cp -r ../chapter2/iris_classification_xgboost .
$ mv src/main.rs src/lib.rs
$
代码清单 8-16
为 Java 接口复制 xgboost
```

现在我们需要对代码进行一些编辑。请注意，所有代码都写在 `run` 函数中，而该函数又在 `main` 函数中被调用。在这种情况下，我们不需要 `main` 函数，因此我们将删除它。然后，我们将 `run` 函数拆分为两个函数：`fit` 和 `predict`。`fit` 函数将读取训练文件，根据相关向量进行整理，然后训练模型。模型训练完成后，我们将模型保存到 `xgb.model` 文件中（代码清单 8-17）。

```
pub fn fit() -> Result> {
let training_file = "data/iris.csv";
let file = File::open(training_file).unwrap();
let mut rdr = csv::Reader::from_reader(file);
let mut data = Vec::new();
for result in rdr.deserialize() {
let r: Flower = result.unwrap();
data.push(r); // data contains all the records
}
// previous code that was part of run function.
// train model, and print evaluation data
let booster = Booster::train(&params).unwrap();
// save and load model file
println!("\nSaving Booster model...");
booster.save("xgb.model").unwrap();
Ok(())
}
代码清单 8-17
chapter8/iris_classification_xgboost/iris_classification_library/src/lib.rs
```

类似地，我们有 `predict` 方法，它跳过了所有训练代码，只包含数据读取、整理代码和预测代码（代码清单 8-18）。

```
pub fn predict() -> Result> {
println!("Loading model");
let booster = Booster::load("xgb.model").unwrap();
let predict_file = "data/predict.csv";
let file = File::open(predict_file).unwrap();
let mut rdr = csv::Reader::from_reader(file);
let mut data = Vec::new();
for result in rdr.deserialize() {
let r: Flower = result.unwrap();
data.push(r); // data contains all the records
}
let val_size: usize = data.len();
// differentiate the features and the labels.
let flower_x_val: Vec = data.iter().flat_map(
|r| r.into_feature_vector()).collect();
let flower_y_val: Vec = data.iter().map(
|r| r.into_labels()).collect();
// validation matrix with 1 row
let mut dval = DMatrix::from_dense(
&flower_x_val, val_size).unwrap();
dval.set_labels(&flower_y_val).unwrap();
let preds = booster.predict(&dval).unwrap();
Ok(flower_decoder(preds[0]))
}
代码清单 8-18
chapter8/iris_classification_xgboost/iris_classification_library/src/lib.rs
```



检查一下，在 `fit` 函数中，我们训练模型并保存模型。类似地，在 `predict` 方法中，我们加载模型来执行预测。你可能已经理解，这种在运行之间将模型持久化到磁盘的方法并不理想。此外，对于每个预测请求，它都会一遍又一遍地读取整个模型。更好的方法是将模型持久化在内存中，然后传递模型的指针，但这可能不稳定，并且可能存在内存泄漏。你可以看到，在 Python 部分也出现了同样的方法。

**Java 类** 现在我们已经重构了 Rust 类以覆盖所需功能，我们将回到 Java 类定义。需要注意的主要一点是，我们需要定义一个类，其方法名称与我们将要调用的函数名称相同，以便 JNI 能够有效地链接这些函数。因此，我们定义如清单 8-19 所示的签名。

```
class IrisClassificationXgboost {
private static native void fit();
private static native String predict();
static {
System.loadLibrary("iris_classification_xgboost");
}
}
清单 8-19
chapter8/iris_classification_xgboost/iris_classification_library/src/lib.rs
```

在[清单 6-19] 的 Java 类中，我们有 `IrisClassificationXgboost` 类，然后在其下定义了本地方法 `fit` 和 `predict`。`static System.loadLibrary` 将加载库，理解这些函数，并充当适当的链接器。

**包装函数**：现在我们将编写适当的链接器函数来包装我们的功能。为了简化操作，让我们创建一个 `iris_classification_library` 文件夹，并将 Rust 代码移入该文件夹。我们还需要在 `Cargo.toml` 中做一些修改。除了之前的依赖项，我们还需要添加 `jni` 依赖项，并且需要将 crate 类型指定为 `cdylib`（清单 8-20）。

```
[package]
name = "iris_classification_xgboost"
version = "0.1.0"
edition = "2018"
[dependencies]
