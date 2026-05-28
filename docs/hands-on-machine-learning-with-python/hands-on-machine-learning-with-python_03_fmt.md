# 2. 开始使用 NumPy

我们在前一章学习了 Python 编程语言的基础知识。本章将完全专注于学习 NumPy 库的基础知识。在本章中，我们将进行大量的动手编程练习。虽然就 NumPy 和 Python 而言，编程并不十分困难，但其概念值得学习。我建议所有读者花些时间来理解本章所呈现的思想。

本章的一个主要前提是读者应该已经探索过用于 Python 编程的 Jupyter Notebook。如果你还没有（正如我在前一章末尾所建议的那样），我建议你学习如何有效地使用它。以下是教你如何有效使用 Jupyter Notebook 的网页列表：
- `www.dataquest.io/blog/jupyter-notebook-tutorial/`
- `https://jupyter.org/documentation`
- `https://realpython.com/jupyter-notebook-introduction/`
- `www.tutorialspoint.com/jupyter/jupyter_notebook_markdown_cells.htm`
- `www.datacamp.com/community/tutorials/tutorial-jupyter-notebook`

整章内容完全聚焦于 NumPy 及其功能。本章涵盖以下主题：
- 开始使用 NumPy
- 多维 Ndarray
- Ndarray 的索引
- Ndarray 属性
- NumPy 常量

学习完本章后，我们将能够熟练掌握使用 NumPy 进行编程的基本方面。

## 开始使用 NumPy

我希望你已经足够熟悉 Jupyter，可以开始编写一些小代码片段了。为本章创建一个新的 Jupyter Notebook，我们将始终为每一章的代码使用独立的 Jupyter Notebook。这样可以使代码组织有序，便于参考。

我们可以在 Jupyter Notebook 中运行操作系统命令，只需在命令前加上感叹号，如下所示：
```python
!pip3 install numpy
```

我们知道，在 Linux 系统中默认可以访问 Python 3 和 `pip3`，并且在 Windows 系统中安装时，我们已经将 Python 的安装目录添加到了系统环境变量 `PATH` 中。这就是为什么我们刚刚执行的命令应该能够无错误地运行，并将 NumPy 库安装到你的计算机上。

**注意**

如果命令执行输出显示警告，提示 `pip` 有新版本，你可能希望使用以下命令升级你的 `pip` 工具：
```python
!pip3 install --upgrade --user pip
```
请记住，这不会对我们安装的库或演示的代码示例产生任何影响。库是从仓库 `https://pypi.org/`（也称为 Python 包索引）获取的，任何版本的 `pip` 工具都可以安装最新版本的库。

一旦我们完成了 NumPy 的安装（以及如果你选择升级 `pip`），我们就可以开始使用 NumPy 进行编程了。但是等等！什么是 NumPy？我们为什么要学习它？它与机器学习有什么关系？自从你开始阅读本章以来，这些问题一定一直困扰着你。让我来回答它们。

NumPy 是用于数值计算的基础库。它是科学 Python 生态系统不可或缺的一部分。如果你想学习该生态系统中的任何其他库，我（或任何经验丰富的专业人士）都会建议你先学习 NumPy。NumPy 之所以重要，是因为它用于存储数据。它有一个基础但非常通用的数据结构，称为 `Ndarray`。它的意思是 **N 维数组**。Python 有许多类似数组的数据结构（例如，列表）。但 `Ndarray` 是存储科学和数值数据最通用、最受青睐的结构。

许多库都有自己的数据结构，并且大多数都以 `Ndarray` 为基础。而且 `Ndarray` 与许多数据结构和例程兼容，就像列表一样。我们将在下一章中看到这些示例。但现在，让我们专注于 `Ndarray`。

让我们创建一个简单的 `Ndarray`，如下所示：
```python
import numpy as np
lst1 = [1, 2, 3]
arr1 = np.array(lst1)
```

在这里，我们导入 NumPy 并为其设置别名。然后，我们创建一个列表，并将其作为参数传递给函数 `array()`。让我们看看所有使用过的变量的数据类型：
```python
print(type(lst1))
print(type(arr1))
```

输出如下：

让我们看看 `Ndarray` 的内容，如下所示：
```python
arr1
```

输出如下：
```
array([1, 2, 3])
```

我们可以将其写成一行，如下所示：
```python
arr1 = np.array([1, 2, 3])
```

我们可以指定 `Ndarray` 成员的数据类型，如下所示：
```python
arr1 = np.array([1, 2, 3], dtype=np.uint8)
```

此 URL 包含 `Ndarray` 支持的数据类型的完整列表：
`https://numpy.org/devdocs/user/basics.types.html`

### 多维 Ndarray

