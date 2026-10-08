# 第 2 章 使用对象存储存储大对象

# 使用 Oracle 云功能扩展 Oracle Application Express

Oracle Application Express (APEX) 是一个出色的软件开发堆栈，允许开发人员专注于实现关键业务逻辑，并简单地使用预制组件来实现核心应用程序功能，例如身份验证和与数据库交互。就像 `Lego` `Technic` (`lego.com/themes/technic`) 产品系列（我是其忠实粉丝）一样，这些构建套件包含各种功能部件，例如齿轮、电机和/或气动泵。这些允许初出茅庐的工程师快速构建有用的车辆或机器。但是，有时可能会出现一个功能需求，但没有可用的标准模块来满足它，例如，创建一个水果分拣传送带系统，该系统需要一个摄像头和一个能够识别水果并将其引导到特定篮子的嵌入式微控制器。今天，我们可以很容易地找到像 `Espressif Systems` (`www.espressif.com/`) 开发的 `ESP32` 这样的嵌入式微控制器，它们集成了一个小型摄像头，并具有多个 GPIO（通用输入/输出）引脚，然后可以控制兼容 `Lego` 的电机。GPIO 引脚是在积木生态系统内集成外部系统的关键。

Oracle 云基础设施 (OCI) 提供了许多有趣的云原生服务，可以进一步扩展 APEX 应用程序，超越仅使用 PL/SQL 所能实现的功能。这些服务提供专门的功能，可按需通过网络使用，并且通常很少或不需要管理底层基础设施。此类服务的示例包括 `Functions`、`Event Service` 和 `API Management`。

OCI 对象存储是另一个有用的功能，可用于补充 APEX 应用程序，用于存储开发人员原本会作为 BLOB 列存储在 Oracle 数据库中的大文件。在本章中，我们将逐步介绍这些概念，并学习如何扩展使用 OCI 提供的这些组件的 APEX 应用程序，以解决更复杂的需求。

© Adrian Png 和 Heli Helskyaho 2022

A. Png 和 H. Helskyaho，《使用 Oracle 云功能扩展 Oracle Application Express》，

`doi.org/10.1007/978-1-4842-8170-3_2`

## Oracle 云基础设施

Oracle 云基础设施是 Oracle 的第二代云服务，它使用最先进的技术、强大的硬件平台和基于标准的服务提供基础设施即服务 (IaaS)。如果您尚未注册，请访问 URL `www.oracle.com/cloud/free/` 并注册一个帐户。它包括一个 30 天试用期和在此期间使用的标准 300 美元信用额度。

试用期结束后，用户可以选择升级到付费帐户，或继续使用 Oracle Cloud Free Tier 产品中包含的 Always Free 资源。在撰写本文时，Always Free 资源列表包括：

• 两个计算实例（虚拟机），每个具有有限的 OCPU 和内存。

• 两个块卷存储，总分配磁盘空间为 100 GB。请注意，每个计算实例需要至少 47 GB 用于其引导卷。

• 10 GB 的 OCI 对象存储和 10 GB 的 OCI 归档存储。

• 访问资源管理器以使用 `Terraform` (`https://terraform.io`) 进行部署。

• 两个自治数据库，有四种不同的工作负载类型可供选择。每个数据库配备 1 个 OCPU 和 20 GB 存储。

• `NoSQL` 数据库，包含 3 个表，每个表配备 25 GB 存储。

• 一个灵活的和一个第 4 层网络负载均衡器。

• 管理服务，包括有限的监控、通知和服务连接器。

在第 3 章和第 4 章中，我们将介绍 OCI 对象存储、函数和事件的使用。除了 OCI 对象存储之外，其余服务没有 Always Free 分配。您需要注册 30 天试用期或升级现有的 Oracle Cloud Free Tier 帐户。

## 在 Oracle 数据库中存储文件

在决定如何将文件与数据记录一起存储时，最常见的方法，也许是最先想到的，是将文件存储为 `BLOB` 或 `CLOB` 列。Oracle 数据库支持最大 8 到 128 TB 的大小，具体取决于数据库配置。在数据库记录中管理大对象 (LOB) 简单直接，更重要的是，它享有在数据库事务内操作的好处。

