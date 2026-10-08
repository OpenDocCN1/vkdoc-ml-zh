# 第 5 章 自治数据库中的 Oracle 机器学习

Oracle 机器学习 (OML) 利用数据库的强大功能来处理机器学习过程的数据，而无需将数据移出数据库，也不会危及数据安全。OML 支持三种编程语言：SQL (OML4SQL)、R (OML4R) 和 Python (OML4Py)。SQL 和 Python 通过其 API 在 Oracle 自治数据库中自动可用。很快，R 也很有可能可用。目前 R 仅在 Oracle 数据库上可用，在自治数据库上不可用。

要在 OCI 中使用 Oracle 机器学习，您需要一个 Oracle 云基础设施 (OCI) 帐户和一个 Oracle 自治数据库。您还需要一个具有 Oracle 机器学习权限的用户。

## 设置 Oracle 机器学习环境

为了能够使用 OML，您需要一个 *OCI 帐户*、一个 *自治数据库* 和一个 *OCI 用户*。OML 不需要单独的许可证，因此 Oracle Cloud Free Tier 帐户足以测试该服务。如果您没有 OCI 帐户，请访问 [www.oracle.com/cloud/free/](http://www.oracle.com/cloud/free/) 并按照说明创建一个 Free Tier 帐户。登录该帐户。

## 创建自治数据库

如果您还没有自治数据库，则需要创建一个。如果您已经创建了自治数据库，则可以跳过此步骤。

© Adrian Png 和 Heli Helskyaho 2022

A. Png 和 H. Helskyaho，《*使用 Oracle 云功能扩展 Oracle Application Express*》，

[`doi.org/10.1007/978-1-4842-8170-3_5`](https://doi.org/10.1007/978-1-4842-8170-3_5#DOI)

![](img/index-152_1.jpg)

如果您想为自治数据库创建一个单独的区间，请转到 OCI 控制台中的导航菜单，选择 *Identity & Security* 和 *Compartment*。单击 *Create Compartment*。为区间定义一个 *名称* 和一个 *描述*，如图 5-1 所示。从 *Parent Compartment* 列表中选择父区间。单击 *Create Compartment*。

图 5-1. 创建区间

然后创建数据库。从 OCI 导航菜单中选择 *Oracle Database*，然后选择 *Autonomous Database*。选择您要用于此自治数据库的 *区间*，如图 5-2 所示。然后，单击 *Create Autonomous Database*。

![](img/index-153_1.jpg)

![](img/index-153_2.jpg)

图 5-2. 选择区间并创建自治数据库

首先，您需要为自治数据库提供基本信息，如图 5-3 所示。验证 *区间* 是否正确。如果不正确，请从列表中选择正确的区间进行更改。Oracle 会自动建议一个 *显示名称* 和一个 *数据库名称*，但您可以根据需要更改其中一个或两个。

图 5-3. 创建自治数据库的基本信息

然后，选择 *工作负载类型*（图 5-4）和 *部署类型*。选择 *Transaction Processing* 和 *Shared Infrastructure*。如果您更喜欢创建用于数据仓库的自治数据库，请选择 *Data Warehouse* 作为工作负载类型。

![](img/index-154_1.jpg)

![](img/index-154_2.png)

图 5-4. 为自治数据库选择工作负载类型和部署类型

然后，配置数据库，如图 5-5 所示。选择 *数据库版本*。如果您使用的是付费帐户，请定义 *OCPU 计数*、*存储* 大小，并可能启用 *OCPU 自动缩放*。对于 Free Tier 帐户，唯一的配置选项是数据库版本。

图 5-5. 为自治数据库选择数据库版本、OCPU 计数和存储大小

然后，为数据库的管理员用户 (`Admin`) 定义密码，如图 5-6 所示。

![](img/index-155_1.jpg)

![](img/index-155_2.png)

图 5-6. 为自治数据库的管理员用户定义密码

选择网络访问，如图 5-7 所示。

## 第 5 章 自治数据库中的 Oracle 机器学习

***图 5-7.** 为自治数据库定义网络访问*

选择 `许可证类型` 并定义一个 `联系电子邮件地址` 以接收通知或公告，如图 5-8 所示。单击 `Show Advanced Options`。

![配置界面图片1](img/index-156_1.jpg)

![配置界面图片2](img/index-156_2.jpg)

***图 5-8.** 为自治数据库选择许可证类型并提供操作通知和公告的联系信息*

选择 `加密管理设置`（图 5-9）。在 `维护` 下，您可以选择 `补丁级别`。默认是 `Regular`。对于付费帐户，如果您的区域支持，您可以将补丁级别更改为 `Early`。在 `标签` 中，您可以为数据库添加标签。单击 `Create Autonomous Database`。

***图 5-9.** 为自治数据库定义加密管理设置、维护首选项，并在需要时添加标签*

![加密设置图片](img/index-157_1.png)

![配置完成图片](img/index-157_2.jpg)

数据库现已配置完毕。`生命周期状态` 为 `Provisioning`，ATP 徽标的颜色为黄色，直到数据库完全配置完毕。然后颜色变为绿色，生命周期状态变为 `Available`（图 5-10）。

***图 5-10.** 自治数据库的详细信息页面*

### 获取使用 OML 服务所需的 URL

为了能够使用 Oracle 机器学习服务，您需要正确的 URL。单击 `Service Console`。使用 `Admin` 作为用户和您之前创建的密码登录。导航到 `Development`，如图 5-11 所示。

***图 5-11.** 自治数据库的开发部分*

![开发部分图片](img/index-158_1.jpg)

向下滚动到 `Oracle Machine Learning RESTful services`（图 5-12）。复制 `Use this URL to obtain a REST authentication token for OML-provided REST APIs`、`All OML Services REST APIs use the following common base URL` 和 `All embedded Python REST APIs use the following common base URL` 的 URL。将它们存储在一个安全的地方。

***图 5-12.** 获取使用 Oracle 机器学习所需的 URL*

要获取 OML 用户界面的 URL，请单击 `Oracle Machine Learning User Interface`（图 5-12）并保存该 URL。如果您想使用 OML 控制台（例如，构建笔记本或使用 AutoML），您将需要此 URL。

### 创建 OML 用户

为了能够在自治数据库中使用 OML 功能，您需要拥有一个具有 `OML 权限` 的数据库用户。如果您有现有的数据库帐户，但尚未将其添加到机器学习，则需要添加它。如果您已经在自治数据库中创建了 OML 用户，则可以跳过此步骤。

至少有两种简单的方法可以创建新用户或将现有数据库用户添加到机器学习：Oracle ML 用户管理或数据库操作。

![用户管理入口图片](img/index-159_1.jpg)

![用户管理入口图片2](img/index-159_2.jpg)

#### 使用 Oracle ML 用户管理创建用户

在 `Autonomous Database Details` 页面（图 5-10）上，选择 `Tools` 选项卡（图 5-13）。

***图 5-13.** 自治详细信息页面上的工具选项卡*

导航到 `Oracle ML User Administration`，然后单击 `Open Oracle ML Administration`。

使用您的 `Admin` 凭据登录。单击 `Create`（图 5-14）。

***图 5-14.** Oracle 机器学习用户管理页面*

定义（图 5-15）`用户名`，可选地定义 `名字` 和 `姓氏`，以及 `电子邮件地址`。如果您想现在定义密码，请禁用 `Generate password and email account details to user. User will be required to reset the password on first sign in.`。单击 `Create`。

![用户定义图片](img/index-160_1.jpg)

![用户定义完成图片](img/index-160_2.jpg)

***图 5-15.** 创建新的机器学习用户*

现在，您拥有一个具有使用 Oracle 机器学习所需权限的数据库用户。

如果您已有数据库用户，但该用户尚未添加到机器学习，请在 `Users` 页面（图 5-14）上启用 `Show All Users`。此用户的 `角色` 是 `None`。通过单击用户名从列表中选择用户。然后在 `Edit User` 页面（图 5-16）上编辑用户数据，并单击 `Save`。现在，数据库用户能够使用机器学习。此用户的角色现在在用户列表中为 `Developer`。

***图 5-16.** 为现有用户添加使用 Oracle 机器学习的权限*

![用户角色设置图片](img/index-161_1.jpg)

#### 使用数据库操作创建用户

创建用户或将用户添加到机器学习的另一种方法是使用数据库操作。在 `Autonomous Database Details` 页面（图 5-13）上，选择 `Database Actions`。使用 `Admin` 凭据登录。选择 `Database Users`。如果您还没有创建数据库用户，请单击 `Create User`。插入创建用户所需的数据（图 5-17）。

***图 5-17.** 在数据库操作中创建用户*

然后通过启用权限来定义所需的权限，如图 5-18 所示。您需要启用 `OML` 才能将用户添加到机器学习。如果需要，您可以启用其他权限。`Graph` 授予用户使用 Graph 服务的权限。这对于我们的示例不是强制性的，但如果您想基于图数据构建机器学习模型，则需要它。如果需要启用用户访问数据库操作，则需要启用 `Web Access`。启用 `Web Access` 会自动填写 `Web access advanced features` 部分中的信息。

![权限设置图片](img/index-162_1.jpg)

***图 5-18.** 为新用户定义权限*

### Oracle 机器学习

`OML4SQL` 是在 Oracle 数据库中实现的一组用于机器学习的 `PL/SQL` 包。`OML4SQL` 由三个 `PL/SQL` 包组成：

• `DBMS_PREDICTIVE_ANALYTICS`，包含使用预测分析执行半自动化机器学习的例程

• `DBMS_DATA_MINING_TRANSFORMING`，包含用于为 `OML4SQL` 算法转换数据的例程

• `DBMS_DATA_MINING`，包含例如创建和管理机器学习模型以及评估它们的例程

它们都有几个子程序。

用于 Python 的 Oracle 机器学习 (`OML4Py`) 使您能够运行 Python 命令，对存储在 Oracle 数据库中或可通过 Oracle 数据库访问的数据进行数据转换、统计、机器学习和图形分析，使用 Python API。`OML4Py` 包含在 Oracle 自治数据库中。它是一个 Python 模块，使用户能够使用 Python 语法操作数据库表和视图中的数据，而无需将数据移出数据库。`OML4Py` 函数和方法透明地将选定的 Python 函数集转换为 `SQL`，以便在数据库内执行。`OML4Py` 包含典型的 Python 机器学习库。但是，`OML4Py` 的特殊之处在于它还包括 Oracle 特定的库。`AutoML` 功能构建在 Python 之上。要在笔记本中使用 `AutoML` 功能，您需要导入库 `oml`，并从 `oml` 导入 `automl`。如果您想在本地数据库上使用 `OML4Py`，则必须在数据库中安装 Python、所需的 Python 库和 `OML4Py` 服务器组件，并且必须安装 `OML4Py` 客户端。

`OML4R` 是 Oracle 数据库内部的一组 R 库。`OML4R` 在云中尚不可用，但在本地可用。`OML4Spark` 是 `Oracle Big Data Connectors` 软件套件的一个组件，用于本地大数据解决方案，并包含在 `Oracle Big Data Service` 中。

### Oracle 机器学习用户界面简介

# 第 5 章 自治数据库中的 Oracle 机器学习

导航到自治数据库服务控制台的 `Development` 部分，如图 5-11 所示，然后选择 `Oracle Machine Learning User Interface`。以我们本章前面创建的机器学习用户身份登录（图 5-19）。

![](img/index-164_1.jpg)

**提示** 为了将来更容易找到登录页面，请将 URL 保存到您的收藏夹。

***图 5-19.** 登录 Oracle 机器学习*

Oracle 机器学习 (OML) 控制台打开，如图 5-20 所示。此控制台有一个 `How Do I?` 部分，其中包含大量关于如何开始使用 Oracle 机器学习 (OML) 的内容。在控制台的右上角，您可以看到用于登录 Oracle 机器学习的用户名。如果您选择下拉菜单，您将能够更改用户的 *Preferences* 和 *password*。其中一个首选项是，例如，您的时区。您还可以查看 *Help* 和 *About* 信息，以及从服务 *Sign Out*。在 Oracle 机器学习上工作时，您始终在一个由单个或多个项目组成的工作区内工作。在用户信息旁边，您可以找到关于 `Workspace` 和 `Project` 的信息。根据您的权限，您可以 `Manage Workspaces`（图 5-21）和 `Workspace Permissions`，创建 `New Project`、`Select Project` 或查看 `Recent Projects`。如果您选择管理工作区，您可以 `Edit`、`Delete` 或授予所选工作区 `Permissions`，如图 5-21 所示。您能够授予对您自己的工作区的权限。

![](img/index-165_1.jpg)
![](img/index-165_2.jpg)

***图 5-20.** Oracle 机器学习控制台*
***图 5-21.** 管理工作区*

在 `Quick Actions` 部分（图 5-20），您可以使用 `AutoML` 功能，管理和部署机器学习 `Models`，使用 `Scratchpad`，使用 `Notebooks` 和 `Jobs`，以及查看预构建的 `Examples`。

在左侧，您还可以看到 `Recent Notebooks` 和 `Recent Experiments` 的列表。Experiment 是 AutoML 过程的结果。

在屏幕底部，您还可以看到 `Recent Activities` 部分，其中显示了在 Oracle 机器学习中执行的操作日志。

![](img/index-166_1.jpg)

如果您单击左上角的导航菜单（图 5-22），您始终可以通过选择 `Home` 导航回主页面。在此导航菜单中，您还可以导航到 `Notebooks` 或 `AutoML` 体验；查看现有的 `Models`、`Templates`、`Jobs`、`Connection Groups` 和 `Notebook Sessions`。

***图 5-22.** Oracle 机器学习的导航菜单*

## 管理

有些任务只允许具有管理权限的用户执行。管理员用户创建、编辑和删除 Oracle 机器学习用户帐户，并重新分配用户工作区。

要管理机器学习服务，请使用 `Admin` 凭据登录。您将收到一条警告：
```
Administrator is restricted from creating notebooks and jobs, please create a new OML user or use an existing OML user for notebook and job creations。
```
这意味着管理用户只允许执行管理工作，不允许执行与机器学习过程本身相关的任何操作。Oracle 机器学习控制台确实显示 `How do I?` 和 `Quick Actions`，但如果您选择，例如，Notebooks，笔记本列表为空，并且您无法创建新笔记本。

如果您从右上角选择下拉菜单（图 5-23），您可以 `Select Project`、创建 `New Project` 或 `Manage Workspaces`。

![](img/index-167_1.jpg)

***图 5-23.** 以 Admin 用户身份登录时的 Oracle 机器学习控制台*

如果您选择右上角的 `ADMIN` 下拉菜单，您可以编辑用户 *Preferences*、`Manage OML Users`、查看 *Help* 和 *About*，以及 *Sign Out*。

您也可以选择左上角的导航菜单。该菜单（图 5-24）包括一个单独的管理任务部分。您可以 `Manage OML Users`、管理 `User Data` 或检查 `Compute Resources`。如果您选择 Manage OML Users，您将能够 `Create` 新用户、`Delete` 用户、通过选择 `Show All Users` 查看数据库中的所有用户，或从列表中 `Search` 用户。您还可以通过从列表中选择用户来编辑用户。

![](img/index-168_1.jpg)

***图 5-24.** Admin 用户的 Oracle 机器学习导航菜单*

通过选择 `User Data`，管理员用户可以删除用户和所有用户相关的对象，例如工作区、项目和笔记本。或者，管理员可以执行用户的工作区重新分配。

![](img/index-169_1.jpg)
![](img/index-169_2.jpg)

`计算资源` 指的是解释器连接的后端服务，例如数据库。您可以在图 5-25 中看到一个计算资源的示例。计算资源页面列出了每个资源、其类型、注释和最后更新详细信息。

***图 5-25.** 计算资源*

如果您从列表中选择一个数据库的计算资源，您可以查看资源详细信息（图 5-26）。数据是只读的，除了可以更改的内存设置。对于资源 `databasename_high`，内存设置必须在 8 到 16 之间，对于 `databasename_medium` 在 4 到 8 之间，对于资源 `databasename_low` 在 2 到 4 之间。内存设置仅适用于 Python 解释器。单击 `Back to Compute Resources` 返回列表并保存对内存预留的可能更改。您可以从 Oracle 文档中阅读更多关于 OCPU 数量和并发笔记本的限制信息。

***图 5-26.** Oracle 资源页面*
![](img/index-170_1.jpg)
![](img/index-170_2.jpg)

如果您从导航菜单中选择 `Connection Groups`，具有管理员角色的用户可以管理 Zeppelin 解释器集，即构成连接组的数据库连接集合（图 5-27）。`Global` 连接组在配置新数据库时自动创建。您可以 `Edit`、`Stop` 或 `Refresh` 一个连接组。

***图 5-27.** 连接组页面*

您可以从连接组中选择单个连接并进行编辑。例如，在图 5-28 中，您可以看到一个 Oracle 数据库连接的详细信息。

***图 5-28.** Oracle 数据库连接*

## 笔记本

Oracle 机器学习笔记本使用 Apache Zeppelin 笔记本，为协作式机器学习过程提供基于 Web 的界面。笔记本是执行和记录机器学习过程的界面：数据探索、数据可视化、数据准备、训练和测试机器学习模型、评估模型以及部署它们。

![](img/index-171_1.jpg)

如果您从 OML 控制台选择 `Examples`，您可以找到几个预定义笔记本的示例，如图 5-29 所示。

***图 5-29.** Oracle 机器学习上的示例模板*

您可以选择这些示例笔记本中的任何一个来研究它并了解

# 导入所需库

```python
import oml

from oml import ai
```

# 获取现有表的句柄

`ENROLL = oml.sync(table = "STUDENTTRAIN")`

# 从数据集中删除 ENROLLED 属性

`ENROLL_x = ENROLL.drop('ENROLLED')`

# 将 ENROLLED 定义为 AI 算法的目标属性

## 第 5 章 自治数据库中的 Oracle 机器学习

```
ENROLL_y = ENROLL ['ENROLLED']
```

# 创建属性重要性对象

`ai_obj = ai()`

# 拟合模型

```
ai_obj = ai_obj.fit(ENROLL_x, ENROLL_y)
```

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
