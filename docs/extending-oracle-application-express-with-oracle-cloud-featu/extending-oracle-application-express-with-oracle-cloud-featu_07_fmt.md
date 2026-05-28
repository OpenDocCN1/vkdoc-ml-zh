# 第 5 章 自治数据库中的 Oracle 机器学习

## 输出内容

`ai_obj`

算法名称: 属性重要性

挖掘函数: `ATTRIBUTE_IMPORTANCE`

设置:

设置名称 设置值

0 `ALGO_NAME` `ALGO_AI_MDL`

1 `ODMS_DETAILS` `ODMS_ENABLE`

2 `ODMS_MISSING_VALUE_TREATMENT` `ODMS_MISSING_VALUE_AUTO`

3 `ODMS_SAMPLING` `ODMS_SAMPLING_DISABLE`

4 `PREP_AUTO` `ON`

全局统计信息:

属性名称 属性值

0 `NUM_ROWS` 13596

属性:

`AGE`

`CREDITS`

`FIRSTNAME`

`GENDER`

`LASTNAME`

`STUDENTID`

分区: 否

重要性:

变量 重要性 排名

0 `AGE` 0.0 1

1 `CREDITS` 0.0 1

2 `FIRSTNAME` 0.0 1

3 `GENDER` 0.0 1

4 `LASTNAME` 0.0 1

5 `STUDENTID` 0.0 1

你可以为这些使用数据库内算法创建的模型指定设置，就像我们在 `OML4SQL` 部分所做的那样。如果你未定义任何设置，则会使用默认设置。其中一些设置是共享设置，例如，是否使用自动数据准备（ADP），而另一些则是算法特定的。

你还可以创建一个 `OML4Py` 模型，作为使用 `OML4Py`、`OML4SQL` 或 `OML4R` 创建的现有数据库内机器学习模型的代理对象。你可以使用模型对象的 `predict` 和 `predict_proba` 方法对新数据进行评分，或者对模型代理对象使用 `PREDICTION` SQL 函数，该函数直接在数据库中进行评分。你可以使用 `export_sermodel` 函数从 Python 导出模型，将其保存到数据库表中，然后使用 `DBMS_DATA_MINING.IMPORT_SERMODEL` 过程将其导入到另一个 Oracle 数据库。你可以使用 Oracle 机器学习服务的 REST API，例如，存储 OML 模型并为模型创建评分端点。

自动化机器学习（AutoML）也是 `OML4Py` 实现的一部分。`AutoML` 自动执行常见的建模任务，例如算法选择（`oml.automl.AlgorithmSelection`）、特征选择（`oml.automl.FeatureSelection`）、模型选择（`oml.automl.ModelSelection`）或超参数调优（`oml.automl.ModelTuning`）。`AutoML` 目前仅管理机器学习的分类和回归用例。

嵌入式 Python 执行允许你直接在 Oracle 数据库实例中调用用户定义的 Python 函数，并使用 Python 脚本存储库将它们作为脚本存储和管理在数据库中。用户定义的 Python 函数可以定义为私有或全局。用户定义的 Python 函数可以生成结构化或 PNG 图像格式的结果。

例如，你可以构建原生 Python 模型。然后，你可以将它们存储和管理在 *Python 脚本存储库* 中。

这是一个创建用户定义函数 `my_own_function` 的模拟示例：

```python
def my_own_function(data):
    import oml
    ...
    return something
```

你可以通过首先为函数定义一个字符串，然后将其保存到存储库中，从而将函数保存到 Python 脚本存储库。让我们看一个关于如何操作的模拟示例。定义字符串：

```python
my_own_function_str = """def my_own_function(data):
    import oml
    ...
    return something
"""
```

然后，将该字符串作为脚本保存到 Python 脚本存储库：

```python
oml.script.create("my_own_function", func=my_own_function_str, overwrite=True)
```

要检查存储库中的脚本：

```python
oml.script.dir()
```

你可以使用 `oml.script.load` 从存储库将脚本加载到 Python 会话中，并使用 `oml.script.drop` 从存储库中删除它们。你可以使用与授予和撤销数据存储及数据存储对象权限相同的命令来授予和撤销脚本的权限：`oml.grant` 和 `oml.revoke`。

用户定义的 Python 函数可以在单个 Python 引擎中运行，也可以使用多个 Python 引擎以数据并行或任务并行的方式运行。在数据并行处理中，你对数据进行分区，并使用一个或多个 Python 引擎对每个数据子集调用 Python 函数。在任务并行处理中，你多次调用函数，在一个或多个 Python 引擎中，并将一个唯一索引作为参数传入。

要调用用户定义的 Python 函数，你可以使用嵌入式 Python 执行函数。它们是用于嵌入式 Python 执行的 Python API 的一部分。

• `oml.do_eval`，运行用户自定义的 Python 函数

• `oml.group_apply`，根据一个或多个列中的值对数据库表进行分区，并在每个分区上运行该函数

• `oml.index_apply`，多次运行一个 Python 函数，每次向用户自定义函数传递一个唯一的调用索引。这可用于执行并行模拟，例如蒙特卡洛分析。

• `oml.row_apply`，将数据库表分块（行集），并在每个块的数据上运行用户自定义函数

• `oml.table_apply`，将数据库中的数据作为一个单一的 `pandas.DataFrame` 运行 Python 函数

你可以使用新的数据集（`newdata`）调用我们的示例用户自定义函数，例如使用 `table_apply`：

```python
something = oml.table_apply(data=NEWDATA, func=my_own_function)
```

`OML4Py` 和嵌入式 Python 执行拥有数据字典视图（`ALL_`、`DBA_`、`USER_`）：

• `ALL_PYQ_DATASTORES`，关于 `OML4Py` 数据存储的信息

• `ALL_PYQ_DATASTORE_CONTENTS`，关于 `OML4Py` 数据存储中对象的信息

• `ALL_PYQ_SCRIPTS`，脚本仓库中的用户自定义 Python 函数

有时你需要监控和管理 `OML4Py` 使用的资源。Python 库 `psutil` 可用于系统监控、性能分析、限制 Python 进程资源以及管理正在运行的进程。你可以从 `psutil` 文档（https://psutil.readthedocs.io/en/latest/#）中了解更多信息。

## 数据库内机器学习与 APEX

由于数据库内机器学习就在数据库中，因此从 APEX 使用 OML 非常容易。例如，你可以根据机器学习模型的预测创建一个新表（或视图），并在 APEX 中使用它。例如，我们的客户分为家庭客户和公司客户。我们为家庭客户（`Household_model`）和公司客户（`Company_model`）分别构建一个模型，以预测他们购买我们产品 1 的可能性。然后，我们创建一个表（`Household_contacts`），其中包含家庭客户的数据、对购买产品 1 的预测（0=不购买，1=购买）以及该预测的概率。在此示例中，我们只选取那些尚未购买产品 1（`product1 = 0`）的客户。我们使用一个模型（`Household_model`）来提供预测数据。

```sql
Create table Household_contacts as
select cust_id, PREDICTION (Household_model using *) prediction,
PREDICTION_PROBABILITY(Household_model using *) pred_probability
from household_customers where product1 = 0;
```

如果 `OML` 和 `APEX` 位于同一个数据库中，你能做的事情将无可限量。如果它们位于不同的数据库中，你可以将模型导出/导入到包含 `APEX` 的同一个数据库中，或者使用 Oracle 机器学习 REST 服务。

## 总结

# Oracle 机器学习 REST 服务

Oracle 机器学习可以与三种语言一起使用：SQL、Python 和 R。要在 Oracle 数据库中使用这些语言，你需要调用它们的 OML API：`OML4SQL`、`OML4Py` 和 `OML4R`。`OML4SQL` 包含用于机器学习的数据库包。`OML4Py` 可用于使用 Python 语法和库进行机器学习过程、使用用户自定义函数进行嵌入式 Python 执行，或使用 AutoML 功能。你可以将 Python 对象和数据存储到 Python 数据存储中，并将用户自定义函数存储到 Python 脚本仓库中。在 `OML4Py` 数据存储和 Python 脚本仓库中，你可以对它们进行管理和使用。`OML4R` 在 Oracle 自治数据库中尚不可用。

Oracle 自治数据库中的 Oracle 机器学习控制台提供了笔记本和 AutoML 等功能。

## REST API 简介

`API` 源自“应用程序编程接口”（Application Programming Interface）一词。`API` 是一组规则，定义了应用程序或设备如何相互连接和通信。

