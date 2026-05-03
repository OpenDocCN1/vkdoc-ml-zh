# 包含在 prefect_var_1 中的值
```

YAML 文件中的变量

可以在项目 YAML 文件中引用变量。在 Prefect 中引用 YAML 文件的语法是 `{{ variable name }}`。以下是 Prefect 文档提供的一个示例，其中传递了一个变量来为部署拉取步骤中的 git 仓库指定分支：

```
pull:
- prefect.deployments.steps.git_clone:
repository: https://github.com/PrefectHQ/hello-projects.git
branch: "{{ prefect.variables.deployment_branch }}"
```

上述变量将在运行时进行评估。也可以在不直接更新部署的情况下更改或修改变量。

任务运行器
在 Prefect 中，任务可以以不同的方式运行。它们是执行任务的并发、并行和分布式方式。执行任务时无需指定任务运行器。任务简单地作为 Python 函数执行，并为该函数产生适当的返回值。通过使用默认设置，当在流程中调用任务时，Prefect 会顺序执行该函数。
顺序执行是一种方法，其中函数或任务一个接一个地执行，只有在前一个任务或函数完成后才执行下一个。这是最简单的任务执行模型，它按特定顺序一次执行一个任务。Prefect 提供了一个内置的任务运行器，称为 `SequentialTaskRunner`。

这个 `SequentialTaskRunner` 同时适用于 Python 中的同步和异步函数。以下是一个示例：

```
from prefect import flow, task
from prefect.task_runners import SequentialTaskRunner
@task
def task1():
print("I am task1")
@task
def task2():
task1.fn()
print("I am task2")

