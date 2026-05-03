# 使用 submit 可以创建 Prefect Future，有助于返回任务的状态和结果
@flow (task_runner=SequentialTaskRunner())
def flow1():
task1.submit()
task2.submit()
if __name__ == "__main__":
flow1()
```

Prefect 还提供了 `ConcurrentTaskRunner`，这是另一个内置的任务运行器。

`ConcurrentTaskRunner` 以并发方式执行任务，其中多个函数或任务可以启动、运行和完成，而无需等待另一个进程完成。一个任务的执行不会阻塞流程中另一个任务的执行。以下是一个示例：

```
from prefect import flow, task
from prefect.task_runners import ConcurrentTaskRunner
import time
@task
def stop_at_floor(floor):
print(f"elevator moving to floor {floor}")
time.sleep(floor)
print(f"elevator stops on floor {floor}")
@flow(task_runner=ConcurrentTaskRunner())
def elevator():
for floor in range(10, 0, -1):
stop_at_floor.submit(floor)
if __name__ == "__main__":
elevator()
```

此外，Prefect 还附带 `DaskTaskRunner`，这是一个并行任务运行器。它需要安装 Python 包 `prefect-dask`。`DaskTaskRunner` 将任务提交给分布式调度器。对于任何给定的流程运行，都会创建一个临时的 Dask 集群。以下是一个示例：

```
import dask
from prefect import flow, task
from prefect_dask.task_runners import DaskTaskRunner
@task
def show(x):
print(x)

