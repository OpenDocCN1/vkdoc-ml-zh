# 4. 处理数据

机器学习应用依赖于数据运行。构建机器学习应用时，主要活动之一是从不同数据源整理数据、以有效格式存储，并将原始数据转换为适合机器学习应用的格式。数据可能以不同格式出现。在前面的章节中，我们主要处理`CSV`文件。`CSV`文件非常适合存储和检索信息。此外，`CSV`中的数据范式可以很好地转换为大多数计算发生的矩阵格式，这是一个额外优势。但在实际处理真实数据时，我们很少发现数据以完美的`CSV`格式呈现。在本章中，我们将探讨存储和检索信息的几种流行范式，并了解如何利用它们来提取、处理和存储数据。

## 4. JSON

`JSON`代表`JavaScript`对象表示法（`JavaScript Object Notation`）。它是一种非常常见的用于异步浏览器-服务器通信的格式；因此，很多时候来自某些网络进程的数据会直接以`JSON`格式存储。许多开发者认为`JSON`是`Web`的默认序列化-反序列化数据结构，其结果是许多`Web API`以`JSON`格式发布数据。在本节中，我们将了解如何处理`JSON`数据。

`Serde`及其相关`crate`非常适合不同数据格式的序列化和反序列化。对于`JSON`，我们有`serde_json`。

为了了解如何处理`JSON`数据结构以及如何编写将`JSON`对象解析为`Rust`数据类型的代码，让我们创建一个名为`data_formats`的包（代码清单 4-1）。

```
$ cd chapter4 && cargo new data_formats –bin
代码清单 4-1
为 `data_formats` 创建 `bin` 包
```

现在，在`data_formats`文件夹内，打开`Cargo.toml`文件，并用代码清单 4-2 中所示的依赖项更新其内容。正如我们之前所见，`serde`和`serde-derive`用于数据管理，而`serde_json`用于解析`JSON`文件。

```
[package]
name = "data_formats"
version = "0.1.0"
edition = "2018"
[dependencies]
serde = "1.0.90"
serde_derive = "1.0.90"
serde_json = "1.0"
代码清单 4-2
chapter4/working_with_data/data_formats/Cargo.toml
```

现在，我们应该能够读取任意`JSON`字符串并获取`JSON`的结构。这由`serde_json::Value`枚举实现（代码清单 4-3）。

```
pub fn run() -> Result<(), Box<dyn std::error::Error>> {
    let json_str = r#"{
        "FirstName": "John",
        "LastName": "Doe",
        "Age": 43,
        "Address": {
            "Street": "Downing Street 10",
            "City": "London",
            "Country": "Great Britain"
        },
        "PhoneNumbers": [
            "+44 1234567",
            "+44 2345678"
        ]
    }"#;
    let person: serde_json::Value = serde_json::from_str(json_str)
        .expect("JSON 格式不正确");
    let address = person.get("Address").unwrap();
    println!("{:?}", address.get("City").unwrap());
    Ok(())
}
代码清单 4-3
chapter4/working_with_data/data_formats/src/jsonreading.rs
```

为了能够运行代码，我们需要在`main`函数中调用该函数。与我们目前看到的其他包类似，我们可以定义包含`main`方法的`main.rs`文件，该方法仅解析传入的参数。如果传入的参数是`json`，则将控制权传递给`jsonreading`模块中的`run`函数。为此，当然，我们需要使用`mod`关键字暴露`jsonreading`模块（代码清单 4-4）。

```
#[macro_use]
extern crate serde_derive;
extern crate serde;
extern crate serde_json;
extern crate serde_xml_rs;

use std::vec::Vec;
use std::process::exit;
use std::env::args;

mod jsonreading;
mod xmlreading;
mod csvreading;

fn main() {
    let args: Vec<String> = args().collect();
    let model = if args.len() < 2 {
        println!("无操作");
        return Ok(());
    } else {
        match args[1].as_ref() {
            "json" => jsonreading::run(),
            _ => { println!("无操作"); Ok(()) },
        }
    };
    // 将主代码放在另一个函数中有两个目的：
    // 1. 我们可以使用 '?' 运算符。
    // 2. 我们可以安全地调用 exit，它不会运行任何析构函数。
    exit(match res {
        Ok(_) => 0,
        Err(e) => {
            println!("{}", e);
            1
        }
    })
}
代码清单 4-4
chapter4/working_with_data/data_formats/src/main.rs
```

现在我们已经有了这些结构，可以运行该包并获取输出（代码清单 4-5）。

```
$ cargo run json
   Finished dev [unoptimized + debuginfo] target(s) in 0.05s
    Running 'target/debug/data_formats json'
String("London")
代码清单 4-5
chapter4/working_with_data/data_formats/src/jsonreading.rs
```

虽然清单 4-3 中的代码可以运行，但其缺点在于我们没有利用`Rust`的强类型能力。使用强类型数据结构非常有益，因为当传入的数据格式发生变化时（这种情况经常发生），你希望程序能够失败，并且你希望快速获知这一信息。你不希望损坏的数据畅通无阻地通过，进而污染下游的机器学习应用。

因此，我们要做的是引入一个强类型结构，并基于该结构进行反序列化。这样，我们还能额外获得数据验证的好处。

举个例子，我们将处理一个稍微复杂一点的`JSON`文件，它是一个包含不同领域诺贝尔奖的数据集。^(12) 为了解析这个数据集，我们将创建一个本质上为结构体层次结构的东西（清单 4-6）。

```
#[derive(Debug, Serialize, Deserialize)]
struct Prizes {
    prizes: Vec<Prize>,
}

#[derive(Debug, Serialize, Deserialize)]
#[allow(non_snake_case)]
struct Prize {
    category: String,
    #[serde(default)]
    overallMotivation: Option<String>,
    laureates: Vec<NobelLaureate>,
    #[serde(deserialize_with = "de_u16_from_str")]
    year: u16,
}

#[derive(Debug, Serialize, Deserialize)]
struct NobelLaureate {
    share: String,
    #[serde(default)]
    motivation: Option<String>,
    surname: String,
    #[serde(deserialize_with = "de_u16_from_str")]
    id: u16,
    firstname: String,
}
清单 4-6
chapter4/working_with_data/data_formats/src/jsonreading.rs
```

所以，顶层字段是`prizes`，它是一个`Prizes`列表，每个`Prizes`包含`categories`、`overallMotivation`和`year`作为单一属性，以及`laureates`作为`laureates`列表；`laureates`包含`share`、`motivation`、`surname`、`id`和`firstname`作为属性。`Motivation`和`overallMotivation`可能不存在。因此，我们可以通过“告知”`serde`提供默认值（此处为`None`）来将它们设为可选。由于`year`和`id`是数字，我们将尝试将它们修改为`u16`类型，并编写一个简单的辅助函数`de_u16_from_str`来执行从字符串到`u16`的转换。默认情况下，`serde`反序列化器只支持转换为字符串（清单 4-7）。

