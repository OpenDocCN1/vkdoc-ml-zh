# 如果出现提示，输入 yes 并回车
$
代码清单 5-37
Ubuntu 安装 clang
```

如果你使用的是 Mac，它应该已预装 clang，或者你可以通过 Xcode 安装。^(²⁴)

接下来，我们讨论如何创建 snips 模型。由于 snips 模型是 Python 模型，我们可以创建一个虚拟环境并安装该包（代码清单 5-38）。

```
$ python3 -m venv venv
$ source venv/bin/activate
(venv) $ pip install snips-nlu
(venv) $
代码清单 5-38
创建 Python 虚拟环境
```

要下载模型，我们需要下载英文模型。截至撰写本文时，其他可用模型包括德语、西班牙语、法语、意大利语、日语、韩语和葡萄牙语（巴西方言和葡萄牙方言各一个）。^(²⁵) 实现不同语言涉及大量仓库的修改，过程并不简单，因此 snips 团队正在努力简化整个流程。^(²⁶) 如果下载英文模型在后续步骤中无法正常工作，我们也可以下载所有资源并重试（代码清单 5-39）。

```
(venv) $ python -m snips_nlu download en
