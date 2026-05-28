# 三、利用云平台

*   提供 IaaS 解决方案的云提供商回顾
*   谷歌云平台(GCP)和亚马逊网络服务(AWS)云产品概述，包括数据存储、数据预处理、模型创建和模型部署功能
*   如何在云中实现 Weka
*   ML 云提供商 API 产品概述
*   项目:使用 Android 的云语音 API 实现 GCP ML
*   ML 云数据工具概述
*   对 ML 的云数据策略的回顾，包括 NoSQL 数据库的使用

## 3.1 导言

高度可扩展的计算资源的可用性是推动人工智能爆炸的三大趋势之一。在本章中，您将分析云服务提供商。您正在专门寻找他们可以帮助您交付 ML 解决方案的方法。

The big four cloud providers that offer `Infrastructure as a Service (IaaS)` solutions are the drivers behind this megatrend. Table 3-1 shows a summary of the big four service providers.

**Table 3-1 美国四大云服务提供商**

| `IaaS` 提供商 | 网站(全球资讯网的主机站) | 注意 |
| --- | --- | --- |
| 谷歌云平台 | `cloud.google.com` | 轻松集成所有有用的谷歌工具，包括 Android 手机。 |
| 亚马逊网络服务 | `aws.amazon.com` | 最大的云提供商。为 ML 提供全套服务。 |
| 微软 Azure | `azure.microsoft.com` | 增长最快的 `IaaS` 提供商。 |
| IBM 云 | `ibm.com/cloud` | 沃森是云 ML 的先驱。 |

`IaaS` 解决方案允许您扩展计算环境，以满足您在 CPU、内存和存储方面的需求。您只需要为所需的资源付费。还使您能够轻松地跨地理区域分配资源。

这种方法比构建您自己的服务器并在它们变得太慢时升级它们要容易得多，也便宜得多。

在这一章中，您将研究主要厂商的云产品，看看它们如何帮助您创建和部署 ML 解决方案。

### 商业云提供商

One of the advantages of creating `CML` solutions compared to `DL` is that they require for less data and CPU resources. This generally enables you to create solutions entirely on the desktop. However, you should not overlook the cloud. The cloud providers continuously improve their ML offerings. Today they provide an amazing array of services and APIs that make it easier than ever for developers who do not have prior ML experience to create and deploy ML solutions.

> 云 ML 服务不是免费的。无论他们使用何种类型的容器或虚拟化技术，在某些时候都需要专用或共享的硬件(CPU、内存、存储)。每个提供商通常都有一个免费的试用版，所以你可以在购买前试用这项服务。

定价与您消耗的计算和存储资源成正比。

With your focus on Java, you will next investigate the ML cloud potential of the four large US-based cloud providers. In the next sections, you will review the following ML-related services for each of the providers:

*   **数据存储**: `IaaS` 提供商为 ML 解决方案提供出色的数据存储产品。它们包括平面文件存储、传统的关系数据库、`NoSQL` 数据库等等。
*   **数据预处理**: 平台提供了哪些工具来帮助你准备数据(`ML-Gate 4`)？
*   **模型创建**: 云平台提供了哪些工具和 ML 框架来帮助你创建你的模型(`ML-Gate 3`)？
*   **模型部署**: 有哪些方法可以部署你的 ML 模型进行预测，比如 API 创建或者直接托管访问？

The key considerations outlined in Table 3-2 can help you decide if cloud services are a good fit for your ML project.

**Table 3-2 云提供商考虑事项**

| 种类 | 考虑 |
| --- | --- |
| 本地资源可用性 | 您是否有可以处理大型数据集并构建 ML 模型的本地台式机或服务器？本地处理允许您保持对数据的控制，并避免云使用费。 |
| 深度学习？ | 深度学习项目往往倾向于基于云的架构，因为它们依赖于更大的数据集和对模型创建的高计算要求。 |
| 地理多样性 | 云提供商可以让你在全球多个国家和地区增加资源。将资源放在离用户尽可能近的地方是有利的。 |
| 数据量 | 您是否有一个可以在桌面上管理的数据集大小，就像 `CML` 项目中经常出现的情况一样？ |
| 可量测性 | 您预计未来您的数据或存储需求会增长吗？云提供商提供了更好的可扩展性。添加云资源比升级或购买更强大的台式机/服务器要容易得多。 |
| 时间限制 | 模型创建时间重要吗？即使对于具有中等规模到大型数据集的 `CML` 项目，在台式机或服务器单 CPU 上创建模型也可能需要几分钟到几小时。将这些计算密集型操作转移到云上可以大大减少模型创建时间。如果您需要实时或接近实时的创建时间，云是您唯一的选择。 |
| 有效 | 您需要高可用性吗？您的项目可以从所有云提供商提供的分布式多节点架构中受益。 |
| 安全考虑 | 如果你有自己的联网服务器，你就会知道安全是一个多么大的挑战。云提供商简化了安全性，因为您可以利用他们的大规模基础架构。 |
| 隐私考虑 | 您的客户可能不希望他们的数据放在由四大提供商之一管理的公共云网络上。在这种情况下，您可以实现私有云解决方案并收取额外费用。 |

即使您决定不为您的项目使用云提供商，关注他们的产品也很重要。服务会不断更新，您的决定可能会根据这些更新而改变。

### 竞争定位

每个人都想知道哪个云提供商最适合机器学习。当然，没有简单的答案。

在任优势在任何决策中都起着重要作用。如果您已经与非 ML 服务的云提供商建立了关系，您更有可能选择相同的提供商来提供 ML 服务。不利的一面是，你可能会发现自己被某个提供商所束缚。ML 的前景变化很快，ML 的产品也有一些显著的不同。密切关注所有服务的发展。

选择一个框架无关的云提供商是有优势的。在接下来的部分你会看到，Google 云平台的框架选择有限，主要依靠强大的 TensorFlow 框架。GCP 确实有优势，可以很好地契合你对移动设备和安卓系统的关注。

## 各大云服务提供商

各大云服务商各有优劣。图3-1展示了一些主要云服务商的概况。该图以市场份额为X轴、增长率为Y轴进行绘制。数据来源于公开的企业财报。增长率代表季度间的收入对比。市场份额代表各服务商报告的活跃用户数。图中所示的云服务商均提供按需付费服务，帮助你部署机器学习解决方案。美国四大服务商的总市场份额接近70%。大多数观察家认为`Amazon Web Services`是市场份额领导者。然而，所有服务商之间存在着激烈竞争。在北美以外，尤其是在亚洲，`Alibaba cloud`（也称为阿里云）是一个非常强劲的参与者。
![img/468661_1_En_3_Fig1_HTML.jpg](img/468661_1_En_3_Fig1_HTML.jpg)  
图 3-1 云提供商的竞争地位

### 定价

如果您决定部署云服务，定价很重要。它代表一项直接费用，与第一章中讨论的货币化相反。

近年来，四大巨头之间的激烈市场竞争已将云服务价格压低。如今，基本不存在定价套利机会。

> 由于最大的云提供商之间的激烈竞争，如今不同平台的云资源成本基本相同。四大银行敏锐地意识到他们竞争对手的产品，定价套利机会不再存在。

云服务商让估算潜在成本变得容易。每个服务商都提供定价计算器，可以让你准确了解潜在成本。图3-2展示了GCP定价计算器。这些交互式工具允许你指定许多参数，包括云服务类型、CPU数量、存储、操作系统、可用性、区域等。填写必填字段后，该工具会显示计算出的每月和每小时成本。
![img/468661_1_En_3_Fig2_HTML.jpg](img/468661_1_En_3_Fig2_HTML.jpg)  
图 3-2 GCP 定价计算器

图 3-2 显示了谷歌计算引擎服务的指定最低配置。结果显示每月成本为 30 美元或每小时 0.04 美元。如果你在`AWS`、`Azure`或`Watson`上运行类似的计算，你会发现定价是可比的。

请记住，在使用定价工具时，服务通常提供的免费试用期不包括在定价估算中。在许多情况下，您可以获得为期一年的免费试用，以测试提供商的服务。

## 3.2 谷歌云平台（GCP）

`GCP`为你提供访问大量且不断增长的硬件和软件资源（称为服务）的权限。`GCP`提供的服务非常广泛。谷歌将`GCP`服务划分为表3-3所示的较高级别类别。每个类别中都有许多服务。这里仅突出显示了机器学习和深度学习所需的具体服务。

表 3-3 GCP 服务（突出显示与机器学习相关的服务）

| GCP 服务类别 |
| :--- |
| `Compute Engine` |
| **存储和数据库** |
| 大数据 |
| **`Cloud AI`、`Cloud ML Engine`、`Cloud ML API`** |
| API 平台和生态系统 |
| **数据传输** |
| 身份和安全性 |
| **管理工具** |
| 开发者工具：`Cloud SDK`、`Eclipse` 的云工具 |
| **物联网** |
| 专业性劳务 |
| **生产力工具：`Android`** |
| 建立关系网 |

接下来，您将探索突出显示的机器学习相关服务。第一步是注册`GCP`或登录您现有的帐户。图3-3显示了`GCP`仪表板。`GCP`仪表盘的地址是`https://console.cloud.google.com/`。

`GCP`仪表板在左侧面板的中间位置显示`Compute Engine`。`Compute Engine`允许你使用运行在谷歌基础设施上的虚拟机。点击`Compute Engine`后，你就能创建你的第一个虚拟机实例。
![img/468661_1_En_3_Fig3_HTML.jpg](img/468661_1_En_3_Fig3_HTML.jpg)  
图 3-3 GCP 仪表板

### 谷歌计算引擎（GCE）虚拟机（VM）

尽管`GCP`机器学习服务集中于深度学习，`GCE VM`提供了部署任何开源包的灵活性。您可以部署虚拟机来运行`Java`、开源的`Weka`机器学习环境和基于`Java`的数据存储库，比如`Cassandra` NoSQL 数据库。在`GCE`虚拟机上运行这些包通常比在本地桌面环境中配置它们更容易，因为谷歌为许多流行的包提供了现成的映像，如果你的项目需要在以后进行扩展，你将拥有云平台可伸缩性的所有优势。

图3-4显示了创建`GCE VM`时可用的选项。
![img/468661_1_En_3_Fig4_HTML.jpg](img/468661_1_En_3_Fig4_HTML.jpg)  
图 3-4 GCE 虚拟机实例创建选项

When creating a VM you can choose an instance name and a region for the VM. Of particular interest are the machine type and the boot disk. The machine type specifies the CPU/memory capacity. The pricing information show in the right panel directly relates to the machine capacity you select. For initial testing, you can choose the micro instance. Figure 3-5 shows the boot disk options available. Many Unix configurations are available.

![img/468661_1_En_3_Fig5_HTML.jpg](img/468661_1_En_3_Fig5_HTML.jpg) Figure 3-5 GCE 虚拟机实例操作系统选项

