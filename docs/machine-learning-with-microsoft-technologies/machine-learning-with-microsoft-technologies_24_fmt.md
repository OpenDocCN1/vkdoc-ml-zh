# 19. Bot Framework

机器人是一种能够以对话方式与用户交互的应用程序 [1]。它可以是一个非常简单的、支持对话和基本问题的应用程序，也可以是能够理解语言的复杂应用程序。在 Microsoft Azure 中，可以使用 C# 或 Node.js 创建机器人。您可以使用 .NET SDK [2] 创建机器人，并通过模拟器 [3] 等工具进行测试。此外，一些机器人组件可帮助您添加更多功能 [4]。本章将介绍一个使用 Azure 组件的非常简单的机器人。首先，将展示如何在 Azure 中创建机器人服务，然后介绍如何创建一个用于问答的简单机器人，以及一个更复杂的机器人。

## 在 Azure 中创建机器人

登录您的 Azure 帐户并创建一个新服务名称：Web 应用机器人（图 19-1）。

创建新的机器人服务时，您必须提供一些信息，例如机器人名称、订阅、资源组、位置等。您需要在开始时选择的主要字段之一是机器人模板。默认选项是基本 (C#)。在我们的场景中，我们将把默认选项更改为“表单”，这有助于我们创建一个引导最终用户的机器人应用程序。

![../images/463840_1_En_19_Chapter/463840_1_En_19_Fig1_HTML.jpg](img/463840_1_En_19_Fig1_HTML.jpg)

图 19-1

Azure 中的 Web 应用机器人服务

如图 19-2 所示，应选择“表单”选项。

![../images/463840_1_En_19_Chapter/463840_1_En_19_Fig2_HTML.jpg](img/463840_1_En_19_Fig2_HTML.jpg)

图 19-2

用于问答的 Bot Framework

完成机器人应用程序后，生成的服务包含三个主要类别：机器人管理、应用服务设置以及支持和故障排除。首先，检查所创建机器人的输出。转到“机器人管理” ➤ “在网络聊天中测试”。

要查看 Bot Framework 的默认结果，请单击“在网络聊天中测试”并输入“Hi”。结果将显示为“欢迎使用简单的三明治点单机器人！”，然后列出三明治选项（图 19-3）。

![../images/463840_1_En_19_Chapter/463840_1_En_19_Fig3_HTML.jpg](img/463840_1_En_19_Fig3_HTML.jpg)

图 19-3

Bot Framework 测试

在“机器人管理”部分下，第一个选项是“生成”，它提供了创建机器人框架的所有源代码。第一个选项是“打开在线代码编辑器”。使用此选项，开发人员可以在在线代码编辑器中查看代码。

如图 19-4 所示，在“在线代码编辑器”下，显示了机器人背后的代码。

![../images/463840_1_En_19_Chapter/463840_1_En_19_Fig4_HTML.jpg](img/463840_1_En_19_Fig4_HTML.jpg)

图 19-4

Bot Framework 的“生成”部分

要查看代码，请单击“打开在线代码编辑器”。将显示 Web 应用机器人框架的代码（图 19-5）。如图 19-5 所示，在“模型”文件夹下的 `Sandwiches` 文件中，显示了用户可能询问的对话框列表。

此外，在图 19-5 中，每个问题和可能性都按照`枚举`数据结构表示。每个枚举定义了在与最终用户的对话中可能出现的不同问题。您可以在线更改代码中的问题和产品类型。例如，您可以将三明治选项更改为鸡肉、火腿、羊肉、猪肉或素食。

![../images/463840_1_En_19_Chapter/463840_1_En_19_Fig5_HTML.jpg](img/463840_1_En_19_Fig5_HTML.jpg)

图 19-5

对话框机器人框架的在线编辑器

对文件的更改将被保存。您必须在 .NET 应用程序中构建和编译它们。要构建文件，请单击“打开控制台”并输入`build.cmd`（图 19-6）。

![../images/463840_1_En_19_Chapter/463840_1_En_19_Fig6_HTML.jpg](img/463840_1_En_19_Fig6_HTML.jpg)

图 19-6

构建机器人在线编辑器

现在，我们必须在“在网络聊天中测试”中测试并检查更改。在构建框架后，首次输入“Hi”。如图 19-7 所示，关于三明治类型的第一个选项已更改。

![../images/463840_1_En_19_Chapter/463840_1_En_19_Fig7_HTML.jpg](img/463840_1_En_19_Fig7_HTML.jpg)

图 19-7

机器人框架更改的结果

现在，当你选择“火腿”选项时，下一个三明治选项将会显示，这是一个关于选择三明治长度的问题。可以将代码嵌入到频道中，例如 Facebook、Telegram、Skype 等（图 19-8）。

![../images/463840_1_En_19_Chapter/463840_1_En_19_Fig8_HTML.jpg](img/463840_1_En_19_Fig8_HTML.jpg)

图 19-8

聊天机器人中的频道

点击“频道”，注意在不同频道中嵌入机器人的不同方式。其中一种方式是在 Web 应用程序中嵌入机器人，即使用“获取机器人嵌入代码”。如果你想将代码嵌入到 HTML 页面中，必须复制嵌入代码，并用上方窗口中显示的密钥替换掉秘密代码（图 19-9）。

![../images/463840_1_En_19_Chapter/463840_1_En_19_Fig9_HTML.jpg](img/463840_1_En_19_Fig9_HTML.jpg)

图 19-9

频道中的嵌入代码

现在你有了一个使用密钥的代码，如下所示。

```
Now you can embed the code in any web site. In the following section
I will show how to create a question-and-answer chatbot and embed it in
another web site.
```

# 问答聊天机器人

## 创建 Web 应用机器人服务

问答（QnA）聊天机器人可用于任何网站，以引导用户。创建聊天机器人的步骤与上一节相同。首先，我们创建一个 Web 应用机器人服务，并选择“问答机器人”模板（图 19-10）。

![用于 QnA 聊天机器人的 Web 应用机器人](img/463840_1_En_19_Fig10_HTML.jpg)

## 测试与初始配置

创建 QnA 聊天机器人后，如图 19-10 所示，我们必须在“Web 聊天中测试”中进行测试。测试聊天机器人时，你会收到一条错误消息（图 19-11），要求你设置一些参数，例如 `QnAKnowledgebaseId`、`QnAAuthKey` 和 `QnAEndpointHostName`。

![测试 Web QnA 聊天机器人](img/463840_1_En_19_Fig11_HTML.jpg)

## 设置 QnA Maker 服务

要设置这些参数，我们必须在 Azure 中创建一个 QnA Maker 服务。在你的 Azure 账户中，设置 QnA Maker 服务（图 19-12）。

![QnA Maker](img/463840_1_En_19_Fig12_HTML.jpg)

在 Azure 中创建 QnA Maker 后，你必须设置一个知识库 [5]。使用你的 Azure 账户，登录到 `qnmaker.ai` 网站（图 19-13）。

![QnA Maker 网站](img/463840_1_En_19_Fig13_HTML.jpg)

点击页面顶部的“创建知识库”（图 19-13）。第一步是创建一个 QnA 服务（与上一节相同）。第二步是将知识服务连接到 Azure 中的 QnA 组件。第三步是选择相关的 Azure 目录 ID、Azure 订阅名称和 Azure QnA 服务。然后，你必须为知识库选择一个名称（图 19-14）。

![将知识库（KB）与 Azure 连接](img/463840_1_En_19_Fig14_HTML.jpg)

## 上传知识库并创建模型

接下来，我们必须以 URL 或文件格式（`.et`、`.pdf`、`.doc`、`.docx`、`.xlsx`）上传一些知识库。该文件包含一些常见问题解答（FAQ）。我创建了一个包含某会议常见问题解答的 Word 文件（图 19-15）。

![常见问题解答](img/463840_1_En_19_Fig15_HTML.jpg)

现在，你必须将文件上传到网站并创建知识库（图 19-16）。

![创建知识库](img/463840_1_En_19_Fig16_HTML.jpg)

## 扩展与测试知识库

创建知识库后，你可以通过点击“添加 QnA 对”来扩展知识库问题（图 19-17）。此外，还可以添加更多的问题和答案。

![添加 QnA 对](img/463840_1_En_19_Fig17_HTML.jpg)

上传并添加问题后，你必须“保存并训练”知识库。训练模型后，点击“测试”。输入“Conference”，知识库将返回结果（图 19-18）。

![测试知识库](img/463840_1_En_19_Fig18_HTML.jpg)

## 发布知识库并配置应用

测试知识库后，我们必须将其发布。发布知识库后，将构建一个 HTTP 请求。创建的请求包含所有必需的信息，例如知识库 ID、终结点链接和身份验证密钥（图 19-19）。

![创建 HTTP 请求](img/463840_1_En_19_Fig19_HTML.jpg)

这些参数可以在图 19-11 中查看。在 `webappqnabook`（图 19-20）中，点击“应用程序设置”，并使用 HTTP 请求参数填充这些参数（图 19-20）。

![应用程序设置参数](img/463840_1_En_19_Fig20_HTML.jpg)

## 最终测试

现在，我们必须测试聊天机器人。在 Web Chat 中点击“测试”，然后输入“Conference”。输出将显示在测试窗口中（图 19-21）。

![测试聊天机器人](img/463840_1_En_19_Fig21_HTML.jpg)

## 嵌入聊天机器人到网页

你可以将问题和答案嵌入到任何页面和渠道中。在此示例中，该聊天机器人已嵌入到会议网站中，以引导用户。要将聊天机器人嵌入网站，我们必须获取嵌入代码。要获取嵌入代码，请遵循与图 19-9 相关的相同步骤。

接下来，复制代码并将其放入网页中（图 19-22）。

![在网页中复制嵌入代码](img/463840_1_En_19_Fig22_HTML.jpg)

测试代码，你将看到网页上的聊天机器人（图 19-23）。

![嵌入网页的聊天机器人](img/463840_1_En_19_Fig23_HTML.jpg)

## 总结
本章解释了创建聊天机器人的一般步骤。

## 参考文献

1.  Kamran Iqbal 等，“关于 Azure 机器人服务”，Microsoft Azure，[`https://docs.microsoft.com/en-us/azure/bot-service/bot-service-overview-introduction?view=azure-bot-service-3.0`](https://docs.microsoft.com/en-us/azure/bot-service/bot-service-overview-introduction%253Fview%253Dazure-bot-service-3.0)，2019 年 4 月 17 日。
2.  Bot Builder，[`https://marketplace.visualstudio.com/items?itemName=SkillBotBuilder.SkillBotBuilder`](https://marketplace.visualstudio.com/items%253FitemName%253DSkillBotBuilder.SkillBotBuilder)。
3.  Tony Anziano，“Bot Framework-Emulator”，GitHub，[`https://github.com/Microsoft/BotFramework-Emulator/releases`](https://github.com/Microsoft/BotFramework-Emulator/releases)，2019 年。
4.  Microsoft Visual Studio，下载，[`https://visualstudio.microsoft.com/downloads/`](https://visualstudio.microsoft.com/downloads/)，2019 年。
5.  Microsoft QnA Maker，[`www.qnamaker.ai/`](http://www.qnamaker.ai/)，2019 年。

```

```