然而，随着时间的推移，表空间大小的增长可能变得难以管理，并且可能成本高昂，因为数据文件通常位于高性能且因此更昂贵的存储设备上。如果这些大对象的更改级别较低，则成本会逐渐超过将 LOB 存储在表中的好处。

有替代方法可以将文件存储在外部，然后在数据库记录中链接存储位置。例如，Oracle 数据库有一种称为 `BFILE` 的数据类型，它将实际文件存储在操作系统文件系统上，而仅在关联的数据库记录中存储对它的引用。这种方法的一个隐藏好处是将文件存储在更便宜、性能更低的磁盘上以节省成本。然而，挑战是显而易见的，那就是维护这些链接的完整性和一致性的能力。

## OCI 对象存储替代方案

在过去十年中，我们见证了云技术的许多进步。一项重要的发展是对象存储和基于云的大型存储系统。像 Dropbox、Microsoft OneDrive、AWS S3 等许多服务为消费者和开发人员提供了存储文档和备份的替代平台。OCI 也不例外。在本节中，我们将探讨使用 OCI 对象存储作为外部 LOB 系统所涉及的内容，以及如何将其与 APEX 应用程序集成。

OCI 对象存储是一种托管存储解决方案，按使用量收费，即每月以 GB 为单位的存储容量，以及每月以 10,000 为单位的请求数（`GET`、`PUT`、`POST` 和 `DELETE`），前 50,000 次请求免费。它还具有自动分层和生命周期管理策略，可用于在三个存储级别之间移动对象：标准、低频访问和归档。使用 Oracle Cloud 的成本估算器 (`www.oracle.com/cloud/cost-estimator.html`)，1,000 GB 每月分别花费 25、10 和 3 美元。相比之下，相同数量的块卷存储按当前费率计算将花费 26 美元以上。

该存储系统具有高度冗余。存储在系统中的对象跨容错域复制，对于某些具有多个可用性域的区域，则跨区域复制。存储在系统中的数据使用校验和主动监控完整性。该系统具有自我修复能力，无需任何用户干预即可解决检测到的任何数据损坏。

存储系统中的对象组织在桶中。对这些桶的访问可以通过 OCI 身份和访问管理 (IAM) 策略进行管理。每个桶还具有以下功能：

• **对象版本控制**: 如果启用，OCI 对象存储将在对象被更新或删除时保留其旧版本。

• **发出 OCI 事件**: 启用后，对象存储将在创建、更新或删除对象时发出事件。这些事件可用于触发其他 OCI 资源，例如执行 `Functions`。

• **自动分层**: 如果启用此功能，OCI 会监控数据的访问方式，并会自动将较大的对象（超过 1 MiB）从标准层移动到低频访问层。虽然将对象存储在后一层更便宜，但请注意，访问它们时需要付费。

出于安全考虑，桶创建时其可见性设置为私有，但如有必要可以更改为公共。此外，存储在 OCI 对象存储中的所有文件在静态时都经过加密，用户可以选择使用自己的加密密钥。

创建后，还需要注意其他 OCI 对象存储功能：

• **预认证请求**: 创建后，这些唯一的 URL 提供对桶或对象的访问，而无需对 HTTP 请求进行签名。所有请求都必须指定过期日期。

• **生命周期策略规则**: 此处定义的规则将允许 OCI 对象存储自动管理对象，例如，根据指定的天数以及（如有必要）对象名称过滤器，将它们移动到归档存储层。

• **复制策略**: 这些策略允许 OCI 对象存储服务自动将对象更改（创建、更新和删除）的副本复制到目标区域。

• **保留规则**: 这些规则指定对象必须在桶中保留多长时间。在策略指定的期间内，不允许进行更改、更新或删除。在必须为法规遵从性保留上传到桶的文件的情况下，这是一个非常有用的功能。

> **注意** 对于生命周期和复制策略，您需要在根区间中有一个策略，允许目标区域中的 OCI 对象存储服务对桶和对象进行更改。例如，当目标区域在美国凤凰城时，策略如下：
> ```
> Allow service objectstorage-us-phoenix-1 to manage object-family in compartment DEV
> ```