`REST` 是“表述性状态转移”（REpresentational State Transfer）的缩写。`REST` 是一组架构约束，由 Roy Fielding 在其 2000 年的论文中首次提出：[www.ics.uci.edu/~fielding/pubs/dissertation/rest_arch_style.htm](http://www.ics.uci.edu/~fielding/pubs/dissertation/rest_arch_style.htm)

`REST` 既不是一种协议，也不是一种标准。它是一种用于架构和设计 Web 应用程序的风格。开发者可以通过多种方式实现 `REST`。`REST API` 通常通过超文本传输协议（`HTTP`）提供服务。换句话说，在 `REST API` 的上下文中，`HTTP` 是一种*契约*、一种通信协议，而 `REST` 则是一种概念。

`HTTP` 是一种应用层协议。它是一种无状态协议，遵循客户端-服务器模型：客户端和服务器通过交换消息进行通信。客户端打开一个连接以发出*请求*，并等待直到从服务器收到*响应*。

这些请求和响应被称为*HTTP 消息*。

一个请求由*请求行*、可选的*首部行*（后跟 `CRLF`，即回车和换行）、一个表示首部字段结束的*空行*（后跟 `CRLF`），以及可选的*消息体*组成。请求行包含请求的基本信息：`HTTP` 请求方法、要获取资源的路径（请求 `URI`），以及所使用的 `HTTP` 协议版本。例如：

```
GET /<oml-cloud-service-location-url>/omlmod/v1/api/ HTTP/1.1
```

© Adrian Png 和 Heli Helskyaho 2022

A. Png 和 H. Helskyaho，《使用 Oracle 云功能扩展 Oracle Application Express》，

[`doi.org/10.1007/978-1-4842-8170-3_6`](https://doi.org/10.1007/978-1-4842-8170-3_6#DOI)

有九种 `HTTP` 请求方法可用于 `REST API` 调用：`GET`、`HEAD`、`POST`、`PUT`、`DELETE`、`CONNECT`、`OPTIONS`、`TRACE` 和 `PATCH`。请求 `URI`（统一资源标识符）标识了要应用该请求的资源。

`HTTP` 首部表示与 `API` 请求或响应相关的元数据。一个*`HTTP` 首部*由名称-值对组成，名称和值之间用冒号（`:`）分隔。例如：

```
Accept-Language: en-us
Accept-Encoding: gzip, deflate
Content-Type: application/json
Accept: application/json
```

值之前的空格会被忽略。

媒体类型（多用途互联网邮件扩展或 `MIME` 类型）用于首部中，是互联网上文件格式的两部分标识符。这两部分（类型和子类型）由斜杠（`/`）分隔。例如：

```
application/json
audio/mpeg
image/png
```

多部分类型表示复合文档：即属于同一事务的多个独立文件。有两种多部分类型：`message` 和 `multipart`。*`Message`* 封装其他消息，而 *`multipart`* 由多个组件组成，每个组件可能具有不同的媒体类型。例如：

```
message/rfc822
multipart/form-data
```

你可以在 [www.iana.org/assignments/media-types/](http://www.iana.org/assignments/media-types/media-types.xhtml) 查看所有已定义的媒体类型。

`Cookie` 数据也在 `HTTP` 首部内传输。`HTTP` 协议是无状态的，但 *`HTTP cookies`* 通过允许在每个 `HTTP` 请求上创建会话以共享相同的上下文或状态，使得使用有状态会话成为可能。

一个空行表示首部部分结束，可选的消息体开始。消息体承载了你想要获取的实际 `HTTP` 请求数据。消息数据的内容取决于将要被调用的服务。例如，如果服务需要客户 `ID` 作为参数才能返回响应，那么客户 `ID` 就在请求消息的消息体部分中给出。

一个响应包含 `HTTP` 协议版本、状态码、状态消息、`HTTP` 标头，以及可选地包含所获取资源的正文。

*`HTTP` 响应状态码* 用于指示特定 `HTTP` 请求是否已成功完成，或者是否出现错误。响应分为五类：

- 信息响应 (`100–199`)
- 成功响应 (`200–299`)
- 重定向消息 (`300–399`)
- 客户端错误响应 (`400–499`)
- 服务器错误响应 (`500–599`)

你可以从 [developer.mozilla.org/zh-CN/docs/Web/HTTP/Resources_and_specifications](https://developer.mozilla.org/en-US/docs/Web/HTTP/Resources_and_specifications) 了解更多关于 `HTTP` 的信息。

`REST API` 请求可能需要在服务器端验证对资源的授权。一种选择是使用 *`OAuth 2.0`*。`OAuth 2.0` 是一种开放标准的授权协议，使用访问令牌进行授权。令牌的理念是，你可以在不提供凭证的情况下获得对服务的访问权限。你首先进行身份验证以获取令牌，然后使用该令牌调用服务。访问令牌可以是持有者令牌或发送者约束令牌。*持有者令牌* 意味着任何持有该令牌的人都可以使用它。如果你丢失了它，任何人都可以使用它。*发送者约束令牌* 只能由*有权*使用它的人使用。它就像一张航空登机牌：允许从 A 地到 B 地的航班，但仅限对其有效的人使用。登机牌上的姓名必须与护照上的姓名匹配，护照上的照片必须与客户的面部匹配。在 `OAuth 2.0` 中，这通常通过加密密钥解决方案来实现。

要获取令牌，你必须首先进行身份验证。有几种用于身份验证的 `OAuth` *授权类型*。例如：

- `authorization_code`（授权码授权）
- `client_credentials`（客户端凭证授权）
- `password`（资源所有者密码授权）
- `urn:ietf:params:oauth:grant-type:device_code`（设备授权）

授权类型指的是身份验证方法。例如，客户端凭证授权使用客户端 `ID` 和客户端密钥在用户上下文之外接收令牌。密码授权将用户的凭证（用户名、密码）交换为访问令牌，以便使用该用户的凭证访问资源。密码授权已被弃用，但在 `OML REST API` 中仍在使用。

使用其中一种授权类型进行身份验证后，你将获得授权令牌，并准备好使用该服务。授权通常有一个过期时间，过期后你需要获取一个新令牌。

### OML REST 服务简介

Oracle 机器学习服务的 REST API 提供了托管在 Oracle 自治数据库上的 REST API 端点，用于使用 Oracle 机器学习功能，例如，创建和存储机器学习模型以及为模型创建评分端点。OML 服务的 REST API 支持 OML 模型和开放神经网络交换（ONNX）格式模型。ONNX 是用于机器学习互操作性的开放标准。嵌入式 Python 执行的 REST API 使你能够执行存储在 Python 脚本仓库的自治数据库中的用户自定义 Python 函数，或使用存储在 OML4Py 数据存储中的 Python 对象。

要能够使用 REST 服务，你需要一个身份验证令牌。你从自治数据库的 `/omlusers` 端点检索令牌，然后使用此用户令牌和 REST API 端点中的操作，调用 Oracle 数据库中的机器学习功能。

## Oracle 机器学习服务的 REST API

Oracle 机器学习服务的 REST API 通过托管在 Oracle 上的 `/omlmod/` REST API 端点提供对数据库内机器学习的访问。Oracle 机器学习服务的 REST API 支持 Oracle 机器学习模型和开放神经网络交换（ONNX）格式模型。ONNX 是用于机器学习互操作性的开放标准。您可以从 [`onnx.ai/`](https://onnx.ai/) 了解更多信息。

Oracle 机器学习支持的机器学习模型类型（机器学习函数）例如有分类、回归、聚类和特征提取。对于 ONNX 格式模型，支持的机器学习模型函数是分类、回归和聚类。ONNX 分类还包括图像模型。

OML 的主要优势（除了增强的安全性和更多功能之外）在于所有与数据相关的繁重工作都在数据库内部完成，并且所有数据库功能都可用于此。Oracle 机器学习甚至支持分区模型。您可以以最有用的方式拆分数据，并为数据集的不同数据拆分构建不同的模型。

OML REST 服务由四种类型的 API 组成：通用、存储库、部署和认知文本。

- **通用**端点返回关于 OML 服务 API 所有可用版本的元数据、OML 服务的 Open API 规范（JSON 格式）以及 OML 服务 API 版本 1 的元数据。
- **存储库**端点处理与机器学习模型相关的数据。例如，您可以使用这些端点获取模型详细信息或删除模型。
- **部署**端点允许您例如创建或删除模型端点、获取模型端点的详细信息或使用端点对模型进行评分。
- **认知文本**端点包括诸如从文本中查找最相关的关键词、主题或情感分析等功能。

使用 Oracle 机器学习服务 REST API，您可以对 Oracle 机器学习和 ONNX 格式模型执行多项功能。您可以：

• 获取关于 OML 服务 API 所有可用版本的元数据
• 获取 OML 服务的 Open API 规范（JSON 格式）
• 获取 OML 服务 API 版本 1 的元数据
• 存储、删除和列出已部署的模型
• 检索模型的元数据、信息或内容
• 在命名空间下组织模型
• 创建、删除和列出模型端点
• 获取端点的详细信息，或使用模型端点进行评分
• 获取模型端点的 Open API 3.0 (Swagger) 文档
• 获取 OML 服务提供的所有认知文本端点
• 获取最相关的关键词或主题
• 获取提供的文本字符串列表的数值特征、语义相似度、情感或摘要

您可以在此处找到所有这些 REST 端点：[`docs.oracle.com/en/database/oracle/machine-learning/omlss/omlss/rest-endpoints.html`](https://docs.oracle.com/en/database/oracle/machine-learning/omlss/omlss/rest-endpoints.html)。

支持的请求方法是 `GET`、`POST` 和 `DELETE`，HTTP 请求行的 URL 结构形式为 `https://<oml-cloud-service-location-url>.oraclecloudapps.com`，其中 `oml-cloud-service-location-url` 由租户 ID、数据库名称和区域组成。

OML 服务 REST API 支持在 HTTP 请求或响应的标头部分传递以下标头：

• `Authorization`
• `Location`
• `Content-Type`

在 REST API 中，媒体类型是请求或响应消息体数据的格式。OML 服务 REST API 支持的媒体类型是：

• `application/json`
• `multipart/form-data`
• `application/octet-stream`

## 用于嵌入式 Python 执行的 REST API

用于嵌入式 Python 执行的 REST API 可用于使用 REST 客户端在 Oracle 自治数据库中执行 Python 代码。它调用存储在 Python 脚本存储库中的命名脚本中的用户定义函数 (UDF) 或存储在 Oracle 自治数据库的 OML4Py 数据存储中的 Python 对象。您可以找到所有端点信息请参考[此处：`docs.oracle.com/en/database/oracle/machine-learning/oml4py/1/mlepe/rest-endpoints.html`](https://docs.oracle.com/en/database/oracle/machine-learning/oml4py/1/mlepe/rest-endpoints.html)。

要开始使用，您需要先通过 `.../omlusers/` 获取一个令牌，然后使用该令牌调用 `.../api/py-scripts/` 上的 REST API。这些 REST API 位于自治数据库中，并使用令牌来验证 Oracle Machine Learning 用户的身份。令牌通过提供用户的用户名和密码来获取，且仅在有限时间内有效。

在执行 REST API 嵌入式 Python 执行功能时，您可以通过可选的 `service` 参数指定要使用的自治数据库服务级别。不同的服务名称可为 Oracle 自治数据库提供不同级别的性能和并发能力。可用的服务名称包括 `LOW`、`MEDIUM` 和 `HIGH`。

对嵌入式 Python 执行端点的 REST API 执行操作时，会使用标准的 HTTP 方法请求。支持的 HTTP 方法请求包括 `GET`、`HEAD` 和 `POST`。嵌入式 Python 执行的 REST API 支持 `application/json` 媒体类型，以及可在 HTTP 请求或响应的标头部分传递的以下标头：

• `Accept-Encoding`
• `Content-Encoding`
• `Content-Type`

响应消息始终包含一个状态码，用于表示请求是否成功。响应消息返回的状态码包括：

• `200 OK`
• `201 Created`
• `202 Accepted`
• `302 Found`
• `400 Bad Request`
• `401 Unauthorized`
• `500 Internal Server`

嵌入式 Python 执行功能端点的 REST API 调用默认模式为同步模式，但您也可以利用这些状态码以异步方式使用它。将 `async` Flag 参数设置为 `true` 来调用脚本执行函数。然后，使用该函数返回的作业 ID 获取作业的状态码。状态码 `202` 表示作业正在等待处理，`302` 表示作业已完成。当作业完成时，返回信息中还包含一个 `Content-Location` 标头。请使用该标头获取脚本的结果。

REST API 包含三种类型的任务：*获取版本元数据*、*列出脚本*和*执行脚本*。

- **获取版本元数据**REST 端点返回有关 API 版本的元数据。
- **列出脚本**REST 端点返回 Python 脚本存储库中可用的脚本列表。
- **执行脚本**REST 端点以类似于本章“OML4Py 部分中的 OCI 内数据库 Oracle Machine Learning”中所述的方式，执行 Python 脚本存储库中的脚本。

`/api/py-scripts/v1/` 端点具有多个用于执行用户定义函数的操作：

• `do-eval`，用于执行一个 Python 函数；您可以选择指定脚本的所有者。
• `table-apply`，用于对指定数据执行一个 Python 函数；您可以选择指定脚本的所有者。

• `group-apply`，用于对分组数据执行一个 Python 函数；您可以选择指定脚本的所有者。
• `index-apply`，用于多次执行一个函数；您可以选择指定脚本的所有者。
• `row-apply`，用于对数据行块执行一个 Python 函数；您可以选择指定脚本的所有者。
• `jobs`，用于检索异步作业的状态或结果。

# 使用 OML REST 服务

要能够使用 OML REST 服务，您需要一个 REST 客户端、一个令牌以及用于发送请求的端点。

## 安装和设置 REST 客户端 (`cURL`)

您需要一个 REST 客户端来使用 REST API。可以使用任何 REST 客户端，但在本例中，我们将使用 `cURL`。要安全地连接到服务器，您必须拥有支持 SSL 的 `cURL` 版本，并提供 SSL 证书颁发机构 (CA) 证书文件或捆绑包，以针对 Verisign CA 证书进行身份验证。

如果你尚未安装，请按照以下步骤操作：

1.  使用浏览器导航到 `cURL` 主页 [`curl.haxx.se`](http://curl.haxx.se)，然后点击页面顶部菜单中的“下载”。
2.  选择正确的 `cURL` 版本。
3.  点击链接下载 ZIP 文件，并安装该软件。
4.  导航到 `cURL` CA 证书页面 [`curl.haxx.se/docs/caextract.html`](http://curl.haxx.se/docs/caextract.html)。
5.  下载 `ca-bundle.crt` SSL CA 证书包 (`cacert.pem`)。
6.  将其保存在你安装 `cURL` 的同一文件夹中。
7.  打开一个命令窗口（命令提示符）。
8.  导航到你安装 `cURL` 的目录。
9.  将 `cURL` 环境变量 `CURL_CA_BUNDLE` 设置为 SSL 证书颁发机构（CA）证书包的位置。例如：
    ```
    C:\Curl> set CURL_CA_BUNDLE=ca-bundle.crt
    ```

## 创建访问令牌

要使用 REST API 访问 OML 服务，你必须提供一个访问令牌。获取 REST 身份验证令牌的 URL，如图[5-12](https://doi.org/10.1007/978-1-4842-8170-3_5#Fig12)所示。要完成身份验证并获取访问令牌，请使用 `cURL`。

`-d` 选项用于传递用户名和密码。用户名是本章前面创建的自治数据库 OML 用户的用户名。

令牌是使用 OML REST URL（我们称之为：“使用此 URL 获取 OML 提供的 REST API 的 REST 身份验证令牌”）并附加 `api/oauth2/v1/token` 来获取的。

语法如下所示：
```
curl -X POST --header "Content-Type: application/json" --header "Accept: application/json" -d "{"grant_type":"password", "username":"'**MyUser**'", "password":"'**MyPassword**'"}" "**<oml-cloud-service-location-url>**/omluser/api/oauth2/v1/token"
```
编辑脚本中的用户名、密码和 URL。转到命令提示符，并导航到你安装 `cURL` 的文件夹。执行该脚本。复制令牌并将其保存在安全的地方。请注意，令牌仅在规定时间内有效。响应消息中包含过期时间。

然后，使用该令牌发送请求。例如，要获取已部署的现有模型，请编辑 `<oml-cloud-service-location-url>`，将令牌复制/粘贴到 `addTokenHere`，然后运行以下代码：
```
curl --location --request GET "https://**<oml-cloud-service-location-url>**/omlmod/v1/models" --header "Authorization: Bearer **addTokenHere**"
```
或者，你可以请求某个端点的 Open API 规范（Swagger）文档：
```
curl --location --request GET "https://**<oml-cloud-service-location-url>**/omlmod/v1/api" --header "Authorization: Bearer **addTokenHere**"
```
你可以通过向路径添加部署和 uri，并编辑 uri 的值来调用任何端点：`/omlmod/v1/deployment/{uri}/api`。这将返回该端点的 Open API 规范（Swagger 文档）。

要从数据库中查询已注册的现有 Python 脚本：
```
curl -i -X GET --header "Authorization: Bearer eyJhbGciOiJSUz...=" --header "Accept: application/json" https://**<oml-cloud-service-location-url>**/oml/api/py-scripts/v1/scripts
```
它将返回头部信息，并在空行之后返回包含数据的消息体：
```
HTTP/1.1 200 OK

Date: Thu, 28 Apr 2022 12:50:41 GMT

Content-Type: application/json

Content-Length: 1129

Connection: keep-alive

Cache-Control: no-cache, no-store, must-revalidate

Pragma: no-cache

X-Frame-Options: SAMEORIGIN

X-XSS-Protection: 1;mode=block

Strict-Transport-Security: max-age=31536000; includeSubDomains

X-Content-Type-Options: nosniff

Content-Security-Policy: frame-ancestors 'none'

Set-Cookie: JSESSIONID=node01itqttned1z8615ljt3ijn0mge4.node0; Path=/oml; Secure; HttpOnly

Expires: Thu, 01 Jan 1970 00:00:00 GMT

{"result":[{"owner":"RESTMLUSER","date":"2022-04-27T07:23:12.000Z","name":"nb_predict","description":"基于 AutoML 笔记本构建的 UDF，用于根据 Beer_data_autoML 表中的数据预测啤酒的总体评分。","script":"def nb_predict(): \n import pandas as pd ...\n\nreturn(json_result)"}]}
```
使用 `cURL` 时，您也可以使用 `cURL` 环境变量，例如 `$token` 来表示令牌。要将令牌赋值给变量，请使用 `export` 命令，并将值插入单引号内：
```
export token='eyJhbG...='
```

## 使用 OML REST 服务的示例

要调用 OML 服务的 REST API，您首先需要按照前面所述获取令牌。然后，使用该令牌，您可以调用 REST 端点中的操作。

令牌的 REST 端点路径是 `/omusers/`；对于 OML，路径是 `/omlmod/`；对于 Python，路径是 `/oml/`。

让我们以认知文本 REST 端点为例。这是一个 REST API，它还需要一个消息体来传递数据。我们以 Adrian Png 和 Luc Demanche 所著的《Oracle Cloud Free Tier 入门指南：使用 Always Free 资源创建现代 Web 应用程序》一书的描述为例，调用“获取最相关主题”端点，从描述中找出前十个主题。
```
curl -X POST --header "Authorization: Bearer eyJhbGciOiJSUzI1NiJ9....="

"https://**<oml-cloud-service-location-url>**/omlmod/v1/**cognitive-text/topics**"

--header 'Content-Type: application/json'
```

第 6 章 Oracle 机器学习 REST 服务

`-d` `'{"textList":["使用这本全面指南，开始使用 Oracle Cloud Free Tier。阅读本书并在 Free Tier 中创建自己的应用程序，是熟悉并掌握 Oracle Cloud Infrastructure 专业知识的绝佳途径。更棒的是，Free Tier 本身功能足够强大，并提供了创建安全、稳健、适度规模的多层 Web 应用程序所需的所有要素。本书中的示例介绍了 Oracle Cloud Infrastructure 提供的广泛 Always Free 选项。您将学习如何配置自治数据库和自治 Linux 计算节点。您还将了解如何使用 Terraform 将基础设施作为代码进行管理。您还将学习虚拟云网络和应用程序部署，包括如何在 Oracle REST 数据服务的基础上创建和部署面向公众的 Oracle Application Express 解决方案以及三层 Web 应用程序。本书还简要介绍了如何使用和管理 Oracle 机器学习笔记本的访问权限。云计算是一个强劲的行业趋势。掌握本书内容将使您能够顺利过渡到提供和支持基于云的应用程序和数据库。您将拥有部署适度应用程序所需的知识和技能，并加深对 Oracle 云平台的理解，这将帮助您超越 Always Free 选项的限制，充分利用 Oracle Cloud Infrastructure 所能提供的一切。"], "topN":10}'`

响应消息体返回 `topicResults`：

```json
..."topicResults": [{
    "topic": "云计算",
    "weight": 0.21997470116809562
}, {
    "topic": "谷歌云平台",
    "weight": 0.21351581196215733
}, {
    "topic": "甲骨文公司",
    "weight": 0.18296549446070967
}, {
    "topic": "IT 基础设施",
    "weight": 0.1509873670535844
}, {
    "topic": "云存储",
    "weight": 0.14193632625983807
}, {
    "topic": "平台即服务",
    "weight": 0.12096843030927087
}, {
    "topic": "Web 应用程序",
    "weight": 0.1108704736442413
}, {
    "topic": "Linux 基金会",
    "weight": 0.09169194469729022
}, {
    "topic": "Google App Engine",
    "weight": 0.09149938274982759
}, {
    "topic": "虚拟学习环境",
    "weight": 0.08927694707494302
}]
}]
```

这段文本包含哪些情感？让我们用相同的文本调用认知文本 REST 端点的情感分析接口来找出答案。文本内容相同，为节省篇幅，部分内容已用省略号代替。

```bash
curl -X POST --header "Authorization: Bearer eyJhbGciOiJSUzI1NiJ9...=" \
"https://<oml-cloud-service-location-url>/omlmod/v1/cognitive-text/sentiment" \
--header 'Content-Type: application/json' \
-d '{"textList":["使用这本综合指南开始使用 Oracle Cloud 免费套餐。...Oracle 云基础设施所能提供的服务。"]}'
```

结果消息体如下所示：

```json
... "sentimentResults": [{
    "sentiment": "positive",
    "confidence": 0.7928020390465644
}, {
    "sentiment": "neutral",
    "confidence": 0.11748919864179037
}, {
    "sentiment": "negative",
    "confidence": 0.08970876231164532
}]
}]
```

要获取文本摘要，我们可以使用以下命令调用 `cognitive-text` REST 端点 `summary`：

```bash
curl -X POST --header "Authorization: Bearer ...=" "https://<oml-cloud-service-location-url>/omlmod/v1/cognitive-text/summary" \
--header 'Content-Type: application/json' \
-d '{"textList":["使用这本综合指南开始使用 Oracle Cloud 免费套餐。...Oracle 云基础设施所能提供的服务。"], "topN":5}'
```

响应消息体：

```json
... [{
    "text": "使用这本综合指南开始使用 Oracle Cloud 免费套餐。...Oracle 云基础设施所能提供的服务。",
    "summaryResults": [{
        "sentence": "阅读本书并在免费套餐中创建自己的应用程序是熟悉并掌握 Oracle 云基础设施的绝佳方式。",
        "weight": 0.6736350343541044
    }, {
        "sentence": "本书中的示例介绍了 Oracle 云基础设施提供的广泛始终免费选项。",
        "weight": 0.596030953896922
    }, {
        "sentence": "您还将了解虚拟云网络和应用程序部署，包括如何在 Oracle REST 数据服务的基础上创建和部署面向公众的 Oracle Application Express 解决方案和三层 Web 应用程序。",
        "weight": 0.8162674885046073
    }, {
        "sentence": "掌握本书内容后，您将能够顺利过渡到提供和支持基于云的应用程序和数据库。",
        "weight": 0.6206552547451695
    }, {
        "sentence": "您将拥有部署适度应用程序所需的知识和技能，并加深对 Oracle 云平台的理解，这将帮助您超越始终免费选项的限制，充分利用 Oracle 云基础设施所能提供的一切。",
        "weight": 0.7240305647558988
    }]
}]
```

此示例中的请求作为 HTTP 请求消息如下所示：

```
POST /omlmod/v1/cognitive-text/summary HTTP/1.1
Host: <oml-cloud-service-location-url>
Authorization: Bearer eyJ...ik=
Content-Type: application/json
Content-Length: 1590

{"textList":["使用这本综合指南开始使用 Oracle Cloud 免费套餐。...Oracle 云基础设施所能提供的服务。"], "topN":5}
```

第一行是请求行，然后是标头数据，在空行之后是消息体。

响应消息如下所示：

```
HTTP/1.1 200 OK
Date: Fri, 22 Apr 2022 07:58:43 GMT
Content-Type: application/json
Content-Length: 2803
Connection: keep-alive

[{"text":"使用这本综合指南开始使用 Oracle Cloud 免费套餐。...Oracle 云基础设施所能提供的服务。", "summaryResults":[{"sentence":"阅读本书并在免费套餐中创建自己的应用程序是熟悉并掌握 Oracle 云基础设施的绝佳方式。","weight":0.6...]}]
```

# 第6章 Oracle 机器学习 REST 服务

```json
[
  {"sentence": "本书中的示例介绍了 Oracle 云基础设施提供的广泛始终免费选项。", "weight": 0.596030953896922},
  {"sentence": "您还将了解虚拟云网络和应用程序部署，包括如何在 Oracle REST 数据服务的基础上创建和部署面向公众的 Oracle Application Express 解决方案和三层 Web 应用程序。", "weight": 0.8162674885046073},
  {"sentence": "掌握本书内容后，您将能够顺利过渡到提供和支持基于云的应用程序和数据库。", "weight": 0.6206552547451695},
  {"sentence": "您将拥有部署适度应用程序所需的知识和技能，并加深对 Oracle 云平台的理解，这将帮助您超越始终免费选项的限制，充分利用 Oracle 云基础设施所能提供的一切。", "weight": 0.7240305647558988}
]
```

## 响应消息格式
响应消息以 HTTP 版本和 HTTP 响应状态码开头。然后，它包含一些标头数据和消息正文。

## 在 Python 脚本存储库中注册函数
要使用 Python REST API 执行嵌入式 Python 代码，您需要在 Python 脚本存储库中注册 UDF Python 函数，或者在数据库中的 OML4Py 数据存储中注册 Python 对象。如果 Python 对象未存储在 Python 脚本存储库或 OML4Py 数据存储中，则它们仅存在于 Python 会话期间。

## 查看 Python 脚本存储库中的脚本
要查看 Python 脚本存储库中的 UDF 脚本，请调用端点 `scripts`：

```bash
curl -i -X GET --header "Authorization: Bearer ...PTik="oml/api/py-scripts/v1/scripts
```

如本章前面所述，它会返回带有状态码和正文（结果）的标头（部分数据已替换为三个点）：

```
HTTP/1.1 200 OK

Date: Mon, 25 Apr 2022 07:09:45 GMT
Content-Type: application/json
Content-Length: 3132
Connection: keep-alive
Cache-Control: no-cache, no-store, must-revalidate
Pragma: no-cache
X-Frame-Options: SAMEORIGIN
X-XSS-Protection: 1;mode=block
Strict-Transport-Security: max-age=31536000; includeSubDomains
X-Content-Type-Options: nosniff
Content-Security-Policy: frame-ancestors 'none'
Set-Cookie: JSESSIONID=node0au1vdygtmknu10sn1bbxkbukb6.node0; Path=/oml; Secure; HttpOnly
Expires: Thu, 01 Jan 1970 00:00:00 GMT

{"result":[
{"所有者":"PYQSYS","日期":"2022-04-23T12:36:14.000Z","名称":"nn_pred", "描述":"基于 AutoML 笔记本构建的 UDF，用于使用表名作为输入参数预测 ENROLLED","脚本":"def nn_pred...)\n return (pred)"},
{"所有者":"PYQSYS","日期":"2022-04-23T12:37:44.000Z","名称":"nn_predi", "描述":"基于 AutoML 构建的 UDF ...\n return (pred)"},
{"所有者":"PYQSYS","日期":"2022-04-23T09:09:20.000Z","名称":"nn_predict", "描述":"基于 AutoML 构建的 UDF..."},
{"所有者":"PYQSYS","日期":"2022-04-23T12:15:23.000Z","名称":"nn_prediction..."}
]}
```

## 在 OML4Py 数据存储中注册模型
让我们使用在第[5](https://doi.org/10.1007/978-1-4842-8170-3_5)章中基于 AutoML 实验创建的笔记本。创建的模型是一个 Python 代理对象，会话关闭后它将消失。

因此，我们需要在笔记本上运行所有步骤，以在 Python 会话中获取代理对象。要将模型存储在 OML4Py 数据存储中，我们在笔记本中添加一个步骤，将创建的模型 (`nn_mod`) 注册到 OML4Py 数据存储 (`ds_pymodel`)：

```python
%python
oml.ds.save(objs={'nn_mod':nn_mod}, name="ds_pymodel", grantable=True, overwrite=True)
```

在此示例中，模型 `nn_mod` 存储在名为 `ds_pymodel` 的 OML4Py 数据存储中，并且权限已授予所有用户。现在它已存储在 OML4Py 数据存储中，我们可以随时调用它。

## 管理 OML4Py 数据存储权限
Python 对象始终在*用户的模式*下注册到 OML4Py 数据存储中。您可以使用 `oml.grant` 添加权限，并使用 `oml.revoke` 移除权限。在此示例中，该用户有两个 OML4Py 数据存储：`ds_pymodel` 和 `ds_pymodel_test`。我们仅向一个用户 (`MYUSER1`) 授予对 `ds_pymodel_test` 的权限，并向所有用户 (`None`) 授予对数据存储 `ds_pymodel` 的权限：

```python
%python
oml.grant(name="ds_pymodel_test", typ="datastore", user="MYUSER1")
```

```python
%python
oml.grant(name="ds_pymodel", typ="datastore", user=None)
```

您可以使用 `oml.ds.dir` 检查模型是否在 OML4Py 数据存储中。该函数可以使用以下参数进行定义：`user`、`grant`、`granted`、`grantable`、`all` 和 `private`。

```python
%python
oml.ds.dir(dstype='grant')
```
```
datastore_name  grantee
0       ds_pymodel     PUBLIC
1  ds_pymodel_test     MYUSER1
```

## 查看与删除数据存储内容
要查看 OML4Py 数据存储包含的内容，请使用 `oml.ds.describe`：

```python
%python
oml.ds.describe(name='ds_pymodel', owner=None)
```
```
object_name    class  size  length  row_count  col_count
0      nn_mod  oml.nn  3198       1          1          1
```

您可以使用 `oml.ds.delete` 从 OML4Py 数据存储中删除对象。

OML4Py 数据存储有两个数据库视图：`ALL_PYQ_DATASTORES` 和 `ALL_PYQ_DATASTORE_CONTENTS`。您可以使用 SQL 查询这些视图，以获取有关现有 OML4Py 数据存储的信息。

## 创建 UDF 进行预测
让我们创建一个名为 `nn_predict` 的 UDF。该 UDF 将使用存储在 OML4Py 数据存储 `ds_pymodel` 中的 `nn_mod` 模型，基于名为 `STUDENTTRAIN` 的表中的数据，为 `ENROLLED` 字段创建预测。结果中还包含 `STUDENTID`，以便在构建 APEX 应用程序时，我们能够将这些数据与数据库中的数据进行连接。在 UDF 中，我们导入所需的库，将表的内容获取到数据框中，从 OML4Py 数据存储加载模型，使用模型进行预测，最后以 JSON 格式返回结果。

```python
nn_predict = """def nn_predict():
    import pandas as pd
    import oml
    import json
    data = oml.sync(table = ' STUDENTTRAIN ')
```

# 第 6 章 Oracle 机器学习 REST 服务

首先，我们有一个Python函数，用于加载模型并进行预测：

```python
obj_dict = oml.ds.load(name="ds_pymodel", to_globals=False)

nn_mod = obj_dict["nn_mod"]

mod_predict = nn_mod.predict(data ,supplemental_cols = data

[:, ['STUDENTID']]).pull()

json_result = mod_predict.to_json(orient='records')

return(json_result)"""
```

然后，将该函数（UDF）存储在Python脚本存储库中：

`oml.script.create`(
    name="nn_predict",
    func=nn_predict,
    is_global=True,
    overwrite=True,
    description= '一个基于 AutoML 笔记本构建的 UDF，用于使用表 STUDENTTRAIN 中的数据预测 ENROLLED。'
)

UDF 被注册到 `PYQSYS` 模式中。

要从另一个 Python 会话的笔记本中调用它，您需要导入 `oml` 并从 Python 脚本存储库加载该 UDF。然后，使用执行 Python 代码的函数之一来调用它。在此示例中，使用 `do_eval`：

```python
import oml

nn_predict = oml.script.load(name="nn_predict")

oml.do_eval(nn_predict)
```

要获取为用户注册的可用脚本列表，请使用 `oml.script.dir`：

```python
oml.script.dir(sctype='user')
```
```
name ... date
0 nn_predict ... 2022-04-25 08:04:45
1 nn_predict_test ... 2022-04-25 11:55:38

[2 rows x 4 columns]
```

或者，要列出已定义为全局的脚本：

```python
oml.script.dir(sctype='global')
```
```
name ... date
0 nn_pred ... 2022-04-23 12:36:14
1 nn_predi ... 2022-04-23 12:37:44
2 nn_predict ... 2022-04-25 08:05:32
3 nn_prediction ... 2022-04-23 12:15:23

[4 rows x 4 columns]
```

您可以使用与 OML4Py 数据存储中 Python 对象相同的 `oml.grant` 和 `oml.revoke` 来授予和撤销权限，但对于脚本，需要定义 `typ="pyqscript"`。

另一种调用 UDF 的方法是使用 REST API：

```bash
curl -i -X POST --header "Authorization: Bearer ...="
--header "Content-Type: application/json"
--header "Accept: application/json"
-d '{"service":"MEDIUM"}'
"https://`<oml-cloud-service-location-url>`/oml/tenants/`MyTenancy`/databases/`MyDatabase`/api/py-scripts/v1/do-eval/`nn_predict`"
```

请注意，您可以从 OCI 数据库服务控制台获取所需的 URL。

如果您想从 Python 脚本存储库中删除 UDF，请使用 `oml.script.drop` 并将函数名放在引号 "" 中：

```python
import oml
oml.script.drop("nn_predict")
```

如果您想查看数据库中的 UDF，可以使用 SQL 查询数据库视图 `ALL_PYQ_SCRIPTS`。

## 从 APEX 使用 OML REST 服务

在 APEX 中，您可以通过其 PL/SQL 包使用 OML4SQL，并通过 OML4Py 嵌入式 Python 执行 (EPE) SQL API 使用 OML4Py。要从 SQL 调用 Python UDF，您可以在 SQL 查询中包含 OML4Py EPE 函数。这些函数包括与嵌入式 Python REST API 功能相似的函数包括：`pyqEval`、`pyqTableEval`、`pyqRowEval`、`pyqGroupEval` 和 `pyqIndexEval`。

您也可以使用 REST API。即使 APEX 与机器学习对象位于不同的数据库中，此方法也同样适用。

第一步是进行身份验证以获取授权令牌。OML REST 服务使用 OAuth 2.0 密码授权流程，而 APEX 仅支持客户端凭证流程。

密码授权流程意味着访问令牌会与用户名和密码一同提供，并附加到用户的权限上。为 OML REST API 创建一个专用用户可能很有用，该用户仅拥有使用该服务所需的有限权限。

而客户端凭证流程则使用服务器到服务器的身份验证。

Web 凭证是在 APEX 中处理凭证的最佳方式。由于 APEX Web 凭证仅允许客户端凭证 OAuth 2.0 令牌，而不允许密码令牌，因此您无法直接创建用于获取令牌的 Web 凭证。对此有几种技术解决方案。让我们来看一种可能的方案。

在此方案中，我们为令牌创建一个 Web 凭证，但使用 REST 数据服务来获取该令牌。首先，为令牌创建 Web 凭证。转到 App Builder，选择 Workspace Utilities，然后选择 Web Credentials。此 Web 凭证不会调用服务并获取令牌；它仅存储令牌，而令牌是通过稍后解释的另一种机制获取后存入的。所需的简单 Web 凭证如图 6-1 所示。只需为凭证命名，并选择 HTTP Header 作为 Authentication Type。

### 图 6-1. 创建 Web 凭证

然后，我们创建一个用于获取 OAuth2 令牌的数据源。选择应用程序，然后选择 *Shared Components*。导航到左下角并点击 *REST Data Sources*。点击 *Create*。选择 *From scratch*，然后点击 *Next*。选择 *Simple HTTP* 作为 REST Data Source Type，定义一个 *名称*，并将 `<oml-cloud-service-location-url>/omluser/*` 作为 *URL Endpoint* 插入。点击 *Next*。现在，您已经定义了远程服务器（包括远程服务器、基础 URL 和服务 URL 路径）。点击 *Next*。对于分页类型，保留默认值：*No Pagination*，然后点击 *Next*。**不要**启用 *Authentication Required*。点击 *Advanced*。添加所有必需的参数，如图 6-2 所示。

首先，为请求消息添加 HTTP 标头参数：`Content-Type = application/json`，以及 `Accept = application/json`。然后，选择 POST 作为 HTTP 方法。在请求体中，插入密码流程所需的信息。

```json
{
  "grant_type" : "password",
  "username" : "您的用户名",
  "password" : "您的密码"
}
```

将您的用户名和密码分别插入到 `YourUsername` 和 `YourPassword` 处。然后，通过选择页面中 Response Sample 字段的 Choose File 并插入文件来定义响应消息。您可以在 Oracle 文档的 REST API 端点部分找到该文件。

### 图 6-2. REST 数据服务的参数

然后点击 *Discover*。如果成功获取到令牌，如图 6-3 所示，点击 *Create REST Data Source*。

### 图 6-3. REST 数据服务返回一个令牌

现在，我们已经构建了一个用于获取令牌的 REST 数据源和一个用于保存令牌的 Web 凭证。

然后，我们需要一个过程（`set_omldb_amst_token_to_header`）来从数据源获取令牌并将其存入 Web 凭证，同时将该令牌设置为给定 Web 凭证的会话令牌。该令牌仅对调用它的 APEX 会话有效。该过程会获取令牌，并且令牌会附带其过期时间。

```sql
-- set_omldb_amst_token_to_header 使用 apex_exec 和
```

# 第 6 章 Oracle 机器学习 REST 服务

在 APEX 中定义的 REST 数据源，例如 `OMLDB_AMST_TOKEN`

```sql
procedure set_omldb_amst_token_to_header(
    p_token_source in varchar2,
    p_credential in varchar2,
    p_token_expiry_epoch out number) is
    --
    l_columns apex_exec.t_columns;
    l_context apex_exec.t_context;
    type t_column_position is table of pls_integer index by varchar2(32767);
    l_column_position t_column_position;
begin
    --
    -- 指定要从 Web 源模块中选择的列
    apex_exec.add_column(p_columns => l_columns, p_column_name => 'ACCESSTOKEN');
    apex_exec.add_column(p_columns => l_columns, p_column_name => 'EXPIRESIN');
    apex_exec.add_column(p_columns => l_columns, p_column_name => 'TOKENTYPE');
    --
    -- 调用 Web 源模块并选择数据
    l_context := apex_exec.open_rest_source_query(
        p_static_id => p_token_source,
        p_columns => l_columns );
    --
    -- 现在获取所选列的结果集位置
    l_column_position('ACCESSTOKEN') := apex_exec.get_column_position(l_context, 'ACCESSTOKEN');
    l_column_position('EXPIRESIN') := apex_exec.get_column_position(l_context, 'EXPIRESIN');
    l_column_position('TOKENTYPE') := apex_exec.get_column_position(l_context, 'TOKENTYPE');
    --
    -- 遍历结果集（本例中仅有一行），将令牌设置为会话凭据，并将令牌过期时间设置为纪元时间
    while apex_exec.next_row( l_context ) loop
        apex_credential.set_session_credentials (
            p_credential_static_id => p_credential,
            p_key => 'Authorization',
            p_value => 'Bearer ' || apex_exec.get_varchar2(
                l_context, l_column_position('ACCESSTOKEN')
            )
        );
        p_token_expiry_epoch := convert_to_epoch(sysdate) + apex_exec.get_number(
            l_context, l_column_position('EXPIRESIN')
        );
    end loop;
    --
    -- 最后：释放所有资源
    apex_exec.close( l_context );
exception
    when others then
        -- 重要：当发生异常时，也要释放所有资源！
        apex_exec.close( l_context );
        raise;
end set_omldb_amst_token_to_header;
```

## 函数 `convert_to_epoch` 说明

函数 `convert_to_epoch` 将给定的日期转换为纪元时间。纪元时间用于处理令牌过期。令牌的有效期为一小时。

```sql
-- convert_to_epoch 将给定的日期转换为纪元时间，即从 1970 年 1 月 1 日开始的秒数
function convert_to_epoch(p_date in date) return number is
    l_epoch number(38);
begin
    select (p_date - to_date('1-1-1970 00:00:00','MM-DD-YYYY HH24:Mi:SS'))*24*3600
    into l_epoch from dual;
    return l_epoch;
end convert_to_epoch;
```

## 对于过期时间，我们需要一个应用程序项

在 APEX 中，进入应用程序，选择`共享组件`、`应用程序项`，并添加应用程序项，如图 6-4 所示。

![图 6-4. 添加应用程序项](img/index-230_1.jpg)

**图 6-4.** 添加应用程序项

现在，我们已经准备好调用 OML REST API。我们将调用本章前面使用过的同一个 `cognitive-text/summary` 接口。为了能够调用它，我们定义一个`REST 数据源`。选择应用程序，然后选择`共享组件`。导航到左下角并点击`REST 数据源`。创建一个新的 REST 数据源，如图 6-5 所示。定义一个名称，并为 REST 数据源类型选择`简单 HTTP`。

定义服务端点的路径，并在身份验证部分选择之前定义的 Web 凭据作为凭据。

![图 6-5. 为摘要 API 端点创建 REST 数据源](img/index-231_1.jpg)
![图 6-5. 为摘要 API 端点创建 REST 数据源](img/index-231_2.jpg)

**图 6-5.** 为摘要 API 端点创建 REST 数据源

摘要端点的请求操作类型为 POST。您可以删除所有其他操作，如图 6-6 所示。

**图 6-6.** REST 数据源操作

![图 6-6. REST 数据源操作](img/index-232_1.jpg)

选择`获取行`作为数据库操作，并插入请求体模板（图 6-7）。

**图 6-7.** POST 操作的操作参数

定义参数。为 HTTP 标头插入一个 `Content-Type: application/json` 参数，如图 6-8 所示。

![图 6-8. 定义标头参数](img/index-233_1.jpg)
![图 6-8. 定义标头参数](img/index-233_2.jpg)

**图 6-8.** 定义标头参数

然后，插入请求正文参数：`Textlist`（图 6-9）和 `TopN`（图 6-10）。

**图 6-9.** 定义消息正文参数 TextList

![图 6-9. 定义消息正文参数 TextList](img/index-234_1.jpg)
![图 6-9. 定义消息正文参数 TextList](img/index-234_2.jpg)

**图 6-10.** 定义消息正文参数 TopN

如图 6-11 所示定义响应消息。

**图 6-11.** 定义响应消息

![图 6-11. 定义响应消息](img/index-235_1.jpg)
![图 6-11. 定义响应消息](img/index-235_2.jpg)

如图 6-12 和 6-13 所示，定义响应消息的参数 `sentence` 和 `weight`。

**图 6-12.** 定义 sentence 参数

**图 6-13.** 定义 weight 参数

![图 6-13. 定义 weight 参数](img/index-236_1.jpg)

现在，您可以使用 Web 数据源，通过 OML REST API 获取所提供文本字符串列表的摘要。

使用向导创建一个页面，例如交互式报表。选择 REST 数据源，然后选择您之前为 REST 数据源创建的 REST 数据源，如图 6-14 所示。

**图 6-14.** 创建交互式报表

![图 6-14. 创建交互式报表](img/index-237_1.jpg)

打开页面进行编辑。首先，如图 6-15 和 6-16 所示，创建一个 `GetToken` 进程。

**图 6-15.** 创建 GetToken 进程

![图 6-15. 创建 GetToken 进程](img/index-238_1.jpg)

**图 6-16.** GetToken 进程的 PL/SQL 代码

```sql
if :G_TOKEN_EXPIRY is null or omldb_util.convert_to_epoch(sysdate) > :G_TOKEN_EXPIRY then
    omldb_util.set_omldb_amst_token_to_header(p_token_source => 'OMLDB_AMST_TOKEN'
                                             ,p_credential => 'OMLDB_AMST_HEADER');
end if;
```

# 第 6 章 Oracle 机器学习 REST 服务

创建一个*区域查询*。然后，如图 6-17 所示，添加三个查询参数项，这些参数将作为请求参数传递给 OML 数据源。其中两个是页面项，一个是按钮。`P15_TEXT` 是文本区域类型，`P15_TOPN` 是数字字段类型，`Query` 是一个按钮，用于提交页面并执行查询。

![](img/index-240_1.jpg)

*图 6-17.* 向 APEX 页面添加项

这些参数需要与 REST 数据源中的参数连接起来，如图 6-18 和 6-19 所示。

![](img/index-241_1.jpg)

*图 6-18.* 将 Textlist 参数连接到 `P15_TEXT` 字段

![](img/index-242_1.jpg)

*图 6-19.* 将 TopN 参数连接到 `P15_TOPN` 字段

此外，创建*子区域* epoch（图 6-17）和两个页面项：`F15_TOKEN_EXPIRY`（仅显示类型）和 `F15_TOKEN`（文本字段类型）。

![](img/index-243_1.jpg)

然后，您可以运行该页面，如图 6-20 所示。

![](img/index-244_1.jpg)

*图 6-20.* 使用摘要 REST API 的 APEX 应用程序

## 总结

通过 REST API，使用 Oracle 数据库中的机器学习功能（包括 Oracle 机器学习和嵌入式 Python 执行）又多了一种选择。REST API 的流程是首先获取令牌，然后使用 OAuth 2.0 身份验证令牌通过 HTTP 消息请求 REST API 端点。在 APEX 中调用 REST API 服务时，Web 凭据和 REST 数据源是所使用的工具。

# 第 7 章 数据标注

监督学习是机器学习的一个子类别，其过程由标注数据监督。模型使用已知的输入和输出进行训练和测试。

已知输出数据。对于已知输出数据，意味着数据必须被标记。数据标记将监督学习无法使用的数据集转变为监督学习可用的数据集。

数据标记服务提供了一种流程，用于识别复杂对象（如文档、文本和图像）的特征，并根据这些特征对其进行注释或标记。数据标记过程包括创建数据记录数据集，并对这些记录应用标签。数据集可以导出为行分隔的 JSON (JSONL) 格式，以便在监督机器学习过程中进一步使用。

使用此服务，您可以标记 PDF、TIF 和 TIFF 格式的文档，TXT 格式的文本文件，以及 JPG、JPEG 和 PNG 格式的图像。

## 设置服务

要开始使用数据标记服务，您需要一个具有服务管理员角色权限的 Oracle Cloud Infrastructure (OCI) 账户，或者一个可以设置服务并授予您所需权限的人员。您还需要一个受支持的浏览器。请从 OCI 文档中查看受支持的浏览器。

首先，为数据标记资源创建一个区间。这不是强制步骤，但建议这样做，以提高安全性并更轻松地管理权限。从导航菜单中选择*身份与安全*，然后选择*区间*。

选择您想要在其中创建子区间的区间，然后点击*创建区间*，如图 7-1 所示。

![](img/index-245_1.png)

*图 7-1.* 区间页面的子区间部分

输入所需数据，如图 7-2 所示：名称、描述以及可能的标签。然后，点击*创建区间*。

![](img/index-246_1.jpg)

*图 7-2.* 创建区间

然后，创建用于存储数据的*存储桶*。即使您使用本地计算机的数据，也需要一个对象存储和一个存储桶来存储数据标记过程创建的数据集和记录。要创建存储桶，请从导航菜单中选择*存储*。然后，选择*存储桶*。从列表中选择正确的区间，然后点击*创建存储桶*（图 7-3）。

![](img/index-247_1.jpg)

![](img/index-247_2.jpg)

*图 7-3.* 现有存储桶列表和创建新存储桶的按钮

定义*存储桶名称*，并选择*默认存储层*和*加密*方式，如图 7-4 所示。点击*创建*。

![](img/index-248_1.jpg)

*图 7-4.* 创建新存储桶

对数据标记服务的访问权限以及用户拥有的访问类型，由*策略*控制。默认情况下，只有管理员组中的用户才能访问所有数据标记资源。所有其他用户组的权限必须通过策略进行管理。

可以为单个用户定义策略，但最佳实践是创建一个用户组，为该组定义策略，然后将用户添加到该组中。使用此方法可以更轻松地维护权限。

首先，为数据标记用户创建一个用户组。然后，将用户添加到此组。在此示例中，我们将创建一个 IAM 用户组和一个 IAM 用户。

**注意**：如果您使用的是身份云服务提供商（iDCS 提供商），例如 Oracle 身份云服务 (OiCS)，请在*联合*下创建组并分配用户。

通过从导航菜单中选择*身份与安全*来为数据标记创建一个用户组。然后，选择*组*以查看组页面，如图 7-5 所示。

![](img/index-249_1.jpg)

*图 7-5.* 用户组列表和创建新组的按钮

点击*创建组*。定义*名称*和*描述*（图 7-6）。然后，点击*创建*。

![](img/index-250_1.jpg)

*图 7-6.* 创建新用户组

下一步是创建一个新用户，或将现有用户添加到此 DataLabelingGroup。要创建新用户，请从导航菜单中选择*身份与安全*。然后，选择*用户*。在用户页面中点击*创建用户*来添加新用户（图 7-7）。

![](img/index-250_1.jpg)

![](img/index-250_2.jpg)

*图 7-7.* 用户列表及添加新用户的按钮

为用户输入*名称*和*描述*（图 7-8）。您还可以添加*电子邮件地址*。点击*创建*。

**注意**：如果您正在创建 Oracle 身份云服务用户，则可以在创建用户时将其添加到用户组。

![](img/index-251_1.jpg)

![](img/index-251_2.jpg)

*图 7-8.* 创建新用户

要将用户添加到用户组，请从导航菜单中选择*身份与安全*。然后选择*组*。导航到正确的组（DataLabelingGroup），如图 7-9 所示。

![](img/index-252_1.jpg)

*图 7-9.* 组成员列表及添加新成员的按钮

点击*将用户添加到组*。从用户列表中选择用户，然后点击*添加*（图 7-10）。

![](img/index-252_2.jpg)

*图 7-10.* 将用户添加到用户组

*资源主体*用于授予对 Oracle 云基础设施资源的访问权限。`Datalabelingdataset` 是数据标注服务所需的唯一资源主体。

使用该资源主体规则创建一个*动态组*。要创建动态组，请从导航菜单中选择*身份与安全*，然后选择*动态组*。

![](img/index-253_1.jpg)

*图 7-11.* 动态组列表及创建新动态组的按钮

点击*创建动态组*。为动态组输入*名称*和*描述*。对于规则，定义 ALL {resource.type = 'datalabelingdataset'}，如图 7-12 所示。

![](img/index-253_2.jpg)

*图 7-12.* 使用已定义规则创建新动态组

下一步是在父区间中创建两个*策略*。从导航菜单中选择*身份与安全*，然后选择*策略*。导航到父区间（在我们的示例中为 Heli 区间）。我们为演示创建的策略是 DataLabeling-non-tech-user-policy 和 DataLabeling-dynamic-group-policy，如图 7-13 所示。

![](img/index-254_1.jpg)

*图 7-13.* 策略列表及创建新策略的按钮

要创建策略，请点击*创建策略*。

首先，为用户组（非管理员用户）创建策略，如下所示：

```
allow group DataLabelingGroup to read buckets in compartment DataLabeling
allow group DataLabelingGroup to manage objects in compartment DataLabeling
```

# 第 7 章 数据标注

在我们的示例中，我们创建了一个名为 `DataLabeling` 的区间、一个名为 `DataLabelingGroup` 的用户组以及一个名为 `DataLabelingDynamicGroup` 的动态用户组。然后我们创建了两个策略集：`DataLabeling-non-tech-user-policy` 和 `DataLabeling-dynamic-group-policy`。

要了解有关最佳实践、设置 OCI 环境以及策略的更多信息，请参阅 Oracle 手册。例如，要了解更多关于策略的信息，请访问
[`docs.oracle.com/en-us/iaas/Content/Identity/Concepts/policies.htm`](https://docs.oracle.com/en-us/iaas/Content/Identity/Concepts/policies.htm)。

首先，为组 `DataLabelingGroup` 创建策略：

```
allow group DataLabelingGroup to read objectstorage-namespaces in compartment DataLabeling
allow group DataLabelingGroup to manage data-labeling-family in compartment DataLabeling
```

然后，为动态组 `DataLabelingDynamicGroup` 创建策略：

```
allow dynamic-group DataLabelingDynamicGroup to read buckets in compartment DataLabeling
allow dynamic-group DataLabelingDynamicGroup to read objects in compartment DataLabeling
allow dynamic-group DataLabelingDynamicGroup to manage objects in compartment DataLabeling where any {request.permission='OBJECT_CREATE'}
```

请记住在策略构建器中点击`显示手动编辑器`，以便能够添加这些策略。

![img/index-254_1.jpg](img/index-254_1.jpg)

要开始使用数据标注服务，请点击导航菜单上的`分析与AI`。在机器学习下选择`数据标注`（图 7-14）。

**图 7-14.** OCI 控制台中的数据标注

## 数据集、记录和标注

数据标注过程会对数据集中的记录进行标注。该过程的第一步是创建数据集。一个数据集由一个或多个数据记录组成。选择包含待标注数据的文件。创建数据集的过程会将这些文件转换为记录。

然后，您手动标注数据集中的记录。标注意味着您为数据集中的每条记录赋予一个标签或注释。

![img/index-255_1.jpg](img/index-255_1.jpg)

![img/index-255_2.jpg](img/index-255_2.jpg)

## 创建数据集和记录

在数据标注中（图 7-15），选择`数据集`。选择正确的区间。这是您设置策略的区间。点击`创建数据集`按钮。

**图 7-15.** 数据集列表和用于创建新数据集的按钮

为数据集定义一个`名称`，并可选择定义一个`描述`。然后，选择数据类型，即`数据集格式`：图像、文本或文档。在图 7-16 中，已选择了图像选项。

**图 7-16.** 为新数据集添加详细信息

![img/index-256_1.jpg](img/index-256_1.jpg)

`注释类`选项取决于所选的数据集格式。对于`图像`，注释类选项为单标签、多标签或对象检测。对于`文本`，选项为单标签、多标签或实体提取。对于`文档`，选项为单标签或多标签。选择首选的注释类。在图 7-17 中，您可以看到图像数据的注释类选项。如果需要，您还可以为数据集添加`标签`。准备就绪后，点击`下一步`。

**图 7-17.** 图像可用的注释类

添加用于创建数据集的文件有两种选项（图 7-18）：`上传本地文件` 或 `从对象存储中选择`。根据您要标注的数据选择相应选项。

**注意** 如果您想一次从本地文件夹加载超过 100 个文件，请在创建数据集之前将它们加载到对象存储桶中，或者使用 `CLI` 或 `SDK`。

![img/index-257_1.jpg](img/index-257_1.jpg)

![img/index-257_2.jpg](img/index-257_2.jpg)

**图 7-18.** 为数据集选择数据源

然后，选择正确的`区间`，如图 7-19 所示。选择`存储桶`。如果您选择了`上传本地文件`，则该存储桶是您要存储数据集的桶；如果您选择了`从对象存储中选择`，则该存储桶是数据所在的桶。如果需要，您可以添加一个`前缀`。如果您选择上传本地文件，此前缀将添加到上传到对象存储桶的每个文件的文件名中。如果您选择从对象存储中选择，此前缀将用作创建数据集所用文件的过滤器，并且仅从满足过滤器条件的文件创建数据集。

**图 7-19.** 为数据集定义对象存储详细信息

然后，选择将添加到数据集的文件。如果您选择上传本地文件作为数据源（图 7-18），您将从本地计算机选择文件（图 7-20）。如果您选择从对象存储中选择，则会显示来自该对象存储的文件以供选择。

![img/index-258_1.jpg](img/index-258_1.jpg)

![img/index-258_2.jpg](img/index-258_2.jpg)

**图 7-20.** 为数据集选择文件

最后一步是添加在标注数据集中的记录时将使用的标签（图 7-21）。您必须至少添加一个标签，但最好添加所有需要的标签。如果您后来发现缺少标签，可以稍后添加更多标签。点击`下一步`。

**图 7-21.** 为数据集定义标签

现在在`审核页面`（图 7-22）上，检查一切是否符合您的要求。如果您发现任何问题，请点击`编辑数据集详细信息`或`编辑文件和标签`来纠正错误。如果一切正常，请点击`创建`。数据集创建过程开始，并可在数据集详细信息页面上看到状态显示为`正在创建`（图 7-23）。

![img/index-259_1.png](img/index-259_1.png)

![img/index-259_2.jpg](img/index-259_2.jpg)

**图 7-22.** 创建数据集前检查详细信息的审核页面

**图 7-23.** 正在创建的数据集的数据集详细信息页面

为数据集选择的每个文件都将转换为数据集中的一条记录。

当数据集成功创建后，您可以在所选数据集的`数据集详细信息`页面中看到记录，其状态显示为`活动`。如图 7-24 所示。当数据集首次创建时，所有记录都被标记为未标注。

![img/index-260_1.jpg](img/index-260_1.jpg)

**图 7-24.** 数据集详细信息

您可以在数据标注主页上的`工作请求`（图 7-26）选项卡中跟踪数据集创建过程的进度。您可以通过选择`日志消息`（图 7-29）来详细了解工作请求的进展情况。如果在数据集创建过程中出现任何错误，您可以在`错误消息`选项卡（图 7-30）中找到相关信息。

## 标注数据集中的记录

下一步是标注记录。如果您在创建数据集时没有添加所有需要的标签，您可以点击数据集详细信息页面上的`添加标签`按钮（图 7-24）来添加更多标签。标签的最大数量为 255。您可以在图 7-24 所示的标签选项卡下找到数据集的标签列表。

要标注一条记录，您可以点击列表中该记录的名称，或者选择三点菜单并选择`标注`。从标签列表中选择正确的标签。

然后，如果您不想标注更多记录，请点击`保存`；如果您想标注下一条记录，请点击`保存并下一步`。如果您不想标注该记录，请点击`跳过`，下一条记录将被打开。在图 7-25 中，有一个对象检测的示例。标签用于标记图片中人物的鼻子、嘴巴和眼睛。要标注，请选择`边界框`、所需的`标签`，然后在图片中`标记该标签的区域`。您可以使用`移除框`来移除任何绘制在错误位置的框。工具栏提供了在画布中移动图片、放大和缩小、重置缩放以及调整图像的工具。

![img/index-261_1.jpg](img/index-261_1.jpg)

**图 7-25.** 标注记录

在数据集详细信息页面上，您可以通过标注状态过滤记录，以仅查看已标注记录或未标注记录。您还可以选择以`数据记录`或`图库视图`的形式查看记录。图库视图显示记录的内容，而不仅仅是名称。在图库视图中，您可以通过标签搜索记录，以列出具有所需标签的记录。在数据记录视图中，您可以通过点击表头按记录名称或创建时间对记录进行排序。

如果您想编辑数据集信息，可以点击`编辑`（图 7-24）。您可以编辑数据集的名称及其描述，但不允许更改对象存储信息。

如果您想从本地驱动器添加更多文件，可以通过相同的“编辑数据集”表单进行添加。只需拖放文件，或使用文件选择功能进行选择即可。文件的记录会自动生成。


# 第 7 章 数据标注

如果在数据集详细信息页面（图 7-24）中选择*更多操作*，您可以执行移动资源、添加标签、查看工作请求或删除数据集等操作。*移动资源*允许您将数据集从一个区间移动到另一个区间。*添加标签*允许您向数据集添加新标签。创建数据集时，系统会自动生成两个标签：`CreatedBy` 和 `CreatedOn`。*查看工作请求*会显示该区间上的所有工作请求，如图 7-26 所示。如果遇到无法自行解决的工作请求问题，三点菜单允许您打开一个支持请求。*删除*则会删除数据集。

![](img/index-262_1.jpg)

![](img/index-262_2.jpg)

***图 7-26.** 工作请求*

数据集创建完成后，可以在数据集列表中找到它（如图 7-27 所示）。如果数据集创建成功，其状态为*活动*。数据集的其他可能状态包括*创建中*、*更新中*、*删除中*、*已删除*、*失败*和*需要注意*。

“失败”和“需要注意”都表示创建未成功。随着列表变长，您可能会发现数据集列表左侧的*筛选器*非常有用。使用筛选器，您可以按*数据集状态*或*标注类别*进行筛选。

***图 7-27.** 数据集列表*

要查看数据集详细信息，请通过单击其名称或从三点菜单（即右侧标有三个点的菜单）中选择“详细信息”来选择数据集（图 7-27）。

为了能够在任何机器学习过程中使用已标注的数据，请使用数据集详细信息页面（图 7-24）中的*导出*按钮将其导出。此导出的结果将存储在 JSONL（JSON Lines 格式）文件中。您可以选择文件存储的存储桶，并可选地为它们定义前缀。默认情况下，文件中仅包含已标注的记录，但如果您也想包含未标注的记录，请选择*包含未标注的记录以导出*。导出会创建两个 JSONL 文件：一个包含元数据，另一个包含每条记录的数据。主文件（即元数据文件）包含数据集 ID、区间 ID、显示名称、标签集的值、标注格式、数据集来源和格式的详细信息，以及包含数据集记录的其他文件的信息。另一个文件包含每条记录的信息，以及其标注方式、时间和人员。

## 向数据集添加新记录

在许多情况下，标注数据量不足、缺乏足够的多样性，或者由于某些原因，您无法充分训练模型，需要更多/更好的数据来训练模型。在您创建了数据集及其记录之后，可以向数据集添加新记录。

如本章前面所述，如果您想从本地目录向数据集添加新记录，可以通过在数据集详细信息页面（图 7-24）选择*编辑*，然后拖放文件或使用文件选择功能进行选择。

如果您想将对象存储中的文件包含到数据集中，只需单击数据集详细信息页面上的*生成记录*按钮。位于所选存储桶中且满足数据集条件的文件将自动作为记录添加到数据集中。之前存在的记录保持不变，包括它们的标签，新记录则被添加到数据集中。您可以像本章前面标注记录一样对它们进行标注。

然后，您可以导出新的数据集用于模型构建，以查看其效果是否更好。

## 数据标注错误

当您开始创建数据集时，会创建一个*工作请求*。您可以在“数据标注”和“工作请求”下找到所有工作请求，如图 7-28 所示。

![](img/index-264_1.jpg)

![](img/index-264_2.jpg)

***图 7-28.** 工作请求列表*

如果您从列表中选择一个工作请求，您可以在*日志消息*页面（如图 7-29 所示）中查看该工作请求期间执行的所有操作。

***图 7-29.** 工作请求的日志消息*

如果工作请求因某种原因失败，并且数据集处于“需要注意”状态，您可以在*错误消息*页面（如图 7-30 所示）中查看详细信息。如果您选择“消息”字段右侧的三点菜单，可以选择*查看完整消息*以查看完整的错误消息。

![](img/index-265_1.jpg)

***图 7-30.** 工作请求的错误消息*

有时此消息很有用，您能够解决问题。但有时您只需要向 Oracle 支持部门提交支持工单。

一个非常典型的问题是您选择了错误的区间，或者您根本没有足够的权限访问所有必需的资源。或者，可能是存储桶缺失，或者您没有使用它的权限。

也可能是您遇到了某个*限制*。支持的文档文件格式为 PDF、TIF 和 TIFF，文本支持的格式为 TXT。对于图像，支持的格式为 JPEG、JPG 和 PNG。另外，请注意，创建数据集时，大小超过 6 MB 的图像不会被保存。您也可能已经耗尽了资源或配额限制。在图 7-31 中，您可以看到数据标注服务的*限制、配额和使用情况*。如果您为任何限制选择三点菜单，您可以选择*创建配额策略存根*以获取更改配额策略的存根，或者您可以打开一个支持请求。

![](img/index-266_1.jpg)

***图 7-31.** 限制、配额和使用情况*

## 使用 OCI API 通过 APEX 进行数据标注

如果您更喜欢从 APEX 使用数据标注服务，可以通过 OCI 数据标注 API 来实现。

### 数据标注 API

数据标注有两个 API：数据标注 API 和数据标注管理 API。*数据标注 API* 用于在图像、文本或文档上创建标注（标签）并生成快照。*数据标注管理 API* 可用于创建、列出、编辑和删除数据集。在编写本书时，这些 API 的版本是 `/20211001`。数据标注 API 的文档可以在 [`docs.oracle.com/en-us/iaas/api/#/en/datalabeling-dp/20211001/`](https://docs.oracle.com/en-us/iaas/api/#/en/datalabeling-dp/20211001/) 找到，数据标注管理 API 的文档可以在 [`docs.oracle.com/en-us/iaas/api/#/en/datalabeling/20211001/`](https://docs.oracle.com/en-us/iaas/api/#/en/datalabeling/20211001/) 找到。

### 为数据标注 API 设置 APEX

首先，如果您还没有创建 IAM 用户，请在 OCI 控制台中创建一个，并将该用户添加到数据标注组（在我们的示例中为 `DataLabelingGroup`）。然后，返回进入用户的详细信息页面，确保用户已选择 API 密钥。从导航菜单中选择*身份与安全*。然后，选择*用户*。从用户列表中选择该用户，进入用户详细信息页面（图 7-32）。

![](img/index-267_1.jpg)

***图 7-32.** 用户详细信息页面*

点击*编辑用户功能*，并检查*API 密钥*是否已选中（图 7-33）。要限制用户仅能使用 API 密钥与 OCI 资源交互，请取消选中所有其他选项。

**注意** 如果您希望用户能够使用其他方法，请不要限制对 API 密钥的访问。

![](img/index-268_1.png)

![](img/index-268_2.jpg)

***图 7-33.** 定义用户功能*

要使用 OCI REST API，需要一对 API 密钥。要获取 API 密钥对，请从用户详细信息页面的左侧选择*API 密钥*（图 7-34）。

***图 7-34.** API 密钥资源页面*

![](img/index-269_1.png)

点击*添加 API 密钥*。将打开“添加 API 密钥”页面（图 7-35）。

***图 7-35.** 添加 API 密钥*

选择*生成 API 密钥对*。点击*下载私钥*。您刚刚下载的文件是 `.pem` 类型，内容类似于：

```text
-----BEGIN PRIVATE KEY-----
MIIEvAIBADANBgkqhkiG9w0BAQEFAASCBKYwggSiAgEAAoIBAQCv2FWr73tHBsrA
VhqKE/WfOrzE6WzaYDzgAycr2SGOa3bQrkXx31uo0O9Rk2GHy71Zl+SzwF0Qz3Bh
+HTDlaSC1FZSHZ/9DAUMidAjSpvMBnPAcIKGmmECgYASx9EM7tdWvhD3kpN9uBuc
r7ZBCU/O1mxeIm7eNlbCbLCfGsVhP4Q8v1HKQZz54y9YMRZW8eO3YEHtwwRJVegz
8n+yYWYa/IMLLq1i8ZRqhg==
-----END PRIVATE KEY-----
```

删除第一行（`-----BEGIN PRIVATE KEY-----`）和最后一行（`-----END PRIVATE KEY-----`），然后将内容复制到安全的地方。这是用于 Web 凭据的私钥。然后点击*添加*。检查*配置文件预览*（图 7-36），以获取 APEX 中 Web 凭据所需的其他详细信息。

![](img/index-270_1.png)

***图 7-36.** 配置文件预览*

我们稍后创建 Web 凭据和 REST 数据源所需的信息如下：

* 用户 OCID
* 指纹
* OCI 私钥
* 租户 OCID
* 区间 OCID
* 租户区域
* 命名空间

![](img/index-271_1.jpg)


# 第 7 章 数据标注

您始终可以通过选择查看配置文件（如图 7-37 所示）从用户页面找到这些信息。如果您没有所有必需的信息，请联系您的管理员获取。

### 图 7-37. 从 OCI 控制台查找查看配置文件

然后，以您希望其有权访问数据标注 API 的用户身份登录 APEX。创建一个新的 Web 凭据。转到工作区实用程序，并选择 Web 凭据。点击创建。为 Web 凭据指定一个名称，并选择 OCI 作为身份验证类型。然后，将 OCI 用户 ID（用户 OCID）、OCI 私钥、OCI 租户 ID（租户 OCID）和 OCI 公钥指纹粘贴到相应的字段中，如图 7-38 所示。点击创建。

**注意** 如果您没有所有信息，可以返回 OCI 控制台，从“身份与安全”中查看。选择“用户”，然后从用户列表中选择正确的 APEX 用户。在“用户详细信息”中，选择“查看配置文件”。

![img/index-272_1.jpg](img/index-272_1.jpg)

### 图 7-38. 创建 Web 凭据

## 在 APEX 中使用数据标注 API

创建 Web 凭据后，您就可以使用 OCI API 服务了。对于数据标注，有两个 API：数据标注 API 和数据标注管理 API。OCI API 参考和端点可以在 Oracle 文档中找到：

[`docs.oracle.com/en-us/iaas/api/#/`](https://docs.oracle.com/en-us/iaas/api/#/)

要查看 API 请求的请求详情，请导航至该 API 参考。要能够使用特定的 API 请求，您需要创建一个 REST 数据源。

为此，在 APEX 应用程序生成器中，选择您想要创建此 REST 数据源的应用程序。选择共享组件。导航至数据源并选择 REST 数据源。点击创建。选择从头开始，然后点击下一步。

![img/index-273_1.jpg](img/index-273_1.jpg)

将 REST 数据源类型选择为 OCI，如图 7-39 所示。为数据源命名。在我们的示例中，此数据源将列出来自 `DataLabeling` compartment 的所有现有数据集。`ListDatasets` 是 Data Labeling Management API 的一部分（`GET /20211001/datasets`），因此我们将 URL 端点设置为 Data Labeling Management API 端点。您可以从 Oracle API 文档中找到该端点。

在我们的示例中，Data Labeling API 的 API 端点是 `https://datalabeling-dp.us-phoenix-1.oci.oraclecloud.com`，而 Data Labeling Management API 的端点是 `https://datalabeling-cp.us-phoenix-1.oci.oraclecloud.com`。然后点击下一步。

### 图 7-39. 为 Data Labeling Management API 服务定义 OCI REST 数据源

![img/index-274_1.jpg](img/index-274_1.jpg)

下一步是指定要使用的请求。在我们的示例中，它是 `Datasets`。

在服务 URL 路径字段中插入信息，如图 7-40 所示。您可以从 Oracle 手册的 API 文档中找到服务 URL 路径。然后点击下一步。

### 图 7-40. 在 Data Labeling Management API 中为 Dataset 请求定义远程服务器

选择需要身份验证，然后从列表中选择 Web 凭据，如图 7-41 所示。凭据使用其静态标识符列出。然后点击高级。

![img/index-275_1.jpg](img/index-275_1.jpg)

### 图 7-41. 为 API 请求定义身份验证

在高级部分（图 7-42），定义用于 Data Labeling 的 compartment。从参数类型列表中选择查询字符串变量，输入 `compartmentId` 作为参数名称，并将 Data Labeling compartment 的 compartment id 复制/粘贴到值字段中。选择静态。然后点击发现。

![img/index-276_1.jpg](img/index-276_1.jpg)

### 图 7-42. 为 REST 数据源定义高级参数

如果 REST 数据源的所有设置都正确且权限到位，发现过程将列出 Data Labeling compartment 中的数据集，如图 7-43 所示。然后点击创建 REST 数据源。

![img/index-277_1.jpg](img/index-277_1.jpg)

### 图 7-43. REST 数据源预览：所选 compartment 中的 Data Labeling 数据集列表

现在您已创建了一个 REST 数据源，您可以将其用作 APEX 中的数据源并构建 APEX 应用程序。以类似的方式，您可以为所有需要的 Data Labeling 或 Data Labeling Management API 请求创建 REST 数据源。

在 APEX 中使用 REST 数据源非常简单。例如，您可以从我们刚刚创建的 API 请求创建一个页面，以列出所选 compartment 中的所有数据集。在您的 APEX 应用程序中，选择创建页面。选择报表和交互式网格。填写所需信息。点击下一步。然后，如图 7-44 所示，选择 REST 数据源作为数据源。然后从 REST 数据源列表中选择已创建的 REST 数据源。选择您想要用于交互式网格的列。然后点击创建。

![img/index-278_1.jpg](img/index-278_1.jpg)
![img/index-278_2.jpg](img/index-278_2.jpg)

### 图 7-44. 基于 REST 数据源创建交互式网格

在图 7-45 中，您可以看到这个简单的 APEX 应用程序。

### 图 7-45. 一个显示所选 compartment 中数据集的简单 APEX 应用程序

由于创建数据集是 Data Labeling 服务的一项功能，编辑现有数据集或添加新数据集需要使用 API 请求。请**不要**在没有 API 的情况下编辑记录、数据集或标签：对记录、数据集和注释（标签）的所有修改都必须通过 API 请求完成。例如，`CreateDataset` 创建一个数据集，`DeleteDataset` 删除一个数据集，`UpdateDataset` 更新数据集。其他对象，例如存储桶中的文件，可以在没有 Data Labeling API 的情况下进行管理。

## 总结

Data Labeling 过程用于识别复杂对象（如文档、文本和图像）的特征，并根据这些特征对其进行注释或标记。在 Data Labeling 过程中，您创建数据记录的数据集，并将标签应用于这些记录。标记后的数据集可以导出为行分隔的 JSON (JSONL)，用于监督式机器学习过程。

Data Labeling 服务有两个 API：Data Labeling API 和 Data Labeling Management API。可以从 APEX 调用这些 API，以便在 APEX 应用程序中使用这些功能。

# 第 8 章 异常检测

异常检测用于识别与常态显著不同的意外事件、观测值或项目。要检测异常，首先需要训练一个模型来学习什么是正常行为。然后，应用统计技术来确定特定数据点是否为异常。

OCI Anomaly Detection 服务是一项托管服务，无需深入的机器学习知识即可使用异常检测。您可以轻松地使用自己的数据构建机器学习模型，然后使用它来检测另一组数据中的异常。

Anomaly Detection 服务使用多变量状态估计技术 (MSET)，然后结合序贯概率比检验 (SPRT) 来监控实际观测值与 MSET 基于相关变量预测的估计值之间的残差。只要模型经过训练能够确定给定时间序列中什么是正常的，该服务就可以检测不同类型的异常，包括从未见过的异常。

异常检测识别与大多数数据非常不同的事件/观测值。模型是使用典型的、正常的数据创建和训练的。然后，将一个新的数据集提供给模型。将数据集的实际值与模型的预测值进行比较。如果实际值远离模型预测的值，则可能是一个异常。训练数据不应包含异常值，因为数据应该教会模型什么是正常的。数据集应包含所有可能业务用例的正常数据，以便能够教会正确的“正常”。

异常的原因有很多。异常的原因可能是人为错误、抽样错误、数据处理错误、实验错误或许多其他原因，但也可能是一个真正的异常，对业务具有严重意义。异常值可能表明过程中出现了问题，应该修复以避免对业务造成干扰。例如，温度异常可能导致机械故障，从而引起系统中断。为了避免过程中的错误，应避免温度变化。或者，可能是仪器错误，表明该特定仪器有故障。也有可能该异常并非异常，而是一个完全正常的数据，只是最初未包含在训练数据集中以教会模型什么是正常的。那么，明智的做法是向训练数据集中添加更多示例并重新训练模型。

## Anomaly Detection 服务

这是 Anomaly Detection 服务的第一个版本，Oracle 有很多计划。

通过添加流式支持以实现实时检测，或引入用户反馈以优化学习过程等功能，来改进它。

要使用异常检测服务，请从 OCI 控制台导航菜单中选择`分析与 AI`。然后在`AI 服务`下选择`异常检测`。

## 设置异常检测服务

要开始使用异常检测服务，您需要一个具有服务管理员角色权限的 Oracle Cloud Infrastructure (OCI) 账户，或者由能够设置该服务并授予您所需权限的人员来操作。您还需要一个受支持的浏览器。请查阅 OCI 文档了解受支持的浏览器。

接下来，我们将了解如何设置 OCI 环境，以便您能够使用异常检测服务。

## 区间、策略、用户组和用户

为异常检测服务使用的资源创建一个`区间`。这不是强制步骤，但可能是个好主意，因为它允许您授予对该区间内资源的权限。要创建区间：

1. 从 OCI 控制台导航菜单中选择`身份与安全`。

2. 选择`区间`。

3. 导航到您希望在其下创建新区间（作为子区间）的父区间。

4. 在`子区间`部分（图 8-1），点击`创建区间`。

    ![](img/index-282_1.jpg)

5. 填写所需信息，如图 8-2 所示。

6. 点击`创建区间`。

    ![](img/index-283_1.jpg)

在我们的示例中，我们创建了一个名为 `AnomalyDetection` 的区间。

然后，我们需要一个具有正确`权限`的`用户`来使用该区间内的服务。这些权限通过`策略`授予。

有两种类型的用户：`IAM 用户`和使用身份提供商服务登录的联合用户。这些身份提供商的一个例子是 `Oracle Identity Cloud Server`。`IAM 用户`使用图 8-3 中所示的 `Oracle Cloud Infrastructure 直接登录`，而 `Oracle Identity Cloud Server` 用户使用同一图中所示的`单点登录 (SSO)`。

![](img/index-284_1.png)

可以直接将所有策略授予用户，但最佳实践是创建一个用户组，将策略分配给该组，然后将用户添加到用户组中。按照此实践，创建一个用户组。创建用户组的方式取决于用户类型。

如果您有 `IAM 用户`，请按照以下说明操作：

1. 从导航菜单中选择`身份与安全`。

2. 从身份与安全中选择`组`。

    ![](img/index-285_1.jpg)

3. 点击`创建组`。

4. 添加组名称（例如，`AnomalyDetectionGroup`）和描述。

5. 点击`创建`。

如果您有联合用户，请按照以下说明操作：

1. 从导航菜单中选择`身份与安全`。

2. 选择`联合`。

3. 从列表中选择`身份提供商`。如果缺失，则添加一个新的。然后选择它。在我们的示例中，我们为 `Oracle Identity Cloud Service` 用户创建了一个用户组。

4. 导航到`资源`部分，并选择`组`，如图 8-4 所示。

    ![](img/index-286_1.jpg)

5. 点击`创建 IDCS 组`。

6. 添加所需数据，如图 8-5 所示。如果用户已存在，您可以通过从`用户`列表中选择该用户将其添加到组中。

7. 点击`创建`。

然后，创建使用异常检测服务的`策略`。所需的策略是允许 <subject> 管理 `ai-service-anomaly-detection-family` 在 `compartment` 中，其中 subject 可以是 `group <group_name> | group id <group_ocid> | dynamic-group <dynamic-group_name> | dynamic-group id <dynamic-group_ocid> | any-user`。

在我们的示例中，我们将创建如下策略：

```
allow group AnomalyDetectionGroup to manage ai-service-anomaly-detection-family in compartment AnomalyDetection
```

要添加所需的策略，请遵循以下说明：

1. 从导航菜单中选择`身份与安全`。

2. 选择`策略`。

3. 选择异常检测区间的父区间。

4. 点击`创建策略`按钮，如图 8-6 所示。

    ![](img/index-287_1.jpg)
    ![](img/index-287_2.jpg)

5. 填写策略的名称和描述，如图 8-7 所示。确认区间正确。

6. 在`策略构建器`中，启用`显示手动编辑器`。

7. 将策略文本粘贴到编辑器中。

8. 点击`创建`。

现在，您拥有了一个具有异常检测服务所需权限的用户组。

如果您尚未创建用户，请创建一个`用户`（可以是 `IAM 用户`或 `Oracle Identity Cloud Server` 用户）。要创建 `IAM 用户`：

1. 从 OCI 控制台导航菜单中选择`身份与安全`。

2. 选择`用户`。

3. 点击`创建用户`并填写所需数据。在此示例中，我们创建一个名为 `Anomalydetectionuser` 的 `IAM 用户`。

4. 点击`创建`。

要创建 `Oracle Identity Cloud Server` 用户（或其他身份提供商的联合用户）：

1. 从 OCI 控制台导航菜单中选择`身份与安全`。

2. 选择`联合`。

3. 如果您尚未定义身份提供商，请点击添加身份提供商和新的联合。

4. 然后，选择您想要的联合。在我们的示例中，它是 `OracleIdentityCloudService`。

5. 导航到`用户`部分，如图 8-8 所示。

    ![](img/index-289_1.jpg)

6. 点击`创建用户`。

7. 填写用户名、电子邮件地址，并可选择填写用户的名字和姓氏，如图 8-9 所示。

8. 如果需要，您可以通过从列表中选择用户组名称，将用户添加到之前创建的用户组中。

9. 点击`创建`。

最后但同样重要的是，如果尚未完成，请将用户添加到用户组。找到您创建的用户组，无论是 `IAM 用户组`还是联合用户组。

对于 `IAM 用户组`：

1. 从导航菜单中选择`身份与安全`。

2. 从身份与安全中选择`组`。从用户组列表中选择异常检测组，如图 8-10 所示。

    ![](img/index-290_1.jpg)

3. 点击`将用户添加到组`。

4. 从`用户`列表中选择用户。

5. 点击`添加`。

在我们的示例中，我们将用户 `AnomalyDetectionUser` 添加到了用户组 `AnomalyDetectionGroup` 中。

对于联合用户组，您可以选择一个用户并将其添加到用户组，或者选择一个用户组并向其中添加用户。

1. 从导航菜单中选择`身份与安全`。

2. 从身份与安全中选择`联合`。

3. 从列表中选择所需的联合（身份提供商）。

4. 然后，从用户列表中选择用户，或者从资源中选择组。

5. 如果您选择了用户，则点击`添加到 IDCS 组`，并从`组`菜单中选择该组。

6. 如果您选择了`组`，则选择异常检测组。

好的，作为高级文档工程师和翻译员，我将严格遵循您提供的注意事项和示例，将给定的英文文本翻译成中文。

从图 8-11. 用户组所示的用户组列表中，单击`添加用户到组`。单击`添加到 IDCS 组`，然后从 `用户`列表中选择用户。单击`添加`。

![](img/index-291_1.jpg)

现在，您已经为异常检测服务创建了一个`区间`，一个拥有使用该服务所需`权限`的`用户组`，并且您已经创建了一个`用户`并将其`添加`到用户组中。您已准备好使用该服务。

## 保险库、密钥和机密

如果您想使用自治数据库中的表作为机器学习模型创建的数据源，您还应该设置好保险库、密钥和机密。如果您仅使用对象存储桶和本地文件作为数据源，则可以跳过此步骤。要完成所有这些操作，您需要拥有管理保险库和密钥的权限，或者应请管理员为您执行此操作。

# 第 8 章 异常检测

*保险库*是一个存储所有类型机密信息的地方，这些信息涉及访问 OCI 中任何资源的身份验证详细信息。您无需在配置文件或代码中显示任何身份验证详细信息，只需安全地从保险库中使用即可。保险库是 OCI 中的一项托管云服务。它为*机密*提供了一个安全存储。您可以将密码、钱包、证书、SSH 密钥或身份验证令牌（您在配置文件中使用这些信息）作为机密存储在 OCI 保险库中。当您需要访问资源或其他服务时，可以从保险库中检索这些机密。*主加密密钥*用于加密机密信息。

## 创建保险库

1. 从 OCI 控制台导航菜单中选择*身份与安全*，然后选择*保险库*。
2. 选择所需的区间，然后单击*创建保险库*。
3. 检查区间是否正确，并为保险库输入*名称*。
4. 单击*创建保险库*。

然后，您需要创建一个*密钥*。为此：

1. 选择您刚刚创建的保险库以进入*保险库详细信息*页面。
2. 在*资源*中导航到*主加密密钥*。
3. 在主加密密钥中，单击*创建密钥*。
4. 定义*保护模式*：HSM 或软件。选择 HSM。
5. 定义*密钥名称*。
6. 选择*密钥形状：算法*。这是密钥加密、解密、签名或验证数据的数学机制。不同的算法用于不同的目的。支持的算法包括高级加密标准 (AES)、Rivest–Shamir–Adleman (RSA) 和椭圆曲线加密数字签名算法 (ECDSA)。AES 是默认算法。选择它。
7. 选择*密钥形状：长度*的值。默认值为 256 位。选择它。
8. 单击*创建密钥*。

然后，我们需要创建所有必需的机密。要创建机密：

1. 在*保险库详细信息*页面中，导航到*资源*中的*机密*。
2. 单击*创建机密*。
3. 提供如图 8-12 所示的以下详细信息：
    a. *区间*：检查所选的区间是否正确。机密不必与保险库位于同一区间。
    b. *名称*：机密的唯一名称。
    c. *描述*：机密的描述。

    ![](img/index-293_1.jpg)

    d. *加密密钥*：从列表中选择您刚刚创建的加密密钥。此密钥将用于加密机密。如果找不到，请检查区间是否正确。如果不正确，请单击*更改区间*并从列表中选择正确的区间。
    e. *机密类型模板*：纯文本或 Base64。选择纯文本。
    f. *机密内容*：输入异常检测用户的密码。
    g. 单击*创建机密*。

**图 8-12.** 创建机密

现在，异常检测用户的密码已存储在 OCI 保险库中，并可在需要时从中使用。

要连接到自治数据库，仅有用户名和密码是不够的；您还需要定义*连接详细信息*。连接信息也可以作为机密存储在保险库中。

首先，您需要获取*钱包*。在 OCI 控制台中，选择 Oracle 数据库，自治数据库。然后从自治数据库列表中选择您的数据库。在自治数据库详细信息页面（图 8-13）中，单击*数据库连接*。

![![](img/index-294_2.png)

**图 8-13.** 自治数据库详细信息页面

然后，如图 8-14 所示，选择*实例钱包*作为钱包类型，并单击*下载钱包*。

**图 8-14.** 下载钱包

![](img/index-295_1.png)

钱包需要一个密码。如图[8-15 所示定义密码，并单击*下载*。将 ZIP 文件保存到安全位置。单击*关闭*。

**图 8-15.** 为钱包定义密码

由于钱包由多个文件组成，我们将对所有文件进行 base64 编码。将 `wallet.zip` 文件解压到本地计算机上的一个文件夹中。您可以使用任何 Base64 编码器，例如 [www.base64encode.org/](http://www.base64encode.org/)。每次将一个文件（`README.txt` 除外）拖放到编码器中，单击 ENCODE，并将编码后的文件保存到安全位置。例如，将文件 `cwallet.sso` 拖放到编码器，单击 ENCODE，下载编码后的文件，并将其保存为例如 `encoded_cwallet_sso.txt`。当您对 `wallet.zip` 文件中的所有文件都执行此操作后，您的本地文件夹应类似于图 8-16。

![](img/index-296_1.png)

**图 8-16.** 钱包文件及其编码版本

然后，为每个文件创建一个*机密*，如图 8-17 所示：

1. 在*保险库详细信息*页面中，导航到*资源*中的*机密*。
2. 单击*创建机密*。
3. 提供所需信息：区间、名称、描述和加密密钥。
4. 选择*Base64*作为*机密类型*。
5. 将编码文件的内容粘贴到*机密内容*字段。
6. 单击*创建机密*。

![](img/index-297_1.jpg)

**图 8-17.** 为钱包文件创建机密的示例

按照相同的过程为您本地计算机上钱包文件夹中的所有文件创建机密。

当我们创建钱包时，我们为其定义了一个*密码*。此密码也应作为机密存储。按照本章前面为 AnomalyDetectionUser 密码执行的相同过程，创建一个机密。

现在，您的机密列表应类似于图 8-18。等待所有机密处于活动状态，以便可供使用。

![](img/index-298_1.jpg)
![](img/index-298_2.jpg)

**图 8-18.** 机密列表

一个机密可以有多个*版本*。例如，如果密码更改，您也需要更新机密。这可以通过创建机密的新版本来完成。从机密列表中选择该机密。在*机密详细信息*页面中，从*表范围*中选择*版本*，如图 8-19 所示。单击*创建机密版本*。更新数据（例如密码），然后单击*创建机密版本*。

**图 8-19.** 显示机密版本的机密详细信息页面

您还可以为机密定义规则。有两种规则类型：

1. *机密重用规则*：防止在机密的不同版本之间重用机密内容。有两个配置选项：*对已删除的机密版本强制执行*和*不对已删除的机密版本强制执行*。
2. *机密过期*：定义版本的过期时间间隔，以及内容检索是否在过期时被阻止。

![](img/index-299_1.jpg)

要定义规则，请在“**机密详细信息**”页面上选择“*规则*”作为“*表范围*”。单击“*编辑规则*”。然后从列表中选择规则类型，如图 8-20 所示，并定义规则的配置。

**图 8-20.** 编辑机密的规则

同时确保用户有权访问已创建的 Vault 及其内容。策略可能如下所示：

允许动态组 AnomalyDetectionDynamicGroup 读取 compartment Heli 中的 secret-family

如果您的 Autonomous Database 配置了*私有端点*，则需要为异常检测服务配置私有端点。导航至异常检测中的私有端点，如图 8-21 所示，单击*创建私有端点*，填写所需信息，然后单击*创建*。

![](img/index-300_1.jpg)

**图 8-21.** 私有端点列表及创建新私有端点的按钮

在向 OCI 创建不同类型的对象时，您通常需要选择“正确”的 compartment。此决策很重要，因为它会影响例如云租户的架构、安全性和可维护性。在此示例中，我们为常规围墙信息创建了机密到 Compartment Heli，并将特定于异常检测的机密创建到 AnomalyDetection Compartment。您最终可能会得到不同的解决方案。

## 异常检测服务

异常检测服务由*项目*、*模型*、*数据资产*和*检测*组成。

*项目*是用于组织数据资产、模型和检测的工作空间。*数据资产*是一种抽象的数据格式，包含用于模型训练的实际数据集的元数据。数据资产可以从位于 Oracle Object Storage、Oracle Autonomous Transaction Processing 或 InfluxDB 中的数据创建。*模型*是一个机器学习模型，它已使用数据资产进行训练，以检测时间序列数据中的异常。大多数参数由服务调整；只有少数参数留给用户定义。当模型成功训练后，它会自动部署到云环境中。然后，该模型可用于从新数据集中*检测*异常。一个项目可以有多个数据资产和多个模型。

## 数据要求

使用异常检测服务时，对数据集有严格的要求，因为它是一个自动化的异常检测过程。这些要求使得模型能够学习什么是“正常”，这是一种识别异常数据的能力。数据集的内容必须非常好：

• 训练数据集不应包含异常。
• 所有不同的典型业务场景都应包含在训练数据集中。

异常检测服务使用两种类型的机器学习内核：*多变量*和*单变量*内核。前者用于多变量相关信号，后者用于单变量独立信号。单变量异常检测在每个单独的指标中查找异常，而多变量异常检测为系统中的所有指标构建单个模型。单变量内核一次只处理一个信号，因此无法处理多个信号之间的集体异常。

异常检测服务的核心算法使用多变量状态

# 第 8 章 异常检测

估计技术（`MSET`）和一种称为 `MSET2` 的多变量异常检测算法。此算法由 Oracle Labs 创建。使用 `MSET2` 算法时，对数据集的要求是信号之间的相关性应相对较高。多变量内核会排除相关性较低的信号，并切换到单变量内核以构建所有单变量模型。单变量内核使用基于窗口的特征工程方法；因此，为了学习模式或推断异常，它需要在实际数据之前额外有一个窗口大小的数据训练或检测数据。默认情况下，窗口大小为 20 个时间戳。时间戳的最小总数为 80。单变量模型不会检测小于窗口大小的数据片段中的异常，因此当数据集大小是窗口大小的倍数时，可获得最佳结果。单变量核无法在异常检测服务中单独调用。它仅由多变量核自动调用。

异常检测服务支持包含*时间序列*数据的 `CSV` 和 `JSON` 文件格式：时间戳和数值属性。该服务还支持来自自治事务处理数据库和 InfluxDB 的数据。这些数据源在数据集内容方面有类似的要求。数据集中的每一行代表在给定时间戳对属性的一个*观测值*。通常，第一列包含时间戳，其后是其他数值属性。这些其他属性通常是传感器或信号数据。

**注意**：属性（时间戳除外）只能包含*数值*。所使用的算法无法理解其他数据类型。如果数据集中需要布尔标志值，则必须将其转换为数值。

时间戳属性是可选的。但是，如果为某一行提供了时间戳，则必须为所有行提供时间戳。时间戳属性必须命名为 `timestamp`（全小写，无空格），并且必须是数据集的第一个属性。数据集中的时间戳必须按递增顺序排序，并且不允许重复。时间戳可以有不同的频率。例如，1 小时内 100,000 个观测值，下一小时 10 个观测值。时间戳必须遵循 ISO 8601 格式。最佳实践是使用精确到秒或毫秒的时间。

数据集中的属性还有其他一些要求。属性名称必须*唯一*。属性数量上限为 300。属性的值可以缺失，但在这种情况下，必须表示为 `null`。整行不能所有值都缺失；至少有一个属性必须有值。任何属性都不能命名为 `MSET`。同样重要的是，为数据集选择的属性必须恰当：它们应涵盖业务场景，且所有属性都具有完整的值范围。

## CSV 格式数据

`CSV` 格式数据必须由逗号分隔的行组成，第一行为标题，其他行为数据。每列代表一个属性数据；每行代表特定时间戳的一个观测值。如果数据中指定了时间戳，则第一列必须命名为 `timestamp`。允许使用 `null` 表示缺失值，仅允许数值数据类型值，并且数据按时间戳排序。最后一行不能是空行；它必须是一个观测值。

## JSON 格式数据

类似地，`JSON` 格式数据也必须仅包含时间戳和数值属性。请使用以下键：

```json
{
  "requestType": "INLINE",
  "signalNames": ["sensor1", "sensor2", "sensor3", "sensor4", "sensor5",
                  "sensor6", "sensor7", "sensor8", "sensor9", "sensor10"],
  "data": [
    { "timestamp": "2021-01-01T08:01:01.000Z", "values": [1, 5.2, 6, 1, 2.3, 4, 2, 3.2, null, 7] },
    { "timestamp": "2021-01-02T08:01:02.000Z", "values": [1, 2.2, 7, 1, 3.2, 3, 1, 2.3, 5, null] }
  ]
}
```

**注意**：在 `JSON` 文件中，缺失值应添加为不带引号的 `null`。

## 数据集要求

在模型创建和训练阶段，训练数据中的观测值/行数必须至少为 `8 * 属性数` 或 `80`（取两者中的较大值）。例如，对于 100 个属性，所需的最小行数为 `Max(8 * 100, 80) = 800` 行。对于 5 个属性，所需的最小行数为 `Max(8 * 5, 80) = 80` 行。

如果向数据集中添加了新属性或属性名称发生了更改，那么，必须重新构建模型以纳入新属性，以便在检测时考虑该属性。如果在训练过程中检测到某个属性是另一个属性的重复项，该属性会被自动丢弃。在为数据集选择属性时，请仅选择彼此*相关*且属于同一*系统或流程*的属性。确保这些属性在某种程度上相互关联至关重要。如果存在多个系统或流程，请使用描述该流程的数据集为每个流程构建单独的模型。

仔细选择数据集的属性还有另一个原因。如前所述，异常检测使用多变量方法，每增加一个属性都会引入该属性与所有其他属性之间的交互作用。因此，随着属性数量的增加，计算成本会迅速上升。

当从新数据集中检测异常时，检测负载的最大大小为 300 个属性或最多 30,000 个数据点。数据点的计算方式为属性数量 × 行数。此限制可能会发生变化，但请记住，负载可能存在限制。对于异常检测，数据质量良好至关重要，并且在允许的最大数据限制范围内，数据越多越好。

大多数情况下，如果模型创建或检测失败，原因在于未满足一个或多个要求。例如：
- 如果数据集缺少标题行，您会收到错误消息：“缺少由信号标签组成的标题行。标题行应包含‘时间戳’（如果存在）以及每个信号的标签。”
- 如果用于测试的数据集与训练数据集的标题不同，您会收到错误消息：“由于[检测数据不包含训练期间使用的所有信号，请提供以下信号：[temperature_1, temperature_2, temperature_3, temperature_4, temperature_5, pressure_1, pressure_2, pressure_3, pressure_4, pressure_5]]，渲染异常可视化时发生错误，请联系支持人员。”
- 如果标题缺失或与训练数据集中的标题不同，模型将找不到任何异常。“未检测到所选模型类型和检测数据的异常。”
- 或者，如果数据集没有足够的行，您会收到错误消息：“[训练数据的行数不足！]”
- 如果数据集包含过多数据点，您将收到的错误是：“由于[数据中的总数据点数超过最大数量限制！请确保检测数据中的总数据点数不超过 30000。信号名称的数量与信号数量不匹配。请为每个信号提供一个名称！]，渲染异常可视化时发生错误，请联系支持人员。”

创建和训练模型需要相当长的时间，因此在开始创建模型之前，检查数据并确保数据集符合要求可能是值得的。

用户无需理解内核或流程本身；该服务会自动优化结果：一个训练好的模型。但用户*必须*向流程提供满足要求的数据集。

## 创建机器学习模型并检测异常

要开始使用异常检测，请从 OCI 控制台导航菜单中选择*AI 与分析*，然后在*AI 服务*下选择*异常检测*。此时将打开*项目*页面。

创建模型首先要创建一个项目或选择一个现有项目。如果您无法创建项目，而是收到错误消息“！加载数据时出错”，则有两种可能的解释：
- 您选择了错误的区间。请选择正确的区间。
- 您没有异常检测资源的权限。请按照本章前面所述，请求管理员授予您权限。

要创建新项目，请选择正确的区间，然后点击*创建项目*，如图 8-22 所示。如果要在现有项目下创建模型，请从项目列表中选择项目名称。

![图 8-22](img/index-305_1.jpg)
![图 8-22](img/index-305_2.jpg)

从列表中选择正确的区间，为项目输入名称和描述，如图 8-23 所示。然后点击*创建*。该项目将出现在项目列表中。

![图 8-23](img/index-306_1.jpg)

点击项目名称，或点击右侧的三点菜单，然后选择*查看详情*。*项目详情*页面将打开（图 8-24）。您可以编辑项目、将其移至其他区间、为其添加标签或删除项目。

![图 8-24](img/index-306_1.jpg)

一个项目由*模型*、*数据资产*和*指标*组成，如图 8-24 所示。

第一步是找到训练过程所需的数据。然后，使用这些数据创建一个模型可用于训练的*数据资产*。数据资产可以通过从*项目详情*页面（图 8-24）选择*数据资产*并输入所需信息来单独创建。或者，也可以在创建和训练模型时创建。为此，请点击*创建并训练模型*。

第一步是选择用于模型创建和训练的数据资产。

**注意**：数据资产必须包含典型值；它不应包含任何异常值，因为此数据资产用于训练模型识别正常数据，以便能够理解什么是非正常数据。

您可以从列表中选择现有的数据资产（图 8-25），也可以创建新的数据资产。请记住，数据资产不仅仅是用于训练的数据文件；它还包含用于模型构建的元数据。这就是为什么必须首先创建数据资产。

![图 8-25](img/index-307_1.jpg)

在本示例中，我们决定创建一个新的数据资产，并选择*创建新的数据资产*。在*创建数据资产*页面（图 8-31）上填写所需信息：数据资产的区间、名称和描述，并选择数据资产的类型。类型可以是 Oracle 对象存储、Oracle 自治事务处理数据库或 InfluxDB。

如果您选择`Oracle 对象存储`，请选择存储桶以及该存储桶中的训练数据文件（`JSON`、`CSV`）。命名空间也会显示，但您无法更改它。

如果您选择`Oracle 自治事务处理数据库`作为数据源类型，则应输入以下数据：

• 数据库名称

• 表名称

• 用户名

• 凭证

• 配置

输入数据库名称、包含数据的表名称以及用于登录的用户名。添加所需的凭证。在此上下文中，凭证不仅仅指用户名和密码。在使用此服务与 ATP 数据源之前，您需要为区间创建保管库、加密密钥和密钥。如果您尚未创建它们，请按照本章前面所述的方法创建，或请管理员为您创建。在图 8-26 中，您可以看到 ATP 数据库所需的信息，在图 8-27 和 8-28 中，可以看到如何使用密钥来创建连接。在图 8-29 和 8-30 中，您可以看到配置是如何完成的。

![](img/index-308_1.jpg)
*图 8-26. 选择 ATP 作为数据资产的数据源*

![](img/index-308_2.png)
*图 8-27. 从 ATP 创建数据资产时定义第一组凭证*

![](img/index-309_1.jpg)
*图 8-28. 从 ATP 创建数据资产时定义其余凭证*

![](img/index-309_2.jpg)
*图 8-29. 为作为数据资产数据源的 ATP 定义第一组配置*

![](img/index-310_1.jpg)
*图 8-30. 为作为数据资产数据源的 ATP 定义其余配置*

您还可以附加一个私有端点来跨租户访问外部数据库。如果您配置了私有端点，请启用`附加私有端点`，然后从区间中的私有端点列表中选择它。

如果您选择 `InfluxDB` 作为数据源类型，请插入用户名、凭证、度量名称和 `URL`，并从列表中选择 `InfluxDB` 版本。如果您需要访问外部数据库，请选择“附加私有端点”来创建一个私有端点。

在我们的示例中，我们选择使用 `Oracle 对象存储`（图 8-31），并从存储桶中选择一个 `csv` 文件。选择训练数据后，单击`创建`，即可看到已选择的数据资产，如图 8-32 所示。

![](img/index-311_1.jpg)
*图 8-31. 从存储在 Oracle 对象存储中的 csv 文件创建数据资产*

![](img/index-311_2.png)
*图 8-32. 在继续模型创建和训练之前需要检查的数据资产信息*

单击`下一步`。现在，我们将使用所选的数据资产创建并训练一个模型。选择区间，并为模型定义名称和描述，如图 8-33 所示。然后，为`目标误报概率`和`训练分数比率`选择值。这些是用户需要定义的唯一参数。它们的默认值分别为：目标误报概率为 `0.01`，训练分数比率为 `0.7`。

*图 8-33. 定义模型创建的参数*

*误报概率 (FAP)* 定义了被标记为异常的时间戳百分比。如果已知实际业务场景中的异常百分比水平，则可以将 `FAP` 设置为与该水平相同。默认值（`0.01` 或 1%）被认为对许多场景相对合适。如果您将 `FAP` 定义得较高，则意味着误报的可能性较高。同时请注意，`FAP` 定义得越低，模型训练所需的时间就越长，并且存在永远无法达到目标 `FAP` 的风险。`FAP` 在每个信号级别进行计算，然后对所有信号取平均值，作为模型最终达到的 `FAP`。`FAB` 的计算方法是：将所有信号中的异常数相加，然后除以数据点总数（信号数量乘以时间戳数量）。`FAP = 每个信号中的异常数之和 /（信号数量 * 时间戳数量）`。该公式如公式 8-1 所示。

`信号数量`
∑
`异常数量`
*i*=1
∗
`信号数量 时间戳数量`

*公式 8-1.*

从公式中可以看出，允许模型学习的误报数量越多（每个信号的异常数量越高），`FAP` 就越高。

*训练分数比率 (TFR)* 指定用于训练模型的数据集的比例。例如，如果您将 `TFR` 设置为 `0.7`，则该数据集中 70% 的数据用于训练模型，30% 用于评估模型。这 30% 的数据也用于报告模型性能（例如 `FAP`）。定义训练分数比率是强制性的。该值必须在 `0.5` 到 `0.8` 范围内：至少 50% 的数据必须用于训练，但不得超过 80%。

好的，作为一名高级文档工程师和翻译员，我将遵循您提供的注意事项和示例，将给定的英文文本翻译成中文。

插入所有参数后，点击`下一步`。查看图 8-34 中的`训练数据` `信息`和`模型信息`。如果一切正确，点击`创建`来创建并训练模型。如果需要修改某些内容，请点击`上一步`。

*图 8-34. 创建模型前需审查的模型信息*

![](img/index-314_1.jpg)

创建和训练模型需要一些时间。在此过程中，您会看到模型处于`创建中`状态。当过程完成时，状态会更新为`活动`。其他可能的状态包括`删除中`、`已删除`和`失败`。

失败最常见的原因是没有遵循本章前面所述的一个或多个数据集要求。

要调查模型，可以从模型列表中点击模型名称选择它，或者选择右侧的三点菜单并选择`查看详细信息`。在`模型详细信息`页面，您可以查看为模型定义的常规信息、模型信息和标签。`常规信息`（图 8-35）包括与 `OCI` 相关的信息。您可以通过点击`编辑`按钮更改名称和描述，并通过`移动资源`按钮将模型移动到另一个 compartment。您可以使用`删除`按钮删除模型。

*图 8-35. 模型详细信息页面*

*模型信息*（图 Model3）包括关于模型创建的信息。它显示了创建模型时使用的`目标误报概率`和`训练分数比率`的值，以及模型的`模型误报概率`和`多变量误报概率`。如果您想调查模型详细信息，请点击`模型详细信息`旁边的`下载`链接，将模型详细信息下载到一个 `JSON` 文件中。此文件名为 `details_result.json`。您可以在图 8-36 中看到文件内容的摘录。

![](img/index-315_1.png)
*图 8-36. details_result.json 文件摘录*

要调查标签，请选择`标签`选项卡（图 8-37）。模型创建会自动添加两个标签：`CreatedBy` 和 `CreatedOn`。您可以通过点击`添加标签`按钮来添加更多标签。

![](img/index-316_1.jpg)
*图 8-37. 模型详细信息页面上的模型信息。模型详细信息页面还包含常规信息和标签选项卡*

现在模型已成功创建并训练，它可以用于新的数据集来检测其中的异常。为此，请点击`检测异常`。然后，通过将文件拖放到框中，或点击`选择文件`并从本地文件夹中找到正确的文件（图 8-38），来选择要检查可能异常的文件。文件格式可以是 `JSON` 或 `CSV`。然后点击`检测`。

*图 8-38. 使用 JSON 文件检测异常*

**注意** 用于异常检测的数据，其标题中的数据标签必须与训练数据完全相同。换句话说，列名和列数必须与原始训练数据匹配，但文件可以是另一种文件类型。

![](img/index-317_1.jpg)

可能的异常会以图表形式显示。在图表中，水平轴代表时间戳或索引，垂直轴代表信号值、度量值。每个信号或度量值都显示在各自的子图中。在每个子图中，橙色线表示信号的实际输入值，紫色线表示机器学习模型预测的估计值。红色线突出显示在该时间戳检测到的异常。如果实际值与预测值之间的差异足够大，则检测到异常并用红色标记。

在图 8-39 中，您可以看到 `Measure_1` 在一个时间戳上可能存在的异常。将鼠标移到图表上，可以看到图表右上角显示特定时间戳的实际值和估计值。如果模型检测到异常，并不一定意味着存在异常。可能是训练数据集没有涵盖流程的所有正常情况，因此将此情况视为异常。如果是这种情况，请创建一个也包含此情况的新训练数据集，并使用新数据集训练模型。

*图 8-39. 检测到的异常图表*

在图 8-40 中，您可以看到一个估计值远高于实际值的示例。因此，该时间戳上可能存在异常。

![](img/index-318_1.jpg)
*图 8-40. 估计值远高于实际值的可能异常*

![](img/index-318_2.jpg)

在图 8-41 中，您可以看到一个实际值远高于估计值的示例，因此，该数据点上可能存在异常。

*图 8-41. 估计值远低于实际值的可能异常*

默认情况下，所有变量都显示为图表，每个变量在其自己的图表上。但是，通过选择`选择用于可视化的列标签（带异常）`（图 8-42）并选择一个或多个变量，您可以限制图表仅显示那些可能存在异常的变量。

![](img/index-319_1.jpg)
*图 8-42. 过滤用于异常可视化的列标签或将检测到的异常下载到 JSON 文件*

![](img/index-319_2.jpg)

## 第 8 章 异常检测

图表集中的最后两个子图是`聚合异常分数`和`每个信号的异常分数`。您可以在图 8-43 中看到这些图表的示例。聚合异常分数通过综合考虑所有信号的异常，指示给定时间戳上异常的显著程度。分数越高，显著性越大。每个信号的异常分数指示单个信号在给定时间戳上的显著性及其分数。如果多个信号在同一时间戳出现异常，它们都会显示其分数。当然，并非所有信号都会同时标记异常。

**图 8-43.`聚合异常分数`和`每个信号的异常分数`图表**

您还可以从`选择可视化信号模型`中选择可视化的模型。选项包括全部、单变量和多变量。默认值为全部。如果像图 8-44 中那样选择`单变量`，则会显示使用单变量模型发现的异常；如果选择`多变量`，则会显示使用多变量模型发现的异常。

![](img/index-320_1.jpg)

**图 8-44.`为图表选择的单变量信号模式`**

异常不会永久保存，并且一次只能看到一个异常分析。创建异常分析非常快，因此如果需要，您可以随时重新创建。如果要将异常数据保存到文件中，请点击图 8-42 中所示的`下载 JSON`按钮，将结果下载到 JSON 文件中。该文件名为`anomalies.json`。您可以在图 8-45 中看到此 JSON 文件的摘录。如您所见，JSON 文件包含一个按时间戳分组的异常数组。每个时间戳可以包含由单个信号或多个信号生成的异常。由一个信号生成的异常包含信号名称、实际值、估计值和异常分数的元组。异常分数指示异常的显著性。此分数其值范围在 0 到 1 之间。每个时间戳还包含一个归一化分数，该分数结合了该时间戳内单个或多个告警信号的重要性评分。

这些信息对于利用异常分数自动化执行业务工作流以立即采取行动非常有价值。例如，每个识别出的异常的估计值使得评估异常发生的严重程度成为可能。或者，特定信号的异常分数能够评估不同信号间异常的相对严重性。此外，随时间累积的异常聚合分数可以判断异常是否随时间推移而变得更加严重。

![](img/index-321_1.png)

![](img/index-321_2.jpg)

**图 8-45.`该 JSON 文件 anomalies.json 的摘录`**

### 指标

您可以在`项目详情`页面的`资源`下找到`指标`，如图 8-24 所示。您也可以在`模型详情`页面的`资源`下找到`指标`，如图 8-46 所示。这两种指标的区别在于：在项目详情中，您可以看到模型训练的指标；而在模型详情页面中，您可以看到检测的指标。

**图 8-46.`查看项目和模型的指标`**

![](img/index-322_1.jpg)

您可以定义`开始时间`和`结束时间`。通过`快速选择`列表，您可以轻松选择，例如`最近 7 天`。然后开始时间会自动更改为七天前。

指标显示模型的五个指标：`失败`、`成功`、`成功率`、`延迟`和`总计`。在这五个指标窗格中，每个窗格都有一个“i”符号，用于获取有关特定指标的更多信息。

此外，在每个窗格中，您可以定义`时间间隔`和使用的`统计`方法。时间间隔可以是自动、一分钟、五分钟、一小时或一天。统计方法可以是比率、总和、平均值、最小值、最大值、计数、P50、P90、P95、P99 或 P99.9。您可以将光标悬停在屏幕上方，以查看某个时间点所有指标的情况。在图 8-47 中，您可以看到其中四个指标的示例。

**图 8-47.`模型训练的四个指标示例`**

如果您单击其中一个指标窗格，它会展开，您可以更详细地查看。您可以在图 8-48 中看到此操作的示例。您可以更改时间间隔或统计方法，或者使用`调整 X 轴`滑块来调整时间线上的指标。用鼠标抓住滑块的末端并移动滑块；X 轴会随之调整。从`选项`列表中，您可以选择`在指标资源管理器中查看查询`、`复制图表 URL`、`复制查询 (MQL)`、`为此查询创建告警`或`表格视图`。

![](img/index-323_1.jpg)

![](img/index-323_2.jpg)

**图 8-48.`一个指标，调整 X 轴并查看选项`**

如果您选择`在指标资源管理器中查看查询`，则会打开指标资源管理器（如图 8-49 所示）。您可以详细探索指标，例如调整 X 轴和 Y 轴。或者，您可以使用`查询编辑器`编辑查询。基于编辑内容，您可以更新图表或创建`告警`。您还可以使用`OCI 仪表盘`可视化查询。

**图 8-49.`指标资源管理器`**

如果您选择`复制图表 URL`，图表的 URL 将被复制到内存中，您可以将其粘贴到任何需要的地方。`复制查询 (MQL)`会复制查询，以便您可以将其粘贴到需要的地方。

`为此查询创建告警`会将您带到`创建告警`页面，您可以在其中为该指标创建告警。`表格视图`显示相同的查询结果，但以表格数据形式呈现。要切换回图表，请从`选项`中选择`图表视图`。

指标仅对活跃模型和具有相应权限的用户可用。如果您看不到任何指标，您可能缺少相应权限。请创建适当的策略来授予这些权限。策略示例如下：

允许组 AnomalyDetectionGroup 在 Heli 区间内使用指标

另请注意，要查看告警，您也需要权限。策略示例如下：

允许组 AnomalyDetectionGroup 在 Heli 区间内使用告警

当然，所定义的策略取决于具体的权限需求。请务必仔细考虑是否确实需要该权限，如果需要，则应在正确的层级进行定义。

### 使用 OCI API 通过 APEX 进行异常检测

#### 异常检测 API

在编写本书时，异常检测 API 的版本为`/20210101`。异常检测 API 的文档可在此处找到：

https://docs.oracle.com/en-us/iaas/api/#/en/anomalydetection/20210101/

该文档包含 API 参考和所有可用的 API 端点。API 参考涵盖了`DataAsset`、`DataAssetCollection`、`Model`、`Project`、`WorkRequest`、`WorkRequestError`、`WorkRequestLogEntry`和 Data Types。这是一个相当新的服务，因此 API 也在不断演进。

#### 为异常检测 API 设置 APEX

首先，如果您尚未在 OCI 控制台中创建 IAM `用户`，请先创建。将该用户添加到用于异常检测的`组`中，在我们的示例中为 AnomalyDetectionGroup。

然后，返回用户的详细信息页面，确保该用户已选择`API 密钥`。为此，请单击`编辑用户功能`，并确保选中 API 密钥。为了限制用户仅使用 API 密钥与 OCI 资源交互，请取消选中所有其他选项。

要使用 OCI REST API，需要一对 API 密钥。获取 API 密钥对的步骤如下：

1. 在用户详细信息页面的左侧选择`API 密钥`。

2. 单击`添加 API 密钥`。

3. 选择`生成 API 密钥对`。

4. 单击`下载私钥`。

5. 单击`添加`。

您刚刚下载的文件是 pem 类型，内容类似于：

```
-----BEGIN PRIVATE KEY-----
MIIEvAIBADANBgkqhkiG9w0BAQEFAASCBKYwggSiAgEAAoIBAQCv2FWr73tHBsrA
VhqKE/WfOrzE6WzaYDzgAycr2SGOa3bQrkXx31uo0O9Rk2GHy71Zl+SzwF0Qz3Bh
+HTDlaSC1FZSHZ/9DAUMidAjSpvMBnPAcIKGmmECgYASx9EM7tdWvhD3kpN9uBuc
r7ZBCU/O1mxeIm7eNlbCbLCfGsVhP4Q8v1HKQZz54y9YMRZW8eO3YEHtwwRJVegz
8n+yYWYa/IMLLq1i8ZRqhg==
-----END PRIVATE KEY-----
```

删除第一行（`-----BEGIN PRIVATE KEY-----`）和最后一行（`-----END PRIVATE KEY-----`），然后将内容复制到安全的地方。这就是您将用于 Web 凭据的`私钥`。查看`配置预览`以获取在 APEX 中创建 Web 凭据所需的其他详细信息。您需要的信息包括：

• 用户 OCID
• 指纹
• OCI 私钥
• 租户 OCID
• 区间 OCID
• 租户的区域
• 命名空间

然后，以您希望其有权访问异常检测 API 的用户身份登录 APEX。创建一个新的 Web 凭据。

1. 转到`工作区实用程序`。

2. 选择`Web 凭据`。

3. 单击`创建`。

4. 为 Web 凭据指定一个`名称`，并选择`Oracle Cloud Infrastructure (OCI)`作为`身份验证类型`。

5. 将 OCI 用户 ID（用户 OCID）、OCI 私钥、OCI 租户 ID（租户 OCID）和 OCI 公钥指纹粘贴到正确的字段中。

6. 单击`创建`。

**注意** 如果您没有所有信息，可以随时返回 OCI 控制台查看“身份与安全”部分，或者询问管理员。

#### 使用 APEX 调用异常检测 API

# 第 8 章 异常检测

创建好 *Web 凭据*后，您就可以使用 OCI API 服务了。例如，您可以创建一个 *REST 数据源*来使用它们。我们将创建一个数据源，用于查看区间内所有可用的模型。

1.  在 *APEX 应用程序生成器*中，选择您要创建此 REST 数据源的应用程序。
2.  选择*共享组件*。
3.  导航到*数据源*并选择 *REST 数据源*。
4.  单击*创建*。
5.  选择*从头开始*，然后单击*下一步*。
6.  将 *REST 数据源类型*选择为 *Oracle Cloud Infrastructure (OCI)*。
7.  为 REST 数据源指定一个*名称*。
8.  定义 URL 端点（请查阅 Oracle 文档以找到正确的端点）。在我们的示例中，端点为 [`anomalydetection.aiservice.us-phoenix-1.oci.oraclecloud.com`](https://anomalydetection.aiservice.us-phoenix-1.oci.oraclecloud.com)。
9.  点击 *下一步*。
10. 选择*新建*作为*远程服务器*（如果您已使用过异常检测服务，请从列表中选择该服务器）。
11. *基础 URL* 应指向上一屏幕中选择的端点。
12. 为*服务 URL 路径*选择所需的 API 服务。在我们的示例中：`/20210101/models/`。
13. 点击 *下一步*。
14. 启用*需要身份验证*。
15. 从列表中选择*Web 凭证*。
16. 点击 *高级*。
17. 选择*查询字符串*变量作为*参数类型*，输入 `CompartmentId` 作为*参数名称*，并将 compartment 的 OCID 粘贴到*值*字段中。启用*静态*。
18. 点击 *发现*。
19. 如果一切设置正确，列表将显示该 compartment 中的所有模型。
20. 点击 *创建 REST 数据源*。

现在，您可以使用该 REST 数据源创建一个页面，列出此 compartment 中异常检测服务的所有模型。例如，您可以选择一个模型的 OCID，并创建一个 APEX 应用程序，使用该模型从您的数据集中检测异常。

在图 8-50 中，您可以看到一个非常简单的 APEX 应用程序示例，该应用程序将 compartment、项目和模型的 OCID 以及包含数据的文件作为输入。点击“检测”即可调用 API 检测异常。

![](img/index-328_1.jpg)

*图 8-50. 一个调用异常检测服务 API 检测异常的简单 APEX 应用程序示例*

## 总结

异常检测是一种机器学习方法，用于识别与常态显著不同的意外事件、观测值或项目。通常，机器学习和数据科学被视为非常专业的领域，许多开发者对于开始学习数学、统计学、算法等感到不适应。异常检测服务使得无需数据科学专业知识即可使用异常检测。Oracle 云基础设施 (OCI) 异常检测服务是一种托管式 AI 服务，使用户能够轻松构建特定于业务的异常检测机器学习模型，以标记关键事件，然后使用这些模型识别其业务数据中的异常。事件检测得越早，就能越早得到修复。异常检测服务还提供专门的 API，以便在 OCI 控制台之外使用这些服务。有限的资源通常是机器学习的瓶颈，尤其是在训练模型时。在 OCI 中，您可以按需扩展。

# 第 9 章 语言

语言是一种 AI 服务，用于使用预训练的机器学习模型执行不同类型的文本分析。该服务使机器学习的应用变得简单易行。

您可以使用语言服务进行：

• 语言检测
• 文本分类
• 关键短语提取
• 命名实体识别
• 情感分析

这些预训练模型由 Oracle 定期重新训练和监控。

您可以通过 OCI 控制台、REST API、SDK 或 CLI 访问 OCI 语言服务。目前，语言服务并非在所有区域都托管，因此要使用它，请务必检查它是否托管在您计划使用的区域中。目前，它仅在商业区域可用。语言检测模型支持超过一百种语言；其他模型主要支持英语，但每天都在学习其他语言。

该服务不检查或修正语法或拼写错误。语法或拼写错误可能导致模型性能不佳或预训练模型产生意外结果。

## 语言服务的不同用例

自然语言处理（NLP）是人工智能和语言学的一个子领域。它使计算机能够理解、解释和操控人类语言。NLP 有多种应用，包括 OCI 语言服务所提供的那些。OCI 语言服务仅针对书面文本使用 NLP。

© Adrian Png 和 Heli Helskyaho，2022 年

A. Png 和 H. Helskyaho，《使用 Oracle 云功能扩展 Oracle Application Express》，[`doi.org/10.1007/978-1-4842-8170-3_9`](https://doi.org/10.1007/978-1-4842-8170-3_9#DOI)

语言服务中提供了五种预训练的机器学习模型：语言检测、文本分类、关键短语提取、命名实体识别和情感分析。

在 OCI 控制台上，它们会同时被自动调用，你可以看到所有模型的结果。但是，在使用 API 接口时，你可以决定调用哪些服务以及调用的顺序。你可以只调用一个，也可以按顺序调用多个。例如，当从 API 调用文本分类服务时，你需要提供语言代码。这意味着你可以先调用语言检测服务来找出文本的语言，然后利用该信息再调用文本分类。

### 语言检测

语言检测用于检测输入文本的语言，并给出检测的置信度分数。置信度分数的范围是 0 到 1；越接近 1，置信度越高。语言检测在许多业务场景中都非常有用。它可以用于自动化许多业务流程，例如客户反馈流程。处理反馈的第一步是检测语言，然后基于该语言进行情感分析，或使用该语言的关键短语提取来检测反馈中的关键词。甚至可以用正确的语言自动或手动回复客户。由于有了语言检测步骤，我们现在知道负责回复客户的客户满意度员工需要具备哪种语言技能。

另一个例子是客户支持互动。在客户支持互动中，能够自动检测客户使用的语言，然后基于该信息引导客户服务流程，这非常有价值。能够用客户自己的语言帮助客户可以提高客户满意度，并使客户支持流程更高效。聊天机器人可以自动切换到正确的语言，用户手册会自动以客户所说的语言提供，或者会自动选择会说所需语言的客服人员来服务客户。其他一些可能的用例包括根据语言自动对文档进行分类，或者将来电或客户支持工单路由到本地化团队。

![](img/index-331_1.png)

### 文本分类

文本分类用于识别文档的类别和子类别。它可以识别超过 600 个类别，并且每天都在学习识别更多类别。文本分类可用于自动化业务流程等场景。它可以用于将一组数据划分为发票或食谱等类别。或者用于自动化处理各种金融文档。图 9-1 中有一个文本分类的示例。该文本是 Heli Helskyaho、Jean Yu 和 Kai Yu 所著书籍《面向 Oracle 数据库专业人员的机器学习：部署模型驱动应用与自动化流水线》描述的一部分。文本分类将其归类为 *计算机与电子/编程*，置信度为 1.0000。

*图 9-1. 语言检测和文本分类示例*

### 关键短语提取

关键短语提取可从文本中提取最相关的关键词或短语。它可用于总结文本内容或识别文本的主要主题。

关键短语由文本中的主语和宾语组成。与这些主语或宾语相关的任何修饰语（形容词）也会被纳入候选关键短语中。初始化候选短语后，每个候选短语的重要性会通过统计方法进行衡量。

![](img/index-332_1.png)

非必要的候选短语会从选定的关键短语中移除。每个关键短语的置信度分数表示该过程对其准确性的把握程度。置信度分数的取值范围为 0 到 1。

在图 9-2 中，你可以看到一个关键短语提取的示例。所使用的文本摘自由 Heli Helskyaho、Jean Yu 和 Kai Yu 合著的《Oracle 数据库专业人员的机器学习：部署模型驱动的应用与自动化流水线》一书的描述部分。

关键短语提取从文本中找到了多个关键词或短语，并且对所有这些结果几乎都达到了 100%的置信度。

*图 9-2. 关键短语提取示例*

### 命名实体识别

命名实体识别（NER）可检测文本中的命名实体，并将其分类为预定义的类别，例如人物、产品、日期、语言、地点、货币、组织等。

例如，让我们选取 Adrian Png 和 Luc Demanche 合著的《Oracle 云免费套餐入门：使用始终免费资源创建现代 Web 应用》一书描述中的一小段，看看 NER 能从中发现什么。

# 第 9 章 语言

## 命名实体识别

在图 9-3 中，你可以看到它从文本中找到了多个产品、组织以及一个数量/数字元素。它用颜色标记找到的实体，例如，产品类别的实体用红色标记，并显示实体类别名称。同时，它还显示了判断的置信度。例如，在文本的一部分中，“Oracle”被定义为产品，置信度为 0.5991；而在另一部分中，它被定义为公司，置信度为 1.0000。这意味着该服务并非仅仅使用一个单词列表并根据列表来定义类别；相反，它使用更复杂的算法来理解单词的上下文，并进行更精细的分类。NER 并非易事，错误也时常发生。例如，语法错误或拼写错误都可能导致问题与误分类。

![](img/index-333_1.jpg)

***图 9-3.** 命名实体识别示例*

## 情感分析

**情感分析**包括基于方面的情感分析（`ABSA`）和句子级情感分析。`ABSA` 从给定文本中识别出各个方面，并将每个方面分类为积极、消极、混合或中性倾向。它还为每个分类定义一个置信度分数。每个类别的置信度分数范围在 0 到 1 之间。所有四个类别的分数总和为 1。句子级情感分析则分析整个句子，并为文本中的每个句子给出置信度分数。

在图 9-4 中，你可以看到一位读者对《真实世界的 SQL 与 PL/SQL：专家建议》一书（作者为 Nanda、Tierney、Helskyaho、Widlake 和 Nuijten）评论的情感分析。基于方面的情感分析从文本中提取各个方面，并为每个方面定义一个情感类别，同时为每个判断定义置信度。然后，利用这些分析，它定义文档情感及其置信度。在我们的示例中，这条评论被定义为*混合*，置信度为 0.5680。情感分析部分的下方区域将文本中的完整句子分类到各个类别中。例如，第一个句子被归类为*中性*，置信度为 0.6251；最后一个句子被归类为*消极*，置信度为 0.7913。

![](img/index-334_1.png)

***图 9-4.** 情感分析示例*

情感分析在许多业务场景中非常有用。例如，你可以利用情感分析来追踪社交媒体，检测人们关于你的产品或品牌的评论是正面还是负面。或者，你可以自动监控客户反馈。然后，根据情感倾向，将其连同所需的优先级一起分派给公司内相应的团队。

## 设置服务

要开始使用语言服务，你需要一个拥有服务管理员角色权限的 Oracle 云基础设施（`OCI`）账户，或者由能够设置服务并授予你所需权限的人来操作。你还需要一个受支持的浏览器。请查阅 `OCI` 文档中关于受支持浏览器的信息。

如果你尚未创建，请创建一个 `OCI` 用户。该用户可以是联合用户或 `IAM` 用户。联合用户意味着用户认证由身份提供商完成。在本示例中，我们将创建一个 `IAM` 用户。

从 `OCI` 控制台导航菜单中选择*身份与安全*，然后选择*用户*。点击*创建用户*，并填写如图 9-5 所示所需数据。点击*创建*。

在本示例中，我们创建一个名为 `LanguageUser` 的 `IAM` 用户。

![](img/index-335_1.jpg)

***图 9-5.** 创建用户*

默认情况下，只有管理员组中的用户才能访问服务资源。这也适用于语言服务资源。因此，你需要为新创建的用户授予访问所需资源的权限（策略）。可以直接向用户添加所有策略，但最佳实践是创建一个用户组，将策略分配给该组，然后将用户添加到用户组中。

遵循这一实践，创建一个用户组。从*身份与安全*中选择*组*，然后点击*创建组*。输入组名称（例如，`LanguageGroup`）和描述，如图 9-6 所示。点击*创建*。

![](img/index-336_1.jpg)

![](img/index-336_2.jpg)

***图 9-6.** 创建用户组*

**注意** 如果你使用的是身份云服务提供商（`IDCS` 提供商），例如 Oracle 身份云服务（`OICS`），请在联合认证下创建组并分配用户。

然后，将用户添加到用户组。从组列表中选择 `Language` 用户组。在*组详细信息*页面，导航到*组成员*部分，点击*将用户添加到组*。从*用户*列表中选择该用户（图 9-7），然后点击*添加*。

***图 9-7.** 将用户添加到用户组*

下一步是添加所需的策略。有两种资源类型：*聚合*和*单个*。这意味着你可以使用聚合资源类型一次性授予所有类型语言服务的权限，或者使用单个资源类型将权限限制为一种或多种语言服务类型。要使用聚合类型授予权限，请使用资源类型 `ai-service-language-family`。单个资源类型包括：

• `ai-service-language-entities`

• `ai-service-dominant-language`

• `ai-service-language-sentiments`

• `ai-service-language-keyphrases`

• `ai-service-language-text-classification`

如果你更倾向于仅授予所需权限，而非聚合权限，则需要仔细选择应使用的单个资源类型。在表 9-1 中，你可以看到每个 `API` 操作以及调用该操作所需的、具有 `USE` 权限的资源类型。

***表 9-1.** `API` 操作及执行操作所需的资源类型*

| API 操作 | 资源类型 |
| :--- | :--- |
| `BatchDetectDominantLanguage` | `ai-service-dominant-language` |
| `BatchDetectLanguageentities` | `ai-service-language-entities` |
| `BatchDetectLanguageKeyphrases` | `ai-service-language-keyphrases` |
| `BatchDetectLanguageSentiments` | `ai-service-language-sentiments` |
| `BatchDetectLanguagetextClassification` | `ai-service-language-text-classification` |
| `DetectDominantLanguage` | `ai-service-dominant-language` |
| `DetectLanguageentities` | `ai-service-language-entities` |
| `DetectLanguageKeyphrases` | `ai-service-language-keyphrases` |
| `DetectLanguageSentiments` | `ai-service-language-sentiments` |
| `DetectLanguagetextClassification` | `ai-service-language-text-classification` |

单个策略的语法如下：

```
allow group <group-name> to use <resource type> in tenancy
```

例如：

```
allow group LanguageGroup to use ai-service-language-keyphrases in tenancy
```

聚合策略的语法如下：

```
allow group <group-name> to use ai-service-language-family in tenancy
```

在我们的示例中，我们将创建一个策略，如图 9-8 所示：

```
allow group LanguageGroup to use ai-service-language-family in tenancy
```

**注意** 由于语言服务不使用特定区间中的资源，因此您需要在**租户**级别创建策略。

![](img/index-339_1.jpg)

***图 9-8.** 创建用于使用语言服务的策略*

现在，用户已准备好使用语言服务。

## 使用 OCI 控制台分析文本

要开始使用语言服务，请从 `OCI` 控制台导航菜单中选择*分析与 AI*。然后，在*AI 服务*下选择*语言*。进入语言服务后，您可以看到两个选项：*概览*和*文本分析*（图 9-9）。概览页面包含大量指向手册、博客和其他资源的链接，以及关于该服务的通用信息。这是深入了解该服务的好地方。通过选择文本分析可以找到该服务。

![](img/index-340_1.jpg)

![](img/index-340_2.jpg)

***图 9-9.** `OCI` 控制台中的语言服务*

使用该服务非常简单：在*输入要分析的文本*框中输入文本，然后点击*分析*。*结果*部分将展开（图 9-10），您可以看到对刚输入的文本进行的所有不同分析。

***图 9-10.** 分析结果*

在我们的示例中（图 9-10），语言检测功能检测到语言为英语，置信度为 0.9987，并高置信度（1.0000）地将其分类为“计算机与电子/企业软件”。命名实体识别服务从文本中检测到多个实体（图 9-11），而关键短语提取则从文本中提取了多个关键词。每个实体或关键词识别都附有置信度。

![](img/index-341_1.jpg)

![](img/index-341_2.png)

# 第 9 章 语言

图 9-11. 使用 OCI 控制台进行命名实体识别和关键短语提取的示例

情感分析（图 9-12）首先判断文本总体上是正面、负面、混合还是中性。然后，它基于方面和每个句子分析情感。有趣的是，在我们的示例中，基于方面的情感分析发现“异常检测服务”是负面的。

图 9-12. 使用 OCI 控制台进行情感分析的示例，包括基于方面和句子级别的情感分析

这个示例对该服务来说可能太简单了，所以让我们用芬兰语（芬兰使用的一种语言）的文本测试一下。在图 9-13 中，我们有一段芬兰语文本，该服务在识别语言时非常自信。文本内容大致如下：

“这段文本是另一种语言，因为我想知道这个服务是否能理解

![](img/index-342_1.jpg)

![](img/index-342_2.png)

其他语言。我现在所在的国家被雪覆盖，因为现在是冬天。天气寒冷且相当阴暗。幸运的是，春天很快就要来了，然后是夏天。夏天是我最喜欢的季节。”

图 9-13. 芬兰语的语言检测

文本分类服务（图 9-14）尝试对文本进行分类。它对自己的判断并不自信，而且这种不自信是正确的，因为分析结果并不准确。关键词提取似乎能够以高置信度找到关键词。

图 9-14. 对芬兰语文本数据进行文本分类、命名实体识别和关键词提取

![](img/index-343_1.png)

![](img/index-343_2.jpg)

情感分析（图 9-15）判定该文本为中性。它没有找到任何情感倾向，这可以理解，因为它的芬兰语能力仍然有限。该语言服务相当新，并且正在不断改进。

图 9-15. 对芬兰语文本进行情感分析

您还可以通过点击*结果*部分中的*显示 JSON* 来以 JSON 格式显示结果。

您可以通过点击*下载*链接下载 JSON 文件，或者通过点击*复制*链接复制 JSON 文本（图 9-16）。

图 9-16. 以 JSON 格式查看语言服务结果。复制或下载内容到 JSON 文件

## 使用 OCI 语言 API 和 APEX 分析文本

您可以使用 OCI 语言 API 或 OCI 命令行界面 (CLI) 来分析输入文本。在本节中，我们将讨论 OCI 语言 API 以及如何从 APEX 中使用它。

### 语言 API

当前版本的 OCI 语言 API 参考和端点可以在此处找到：[`docs.oracle.com/en-us/iaas/api/#/en/language/20210101/`](https://docs.oracle.com/en-us/iaas/api/#/en/language/20210101/)

在撰写本书时，OCI 语言服务 API 包含以下用于批量处理的 POST 请求：

• `BatchDetectDominantLanguage`

• `BatchDetectLanguageEntities`

• `BatchDetectLanguageKeyPhrases`

• `BatchDetectLanguageSentiments`

• `BatchDetectLanguageTextClassification`

以及以下用于单个文档处理的 POST 请求：

• `DetectDominantLanguage`

• `DetectLanguageEntities`

• `DetectLanguageKeyPhrases`

• `DetectLanguageSentiments`

• `DetectLanguageTextClassification`

文档对这些 API 进行了详细描述，并且在 *OCI 语言 API 参考和端点*文档的*数据类型*部分，您可以找到每个 API 调用的参考信息。在数据类型部分，包含了属性的信息以及请求 JSON 文件的示例。

服务使用存在一些限制。大多数限制可以通过联系 Oracle 来避免。在撰写本书时，每个请求的字符数限制为 1000，每个租户的并发请求数限制为 5。超出这些限制将导致 400 API 请求错误。

![](img/index-345_1.png)

![](img/index-345_2.png)

### 为语言 API 设置 APEX

首先，请确保您已完成*设置服务*部分中说明的所有预备任务。

需要使用 API 密钥对才能使用 OCI REST API。首先，请验证语言服务的 OCI 用户是否已选择 API 密钥。转到*用户详细信息*页面（图 9-17），然后点击*编辑用户功能*。验证*API 密钥*是否已勾选，如图 9-18 所示。

图 9-17. OCI 控制台中的用户详细信息页面

图 9-18. 编辑用户功能

![](img/index-346_1.jpg)

![](img/index-346_2.png)

下一步是获取 API 密钥对：

1.  在用户详细信息页面的*资源*部分中，导航到*API 密钥*（图 9-19）。

图 9-19. 用户详细信息页面中的 API 密钥

2.  点击*添加 API 密钥*。

3.  选择*生成 API 密钥对*（图 9-20）。

图 9-20. 添加 API 密钥并下载私钥

4.  点击*下载私钥*。您之后将无法再访问此密钥，因此请务必将 `pem` 文件保存在安全的地方。

5.  点击*添加*。

6.  此时会显示**配置文件预览**。稍后创建连接时需要用到这些信息，但所有信息均可随时查阅。

您刚刚下载的文件类型为 `pem`，内容大致如下：

```
-----BEGIN PRIVATE KEY-----
MIIEvQIBADANBgkqhkiG9w0BAQEFAASCBKcwggSjAgEAAoIBAQCs1pNSW19iWdkx
4PvGo8PqfO0OpiQxAh5X7pe9WKOfmC44vTazvz8se0++DNs3Lz/oOu0kYLsmsPr1
fHQgXglEy7iF3y0VftYYnaaPT+qaI+Adahr6wZpjhmtk8lDoUZv1dTySbHu7nu5w
tyf+XmqkJ6Y7HbVWgsQEDcRKzlxfqV2m0R9XtBshwEEk2YC0PNERNb5KXDrHz00T
ZIY00St7YGWEpDPA+Qc9IJO/GCJ1kxq4sTZWvflZNzfoSrhK7UHOLsX1sqACFTn6
y9K+Y7T+8Pwr45AJHY/8luz/ujfSth4mfyK8RJAGEd/ahY0Z6cGeBDGWCZZDrc53
5K
-----END PRIVATE KEY-----
```

删除第一行（`-----BEGIN PRIVATE KEY-----`）和最后一行（`-----END PRIVATE KEY-----`），然后将内容复制到安全位置。这就是您将用于 Web 凭据的*私钥*。请检查*配置预览*，以获取在 APEX 中创建 Web 凭据所需的其他详细信息。您需要的信息包括：

• 用户 `OCID`

• 指纹

• OCI 私钥

• 租户 `OCID`

• compartment `OCID`

• 租户区域

• 命名空间

如果您已经关闭了*配置文件预览*，可以通过点击*查看配置文件*链接（图 9-21）在*用户详细信息*页面找到这些信息。

![](img/index-348_1.png)

图 9-21. 用户详细信息页面上的查看配置文件链接

然后，使用 APEX 用户凭据登录 APEX。

创建一个新的 Web 凭据。

1.  转到*应用程序生成器*和*工作区实用程序*。

2.  选择 *Web 凭据*。

3.  单击 *创建*。

4.  为 Web 凭据指定一个*名称*，并选择 *Oracle Cloud Infrastructure (OCI)* 作为*身份验证类型*。

5.  将 OCI 用户 ID（用户 `OCID`）、OCI 私钥、OCI 租户 ID（租户 `OCID`）和 OCI 公钥指纹粘贴到相应的字段中。

6.  单击 *创建*。

**注意** 如果您没有所有信息，可以随时返回 OCI 控制台并查看“身份与安全”部分，或者询问管理员。

现在，您已成功创建从 APEX 到 OCI 的工作连接。

![](img/index-349_1.jpg)

### 在 APEX 中使用 Language API

创建*Web 凭据*后，您就可以使用 OCI API 服务了。

要使用 OCI API 服务，例如，您可以创建一个*REST 数据源*。创建 REST 数据源的步骤如下：

1.  转到 *APEX 应用程序生成器*，选择您要创建此 REST 数据源的应用程序。

2.  选择 *共享组件*。

3.  导航到 *数据源*，然后选择 *REST 数据源*。

4.  单击 *创建*。

5.  选择 *从头开始*，然后单击 *下一步*。

6.  选择 *Oracle Cloud Infrastructure (OCI)* 作为 *REST 数据源类型*，如图 9-22 所示。

***图 9-22.** 创建 REST 数据源的常规部分*

7. 为 REST 数据源指定一个*名称*，并定义 *URL 端点*（请查阅 Oracle 文档以找到正确的端点）。参见图 9-22。在我们的示例中，端点是 [`language.aiservice.us-phoenix-1.oci.oraclecloud.com`](https://language.aiservice.us-phoenix-1.oci.oraclecloud.com)。

8. 单击 *下一步*。

![](img/index-350_1.jpg)

第 9 章 语言

9. 选择 *新建* 作为 *远程服务器*（如果您已经使用过 Language 服务，请从列表中选择该服务器）。*基本 URL* 应指向上一屏幕中选择的端点。在 *服务 URL 路径* 中键入所需的 API 服务。在我们的示例中：`/20210101/actions/detectDominantLanguage`。参见图 9-23。

***图 9-23.** 创建 REST 数据源的远程服务器部分*

10. 单击 *下一步*。

11. 启用 *需要身份验证*，并从列表中选择 *Web 凭据*，如图 9-24 所示。

![](img/index-351_1.jpg)

第 9 章 语言

***图 9-24.** 创建 REST 数据源的身份验证部分*

12. 单击 *高级*。

13. 选择 *查询字符串* 变量作为 *请求/响应正文*，然后在 *参数名称* 中输入“text”，如图 9-25 所示。接着，点击 *响应示例* 字段中的加号，并选择一个响应示例 JSON 文件。您可以从 Oracle 文档页面中找到该文件。

![](img/index-352_1.jpg)

![](img/index-352_2.jpg)

第 9 章 语言

***图 9-25.** 创建 REST 数据源的参数部分*

14. 点击 *发现*。

15. 查看如图 9-26 所示的详细信息。

***图 9-26.** 创建 REST 数据源的预览数据部分*

![](img/index-353_1.jpg)

![](img/index-353_2.jpg)

第 9 章 语言

16. 点击图 9-27 中的 *更多详情* 按钮。

***图 9-27.** 创建 REST 数据源的预览数据配置文件部分*

17. 调查图 9-28 中显示的所有选项卡：数据配置文件、操作、响应正文和响应标头。

***图 9-28.** 创建前预览 REST 数据服务*

第 9 章 语言

18. 点击 *创建 REST 数据源*。

现在，您已创建了一个用于检测给定文本语言的 POST REST 数据源。创建其他 REST 数据源的过程类似，并且在 APEX 中使用 REST 数据源非常简单。

**总结**

OCI 语言服务是 OCI 中托管 AI 服务的一部分。它用于文本分析。该服务由复杂的机器学习元素组成，这些元素对用户不可见，因此即使对机器学习和数据科学知之甚少，也很容易使用。这些预训练模型由 Oracle 定期重新训练和监控。

OCI 语言服务可以通过 OCI 控制台使用，也可以与 REST API、SDK 或 CLI 一起使用。在本章中，我们了解了如何从 OCI 控制台使用它，以及如何通过 REST 数据源在 APEX 中使用它。语言服务可用于语言检测、文本分类、关键短语提取、命名实体识别和情感分析。这是一项相当新的服务。它一直在改进，并且添加了更多功能。

**第 10 章 视觉**

人脑处理视觉和听觉的能力使我们能够与他人和环境互动。几乎每个计算设备——台式机、笔记本电脑和移动设备——都有一个显示器、麦克风和扬声器，用于通过用户的眼睛、嘴巴和耳朵与人类用户进行交互。迄今为止，它们是人类五种感官中仅有的两种在机器中被人工复制的感官。其余三种——嗅觉、味觉和触觉——涉及复杂的生物化学和物理化学过程，鉴于当今科学和技术的进步，这些过程不易复制。存在一些解决方案，但大多仅存在于实验设备中。

如今，日常计算机被设计为接受和产生二进制输入和输出。例如，要执行命令，用户在键盘（一种输入设备）上键入，该设备提供计算机要处理的指令。该过程的结果随后通过显示器或声音设备反馈给用户。

计算机的响应是数字生成的；然而，当它离开设备时，它使用模拟信号传递给用户。人眼和耳朵接收这些模拟输入，然后通过大脑进行处理。后者随后根据这些处理过的信号做出决策。计算机能否像人类一样对视觉和声音做出反应？答案是肯定的。

图像和语音识别任务是*人工智能*（AI）和*机器学习*（ML）最普遍的应用之一。*文本转语音*（TTS）软件已经存在相当长一段时间，常见于像 *Microsoft Windows* 这样的操作系统中，用于提高计算机的可访问性。*计算机视觉*（CV）的早期示例包括用于辅助医学图像诊断的图像分割。

然而，这些技术通常是专有的、昂贵的，并且不容易集成到消费类应用程序中。开发良好的 ML 模型来执行这些任务的一些早期挑战包括提供足够的训练数据和计算能力。然而，*大数据*和*高性能计算*（HPC）的进步极大地提高了我们创建健壮、准确和通用化 ML 模型的能力。

© Adrian Png 和 Heli Helskyaho 2022

A. Png 和 H. Helskyaho，*使用 Oracle 云功能扩展 Oracle Application Express*，

[`doi.org/10.1007/978-1-4842-8170-3_10`](https://doi.org/10.1007/978-1-4842-8170-3_10#DOI)

第 10 章 视觉

例如，*ImageNet* ([`image-net.org/`](https://image-net.org/))，一个大规模收集标记数字图像存储库的努力，以及*图形处理单元*（GPU）的可用性，使得训练*卷积神经网络*（CNN）模型取得了成功。CNN 是*人工神经网络*（ANN）的一个强大变体，通常用于 AI 的一个子领域，称为*深度学习*。CNN 模型具有高度的通用性，但 ML 工程师也可以使用一种称为*迁移学习*的概念来训练它们执行专门的识别任务。这些概念体现在*Oracle 云基础设施*（OCI）*视觉* AI 服务中。

要使工具有效，它还必须易于集成到其他系统中，例如 Web 应用程序。OCI AI 服务包括定义良好的 REST API，允许开发人员将 CV 和 TTS 功能集成到像 *Oracle Application Express*（APEX）这样的 Web 平台中。在本章中，我们将介绍 Vision，它的要求，以及如何在日常 APEX 应用程序中使用此服务。

**使用 OCI REST API**

Oracle 提供了一套全面的 REST API，允许开发人员和管理员使用命令行界面（CLI）或自定义应用程序/脚本创建、管理和使用 OCI 资源。软件开发工具包（SDK）也可用于一些更流行的编程语言，如 JavaTM、Go 和 Node.JSTM。还有 PL/SQL API，但在撰写本文时，仅可在 Oracle 自治数据库（ADB）上直接使用。因此，我们将重点介绍如何直接使用 `APEX_WEB_SERVICE` PL/SQL 包与 REST API 一起工作，无论您的 Oracle 数据库部署在何处，该方法都适用。

回顾一下，OCI REST API 的使用受严格的身份和访问管理（IAM）策略约束。IAM 策略中定义的权限被分配给 IAM 组。为希望使用 API 的实体创建一个 IAM 用户，并将其分配到相关的组中。

创建 IAM 用户后，必须使用 OCI 控制台、OCI CLI 或广泛使用的 OpenSSL 应用程序生成一组私钥和公钥。必须通过 OCI 控制台将公钥添加到用户的账户中。添加后，收集所需信息如下：

• 租户 OCID

• 用户 OCID

• 用户 API 公钥的指纹

第 10 章 视觉

作为额外的安全层，使用 OCI REST API 要求所有请求都使用用户的 API 密钥进行加密签名。这可能是一个复杂的过程，需要客户端编程语言支持 RSA 公钥加密。

幸运的是，对于 APEX 开发人员，我们可以通过使用 APEX *Web 凭据*来避免此过程。创建和使用特殊的 OCI 凭据类型将使开发人员能够调用 API，而无需实现请求签名算法。

关于如何创建必要的 IAM 组、用户和策略，设置用户的 API 密钥，以及创建 OCI Web 凭据的说明，详见第 [2](https://doi.org/10.1007/978-1-4842-8170-3_2) 章。

**OCI 对象存储**

使用 Vision 服务需要借助 OCI *对象存储*来暂存待处理的图像文件。请按照第 [2](https://doi.org/10.1007/978-1-4842-8170-3_2) 章的说明创建以下资源：

• IAM 用户：*demoapex*

• IAM 组：*DemoVisionGroup*

• 对象存储桶：*ai-vision-demo*

# IAM 用户和组用于代表 APEX 应用程序访问 OCI 资源。

组是 OCI 在 IAM 策略中使用的实体，用于通过策略语句定义其可以或不可以执行的操作。将 IAM 用户`demoapex`分配到组`DemoVisionGroup`中。

为了允许 Vision 演示应用程序访问对象存储桶并上传文件，请创建一个名为`Demo-Object-Policy`的策略，并添加以下语句：

允许组 DemoVisionGroup 读取 adrian compartment 中的存储桶
允许组 DemoVisionGroup 管理 adrian compartment 中的对象

为方便使用对象存储，请在 APEX 应用程序的解析模式中创建一个 PL/SQL 包，其中包含两个过程，用于在指定存储桶中上传和删除对象。例如：

```sql
create or replace package pkg_oci_os_util
as
gc_objectstorage_endpoint constant varchar2(500) :=
'https://objectstorage.us-phoenix-1.oraclecloud.com';
gc_namespace constant varchar2(30) := 'abcd1234xyz';
gc_credential_static_id constant varchar2(50) :=
'OCI_CREDENTIALS';

procedure p_upload_object(
    p_bucket_name in varchar2
  , p_file_blob in blob
  , p_filename in varchar2
  , p_mime_type in varchar2
);

procedure p_delete_object(
    p_bucket_name in varchar2
  , p_filename in varchar2
);
end pkg_oci_os_util;
```

请根据您的环境更新以下包常量：

* `gc_objectstorage_endpoint`：使用相应的 OCI 区域更新端点。在示例中，端点是针对 us-phoenix-1（美国菲尼克斯区域）的。
* `gc_namespace`：要获取租户的命名空间字符串，请点击 OCI 控制台右上角的个人资料按钮（参见图 10-1）。点击标记为“A”的租户链接。您应输入的值可以在*对象存储设置*下找到，并标记为“B”。
* `gc_credential_static_id`：替换该值，或创建一个静态 ID 为 OCI_CREDENTIALS 的 APEX Web 凭证。该凭证的类型应为 Oracle Cloud Infrastructure (OCI)。必须正确填写 API 密钥和必要信息。

![图 10-1. 访问租户详细信息以获取命名空间字符串](img/index-359_1.png)

`pkg_oci_os_util`包规范和包体的完整代码可在本章的源代码目录中找到。

## OCI Vision

我在商务（或个人）旅行中遇到的一个挑战是整理收据，并可能报销旅行期间产生的费用。每张收据都不同，手动将它们输入雇主的报销应用程序可能是一项艰巨的任务。您可以购买带有配套软件的专用扫描仪来扫描这些收据，并将其输入到像 QuickBooks 这样的会计应用程序中，但这些解决方案成本可能很高。如今，软件甚至可能处理移动设备拍摄的收据照片，但如果您的公司有用于管理费用的自定义应用程序，该怎么办？

为了处理收据图像，计算机必须执行光学字符识别（OCR），这涉及处理并识别光栅图像中的文本。机器学习通常用于执行此类任务，并且有几种 OCR 引擎，例如开源的 Tesseract ([`tesseract-ocr.github.io/`](https://tesseract-ocr.github.io/))，开发人员可以将其集成到他们的应用程序中。对于 APEX 开发人员来说，这可以使用我们在第 3 章学过的 Oracle Functions 来完成。

然而，从图像中解读文本只是解决方案的一部分。其他挑战依然存在，例如理解文本的上下文并将其映射到数据库表中进行存储。一些常见场景涉及发票中的表格以及采购收据中的键值对。因此，包括 Oracle 在内的多家云供应商开发了基于 REST 的高级 API 服务，能够使用预训练的 AI 模型来执行此任务，这并不令人意外。

Vision 提供了多个预训练模型，大致可分为两类：**文档 AI** 和**图像分析**。相应地，开发人员可以使用 AnalyzeDocument 和 AnalyzeImage 操作来访问各自的 OCI REST API。

顾名思义，文档 AI 预训练模型允许开发人员处理文档图像，并执行以下一项或多项任务：

* **OCR**：文本检测，可选择创建可搜索文件并保存为*便携式文档格式* (PDF)。
* **文档分类**：分析并分类文档类型，同时提供置信度分数。
* **语言分类**：基于 ISO-639-2 标准（[www.iso.org/standard/4767.html](http://www.iso.org/standard/4767.html)）返回三个字母的语言代码，并提供置信度分数。支持的语言包括英语、法语、简体中文等。
* **表格提取**：识别以表格格式书写的文本，并按相同的行和列提取它们。
* **键值提取**：此预训练模型从收据中提取键值对，例如商户位置和联系信息、购买的商品、数量和价格等字段。

对于图像分析，开发人员可以请求执行以下任务：

* **对象检测**：识别对象、其类别以及对象所在的边界框。
* **图像分类**：分配并列出图像中找到的所有对象的标签。
* **OCR**：检测图像中的文本，并提供其所在的边界框。

**注意**：除了用于图像分析任务的预训练模型外，Vision 还允许用户创建自定义模型。开发人员可以创建 Vision 项目，并借助 OCI Data Labeling 服务，为更具体的计算机视觉任务训练自定义模型。该过程涉及使用一种称为*迁移学习*的概念，这超出了本章的讨论范围。

## Travel Receipts APEX 应用程序

对于我们的下一个应用程序，我们将编写一个 Travel Receipts APEX 应用程序，该应用程序允许用户拍摄并上传图像，然后使用 Vision 的键值提取预训练模型解析收据中的字段并呈现其值。以下是用户通过 APEX 页面上传并提交扫描收据时发生情况的概要。

1. 从`APEX_APPLICATION_TEMP_FILES`表中检索图像 BLOB。
2. 使用`pkg_oci_os_util.p_upload_object`过程将 BLOB 上传到 ai-vision-demo 对象存储桶。
3. 使用`pkg_oci_vision_util.f_analyze_document`函数准备并提交 Vision 文档分析请求，并接收 JSON 格式的结果。
4. 将图像和文档分析结果都保存到表中。

## RECEIPT 表结构

首先创建一个名为`RECEIPT`的简单数据库表，包含以下列：

| 列名 | 数据类型 | 用途 |
| :--- | :--- | :--- |
| reCeipt_iD | nUMBer | 主键 |
| sCanneD_iMaGe | BLoB | 用于存储扫描的图像 |
| FiLenaMe | VarChar2(1000) | 上传图像的文件名 |
| MiMe_tYpe | VarChar2(100) | 上传图像的 MIME 类型 |
| reCeipt_Data | CLoB | 用于存储 Vision 返回的结果。添加 is_json 检查约束 |

接下来，创建一个用于处理 Vision API 的 PL/SQL 包。以下代码封装了准备请求并将其提交到 Vision 的 AnalyzeDocument REST 端点的必要步骤：

```sql
create or replace package body pkg_oci_vision_util
as
function f_analyze_document(
    p_bucket_name in varchar2
  , p_filename    in varchar2
) return blob
as
    c_path constant varchar2(100) :=
        '/20220125/actions/analyzeDocument';
    l_response blob;
begin
apex_web_service.g_request_headers(1).name := 'Content-Type';
apex_web_service.g_request_headers(1).value :=
'application/json';
end f_analyze_document;
end pkg_oci_vision_util;
```

# 第10章 视觉

## AnalyzeDocument 端点

`AnalyzeDocument` 端点通过 POST 方法调用，在请求体中，开发者必须包含一个包含请求详细信息的 JSON 负载。由 `apex_web_service.make_rest_request` 发送的请求负载如下所示：

```json
{
    "document": {
        "source": "OBJECT_STORAGE",
        "namespaceName": "abcd1234xyz",
        "bucketName": "ai-vision-demo",
        "objectName": "receipt01.png"
    },
    "features": [
        {
            "featureType": "KEY_VALUE_DETECTION"
        }
    ]
}
```

它包含以下关键属性：

• **文档**：我们可以以内联方式提交文档，即文档嵌入在请求负载中，或者作为对象存储引用提交。如果文档对象的来源是 `OBJECT_STORAGE`，则它还必须包含共同指定对象位置的属性。这些属性定义了以下内容：
   a. 命名空间名称
   b. 存储桶名称
   c. 对象名称

• **功能**：此属性接受一个功能类型数组，这意味着开发者可以请求在单个 REST 调用中执行多项分析。

在开始处理 APEX 应用程序之前，请确保：
1. 已创建用于让应用程序访问对象存储和视觉服务的 IAM 用户和组。
2. 已在工作区中创建用于 OCI REST API 访问的 APEX Web 凭证。
3. 已定义所需的 IAM 策略和语句，以允许 IAM 用户读取和写入目标对象存储桶，并能够使用视觉服务。

在本章前面，我们创建了 IAM 用户 *demoapex*，并将其分配到组 *DemoVisionGroup*。为了允许 *demoapex* 使用视觉服务，请在根区间中创建一个包含以下语句的策略：
```sql
allow group DemoVisionGroup to use ai-service-vision-family in tenancy
```

## APEX 应用程序设置

创建一个包含三个页面的新 APEX 应用程序：
• **页面 1**：主页
• **页面 2**：上传文档
• **页面 3**：查看收据详情

![](img/index-365_1.jpg)

### 主页

在主页上，显示已上传并分析过的收据列表。使用 RECEIPT 表作为源创建一个经典报表（图 10-2）。它应显示 RECEIPT_ID、FILENAME 和 MIME_TYPE 列。第一个报表列应使用 RECEIPT_ID 的值呈现一个指向页面 3 的链接。同时创建一个用于上传图像的按钮，并将其放置在报表的右上角。该按钮应重定向到页面 2，这是一个我们接下来将创建的模态对话框页面。

***图 10-2.** 主页，显示已上传收据的交互式报表*

### 上传页面

第二个用于上传图像的页面是大部分操作发生的地方。添加以下 APEX 组件：
• **P2_FILE**：一个类型为 *文件浏览* 的页面项。
   • 如果您使用的 APEX 版本支持，请将 *显示为* 值设置为 *块状拖放区*。
   • 将 *存储类型* 设置为 *表 APEX_APPLICATION_TEMP_FILES*。
   • 设置 APEX_APPLICATION_TEMP_FILES 中的文件在请求结束时被清除。
   • 将接受的文件类型设置为 *image/**。
   • 将其设置为必填字段。
   • 对于字段的外观，将模板设置为 *隐藏*，然后通过模板选项，拉伸表单项目，使其占据所有水平空间。最后，将标签列跨度设置为 *0*。
• **取消**：一个用于中止并返回主页的按钮。添加一个动态操作来处理点击事件并执行 *取消* *对话框* 动作。
• **提交**：一个用于提交页面的按钮。此按钮应执行验证并确保已选择文件。
• 处理流程：
   • **processFile**：此流程包含用于执行本章前面所述工作流的 PL/SQL 代码。它应仅在 *提交* 按钮被点击时运行。
   • **closeDialog**：这是一个标准流程，用于在处理完成后关闭模态对话框。

图 10-3 展示了页面完成后的样子。

![](img/index-367_1.jpg)

***图 10-3.** 一个用于处理文件上传和触发文档分析的模态对话框页面*

用户在模态对话框的指定区域选择或拖放一个图像文件，然后点击 *提交* 按钮。随后触发 *processFile* 流程，并执行以下 PL/SQL 代码：
```sql
declare
  l_receipt_id receipt.receipt_id%type;
begin
  for file in (
    select * from apex_application_temp_files
    where name = :P2_FILE
  ) loop
    pkg_receipt.p_create(
      p_scanned_image => file.blob_content
    , p_filename => file.filename
    , p_mime_type => file.mime_type
    , p_receipt_id => l_receipt_id
    );
  end loop;
end;
```

首先从 `APEX_APPLICATION_TEMP_FILES` 表中检索上传的文件。然后，该流程调用过程 `pkg_receipt.p_create`，这是一个外观[模式](https://wikipedia.org/wiki/Facade_pattern)，它依次调用以下内容：
• `pkg_oci_os_util.p_upload_object`，一个将文件上传到指定对象存储桶的过程。
• `pkg_oci_os_vision_util.f_analyze_document`，一个调用 *AnalyzeDocument* OCI REST 操作并返回包含分析结果的 JSON 的函数。
• `pkg_oci_os_util.p_delete_object`，一个执行清理操作以从存储桶中删除已上传文件的过程。

最后一步将上传的文件和由 `pkg_oci_os_vision_util.f_analyze_document` 函数返回的结果一起插入。存储结果使我们能够进行查询。以下 JSON 是 Document AI 结果的精简版本：
```json
{
  "documentMetadata" : {
    "pageCount" : 1,
    "mimeType" : "image/jpeg"
  },
  "pages" : [ {
    "pageNumber" : 1,
    "dimensions" : {
      "width" : 651.0,
      "height" : 868.0,
    }
  } ]
}
```

# 第 10 章 视觉

```json
"unit" : "PIXEL"
},
"detectedDocumentTypes" : null,
"detectedLanguages" : null,
"words" : [ ... ]
"tables" : null,
"documentFields" : [ ... ]
} ],
"detectedDocumentTypes" : null,
"detectedLanguages" : null,
"documentClassificationModelVersion" : null,
"languageClassificationModelVersion" : null,
"textDetectionModelVersion" : "1.2.87",
"keyValueDetectionModelVersion" : "1.2.87",
"tableDetectionModelVersion" : null,
"errors" : null,
"searchablePdf" : null
}
```

结果的内容取决于请求负载中列出的功能。例如，如果添加的功能之一是 `TABLE_DETECTION`，那么页面对象中的 `table` 数组将包含在分析过程中发现的任何表格结构的行和单元格数组。在我们的示例中，我们仅指定检测收据中的键值对，因此，如果找到任何内容，我们将在 `documentFields` 数组中找到对象。

在示例应用程序中，我们期望只上传收据。这些收据通常包含以下类型的信息：
• 商户详情和摘要信息，例如交易中购买商品的总价。
• 购买的商品列表，包括商品名称、价格和数量。

创建第三个页面，用于查看 `RECEIPT` 表中的记录。包含以下区域：
• **扫描图像**：一个包含两个页面项目的 *静态内容* 区域：
  • `P3_RECEIPT_ID`：一个 *隐藏* 页面项，用于存储记录的主键。
  • `P3_SCANNED_IMAGE`：一个 *显示图像* 页面项，用于渲染以 `BLOB` 形式存储在 `RECEIPT` 表中的扫描图像。
• **解析数据**：另一个 *静态内容* 区域，将作为接下来两个子区域的容器。将解析后的数据与扫描图像并排显示，并将其配置为使用 *选项卡容器* 模板。
  • **键值对**：创建此 *经典报表* 区域，作为 *解析数据* 区域的子区域。
  • **行项目**：在 *解析数据* 区域下添加第二个 *经典报表* 区域。
• **按钮容器**：将此包含 *关闭* 和 *删除* 按钮的 *静态内容* 区域放置在 *对话框页脚* 位置。这些按钮将分别执行标准操作：通过动态操作关闭模态对话框，以及通过页面进程删除记录。

如本章前面所述，键值对位于 `documentFields` 数组中。以下代码片段显示了示例收据中找到的第一个文档字段。

```json
"documentFields" : [ {
    "fieldType" : "KEY_VALUE",
    "fieldLabel" : {
        "name" : "MerchantName",
        "confidence" : 0.9999982
    },
    "fieldName" : null,
    "fieldValue" : {
        "valueType" : "STRING",
        "text" : null,
        "confidence" : null,
        "boundingPolygon" : {
            "normalizedVertices" : [ {
                "x" : 0.1674347158218126,
                "y" : 0.12211981566820276
            }, {
                "x" : 0.6851611159364199,
                "y" : 0.12211981566820276
            }, {
                "x" : 0.6851611159364199,
                "y" : 0.1497695852534562
            }, {
                "x" : 0.1674347158218126,
                "y" : 0.1497695852534562
            } ]
        },
        "wordIndexes" : [ 0, 1, 2 ],
        "value" : "The Singapore Mint"
    }
} ]
```

**注意**：Vision 在图像中找到的每个文本值都有一个关联的边界多边形/框和置信度分数。这些为开发者提供了图像中推断出文本的像素坐标，以及一个介于 0 和 1 之间的值，表示预测值正确的概率。例如，边界框坐标可用于显示和突出显示找到术语的位置，而置信度分数可与阈值一起使用，以决定接受或丢弃哪些预测值。

此数组中的对象可能属于 `KEY_VALUE` 或 `LINE_ITEM_GROUP` 字段类型。`KEY_VALUE` 类型的对象可能包含来自预定义字段的值，例如商户名称（`MerchantName`）、电话号码（`MerchantPhoneNumber`）、收据交易日期（`TransactionDate`）和总费用（`Total`）。

要检索收据中找到的键值对，请将 *键值对* 区域的源设置为使用以下 SQL 查询：

```sql
select
    data.field_name
    , data.field_value
from
    receipt r
    , json_table(
        r.receipt_data
        , '$.pages[*].documentFields[*]'
        columns(
            field_name varchar2(30) path '$.fieldLabel.name'
            , field_value varchar2(500) path '$.fieldValue.value'
            , field_type varchar2(30) path '$.fieldType'
        )
    ) data
where 1 = 1
    and receipt_id = :P3_RECEIPT_ID
    and data.field_type = 'KEY_VALUE'
```

然后，自定义报表的呈现方式。选择该区域，然后切换到 *属性* 选项卡。在 *外观* 部分下，为模板选择 *值属性对 - 行*。图 10-4 显示了页面完成后的外观。

***图 10-4.** 显示找到的预定义文档*

在收据中发现的项目列表可以在类型为 `LINE_ITEM_GROUP` 的文档字段中找到。在此对象内部，有一个 `items` 数组，该数组是类似于父对象的对象集合。每个对象代表一个行项目（字段类型为 `LINE_ITEM`）。它本身又包含一个 `items` 数组，其中包含字段类型为 `LINE_ITEM_FIELD` 的对象。此数组的元素可能包含行项目的名称、价格、数量和总价。这些嵌套元素的示例如下所示：

```json
{
    "fieldType" : "LINE_ITEM_GROUP",
    "fieldLabel" : {
        "name" : "line item group",
        "confidence" : null
    },
    "fieldName" : null,
    "fieldValue" : {
        "valueType" : "ARRAY",
        "text" : null,
        "confidence" : null,
        "boundingPolygon" : null,
        "wordIndexes" : null,
        "items" : [ {
            "fieldType" : "LINE_ITEM",
            "fieldLabel" : null,
            "fieldName" : null,
            "fieldValue" : {
                "valueType" : "ARRAY",
                "text" : null,
                "confidence" : null,
                "boundingPolygon" : null,
                "wordIndexes" : null,
                "items" : [ {
                    "fieldType" : "LINE_ITEM_FIELD",
                    "fieldLabel" : {
                        "name" : "ItemTotalPrice",
                        "confidence" : 0.9988584
                    },
                    "fieldName" : null,
                    "fieldValue" : {
```

# 第 10 章 视觉

要显示行项目，请配置`行项目`经典报表区域，使用`值属性对-行`模板来显示结果，然后将其源设置为使用以下 SQL 查询：

```sql
select
    data.field_name
    , data.field_value
from
    receipt r
    , json_table(
        r.receipt_data
        , '$.pages[*].documentFields[*].fieldValue.items[*]'
        columns(
            row_number for ordinality
            , nested path '$.fieldValue.items[*]' columns (
                field_name varchar2(30) path '$.fieldLabel.name'
                , field_value varchar2(500) path '$.fieldValue.value'
                , field_type varchar2(30) path '$.fieldType'
            )
        )
    ) data
where 1 = 1
    and receipt_id = :P3_RECEIPT_ID
    and data.field_type = 'LINE_ITEM_FIELD'
order by data.row_number
    , case data.field_name
        when 'ItemName' then 1
        when 'ItemPrice' then 2
        when 'ItemQuantity' then 3
        when 'ItemTotalPrice' then 4
        else null
    end nulls last
```

![](img/index-376_1.jpg)

点击`行项目`将显示在提交的图像中发现了哪些项目（图 10-5）。

***图 10-5.** 查看收据详情页面，显示从收据中提取的行项目*

**总结**

人工智能和机器学习的目标是创建能够模仿人类行为的计算机系统和机器人。人类大脑作为我们决策过程的核心，高度依赖于包括视觉在内的五种感官。数字捕获设备的普及、更便宜高效的数据存储以及更强大的计算机处理能力，推动了计算机视觉技术的进步。如今，OCI 通过其 AI 服务产品（包括视觉）提供“AI 即服务”。这使得 APEX 开发者能够更轻松地集成涉及图像/物体识别、文本提取和文档分类功能的解决方案。本章介绍的示例应用程序只是 AI 可以应用的一个领域，希望它能激发您创建更智能的 Web 应用程序。

# 第 11 章 电子邮件投递服务

*电子邮件*可以说是互联网上最古老、使用最广泛的应用程序之一，也是不可或缺的通信工具。它最初由计算机程序员雷·汤姆林森在 20 世纪 70 年代为高级研究计划局网络（ARPANET）实现。电子邮件依赖于客户端应用程序和一组服务器网络，这些服务器使用简单邮件传输协议（SMTP）来接受、转发、投递和存储消息。

虽然电子邮件旨在提供人与人之间的多方对话，但软件应用程序也常使用它来发送通知、信息等。编程语言和框架实现了与电子邮件服务器通信的 API，代表应用程序使用 SMTP。在*Oracle Application Express*（APEX）应用程序中，*APEX\_MAIL* 是一个 *PL/SQL* API，它封装了*Oracle 数据库*中的 *UTL\_SMTP* 包。然而，应用程序依赖于对电子邮件服务器或服务的访问来中继消息。

要从应用程序发送电子邮件，我们可以使用托管服务或配置自管理的 SMTP 服务器。如今，公司依赖在第三方云服务（如 *Microsoft 365*）上处理电子邮件。*Oracle Cloud Infrastructure* (OCI) 也提供了一项名为 *Email Delivery* 的托管服务。该服务不包含在 *Oracle Cloud Free Tier* 免费套餐中，用户需要升级到付费账户才能使用。

在本章中，我们将逐步介绍为使用 Email Delivery 服务准备 OCI 租户所需的步骤。完成这些资源的配置后，我们将配置一个 APEX 实例，使其能够使用 OCI 的 SMTP 端点，通过 `APEX_MAIL` 包发送电子邮件。我们还将探讨可能影响电子邮件送达率的各种问题、可以采取哪些措施来确保邮件成功发送，以及在遇到困难时可以使用哪些工具。

## 设置 OCI 资源

Email Delivery 服务在大多数（如果不是全部）OCI 区域都提供了一个 SMTP 服务器，用于发送电子邮件。虽然该服务需要用户身份验证才能访问，但对客户端连接没有网络限制。不过，还有额外的保护层以防止滥用。创建并配置所需的 OCI 资源，这些资源共同有助于保护服务安全并提高电子邮件的送达率。

### 身份与访问管理

安全是所有 OCI 操作的核心，因此，与所有其他 OCI 服务一样，Email Delivery 要求创建并配置必要的 *Identity and Access Management* (IAM) 资源和策略。建议为应用程序创建一个单独的 IAM 用户，以便*通过*该服务发送电子邮件。首先创建一个 IAM 组，IAM 策略将使用该组来分配必要的权限。

1.  通过点击导航菜单，然后点击 *Identity & Security*，接着点击 *Groups*，导航到“组”概览页面。
2.  点击 *Create Group*。
3.  输入一个合适的名称，例如 *EmailDeliveryAgents*，并提供其用途的简短描述（图 11-1）。
4.  点击 *Create* 创建所需的组。

![](img/index-379_1.jpg)

***图 11-1.** 创建一个 IAM 组*

下一个任务是创建一个 IAM 策略，并为该组添加使用 Email Delivery 服务资源所需的语句。

1.  通过点击导航菜单，然后点击 *Identity & Security*，接着点击 *Policies*，导航到“策略”概览页面。或者，如果您在“组”概览页面上，只需点击页面左侧菜单中的 *Policies* 链接即可。
2.  进入“策略”概览页面后，选择要创建此策略的 compartment。我们将在租户中创建它，因此请选择页面左侧 *List Scope* 部分下的根 compartment。
3.  点击 *Create Policy*。

![](img/index-380_1.jpg)

4.  输入所需的策略名称和描述。确保该策略是在租户中创建的，然后切换 *Policy Builder* 以显示手动编辑器（图 11-2）。

***图 11-2.** 创建策略并使用手动编辑器添加所需语句*

**注意**：策略构建器只有用于创建语句的模板，以允许一个组管理相关的 OCI 资源（图 11-3），而对于仅通过该服务发送电子邮件来说，通常不需要这些模板。

![](img/index-381_1.jpg)

使用以下语法输入策略语句：

```
Allow group <组名> to use email-family in compartment <compartment 名称>
```

例如：

```
Allow group EmailDeliveryAgents to use email-family in compartment adrian
```

***图 11-3.** 用于生成管理相关 OCI 资源语句的策略模板*

接下来，我们将创建 IAM 用户 *emailagent*，然后将其分配到组

# 第 11 章 电子邮件投递服务

*EmailDeliveryAgents*，从而授予其使用该服务所需的权限：
1.  通过点击导航菜单导航至`策略概览页面`，然后依次点击`身份与安全`和`用户`。
2.  或者，如果你仍在组或策略概览页面，只需点击页面左侧菜单中的`用户`链接。
3.  点击`创建用户`。

![](img/index-382_1.jpg)

4.  选择用户类型`IAM 用户`。
5.  输入合适的用户名和描述，但将电子邮件字段留空，因为该用户不需要这些信息（图 11-4）。
6.  点击`创建`以创建用户。

**图 11-4.** 使用所需信息创建 IAM 用户

用户创建完成后，你将返回用户的详细信息页面。在那里，你可以找到多个可对该用户执行的管理操作。根据最小权限原则，我们应限制此`IAM`用户在`OCI`租户中的操作范围。

1.  点击`编辑用户能力`。
2.  默认情况下，新创建的用户会启用所有能力。通过取消选中所有其他选项，将其限制为仅`SMTP 凭证`（图 11-5）。
3.  点击`保存更改`。

![](img/index-383_1.jpg)

**图 11-5.** 遵循最小权限原则，移除使用电子邮件投递服务所不需要的用户能力

此时，`IAM`用户只能创建`SMTP 凭证`。但是，由于该用户没有`本地密码`能力，你无法以该用户身份登录`OCI`控制台来生成用户名和密码对。请改用你的管理用户来执行此操作。

1.  在用户的详细信息页面上，向下滚动，在`资源`下，点击链接`SMTP 凭证`。
2.  点击`生成 SMTP 凭证`。
3.  输入一个有助于你回忆凭证用途和/或使用位置的描述（图 11-6）。
4.  点击`生成 SMTP 凭证`以生成凭证。

![](img/index-384_1.jpg)

**图 11-6.** 生成 SMTP 凭证并提供有用的描述

> **注意** 创建 SMTP 凭证后，会弹出一个模态框，显示用户名和密码（图 11-7）。这些是你将用于连接`OCI`电子邮件投递服务`SMTP`服务器的凭据。密码**仅显示一次**，请立即安全保存。如果丢失密码，请生成一组新的凭证。

![](img/index-385_1.jpg)

**图 11-7.** SMTP 凭证的密码仅显示一次。请安全存储！

最后一步是将用户分配到相关组（图 11-8）：
1.  在用户的详细信息页面上，向下滚动，在`资源`下，点击链接`组`。
2.  点击`将用户添加到组`。
3.  选择`IAM`组`EmailDeliveryAgents`。
4.  点击`添加`以将用户分配到该组。

![](img/index-386_1.jpg)

**图 11-8.** 将 IAM 用户分配到适当的组

至此，为使用电子邮件投递服务而进行的`IAM`资源配置已完成。

## 区域、可用性和服务器信息

虽然`OCI IAM`资源是全局性的，但与电子邮件投递服务相关的资源则不是。这些资源包括：

* SMTP 服务器
* 电子邮件域
* 已批准的发送者
* 抑制列表

在选择托管`OCI`资源的区域时，请务必验证该区域是否拥有所有必需的服务。使用此网站上的矩阵（[www.oracle.com/cloud/data-regions/](http://www.oracle.com/cloud/data-regions/)）来指导你的区域选择。在撰写本文时，所有`OCI`数据区域似乎都支持电子邮件投递服务。

![](img/index-387_1.png)