创建实例后，CGE 将处理请求。需要几秒钟时间，实例才会启动并变得可用。图 3-6 显示了新的微型虚拟机实例。如果点击 SSH 下拉对话框，将可以立即连接到实例，也如图 3-6 所示。

您创建的每个虚拟机实例都有一个内部和外部 IP 地址。如果您希望通过互联网访问虚拟机，您应该使用外部 IP 地址。您可以使用支持 SSH 的 FTP 客户端(如 FileZilla)向/从您的虚拟机传输文件。您还可以在 Windows 上使用第三方 SSH shell 程序，如 Putty。有关 VM 实例连接的更多信息，请参考以下 Google 页面:

`https://cloud.google.com/compute/docs/instances/connecting-advanced`

![img/468661_1_En_3_Fig6_HTML.jpg](img/468661_1_En_3_Fig6_HTML.jpg) Figure 3-6 从 GCP 仪表板到 GCE 虚拟机的 SSH 连接

如果您喜欢使用命令行界面来管理您的 CGE 虚拟机，Google 提供了 Google Cloud SDK。

## 谷歌云 SDK

Google Cloud SDK is a command-line interface for Google Cloud Platform products and services. Cloud SDK is a set of tools:

*   `gcloud` tool:管理认证、本地配置、开发者工作流以及与云平台 API 的交互。

*   `gsutil` 工具:`gsutil` 提供命令行访问来管理云存储桶和对象。

*   `bq` 工具:允许您通过命令行在 BigQuery 中运行查询、操作数据集、表和实体。

*   `kubectl` 工具:协调 `gcloud` 上 Kubernetes 容器集群的部署和管理。

You can run each of these tools interactively or in your automated scripts. Figure 3-7 shows the Cloud SDK download page.

![img/468661_1_En_3_Fig7_HTML.jpg](img/468661_1_En_3_Fig7_HTML.jpg) Figure 3-7 谷歌云 SDK 下载页面

Cloud SDK is available for all platforms. Figure 3-8 shows the Cloud SDK after successful installation.

![img/468661_1_En_3_Fig8_HTML.jpg](img/468661_1_En_3_Fig8_HTML.jpg) Figure 3-8 云 SDK 安装

一旦安装了 Cloud SDK，您就可以从命令行管理任何 GCP 服务。这包括谷歌计算引擎(GCE)和机器学习引擎(MLE)。

The `gcloud compute` command-line tool lets you manage your Google Compute Engine resources in a friendlier format than using the Compute Engine API. The `gcloud init` command shown in Figure 3-9 allows you to update the parameters of the VM if you decide to change them later.

![img/468661_1_En_3_Fig9_HTML.jpg](img/468661_1_En_3_Fig9_HTML.jpg) Figure 3-9 使用 `gcloud init` 配置 GCE 虚拟机

For installing packages such as Java, Weka, or Casssandra, SSH access is the best method. You launched this earlier from the dashboard (Figure 3-6). If you wish to do this from the command line, you can use the following:

```
gcloud compute --project "subtle-bus-204821" ssh --zone "us-east1-b" "instance-1"
```

Cloud SDK 提供了无限的命令行可能性。查看下面所示的 GCP `gcloud` 参考页面，了解所有可用的 `gcloud` 命令:

`https://cloud.google.com/sdk/gcloud/reference/`

## 谷歌云客户端库

谷歌让你很容易将 Java 与所有的 GCP 服务结合使用。GitHub 上提供了 GCP Java 客户端库:

`https://github.com/GoogleCloudPlatform/google-cloud-java`

Google 推荐调用 Google Cloud APIs 的客户端库。据 Google 称，它们通过使用每种受支持语言的自然约定和风格，提供了优化的开发人员体验。

Java 客户端库对于希望集成 GCP 服务的 Android 开发人员也很有用。

## Eclipse 的云工具(CT4E)

第一章讲述了为 Java 建立 Eclipse 开发环境。即使 Android 开发人员不再使用 Eclipse 来支持 Android Studio，Google 也一直支持 Eclipse IDE。毫不奇怪，他们为 Eclipse 提供了一个云工具插件(CT4E)。

图 3-10 显示了 CT4E 文档页面。该插件可从以下网址获得:

`https://github.com/GoogleCloudPlatform/google-cloud-eclipse`

CT4E wiki 页面还包含许多有用的信息:

`https://github.com/GoogleCloudPlatform/google-cloud-eclipse/wiki/Cloud-Tools-for-Eclipse-Technical-Design`

![img/468661_1_En_3_Fig10_HTML.jpg](img/468661_1_En_3_Fig10_HTML.jpg) Figure 3-10 Eclipse 快速入门和文档的云工具

CT4E 支持在 Eclipse IDE 版本 4.5 和更高版本中的 GCP 上开发 Java 应用程序。使用 CT4E，您可以构建运行在 GCE 之上的 web 应用程序。

## GCP 云机器学习引擎(ML 引擎)

你一直在探索谷歌云平台和云 SDK。现在你会看云 ML 引擎，谷歌机器学习引擎 API。

## 3.3 亚马逊 AWS

AWS 有令人眼花缭乱的云服务。似乎每周他们都会推出一项新服务。在本节中，您将探索 AWS 的机器学习方面。

AWS 有一个为期一年的免费试用，允许您探索许多服务，包括 ML。

Earlier you saw that Synergy Research Group placed AWS in a league of its own. Consistent with their research, AWS does seem to have some advantages:

*   AWS 提供更强大的服务、区域、配置等。很难跟踪所有的 AWS 产品。
*   AWS 有一个发达的市场。第三方供应商打包免费和商业解决方案。这些市场产品简化了设置，因为您不必担心所有的设置步骤。
*   对于 ML，AWS 是框架不可知的。AWS ML 支持自带算法和自带框架，提供了最大的灵活性。

Table 3-4 shows a list of the AWS ML and EC2 compute services.Table 3-4

AWS ML 服务

| 服务 | 描述 |
| --- | --- |
| Amazon Comprehend | 亚马逊的 NLP 解决方案。`Amazon Comprehend` 可以提取关于文档内容的见解。 |
| AWS DeepLens | AWS DeepLens 是一个无线摄像机和 API，允许您开发计算机视觉应用程序。 |
| Amazon Lex | 一种使用语音和文本在任何应用程序中构建对话界面的服务。 |
| 机器学习 | AWS 核心 ML 服务，用于创建和部署 ML 模型。 |
| Amazon Polly | 将文本转换成逼真语音的云服务。你可以使用 `Amazon Polly` 来开发增加参与度和可访问性的应用程序。 |
| Amazon SageMaker | 完全托管的机器学习服务。根据数据和预测的类型，可以使用许多算法。`SageMaker` 允许用户在 AWS 上部署 `TensorFlow` 或在 AWS 上部署 `Apache MXNet`。 |
| Amazon Rekognition | 图像和视频分析的 ML API。该服务可以识别物体、人、文本、场景和活动。 |
| Amazon Transcribe | 使用 ML 识别音频文件中的语音，并将其转录为文本。 |
| Amazon Translate | 使用 ML 在英语和其他六种语言之间翻译文档。 |
| EC2 计算引擎 | `EC2` 是主要的 AWS 计算引擎，可以用来管理 ML 的 VM 实例，包括 AWS 深度学习 AMI。 |

这些服务中有许多类似于 GCP 提供的 DL 产品。您将重点关注两个突出显示的 ML 核心服务：AWS 机器学习和 SageMaker。

### GCP 自由层定价详细信息

如果你决定使用 GCP 为你的 ML 项目，你可以利用一年的免费试用期开始。通过免费试用，您将可以使用所有谷歌云平台(GCP)产品。该试用版包括构建和运行您的应用、网站和服务所需的一切。

The free trial has some limitations:

*   服务水平协议(SLA)不适用。这对于免费层产品来说是合理的。如果服务因任何原因变得不可用，您没有追索权。
*   计算引擎限于八个内核。
*   并非所有服务都可用。
*   不允许加密货币挖掘。
*   免费试用期为 12 个月或 300 美元的使用费。
*   不自动续订。免费试用结束后不会自动收费。

GCP 免费试用是一个非常好的交易，当然可以帮助您确定服务是否适合您的项目。

While you use your GCP free trial, keep in mind that if you go over the trial usage limits shown below, charges will apply:

*   每月 1 个 `f1-微型` 虚拟机实例(美国地区，不包括北弗吉尼亚)
*   每月 30GB 的标准永久磁盘存储
*   每月 5GB 的快照存储
*   每月从北美到其他目的地的 1GB 出口(不包括澳大利亚和中国)

### GCP Cloud ML Engine

Figure 3-11 shows the GCP Cloud ML Engine setup page. The first step is to enable the API. It can take up to 10 minutes to enable.![img/468661_1_En_3_Fig11_HTML.jpg](img/468661_1_En_3_Fig11_HTML.jpg) Figure 3-11

云机器学习引擎 API

Cloud ML Engine API 允许您创建和使用机器学习模型。在上一节中，您看到了如何创建一个可以托管任何软件包的 VM 实例，比如 Weka ML 环境。Cloud ML 引擎通过 API 让你直接与特定的 Google ML 工具接口，从而简化了这个过程。

The downside is that you are restricted to the ML frameworks that Google MLE supports. Google Cloud MLE currently supports the following frameworks:

*   面向 `TensorFlow` 的云 ML 引擎
*   `scikit-learn` 和 `XGBoost` 的云 ML 引擎
*   `Keras` 的云 ML 引擎

Note that none of the Google ML Engine options are Java-based.

> GCP ML 服务非常适合希望基于 `TensorFlow` 框架创建和部署 DL 模型的开发人员。最近，Google 增加了对其他框架的支持，包括 `scikit-learn`、`XGBoost` 和 `Keras`。Google 声明基于 Python 的 `scikit-learn` 是为希望部署经典 ML 模型的开发人员准备的。

如果您确实希望在 GCE 上试验 Tensorflow，Google 提供了一个很好的教程：

