# 第 4 章 卡尔曼滤波器

理解或控制一个物理系统通常需要该系统的模型，即了解系统的特性和结构。模型可以是预定义的结构，也可以完全通过数据来确定。在卡尔曼滤波中，我们创建一个模型，并将其作为了解系统的框架。这是第 1 章中自主学习方法论中控制分支的一部分。自主学习机器控制学习智能。

卡尔曼滤波器的重要性在于，它们严谨地处理了你想进一步了解的系统中存在的不确定性。如果你有系统模型，那么模型中存在不确定性；系统的测量值中也存在不确定性（即噪声）。

一个系统可以通过其动态状态和名义上恒定的参数来定义。例如，如果你正在研究一个在桌子上滑动的物体，其状态将是位置和速度。参数将是物体的质量和摩擦系数。物体上可能还存在我们想要估计的外力。

参数和状态共同构成了模型。你需要了解两者才能正确理解系统。有时很难判断某个量应该被视为状态还是参数。质量通常是一个参数，但在飞机、汽车或火箭中，质量会随着燃料消耗而变化，因此通常被建模为一个状态。

由 R. E. Kalman 等人发明的卡尔曼滤波器，是一个用于估计或学习系统状态的数学框架。估计器能给出系统动态状态（例如移动质点的位置和速度）的统计最优估计。