```
fn de_u16_from_str(deserializer: D)
-> Result
where D: Deserializer
{
let s = String::deserialize(deserializer)?;
u16::from_str(&s).map_err(de::Error::custom)
}
清单 4-7
`chapter4/working_with_data/data_formats/src/jsonreading.rs`
```

在清单 4-7 中，`deserializer` 是实现了 Serde 反序列化器 trait 的对象，内存中对此对象的任何引用都将具有 `'a` 生命周期。一旦从反序列化器中获取到特定的字符串对象，我们就使用 `u16::from_str` 函数将其解析为 `u16` 对象并返回。如果出现错误，则会引发 `de::Error::custom` 错误。

现在我们已经完成了结构体的定义，serde 应该能够轻松地将文件读取到该结构中（清单 4-8）。

```
use std::fs::File;
pub fn run() -> Result> {
// 之前的代码 ...
println!("from prizes json file");
let file = File::open("data/prize.json")
.expect("file should open read only");
let prizes_data: Prizes = serde_json::from_reader(file)
.expect("file should be proper JSON");
// 检查输出
let prizes_0 = &prizes_data.prizes[0];
println!("category: {:?}", prizes_0.category);
}
清单 4-8
`chapter4/working_with_data/data_formats/src/jsonreading.rs`
```

运行清单 4-8 的输出应与清单 4-9 中的输出类似。

```
$ cargo run json
Finished dev [unoptimized + debuginfo] target(s) in 0.02s
Running `target/debug/data_formats json`
String("London")
from prizes json file
category: "physics"
清单 4-9
`chapter4/working_with_data/data_formats/src/jsonreading.rs` 输出
```

如果没有错误，我们就能毫无问题地解析 JSON 文件，并且定义的模式结构体也能正常工作。

## 4.2 XML

另一种流行的数据格式是 XML 数据格式。XML 格式也被认为是应用和设备间通信的开放标准之一。因此，大量数据也以 XML 格式提供。解析 XML 文件的工作方式和逻辑与 JSON 文件格式中展示的相同。我们以结构体层次结构定义数据，然后尝试使用 serde 反序列化数据。在这种情况下，为了解析 XML 文件，我们将使用 `serde-xml-rs` crate。

解析 XML 文件的代码将位于同一个二进制包 `data_formats` 中，即我们在之前的 JSON 模式中创建的那个。这应该能让我们意识到，XML 和 JSON 本质上都是层次化的数据格式，因此在编写 Rust 解析代码时，它们将具有相似的代码结构。唯一的区别在于底层的依赖项。本章剩余部分的代码也将体现类似的动机。因此，我们将在同一个 `data_formats` 二进制包中更新 `Cargo.toml` 文件（清单 4-10）。请注意，除了之前见过的 crate 之外，我们还添加了 `serde-xml-rs` crate 用于解析 XML 文件。

```
[dependencies]
serde = "1.0.90"
serde_derive = "1.0.90"
serde_json = "1.0"
serde-xml-rs = "0.3.1" // 添加 xml 依赖项
清单 4-10
`chapter4/working_with_data/data_formats/Cargo.toml`
```

我们将为 `sample_2.xml` 文件创建一个 Rust 模型。该文件可以在 `chapter4/data/sample_2.xml` 中找到。文件结构类似于清单 4-11 所示。

```
project
libraries
library
module
files
file
libraries
library
module
files
file
清单 4-11
可能的文件结构
```

打开文件并注意上述结构。因此，我们将继续创建捕获 XML 文件中上述结构的结构体（清单 4-12）。

```
use serde_xml_rs;
use serde_xml_rs::from_reader;
use serde_xml_rs::Deserializer;
#[derive(Deserialize, Debug)]
struct Project {
name: String,
libraries: Vec,
module: Vec,
}
#[derive(Deserialize, Debug)]
struct Module {
files: Vec,
#[serde(default)]
libraries: Vec,
}
#[derive(Deserialize, Debug)]
struct Files {
file: Vec,
}
#[derive(Deserialize, Debug)]
struct FileName {
name: String,
#[serde(rename = "type")]
lang: String,
#[serde(rename = "$value")]
body: String,
}
#[derive(Deserialize, Debug)]
struct Libraries {
library: Vec,
}
#[derive(Deserialize, Debug)]
struct Library {
#[serde(rename = "groupId")]
group_id: String,
#[serde(rename = "artifactId")]
artifact_id: String,
version: String,
}
清单 4-12
`chapter4/working_with_data/data_formats/src/xmlreading.rs`
```

在一个小的技术细节上，在结构体 `FileName` 中，我们不能使用 `type` 作为属性名，需要将其从 `lang` 重命名为 `type`，因为 `type` 是 Rust 中的关键字。其他的重命名是出于美观原因。另外，`#[serde(rename = "$value")]` 有助于获取特定字段的值。所有其他构造应该是不言自明的，因为这类似于在 csv 和 json 中反序列化的方式。

现在，我们应该能够将文件传递给反序列化器，这将创建我们的 `project` 变量（清单 4-13）。

```
pub fn run() -> Result> {
let file = File::open("data/sample_2.xml").unwrap();
let project: Project = from_reader(file).unwrap();
println!("{:#?}", project.libraries[0].library[0]);
Ok(())
}
清单 4-13
`chapter4/working_with_data/data_formats/src/xmlreading.rs`
```

与我们在 JSON 部分看到的情况类似，我们需要在 `main.rs` 文件中添加模块，并添加一个参数，以便能够执行 `xmlreading` 模块中的 `run` 函数。因此，我们将在 `main` 方法中添加以下几行代码（清单 4-14）。

```
// 之前的导入
mod jsonreading;
mod xmlreading;
fn main() {
// 之前的代码 ...
let res = match model {
None => {println!("无内容", ); Ok(())},
Some("json") => jsonreading::run(),
Some("xml") => xmlreading::run(),
Some(_) => {println!(
"目前仅允许 json 和 xml 格式", ); Ok(())},
};
// 代码的剩余部分 ...
}
代码清单 4-14
`chapter4/working_with_data/data_formats/src/main.rs`
```

请注意代码清单 4-12 中强类型构造的优势。这本质上是一种免费的数据验证：只需编写一次，所有数据问题便迎刃而解。此外，我们还能像操作代码一样高效地遍历数据。例如，类似下面的操作变得可行。^(¹³)

