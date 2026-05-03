# 这里，我们声明我们的系统每个工作进程有 1 个 GPU 和 1 个进程可用
@flow(
task_runner=DaskTaskRunner(
cluster_kwargs={"n_workers": 1, "resources": {"GPU": 1, "process": 1}}
)
)
def my_flow():
with dask.annotate(resources={'GPU': 1}):
future = show(0)  # 此任务在工作进程上需要 1 个 GPU 资源
with dask.annotate(resources={'process': 1}):