© Michael Paluszek 和 Stephanie Thomas 2019
M. Paluszek 和 S. Thomas, *MATLAB 机器学习秘籍*,
[`doi.org/10.1007/978-1-4842-3916-2_4`](https://doi.org/10.1007/978-1-4842-3916-2_4)

![](img/index-91_1.png)
![](img/index-91_2.png)
![](img/index-91_3.png)

卡尔曼滤波器也可以被编写用于识别系统的参数。因此，卡尔曼滤波器为状态识别和参数识别提供了一个框架。

卡尔曼滤波器的另一个应用是系统辨识。系统辨识是识别任何系统结构和参数的过程。例如，对于一个简单的弹簧上的质量块，它就是要识别或确定质量与弹簧常数的值，同时确定用于建模该系统的微分方程。这是一种起源于控制理论的机器学习形式。系统辨识有许多方法。在本章中，我们只研究卡尔曼滤波器。“学习”这个词通常不与估计相关联，但实际上它们是同一回事。

系统辨识问题的一个重要方面是，根据可用的测量值，确定哪些参数和状态实际上可以被估计。这适用于所有学习系统。问题在于，我们能否通过观察来了解我们需要知道的关于某事物的信息？为此，我们需要知道一个参数或状态是否可观测，并且能否被独立区分。例如，假设我们使用牛顿定律：*F* = *ma* (4.1)，其中 *F* 是力，*m* 是质量，*a* 是加速度，作为我们的模型，并且我们的测量值是加速度。我们能同时估计力和质量吗？答案是否定的，因为我们测量的是力与质量的*比值* *a* = *F* / *m* (4.2)。我们无法将两者分开。如果我们有一个力传感器或一个质量传感器，我们就可以分别确定它们。在所有学习系统中，包括卡尔曼滤波器，你都需要注意这个问题。

## 4.1 使用线性卡尔曼滤波器的状态估计器

### 4.1.1 问题

你想要估计一个通过弹簧和阻尼器连接到结构上的质量块的速度和位置。该系统如图 4.1 所示。`m` 是质量，`k` 是弹簧常数，`c` 是阻尼常数，`f` 是外力。`x` 是位置。质量块仅在一个方向上移动。

假设我们在质量块附近有一个摄像头。摄像头将在质量块上升期间对准它。这将产生一个地面与摄像头视轴之间夹角的测量值。角度测量几何关系如图 4.2 所示。该角度是从一个偏移基线上测量的。

我们想使用传统的线性卡尔曼滤波器来估计系统的状态。这适用于可以用线性方程建模的简单系统。

![](img/index-92_1.png)
![](img/index-92_2.png)
![](img/index-92_3.png)
![](img/index-92_4.png)
![](img/index-92_5.png)
![](img/index-92_6.png)

**图 4.1：** 弹簧-质量-阻尼器系统。质量块在右侧。弹簧在质量块左上方。阻尼器在下方。
k m f c x

**图 4.2：** 角度测量几何关系。
质量块 r 角度 基线

### 4.1.2 解决方案

首先，我们需要为质量系统定义一个数学模型并将其编码。然后，我们将使用贝叶斯定理从基本原理推导出卡尔曼滤波器。最后，我们给出实现卡尔曼滤波器估计器以解决弹簧-质量问题的代码。

### 4.1.3 工作原理

**弹簧-质量系统模型**

对系统进行建模的连续时间微分方程为：
`dr / dt = v` (4.3)
`m * dv / dt = f − cv − kx` (4.4)

![](img/index-93_1.png)
![](img/index-93_2.png)
![](img/index-93_3.png)
![](img/index-93_4.png)
![](img/index-93_5.png)
![](img/index-93_6.png)
![](img/index-93_7.png)
![](img/index-93_8.png)
![](img/index-93_9.png)
![](img/index-93_10.png)
![](img/index-93_11.png)
![](img/index-93_12.png)
![](img/index-93_13.png)
![](img/index-93_14.png)
![](img/index-93_15.png)

这表明位置 `r` 相对于时间 `t` 的变化率是速度 `v`。速度相对于时间的变化率（乘以质量）等于外力减去阻尼常数乘以速度，再减去弹簧常数乘以位置。第二个方程就是牛顿定律，其中总力为 `F`，总加速度 `aT` 等于总力除以质量 `m`，即 `F = f − cv − kx` (4.5)，`dv/dt = aT` (4.6)。

为简化问题，我们将第二个方程两边同时除以质量，得到： `dr/dt = v` (4.7)，`dv/dt = a − 2ζωv − ω²x` (4.8)。

其中 `c/m = 2ζω` (4.9)，`k/m = ω²` (4.10)。

`a` 是外力 `f` 产生的加速度，`ζ` 是阻尼比，`ω` 是无阻尼固有频率。无阻尼固有频率是指质量在没有阻尼的情况下振动的频率。阻尼比表示系统阻尼的快慢以及我们观察到的振动水平。当阻尼比为零时，系统永不衰减，质量将永远振动下去。当阻尼比为 1 时，则观察不到任何振动。这种形式使我们更容易理解预期的阻尼和振动情况。你可以立即知道频率以及振动衰减的速率。而 `m`、`c` 和 `k` 虽然包含相同的信息，但并不能如此直观地体现这一点。

以下展示了带阻尼的振荡器仿真（OscillatorDampingRatioSim）。它显示了不同的阻尼比。运行不同阻尼比仿真的循环如下所示。

```
for j = 1:length(zeta)
    % 初始状态 [位置;速度]
    x = [0;1];
    % 从数组中选取阻尼比
    d.zeta = zeta(j);
    % 为图例打印字符串
    s{j} = sprintf('zeta = %6.4f',zeta(j));
    
    for k = 1:nSim
        % 绘图存储
        xPlot(j,k) = x(1);
        % 传播（数值积分）状态方程
        x = RungeKutta( @RHSOscillator, 0, x, dT, d );
    end
end
```

![](img/index-94_1.png)
![](img/index-94_2.png)
![](img/index-94_3.png)

阻尼比演示的结果如图 4.3 所示。初始条件为零位置和速度为 1。可以看到对不同阻尼比水平的响应。

当 `zeta` 为零时，系统无阻尼，将永远振动。临界阻尼（从最小化执行器能耗的角度来看是理想的）为 0.7071。阻尼比为 1 时，对阶跃扰动不会产生过冲。在本例中，由于初始条件并非静止状态，因此出现了“过冲”。

**图 4.3：** 不同阻尼比 `zeta` 下的弹簧-质量-阻尼系统仿真。
zeta = 0.0000
zeta = 0.2000
zeta = 0.7071
zeta = 1.0000
r 0
-5 0
时间 (秒)

动力学方程采用所谓的状态空间形式，因为状态向量： `x = [r; v]` (4.11) 的导数没有乘以任何系数，并且左侧只有一阶导数。有时你会看到如下方程： `Q ẋ = Ax + Bu` (4.12)。如果 `Q` 不可逆，则无法进行如下操作： `ẋ = Q⁻¹Ax + Q⁻¹Bu` (4.13)。

![](img/index-95_1.png)
![](img/index-95_2.png)

来得到状态空间方程。从概念上讲，如果 `Q` 不可逆，这等同于少于 `N` 个独立方程（其中 `N` 是 `x` 的长度，即状态的数量）。

我们所有的滤波器推导都基于状态空间形式的动力学方程。此外，大多数数值积分方案都是为求解一阶微分方程组而设计的。

状态方程（一阶微分方程）的右侧函数 `RHSOscillator` 如下所示。请注意，如果没有输入参数，它将返回默认数据结构。代码 `if (nargin < 1)` 指示函数在没有输入时返回数据结构。这是一种使函数自文档化并保持数据结构一致性的便捷方法。实际的工作代码只有一行。

```
xDot = [x(2);d.a-2*d.zeta*d.omega*x(2)-d.omega^2*x(1)];
```

以下列出了仿真脚本 `OscillatorSim`。它使用 `RungeKutta` 函数对右侧函数 `RHSOscillator` 进行数值积分。

我们首先从右侧函数获取默认数据结构，然后填入所需的参数。每一步都会生成包含随机噪声的测量值 `y`。共有两个测量值：位置和角度。

以下代码仅展示了 `OscillatorSim` 的仿真循环。角度测量仅涉及三角学。第一行测量代码计算角度，这是一个非线性测量。第二行测量垂直距离，这是线性测量。

```matlab
for k = 1:nSim
    % 测量值
    yTheta = atan(x(1)/baseline) + yTheta1Sigma*randn(1,1);
    yR = x(1) + yR1Sigma*randn(1,1);
    % 绘图存储
    xPlot(:,k) = [x;yTheta;yR];
    % 传播（数值积分）状态方程
    x = RungeKutta( @RHSOscillator, 0, x, dT, dRHS );
end
```

![img/index-96_1.png](img/index-96_1.png)

![img/index-96_2.png](img/index-96_2.png)

![img/index-96_3.png](img/index-96_3.png)

![img/index-96_4.png](img/index-96_4.png)

![img/index-96_5.png](img/index-96_5.png)

仿真结果如图 4.4 所示。输入是一个扰动加速度，在时间 `t = 0` 时从零跳变到其值。它在整个仿真期间保持恒定。

这被称为阶跃扰动。这会导致系统振荡。由于阻尼的存在，振荡幅度会缓慢衰减至零。如果阻尼比为 1，我们将看不到任何振荡，如图 4.3 所示。

**图 4.4：** 弹簧-质量-阻尼器系统仿真。输入是一个阶跃加速度。振荡缓慢衰减，即随时间推移趋于零。由于恒定加速度，位置 `r` 产生了一个偏移。

**振荡器**

r (m)

时间 (秒)

0.5

v (m/s)

-0.5

时间 (秒)

(rad)y

时间 (秒)

(m)

r

y

时间 (秒)

通过设置 `v = 0`，可以解析地找到 `r` 图中所示的偏移。本质上，弹簧力与外力相平衡。

```
dv
0 =   = a - ω² x (4.14)
dt

a
x =     (4.15)
ω²
```

我们现在已经完成了模型的推导，可以继续构建卡尔曼滤波器了。

![img/index-97_1.png](img/index-97_1.png)

![img/index-97_2.png](img/index-97_2.png)

![img/index-97_3.png](img/index-97_3.png)

![img/index-97_4.png](img/index-97_4.png)

## 卡尔曼滤波器推导

卡尔曼滤波器可以从贝叶斯定理推导出来。什么是贝叶斯定理？贝叶斯定理是：

```
P(B|Ai) P(Ai)
P(Ai|B) =       (4.16)
P(B)

P(B|Ai) P(Ai)
P(Ai|B) =       (4.17)
P(B)
```

这只是在给定 `B` 的情况下 `Ai` 的概率。`P` 表示“概率”。竖线 `|` 表示“给定”。这假设 `B` 的概率不为零，即 `P(B) ≠ 0`。在贝叶斯解释中，该定理引入了证据对信念的影响。这为整合任何存在不确定性的数据提供了一个严谨的框架。简而言之，给定迄今为止的所有证据（或数据），贝叶斯定理允许你确定新证据如何影响信念。在状态估计的背景下，这就是对状态估计准确性的信念。

图 4.5 展示了卡尔曼滤波器家族及其与贝叶斯滤波器的关系。本书仅涵盖彩色框中的滤波器。下面给出了卡尔曼滤波器的完整推导；这为所有卡尔曼滤波实现提供了一个连贯的框架。不同的滤波器是基于对模型和传感器噪声的假设，以及测量和动力学模型的线性或非线性特性，从贝叶斯模型中推导出来的。让我们看看蓝色分支。加性高斯噪声滤波器可以是线性的或非线性的，具体取决于动力学和测量模型的类型。在许多情况下，你可以对一个非线性系统进行线性化处理，使其围绕正常工作点线性化。然后你可以使用线性卡尔曼滤波器。例如，航天器动力学模型是非线性的，而用于获取滚动和俯仰信息的地球传感器（测量地球弦宽）也是非线性的。

然而，如果我们只关心对地指向，以及相对于标称指向的小偏差，我们可以将动力学方程和测量方程都线性化，并使用线性卡尔曼滤波器。

如果非线性特性很重要，我们必须使用非线性滤波器。扩展卡尔曼滤波器（`EKF`）使用测量方程和动力学方程的偏导数。这些偏导数在每个时间步或每次测量输入时计算。实际上，我们是在每一步对系统进行线性化，并使用线性方程。我们不必进行线性状态传播，即传播动力学方程，而是可以通过数值积分来传播它们。如果我们能获得测量方程和动力学方程的解析导数，这是一种合理的方法。如果任何方程中存在奇点，这种方法可能失效。

无迹卡尔曼滤波器（`UKF`）直接使用非线性方程。它有两种形式：增广型和非增广型。在前者中，我们创建了一个增广状态向量，其中包含状态以及状态和测量噪声变量。这可能会以增加计算量为代价获得更好的结果。

本章中的所有滤波器都是马尔可夫型的，即当前动力学状态完全由前一状态决定。粒子滤波器不在本书讨论范围内。它们是一类蒙特卡洛方法。蒙特卡洛（以著名赌场命名）方法是依赖随机抽样来获得结果的计算算法。例如，对我们的振荡器仿真采用蒙特卡洛方法，就是使用 MATLAB 函数 `randn` 来生成加速度。我们会运行多次测试，以验证我们的质量块按预期运动。

我们的推导将使用符号 `N(μ, σ²)` 来表示正态变量。正态变量是高斯变量的另一种说法。高斯意味着它服从均值为 `μ`（平均值）、方差为 `σ²` 的正态分布。以下来自 `Gaussian` 的代码计算了围绕均值 2、具有一系列标准差的高斯或正态分布。

图 4.6 显示了一个图。图的高度表示该变量取该值的可能性有多大。

```matlab
%% 初始化
mu = 2;             % 均值
sigma = [1 2 3 4];  % 标准差
n = length(sigma);
x = linspace(-7,10);
%% 仿真
xPlot = zeros(n, length(x));
s = cell(1,n);
for k = 1:length(sigma)
    s{k} = sprintf('Sigma = %3.1f',sigma(k));
    f = -(x-mu).^2/(2*sigma(k)^2);
    xPlot(k,:) = exp(f)/sqrt(2*pi*sigma(k)^2);
end
```

![img/index-99_1.png](img/index-99_1.png)

![img/index-99_2.png](img/index-99_2.png)

![img/index-99_3.png](img/index-99_3.png)

**图 4.6：** 围绕均值 2 的正态或高斯随机变量。

0.4

Sigma = 1.0

Sigma = 2.0

0.35

Sigma = 3.0

Sigma = 4.0

0.3

0.25

0.2

高斯

0.15

0.1

0.05

x

给定离散时间下的概率状态空间模型 [[23]](#index_split_007.html#p350)

```
xk = fk(xk-1, wk-1) (4.18)
```

其中 `x` 是状态向量，`w` 是噪声向量，测量方程为：

```
yk = hk(xk, vn) (4.19)
```

其中 `vn` 是测量噪声。这具有隐马尔可夫模型（`HMM`）的形式，因为状态是隐藏的。

如果过程是马尔可夫性的，那么未来状态 `xk` 仅依赖于当前状态 `xk-1`，而不依赖于过去的状态。这可以用方程表示为：

```
p(xk | x1:k-1, y1:k-1) = p(xk | xk-1) (4.20)
```

这里的 `|` 表示“给定”。在这种情况下，第一项读作“给定 `x1:k-1` 和 `y1:k-1` 时 `xk` 的概率”。这是给定所有过去状态以及直到第 `k-1` 次测量的所有测量值时，当前状态的概率。过去状态 `xk-1` 在给定当前状态的情况下与未来无关。

```
p(xk-1 | xk:T, yk:T) = p(xk-1 | xk) (4.21)
```

其中 *T* 是最后一个样本，并且测量值 *yk* 在给定 *xk* 的条件下是条件独立的；也就是说，它们可以仅使用 *xk* 来确定，而不依赖于 *x*1:*k* 或 *y*1:*k−1*。这可以表示为：
`p( yk|x1:k, y1:k−1) = p( yk|xk)` (4.22)

我们可以定义计算分布的递归贝叶斯最优滤波器：`p( xk|y1:k)`
(4.23)

![](img/index-100_1.png)
![](img/index-100_2.png)
![](img/index-100_3.png)

给定：
• 先验分布 `p( x0)`，其中 `x0` 是第一次测量前的状态，
• 状态空间模型
`xk ∼ p( xk|xk−1*)` (4.24)
`yk ∼ p( yk|xk*)` (4.25)
• 测量序列 `y1:k = y1, ..., yk`。

计算基于递归规则
`p( xk−1|y1:k−1) → p( xk|y1:k)` (4.26)

这意味着我们从先验状态 `xk−1` 和所有过去的测量值 `y1:k−1` 得到当前状态 `xk`。假设我们知道前一个时间步的后验分布：`p( xk−1|y1:k−1)`
(4.27)

给定 `y1:k−1` 时 `xk`, `xk−1` 的联合分布可以计算为：`p( xk, xk−1|y1:k−1) = p( xk|xk−1, y1:k−1) p( xk−1|y1:k−1)` (4.28)
`= p( xk|xk−1) p( xk−1|y1:k−1)` (4.29)
因为这是一个马尔可夫过程。对 `xk−1` 进行积分得到最优滤波器的预测步骤，即查普曼-科尔莫戈罗夫方程
`p( xk|y1:k−1) = ∫ p( xk|xk−1, y1:k−1) p( xk−1|y1:k−1) dxk−1`
(4.30)

查普曼-科尔莫戈罗夫方程是一个恒等式，关联了随机过程中不同坐标集合的联合概率分布。测量更新状态由贝叶斯规则得出：
`P( xk|y1:k) = [p( yk|xk) p( xk|yk−1)] / Ck` (4.31)
`Ck = p( yk|y1:k−1) = ∫ p( yk|xk) p( xk|y1:k−1) dxk` (4.32)
`Ck` 是给定所有过去测量值时当前测量值的概率。

如果噪声是加性的且服从高斯分布，状态协方差为 `Qn`，测量协方差为 `Rn`，模型和测量噪声均值为零，那么我们可以将状态方程写为：
`xk = fk( xk−1) + wk−1` (4.33)

![](img/index-101_1.png)
![](img/index-101_2.png)

其中 *x* 是状态向量，*w* 是噪声向量。测量方程变为：`yk = hk( xk) + vn` (4.34)

假设 `Q` 不随时间变化，我们可以写出：
`p( xk|xk−1, y1:k−1) = N( xk; f( xk−1), Q)` (4.35)
其中回忆 *N* 是一个正态变量，在这种情况下，均值为 `xk; f( xk−1)`，这意味着（给定 `f( xk−1)` 且方差为 *Q* 时的 *xk*）。我们现在可以将预测步骤方程 4.30 写为：
`p( xk|y1:k−1) = ∫ N( xk; f( xk−1), Q) p( xk−1|y1:k−1) dxk−1` (4.36)

我们需要找到 *xk* 的前两个矩。矩是变量的期望值（或均值）。第一个矩是变量本身，第二个矩是变量的平方，依此类推。它们是：
`E[ xk] = ∫ xk p( xk|y1:k−1) dxk` (4.37)
`E[ xk xTk] = ∫ xk xTk p( xk|y1:k−1) dxk` (4.38)

*E* 表示期望值。`E[ xk]` 是均值，`E[ xk xTk]` 是协方差。展开第一个矩并利用恒等式 `E[ x] = ∫ x N( x; f(s), Σ) dx = f(s)` 其中 *s* 是任意参数。

`E[ xk]` =
`∫ xk N( xk; f( xk−1), Q) p( xk−1 |y1: k−1) dxk−1  dxk` (4.39)
=
`∫ [∫ N( xk; f( xk−1), Q) dxk] p( xk−1 |y1: k−1) dxk−1` (4.40)
=
`∫ f( xk−1) p( xk−1 |y1: k−1) dxk−1` (4.41)

假设 `p( xk−1 |y1: k−1) = N( xk−1; ˆxk−1 |k−1 , Pxxk−1 |k−1)` 其中 *Pxx* 是 *x* 的协方差
并注意到 `xk = fk( xk−1) + wk−1`，我们得到：
`ˆxk|k−1 = ∫ f( xk−1) N( xk−1; ˆxk−1 |k−1 , Pxxk−1 |k−1) dxk−1` (4.42)

对于二阶矩：
`E[ xkxTk ] = ∫ xkxTk p( xk|y1: k−1) dxk` (4.43)
`= ∫ [∫ N( xk; f( xk−1), Q) xkxTk dxk] p( xk−1 |y1: k−1) dxk−1` (4.44)

![](img/index-102_1.png)
![](img/index-102_2.png)

结果如下：
`Pxxk|k−1 = Q + ∫ f( xk−1) fT ( xk−1) N( xk−1; ˆxk−1 |k−1 , Pxxk−1 |k−1) dxk−1  −  ˆxTk|k−1 ˆxk|k−1` (4.45)

初始状态的协方差是高斯分布，记为 *Pxx0*。卡尔曼滤波器可以无需进一步近似地写为：
`ˆxk|k = ˆxk|k−1 +  Kn yk −  ˆyk|k−1` (4.46)
`Pxxk|k = Pxxk|k−1  − KnPyyk|k−1 K^T_n` (4.47)
`Kn = Pxyk|k−1 Pyyk|k−1 ^{-1}` (4.48)

其中 *Kn* 是卡尔曼增益，*Pyy* 是测量协方差。这些方程的解需要求解以下形式的五个积分：
`I = ∫ g(x) N( x; ˆx, Pxx) dx` (4.49)

滤波器所需的三个积分是：
`Pyyk|k−1 = R + ∫ h(xn) hT (xn) N( xn; ˆxk|k−1 , Pxxk|k−1) dxk −  ˆxTk|k−1  ˆyk|k−1` (4.50)
`Pxyk|k−1 = ∫ xnhT (xn) N( xn; ˆxk|k−1 , Pxxk|k−1) dx` (4.51)
`ˆyk|k−1 = ∫ h(xk) N( xk; ˆxk|k−1 , Pxxk|k−1) dxk` (4.52)

假设我们有一个如下形式的模型：
`xk = Ak−1 xk−1 + Bk−1 uk−1 + qk−1` (4.53)
`yk = Hkxk + rk` (4.54)

其中
• `xk ∈ n` 是系统在时刻 *k* 的状态
• `mk` 是时刻 *k* 的均值状态
• `Ak−1` 是时刻 `k − 1` 的状态转移矩阵
• `Bk − 1` 是时刻 `k − 1` 的输入矩阵
• `uk − 1` 是时刻 `k − 1` 的输入
• `qk−1, N(0, Qk)`, 是时刻 `k − 1` 的高斯过程噪声

![](img/index-103_1.png)

![](img/index-103_2.png)

*   `yk ∈ m` 是时刻 `k` 的测量值
*   `Hk` 是时刻 `k` 的测量矩阵。该矩阵由 `h(x)` 的雅可比矩阵（导数）求得。
*   `rk = N(0, Rk)`, 是时刻 `k` 的高斯测量噪声
*   状态的先验分布是 `x0 = N(m0, P0)`，其中参数 `m0` 和 `P0` 包含了关于系统的所有先验知识。`m0` 是零时刻的均值，`P0` 是协方差。由于我们的状态是高斯分布，这完全描述了状态。
*   `x̂k|k−1` 是在给定 `x̂` at `k−1` 的条件下，`x` 在 `k` 时刻的均值
*   `ŷk|k−1` 是在给定 `x̂` at `k−1` 的条件下，`y` 在 `k` 时刻的均值
*   `n` 表示 `n` 阶向量中的实数，即状态有 `n` 个量。用概率术语表示，该模型为：

    `p(xk|xk−1) = N(xk; Ak−1xk−1, Qk)` (4.55)
    `p(yk|xk) = N(yk; Hkxk, Rk)` (4.56)

这些积分变成了简单的矩阵方程。在以下方程中，`P−` 表示测量更新前的协方差。

    `Pyy_{k|k−1} = HkP−_kH^T_k + Rk` (4.57)
    `Pxy_{k|k−1} = P−_kH^T_k` (4.58)
    `Pxx_{k|k−1} = Ak−1Pk−1A^T_{k−1} + Qk−1` (4.59)
    `x̂k|k−1 = m−_k` (4.60)
    `ŷk|k−1 = Hkm−_k` (4.61)

预测步骤变为：

    `m−_k = Ak−1mk−1` (4.62)
    `P−_k = Ak−1Pk−1A^T_{k−1} + Qk−1` (4.63)

上述协方差方程中的第一项基于状态转移矩阵 `A` 传播协方差。`Qk+1` 在此基础上累加，形成下一个协方差。过程噪声 `Qk+1` 是衡量数学模型 `A` 在表示系统时精确度的指标。例如，假设 `A` 是一个将所有状态阻尼至零的数学模型。如果没有 `Q`，`P` 将变为零。但如果我们对模型并非那么确定，协方差就永远不会小于 `Q`。选取 `Q` 可能很困难。在一个存在不确定扰动的动态系统中，你可以计算扰动的标准差来得到 `Q`。如果模型 `A` 不确定，那么你可以对模型的范围进行统计分析。或者，你可以在仿真中尝试不同的 `Q`，看看哪些效果最好！

![](img/index-104_1.png)
![](img/index-104_2.png)
![](img/index-104_3.png)
![](img/index-104_4.png)
![](img/index-104_5.png)

更新步骤如下：

    `vk = yk − Hkm−_k` (4.64)
    `Sk = HkP−_kH^T_k + Rk` (4.65)
    `Kk = P−_kH^T_kS^{-1}_k` (4.66)
    `mk = m−_k + Kkvk` (4.67)
    `Pk = P−_k − KkSkK^T_k` (4.68)

`Sk` 是一个中间量。`vk` 是残差。残差是测量值与根据估计状态得出的测量估计值之间的差值。`R` 就是测量值的协方差矩阵。如果噪声不是白噪声，则应使用不同的滤波器。白噪声在所有频率上具有相等的能量。许多类型的噪声，例如来自成像器的噪声，并非真正的白噪声，而是带限的，即其噪声存在于有限的频率范围内。有时你可以向 `A` 添加额外的状态来更好地建模噪声，例如，添加一个低通滤波器来限制噪声的带宽。这会使 `A` 变大，但通常不是问题。

**卡尔曼滤波器实现**

现在我们将为质量-弹簧振荡器实现一个卡尔曼滤波器估计器。首先，我们需要一种将连续时间问题转换为离散时间的方法。我们只需要知道离散时刻或固定间隔 `T` 下的状态。我们使用连续到离散的变换，该变换使用了 MATLAB 的 `expm` 函数，该函数执行矩阵指数运算。

此变换在 `CToDZOH` 中编码，其主体如下列代码所示。`T` 是采样周期。

```
[n,m] = size(b);
q     = expm([a*T b*T; zeros(m,n+m)]);
f     = q(1:n,1:n);
g     = q(1:n,n+1:n+m);
```

`CToDZOH` 包含一个针对双积分器的演示。双积分器是一个系统，其中状态的二阶导数直接依赖于外部输入。在此示例中，`x` 是状态，代表位置，而 `a` 是加速度的外部输入。

    `d²r/dt² = a` (4.69)

写成状态空间形式为：

    `dr/dt = v` (4.70)
    `dv/dt = a` (4.71)

或矩阵形式

    `ẋ = Ax + Bu` (4.72)

![](img/index-105_1.png)
![](img/index-105_2.png)
![](img/index-105_3.png)
![](img/index-105_4.png)

其中

    `x = [r, v]^T` (4.73)
    `u = [a]` (4.74)
    `A = [[0, 1], [0, 0]]` (4.75)
    `B = [[0], [1]]` (4.76)

要运行演示，只需在命令行中不带任何输入地运行 `CToDZOH`。

```
>> CToDZOH
双积分器，时间步长为 0.5 秒。
a =
   0     1
   0     0
b =
   0
   1
f =
   1.0000    0.5000
        0    1.0000
g =
   0.1250
   0.5000
```

离散系统矩阵 `f` 很容易理解。第 `k+1` 步的位置状态是第 `k` 步的状态加上第 `k` 步的速度乘以时间步长 `T`（0.5 秒）。第 `k+1` 步的速度是第 `k` 步的速度加上时间步长乘以第 `k` 步的加速度。第 `k` 时刻的加速度乘以 `1/2 T²` 得到对位置的贡献。这只是一个粒子在恒定加速度下的标准解。

    `rk+1 = rk + Tvk + 1/2 T²ak` (4.77)
    `vk+1 = vk + Tak` (4.78)

写成矩阵形式为：

    `xk+1 = f xk + buk` (4.79)

通过离散时间近似，我们可以在每一步 `k` 改变加速度，从而获得时间历程。这假设加速度在周期 `T` 内是恒定的。为了获得良好结果，我们需要选择足够小的 `T`，使得该假设近似成立。

用于测试卡尔曼滤波器的脚本是 `KFSim.m`。`KFInitialize` 用于初始化滤波器（本例中为卡尔曼滤波器，即 `'kf'`）。该函数被编写为能够处理多种类型的卡尔曼滤波器，我们将在 EKF 和 UKF（分别为 `'ekf'` 和 `'ukf'`）的示例中再次使用它。下面展示了该函数。此函数使用动态字段名将输入值分配给每个字段。

仿真开始时，会为仿真中使用的所有变量赋值。我们从函数 `RHSOscillator` 获取数据结构，然后修改其值。我们将连续时间模型写成矩阵形式，然后将其转换为离散时间。请注意，乘以状态 `h` 的测量方程矩阵是 `[1 0]`，表示我们正在测量质量块的位置。使用 MATLAB 的 `randn` 随机数函数向仿真中添加高斯噪声。脚本的其余部分是仿真循环，之后是绘图。

脚本的第一部分创建连续时间状态空间矩阵，并使用 `CToDZOH` 将其转换为离散时间。然后使用 `KFInitialize` 初始化卡尔曼滤波器。

# 卡尔曼滤波与状态估计

## 初始化与仿真设置

```matlab
%% 初始化
tEnd   = 100.0;      % 仿真结束时间（秒）
dT     = 0.1;        % 时间步长（秒）
d      = RHSOscillator(); % 获取默认数据结构
d.a    = 0.1;        % 扰动加速度
d.omega = 0.2;       % 振荡器频率
d.zeta = 0.1;        % 阻尼比
x      = [0;0];      % 初始状态 [位置;速度]
y1Sigma = 1;         % 1 sigma 位置测量噪声
% xdot = a*x + b*u
a = [0 1;-2*d.zeta*d.omega -d.omega²]; % 连续时间模型
b = [0;1];           % 连续时间输入矩阵
% x[k+1] = f*x[k] + g*u[k]
[f,g] = CToDZOH(a,b,dT); % 离散时间模型
xE    = [0.3; 0.1];  % 估计的初始状态
q     = [1e-6 1e-6]; % 模型噪声协方差；
                     % [1e-6 1e-6] 用于低模型噪声测试
                     % [1e-4 1e-4] 用于高模型噪声测试
dKF = KFInitialize('kf','m',xE,'a',f,'b',g,'h',[1 0],...
                   'r',y1Sigma²,'q',diag(q),'p',diag(xE.²));
```

仿真循环依次执行状态测量、卡尔曼滤波器更新和预测步骤，分别使用代码 `KFPredict` 和 `KFUpdate`。积分器位于两者之间，以确保更新和预测的相位正确。你必须小心地将预测和更新步骤放在脚本中的正确位置，以使估计器与仿真时间同步。

```matlab
%% 仿真
nSim = floor(tEnd/dT) + 1;
xPlot = zeros(5,nSim);

for k = 1:nSim
    % 带随机噪声的位置测量
    y = x(1) + y1Sigma*randn(1,1);
    % 更新卡尔曼滤波器
    dKF.y = y;
    dKF = KFUpdate(dKF);
    % 存储绘图数据
    xPlot(:,k) = [x;y;dKF.m-x];
    % 传播（数值积分）状态方程
    x = RungeKutta( @RHSOscillator, 0, x, dT, d );
    % 传播卡尔曼滤波器
    dKF.u = d.a;
    dKF = KFPredict(dKF);
end
```

## 滤波器步骤

预测卡尔曼滤波器步骤 `KFPredict` 如下所示，其头部已简化。该预测将状态向前传播一个时间步长，并同时传播协方差矩阵。这意味着当我们传播状态时，存在不确定性，因此我们必须将其添加到协方差矩阵中。

```matlab
%% KFPREDICT 线性卡尔曼滤波器预测步骤。
function d = KFPredict( d )
% 第一种情况：没有输入矩阵 b
if( isempty(d.b) )
    d.m = d.a*d.m;
else
    d.m = d.a*d.m + d.b*d.u;
end
d.p = d.a*d.p*d.a' + d.q;
```

更新卡尔曼滤波器步骤 `KFUpdate` 如下所示。该步骤将测量值添加到估计中，并考虑测量中的不确定性（噪声）。

```matlab
%% KFUPDATE 线性卡尔曼滤波器测量更新步骤。
function d = KFUpdate( d )
s = d.h*d.p*d.h' + d.r;
```

% 中间值

`k`

= `d.p` * `d.h'` / `s`;

% 卡尔曼增益

`v`

= `d.y` - `d.h` * `d.m`;

% 残差

`d.m` = `d.m` + `k` * `v`;

% 均值更新

`d.p` = `d.p` - `k` * `s` * `k'`;

% 协方差更新

![](img/index-108_1.png)

![](img/index-108_2.png)

![](img/index-108_3.png)

你会注意到，滤波器的“记忆”存储在数据结构 `d` 中。没有使用持久化数据存储。这使得在你的代码中多处使用这些函数变得更加容易。

另请注意，你不需要在每个时间步都调用 `KFUpdate`。你只需要在有新数据时调用它。不过，该滤波器确实假设时间步长是均匀的。

该脚本给出了模型噪声协方差矩阵的两个示例。图 4.7 显示了使用较高数值 `[1e-4 1e-4]` 作为模型协方差时的结果。图 4.8 显示了使用较低数值 `[1e-6 1e-6]` 时的结果。我们没有更改测量协方差，因为只有噪声协方差与模型协方差之间的比值才是重要的。

当使用较高数值时，误差呈高斯分布但带有噪声。当使用较低数值时，结果非常平滑，几乎看不到噪声。然而，在低模型协方差的情况下，误差很大。这是因为滤波器基本上忽略了测量值，因为它认为模型非常精确。你应该在脚本中尝试不同的选项，看看它的表现如何。正如你所见，参数对滤波器学习系统状态的效果有着巨大的影响。

**图 4.7：** 使用较高模型噪声矩阵 `[1e-4 1e-4]` 的卡尔曼滤波器结果。

**振荡器**

r (m) 0

时间 (秒)

0.5

v (m/s) -0.5

时间 (秒)

y (m) -10

时间 (秒)

(m) E 0

r

时间 (秒)

0.1

(m/s)

E

v -0.1

时间 (秒)

![](img/index-109_1.png)

![](img/index-109_2.png)

![](img/index-109_3.png)

**图 4.8：** 使用较低模型噪声矩阵 `[1e-6 1e-6]` 的卡尔曼滤波器结果。噪声较少，但误差很大。

**振荡器**

r (m) 0

时间 (秒)

0.5

v (m/s) -0.5

时间 (秒)

y (m) -10

时间 (秒)

0.5

(m) E

r

-0.5

时间 (秒)

0.1

(m/s)

E

v -0.1

时间 (秒)

## 4.2 使用扩展卡尔曼滤波器进行状态估计

### 4.2.1 问题

我们希望使用带有非线性角度测量的 `EKF` 来跟踪阻尼振荡器。

`EKF` 是为了处理具有非线性动力学模型和/或非线性测量模型的系统而开发的。传统的或线性的滤波器需要线性动力学方程和线性测量模型，即测量值是状态的线性函数。如果模型不是线性的，线性滤波器将无法很好地跟踪状态。

给定一个如下形式的非线性模型：

*xk* = *f*(*x*<sub>*k*−1</sub>, *k* − 1) + *q*<sub>*k*−1</sub>

(4.80)

*yk* = *h*(*xk*, *k*) + *rk*

(4.81)

预测步骤为：

*m*<sub>*k*</sub><sup>−</sup> = *f*(*m*<sub>*k*−1</sub>, *k* − 1)

(4.82)

*P*<sub>*k*</sub><sup>−</sup> = *F*<sub>*x*</sub>(*m*<sub>*k*−1</sub>, *k* − 1) *P*<sub>*k*−1</sub> *F*<sub>*x*</sub>(*m*<sub>*k*−1</sub>, *k* − 1)<sup>T</sup> + *Q*<sub>*k*−1</sub>

(4.83)

![](img/index-110_1.png)

![](img/index-110_2.png)

![](img/index-110_3.png)

![](img/index-110_4.png)

![](img/index-110_5.png)

![](img/index-110_6.png)

*F* 是 *f* 的雅可比矩阵。更新步骤为：

*vk* = *yk* − *h*(*m*<sub>*k*</sub><sup>−</sup>, *k*)

(4.84)

*Sk* = *H*<sub>*x*</sub>(*m*<sub>*k*</sub><sup>−</sup>, *k*) *P*<sub>*k*</sub><sup>−</sup> *H*<sub>*x*</sub>(*m*<sub>*k*</sub><sup>−</sup>, *k*)<sup>T</sup> + *Rk*

(4.85)

*Kk* = *P*<sub>*k*</sub><sup>−</sup> *H*<sub>*x*</sub>(*m*<sub>*k*</sub><sup>−</sup>, *k*)<sup>T</sup> *S*<sub>*k*</sub><sup>−1</sup>

(4.86)

*mk* = *m*<sub>*k*</sub><sup>−</sup> + *Kk* *vk*

(4.87)

# 第4章 卡尔曼滤波器

## 4.2.2 解决方案

*Pk* = *P*<sub>*k*</sub><sup>−</sup> − *Kk* *Sk* *K*<sub>*k*</sub><sup>T</sup> (4.88)

*F*<sub>*x*</sub>(*m*, *k* − 1) 和 *H*<sub>*x*</sub>(*m*, *k*) 是非线性函数 `f` 和 `h` 的雅可比矩阵。雅可比矩阵只是 `F` 和 `H` 的偏导数矩阵。这导致从向量 `F` 和 `H` 得到矩阵。例如，假设我们有 `f(x, y)`，其形式为：

`f = f<sub>x</sub>(x, y)` (4.89)

`f<sub>y</sub>(x, y)`

雅可比矩阵为

`Fk = [ ∂f<sub>x</sub>(xk,yk)/∂x , ∂f<sub>x</sub>(xk,yk)/∂y ; ∂f<sub>y</sub>(xk,yk)/∂x , ∂f<sub>y</sub>(xk,yk)/∂y ]` (4.90)

该矩阵在 `xk`, `yk` 处计算。

雅可比矩阵可以通过解析或数值方法求得。若采用数值方法，则需要围绕 `mk` 的当前值计算雅可比矩阵。在迭代扩展卡尔曼滤波中，更新步骤会在第一次迭代后，使用 `mk` 的更新值进行循环计算。每一步都需要更新 `Hx(m, k)`。

我们将使用与前一个示例中相同的 `KFInitialize` 函数，但现在使用 `'ekf'` 作为输入参数。我们需要为模型动态的导数、测量函数以及测量导数编写函数。这些函数分别在 `RHSOscillatorPartial`、`AngleMeasurement` 和 `AngleMeasurementPartial` 中实现。

我们还需要自定义版本的滤波器来执行预测和更新步骤。

## 4.2.3 工作原理

EKF 需要一个测量函数、一个测量导数函数和一个状态导数函数。状态导数函数用于计算 `a` 矩阵：`xk+1 = akxk` (4.91)

只有当 `ak` 随时间变化时，你才需要使用 EKF。在本问题中，它并不随时间变化。计算 `a` 的函数是 `RHSOscillatorPartial`。它使用了 `CToDZOH`。我们本可以只计算一次 `a`，但使用 `CToDZOH` 使得该函数更具通用性。

![](img/index-111_1.png)

![](img/index-111_2.png)

```matlab
function a = RHSOscillatorPartial( ~, ~, dT, d )
    if( nargin < 1 )
        a = struct('zeta',0.7071,'omega',0.1);
        return
    end
    b = [0;1];
    a = [0 1;d.omega^2 -2*d.zeta*d.omega];
    a = CToDZOH( a, b, dT );
end
```

我们的测量是非线性的（反正切函数），需要围绕每个位置值进行线性化。`AngleMeasurement` 计算测量值，该测量值是非线性但平滑的。

```matlab
y = atan(x(1)/d.baseline);
```

`AngleMeasurementPartial` 计算导数。以下函数计算 `c` 矩阵 `yk = ckxk` (4.92)

通过求取基线角度的反正切函数的导数，可以得到偏导数测量值。注释提醒了你这一事实。

```matlab
% y = atan(x(1)/d.baseline);
u = x(1)/d.baseline;
dH = 1/(1+u^2);
h = [dH 0]/d.baseline;
```

测量函数是平滑的，这很方便。如果存在不连续性，测量偏导数将难以计算。EKF 实现可以处理函数形式的导数或矩阵。对于函数形式，我们使用 `feval` 来调用它们。这可以在 `EKFPredict` 和 `EKFUpdate` 函数中看到。

`EKFPredict` 是 EKF 的状态传播步骤。它使用 `RungeKutta` 对右侧函数进行数值积分。在某些问题中，`RungeKutta` 可能过于复杂，简单的欧拉积分可能更合适。欧拉积分如下：

`xk+1 = xk + ΔT f (x, u, t)` (4.93)

其中 `f (x, u, t)` 是右侧函数，它可以是状态 `x`、时间 `t` 和输入 `u` 的函数。

```matlab
function d = EKFPredict( d )
    % 获取状态转移矩阵
    if( isempty(d.a) )
        a = feval( d.fX, d.m, d.t, d.dT, d.fData );
    else
        ![](img/index-112_1.png)
        ![](img/index-112_2.png)
        a = d.a;
    end
    % 传播均值
    d.m = RungeKutta( d.f, d.t, d.m, d.dT, d.fData );
    % 传播协方差
    d.p = a*d.p*a' + d.q;
end
```

## EKFUpdate

扩展卡尔曼滤波器测量更新步骤。

### 格式

```matlab
d = EKFUpdate( d )
```

### 描述

所有输入均在预测状态之后（参见 `EKFPredict`）。数据字段 `h` 可以包含一个用于计算估计测量值的函数名，或者一个 m 乘 n 的矩阵。如果 `h` 是一个函数名，则必须包含 `hX`，它是一个用于计算 m 乘 n 矩阵作为函数 `h` 的线性化版本的函数。

### 输入

* `d` (.) EKF 数据结构
* `.m` (n,1) 均值
* `.p` (n,n) 协方差
* `.h` (m,n) 可以是矩阵，也可以是函数名或句柄
* `.hX` (*) 用于 `h` 的雅可比矩阵的函数名或句柄
* `.y` (m,1) 测量向量
* `.r` (m,m) 测量协方差向量
* `.hData` (.) 用于 `h` 和 `hX` 函数的数据结构

### 输出

* `d` (.) 更新后的 EKF 数据结构
* `.m` (n,1) 均值
* `.p` (n,n) 协方差
* `.v` (m,1) 残差

```matlab
function d = EKFUpdate( d )
    % 残差
    if( isnumeric( d.h ) )
        h = d.h;
        yE = h*d.m;
        ![](img/index-113_1.png)
        ![](img/index-113_2.png)
    % 否则
    else
        h = feval( d.hX, d.m, d.hData );
        yE = feval( d.h, d.m, d.hData );
    end
    % 残差
    d.v = d.y - yE;
    % 更新步骤
    s = h*d.p*h' + d.r;
    k = d.p*h'/s;
    d.m = d.m + k*d.v;
    d.p = d.p - k*s*k';
end
```

`EKFSim` 脚本利用上述所有函数实现了扩展卡尔曼滤波器，如下列代码所示。这些函数通过 `KFInitialize` 生成的数据结构传递给 EKF。请注意使用 `@` 符号的函数句柄，例如 `@RHSOscillator`。注意 `KFInitialize` 需要 `hX` 和 `fX` 来计算动力学方程和测量方程的偏导数。

```matlab
%% 仿真
xPlot = zeros(5,nSim);
for k = 1:nSim
    % 带随机噪声的角度测量
    y = AngleMeasurement( x, dMeas ) + y1Sigma*randn;
    % 更新卡尔曼滤波器
    dKF.y = y;
    dKF = EKFUpdate(dKF);
    % 绘图存储
    xPlot(:,k) = [x;y;dKF.m-x];
    % 传播（数值积分）状态方程
    x = RungeKutta( @RHSOscillator, 0, x, dT, d );
    % 传播卡尔曼滤波器
    dKF = EKFPredict(dKF);
end
```

图 4.9 显示了结果。误差很小。由于问题动力学是线性的，我们预计它与传统卡尔曼滤波器没有任何差异。

![](img/index-114_1.png)

![](img/index-114_2.png)

![](img/index-114_3.png)

**图 4.9：** 扩展卡尔曼滤波器利用角度测量跟踪振荡器。

**EKF 仿真**

r (m)

时间 (分钟)

**EKF 仿真**

v (m/s) -1

时间 (分钟)

**EKF 仿真**

0.5

y (弧度)

时间 (分钟)

**EKF 仿真**

0.5

(m) E

r -0.5

时间 (分钟)

**EKF 仿真**

0.1

(m/s) E -0.1

v

时间 (分钟)

## 4.3 使用无迹卡尔曼滤波器进行状态估计

### 4.3.1 问题

给定一个非线性角度测量，您希望了解弹簧-阻尼-质量系统的状态。这次我们将使用 UKF。使用 UKF 时，我们直接处理非线性动力学方程和测量方程。我们无需像对 EKF 那样对 `RHSOscillatorPartial` 和 `AngleMeasurementPartial` 进行线性化。UKF 也被称为 sigma *σ* 点滤波器，因为它同时维护距离均值一个 sigma（标准差）的模型。

## 4.3.2 解决方案

我们将创建一个 UKF 作为状态估计器。它将吸收测量值并确定状态。它将基于预先存在的模型自主学习系统的状态。

在以下文本中，我们推导非增广卡尔曼滤波器的方程。这种形式仅允许加性高斯噪声。给定一个如下形式的非线性模型：

*xk* = *f*(*xk−1, k − 1*) + *qk−1* (4.94)

*yk* = *h*(*xk, k*) + *rk* (4.95)

![](img/index-115_1.png)

![](img/index-115_2.png)

![](img/index-115_3.png)

![](img/index-115_4.png)

![](img/index-115_5.png)

![](img/index-115_6.png)

![](img/index-115_7.png)

![](img/index-115_8.png)

![](img/index-115_9.png)

定义权重为：

*W* 0 *m* = *λ* / (*n* + *λ*) (4.96)

*W* 0 *c* = *λ* / (*n* + *λ*) + 1 − *α*2 + *β* (4.97)

*W im* = *λ* / (2(*n* + *λ*)) , *i* = 1 , . . . , 2 *n* (4.98)

*W ic* = *λ* / (2(*n* + *λ*)) , *i* = 1 , . . . , 2 *n* (4.99)

*m* 是均值状态的权重（*m* 代表均值），*c* 是协方差的权重。注意 *W im* = *W ic*。

*λ* = *α*2(*n* + *κ*) − *n* (4.100)

*c* = *λ* + *n* = *α*2(*n* + *κ*) (4.101)

*c* 缩放协方差以计算 sigma 点，即均值周围用于计算待传播附加状态的点分布。*α*、*β* 和 *κ* 是缩放常数。

缩放常数的一般规则是：
• *α* – 状态估计时为 0，参数估计时为 3 减去状态数量。
• *β* – 决定 sigma 点的分布范围。值越小，sigma 点间距越近。
• *κ* – 先验知识的常数。对于高斯过程设为 2。

*n* 是系统的阶数。权重可以写成矩阵形式：

*wm* = [*W* 0 *m · · · W* 2*n* *m*]*T* (4.102)

*W* = (*I −* [*wm · · · wm*]) [*W* 0 *c* . . . . . . *W* 2*n* *c*] (*I −* [*wm · · · wm*])*T* (4.103)

*I* 是 2*n*+1 乘 2*n*+1 的单位矩阵。在方程中，向量 *wm* 被复制了 2*n*+1 次。

*W* 是 2*n*+1 乘 2*n*+1 的矩阵。

预测步骤如下：

*Xk−1* = *mk−1 · · · mk−1* + √(*c*) [*Pk−1 · · · −Pk−1*] (4.104)

*X̂k* = *f*(*Xk−1, k − 1*) (4.105)

*m̂k−* = *X̂k wm* (4.106)

*Pk−* = *X̂k W X̂kT* + *Qk−1* (4.107)

![](img/index-116_1.png)

![](img/index-116_2.png)

![](img/index-116_3.png)

![](img/index-116_4.png)

![](img/index-116_5.png)

其中 *X* 是一个矩阵，其列是状态向量，可能附加了一个 sigma 点向量。更新步骤如下：

*X−k* = [*m̂k− · · · m̂k−*] + √(*c*) [*Pk− · · · −Pk−*] (4.108)

*Y−k* = *h*(*X−k, k*) (4.109)

*μk* = *Y−k wm* (4.110)

*Sk* = *Y−k W*[*Y−k*]*T* + *R* (4.111)

*Ck* = *X−k W*[*Y−k*]*T* (4.112)

*Kk* = *Ck Sk−1* (4.113)

*mk* = *m̂k−* + *Kk*(*yk − μk*) (4.114)

*Pk* = *Pk−* − *Kk Sk KTk* (4.115)

*μk* 是一个测量值矩阵，其中每一列都是经过 sigma 点修改的副本。*Sk* 和 *Ck* 是中间量。*Y−k* 周围的括号仅为了清晰起见。

## 4.3.3 工作原理

权重在 `UKFWeight` 中计算。

```matlab
%% UKFWEIGHT 无迹卡尔曼滤波器权重计算
%% 格式
%
% d = UKFWeight( d )
%
%% 描述
% 无迹卡尔曼滤波器权重。
%
% 权重矩阵用于无迹变换的矩阵形式。
% UKFPredict 和 UKFUpdate 都使用此函数生成的数据结构。
%
% 常数 alpha 决定了 sigma 点围绕 x 的分布范围，
% 通常设置在 10e-4 到 1 之间。beta 包含了 x 分布的
% 先验知识，对于高斯分布取值为 2。kappa 对于状态估计设为 0，
% 对于参数估计设为 3 减去状态数。
%
%% 输入
%
% d
%     (.)
%     包含常数的数据结构
%     .kappa
%         (1,1)
%         状态估计时为 0，参数估计时为 3-#states
%     .m
%         (:,1)
%         平均状态向量
%     .alpha
%         (1,1)
%         决定 sigma 点的分布范围
%     .beta
%         (1,1)
%         先验知识 - 高斯分布时为 2
%
% ![](img/index-117_1.png)
% ![](img/index-117_2.png)
%
%% 输出
%
% d
%     (.)
%     包含常数的数据结构
%     .w
%         (2*n+1,2*n+1)
%         权重矩阵
%     .wM
%         (1,2*n+1)
%         权重数组
%     .wC
%         (2*n+1,1)
%         权重数组
%     .c
%         (1,1)
%         缩放常数
%     .lambda
%         (1,1)
%         缩放常数
%

function d = UKFWeight( d )
% 计算基本常数
n = length(d.m);
a2 = d.alpha^2;
d.lambda = a2*(n + d.kappa) - n;
nL = n + d.lambda;
wMP = 0.5*ones(1,2*n)/nL;
d.wM = [d.lambda/nL wMP]';
d.wC = [d.lambda/nL+(1-a2+d.beta) wMP];
d.c = sqrt(nL);
% 构建矩阵
f = eye(2*n+1) - repmat(d.wM,1,2*n+1);
d.w = f*diag(d.wC)*f';
```

预测 UKF 步骤显示在以下 `UKFPredict` 的摘录中。

```matlab
%% UKFPREDICT 无迹卡尔曼滤波器测量更新步骤
function d = UKFPredict( d )
pS = chol(d.p)';
nS = length(d.m);
nSig = 2*nS + 1;
mM = repmat(d.m,1,nSig);
x = mM + d.c*[zeros(nS,1) pS -pS];
xH = Propagate( x, d );
d.m = xH*d.wM;
d.p = xH*d.w*xH' + d.q;
d.p = 0.5*(d.p + d.p'); % 强制对称

%% 传播每个 sigma 点状态向量
function x = Propagate( x, d )

![](img/index-118_1.png)
![](img/index-118_2.png)

for j = 1:size(x,2)
    x(:,j) = RungeKutta( d.f, d.t, x(:,j), d.dT, d.fData );
end
```

`UKFPredict` 使用 `RungeKutta` 进行预测，这是通过数值积分完成的。

实际上，我们正在运行模型的模拟，并通过下一个函数 `UKFUpdate` 来修正结果。这触及了卡尔曼滤波器的核心。它只是对您的模型进行模拟，并附加一个测量修正步骤。对于传统的线性卡尔曼滤波器，我们使用线性离散时间模型。

更新 UKF 步骤显示在以下列表中。更新将状态传播一个时间步长。

%% UKFUPDATE 无迹卡尔曼滤波器测量更新步骤。

```
function d = UKFUpdate( d )

% 获取 sigma 点

pS
= d.chol(d.p)';

nS
= length(d.m);

nSig
= 2*nS + 1;

mM
= repmat(d.m,1,nSig);

x
= mM + [zeros(nS,1) pS -pS];

[y, r]
= Measurement( x, d );

mu
= y*d.wM;

s
= y*d.w*y' + r;

c
= x*d.w*y';

k
= c/s;

d.v
= d.y - mu;

d.m
= d.m + k*d.v;

d.p
= d.p - k*s*k';
```

%% 基于 sigma 点的量测估计

```
function [y, r] = Measurement( x, d )

nSigma = size(x,2);

% 创建数组

lR
= length(d.r);

y
= zeros(lR,nSigma);

r
= d.r;

for j = 1:nSigma

f
= feval(d.hFun, x(:,j), d.hData );

iR
= 1:lR;

y(iR,j)
= f;

end
```

![](img/index-119_1.png)

![](img/index-119_2.png)

sigma 点通过 `chol` 生成。`chol` 是 Cholesky 分解，用于生成矩阵的近似平方根。真正的矩阵平方根计算成本更高，且结果并不足以证明其额外开销是合理的。其思想是将 sigma 点分布在均值周围，而 `chol` 能很好地完成这一任务。下面是一个比较两种方法的示例：

```
>> z = [1 0.2;0.2 2]

z =

    1.0000    0.2000
    0.2000    2.0000

>> b = chol(z)

b =

    1.0000    0.2000
         0    1.4000

>> b*b

ans =

    1.0000    0.4800
    0.4800    1.9600

>> q = sqrtm(z)

q =

    0.9965    0.0830
    0.0830    1.4118

>> q*q

ans =

    1.0000    0.2000
    0.2000    2.0000
```

平方根函数确实生成了一个平方根！`b*b` 的对角线元素与 `z` 接近，而这正是关键所在。

用于测试 UKF 的脚本 `UKFSim` 如下所示。如前所述，我们不需要像对卡尔曼滤波器和 EKF 那样将连续时间模型转换为离散时间模型。

相反，我们将微分方程的右侧传递给滤波器。你还必须传递一个量测模型，该模型可以是非线性的。你需要在仿真循环中添加 `UKFUpdate` 和 `UKFPredict` 函数调用。我们首先初始化所有参数。`KFInitialize` 在 `'ukf'` 之后接受参数对，以初始化滤波器。其余部分为仿真循环和绘图。初始化需要在调用 `KFInitialize` 后计算权重矩阵。

%% 初始化

```
dKF
= KFInitialize( 'ukf','m',xE,'f',@RHSOscillator,'fData',d,...
'r',y1Sigmaˆ2,'q',q,'p',p,...
'hFun',@AngleMeasurement,'hData',dMeas,'dT',dT);

dKF
= UKFWeight( dKF );
```

这里展示仿真循环：

%% 仿真

```
xPlot = zeros(5,nSim);
```

![](img/index-120_1.png)

![](img/index-120_2.png)

![](img/index-120_3.png)

```
for k = 1:nSim

% 量测
y = AngleMeasurement( x, dMeas ) + y1Sigma*randn;

% 更新卡尔曼滤波器
dKF.y = y;
dKF
= UKFUpdate(dKF);

% 存储绘图数据
xPlot(:,k)
= [x;y;dKF.m-x];

% 传播（数值积分）状态方程
x = RungeKutta( @RHSOscillator, 0, x, dT, d );

% 传播卡尔曼滤波器
dKF = UKFPredict(dKF);

end
```

结果如图 4.10 所示。误差 Δ *rE* 和 Δ *vE* 仅为噪声。量测覆盖了较大的角度范围，这会使线性近似出现问题。

**图 4.10：** 用于状态估计的无迹卡尔曼滤波结果。

**UKF 仿真**

![](img/index-121_1.png)

![](img/index-121_2.png)

![](img/index-121_3.png)

![](img/index-121_4.png)

### 4.4 使用 UKF 进行参数估计

#### 4.4.1 问题

给定一个非线性角度量测，你希望学习弹簧-阻尼-质量系统的参数。UKF 可以配置为完成此任务。

#### 4.4.2 解决方案

解决方案是创建一个配置为参数估计器的 UKF。它将吸收量测数据并确定无阻尼固有频率。它将基于预先存在的模型自主学习系统特性。我们开发的版本需要状态估计，该估计可以通过并行运行的 UKF 生成，如上一节所述。

#### 4.4.3 工作原理

用参数的期望值 *η* 初始化参数滤波器

```
\hat{\eta}(t_0) = E\{\hat{\eta}_0\}
```

以及参数的协方差

```
P_{\eta_0} = E\{(\eta(t_0) - \hat{\eta}_0)(\eta(t_0) - \hat{\eta}_0)^T\}
```

更新序列首先将参数模型不确定性 `Q` 添加到协方差 `P` 中，

```
P = P + Q
```

`Q` 是针对参数，而非状态。随后计算 sigma 点。这些点是通过将协方差矩阵的平方根加到当前参数估计值上得到的。

```
\eta_\sigma = \hat{\eta} \quad \hat{\eta} + \gamma P \quad \hat{\eta} - \gamma P
```

`gamma` 是一个决定 sigma 点分布范围的因子。我们使用 `chol` 函数来计算平方根。

如果有 `L` 个参数，那么 `P` 矩阵是 `L x L` 的，因此这个数组的维度将是 `L x (2L + 1)`。

状态方程的形式如下：

```
\dot{x} = f(x, u, t)
```

测量方程的形式如下：

```
y = h(x, u, t)
```

`x` 是系统的先前状态，由状态估计器或其他过程确定。`u` 是一个结构体，包含系统中所有其他未被估计的输入。`eta` 是一个正在被估计的参数向量，`t` 是时间。`y` 是测量向量。这是一种双重估计方法，因为我们并非同时估计 `x` 和 `eta`。

![](img/index-122_1.png)

![](img/index-122_2.png)

用于测试 UKF 参数估计的脚本 `UKFPSim` 如下所示。为了简化脚本，我们没有进行 UKF 状态估计。通常情况下，你会并行运行 UKF。我们首先初始化所有参数。`KFInitialize` 接受参数对来初始化滤波器。其余部分是仿真循环和绘图。请注意，这里只有更新调用，因为参数与状态不同，不会传播。

```
for k = 1:nSim

% 更新卡尔曼滤波器参数估计
dKF.x = x;

% 绘图存储
xPlot(:,k) = [y;x;dKF.eta;dKF.p];

% 传播（数值积分）状态方程
x = RungeKutta( @RHSOscillator, 0, x, dT, d );

% 引入测量值
y = LinearMeasurement( x ) + y1Sigma*randn;
dKF.y = y;
dKF = UKFPUpdate(dKF);

end
```

UKF 参数更新函数如下所示。它使用 UKF 生成的状态估计值。如前所述，我们使用的是仿真生成的状态精确值。此函数需要一个专门的右侧函数，该函数使用参数估计值 `d.eta`。为此，我们修改了 `RHSOscillator` 并编写了 `RHSOscillatorUKF`。

# UKF参数估计函数

## UKFPUpdate函数

```
function d = UKFPUpdate( d )

d.wA = zeros(d.L,d.n);

D = zeros(d.lY,d.n);

yD = zeros(d.lY,1);

% 更新协方差

d.p = d.p + d.q;

% 计算 sigma 点

d = SigmaPoints( d );

% 我们计算状态，然后计算测量值

% 针对参数 +/- 1 sigma

for k = 1:d.n

d.fData.eta = d.wA(:,k);

x = RungeKutta( d.f, d.t, d.x, d.dT, d.fData );

D(:,k) = feval( d.hFun, x, d.hData );

yD = yD + d.wM(k)*D(:,k);

end
```

![](img/index-123_1.png)

![](img/index-123_2.png)

```
pWD = zeros(d.L,d.lY);

pDD = d.r;

for k = 1:d.n

wD = D(:,k) - yD;

pDD = pDD + d.wC(k)*(wD*wD');

pWD = pWD + d.wC(k)*(d.wA(:,k) - d.eta)*wD';

end

pDD = 0.5*(pDD + pDD');
```

### 引入测量值

```
K = pWD/pDD;

dY = d.y - yD;

d.eta = d.eta + K*dY;

d.p = d.p - K*pDD*K';

d.p = 0.5*(d.p + d.p'); % 强制对称
```

## SigmaPoints函数

```
function d = SigmaPoints( d )

n = 2:(d.L+1);

m = (d.L+2):(2*d.L + 1);

etaM = repmat(d.eta, length(d.eta));

sqrtP = chol(d.p);

d.wA(:,1) = d.eta;

d.wA(:,n) = etaM + d.gamma*sqrtP;

d.wA(:,m) = etaM - d.gamma*sqrtP;
```

## UKFPWeight函数

它还有自己的权重初始化函数 `UKFPWeight.m`。权重矩阵用于无迹变换的矩阵形式。常数 `alpha` 决定了 `sigma` 点围绕参数向量的分布范围，通常设置在 `10e-4` 到 `1` 之间。`beta` 用于整合参数向量分布的先验知识，对于高斯分布取值为 `2`。`kappa` 在状态估计时设为 `0`，在参数估计时设为状态数量的 `3` 倍。

```
function d = UKFPWeight( d )

d.L = length(d.eta);

d.lambda = d.alpha^2*(d.L + d.kappa) - d.L;

d.gamma = sqrt(d.L + d.lambda);

d.wC(1) = d.lambda/(d.L + d.lambda) + (1 - d.alpha^2 + d.beta);

d.wM(1) = d.lambda/(d.L + d.lambda);

d.n = 2*d.L + 1;

for k = 2:d.n

d.wC(k) = 1/(2*(d.L + d.lambda));

d.wM(k) = d.wC(k);

end
```

![](img/index-124_1.png)

![](img/index-124_2.png)

![](img/index-124_3.png)

```
d.wA = zeros(d.L, d.n);

y = feval( d.hFun, d.x, d.hData );

d.lY = length(y);

d.D = zeros(d.lY, d.n);
```

## 模型与测量函数

`RHSOscillatorUKF` 是 UKF 使用的振荡器模型。它的输入格式与 `RHSOscillator` 不同。只有一行代码。

```
xDot = [x(2); d.a - 2*d.zeta*d.eta*x(2) - d.eta^2*x(1)];
```

`LinearMeasurement` 是一个用于演示目的的简单测量函数。UKF 可以使用任意复杂的测量函数。

## 仿真结果

无阻尼振荡器的仿真结果如图 4.11 所示。滤波器能够快速估计无阻尼固有频率。然而，结果存在噪声。您可以通过修改脚本中的数值来探索此脚本。

**图 4.11：** 无迹卡尔曼参数估计结果。`p` 是协方差。它表明我们的参数估计已经收敛。

**UKF 参数估计**

- `y` (弧度) -1, 0.5, 1.5, 时间 (秒)
- `r` (米) -1, 0.5, 1.5, 时间 (秒)
- `v` (米/秒) -2, 0.5, 1.5, 时间 (秒)
- (弧度/秒) 1, 0.5, 1.5, 时间 (秒)
- `p` 0.4, 0.2, 0.5, 1.5, 时间 (秒)

![](img/index-125_1.png)

![](img/index-125_2.png)

![](img/index-125_3.png)

![](img/index-125_4.png)

![](img/index-125_5.png)

![](img/index-125_6.png)

![](img/index-125_7.png)

![](img/index-125_8.png)

![](img/index-125_9.png)

![](img/index-125_10.png)

![](img/index-125_11.png)

![](img/index-125_12.png)

![](img/index-125_13.png)

![](img/index-125_14.png)

![](img/index-125_15.png)

![](img/index-125_16.png)

![](img/index-125_17.png)

![](img/index-125_18.png)

![](img/index-125_19.png)

![](img/index-125_20.png)

![](img/index-125_21.png)

![](img/index-125_22.png)

![](img/index-125_23.png)

![](img/index-125_24.png)

![](img/index-125_25.png)

![](img/index-125_26.png)

![](img/index-125_27.png)

![](img/index-125_28.png)

![](img/index-125_29.png)

![](img/index-125_30.png)

![](img/index-125_31.png)

![](img/index-125_32.png)

![](img/index-125_33.png)

![](img/index-125_34.png)

![](img/index-125_35.png)

![](img/index-125_36.png)

![](img/index-125_37.png)

![](img/index-125_38.png)

![](img/index-125_39.png)

![](img/index-125_40.png)

![](img/index-125_41.png)

![](img/index-125_42.png)

![](img/index-125_43.png)

![](img/index-125_44.png)

![](img/index-125_45.png)

![](img/index-125_46.png)

![](img/index-125_47.png)

![](img/index-125_48.png)

![](img/index-125_49.png)

![](img/index-125_50.png)

![](img/index-125_51.png)

![](img/index-125_52.png)

![](img/index-125_53.png)

![](img/index-125_54.png)

![](img/index-125_55.png)

![](img/index-125_56.png)

![](img/index-125_57.png)

![](img/index-125_58.png)

![](img/index-125_59.png)

![](img/index-125_60.png)

![](img/index-125_61.png)

![](img/index-125_62.png)

![](img/index-125_63.png)

![](img/index-125_64.png)

![](img/index-125_65.png)

![](img/index-125_66.png)

![](img/index-125_67.png)

![](img/index-125_68.png)
![](img/index-125_69.png)
![](img/index-125_70.png)
![](img/index-125_71.png)
![](img/index-125_72.png)
![](img/index-125_73.png)
![](img/index-125_74.png)
![](img/index-125_75.png)
![](img/index-125_76.png)
![](img/index-125_77.png)
![](img/index-125_78.png)
![](img/index-125_79.png)
![](img/index-125_80.png)
![](img/index-125_81.png)
![](img/index-125_82.png)
![](img/index-125_83.png)
![](img/index-125_84.png)
![](img/index-125_85.png)
![](img/index-125_86.png)
![](img/index-125_87.png)
![](img/index-125_88.png)
![](img/index-125_89.png)
![](img/index-125_90.png)
![](img/index-125_91.png)
![](img/index-125_92.png)
![](img/index-125_93.png)
![](img/index-125_94.png)
![](img/index-125_95.png)
![](img/index-125_96.png)
![](img/index-125_97.png)
![](img/index-125_98.png)
![](img/index-125_99.png)
![](img/index-125_100.png)
![](img/index-125_101.png)
![](img/index-125_102.png)
![](img/index-125_103.png)
![](img/index-125_104.png)
![](img/index-125_105.png)
![](img/index-125_106.png)
![](img/index-125_107.png)

## 4.5 总结

本章演示了使用卡尔曼滤波器进行学习。在本例中，学习是指对阻尼振荡器的状态和参数进行估计。我们研究了传统卡尔曼滤波器和无迹卡尔曼滤波器，并探讨了后者的参数学习版本。

所有示例均使用阻尼振荡器完成。表 4.1 列出了随附代码中包含的函数和脚本。

### 表 4.1：章节代码清单

| 文件 | 描述 |
| :--- | :--- |
| `AngleMeasurement` | 质量块的角测量。 |
| `AngleMeasurementPartial` | 角测量导数。 |
| `LinearMeasurement` | 质量的位置测量。 |
| `OscillatorSim` | 阻尼振荡器的仿真。 |
| `OscillatorDampingRatioSim` | 不同阻尼比下的阻尼振荡器仿真。 |
| `RHSOscillator` | 阻尼振荡器的动力学模型。 |
| `RHSOscillatorPartial` | 阻尼振荡器的导数模型。 |
| `RungeKutta` | 四阶龙格-库塔积分器。 |
| `PlotSet` | 从数据集创建二维图。 |
| `TimeLabel` | 生成时间标签和缩放后的时间向量。 |
| `Gaussian` | 绘制高斯分布。 |
| `KFInitialize` | 初始化卡尔曼滤波器。 |
| `KFSim` | 传统卡尔曼滤波器的演示。 |
| `KFPredict` | 传统卡尔曼滤波器的预测步骤。 |
| `KFUpdate` | 传统卡尔曼滤波器的更新步骤。 |
| `EKFPredict` | 扩展卡尔曼滤波器的预测步骤。 |
| `EKFUpdate` | 扩展卡尔曼滤波器的更新步骤。 |
| `UKFPredict` | 无迹卡尔曼滤波器的预测步骤。 |
| `UKFUpdate` | 无迹卡尔曼滤波器的更新步骤。 |
| `UKFPUpdate` | 无迹卡尔曼滤波器参数更新的更新步骤。 |
| `UKFSim` | 无迹卡尔曼滤波器的演示。 |
| `UKFPSim` | 无迹卡尔曼滤波器参数估计的演示。 |
| `UKFWeights` | 生成无迹卡尔曼滤波器的权重。 |
| `UKFPWeights` | 生成无迹卡尔曼滤波器参数估计器的权重。 |
| `RHSOscillatorUKF` | 用于无迹卡尔曼滤波器参数估计的阻尼振荡器动力学模型。 |

![](img/index-126_1.png)
![](img/index-126_2.png)
![](img/index-126_3.png)
![](img/index-126_4.png)
![](img/index-126_5.png)