我们可以创建多维数组，如下所示：
```python
arr1 = np.array([[1, 2, 3], [4, 5, 6]], np.int16)
arr1
```

输出如下：
```
array([[1, 2, 3],
       [4, 5, 6]], dtype=int16)
```

这是一个二维数组。我们也可以创建一个多维（在以下情况下是 3D 数组）数组，如下所示：
```python
arr1 = np.array([[[1, 2, 3], [4, 5, 6]],
                 [[7, 8, 9], [0, 0, 0]],
                 [[-1, -1, -1], [1, 1, 1]]], np.int16)
arr1
```

输出如下：
```
array([[[ 1,  2,  3],
        [ 4,  5,  6]],
       [[ 7,  8,  9],
        [ 0,  0,  0]],
       [[-1, -1, -1],
        [ 1,  1,  1]]], dtype=int16)
```

## Ndarray 的索引

我们可以单独寻址 `Ndarray` 的元素（也称为成员）。让我们看看如何对一维 `Ndarray` 进行操作：
```python
arr1 = np.array([1, 2, 3], dtype=np.uint8)
```

我们可以像这样寻址其元素：
```python
print(arr1[0])
print(arr1[1])
print(arr1[2])
```

就像列表一样，它遵循 C 风格的索引，其中第一个元素位于位置 0，第 `n` 个元素位于位置 `(n-1)`。

我们也可以使用负位置编号来查看最后一个元素，如下所示：
```python
print(arr1[-1])
```

倒数第二个元素可以这样查看：
```python
print(arr1[-2])
```

如果我们使用无效索引，如下所示：
```python
print(arr1[3])
```

它会抛出以下错误：
```
IndexError                                Traceback (most recent call last)
<ipython-input-5-2d95f0e3e39a> in <module>
----> 1 print(arr1[3])
IndexError: index 3 is out of bounds for axis 0 with size 3
```

让我们创建一个 2D `Ndarray`，如下所示：
```python
arr1 = np.array([[1, 2, 3], [4, 5, 6]], np.int16)
```

我们也可以寻址 2D `Ndarray` 的元素：
```python
print(arr1[0, 0])
print(arr1[0, 1])
print(arr1[0, 2])
```

输出如下：
```

```

我们可以像这样访问整行：
```python
print(arr1[0, :])
print(arr1[1, :])
```

我们也可以像这样访问整列：
```python
print(arr1[:, 0])
print(arr1[:, 1])
```

我们也可以像这样提取三维数组的元素：

```
arr1 = np.array([[[1, 2, 3], [4, 5, 6]],
[[7, 8, 9], [0, 0, 0]],
[[-1, -1, -1], [1, 1, 1]]], np.int16)
```

让我们像这样寻址 3D 数组的元素：

```
print(arr1 [0, 0, 0])
print(arr1 [1, 1, 2])
print(arr1 [:, 1, 1])
```

我们可以通过这种方式访问`Ndarray`的元素。

## `Ndarray` 属性

我们可以通过查看`Ndarray`的属性来了解更多信息。让我们通过演示来学习所有属性。我们使用之前用过的同一个 3D 矩阵：

```
x2 = np.array([[[1, 2, 3], [4, 5, 6]],[[0, -1, -2], [-3, -4, -5]]], np.int16)
```

我们可以通过以下语句了解其维度数量：

```
print(x2.ndim)
```

输出返回维度数量：

```

```

我们可以通过以下方式了解`Ndarray`的形状：

```
print(x2.shape)
```

形状表示各维度的大小，如下所示：

```
(2, 2, 3)
```

我们可以通过以下方式了解成员的数据类型：

```
print(x2.dtype)
```

输出如下：

```
int16
```

我们可以通过以下方式了解其大小（元素数量）以及存储所需的内存字节数：

```
print(x2.size)
print(x2.nbytes)
```

输出如下：

```

```

我们可以通过以下代码计算转置：

```
print(x2.T)
```

## NumPy 常量

NumPy 库包含许多有用的数学和科学常量，我们可以在程序中使用它们。以下代码片段打印了所有此类重要常量：

```
print(np.inf)
print(np.NAN)
print(np.NINF)
print(np.NZERO)
print(np.PZERO)
print(np.e)
print(np.euler_gamma)
print(np.pi)
```

输出如下：

```
inf
nan
-inf
-0.0
0.0
2.718281828459045
0.5772156649015329
3.141592653589793
```

## 总结

在本章中，我们熟悉了 Python 和 IDLE。我们学习了如何使用 Python 解释器以及如何编写 Python 脚本。我们还学习了如何执行 Python 程序。在下一章中，我们将继续 NumPy 的学习之旅。