# 在 APEX 中使用 OCI 对象存储

在本节中，我们将逐步介绍通过 APEX 应用程序使用 OCI 对象存储存储数码照片所需的步骤和代码。

## 创建区间

OCI 资源使用区间进行分组。当租户刚刚配置时，它只包含两个分区，其中一个称为根分区。最佳实践是在根分区下创建一个新区间，用于创建新的 OCI 组件。有多种方法和动机来隔离您的资源。例如，它们可以根据它们关联的部门、环境类型（DEV、TEST、PROD 等）或计费的成本中心进行分组。

在本章中，我们将创建一个名为 `DEV` 的区间，为此，请从导航菜单中的 `Identity` 项下访问 `Compartments` 页面。单击 `Create Compartment` 按钮，然后提供名称和描述（图 2-1）。

![](img/index-53_1.jpg)

第 2 章 使用对象存储存储大对象

图 2-1. 创建 `DEV` 区间

另外，确保您已选择根区间作为父区间。单击 `Create Compartment` 按钮完成任务。

## 创建 OCI 对象存储桶

创建区间后，继续在对象存储中创建一个桶。转到 `Buckets` 页面。该链接可以在导航菜单中的 `Storage` 下找到，然后是 `Object Storage`。或者，在搜索框中输入术语“bucket”以快速找到该链接。

单击 `Create Bucket` 按钮并提供一个名称，例如“photos”，然后选择 `Standard` 默认存储层。您应该会看到如图 2-2 所示的 Create Bucket 模态对话框。

![](img/index-54_1.jpg)

第 2 章 使用对象存储存储大对象

图 2-2. 创建对象存储桶

## 创建 Oracle 自治数据库（可选）

APEX 包含在所有 Oracle 自治数据库 (`ADB`) 中，无论其工作负载类型如何。此步骤是可选的。使用对象存储 `OCI REST API` 与 APEX 的安装位置无关，因为它完全依赖于平台使用其 `APEX_WEB_SERVICE` 包处理 Web 服务的能力。

要创建 `ADB`，请单击 Oracle Database 导航菜单项下的链接 `Autonomous Transaction Processing` (`ATP`)。创建一个具有 `ATP` 工作负载类型的 Always Free `ADB`（图 2-3）。

![](img/index-55_1.jpg)

第 2 章 使用对象存储存储大对象

图 2-3. 可选地，创建一个具有 `ATP` 工作负载类型的 Always Free `ADB`

## 创建身份和访问管理资源

OCI 用户可以做什么或不能做什么由 OCI 身份和访问管理 (`IAM`) 决定。无论操作是通过 OCI 控制台还是 REST API 执行，都是如此。访问权限使用 OCI 策略进行管理，权限分配给用户组、实例或资源。在本节中，我们将逐步介绍允许开发人员创建、部署和管理 Functions 应用程序所需的 IAM 更改。

### 组

组是用户的集合，然后在策略语句中用于定义访问权限和特权。您可以通过导航菜单中的 `Identity` 项找到 `Groups` 页面的链接。在那里，您可以查看现有组的列表并创建新组。

创建一个名为 `PhotosBucketManagers` 的新组。

![](img/index-56_1.jpg)

第 2 章 使用对象存储存储大对象

### 策略

策略是语句的集合，用于确定用户组或 OCI 资源可以访问哪些内容以及他们可以执行的操作类型。您可以从导航菜单中的 `Identity` 菜单项访问租户的策略。创建一个策略 `PhotosBucketAccessPolicy`（图 2-4），其中包含以下策略语句：

```
allow group PhotosBucketManagers to read buckets in compartment DEV
allow group PhotosBucketManagers to manage objects in compartment DEV where
any { target.bucket.name='photos' }
```

图 2-4. 创建策略以管理对特定桶的访问

这些策略语句将允许 `PhotosBucketManager` 组的成员能够

1. 读取区间 `DEV` 中的所有桶
2. 管理在区间 `DEV` 中创建的 `photos` 桶中的对象

![](img/index-57_1.jpg)

![](img/index-57_2.jpg)

