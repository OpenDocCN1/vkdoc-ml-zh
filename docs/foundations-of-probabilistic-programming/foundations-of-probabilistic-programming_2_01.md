## 前言

Python 语言历史悠久，用途广泛。其最初版本由 Guido van Rossum 于 1989 年开发，作为系统管理工具，用以替代 Bash 脚本和 C 程序。^(1) 自 1991 年公开发布以来，它已发展应用于众多行业，涵盖网页开发、电影、政府、科学和商业等领域。^(2)

我最初在电影行业接触 Python，当时我们用它在不同部门和地点之间自动化数据管理。在过去十年中，Python 已成为数据科学领域的主导工具。

这种主导地位源于两个发展：Jupyter notebook 和强大的第三方库。2001 年，Fernando Perez 启动了 IPython 项目，这是一个受 Maple 和 Mathematica notebook 启发的交互式 Python 环境。^(3) 到 2014 年，该项目中与 notebook 相关的部分被分离出来，成为 Jupyter 项目。这些 notebook 在科学和统计工作环境中表现出色。与此发展并行的是，用于科学和统计计算的第三方库也被开发出来。随着应用场景的增多，Python 程序员可用的功能也大幅增长。从打开网络套接字到处理自然语言文本，各种专用包应有尽有，其丰富程度远超初学者的需求。

这个项目是 Noah Gift 的创意。^(4) 在他作为教育工作者的工作中，他发现数据科学的学生缺乏一种资源，来学习他们真正需要的 Python 部分。市面上有很多通用的 Python 书籍和数据科学书籍，但缺少专门学习入门数据科学所需 Python 知识的资源。这正是我们在此尝试提供的。本书不会教授搭建网页或执行系统管理所需的 Python 知识。它也不是为了教你数据科学，而是为了教你学习数据科学所需的 Python 知识。

希望这本指南能成为你增长数据科学知识之路上的良伴。


### 示例代码

本书示例中展示的大部分代码都可以在 GitHub 上找到：

[`github.com/kbehrman/foundational-python-for-data-science`](https://github.com/kbehrman/foundational-python-for-data-science).

* * *

1   [`docs.python.org/3/faq/general.html#why-was-python-created-in-the-first-place`](https://docs.python.org/3/faq/general.html#why-was-python-created-in-the-first-place)

2   [`www.python.org/success-stories/`](https://www.python.org/success-stories/)

3   [`blog.fperez.org/2012/01/ipython-notebook-historical.html`](http://blog.fperez.org/2012/01/ipython-notebook-historical.html)

4   [`noahgift.com`](https://noahgift.com)

## 图片致谢

| **图片** | **版权归属** |
| 封面 | Boris Znaev/Shutterstock |
| 封面 | Mark.G/Shutterstock |
| 图 1-01 | Colab 对话框截图 © 2021 Google |
| 图 1-02 | 重命名笔记本截图 © 2021 Google |
| 图 1-03 | Google Drive 截图 © 2021 Google |
| 图 1-04 | 编辑文本单元格截图 © 2021 Google |
| 图 1-05 | 格式化文本截图 © 2021 Google |
| 图 1-06 | 列表截图 © 2021 Google |
| 图 1-07 | 标题截图 © 2021 Google |
| 图 1-08 | 目录截图 © 2021 Google |
| 图 1-09 | 隐藏单元格截图 © 2021 Google |
| 图 1-10 | LaTeX 示例截图 © 2021 Google |
| 图 1-11 | 文件截图 © 2021 Google |
| 图 1-12 | 上传文件截图 © 2021 Google |
| 图 1-13 | 挂载 Google Drive 截图 © 2021 Google |
| 图 1-14 | 代码片段截图 © 2021 Google |

## 注册您的图书

在 InformIT 网站上注册您购买的《Python 数据科学基础》副本，以便在更新和/或勘误发布时方便地获取。要开始注册流程，请访问 [informit.com/register](http://informit.com/register) 并登录或创建一个账户。输入产品 ISBN **9780136624356** 并点击提交。在“已注册产品”选项卡中，找到该产品旁边的“访问附加内容”链接，点击该链接即可访问任何可用的附加材料。如果您希望收到关于新版本和更新的独家优惠通知，请勾选接收我们邮件的复选框。

## 关于作者

**肯尼迪·贝尔曼**是一位资深软件工程师。他最初在视觉特效行业使用 Python 管理数字资产，此后便广泛使用该语言。他撰写了多本关于 Python 教育的书籍和培训课程。目前，他在 Envestnet 担任高级数据工程师。

## 第一部分

## 在笔记本环境中学习 Python

