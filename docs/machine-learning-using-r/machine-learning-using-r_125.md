# 将文件放入 HDFS
hdfs.put("/home/sample.txt","/hadoop_practice")
[1] TRUE
```

然后定义 Map 和 Reduce 函数。这段代码片段定义了 Map 和 Reduce 函数如何扫描文本文件，并将其*分词*（通常指按分隔符（如空格）拆分给定句子或文档的术语）为用于计数的键值对。

```