第 2 章 使用对象存储存储大对象

### IAM 用户

使用 OCI 控制台为我们的开发人员创建一个 IAM 用户。访问 `Users` 管理页面。您可以在 OCI 侧边导航栏中的 `Identity` 项下找到它。创建用户 `apexagent01`（图 2-5）。

图 2-5. 创建用户

创建用户后，返回用户的详细信息页面，然后单击按钮 `Edit User Capabilities`（图 2-6）。

图 2-6. 管理用户在 OCI 上可以执行的操作

![](img/index-58_1.jpg)

第 2 章 使用对象存储存储大对象

限制用户使用 API 密钥与 OCI 资源交互的能力。这些密钥在直接使用 OCI REST API、通过 OCI 软件开发工具包 (`SDK`) 或 OCI 命令行界面 (`CLI`) 时使用。

另外，在用户的详细信息页面上，将用户添加到组 `PhotosBucketManager`（图 2-7）。

图 2-7. 将用户添加到 `PhotosBucketManager` 组

### API 密钥

如前所述，要使用 OCI REST API，需要一对 API 密钥。有三种方法可以生成密钥对。要么使用 OCI `CLI`、`OpenSSL`，或者直接通过 OCI 控制台生成它们。在用户的详细信息页面上，单击左侧列表中的 `API Keys` 链接，然后单击按钮 `Add API Key` 启动工具（图 2-8）。

![](img/index-59_1.jpg)

第 2 章 使用对象存储存储大对象

图 2-8. 通过 OCI 控制台生成 API 密钥对

在模态框上，选择 (A) `Generate API Key Pair`，单击图中标记为 (B) 的按钮下载私钥，然后单击 `Add` 按钮完成该过程。

### 收集 APEX Web 凭证的信息

要在 APEX 中使用 OCI REST API，需要 Web 凭证进行身份验证。在离开本节之前，请记下创建它所需的以下信息。

在用户的详细信息页面上，单击链接 `View Configuration file` 以显示信息（图 2-9），该信息提供了您在其他情况下将包含在配置文件中以与 OCI `CLI` 或 `SDK` 一起使用的内容。

![](img/index-60_1.jpg)

第 2 章 使用对象存储存储大对象

图 2-9. OCI `CLI`/`SDK` 配置文件的内容

记下以下内容：

- 用户 OCID
- 指纹
- 租户 OCID
- 私钥（见下文）

下载的私钥文件是 `PEM` 格式的文本文件，内容如下所示：

```
-----BEGIN PRIVATE KEY-----
MIIEvQIBADANBgkqhkiG9w0BAQEFAASCBKcwggSjAgEAAoIBAQCyX+KzKWkzrzxm
1jE/izBdIDbpcYtxHYefRRLhGabsOP0FCmcuClgWnKKk1nZb955q94LFrAidPEEP
dTjI1Tsx4ax3pcmxV7AirHYDRdccD5ymEUwE729xq/YQtALzc8NAC3AsKZnGIjyQ
...
Qsj8FQdGKhB3RvGLCUowI24ZunOmnjDuKLCQ46OoRkn0xEmWlbomIQsnqcllAyfU
ff2cEC7TLdtTS7SImQ0vLtw=
-----END PRIVATE KEY-----
```

分别删除头部和尾部 `-----BEGIN PRIVATE KEY-----` 和 `-----END PRIVATE KEY-----`，以及所有换行符。结果是一个异常长的字符串。不要保存文件，而是将内容复制到安全的地方。这是必须在 Web 凭证中输入的私钥。

第 2 章 使用对象存储存储大对象

## APEX 应用程序

为了演示 APEX 与 OCI 对象存储无缝协作的能力，我们将创建一个名为 `Object Storage Demo` 的 APEX 应用程序。在本章后面，我们将简单地扩展它以包括使用自动生成的缩略图图像。

以下是本节中将执行的任务的概述：

1. 创建一个数据库表，用于管理数码照片的元数据。
2. 创建 OCI 的 Web 凭证。
3. 通过检索与对象存储交互所需的租户命名空间来测试 Web 凭证。
4. 创建一个 REST 数据源以列出 `photos` 桶中的对象。
5. 创建一个报表和页面，用于列出、创建、更新和删除数码照片记录。

