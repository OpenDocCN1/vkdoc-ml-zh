# https://cloud.google.com/appengine/docs/flexible/python/configuring-your-app-with-app-yaml
manual_scaling:
instances: 1
resources:
cpu: 1
memory_gb: 0.5
disk_size_gb: 10
清单 3-30
App Engine 灵活版“app.yaml”文件内容一览
```

App Engine 灵活版要求磁盘大小至少为 10GB。

有关 App Engine 的“**yaml**”文件的更多信息，请参阅 [`https://cloud.google.com/appengine/docs/flexible/python/configuring-your-app-with-app-yaml`](https://cloud.google.com/appengine/docs/flexible/python/configuring-your-app-with-app-yaml) 。



### appengine_config.py 与 lib 文件夹

“**appengine_config.py**”文件和“**lib**”文件夹协同工作，用于处理运行 Web 应用程序所需的所有额外 Python 库（清单 3-31）。

```
from google.appengine.ext import vendor