```rust
pub fn run() -> Result<()> {
    // 之前的代码 ...
    println!("{:#?}", project.libraries[0].library[0]);
}
```

运行 `xmlreading::run` 函数并查看上述打印语句的输出，我们应该会得到代码清单 4-15 所示的结果。

```
$ cargo run xml
    Finished dev [unoptimized + debuginfo] target(s) in 0.03s
     Running `target/debug/data_formats xml`
Library {
    group_id: "org.example",
    artifact_id: "",
    version: "0.1",
}
代码清单 4-15
xmlreading::run 输出
```

在本书附带的代码中，为完整起见，也保留了 csv 代码。该代码可从本书网站 [`apress.com`](http://apress.com) 下载。

## 4.3 网页抓取

到目前为止，我们已经了解了如何读取 csv、json 和 xml 文件格式。尽管这些数据格式在存储和共享结构化数据方面非常流行，但有时数据可能根本不以结构化格式存在，我们需要从不同来源收集数据并将其整合在一起。实现这一目标的方法之一就是网页抓取。网页抓取是指筛选互联网上公开可用的数据，并将数据传递给后续处理流程。要通过 Rust 进行网页抓取，我们需要两个 crate 作为依赖项，即 `reqwest` 和 `scraper`。

为了探索执行网页抓取所需的代码，我们现在将创建一个名为 `scraping` 的新包（代码清单 4-16）。

```
$ cd chapter4 && cargo new scraping –bin
代码清单 4-16
创建 scraping 包
```

现在，我们可以在 `Cargo.toml` 文件中添加 `reqwest` 和 `scraper` 依赖项（代码清单 4-17）。

```toml
[package]
name = "scraping"
version = "0.1.0"
edition = "2018"

[dependencies]
reqwest = "0.9.15"
scraper = "0.10.0"
代码清单 4-17
chapter4/working_with_data/scraping/Cargo.toml
```

`reqwest` 包是一个便捷的高级 Web 客户端，我们将用它来访问特定的网页；而 `scraper` 将用于使用 CSS 选择器解析和查询 HTML 页面。

作为示例，我们将以 [`moneycontrol.com`](http://moneycontrol.com) 网站为例。该网站是一个印度在线商业新闻网站。需要牢记的重要一点是，该网站还发布在 BSE 和 NSE 上市的不同上市公司的定期股票价格。BSE 和 NSE 是印度的两大主要证券交易所。

现在考虑一个假设场景：我们想要构建一个关于某家特定印度公司（即 NTPC）的时间线和价格的数据集。NTPC 是一家由印度政府拥有的火电公司，并在 sensex 指数中上市。我们可以在 moneycontrol 网站的这个 money control NTPC 链接中找到 NTPC 股票的当前价格：[`https://www.moneycontrol.com/india/stockpricequote/power-generation-distribution/ntpc/NTP`](https://www.moneycontrol.com/india/stockpricequote/power-generation-distribution/ntpc/NTP)。

![../images/482357_1_En_4_Chapter/482357_1_En_4_Fig1_HTML.jpg](img/482357_1_En_4_Fig1_HTML.jpg)

图 4-1 Moneycontrol

如图 4-1 所示，列出了 BSE 和 NSE 的价格。要获取数据，我们需要获取页面的响应。这可以使用 `reqwest` API 来完成（代码清单 4-18）。

```rust
use reqwest;

fn main() -> Result<()> {
    let mut resp = reqwest::get(
        "https://www.moneycontrol.com/india/stockpricequote/power-generation-distribution/ntpc/NTP"
    )?;
    assert!(resp.status().is_success());
    // 剩余代码 ...
}
代码清单 4-18
chapter4/working_with/data/scraping/src/main.rs
```

这部分代码会将响应存储在 `resp` 变量中。完成后，我们需要解析 HTML，选择特定的目标数据，然后将其收集到一个变量中。如代码清单 4-19 所示。

```rust
use scraper::{Selector, Html};

fn main() -> Result<()> {
    // 之前的代码 ...
    let body = resp.text().unwrap();
    let fragment = Html::parse_document(&body);
    let stories = Selector::parse("#Bse_Prc_tick > strong:nth-child(1)").unwrap();
    for price in fragment.select(&stories) {
        let price_txt = price.text().collect::<Vec<_>>();
        // 其余代码 ...
        // 剩余代码 ...
    }
}
代码清单 4-19
chapter4/working_with_data/scraping/src/main.rs
```

传入 `Selector::parse` 方法的字符串是 CSS 选择器。我们可以使用 Chrome 开发者工具获取合适的选择器。右键单击特定项目（在本例中为 BSE 价格），然后选择“检查”。这应该会打开 Chrome 开发者工具。打开工具后，复制选择器。请查看图 4-2。

复制选择器后，您应该会得到选择器字符串，它是该价格在网站中的标识符。应用程序几乎完成了。我们现在可以使用标准库中的时间模块来获取捕获价格时的时间快照，然后将其打印到标准输出。然后，我们可能可以在 cron 作业或其他调度系统中运行此应用程序。这样，我们就得到了股票时间和价格的时间序列（代码清单 4-20）。

```rust
use std::time::{SystemTime, UNIX_EPOCH};

fn main() -> Result<()> {
    let start = SystemTime::now();
    let since_the_epoch = start.duration_since(UNIX_EPOCH)
        .expect("时间倒流");
    // 抓取代码 ...
    for price in fragment.select(&stories) {
        let price_txt = price.text().collect::<Vec<_>>();
        if price_txt.len() == 1 {
            println!("{:?}", (since_the_epoch, price_txt[0]));
        }
    }
    Ok(())
}
代码清单 4-20
chapter4/working_with_data/scraping/src/main.rs
```

![../images/482357_1_En_4_Chapter/482357_1_En_4_Fig2_HTML.jpg](img/482357_1_En_4_Fig2_HTML.jpg)

图 4-2 Chrome 选择器

代码清单 4-20 的输出应类似于代码清单 4-21 中的内容。

```
$ cargo run
    Finished dev [unoptimized + debuginfo] target(s) in 0.60s
     Running `target/debug/scraping`
(1566103337.390445s, "1.05")
代码清单 4-21
抓取输出
```

## 4.4 SQL

## 使用 PostgreSQL 和 Diesel 与 SQL 数据库交互

大量数据存储在 SQL 数据库中，因此要在这些数据上运行机器学习算法，我们需要一种与这些数据库通信并从中聚合数据的方法。在本节中，我们将了解 `postgres` 数据库，并尝试加载、访问以及在 `postgres` 数据库上运行 SQL 查询。为此，我们将使用 `postgres` Rust crate，顾名思义，它是用于 `postgres` 的 Rust 原生 crate。除了编写原生 SQL 查询之外，另一种流行的查询数据库方法是使用 ORM。Rust 也有一个流行的 ORM crate，名为 `diesel`，但我们不会讨论如何使用 ORM。这是因为 ORM 通常最适合于 Web 应用程序，在这些应用程序中，开发人员执行的查询类型相当一致且可预测。然而，在机器学习应用中，我们可能需要编写复杂的查询，在这种情况下，使用 ORM 编写查询可能会变得过于复杂。因此，我们将坚持使用 SQL 编写原生查询。

本 SQL 部分的代码也将位于一个名为 `databases` 的独立包中（清单 4-22）。

```bash
$ cd chapter4 && cargo new databases --bin
```

清单 4-22: 创建二进制包 `databases`

现在，在编写任何代码之前，我们需要一个 `postgres` 数据库。有多种创建数据库的方法。我们可以安装 `postgres`，然后使用客户端访问数据库。但可能更简单的方法是使用 `postgres` docker。Docker 是一个容器化平台，用于将软件及其底层依赖项打包到 docker 容器中，以便它在任何环境中都能无缝运行。要使用 docker，我们需要先安装 docker。你可以通过此链接查看安装 docker 的方法：[`https://docs.docker.com/v17.12/install/#supported-platforms`](https://docs.docker.com/v17.12/install/%2523supported-platforms)。安装好 docker 后，可以执行清单 4-23 中的命令来启动一个正在运行的 `postgres` 容器。在单独的终端中执行该命令。

```bash
sudo docker run \
--name rust-postgres \
-e POSTGRES_PASSWORD=postgres -e POSTGRES_USER=postgres \
-p 5432:5432 -d \
postgres
```

清单 4-23: docker postgres

上述命令将运行一个名为 `rust-postgres` 的 `postgres` 容器，用户名为 `postgres`，密码为 `postgres`。Postgres 运行在端口 `5432` 上，因此该端口将映射到主机的 `5432` 端口。`-d` 命令表示以守护进程模式运行此容器，换句话说，即作为后台进程运行。

现在我们可以开始更新 `databases` 包了。作为示例，我们将从此网址输入数据：[`https://www.yr.no/place/India/Karnataka/Bangalore/statistics.html`](https://www.yr.no/place/India/Karnataka/Bangalore/statistics.html)。因此，我们将在另一个模块 `postgres_db.rs` 中创建一个包含数据的元组向量（清单 4-24）。

```rust
pub fn run() -> Result<(), Box<dyn std::error::Error>> {
    let weathers = vec![
        ("January", 21.3, 27.3, 15.1),
        ("February", 23.6, 30.1, 17.0),
        ("March", 26.1, 32.7, 19.5),
        ("April", 28.0, 34.2, 21.8)
    ];
    // 代码的其余部分 ...
}
```

清单 4-24: `chapter4/databases/src/postgres_db.rs`

为了输入数据，我们首先需要在依赖项中包含 `postgres` crate（清单 4-25）。

```toml
[package]
name = "databases"
version = "0.1.0"
edition = "2018"
[dependencies]
postgres = "0.15.2"
```

清单 4-25: `chapter4/working_with_data/databases/Cargo.toml`

然后，我们可以使用连接字符串创建到 `postgres` 的连接，该字符串的格式必须为 “postgresql://user:password@uri:port/databasename”。建立连接后，我们将创建表（清单 4-26）。

```rust
use postgres;
use postgres::{Connection, TlsMode}; // 所有依赖项
pub fn run() -> Result<(), Box<dyn std::error::Error>> {
    let conn = Connection::connect(
        "postgresql://postgres:postgres@localhost:5432/postgres",
        TlsMode::None)?; // 创建连接
    conn.execute("CREATE TABLE IF NOT EXISTS weather (
        id              SERIAL PRIMARY KEY,
        month           VARCHAR NOT NULL,
        normal          DOUBLE PRECISION NOT NULL,
        warmest         DOUBLE PRECISION NOT NULL,
        coldest         DOUBLE PRECISION NOT NULL
    )", &[])?; // 创建表
    // 剩余代码 ...
}
```

清单 4-26: `chapter4/databases/src/postgres_db.rs`

代码的下一部分（清单 4-27）是将数据从向量插入到表中。

```rust
pub fn run() -> Result<(), Box<dyn std::error::Error>> {
    // 之前的代码 ...
    for weather in &weathers {
        conn.execute("INSERT INTO weather \
            (month, normal, warmest, coldest) \
            VALUES ($1, $2, $3, $4)",
            &[&weather.0, &weather.1, &weather.2, &weather.3])?;
    }
    // 剩余代码 ...
}
```

清单 4-27: `chapter4/databases/src/postgres_db.rs`

在这个例子中，我们同样将 SQL 和 NoSQL 等部分合并在一起，以便可以像之前在 JSON 和 XML 部分看到的那样，对类似的代码功能进行比较。因此，与前面的部分类似，我们需要一个 `main` 方法，在其中暴露 `postgres_db` 模块并执行 `run` 函数（清单 4-28）。

```rust
use std::vec::Vec;
use std::process::exit;
use std::env::args;
mod postgres_db;
fn main() {
    let args: Vec<String> = args().collect();
    let model = if args.len() < 2 {
        println!("nothing");
        Ok(())
    } else {
        match args[1].as_str() {
            Some("postgres") => postgres_db::run(),
            Some(_) => {
                println!("Only postgres allowed for now.");
                Ok(())
            }
        }
    };
    exit(match res {
        Ok(_) => 0,
        Err(e) => {
            println!("{}", e);
            1
        }
    })
}
```

清单 4-28: `chapter4/databases/src/main.rs`

现在，我们应该能够通过运行命令 `cargo run postgres` 来执行相关的表创建 SQL 查询，从而创建该表。

现在可以从数据库中检索数据了。只需执行到目前为止编写的代码，数据就应该已经存在于数据库中。在另一个终端中，打开数据库并运行一个 `select` 查询（清单 4-29）。

## 4.4 使用 PostgreSQL 和 Neo4J 进行数据库编程
```
$ sudo docker exec -it rust-postgres psql --username postgres
psql (11.2 (Debian 11.2-1.pgdg90+1))
Type "help" for help.
postgres=# \dt;
List of relations
Schema |  Name   | Type  |  Owner
--------+---------+-------+----------
public | weather | table | postgres
(1 row)
postgres=# select ∗ from weather;
id |   month   | normal | warmest | coldest
----+-----------+--------+---------+---------
1 | January   |   21.3 |    27.3 |    15.1
2 | February  |   23.6 |    30.1 |      17
.. and the remaining data
(12 rows)
```

清单 4-29 postgres 查询

为了从数据库中检索值，我们可以创建一个具有数据结构的结构体（清单 4-30）。

```rust
#[derive(Debug)]
struct Weather {
    id: i32, month: String,
    normal: f64, warmest: f64,
    coldest: f64
}
```

清单 4-30 `chapter4/databases/src/postgres_db.rs`

现在，我们可以使用这个结构体来保存通过 `select` 查询从数据库中检索到的数据，然后将其用于各种目的（清单 4-31）。

```rust
pub fn run() -> Result<()> {
    // 之前的代码 ...
    for row in &conn.query(
        "SELECT id, month, normal, warmest, coldest \
        FROM weather", &[])? {
        let weather = Weather {
            id: row.get(0),
            month: row.get(1),
            normal: row.get(2),
            warmest: row.get(3),
            coldest: row.get(4)
        };
        println!("{:?}", weather);
    }
    // 剩余代码 ...
}
```

清单 4-31 `chapter4/databases/src/postgres_db.rs`

当我们在包根目录下执行 `cargo run` 时，应该会在控制台中看到打印出的所有值。^(¹⁴)

为了完成 SQL 这一节的内容，我们将对最热列求平均值，以找出班加罗尔气温中最高的温度。SQL 在数据科学中的主要优势在于，它允许我们不将数据拉取到代码中，而是将代码发送到数据所在处。这可以通过将业务逻辑编写为 SQL 查询，并将查询发送到数据库执行来实现（清单 4-32）。

```rust
pub fn run() -> Result<()> {
    // 之前的代码 ...
    for row in &conn.query(
        "SELECT AVG(warmest) FROM weather;", &[])? {
        let x: f64 = row.get(0);
        println!("{:?}", x);  // 对于此处输入的数据，输出 31.075
    }
}
```

清单 4-32 `chapter4/databases/src/postgres_db.rs`

## 4.5 NoSQL

尽管 SQL 数据库非常流行，但它们的主要缺点在于难以扩展。查询依赖索引来理解不同表之间的关系。因此，每次更新表时，都需要重新计算所有内容。这给数据库的更新量带来了真正的瓶颈。如果目标是快速增长的数据接收端，SQL 数据库可能无法胜任。作为解决方案，NoSQL 应运而生。虽然 NoSQL 数据库有多种类型，但在本节中，我们将介绍 Neo4J，它属于图数据库类别。

在图数据库中，会创建节点和边，从而发现数据之间有趣的关系。这些节点和边在命名空间中定义。为了理解其工作原理，我们将使用 MovieLens 数据集^(¹⁵)，并根据它们创建关系。下载 `ml-latest-small.zip` 文件并将其解压到目录中。对于本节中的代码，我们需要通过 HTTP 暴露这些文件，我们可以使用一个方便的 Python 命令来实现。打开终端，`cd` 到 `ml-latest-small` 目录，并运行清单 4-33 中所示的命令。这样，将在 Docker 容器中启动的 Neo4j 就能够获取这些文件。如你所愿，此命令依赖于计算机上已安装的 `python3`。另一个创建简单 HTTP 服务器的简便方法是使用 npm 的 `http-server`^(¹⁶)。请注意，要将其暴露在端口 8000 上。该命令应在包含 MovieLens 目录的文件夹内运行。

```
$ python3 -m http.server
Serving HTTP on 0.0.0.0 port 8000 (http://0.0.0.0:8000/) ...
```

清单 4-33 Bash

要启动 Neo4j 数据库，我们在终端中运行以下 Docker 命令（清单 4-34）。这将启动最新的 Neo4j 容器。

```
sudo docker run \
    --name rust-neo4j \
    --rm --env=NEO4J_AUTH=none \
    --publish=7474:7474 \
    --publish=7687:7687 \
    --volume=$HOME/neo4j/data:/data neo4j:3.5.8
```

清单 4-34 Bash

现在，我们将向 `databases` 包添加与 Neo4j 通信的能力。我们需要在依赖项中添加一个 `rusted-cypher` crate（清单 4-35）。

```toml
[package]
name = "databases"
version = "0.1.0"
edition = "2018"
[dependencies]
postgres = "0.15.2"
rusted_cypher = "1.1.0" // 添加 neo4j 依赖
```

清单 4-35 `chapter4/databases/Cargo.toml`

现在，我们应该能够在代码中连接到数据库。与 SQL 部分类似，需要传递连接字符串，其格式为 `http://username:password@uri:port/db/data`。在我们的例子中，Neo4j 是在无认证的情况下启动的，因此不需要用户名和密码（清单 4-36）。

```rust
use rusted_cypher;
use rusted_cypher::{GraphClient, Statement, GraphError};
use std::iter::repeat;

fn main() -> Result<(), GraphError> {
    let graph = GraphClient::connect(
        "http://localhost:7474/db/data")?;
    // 其余代码..
```

清单 4-36 `chapter4/databases/src/neo4j_db.rs`

要加载电影数据，我们首先需要创建命名空间（清单 4-37）。

```rust
fn main() -> Result<(), GraphError> {
    // 之前的代码 ...
    let mut query = graph.query();
    let statement1 = Statement::new(
        "CREATE CONSTRAINT ON (m:Movie) ASSERT m.id IS UNIQUE;");
    let statement2 = Statement::new(
        " CREATE CONSTRAINT ON (u:User) ASSERT u.id IS UNIQUE;"
    );
    let statement3 = Statement::new(
        " CREATE CONSTRAINT ON (g:Genre) ASSERT g.name IS UNIQUE;"
    );
    query.add_statement(statement1);
    query.add_statement(statement2);
    query.add_statement(statement3);
    query.send()?;
    // 剩余代码 ...
}
```

清单 4-37 `chapter4/databases/src/neo4j_db.rs`

这将创建命名空间。现在，我们应该能够从文件中拉取数据了（清单 4-38）。

```
fn main() -> Result<(), Box<dyn Error>> {
    // 导入 movies.csv
    graph.exec(
        "USING PERIODIC COMMIT LOAD CSV WITH HEADERS \
         FROM \"http://10.0.1.43:8000/movies.csv\" AS line \
         WITH line, SPLIT(line.genres, \"|\") AS Genres \
         CREATE (m:Movie { id: TOINTEGER(line.`movieId`), title: line.`title` }) \
         WITH Genres \
         UNWIND RANGE(0, SIZE(Genres)-1) as i \
         MERGE (g:Genre {name: UPPER(Genres[i])}) \
         CREATE (m)-[r:GENRE {position:i+1}]->(g);"
    )?;
    // 导入 ratings.csv
    graph.exec(
        " USING PERIODIC COMMIT LOAD CSV WITH HEADERS \
         FROM \"http://10.0.1.43:8000/ratings.csv\" AS line \
         WITH line \
         MATCH (m:Movie { id: TOINTEGER(line.`movieId`) }) \
         MATCH (u:User { id: TOINTEGER(line.`userId`) }) \
         CREATE (u)-[r:RATING {rating: TOFLOAT(line.`rating`)}]->(m);"
    )?;
    // 导入 tags
    graph.exec(
        " USING PERIODIC COMMIT LOAD CSV WITH HEADERS \
         FROM \"http://10.0.1.43:8000/tags.csv\" AS line \
         WITH line \
         MATCH (m:Movie { id: TOINTEGER(line.`movieId`) }) \
         MERGE (u:User { id: TOINTEGER(line.`userId`) }) \
         CREATE (u)-[r:TAG {tag: line.`tag`}]->(m);"
    )?;
    // 剩余代码 ...
}
```
清单 4-38 `chapter4/databases/src/neo4j_db.rs`

用于获取数据的 `10.0.1.43` IP 地址是机器的 IP 地址，在你的情况下，这个 IP 会不同。在 Ubuntu 中，可以通过运行命令 `hostname -I` 查看 IP 地址。在 Mac 中，相应的命令是 `ipconfig getifaddr en0`。其他操作系统可以找到类似的命令。

到目前为止，代码应该已将数据存入数据库，并且我们应该能够在 Neo4j 控制台中看到图。对于电影，这可能类似于图 4-3 所示的内容。

![../images/482357_1_En_4_Chapter/482357_1_En_4_Fig3_HTML.jpg](img/482357_1_En_4_Fig3_HTML.jpg)

图 4-3 电影节点

要从图数据库中检索数据或运行查询，我们只需将在控制台中有效的 Cypher 查询传递给 `graph.exec` 方法，该方法将返回相关结果（清单 4-39）。

```rust
pub fn run() -> Result<(), Box<dyn Error>> {
    // 之前的代码 ...
    let result = graph.exec(
        "MATCH (u:User {id: 119}) RETURN u.id"
    )?;
    for row in result.rows() {
        let id: u16 = row.get("u.id")?;
        println!("user id: {}", id);
    }
    // 剩余代码 ...
}
```
清单 4-39 `chapter4/databases/src/neo4j_db.rs`

然后，可以使用图上的关系学习来增强数据 [1]。

## 4.6 在 S3 上存储数据

机器学习模型，尤其是深度学习模型，需要海量数据才能有效训练并创建出具有可用精度的模型。存储如此大量的数据可能是一个挑战，因为我们可能需要拥有数 TB 的磁盘空间，或者投入资源来采购和配置这么大的空间。云存储解决方案使我们能够规避这个问题。亚马逊和 Azure 等各类云提供商都提供低成本的存储解决方案，承诺在互联网上公开或私下存储和管理我们的数据。

Amazon S3 是这方面最流行的解决方案之一。要从 Rust 执行 S3 操作，一个流行的 crate 是 `rusoto`。^(¹⁷) Rusoto 是一个集成项目，为每个 AWS 服务都提供了一个 crate。要使用 S3，我们将使用 `rusoto-s3` crate。

为了探索与 Amazon S3 配合使用的代码，我们将创建一个名为 `s3_files` 的二进制包（清单 4-40）。

```bash
$ cd chapter4 && cargo new s3_files --bin
```
清单 4-40 创建二进制包 `s3_files`

现在应该已经创建了 `s3_files` 文件夹，其中包含 `src/main.rs` 和 `Cargo.toml` 文件。我们现在可以将 S3 依赖项（`rusoto_s3` 和 `rusoto_core` crate）添加到 toml 文件中（清单 4-41）。我们还有一些额外的 crate，例如提供良好日志配置管理的 `env_logger`，以及提供高效文件管理良好类型的 `futures` 和 `futures-fs`。我们之前也见过 `rustlearn` crate、`rand` crate、`csv` crate 和 `ml-utils` crate。

```toml
[package]
name = "s3_files"
version = "0.1.0"
edition = "2018"
[dependencies]
rusoto_s3 = "0.38.0"
rusoto_core = "0.38.0"
env_logger = "0.6.1"
futures = "0.1.26"
futures-fs = "0.0.5"
rand = "0.6.5"
csv = "1.0.7"
ml-utils = { path = "../../ml-utils" }
rustlearn = "0.5.0"
```
清单 4-41 `chapter4/s3_files/Cargo.toml`

现在我们应该能够编写一个函数，该函数接收客户端、存储桶和文件名，并将其放入 S3（清单 4-42）。

```rust
use futures::{Future, Stream};
use rusoto_core;
use rusoto_core::credential::{
    AwsCredentials, DefaultCredentialsProvider};
use rusoto_core::{Region, ProvideAwsCredentials, RusotoError};
use rusoto_s3::{
    CreateBucketRequest, DeleteBucketRequest,
    DeleteObjectRequest, GetObjectRequest, ListObjectsV2Request,
    PutObjectRequest, S3Client, S3,
};

fn push_file_to_s3(
    client: &S3Client, bucket: &str,
    dest_filename: &str, local_filename: &str,
) {
    let mut f = File::open(local_filename).unwrap();
    let mut contents: Vec<u8> = Vec::new();
    match f.read_to_end(&mut contents) {
        Err(why) => panic!("Error opening file to send to S3: {}", why),
        Ok(_) => {
            let req = PutObjectRequest {
                bucket: bucket.to_owned(),
                key: dest_filename.to_owned(),
                body: Some(contents.into()),
                ..Default::default()
            };
            client.put_object(req).sync().expect("Couldn't PUT object");
        }
    }
}
```
清单 4-42 `chapter4/s3_files/src/main.rs`

清单 4-42 中的函数接收一个由 `local_filename` 指定的文件，并将其放入 S3，其中 `dest_filename` 是键。要能够运行此函数，我们需要一个 S3 客户端（清单 4-43）。

# 与 AWS S3 交互并使用 Datafusion

## 使用 `rusoto` 与 S3 交互

现在我们应该能够将客户端传递给 `push_file_to_s3` 函数（清单 4-44）。

```rust
fn main() -> Result<(), Box<dyn Error>> {
    // previous code ...
    let s3_bucket = format!("rust-ml-bucket");
    let filename = format!("iris.csv");
    push_file_to_s3(&client, &s3_bucket, &filename, "data/iris.csv");
    // remaining code ...
}
```
> 清单 4-44
> `chapter4/s3_files/src/main.rs`

到目前为止运行代码应该会将文件推送到 S3 存储桶，并且我们应该能够通过控制台或 AWS 命令行在 S3 中看到该文件（清单 4-45）。^(¹⁸)

```bash
$ aws s3 ls s3://rust-ml-bucket/
2019-04-21 09:28:06       3858 iris.csv
```
> 清单 4-45
> Bash

现在文件已在 S3 中，我们应该能够从 S3 拉取该文件。因此，我们创建一个 `GetObjectRequest`，客户端可以使用它从 S3 拉取数据。数据将是一个 `u8` 字符向量，然后我们可以将其转换为字符串。一旦字符串正确形成，我们就可以返回该字符串。参见清单 4-46。

```rust
use rusoto_s3::GetObjectRequest;
fn pull_object_from_s3(
    client: &S3Client,
    bucket: &str,
    filename: &str,
) -> Result<String, RusotoError<GetObjectError>> {
    let get_req = GetObjectRequest {
        bucket: bucket.to_owned(),
        key: filename.to_owned(),
        ..Default::default()
    };
    let result = client.get_object(get_req).sync().expect("Couldn't GET object");
    println!("get object result: {:#?}", result);
    let stream = result.body.unwrap();
    let body = stream.concat2().wait().unwrap();
    Ok(str::from_utf8(&body).unwrap().to_owned())
}
```
> 清单 4-46
> `chapter4/s3_files/src/main.rs`

现在我们应该能够从 S3 拉取 CSV 文件，并将其加载到 `Flower` 结构体的数组中，类似于我们在第 2 章中看到的那样（清单 4-47）。

```rust
use csv;
use ml_utils::datasets::Flower;
fn main() -> Result<(), Box<dyn Error>> {
    // previous code ...
    let data = pull_object_from_s3(&client, &s3_bucket, &filename)?;
    let mut rdr = csv::Reader::from_reader(data.as_bytes());
    let mut data = Vec::new();
    for result in rdr.deserialize() {
        let r: Flower = result?;
        data.push(r);
    }
    // any of the machine learning code
    // that we have seen in chapter 2.
}
```
> 清单 4-47
> `chapter4/s3_files/src/main.rs`

现在，我们应该能够使用我们在第 2 章中看到的任何机器学习算法来构建任何机器学习结构。除此之外，我们还可以在我们的 S3 应用程序中使用其他结构。创建这些函数的示例可以在 `rusoto` GitHub 仓库中找到。

## 4.7 数据转换

在 Rust 中编写数据转换代码相当直观，因为这通常涉及为数据结构编写结构体，然后为这些结构体实现方法。我们在第 2 章中为不同的结构体（例如 `Flower` 结构体和 `BostonHousing` 结构体）创建了类似的方法。此外，我们在本章开头展示了如何从不同的数据存储反序列化为结构体。尽管这种方法即使对于大型数据集也有效，但如果数据集非常庞大，开发者可能难以为其编写高效的结构体。

在 Rust 生态系统之外，还有 Apache Arrow 生态系统^(¹⁹)，它专门基于数据的列式内存布局系统，允许以 O(1) 的时间复杂度访问数据。Rust 开发者可以利用 `datafusion` crate 来利用 Arrow 生态系统的发展成果。使用 `datafusion`，我们可以编写 SQL 查询来对数据执行转换。缺点是，截至撰写本文时，`datafusion` 库仍需要大量功能才能用于大型应用程序。

为了初步了解如何使用 `datafusion` 解析数据集，我们将使用泰坦尼克号数据集。我们可以从 kaggle 网站下载该数据集：[`https://www.kaggle.com/c/titanic`](https://www.kaggle.com/c/titanic)。

现在，我们将使用 cargo 创建 `data_transformations_datafusion` 包（列表 4-48）。

```bash
$ cd chapter4 && cargo new data_transformations_datafusion --bin
```
> 列表 4-48
> 创建二进制包 `data_transformations_datafusion`

这将创建 `src/main.rs` 和 `Cargo.toml` 文件。我们将在 `Cargo.toml` 中添加 `datafusion` 和 `arrow` 依赖项（列表 4-49）。

```toml
[package]
name = "data_transformations_datafusion"
version = "0.1.0"
edition = "2018"
[dependencies]
datafusion = "0.13.0"
arrow = "0.13.0"
```
> 列表 4-49
> `chapter4/data_transformations_datafusion/Cargo.toml`

为了能够通过 Arrow 运行计算，我们需要一个执行上下文和一个模式 arc。请查看列表 4-50 中所示的代码来创建这些对象。

## 初始化 `use` 与 `main` 函数

```rust
use std::env;
use std::fs::File;
use std::io::Read;
use std::str;
use std::vec::Vec;
use std::error::Error;
use env_logger;
use rusoto_core::{Region, ProvideAwsCredentials, RusotoError};
fn main() -> Result<(), Box<dyn Error>> {
    let _ = env_logger::try_init();
    let region = if let Ok(endpoint) = env::var("S3_ENDPOINT") {
        let region = Region::Custom {
            // name: "us-east-1".to_owned(),
            name: "ap-south-1".to_owned(),
            endpoint: endpoint.to_owned(),
        };
        println!(
            "picked up non-standard endpoint {:?} from S3_ENDPOINT env. variable",
            region
        );
        region
    } else {
        // Region::UsEast1
        Region::ApSouth1
    };
    let credentials = DefaultCredentialsProvider::new()
        .unwrap().credentials()
        .wait().unwrap();
    let client = S3Client::new(region.clone());
    // rest of the code...
```
> 清单 4-43
> `chapter4/s3_files/src/main.rs`

```
use arrow;
use datafusion;
use arrow::array::{BinaryArray, Float64Array, UInt16Array, ListArray};
use arrow::datatypes::{DataType, Field, Schema};
use datafusion::execution::context::ExecutionContext;

fn main() {
    // 创建本地执行上下文
    let mut ctx = ExecutionContext::new();
    // 为数据源（csv 文件）定义模式
    let schema = Arc::new(Schema::new(vec![
        Field::new("PassengerId", DataType::Int32, false),
        Field::new("Survived", DataType::Int32, false),
        Field::new("Pclass", DataType::Int32, false),
        Field::new("Name", DataType::Utf8, false),
        Field::new("Sex", DataType::Utf8, false),
        Field::new("Age", DataType::Int32, true),
        Field::new("SibSp", DataType::Int32, false),
        Field::new("Parch", DataType::Int32, false),
        Field::new("Ticket", DataType::Utf8, false),
        Field::new("Fare", DataType::Float64, false),
        Field::new("Cabin", DataType::Utf8, true),
        Field::new("Embarked", DataType::Utf8, false),
    ]));
    // 向执行上下文注册 csv 文件
    ctx.register_csv("titanic", "titanic/train.csv", &schema, true);
    // 其余代码..
}
列表 4-50
`chapter4/working_with_data/data_transformations_datafusion/src/main.rs`
```

现在，我们应该能够使用 SQL 对数据运行查询。在本例中，我们将找出幸存乘客支付的最高票价（列表 4-51）。

```
fn main() {
    // 之前的代码 ...
    let sql = "SELECT MAX(Fare) FROM titanic WHERE Survived = 1";
    // 其余代码 ...
}
```

列表 4-51
`chapter4/working_with_data/data_transformations_datafusion/src/main.rs`

然后，我们将执行查询，并传递每批次的记录数（列表 4-52）。

```
fn main() {
    // 之前的代码 ...
    let relation = ctx.sql(&sql, 1024 * 1024).unwrap();
    // 其余代码 ...
}
```

列表 4-52
`chapter4/working_with_data/data_transformations_datafusion/src/main.rs`

现在，我们需要遍历所有批次并收集结果（列表 4-53）。

```
fn main() {
    // 之前的代码 ...
    // 显示关系
    let mut results = relation.borrow_mut();
    while let Some(batch) = results.next().unwrap() {
        println!("RecordBatch 有 {} 行和 {} 列", batch.num_rows(), batch.num_columns());
        let name = batch.column(0).as_any().downcast_ref::<Float64Array>().unwrap();
        for i in 0..batch.num_rows() {
            let name_value: f64 = name.value(i);
            println!("name: {}", name_value);
        }
    }
}
```

列表 4-53
`chapter4/working_with_data/data_transformations_datafusion/src/main.rs`

目前，在聚合函数方面，只实现了有限数量的函数。更多 SQL 功能，如`JOIN`、`ORDER BY`和`LIMIT`，尚待实现。另一个需要完善的重要功能是构建类似于 Apache Spark^(²⁰) 的数据帧 API。即便如此，我们现在应该能够利用现有功能实现许多常见的业务逻辑。

## 4.8 使用矩阵

在本节中，我们将探索一种基础而优雅的数据结构——矩阵，以及它的近亲张量。矩阵在机器学习领域具有极其重要的地位，因为几乎所有操作和“学习”过程都涉及矩阵乘法。

简单来说，矩阵是按行和列排列的矩形数据数组。矩阵支持基本的算术运算，如加法和乘法，矩阵运算的数学体系也称为线性代数。最流行的线性代数 crate 是`ndarray`，我们将使用`ndarray`在 Rust 中演示一些矩阵操作。为此，我们将使用`cargo`创建一个名为`matrix_transformations`的二进制包。参见代码清单 4-54。

```
$ cd chapter4 && cargo new matrix_transformations –bin
```

代码清单 4-54
创建二进制包`matrix_transformations`

现在，我们可以在`Cargo.toml`文件中添加`ndarray`依赖项（代码清单 4-55）。

```
[package]
name = "matrix_transformations"
version = "0.1.0"
edition = "2018"

[dependencies]
ndarray-rand = "0.9.0"
ndarray = "0.12.1"
```

代码清单 4-55
`chapter4/matrix_transformations/Cargo.toml`

让我们定义一个 2x3 矩阵，其数学表示如下。

![$$ {A}_{2\mathrm{x}3}=\left[\begin{array}{l}0\kern0.5em 1\kern0.5em 2\\ {}3\kern0.5em 4\kern0.5em 5\end{array}\right] $$](images/482357_1_En_4_Chapter/482357_1_En_4_Chapter_TeX_Equ1.png)

(1)

要创建与上述类似的矩阵，我们可以先创建一个 Rust 向量，然后从该向量创建矩阵。参见代码清单 4-56。

```
fn main() {
    let a1 = arr2(&[[0., 1., 2.], [3., 4., 5.]]);
    // 剩余代码 ...
}
```

代码清单 4-56
`chapter4/matrix_transformations/src/main.rs`

打印该矩阵会得到如下输出。

Finished dev [unoptimized + debuginfo] target(s) in 2.42s

Running `target/debug/matrix_transformations`

```
[[0.0, 1.0, 2.0],
 [3.0, 4.0, 5.0]] shape=[2, 3], strides=[3, 1], layout=C (0x1), const ndim=2
```

我们还可以根据便利性使用其他方法，例如`from_vec`、`from_shape_vec`和`from_iter`。因此，下面的代码清单 4-57 中的代码与上面代码清单 4-56 中的代码是等价的。

```
fn main() {
    // 之前的代码 ...
    let a2 = Array::from_shape_vec((2, 3).strides((3, 1)), vec![0., 1., 2., 3., 4., 5.]).unwrap();
    // 剩余代码 ...
}
```

代码清单 4-57
`chapter4/matrix_transformations/src/main.rs`

一种常见的矩阵变换是求矩阵的转置。矩阵转置是一种镜像操作，我们沿着对角线翻转矩阵。因此，行变成列，列变成行。许多方程中都需要矩阵转置，因此它非常重要。要在`ndarray`中求矩阵的转置，我们调用`.t()`方法（代码清单 4-58）。

```
fn main() {
    // 之前的代码 ...
    let a_T = a1.t();
    // 剩余代码 ...
}
```

代码清单 4-58
`chapter4/matrix_transformations/src/main.rs`

两个矩阵相乘类似于计算它们的点积。我们将 `a1` 与 `a_T` 相乘，它们的维度分别是 2x3 和 3x2，因此结果矩阵应为 2x2（代码清单 4-59）。

```rust
fn main() {
// 之前的代码 ...
let a_mm = a1.dot(&a_T);
println!("{:?}", a_mm.shape()); // 输出 [2, 2]
// 剩余代码 ...
}
```

`ndarray` 中还有更多可执行的操作。请查看完整列表：[`https://docs.rs/ndarray/0.11/ndarray/doc/ndarray_for_numpy_users/index.html`](https://docs.rs/ndarray/0.11/ndarray/doc/ndarray_for_numpy_users/index.html)。

## 4.9 本章小结

在本章中，我们介绍了读取数据的不同方式。我们探讨的不同数据存储方式如下：

*   我们了解了如何从不同的文件格式（如 json 和 xml）加载数据。

*   我们了解了如何通过网络爬虫从互联网上的开放资源创建自己的结构化数据。

*   我们研究了如何从 SQL 和 NoSQL 数据源拉取和存储数据。

*   我们还了解了如何使用 `datafusion` 进行大数据转换，以及使用 `ndarray` 进行矩阵变换。

接下来，我们将学习如何在更真实的机器学习领域（如计算机视觉和自然语言处理）中工作。