### 数码照片表

# 第 2 章 使用对象存储存储大对象

## Object Storage Demo

Object Storage Demo 应用程序的上下文是存储珍贵的数码照片，其中图像的元数据存储在数据库中，而图像文件存储在 OCI 对象存储中，以利用相对更便宜、高度冗余且托管的存储系统。

使用以下 SQL 语句创建照片表：

```sql
create table photo (
    photo_id number generated always as identity
    , title varchar2(500) not null
    , description varchar2(2000)
    , object_name varchar2(1024)
    , filename varchar2(500)
    , mime_type varchar2(100)
    , created_by varchar2(30)
    , created_on timestamp with local time zone
    , updated_by varchar2(30)
    , updated_on timestamp with local time zone
    , constraint photo_pk primary key (photo_id)
    , constraint photo_uk1 unique (object_name)
)
/
```

使用新创建的表创建一个包含表单和报表的 APEX 应用程序。创建应用程序后，转到第 3 页并进行以下更改：

• 将页面重命名为 *Photo Details*。

• 添加一个新的页面项 `P3_PHOTO`：
• 将页面项类型设置为 *Display Image*。
• 将外观模板设置为 *Optional – Above*。
• 添加自定义属性：`style="max-width: 100%; height: auto;"`
• 将页面项设置为基于 *Image URL stored in Page Item Value* 呈现。
• 将源类型设置为 `Expression`，语言设置为 `PL/SQL`，代码如下：

```sql
apex_page.get_url(
    p_application => :APP_ID
    , p_page => 0
    , p_request => 'APPLICATION_PROCESS=GET_OBJECT'
    , p_items => 'APP_PHOTO_ID,APP_BUCKET_NAME'
                 || ',APP_CONTENT_DISPOSITION'
    , p_values => to_char(:P3_PHOTO_ID) || ','
                  || :G_PHOTOS_BUCKET_NAME || ',inline'
    , p_plain_url => true
)
```

• 设置页面项，使其在页面项 `P3_PHOTO_ID` 不为空时呈现。

• 添加一个新的页面项 `P3_FILE`：
• 将页面项类型设置为 *File Browse*。
• 如果 APEX 版本支持，则显示为 *Block Dropzone*。
• 使用 `APEX_APPLICATION_TEMP_FILES` 并在请求结束时清除文件。
• 将文件类型限制为 `image/*`。
• 将以下项设置为页面项类型 *Display Only*，并且仅在页面项 `P3_PHOTO_ID` 不为空时呈现。此外，如果这是一个 *Form Region*，则将 *Query Only* 切换为启用。

• 将 *Process form Photo* 页面处理过程的类型更改为 *Execute Code*，目前，输入以下代码：

```sql
declare
    l_photo photo.%rowtype;
begin
    if :REQUEST = 'CREATE' then
        null; -- 在此处执行 CREATE 操作
    elsif :REQUEST = 'SAVE' then
        null; -- 在此处执行 SAVE 操作
    elsif :REQUEST = 'DELETE' then
        null; -- 在此处执行 DELETE 操作
    else
        null; -- 不执行任何操作
    end if;
end;
```

• 保存页面。

![](img/index-64_1.jpg)

最终页面应类似于图 2-10。

***图 2-10.** 更新后的 APEX 应用程序第 3 页*

## OCI 的 Web 凭证

使用 OCI REST API 时，所有 HTTP 请求都必须由具有所需 API 密钥的 IAM 用户正确签名。使用创建 IAM 用户时收集的信息，创建一个新的 Web 凭证（图 2-11），其身份验证类型为 *Oracle Cloud Infrastructure (OCI)*。使用之前获得的值填写以下字段：

• OCI 用户 ID
• OCI 私钥
• OCI 租户 ID
• OCI 公钥指纹

![](img/index-65_1.jpg)

***图 2-11.** 创建 OCI 的 Web 凭证*

完成后记下 *Static Identifier*。在调用 OCI REST API 时使用 `APEX_WEB_SERVICE.MAKE_REST_REQUEST` 函数时需要它。

