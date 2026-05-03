# 之前的依赖项
ml-utils = { path = "../../../chapter2/ml-utils" } # 提供 ml-utils 的正确路径
jni = "0.12.3"
[lib]
name = "iris_classification_xgboost"
crate-type = ["cdylib"]
清单 8-20
chapter8/iris_classification_xgboost/iris_classification_library/Cargo.toml
```

现在，如果我们在该目录中运行 `cargo build`，我们应该会在 `target/debug` 文件夹中看到一个 `.dylib` 文件或 `.so` 文件（取决于你的操作系统）。现在我们需要定义我们的导出方法（清单 8-21）。

```
use jni;
use jni::JNIEnv;
use jni::objects::{GlobalRef, JClass, JObject, JString};
use jni::sys::{jint, jlong, jstring, jbyteArray};
#[no_mangle]
#[allow(non_snake_case)]
pub unsafe extern "system" fn Java_IrisClassificationXgboost_fit(_env: JNIEnv, _class: JClass) {
fit().unwrap();
}
#[no_mangle]
#[allow(non_snake_case)]
pub unsafe extern "system" fn Java_IrisClassificationXgboost_predict(
env: JNIEnv,
_class: JClass,
) -> jstring {
let output = env.new_string(predict().unwrap())
.expect("无法创建 Java 字符串！");
output.into_inner()
}
清单 8-21
chapter8/iris_classification_xgboost/iris_classification_library/src/lib.rs
```

注意清单 8-21 中我们加载的依赖项。`JNIEnv` 是 JVM 的接口，大多数方法都将基于它工作。`jni::objects` 中的对象是携带额外生命周期信息的对象，这些信息防止它们逃逸出上下文。`jni::sys` 中的对象旨在从 Rust 返回指向相应数据的指针。我们不能发送确切的数据，因为生命周期检查器不允许。

现在让我们关注包装函数 `Java_IrisClassificationXgboost_fit` 和 `Java_IrisClassificationXgboost_predict`。我们将对这些包装函数应用 `no_mangle` 外部属性，这样 Rust 在创建二进制文件时就不会混淆方法名称，Java 也能够识别这些函数。通常，如果在创建二进制文件时不这样做，Rust 会在内部混淆代码中编写的方法名称。^(³⁶) Java 需要按照 `Java_ 类名 _ 方法名` 的格式编写。通过这种方式，创建了 `Java_IrisClassificationXgboost_fit` 和 `Java_IrisClassificationXgboost_predict` 包装函数。

现在 `fit` 方法没问题，因为它是一个 void 方法，但对于 `Java_IrisClassificationXgboost_predict`，我们返回一个字符串。在这种情况下，我们需要处理一些额外的事情。输出类型需要是 `jstring`，因此我们需要使用 `predict` 函数中的 `env.new_string` 创建一个新的 Java 字符串。然后我们将返回指向该函数的指针。

现在我们已经编写了所有将 Rust 函数暴露给 Java 代码的代码。我们现在应该能够运行这段代码了。为此，我们在 Java 中创建 `main` 方法（清单 8-22）。

```
class IrisClassificationXgboost {
// 之前的代码
public static void main(String[] args) {
IrisClassificationXgboost.fit();
String predictions = IrisClassificationXgboost.predict();
System.out.println(predictions);
}
}
清单 8-22
chapter8/iris_classification_xgboost/iris_classification_library/src/lib.rs
```

注意清单 8-22 中，我们将 `IrisClassificationXgboost.predict` 的输出作为 Java 字符串检索，然后将其打印出来。我们现在应该能够编译这段代码并运行这些函数（清单 8-23）。

```
$ cd iris_classification_library \
&& cargo build
$ javac IrisClassificationXgboost.java \
&& java -Djava.library.path=iris_classification_library/target/debug/ \
IrisClassificationXgboost
[15:27:22] 前方危险：您已手动指定了 `updater` 参数。`tree_method` 参数将被忽略。更新器顺序不正确将导致未定义行为。对于常见用途，我们建议改用 `tree_method` 参数。
[15:27:22] src/tree/updater_prune.cc:74: 树剪枝结束，1 个根节点，2 个额外节点，0 个剪枝节点，max_depth=1
[15:27:22] src/tree/updater_prune.cc:74: 树剪枝结束，1 个根节点，4 个额外节点，0 个剪枝节点，max_depth=2
[15:27:22] src/tree/updater_prune.cc:74: 树剪枝结束，1 个根节点，4 个额外节点，0 个剪枝节点，max_depth=2
[0]     test-merror:0.066667    train-merror:0.044444
[15:27:22] src/tree/updater_prune.cc:74: 树剪枝结束，1 个根节点，2 个额外节点，0 个剪枝节点，max_depth=1
[15:27:22] src/tree/updater_prune.cc:74: 树剪枝结束，1 个根节点，4 个额外节点，0 个剪枝节点，max_depth=2
[15:27:22] src/tree/updater_prune.cc:74: 树剪枝结束，1 个根节点，4 个额外节点，0 个剪枝节点，max_depth=2
[1]     test-merror:0.066667    train-merror:0.033333
正在保存 Booster 模型...
正在加载模型
setosa
清单 8-23
chapter8/iris_classification_xgboost/iris_classification_library/src/lib.rs
```

Java 代码能够获取预测结果并将其打印出来。我们现在可以将这些命令编码到一个 makefile 中（清单 8-24）。

```
java_run: lib
javac IrisClassificationXgboost.java \
&& java -Djava.library.path=iris_classification_library/target/debug/ \
IrisClassificationXgboost
.PHONY: lib
javah:
javah IrisClassificationXgboost
lib:
cd iris_classification_library \
&& cargo build
清单 8-24
chapter8/iris_classification_xgboost/Makefile
```

通过这种方式，我们应该能够在 Rust 中创建函数，并在 Java 中调用这些应用程序。



## 8.2 云中的 Rust

在云中使用机器学习有多种好处。

*   云的按需付费模式非常适合突发性和不稳定的 AI 或机器学习工作负载。

*   云使企业能够轻松试验机器学习能力，并随着项目投入生产以及需求增加而进行扩展。

*   构建能为企业带来价值的机器学习应用存在诸多障碍：从围绕数据筛选、构建和训练优秀机器学习模型的专业知识，到机器学习模型所需的专用硬件。优秀的云提供商专注于为所有这些方面提供解决方案。

在本节中，我们将了解 AWS Lambda。在设计和学习了一个 ML 模型之后，最困难的部分实际上是在生产环境中运行和维护它。将无服务器应用集成到机器学习工作流中，可以快速扩展机器学习应用。

在创建无服务器应用时，我们通常会选择 Python 或 Node 作为运行时。幸运的是，我们有 Rust 的 AWS 运行时^(³⁷)，因此我们能够用 Rust 创建无服务器应用。AWS 无服务器应用的 API 定义了 Lambda 编程模型的基于 HTTP 的规范，理想情况下，该规范可以在任何模型中实现。

在我们的应用中，我们将创建一个简单的单词计数应用。要创建该应用，我们需要将依赖项添加到 `Cargo.toml` 文件中。`serde` 依赖项将序列化和反序列化传入的数据。我们可以使用 `log` 和 `simple-logger` 进行日志记录，并使用 `regex` 来解析传入的文本。最后，最重要的是，我们需要添加 `lambda_runtime` 才能创建无服务器应用（清单 8-25）。

```
[package]
name = "my_lambda_function"
version = "0.1.0"
edition = "2018"
[dependencies]
lambda_runtime = "0.1"
serde = "¹"
serde_derive = "¹"
serde_json = "¹"
log = "0.4"
simple_logger = "¹"
regex = "1"
Listing 8-25
chapter8/my_lambda_function/Cargo.toml
```

为了能够序列化和反序列化传入的数据，我们需要创建一个事件结构体，并添加序列化和反序列化功能，以便运行时能够解析数据（清单 8-26）。

```
use serde_derive;
use serde_derive::{Serialize, Deserialize};
#[derive(Serialize, Deserialize)]
struct CustomEvent {
string: String,
}
Listing 8-26
chapter8/my_lambda_function/src/main.rs
```

在我们的 lambda 应用中，我们需要在 `main` 函数中执行处理程序。在清单 8-27 中，`my_handler` 是处理函数，它将包含事件输出的处理代码。

```
use lambda_runtime;
use lambda_runtime::{lambda, Context, error::HandlerError};
use log;
use log::error;
use std::error::Error;
fn main() -> Result> {
simple_logger::init_with_level(log::Level::Debug).unwrap();
lambda!(my_handler);
Ok(())
}
Listing 8-27
chapter8/my_lambda_function/src/main.rs
```

现在，我们应该能够放入处理程序的代码了。传入的数据将被解析并使用 `serde` 放入 `CustomEvent` 结构体中。如果没有提供字符串，则返回提示，说明未提供输入字符串。如果提供了字符串，则创建一个哈希映射，其中包含字符串中的所有单词，其值为单词在字符串中出现的次数。字符串中的单词通过使用 `regex` 模块进行解析来识别。最后，我们需要将其序列化为字符串，我们使用 json 序列化来执行此操作（清单 8-28）。

```
use std::collections;
use std::collections::hash_map::Entry::{Occupied, Vacant};
use regex;
use regex::Regex;
fn my_handler(
event: CustomEvent, ctx: Context)
-> Result {
if event.string == "" {
error!("Empty string in request {}", ctx.aws_request_id);
return Err(ctx.new_error("Empty input string"));
}
let mut map = collections::HashMap::::new();
let re = Regex::new(r"\w+").unwrap();
for caps in re.captures_iter(&event.string) {
if let Some(cap) = caps.get(0) {
let word = cap.as_str();
match map.entry(word.to_string()) {
Occupied(mut view) => { *view.get_mut() += 1; }
Vacant(view) => { view.insert(1); }
}
}
}
let j = serde_json::to_string(&map).unwrap();
Ok(j)
}
Listing 8-28
chapter8/my_lambda_function/src/main.rs
```

现在，虽然应用本身已经完成，但我们还需要执行一些额外的步骤，才能使其在云服务器上的 lambda 运行时中运行。当配置为使用带有运行时 API 的自定义运行时，AWS Lambda 期望部署包包含一个名为 `bootstrap` 的可执行文件。我们可以配置 Cargo 生成一个名为 `bootstrap` 的文件，无论我们的 crate 名称是什么。首先，在文件的 `[package]` 部分，添加 `autobins = false` 设置。然后，在 `Cargo.toml` 的底部，添加一个新的 `[[bin]]` 部分，提供二进制文件的名称和代码的路径（清单 8-29）。

```
[package]
name = "my_lambda_function"
version = "0.1.0"
authors = ["joydeep bhattacharjee"]
autobins = false
edition = "2018"