[https://cloud.google.com/solutions/running-distributed-tensorflow-on-compute-engine](https://cloud.google.com/solutions/running-distributed-tensorflow-on-compute-engine)

本教程展示了如何在多个 GCE 实例上设置 `TensorFlow` 的分布式配置。本教程介绍了 MNIST 数据集。MNIST 在机器学习中被广泛用作图像识别的训练集。我会在第四章详细讨论。

如果您希望探索 `TensorFlow`，但不想使用 Google MLE 创建自己的模型，还有另一个选项。您可以使用预构建的 DL 模型，并通过 GCP ML API 访问它们。在本章的后面，你将为 Android 实现这个项目。

### AWS 机器学习

At the heart of AWS ML is the ML service. Similar to GCP, its main interface is the dashboard. Figure 3-12 shows the AWs ML dashboard. The AWS ML dashboard shows all of your AWS ML work items in a single integrated interface.![img/468661_1_En_3_Fig12_HTML.jpg](img/468661_1_En_3_Fig12_HTML.jpg) Figure 3-12

AWS ML 仪表板

Figure 3-12 shows several types of items the AWS dashboard can manage, including

*   数据源
*   模型
*   批量预测
*   评价

仪表板和直观的 AWS ML 工作流使导入数据、构建模型和评估模型结果变得容易。

### AWS ML 构建和部署模型

为了演示 AWS ML 有多简单，让我们回到第二章介绍的 `PAMAP2_Dataset`。在这个简短的示例中，您将加载数据集，构建 ML 模型，评估模型，甚至进行一些批量预测。

# 在 AWS ML 上使用 PAMAP2 数据集构建第一个模型

## 上传数据到 S3

S3 是 AWS 存储服务。使用 AWS ML 的第一步是将数据上传到 S3。回顾一下，数据集包含几个大文件。每个文件有 54 个属性（列）和超过 300,000 个实例（行）。你将使用 `subject101.dat` 文件在 AWS ML 上构建你的第一个模型。在第 2 章中，你已经将文件转换为 CSV 格式。图 3-13 显示了上传到 AWS S3 后的 CSV 文件。上传时，你需要指定 S3 存储桶的唯一名称。在这个示例中，S3 存储桶被命名为 `pamap2`。
![img/468661_1_En_3_Fig13_HTML.jpg](img/468661_1_En_3_Fig13_HTML.jpg) 图 3-13 上传 ML 数据到 AWS S3

由于文件的大小，上传需要几分钟才能完成。AWS S3 显示数据文件的大小为 135MB。即使对于 CML 应用程序，数据大小也可能很大，这就是 AWS 等云平台的优势所在。

## 指定输入数据

将数据上传到 S3 后，下一步是指定 AWS ML 的输入数据。最简单的方法是使用 S3 数据访问。图 3-14 显示了 AWS ML 创建数据源 - 输入数据界面。在 `S3 location` 字段中指定你希望使用的 S3 存储桶的名称。
![img/468661_1_En_3_Fig14_HTML.jpg](img/468661_1_En_3_Fig14_HTML.jpg) 图 3-14 使用 S3 输入数据的 AWS ML 输入数据

AWS ML 将验证数据，并告知你验证是否成功。如果不成功，你需要记录具体问题，然后返回到 OpenOffice 更正数据。数据验证中最常见的三个问题如下：

*   不正确的字段分隔符是最常见的验证问题。确保在 CSV 文件中使用逗号分隔符。
*   在文本字段周围使用引号。文本中的逗号字符（，）会导致问题。OpenOffice 可以用引号将所有文本字段括起来。
*   保存不带 BOM（字节顺序标记）字符的文件。

## 设置模式

数据验证成功后，下一步是设置模式。图 3-15 显示了 AWS ML 创建数据源 - 模式界面。
![img/468661_1_En_3_Fig15_HTML.jpg](img/468661_1_En_3_Fig15_HTML.jpg) 图 3-15 AWS 检测数据模式

当 AWS ML 验证您的数据时，它会为每个属性（列）分配一个名称和数据类型。在这一步中，您需要浏览每个属性，并确认这些赋值是正确的。这也是检查属性数量是否正确的好时机；在这种情况下，有 54 个属性。这证实了您的 CSV 值解析是正确的。

## 指定目标

AWS ML 创建数据源序列中的第 3 步是指定 *目标*。在第二章中，你将这个重要的属性定义为 *标签*。在分类中， *标签* 是你试图预测的值。

在 `PAMAP2_Dataset` 中，标签位于第 2 列，由赋值名 `Var02` 标识。该标签代表 *活动 ID* ，如表 2-4 中所述。指定标签后，按 Continue，并继续 AWS ML 模型设置。现在是创建模型的时候了。

## 模型设置与创建

图 3-16 显示了 AWS ML 模型设置界面。这是魔法发生的地方。
![img/468661_1_En_3_Fig16_HTML.jpg](img/468661_1_En_3_Fig16_HTML.jpg) 图 3-16 AWS ML 模型设置

在 AWS ML 模型设置中，您有两个选项。您可以选择默认或自定义模型训练方法。

剧透警告：无论你选择哪种方法，你都不会从这个模型中得到一个好的结果。

自定义选项允许您指定几个项目，如训练数据的划分、评估数据的随机或顺序分割，以及其他一些训练参数。

问题是，即使您有一个大型数据集，AWS ML 也会为您选择一个回归算法，不管您是选择自定义还是使用默认算法。AWS ML 强制执行此模型选择，因为您为标签输入了单个数值。

对 `PAMAP2_Dataset` 执行回归不会产生很好的结果。然而，现在不要担心结果。你将在第四章中详细探讨学习类别的匹配算法。

点击继续按钮，模型创建完成。因为数据集很大，所以可能需要几分钟时间。请注意，您甚至不必使用 VM（虚拟机）创建计算资源来创建模型。

## 模型评估与预测

在 AWS ML 创建模型后，您可以评估模型并进行批量预测。图 3-17 显示了模型的评估摘要。
![img/468661_1_En_3_Fig17_HTML.jpg](img/468661_1_En_3_Fig17_HTML.jpg) 图 3-17 AWS ML 模型评估

评估摘要提供了有用的信息。如您所见，模型创建时间为 4 分钟，包括 2 分钟的计算时间，考虑到数据集如此之大，这还算不错。

模型性能显示了令人失望的结果：回归算法产生了比基线更差的质量分数。对于回归任务，AWS 使用标准的 `RMSE`（均方根误差）度量作为基线。对于 `RMSE` 基线，该值越接近零越好。本书的目标之一是避免数学方程，但是如果你想了解更多关于 `RMSE` 基线的知识，AWS 有一个关于测量回归模型准确性的极好的页面：

`https://docs.aws.amazon.com/machine-learning/latest/dg/regression-model-insights.html`

建立模型的目的是利用它进行预测。AWS ML 允许实时、单次或批量预测。批预测特别有用，它允许您加载许多实例来分类为一个批。AWS ML 通过让您将批量预测加载到 S3 存储桶中来实现这一点，就像您加载原始数据集一样。然后，您只需指定批量预测的 S3 位置，然后模型将生成结果。进行批量预测确实会增加成本，我将在本节的最后讨论这一点。

## 总结与展望

AWS ML 是一个设计得非常好的服务。在从加载数据到进行预测的过程中创建的所有资产，都可以在仪表板上使用。在流程的任何阶段进行更改并尝试结果都很容易。

> 在短短几分钟内，AWS ML 服务允许您加载和验证数据、定义模式、构建模型、评估模型以及进行批量预测，所有这些都由集中的 AWS ML 仪表板控制。这简单得可笑。

对于那些需要对 ML 算法的选择进行更多控制的情况，例如复杂的 `PAMAP2_Dataset` ，接下来让我们探索如何使用 AWS 计算资源来构建您自己的 ML 环境。

### AWS EC2 朋友

EC2 是 AWS 计算引擎服务。缩写代表弹性计算云。EC2 使用 AMI（Amazon Machine Interface）来定义它的虚拟实例。

# 在 AWS EC2 上部署 ML 环境

EC2 支持许多类型的 `ami`。在 AWS EC2 上构建您自己的 ML 环境的第一步是选择一个 `AMI` 类型来支持您打算部署的应用程序软件。

图 3-18 显示了几种可用的 `AMI` 类型。`AMI` 类型跨越整个操作系统范围，包括 `Amazon Linux`、`Ubuntu`、`Red Hat`、`CentOS`、`Windows` 等。

If you look closely at Figure 3-18, you will see several AMIs that are deep learning-based. This is just another example of how easy AWS makes it to deploy ML solutions. In this example, you will select the `Deep Learning AMI (Amazon Linux) version 13.0`.

![AWS AMI 选择](img/468661_1_En_3_Fig18_HTML.jpg)
*Figure 3-18 AWS AMI 选择*

AWS ML 是框架不可知的。你看到谷歌 `GCP` 对 `TensorFlow` 的关注度很高；相比之下，AWS ML 提供了许多框架选项。当您基于深度学习基础 AMIs 创建新实例时，AWS 会将流行的 ML 框架与该实例打包在一起。这些特殊的 `ami` 包含了 `Apache MXNet`、`TensorFlow`、`Caffe`、`Caffe2`、`PyTech`、`Keras`、`Theano`、`CNTK`、`Nvidia`、`CUDA` 等各种组合。如果你回头看第一章的表 `1-10` ，你会发现 AWS 深度学习基础 AMIs 几乎包括了所有显示的 ML 框架。这消除了下载和安装所有 ML 框架包的需要，节省了时间。

## 实例类型选择

After you select your AMI, the next step allows you to choose an instance type. Figure 3-19 shows the AWS instance type selection.

![AWS 实例类型配置](img/468661_1_En_3_Fig19_HTML.jpg)
*Figure 3-19 AWS 实例类型配置*

对于这个例子，您将选择 `??.micro` 实例类型。这是一年免费层试用中唯一可用的实例类型。它能够处理许多应用程序，包括您在云中托管 `Weka` ML 环境的目标。有时，AWS 会警告您，您选择的实例不可用于空闲层。这通常是由存储设置造成的。如果您收到此警告，请在启动实例之前仔细检查您的存储是否超过 30GB。您可以在配置的 `存储` 部分编辑存储。

`??.micro` 实例对于了解 AWS ML 来说很好，但是 Amazon 推荐 ML 训练和预测的更高级配置。Amazon 推荐使用 `ml.m4`、`ml.c4` 和 `ml.c5` 实例族进行训练，使用 `ml.c5.xl` 实例类型进行预测。

在访问实例之前，必须配置其安全性。继续完成各个步骤，包括为安全 shell 访问分配密钥。

Figure 3-20 shows the instance once it is up and running.

![AWS 实例摘要](img/468661_1_En_3_Fig20_HTML.jpg)
*Figure 3-20 AWS 实例摘要*

请注意图 3-20 中的实例有一个公共 IP 和 DNS 名称。这是您从外部世界访问实例的方式。您将使用安全 shell (`SSH`)和您在实例安全配置期间设置的密钥。

实例运行后，您就可以在 AWS 云中安装和运行 `Weka` 了。

## 在 AWS 云中运行 Weka ML

Connect to the public IP of the instance using a secure `SSH` client, or `Putty` if you are on Windows. When connecting, note that the user name is `ec2-user` and the credentials are the key that you created at the completion of the setup process. Figure 3-21 shows the initial connection.

![与 AWS EC2 实例的连接](img/468661_1_En_3_Fig21_HTML.jpg)
*Figure 3-21 与 AWS EC2 实例的连接*

When you first connect to the instance, you can change into the `/usr/local` directory and see the preinstalled deep learning packages included with the AMI:

```
001   [ec2-user@ip-172-31-3-37 local]$ pwd
002   /usr/local
003   [ec2-user@ip-172-31-3-37 local]$ ls -lsart
004   total 50096
005         4 drwxr-xr-x  2 root root     4096 Jan  6  2012 src
006         4 drwxr-xr-x  2 root root     4096 Jan  6  2012 sbin
007         4 drwxr-xr-x  2 root root     4096 Jan  6  2012 libexec
008         4 drwxr-xr-x  2 root root     4096 Jan  6  2012 games
009         4 drwxr-xr-x  2 root root     4096 Jan  6  2012 etc
010         4 drwxr-xr-x 13 root root     4096 Jan 15 18:42 ..
011         4 drwxr-xr-x  8 root root     4096 May  8 21:12 share
012         4 drwxr-xr-x 18 root root     4096 May  8 21:13 cuda-8.0
013         4 drwxr-xr-x 19 root root     4096 May  8 21:15 cuda-9.0
014         4 drwxr-xr-x 19 root root     4096 May 10 21:17 cuda-9.1
015         4 drwxr-xr-x 19 root root     4096 May 10 21:19 cuda-9.2
016         4 drwxr-xr-x  7 root root     4096 May 10 08:18 mpi
017         4 drwxr-xr-x  7 root root     4096 May 10 09:39 lib64
018         4 drwxr-xr-x  9 root root     4096 May 10 09:39 include
019         4 drwxr-xr-x  7 root root     4096 May 10 09:39 lib
020         4 drwxr-xr-x  2 root root     4096 May 10 09:39 test
021         4 drwxr-xr-x 22 root root     4096 May 10 09:39 caffe2
022         4 drwxr-xr-x  3 root root     4096 May 10 09:39 caffe
023         4 drwxr-xr-x  2 root root     4096 May 10 09:39 bin
024         0 lrwxrwxrwx  1 root root       20 May 10 09:51 cuda -> /usr/local/cuda-9.0/
025         4 drwxr-xr-x 18 root root     4096 May 30 02:35 .
```

注意上面的代码显示了深度学习 `AMI (Amazon Linux)` 版本 `13.0` 可用的包。AWS 会不断更新这些包，所以在启动 `AMI` 后，您可能会看到稍微不同的内容。

`Weka` 不包括在内，所以你需要自己添加。

注意：您将把 `sudo` 添加到以下每个 Unix 命令的前面，以避免权限问题。

Weka 需要 Java，因此您首先需要检查 Java 是否已包含，如果是，是哪个版本：
```
[ec2-user@ip-172-31-3-37 local]$ java -version
openjdk version "1.8.0_121"
OpenJDK Runtime Environment (Zulu 8.20.0.5-linux64) (build 1.8.0_121-b15)
OpenJDK 64-Bit Server VM (Zulu 8.20.0.5-linux64) (build 25.121-b15, mixed mode)
```
和云提供商的虚拟机实例一样，它们通常预装了 OpenJDK Java 发行版。OpenJDK 对大多数应用来说是没问题的。然而，对于 Weka 安装，必须使用 Oracle Java JDK，因为 Weka 需要一些未包含在 OpenJDK 中的 Swing GUI 库。如果您尝试使用 OpenJDK 运行 Weka，您会看到如下异常，表明一个 Sun 启动器类无法加载：
```
[ec2-user@ip-172-31-3-37 local]$ sudo java -cp weka.jar weka.classifiers.trees.J48 -t /usr/local/weka-3-8-2/data/iris.arff
Exception in thread "main" java.lang.UnsupportedClassVersionError: weka/classifiers/trees/J48 : Unsupported major.minor version 52.0
           at java.lang.ClassLoader.defineClass1(Native Method)
           at java.lang.ClassLoader.defineClass(ClassLoader.java:803)
           at java.security.SecureClassLoader.defineClass(SecureClassLoader.java:142)
           at sun.misc.Launcher$AppClassLoader.loadClass(Launcher.java:312)
           at java.lang.ClassLoader.loadClass(ClassLoader.java:358)
           at sun.launcher.LauncherHelper.checkAndLoadMain(LauncherHelper.java:482)
```
解决方案是使用以下 `wget` 命令下载 Oracle JDK。第 001-006 行设置环境变量。第 008 行执行 `wget`。第 010 行安装您下载的 Oracle JDK 包。
```
[ec2-user@ip-172-31-3-37 local]$ java_base_version="8"
[ec2-user@ip-172-31-3-37 local]$ java_sub_version="141"
[ec2-user@ip-172-31-3-37 local]$ java_base_build="15"
[ec2-user@ip-172-31-3-37 local]$ java_version="${java_base_version}u${java_sub_version}"
[ec2-user@ip-172-31-3-37 local]$ java_build="b${java_base_build}"
[ec2-user@ip-172-31-3-37 local]$ java_version_with_build="${java_version}-${java_build}"

[ec2-user@ip-172-31-3-37 local]$ sudo wget --no-check-certificate --no-cookies --header "Cookie: oraclelicense=accept-securebackup-cookie" http://download.oracle.com/otn-pub/java/jdk/${java_version_with_build}/336fa29ff2bb4ef291e347e091f7f4a7/jdk-${java_version}-linux-x64.rpm

[ec2-user@ip-172-31-3-37 local]$ sudo yum install -y jdk-8u141-linux-x64.rpm
```
Oracle JDK 安装完成后，您的虚拟机实例上现在安装了多个版本的 Java。以下命令显示您实际上有三个版本：两个版本的 OpenJDK 和新的 Oracle JDK。它还允许您选择 Oracle JDK 作为当前选择。
```
[ec2-user@ip-172-31-3-37 local]$ sudo alternatives --config java

There are 3 programs which provide 'java'.

  Selection    Command
-----------------------------------------------
   1           /usr/lib/jvm/jre-1.7.0-openjdk.x86_64/bin/java
   2           /usr/lib/jvm/jre-1.8.0-openjdk.x86_64/bin/java
*+ 3           /usr/java/jdk1.8.0_141/jre/bin/java

Enter to keep the current selection[+], or type selection number: 3
[ec2-user@ip-172-31-3-37 local]$
```
现在 Java 已经准备就绪，是时候下载并安装最新稳定版本的 Weka 了：
```
[ec2-user@ip-172-31-3-37 local]$ sudo wget http://svwh.dl.sourceforge.net/project/weka/weka-3-8/3.8.2/weka-3-8-2.zip
--2018-05-30 02:35:43--  http://svwh.dl.sourceforge.net/project/weka/weka-3-8/3.8.2/weka-3-8-2.zip
Resolving svwh.dl.sourceforge.net (svwh.dl.sourceforge.net)... 72.5.72.15, 2606:c680:0:b:3830:34ff:fe66:6663
Connecting to svwh.dl.sourceforge.net (svwh.dl.sourceforge.net)|72.5.72.15|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 51223056 (49M) [application/octet-stream]
Saving to: ‘weka-3-8-2.zip’

weka-3-8-2.zip             100%[======================================>]  48.85M  39.8MB/s    in 1.2s

2018-05-30 02:35:45 (39.8 MB/s) - ‘weka-3-8-2.zip’ saved [51223056/51223056]
```
下载完成后，确认 `weka-3-8-2.zip` 文件存在于 `/usr/local` 目录中：
```
[ec2-user@ip-172-31-3-37 local]$ pwd
/usr/local
[ec2-user@ip-172-31-3-37 local]$ ls -lsart weka*
total 50096
50024 -rw-r--r--  1 root root 51223056 Dec 21 21:16 weka-3-8-2.zip
[ec2-user@ip-172-31-3-37 local]$
```
接下来，按照下图解压 Weka，完成后进入新的 `weka-3-8-2` 目录，您将看到新目录中的以下内容：
```
[ec2-user@ip-172-31-3-37]$ pwd
/usr/local/

[ec2-user@ip-172-31-3-37]$ sudo unzip weka-3-8-2.zip

[ec2-user@ip-172.31.3.37]$ cd weka-3-8-2

[ec2-user@ip-172-31-3-37 weka-3-8-2]$ ls -l
total 42908
drwxr-xr-x 2 root root     4096 Dec 22 09:30 changelogs
-rw-r--r-- 1 root root    35147 Dec 22 09:30 COPYING
drwxr-xr-x 2 root root     4096 Dec 22 09:30 data
drwxr-xr-x 3 root root     4096 Dec 22 09:30 doc
-rw-r--r-- 1 root root      510 Dec 22 09:30 documentation.css
-rw-r--r-- 1 root root     1863 Dec 22 09:30 documentation.html
-rw-r--r-- 1 root root    16170 Dec 22 09:30 README
-rw-r--r-- 1 root root    43377 Dec 22 09:30 remoteExperimentServer.jar
-rw-r--r-- 1 root root 14763219 Dec 22 09:30 wekaexamples.zip
-rw-r--r-- 1 root root    30414 Dec 22 09:30 weka.gif
-rw-r--r-- 1 root root   359270 Dec 22 09:30 weka.ico
-rw-r--r-- 1 root root 11111002 Dec 22 09:30 weka.jar
-rw-r--r-- 1 root root  6621767 Dec 22 09:30 WekaManual.pdf
-rw-r--r-- 1 root root 10923433 Dec 22 09:30 weka-src.jar
```
您现在已准备好在云中运行 Weka。要从命令行运行 Weka，您指示 Java 在 Weka 附带的数据集之一上运行一个分类器类。在此示例中，您将在 `iris.arff` 数据集上运行随机森林分类器。要运行指定的类而不是主类，请提供 `–cp` 选项。以下显示了成功的 Weka 分类：
```
[ec2-user@ip-172-31-3-37 weka-3-8-2]$ sudo java -cp weka.jar weka.classifiers.trees.J48 -t/usr/local/weka-3-8-2/data/iris.arff

=== Classifier model (full training set) ===

J48 pruned tree
------------------
...
Number of Leaves  :     5
Size of the tree :      9
Time taken to build model: 0.48 seconds
Time taken to test model on training data: 0.01 seconds

=== Error on training data ===

Correctly Classified Instances         147               98      %
Incorrectly Classified Instances         3                2      %
Kappa statistic                          0.97
Mean absolute error                      0.0233
Root mean squared error                  0.108
Relative absolute error                  5.2482 %
Root relative squared error             22.9089 %
Total Number of Instances              150

...

=== Confusion Matrix ===

  a  b  c   <-- classified as
 50  0  0 |   a = Iris-setosa
  0 49  1 |   b = Iris-versicolor
  0  2 48 |   c = Iris-virginica

Time taken to perform cross-validation: 0.08 seconds 

=== Stratified cross-validation ===

...

=== Detailed Accuracy By Class ===

...

=== Confusion Matrix ===

  a  b  c   <-- classified as
 49  1  0 |   a = Iris-setosa
  0 47  3 |   b = Iris-versicolor
  0  2 48 |   c = Iris-virginica
```

我将在第四章中详细介绍 Weka。这个例子表明，在 AWS 云上实现 Weka 非常简单。

### pagemaker

您已经看到了在 AWS 上构建 ML 的几种方法，首先是通过仪表板使用 AWS ML 服务，其次是在 AWS EC2 计算实例上实现自己的 Weka 环境。

`AWS SageMaker` is a fully managed platform to help you build DL models. It is one of the recently added AWS services. The main idea behind `SageMaker` is that ML has been difficult for developers for the following reasons:

*   收集数据、处理数据、构建模型、测试模型和部署模型的过程给开发人员带来了过多的手动工作。

*   由于重复的手动工作，创建 ML 解决方案非常耗时。

*   创建 ML 解决方案太复杂了，因为所需的数据和分析技能已经取代了传统的软件开发。

`SageMaker` tries to address these issues. It promises to remove complexity and overcome the barriers that slow down developers. Figure 3-22 shows the main AWS `SageMaker` page.![img/468661_1_En_3_Fig22_HTML.jpg](img/468661_1_En_3_Fig22_HTML.jpg) Figure 3-22

pagemaker

像所有的 AWS 服务一样，有大量的在线文档帮助您理解服务。`SageMaker` 开发人员指南的链接是

[https://docs.aws.amazon.com/sagemaker/latest/dg](https://docs.aws.amazon.com/sagemaker/latest/dg)。

`SageMaker` 很有潜力。两个特别重要的特性使它成为在 AWS- notebook 实例上实现 ML 的一种强大方式，以及它对算法的灵活支持。

`SageMaker` notebook 实例是一个运行 `Jupyter Notebook` 应用程序的计算实例。`Jupyter` 是一个开源的 web 应用程序，运行在 Python(因此它的拼写)上，允许你创建和共享包含实时代码和可视化的文档。它在 Python 和 DL 领域非常流行。

托管 `Jupyter` 笔记本可以轻松浏览和可视化亚马逊 S3 存储中的训练数据，类似于第二章中介绍的 JavaScript 选项。`Jupyter` 有几个内核，包括对 Python、`Apache MXNet`、`TensorFlow` 和 `PySpark` 的支持。`Jupyter` 不支持 Java 内核。笔记本实例是用 `SageMaker` 实现 ML 的重要部分。

AWS 实验室在 GitHub 上保留了一些优秀的例子供您探索 `SageMaker`:

[https://github.com/awslabs/amazon-sagemaker-examples](https://github.com/awslabs/amazon-sagemaker-examples)

`SageMaker` 的另一个有趣的特性是它的算法灵活性。`SageMaker` 支持两类算法:内置算法和自带算法。内置算法列表可从以下网址获得

[https://docs.aws.amazon.com/sagemaker/latest/dg/algos.html](https://docs.aws.amazon.com/sagemaker/latest/dg/algos.html)。

算法列表非常完整。AWS 声称，由于优化，预装算法的性能是其他提供商的 10 倍。这是一个令人印象深刻的说法。然而，AWS 没有提供他们如何做到这一点的细节，或者它适用于哪些算法。

用户可以自带算法或框架。GitHub 上的 `SageMaker` 示例展示了如何为各种模型和算法实现这一点，包括 `XGBoost`、`k-means`、`R`、`scikit`、`MXNet` 和 `TensorFlow`。

AWS `SageMaker` 提供了令人印象深刻的 ML 功能，但不幸的是，由于它依赖于 `Jupyter`，所以不能很好地与 Java 集成。接下来，您将探索 AWS SDK for Java。

### 用于 Java 的 AWS SDK

Amazon supports Java developers. To show their love for us, they release the SDK for Java to help us accelerate our development. Figure 3-23 shows the main Explore Java on the AWS page available at [https://aws.amazon.com/java/](https://aws.amazon.com/java/) *.* The page includes links for Java, `Eclipse`, and Android.![img/468661_1_En_3_Fig23_HTML.jpg](img/468661_1_En_3_Fig23_HTML.jpg) Figure 3-23

AWS Java 开发人员中心

The Java SDK is available in several formats. Table 3-5 provides a summary of the formats.Table 3-5

AWS Java SDK 包摘要

<colgroup class="calibre12"><col class="tcol"> <col class="tcol"></colgroup> 

| 

格式

 | 

笔记

 |

| --- | --- |

| 面向 Java 1.11 版的 SDK | 当前支持的 SDK。允许您从 Java 访问所有 AWS 服务。 |

| `Eclipse` 的 Java 工具包 | AWS Toolkit for `Eclipse` 方便地包含了 AWS SDK for Java，因此您可以开始在 `Eclipse` 中的 AWS 基础设施服务上构建 Java 应用程序，包括 `Amazon S3`、`Amazon EC2`、`Amazon DynamoDB` 和 Amazon ML。 |

| Android 版 SDK | 一整套文档、库和示例，帮助您将 AWS 服务集成到 Android 应用中。 |

| 面向 Java 的机器学习 SDK | 这是一个专门为 AWS ML 开发的 SDK。它只能从 Maven 资源库下载。如果您只需要 AWS ML 访问，这个较小的库通过排除所有其他 AWS 服务来保持您的项目规模精简。 |

| 面向 Java 版的 SDK | AWS SDK for Java 2.0 Developer Preview 构建状态。2.0 版本目前是预览版，不建议用于生产。 |

表格中显示的前三种格式可以通过直接点击 AWS Java 开发中心提供的链接来下载。

Android 下载的 SDK 包括许多用于所有 AWS 服务的库文件、示例和文档。图 3-24 显示了 ML 库 jar 文件。注意只有 48Kb。您可以查看。jar 库使用 `7-Zip` 实用程序打开归档文件。Maven 资源库的链接是

[http://central.maven.org/maven2/com/amazonaws/aws-android-sdk-machinelearning](http://central.maven.org/maven2/com/amazonaws/aws-android-sdk-machinelearning) .![img/468661_1_En_3_Fig24_HTML.jpg](img/468661_1_En_3_Fig24_HTML.jpg) Figure 3-24

Android 机器学习库的 AWS SDK

There are two versions of the SDK for Java. Version Java 1 is the currently available version, and Java 2 is a developer preview rewrite of Java 1 with some new features. Both versions enable you to easily interface your Java projects with AWS. Java 2 includes two key new features:

*   非阻塞 IO

*   可插拔 HTTP 协议栈

# 3.4 机器学习 API

## 概述

如果您为 Android 进行开发，您可能知道 Google 最近已停止在 Android 中使用 Apache `HTTPClient` 堆栈。Java 2 中的可插拔 HTTP 功能遵循了这一趋势，因为 Java 1 仅支持 `HTTPClient`。使用 Java 2.0，您可以使用其他堆栈，例如 `HTTPurlConnection` 或 `OkHTTP`。

有了 Java SDK，您可以在几分钟内开始使用 Maven 或任何支持 Maven Central 作为工件源的构建系统。《开发人员指南》包括详细的设置和安装说明，可从以下网址获得：
`https://docs.aws.amazon.com/sdk-for-java/v1/developer-guide/welcome.html`

如果您对 Java SDK 的代码感兴趣，可以从以下站点获得：
- `https://github.com/aws/aws-sdk-java`
- `https://github.com/aws/aws-sdk-java-v2`

如果您不需要 SDK 支持的所有 AWS 服务，您可以从以下网址下载用于机器学习的 SDK：
`https://mvnrepository.com/artifact/com.amazonaws/aws-java-sdk-machinelearning`

Figure 3-25 shows the Maven repository for the Java machine learning library. You can download the .jar file or see the instructions for Maven or Gradle depending on your build environment.

```
img/468661_1_En_3_Fig25_HTML.jpg
```

**Figure 3-25** 用于 Java 的 AWS ML SDK 的 Maven 仓库

在 AWS Java 机器学习 SDK 的帮助下，任何通过 AWS ML 仪表板可用的 ML 任务都可以从您的 Java 代码中以编程方式重现。

### AWS 自由层定价详细信息

与 GCP 类似，AWS 提供 12 个月的介绍期。免费层包括一些永不过期的免费服务。其他免费服务在 12 个月内有效。亚马逊提供免费服务覆盖范围的全部细节，网址为：
`https://aws.amazon.com/free/`

The AWS free tier is quite generous. The main highlights of the free tier include:

*   750 小时的 EC2 `??.micro` 实例。您可以根据需要配置实例。您之前使用 `??.micro` 实例在云中配置 Weka。
*   AWS 在空闲层提供 5GB 的 S3 存储。这对于探索 AWS ML 来说已经足够了。
*   AWS 每月允许 100 万次 API 调用。开发人员可以使用这些 API 调用来探索 AWS ML APIs。

You can use the `??.micro` instance for almost anything you wish, including machine learning. There are some additional considerations for developers considering the AWS free tier to explore ML:

*   AWS 支持北美和全球许多地区的 EC2 实例。并非所有服务在每个地区都可用。如果您决定使用 AWS ML 服务，您需要在创建实例之前选择一个支持该服务的地区。
*   使用 AWS ML 的批量预测不包括在自由层定价中，并且不免费。批量预测的成本为每 1，000 次预测 0.10 美元。参见表 1-5 。
*   AWS EC2 实例有公共 IP 地址。如果您停止并重新启动实例，IP 地址将会改变。如果您希望为 EC2 实例分配一个永久的 IP 地址，那么您需要创建 AWS 所说的弹性 IP。弹性 IP 不是免费的，不包括在 AWS 免费层中。使用新的 IP 地址停止并重新启动 EC2 实例不会导致与实例相关的任何配置或数据丢失。

如果你追求的是 AWS SageMaker for ML，除了总的免费等级之外，AWS 还提供额外的每月 250 小时的免费等级，用于构建模型，外加 50 小时的 SageMaker 培训。

有时候，您可能不需要构建和部署自己的 ML 模型。在这些情况下，您可以利用四大云提供商提供的云 API。

In the preceding section, I covered the AWS ML services. Figure 3-26 summarizes the high-level APIs provided by the big four cloud providers: Amazon, Google, IBM, and Microsoft. All of their APIs fall into five distinct categories: language, vision, data insights, speech, and search.

```
img/468661_1_En_3_Fig26_HTML.jpg
```

**Figure 3-26** 高级 ML API 比较

Google 和 AWS 在提供我们实现 ML 解决方案所需的底层工具和构件方面做得很好，而 IBM 和微软在提供我们可以通过 API 访问的高层模型方面做得同样出色。图 3-26 显示他们有许多 API 来解决五个类别中的各种各样的问题。

这些 API 中的大多数都采用了从云提供商拥有的大量数据中创建的 DL 方法。这些 API 大部分是免费试用的。如果您决定在商业上使用这些 API，您通常只需要为每次 API 调用支付云提供商的推断费。回想一下，推断费是做预测的费用。您可以进行实时预测或批量预测。价格近似值见表 1-5 。

### 使用 ML REST APIs

如果您决定使用图 3-26 中的某个服务，他们很可能会允许通过 REST API 调用来访问该服务。使用 REST 调用用 ML 模型 API 进行预测(推断)很容易。API 通常使用 REST 协议，JSON 数据格式将在第二章中介绍。

当对 API 进行 REST 调用时，请求和响应都是 JSON 格式的。例如，清单 3-1 显示了一个 GCP 云视觉 API JSON 请求。该清单只展示了最重要的字段:您的 API 键、对图像源的引用以及所请求的特性。如果您希望实现这个 API 调用，请参考下面的链接，了解有关特定 JSON 要求的完整细节:
`https://cloud.google.com/vision/docs/request`

```
001   POST https://vision.googleapis.com/v1/images:annotate?key=YOUR_API_KEY
002   {
003     "requests": [
004       {
005         "images": {
006           "content": "your_image.jpg"
007         },
008         "features": [
009           {
010             "type": "LABEL_DETECTION"
011           }
012         ]
013       }
014     ]
015   }
```

**Listing 3-1** GCP 云愿景 API JSON 请求示例

发布请求后，您将收到一个 JSON 响应。清单 3-2 展示了一个成功的 JSON 响应；在此例中，您获得了为请求中提交的图像文件所做出的前两个图像标签预测。
```json
001   {
002     "responses": [
003       {
004         "labelAnnotations": [
005           {
006             "mid": "/m/01yrx",
007             "description": "cat",
008             "score": 0.92562944
009           },
010           {
011             "mid": "/m/0307l",
012             "description": "cat like mammal",
013             "score": 0.65950978
014           }
015         ]
016       }
017     ]
018   }
```
**清单 3-2**
GCP 云愿景 API JSON 响应示例

为了帮助您提交 JSON 请求和解析 JSON 响应，Java SDK 提供了示例代码。此外，Java 客户端库可用于各种 API。对于这个例子，参考云视觉 API 客户端库，选择 Java 标签，在[此处](https://cloud.google.com/vision/docs/libraries)。

在下一节中，您将使用这种方法构建一个完整的 Android 应用程序，使用 JSON 来访问强大的 GCP 云语音 API。谷歌云语音 API 将允许你转录设备录制的音频文件。

### 替代 ML API 提供者

有时候，您可能需要考虑替代的云 API 模型提供商。如果你有一个利基应用程序没有被大的云玩家覆盖，专门从事某些应用程序的替代提供商可以提供一个解决方案。

有时，您只是希望将您的产品与所有使用大型云提供商 API 的竞争对手区分开来。在这些情况下，使用替代的小型云 API 提供商可能是一个可行的策略。

表格 3-6 展示了一些替代的云 ML API 提供商。
**表格 3-6**
备选云 ML API 提供商

| 供应者 | 描述 |
| --- | --- |
| [www.diffbot.com/products/automatic/](http://www.diffbot.com/products/automatic/) | 数据析取 |
| [www.beyondverbal.com/api/](http://www.beyondverbal.com/api/) | 一家以色列公司的情感和声音分析 |
| [www.kairos.com/face-recognition-api](http://www.kairos.com/face-recognition-api) | 人脸识别 |
| [https://wit.ai/getting-started](https://wit.ai/getting-started) | 猫机器人 |
| [www.openalpr.com/cloud-api.html](http://www.openalpr.com/cloud-api.html) | 实时车牌识别 |

无论您使用替代的 ML API 还是来自四大云提供商的 ML API，都有大量的产品可供你选择。如果你回想 M 门，在 MLG6，你必须从一个明确定义的问题开始。此时，最佳实践是扫描可用的 API，看看它们是否与问题完全匹配。没有必要重新发明轮子。大型云提供商拥有如此多的数据，很难创造出比他们提供给我们的模型更好的解决方案。虽然四大云提供商有许多 API，但探索外部替代方案是否可用可能会有所收获。

## 3.5 项目:Android 版 GCP 云语音 API

在这个项目中，您将实现 GCP 云语音 API Android 应用程序。您将使用 Android Studio IDE。该项目由 Google (Apache license 2.0)版权所有并发布，可从以下链接下载:
[https://github . com/Google cloud platform/Android-docs-samples/tree/master/Speech/Speech](https://github.com/GoogleCloudPlatform/android-docs-samples/tree/master/speech/Speech)

### 云语音 API 应用概述

从 GitHub 下载项目并将其导入 Android Studio。表格 3-7 显示了关键项目文件的摘要。
**表格 3-7**
GCP 云语音 API 项目文件摘要

| 项目信息 |
| --- |
| 项目名称：GCP 云语音 API<br>来源：GitHub 谷歌云语音平台<br>类型：Android 应用程序 |

| 文件 | 描述 |
| --- | --- |
| `app->src->main->Java->MainActivity.java` | 检查设备权限、启动录音机和语音服务以及设置主视图的主要活动。 |
| `app->src->main->Java->SpeechService.java` | 用于处理 API 访问的服务。这个 Android 服务实现了 GCP 云语音 API 的接口，包括认证和实时口语流。 |
| `app->src->main->Java->MessageDialogFragment.java` | 一个简单的 Android `DialogFragment` 类，应用程序使用它向用户显示消息。 |
| `app->src->main->Java->VoiceRecorder.java` | 这个类实现了 Android `AudioRecord` 类用于语音录制。 |
| `app->src->main->res->layout->main.xml` | 主 XML 布局。 |
| `app->src->main->res->raw->credential.json` | 在 GCP 云 API 中心创建的 JSON 凭证文件。将文件放入 `res/raw` 文件夹。 |
| `app->src->main->res->raw->audio.raw` | 存储在 `/res/raw` 文件夹中的样本音频文件，可以发送给 API 进行分类。音频文件是“布鲁克林大桥有多老了？” |
| `app->src->main->AndroidManifest.xml` | 应用程序清单文件。定义活动和服务。 |

图 3-27 显示了 Android 应用的截图。该应用程序的概念是直接的。该应用程序接受用户的音频输入，并使用 Google Cloud Speech API 来翻译音频并显示文本翻译。该应用程序支持两种音频输入方法：
*   该应用程序可以从设备麦克风录制音频。音频以原始 PCM 格式录制。在第二章中，您看到了数字化语音是一种数据形式。稍后我将讨论更多关于音频文件格式的内容。录制的音频被实时发送到云语音 API 进行翻译。
*   用户也可以按下“加载文件”按钮来加载预先录制的音频文件，该文件将被发送到 API 进行翻译。

The Cloud Speech API uses deep learning to translate recorded audio to text. The app receives the translated text and displays it in real time as the user speaks. Because the API uses deep learning, it does a really impressive job at translating voice input.![img/468661_1_En_3_Fig27_HTML.jpg](img/468661_1_En_3_Fig27_HTML.jpg) Figure 3-27

## GCP 机器学习云语音 API 安卓应用

在设备上运行应用程序允许您使用麦克风录制音频。该应用程序实时批处理口语单词，并将它们传递给 API。如果您正在使用仿真器运行应用程序，您将被限制为按下`加载文件`按钮来为 API 提供音频以进行翻译。

### GCP 机器学习 API

Before you can get the app running on your Android device, you need to perform the following two actions on the Google Cloud Platform dashboard:

*   启用云语音 API。

*   创建 Android 应用程序所需的认证密钥。

要启用 API，请访问以下链接：

[https://console.cloud.google.com/apis/library?filter=category:machine-learning](https://console.cloud.google.com/apis/library?filter=category:machine-learning)

Figure 3-28 shows the currently available GCP Cloud ML APIs, including the Cloud Speech API you wish to implement. Click the Cloud Speech API and enable it.![img/468661_1_En_3_Fig28_HTML.jpg](img/468661_1_En_3_Fig28_HTML.jpg) Figure 3-28

GCP 云 ML APIs

接下来，您需要创建 Android 应用程序所需的认证密钥。

### 云语音 API 认证

云语音 API Android 应用需要你提供一个 JSON 文件认证密钥。该文件将被命名为`credential.json`，并被放置在 app 的`raw`文件夹中。按照以下步骤创建文件。

The first step is to create a service account key. The service account is required for authentication. Figure 3-29 shows the`Credentials`tab within the API dashboard. Choose`Create credentials`, and then select`Service account key`from the drop down list.![img/468661_1_En_3_Fig29_HTML.jpg](img/468661_1_En_3_Fig29_HTML.jpg) Figure 3-29

GCP 创建服务帐户密钥

The`Create service account key`dialog box will be displayed, as shown in Figure 3-30.![img/468661_1_En_3_Fig30_HTML.jpg](img/468661_1_En_3_Fig30_HTML.jpg) Figure 3-30

选择 JSON 服务帐户密钥类型

The service account should appear as`speech2text`, and you should select the`JSON`type. Press the`Create`button and the private key will be saved to your computer, as shown in Figure 3-31.![img/468661_1_En_3_Fig31_HTML.jpg](img/468661_1_En_3_Fig31_HTML.jpg) Figure 3-31

保存 JSON 私钥文件

私钥将以基于项目名称的文件名保存到您的计算机上。如果将项目名称保留为默认值，它将如下所示：

`My First Project-d817dcf314.json`

将此文件重命名为

`credential.json`

The JSON configuration file contents will look similar to the following and is required to access the API from Android:
```json
{
  "type": "service_account",
  "project_id": "subtle-bus-204821",
  "private_key_id": "xxxxxxxxxxxxxxxxxxxxx",
  "private_key": "-----BEGIN PRIVATE KEY-----\nxxxxxxx\n-----END PRIVATE KEY-----\n",
  "client_email": "speech2text@subtle-bus-204821.iam.gserviceaccount.com",
  "client_id": "xxxxxxxxxxxxxxxxxxxxx",
  "auth_uri": "https://accounts.google.com/o/oauth2/auth",
  "token_uri": "https://accounts.google.com/o/oauth2/token",
  "auth_provider_x509_cert_url": "https://www.googleapis.com/oauth2/v1/certs",
  "client_x509_cert_url": "https://www.googleapis.com/robot/v1/metadata/x509/speech2text%40subtle-bus-204821.iam.gserviceaccount.com"
}
```

最后将`credential.json`文件复制到 Android app 的`res/raw`目录下。复制完文件后，就可以在 Android Studio 中成功构建 Android 应用了。

值得注意的是，在应用程序中放置私钥来处理身份验证是测试 API 的一种有用而简单的方法，但这种方法对于生产应用程序来说是不可接受的。对于生产应用程序，您应该实现自己的应用程序服务器来处理应用程序用户的身份验证。因为您只是在测试 API，所以您可以复制。

You can always review and manage your service account keys in the dashboard, as shown in Figure 3-32.![img/468661_1_En_3_Fig32_HTML.jpg](img/468661_1_En_3_Fig32_HTML.jpg) Figure 3-32

显示活动凭据

### Android 音频

With the backend setup complete, you can focus on the client Android app. In this section, you will cover the following key events:

*   录制原始音频

*   向 API 发送音频

*   处理 API JSON 响应

Audio is a form of data. ML often uses audio data as input. Android devices are excellent for working with audio because the hardware supports many codecs for encoding and decoding.

> Android 设备是处理音频数据的优秀工具。Android 支持许多编解码器。`AudioRecord` 和 `AudioTrack` 类支持原始未压缩音频的录制和处理。虽然 Android audio 的延迟不是很大，但大多数 ML Audio APIs 支持高吞吐量，并且不需要低延迟。

## Table 3-8 Android 支持的音频格式

| 多媒体数字信号编解码器 | 编码 | 译 | 细节 | 文件类型 |
| :--- | :--- | :--- | :--- | :--- |
| 加气混凝土 | 4.1+ | Y | 单声道/立体声/5.0/5.1 最高 48khz 采样 | 3GPP、MP4、ADTS AAC |
| 自主移动机器人（Adaptive Multi-Rate 的缩写） | Y | Y | 5-12 千位/秒 12-24 千位/秒 | 第三代合作伙伴项目（the3rdGenerationPartnershipProject） |
| 无损音频编解码器 | Y | Y (3.1+) | 单声道/立体声/最高 44.1/48khz | 无损音频编解码器 |
| MP3 文件 | 普通 | Y | 单色/立体声/8-320kbps | MP3 文件 |
| 迷迪 | 普通 | Y | 支持铃声 | 中间的 |
| Vorbis 先生 | 普通 | Y |  | OGG，水手 |
| 脉冲编码调制 | Y (4.1+) | Y | 8 位/16 位线性 PCM 速率达到硬件极限 | 波浪 |
| 作品 | 普通 | Y (5.0+) |  | -水手 |

GCP 云语音 API 可以接受几种类型的音频数据。flac)、PCM(。wav)文件、AMR 和 Linear-16。FLAC 和 PCM 格式表示原始的未压缩音频数据。虽然压缩的音频数据(如 MP3 文件)传输到云端会更小、更快，但压缩会引入噪声，并可能影响准确性。

Android `AudioRecord` 类是一个非常强大的低级音频 API。任何处理音频的严肃 app，最有可能使用的是 `AudioRecord`。在云语音 API app 中，`AudioRecord` 类用于记录未压缩的原始音频，传递给 API 进行翻译。

Listing 3-3 shows the key audio recording loop in the app. The `ProcessVoice` class shown resides inside the `VoiceRecorder.java` file. The code runs on a thread and is responsible for continuously reading uncompressed voice data from the `AudioRecord` object into the `mBuffer` byte array (line 013).

```java
001   private AudioRecord mAudioRecord;
002   private Thread mThread;
003   private byte[] mBuffer;
004 
005   private class ProcessVoice implements Runnable {
006       @Override
007       public void run() {
008           while (true) {
009               synchronized (mLock) {
010                   if (Thread.currentThread().isInterrupted()) {
011                       break;
012                   }
013                   final int size = mAudioRecord.read(mBuffer, 0, mBuffer.length);
014                   final long now = System.currentTimeMillis();
015                   if (isHearingVoice(mBuffer, size)) {
016                       if (mLastVoiceHeardMillis == Long.MAX_VALUE) {
017                           mVoiceStartedMillis = now;
018                           mCallback.onVoiceStart();
019                       }
020                       mCallback.onVoice(mBuffer, size);
021                       mLastVoiceHeardMillis = now;
022                       if (now - mVoiceStartedMillis > MAX_SPEECH_LENGTH_MILLIS) {
023                           end();
024                       }
025                   } else if (mLastVoiceHeardMillis != Long.MAX_VALUE) {
026                       mCallback.onVoice(mBuffer, size);
027                       if (now - mLastVoiceHeardMillis > SPEECH_TIMEOUT_MILLIS) {
028                           end();
029                       }
030                   }
031               }
032           }
033       }
034   }
```

**Listing 3-3** VoiceRecorder.java 境内的 `ProcessVoice` 类

The code in Listing 3-3 is a typical audio recording implementation. One of the features that makes the app so powerful is the use of the following callbacks:

*   `onVoiceStart` :当录音机开始听到声音时调用。
*   `onVoice` :录音器听到声音时调用。
*   `onVoiceEnd` :当录音器听不到声音时调用。

这些方法的使用是应用程序能够在用户说话时提供实时翻译的原因。

通过将数据传递给 `SpeechService.java` 中的 `识别输入流` 方法，来自麦克风的原始音频被发送到云语音 API。

# SpeechService.java

`SpeechService.java` 在应用内作为 Android 服务运行，负责与云 API 进行交互。代码清单 3-4 展示了该服务如何为 API 构建 JSON 请求消息。

```
001   public void recognizeInputStream(InputStream stream) {
002       try {
003           mApi.recognize(
004                   RecognizeRequest.newBuilder()
005                           .setConfig(RecognitionConfig.newBuilder()
006                                   .setEncoding(RecognitionConfig.AudioEncoding.LINEAR16)
007                                   .setLanguageCode("en-US")
008                                   .setSampleRateHertz(16000)
009                                   .build())
010                           .setAudio(RecognitionAudio.newBuilder()
011                           .setContent(ByteString.readFrom(stream))
012                           .build())
013                           .build(),
014                   mFileResponseObserver);
015       } catch (IOException e) {
016           Log.e(TAG, "Error loading the input", e);
017       }
018   }
```

**代码清单 3-4** 在 `SpeechService.java` 建立 API 请求

在通过其深度学习模型处理每个音频数据流后，Cloud Speech API 将文本结果发送回服务。该服务为你处理了一切。代码清单 3-5 展示了摘自 `MainActivity.java` 的一段代码。此代码设置了一个 `SpeechService` 监听器，并在收到文本结果时将其填充到用户界面中。

```
001   private final SpeechService.Listener mSpeechServiceListener =
002           new SpeechService.Listener() {
003               @Override
004               public void onSpeechRecognized(final String text, final boolean isFinal) {
005                   if (isFinal) {
006                       mVoiceRecorder.dismiss();
007                   }
008                   if (mText != null && !TextUtils.isEmpty(text)) {
009                       runOnUiThread(new Runnable() {
010                           @Override
011                           public void run() {
012                               if (isFinal) {
013                                   mText.setText(null);
014                                   mAdapter.addResult(text);
015                                   mRecyclerView.smoothScrollToPosition(0);
016                               } else {
017                                   mText.setText(text);
018                               }
019                           }
020                       });
021                   }
022               }
023           };
```

**代码清单 3-5** 在 `MainActivity.java` 听文本结果

## 云语音 API 应用摘要

`Google Cloud Speech API` 应用程序是在云中利用深度学习模型的有力例子。这是一个高级实现，因为它提供了一个基于服务的架构来处理所有的 API 接口。回调与服务架构的结合使用提供了实时翻译并创建了无缝的用户体验。

这是一个可以为其他 GCP 机器学习 API 复制的架构。请记住，这也会产生大量的网络流量和 API 访问量。

一旦你成功部署了像 Cloud Speech API 这样的解决方案，你应该定期重新访问仪表板以检查流量和错误。图 3-33 展示了记录有流量的 Cloud Speech API 仪表板。请注意 API 流量量，特别是在免费试用限制内工作时。

![img/468661_1_En_3_Fig33_HTML.jpg](img/468661_1_En_3_Fig33_HTML.jpg)

**图 3-33** GCP 监控 API 访问量

## 3.6 用于机器学习的云数据

云服务提供商擅长存储数据。虽然他们的许多机器学习服务相对较新，但他们提供的数据存储服务从一开始就已经存在。

他们提供的数据存储服务可以采取多种格式，包括：

*   容器图像
*   传统数据库
*   NoSQL 数据库
*   API 和服务
*   虚拟计算机

在本章的前面，您看到了其中的两种方法。在使用 `PAMAP2_Dataset` 演示 AWS 机器学习控制台时，您使用了 AWS S3 存储桶对象存储。然后，在云中设置 Weka 时，您使用虚拟机进行存储。接下来，您将研究非结构化数据的爆炸如何导致 NoSQL 数据库作为存储解决方案的使用增加。

### 非结构化数据

第二章讨论了一个大趋势：数据爆炸。我们将非结构化数据定义为很少或没有元数据以及很少或没有分类的数据。机器学习经常使用非结构化数据。非结构化数据包括许多类别，如视频、电子邮件、图像、物联网设备数据、文件共享、安全数据、监控数据、日志文件、web 数据、用户和会话数据、聊天、消息、twitter 流传感器数据、时间序列数据、物联网设备数据和零售客户数据。

非结构化数据可以通过三个 V 来表征：volume（量）、velocity（速度）和 variety（多样性）。

*   量：数据的大小。参见表 2-2。
*   速度：数据生成的速度。例如，喷气发动机传感器每秒可以产生数千个样本。
*   多样性：有许多不同种类的数据。

Figure 3-34 shows the exponential growth of unstructured data relative to structured data.![img/468661_1_En_3_Fig34_HTML.jpg](img/468661_1_En_3_Fig34_HTML.jpg) Figure 3-34 非结构化数据的增长

传统数据库的问题在于它们难以扩展，并且不太适合非结构化数据。在云中存储非结构化数据的最佳方式之一是使用 `NoSQL` 数据库，因为他们在处理这种类型的数据方面做得更好。

### NoSQL 数据库

`NoSQL` 代表“不是 SQL”这种方法不同于传统的关系数据库管理系统(`RDBMS`)。`SQL` 是关系数据库使用的查询语言。这些数据库依靠表、列、行或模式来组织和检索数据。`NoSQL` 数据库不依赖于这些结构，使用更灵活的数据模型。许多主流企业都采用了 `NoSQL`。

`NoSQL` 数据库的好处包括：
*   可扩展性:水平扩展架构使得添加商品化节点以增加容量变得容易。
*   性能:您可以根据需要通过添加商品化资源来轻松提高性能。
*   高可用性:`NoSQL` 数据库通常使用无主架构，这使得它们不太容易停机。
*   全球可用性:分布式数据库可以跨机器和地理区域复制。
*   灵活的建模:处理 `JSON` 表示的文档或非结构化数据意味着没有严格的数据类型、表或索引。

要理解 `NoSQL` 数据库如何与传统的 `RDBMS` 数据库不同，回顾 `CAP` 定理很有用，该定理由 `Eric Brewer` 最初提出。`CAP` 定理指出，对于分布式数据库架构，不可能同时提供以下三个保证中的两个以上：
*   一致性:每次读取都会收到最近的写入或错误。
*   可用性:始终可以读取或写入系统，但不保证它包含最新的值。
*   分区容差:尽管节点之间的网络丢弃或延迟了任意数量的消息，系统仍能继续运行。

图 3-35 显示了 `CAP` 定理的图形表示，包括许多流行的 `SQL` (`RDBMS`)和 `NoSQL` 数据库的分类。![img/468661_1_En_3_Fig35_HTML.jpg](img/468661_1_En_3_Fig35_HTML.jpg) Figure 3-35 根据 `CAP` 定理的数据库分类

传统的 `SQL` 数据库位于三角形的左侧。它们支持一致性和可用性。然而，它们不易以分布式方式进行分区。分区是 `NoSQL` 架构大规模可扩展性的关键要素。三角形显示了流行的 `NoSQL` 数据库位于三角形的底部和右侧。

数据库理论家使用了两个有趣的术语来描述这些数据库哲学：
*   `ACID`:原子性、一致性、隔离性、持久性
*   `BASE`:基本可用，柔软状态，最终一致性

`RDBMS` 数据库选择 `ACID` 以实现一致性和可用性。分布式 `NoSQL` 数据库选择 `BASE` 以实现分区/一致性或分区/可用性。许多流行的 `NoSQL` 数据库使用 `BASE` 哲学。

表 3-9 显示了最受欢迎的 `NoSQL` 数据库总结。

Table 3-9 流行的 NoSQL 数据库

| 数据库ˌ资料库 | 描述 |
| --- | --- |
| `Google Cloud Firestore` (中文版) | 谷歌的 `NoSQL` 大数据数据库服务。谷歌表示，它可以以低延迟和高吞吐量处理大量工作负载。它支持许多谷歌服务，如地图、`Gmail` 和搜索。 |
| `AWS DynamoDB` | 从亚马逊完全管理的专有 `NoSQL` 数据库。`DynamoDB` 支持键值和文档数据结构。高耐用性和可用性。 |
| `Apache HBase` | 分布式、可扩展的大数据存储。`HBase` 是 `Hadoop` 数据库。`Apache` 项目的目标是托管包含数十亿行和数百万列的非常大的表。用 `Java` 编写，模仿 `Google` 的 `Bigtable`。 |
| `Riak` | 来自 `Basho` 的分布式 `NoSQL` 数据库。允许您存储大量的非结构化键值数据。`IoT` 的流行解决方案。 |
| `Apache Cassandra` | 高度可伸缩的 `NoSQL` 数据库。由于体系结构的选择，声称优于其他 `NoSQL` 数据库。被 `Netflix`、苹果、`eBay` 等使用。 |
| `MongoDB` | 跨平台、基于文档、基于 `JSON` 类文档的 `NoSQL` 数据库。 |
| `CouchDB` | 分布式面向 `NoSQL` 文档的数据库，为交互式应用程序而优化。 |

### NoSQL 数据存储方法

表 3-8 中显示的 `NoSQL` 数据库存在差异。选择 `NoSQL` 数据库时，关键考虑因素是它们如何存储数据。有四种类型的 `NoSQL` 数据存储：
*   键值数据存储
*   文档存储
*   宽列存储
*   图形存储

表 3-10 显示了流行的 `NoSQL` 产品如何归入这些类别。

Table 3-10 NoSQL 数据存储方法

| 数据存储类型 | 特征 | 例子 |
| --- | --- | --- |
| 键/值 | 按关键字索引的哈希表。完全在内存中或内存和磁盘的组合中。不支持辅助索引。 | `Redis`, `Memcached` |
| 文件 | 原则存储对象是一个文档，通常是 `JSON` 格式。支持辅助索引。提供小到中等的可扩展性。在内部，对象以二进制形式存储为 `BSON` 或 `BLOBs`。 | `MongoDB`, `CouchDB`, `Couchbase`, `DynamoDB` |
| 图表 | 用于存储连接的数据集。 | `Neo4j` |
| 宽栏 | 提供宽大的桌子。支持数百万列。提供巨大的可扩展性。支持大数据。 | `HBase`, `Cassandra` |

在选择 `NoSQL` 数据库时，数据大小和性能也是需要考虑的重要因素。`MongoDB` 和 `CouchDB` 是中小型数据集的绝佳选择，而 `Cassandra` 则是大型数据集的绝佳选择。

性能是一个复杂的话题，超出了本章的范围。对于 `CML` 项目，您不需要顶级性能。如果您对 `NoSQL` 性能指标评测感兴趣，`Datastax` 网站上有一个很好的总结：[www.datastax.com/nosql-databases/benchmarks-cassandra-vs-mongodb-vs-hbase](http://www.datastax.com/nosql-databases/benchmarks-cassandra-vs-mongodb-vs-hbase)。

云提供商包含对许多 `NoSQL` 数据库的支持。图 3-36 显示了 `AWS` 数据库快速启动页面。![img/468661_1_En_3_Fig36_HTML.jpg](img/468661_1_En_3_Fig36_HTML.jpg) Figure 3-36 AWS 数据库快速启动选项

你可以看到 AWS 为 Couchbase、MongoDB、两种版本的 Cassandra 以及它自己的 DynamoDB NoSQL 产品提供了 NoSQL 快速入门包。

## Apache Cassandra Java 接口

使用 Quickstart 软件包可以轻松地在 AWS 或 GCP 等云提供商上部署 NoSQL 数据库。在这最后一部分，您将在很高的层次上探索如何从 Java 程序连接到 Apache Cassandra NoSQL 数据库。

DataStax 是 Apache Cassandra 的主要分销商之一。它适用于 AWS 和 Google 计算引擎，可通过以下链接获得：

[`https://AWS.Amazon.com/quickstart/architecture/datastax-enterprise/`](https://aws.amazon.com/quickstart/architecture/datastax-enterprise/)

[`https://console.cloud.Google.com/marketplace/details/datastax-public/datastax-enterprise`](https://console.cloud.google.com/marketplace/details/datastax-public/datastax-enterprise)

请注意，DataStax 不收取 DataStax Enterprise 的费用，但云提供商将收取计算资源费用。云提供商和 DataStax 联手，使得在云中构建高度可伸缩的 Cassandra 集群变得非常容易。

DataStax 发行版的一个重要特性是可以为所有流行的编程语言提供驱动程序，包括 Java。DataStax Java 驱动程序可从以下网址获得：

[`https://github.com/datastax/java-driver`](https://github.com/datastax/java-driver)。

DataStax Java 驱动程序 GitHub 站点有一个非常好的 `Readme.md` 文件，其中包含了关于如何使用 Java 和 Cassandra 快速入门的有用信息。以下是 DataStax Cassandra 发行版和 Java 驱动程序的一些亮点：

*   开源驱动程序（Apache 2.0）适用于所有主要语言，包括 Java。
*   DataStax Java 驱动程序包含连接到 Cassandra 和执行查询的逻辑。
*   DataStax Java 驱动程序支持同步和异步查询。
*   该驱动程序是一个功能丰富且高度可调的 Java 客户端库。
*   驱动程序支持 Apache Cassandra (2.1+)。
*   驱动程序支持 Cassandra 的二进制协议。
*   驱动程序支持 Cassandra 查询语言 v3。

以下代码展示了如何使用 DataStax 驱动程序从 Java 连接到 Cassandra 数据库并执行查询：

```java
Cluster cluster = null;
try {
    // Connect to the cloud Cassandra cluster
    cluster = Cluster.builder()
            .addContactPoint("ip_address")
            .build();
    Session session = cluster.connect();

    // Provide a Query String for the execute method
    ResultSet rs = session.execute("select release_version from system.local");

    // Get the first row
    Row row = rs.one();
    System.out.println(row.getString(0)); // The first column;
} finally {
    if (cluster != null) cluster.close();
}
```

Cassandra 使用 Cassandra 查询语言 (`CQL`)。`CQL` 与 SQL 非常相似。有几种访问 `CQL` 的方式，包括：

*   在 Cassandra 节点的命令行上启动命令行客户端 `cqlsh`。
*   使用 DataStax `DevCenter`，一个图形用户界面。
*   对于开发应用程序，您可以使用官方的 DataStax Java 驱动程序之一。

最有用的 `CQL` 命令之一是 `COPY` 命令。`CQL` 的 `COPY` 命令允许你导入和导出 CSV 文件。回想一下第二章，这些文件是 ML 管道不可分割的一部分。只需一个命令，您就可以填充整个 Cassandra 数据库或将 Cassandra NoSQL 数据库备份到一个 CSV 文件，以便离线处理。有关 `CQL` `COPY` 命令的完整详细信息，请访问：

[`https://docs.datastax.com/en/cql/3.1/cql/cql_reference/copy_r.html`](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/copy_r.html)。

有关其他有用的 `CQL` 命令（包括其他 Java 代码示例）的更多详细信息，请参考位于：

[`https://docs.datastax.com/en/developer/java-driver/3.4/manual/`](https://docs.datastax.com/en/developer/java-driver/3.4/manual/)。

使用像 Cassandra 这样基于云的 NoSQL 数据库的优势之一是可伸缩性。如果您正在收集大量的 ML 数据，并且需要存储这些数据，这将非常有用。几年前，谷歌在谷歌计算引擎上使用 Cassandra 实现了每秒 100 万次写入。您可以在这篇博客文章中了解部署细节：

[`https://cloudplatform.googleblog.com/2014/03/cassandra-hits-one-million-writes-per-second-on-google-compute-engine.html`](https://cloudplatform.googleblog.com/2014/03/cassandra-hits-one-million-writes-per-second-on-google-compute-engine.html)

根据作者的说法，2014 年实现这一结果的成本仅为每百万次写入 0.07 美元。这说明了云平台强大的价值主张。

## 云平台概述

本章有一个宏大的目标：涵盖用于机器学习的云平台。云 ML 是一个发展迅速的领域，特点是快速迭代。在您继续学习后续章节关于算法和 ML 环境的内容时，请记住以下发现：

*   当构建 ML 解决方案时，总是首先扫描可用的云服务，看看是否存在一个可以用最少的模型构建或编码来解决问题的云服务。
*   在过去几年中，每个云提供商都投入了大量资源来部署计算资源、存储解决方案、模型构建平台和深度学习 API，您可以在应用程序中利用这些资源。
*   利用云平台提供的免费层来查看特定服务是否适用于您的特定应用。
*   谷歌通过发布的 JSON API 使 Android 应用程序很容易利用谷歌 ML 引擎服务。
*   像 Cassandra 这样的 NoSQL 数据库允许你实现大规模，并且相对容易配置。

许多服务，尤其是那些由云提供商提供的开源包提供的服务是免费的，但最终用户将为消耗的任何计算资源付费。幸运的是，激烈的行业竞争压低了这些成本。