## 检索租户命名空间

使用对象存储 REST API 时，URL 格式如下：

```
https://objectstorage.<REGION>.oraclecloud.com/n/<NAMESPACE>/b/<BUCKET_NAME>/o/<OBJECT_NAME>
```

占位符 `<REGION>` 的适当值可以从 OCI 文档中使用此链接 [`bit.ly/oci-docs-regions`](https://bit.ly/oci-docs-regions) 获得，而 `<BUCKET_NAME>` 和 `<OBJECT_NAME>` 的值是显而易见的。对于 `<NAMESPACE>`，即租户的命名空间，其值可以从 OCI 控制台获得；但是，我们也可以使用以下 PL/SQL 过程获得它：

```sql
declare
    c_base_url constant varchar2(59) :=
        'https://objectstorage.us-phoenix-1.oraclecloud.com';
    c_endpoint_path constant varchar2(40) := '/n/';
    c_web_credential constant varchar2(30) := 'PAB_APEXAGENT_1';
    l_response clob;
begin
    l_response := apex_web_service.make_rest_request(
        p_url => c_base_url || c_endpoint_path
        , p_http_method => 'GET'
        , p_credential_static_id => c_web_credential
    );
    dbms_output.put_line(l_response);
end;
```

您可以使用 APEX 的 SQL Workshop 执行代码（图 2-12）。

***图 2-12.** 使用 SQL Workshop 检索租户的命名空间*

如果 OCI 资源和 Web 凭证创建正确，则结果中应显示租户的命名空间。

![](img/index-67_1.jpg)

## 应用程序属性 – 替换

创建并设置以下 *Substitution Strings*（图 2-13）的适当值：

• **`G_PHOTOS_BUCKET_NAME`**: 存储上传文件的对象存储桶的名称。
• **`G_THUMBNAILS_BUCKET_NAME`**: 存储生成的缩略图的桶的名称。这将在本章后面讨论。
• **`G_OCI_WEB_CREDENTIAL_ID`**: 用于访问 OCI REST API 的 Web 凭证的静态标识符。
• **`G_BASE_URL`**: 用于访问 OCI REST API 的基本 URL，基于 OCI 区域和租户命名空间构建。

***图 2-13.** 添加替换字符串*

## 与对象存储交互的完整代码

为了方便使用对象存储的 OCI REST API，我们将创建一个名为 `pkg_oci_os_util` 的包，其中包含以下函数和过程：

• `f_generate_object_name`
• `p_upload_object`
• `p_delete_object`
• `p_get_object`

完整的源代码可以从代码仓库的 `ch02/demoapp/packages/` 目录中获取。

# 对象存储操作概述

重要的是要认识到对象存储桶结构本质上是扁平的，即使可以通过使用"/"字符分隔"文件夹"和"文件"来创建伪文件夹。但是，在演示应用程序中，我们选择依赖函数`pkg_oci_os_util.f_generate_object_name`来基于照片表的主键和文件上传到服务器时的名称生成唯一的文件名。这将避免如果上传文件的文件名冲突而导致文件可能被覆盖的情况。

在创建或更新带有新文件的照片时，这两个过程都依赖于包`pkg_oci_os_util.p_upload_object`。它们依赖于`apex_web_service.make_rest_request`函数来发出 PUT HTTP 请求，并将上传文件的内容嵌入到请求体中。以下代码片段突出了关键步骤：

1. 使用基本 API 地址、目标桶名称和生成的 URL 编码形式的对象名称构建请求 URL。
2. 将上传文件的 MIME 类型写入 HTTP 请求头。
3. 执行 REST 调用并检索响应。
4. 检查 HTTP 响应状态码是否为 200 (OK)，如果不是，则引发错误。

```
l_request_url := p_base_url || '/b/' || p_bucket_name
    || '/o/' || apex_util.url_encode(p_object_name);

apex_web_service.g_request_headers(1).name := 'Content-Type';
apex_web_service.g_request_headers(1).value := p_mime_type;

l_response := apex_web_service.make_rest_request(
    p_url => l_request_url
    , p_http_method => 'PUT'
    , p_body_blob => p_blob_content
    , p_credential_static_id => p_oci_web_credential_id
);

if apex_web_service.g_status_code != 200 then
    raise_application_error(
        , l_response || chr(10) || 'status_code='
        || to_char(apex_web_service.g_status_code)
    );
end if;
```

# 第 2 章 使用对象存储存储大对象

删除对象稍微简单一些。在过程`pkg_oci_os_util.p_delete_object`中，在生成请求 URL 后，提交一个 DELETE HTTP 请求，无需额外的 HTTP 头或内容：

```
l_response := apex_web_service.make_rest_request(
    p_url => l_request_url
    , p_http_method => 'DELETE'
    , p_credential_static_id => p_oci_web_credential_id
);

if apex_web_service.g_status_code != 204 then
    raise_application_error(
        , l_response || chr(10) || 'status_code='
        || to_char(apex_web_service.g_status_code)
    );
end if;
```

注意 预期的 HTTP 响应状态码是`204`。

最后，过程`pkg_oci_os_util.p_get_object`从桶中检索一个对象，返回文件、内容类型和长度：

```
l_response := apex_web_service.make_rest_request_b(
    p_url => l_request_url
    , p_http_method => 'GET'
    , p_credential_static_id => p_oci_web_credential_id
);

if apex_web_service.g_status_code != 200 then
    raise_application_error(
        , 'status_code=' || to_char(apex_web_service.g_status_code)
    );
else
    for i in 1..apex_web_service.g_headers.count
    loop
        apex_debug.info(apex_web_service.g_headers(i).name || '='
            || apex_web_service.g_headers(i).value );
        if lower(apex_web_service.g_headers(i).name) =
            'content-length'
        then
            p_content_length := to_number(
                apex_web_service.g_headers(i).value);
        end if;
        if lower(apex_web_service.g_headers(i).name) =
            'content-type'
        then
            p_content_type := apex_web_service.g_headers(i).value;
        end if;
    end loop;
    p_blob_content := l_response;
```

与前两个过程不同，此过程使用过程`apex_web_service.make_rest_request_b`来发出 GET HTTP 请求。如果成功，响应的内容将作为 BLOB 对象返回。根据 OCI REST API 文档版本 20160918 ([`docs.oracle.com/en-us/iaas/api/#/en/objectstorage/20160918/`](https://docs.oracle.com/en-us/iaas/api/#/en/objectstorage/20160918/))，特别是`GetObject`操作，我们可以从 HTTP 响应头中提取内容类型和长度。

要访问对象存储中的对象，我们可以选择 (1) 使用数据库代理请求，(2) 生成预认证请求，或 (3) 使桶公开。最后一个选项通常不推荐。预认证请求生成一个有时限的唯一 URL，客户端可以使用它来访问桶及其所有内容或特定对象，而无需执行必要的 HTTP 请求签名。这是一种可接受的做法，当数据安全很重要但不关键时。

对于此应用程序，我们将使用第一种方法，以便对对象的检索和呈现方式进行细粒度控制。

首先创建三个 APEX 应用程序项：

• `APP_PHOTO_ID`: 这是一个受会话状态保护的变量，用于检索照片记录。
• `APP_BUCKET_NAME`: 这是一个受会话状态保护的变量，用于指定将从中检索对象的桶。
• `APP_CONTENT_DISPOSITION`: 此变量允许客户端代码指定一个提示，告诉浏览器是呈现文件还是下载文件。可接受的值分别是 `inline` 和 `attachment`。

接下来，创建一个名为`GET_OBJECT`的 APEX 应用程序过程。将其设置为作为`AJAX`回调执行。该过程代码的完整源代码可以在文件`ch02/demoapp/snippets/02-app-process-get_object_from_photos.sql`中找到。

应用程序过程代码利用`pkg_oci_os_util.p_get_object`从对象存储中检索由记录标识符指定的文件，并呈现允许使用文件原始文件名下载的 HTTP 响应。为了提高性能并降低成本（用于检索），它还允许浏览器缓存。第 3 页上的页面项`P3_PHOTO`将使用过程`apex_page.get_url`安全生成的 URL 调用此应用程序过程。

同样在第 3 页上，完成处理页面提交的页面处理过程`Process from Photo`。

对于`CREATE`页面请求，将以下代码行：

```
null; -- 在此处执行 CREATE 操作
```

替换为

```
for file in (
    select * from apex_application_temp_files
    where name = :P3_FILE
) loop
    insert into photo(
        title
        , description
        , filename
        , mime_type
    ) values (
        :P3_TITLE
        , :P3_DESCRIPTION
        , file.filename
        , file.mime_type
    ) returning photo_id into l_photo.photo_id;
```

# 第 2 章 使用对象存储存储大对象

对于 *SAVE* 页面请求，将以下代码行：

```sql
null; -- 在此处执行 SAVE 操作
```

替换为

```sql
select * into l_photo
from photo where photo_id = :P3_PHOTO_ID;

update photo
set
  title = :P3_TITLE
, description = :P3_DESCRIPTION
where photo_id = l_photo.photo_id;

for file in (
  select * from apex_application_temp_files
  where name = :P3_FILE
) loop
  l_photo.object_name := pkg_oci_os_util.f_generate_object_name(
    p_photo_id => l_photo.photo_id
  , p_filename => file.filename);

  update photo
  set filename = file.filename
  , mime_type = file.mime_type
  , object_name = l_photo.object_name
  where photo_id = l_photo.photo_id;

  -- 如果文件名与记录中的不匹配，则先删除它。
  if l_photo.filename <> file.filename then
    pkg_oci_os_util.p_delete_object(
      p_base_url => :G_BASE_URL
    , p_bucket_name => :G_PHOTOS_BUCKET_NAME
    , p_object_name => pkg_oci_os_util.f_generate_object_name(
        p_photo_id => l_photo.photo_id
      , p_filename => l_photo.filename)
    , p_oci_web_credential_id => :G_OCI_WEB_CREDENTIAL_ID
    );
  end if;

  pkg_oci_os_util.p_upload_object(
    p_base_url => :G_BASE_URL
  , p_bucket_name => :G_PHOTOS_BUCKET_NAME
  , p_object_name => l_photo.object_name
  , p_blob_content => file.blob_content
  , p_mime_type => file.mime_type
  , p_oci_web_credential_id => :G_OCI_WEB_CREDENTIAL_ID
  );
end loop;
```

最后，对于 *DELETE* 页面请求，将以下代码行：

```sql
null; -- 在此处执行 DELETE 操作
```

替换为

```sql
select * into l_photo
from photo where photo_id = :P3_PHOTO_ID;

delete from photo where photo_id = l_photo.photo_id;

pkg_oci_os_util.p_delete_object(
  p_base_url => :G_BASE_URL
, p_bucket_name => :G_PHOTOS_BUCKET_NAME
, p_object_name => l_photo.object_name
, p_oci_web_credential_id => :G_OCI_WEB_CREDENTIAL_ID
);
```

![](img/index-75_1.jpg)

此屏幕截图（图 2-14）显示了完成后的应用程序外观。

**图 2-14.** 完成的 APEX 应用程序

## 注意事项

与任何将文件存储在数据库外部的方法一样，开发人员必须管理数据完整性和一致性。在传统方法中，文件作为表列存储，我们可以利用数据库事务，以便仅在数据库记录成功写入时才保存文件。当使用外部存储系统时，设计 PL/SQL 过程，以便在文件操作发生错误时发生数据库事务回滚。

决定如何在外部存储系统中命名文件并将其链接到关联的数据库记录也很重要。将 LOB 存储在列中时，命名冲突的风险很小或没有。使用外部存储系统时，存在意外覆盖或删除与其他数据库记录关联的文件的风险。

## 总结

在本章中，我们演示了如何将对象存储用作照片、文档等大文件的替代存储介质。为了管理对象，我们使用了 `OCI` REST API，这些 API 使用非对称加密和数字签名进行保护。幸运的是，`APEX` 封装了使用 `OCI` Web 凭证执行身份验证过程的复杂性，使得使用 `OCI` 资源来构建或补充我们原本难以在纯 `APEX` 和 PL/SQL 中实现的功能变得容易。
