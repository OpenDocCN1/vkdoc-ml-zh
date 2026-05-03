# 根据名称过滤数据
iberia = all_data[all_data['Name'] == 'Iberia']
iberia
代码块 8-3
根据名称过滤数据
```

生成的数据框仅包含对应于伊比利亚半岛的多边形。该数据框如图 8-11 所示。

![](img/526781_1_En_8_Fig11_HTML.jpg)

一张包含四列一行的表格图片。除第一列外，各列标签分别为 Name、Description 和 geometry。展示的行条目中，除 Description 列外，依次为 0、Iberia 和 POLYGON Z double open parentheses negative 8.99346 37.08769 0.00000 comma negative 8.3873 ellipsis。

图 8-11

来自代码块 8-3 的数据。作者供图

现在，让我们使用代码块 8-4 中的代码对葡萄牙执行相同操作。

```
