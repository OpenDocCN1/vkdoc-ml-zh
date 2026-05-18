# 第 6 章 **模糊逻辑**

模糊逻辑 [[26]](#index_split_007.html#p350) 是控制系统设计的一种替代方法。模糊逻辑在集合论的框架内运作，更擅长处理模糊性。例如，可以为传感器定义三个集合：硬故障、软故障和无故障。这三个集合可能重叠，并且在任何给定时刻，传感器在每个集合中可能都有一定的隶属度。每个集合中的隶属度可用于确定要采取的行动。而算法方法则必须为传感器状态分配一个数字。这可能存在问题，并且不一定能代表系统的实际状态。实际上，你是在应用一定程度的模糊性。

自主

学习

机器

控制

学习

智能

当你因疼痛去看医生时，医生通常会尝试让你将一个模糊的概念——疼痛——转换为 0 到 10 之间的数字。由于疼痛是个人感受，你的印象也不精确，你实际上是在给一个模糊的概念或信念赋予一个硬性数字。正如你可能经历过的，这并不十分有效或有用。

调查问卷也是如此。例如，你会被要求对餐厅的服务从 0 到 5 进行评分。然后你再用同样的标准评价其他一堆东西。这使得评论能够为你对餐厅的整体印象得出一个数字。最终得出的 4.8 分真的有意义吗？Netflix 已经放弃了对你看过的电影进行数字评分，转而采用点赞和点踩。似乎他们认为二元决策（实际上是两个集合）比数字更能说明问题。

NASA 和美国国防部喜欢使用从 1 到 9 的技术就绪水平来确定你的工作在就绪程度方面处于哪个阶段。9 表示一项技术已经在目标系统中运行。

© Michael Paluszek 和 Stephanie Thomas 2019

M. Paluszek 和 S. Thomas， *MATLAB 机器学习秘籍*，

[`doi.org/10.1007/978-1-4842-3916-2_6`](https://doi.org/10.1007/978-1-4842-3916-2_6)

![](img/index-152_1.png)

![](img/index-152_2.png)



1 仅仅是一个想法。对于任何稍微复杂的事物，其他所有级别都是模糊的。即使给一项技术评为 9 级，信息量也极其有限。M-16 步枪曾被部署到越南。它经常卡壳。按照 TRL 标准，它是 9 级，但 9 级并不能说明它运行得有多好。同样，当你阅读士兵和海军陆战队员的印象时，步枪的就绪程度最好用模糊信念来表示。

本章将向你展示如何为挡风玻璃雨刮器实现一个模糊逻辑控制系统。与其他章节不同，我们将使用语言概念，而不是硬性数字。当然，当你设置雨刮器电机速度时，你需要选择一个数字（对输出进行去模糊化），但所有中间步骤都采用模糊逻辑。

**6.1**

**构建模糊逻辑系统**

**6.1.1 问题**

我们希望有一个工具来构建模糊逻辑控制器。

**6.1.2 解决方案**

构建一个 MATLAB 函数，该函数接受定义模糊控制器所需一切内容的参数对。

**6.1.3 工作原理**

要创建一个模糊系统，你必须创建输入、输出和规则。你也可以为模糊推理的某些部分选择方法。模糊推理引擎有三个步骤：
1. 模糊化
2. 触发
3. 去模糊化

模糊系统数据存储在一个 MATLAB 数据结构中。该结构具有以下字段：

• 输入 *{*: *}*
• 输出 *{*: *}*
• 规则 *{*: *}*
• 蕴含
• 聚合
• 去模糊化

前三个字段是结构体的元胞数组。规则和模糊集有单独的结构体，如下所述。最后三个字段是包含所需函数名称的字符串。

![](img/index-153_1.png)

![](img/index-153_2.png)

![](img/index-153_3.png)

![](img/index-153_4.png)

![](img/index-153_5.png)

![](img/index-153_6.png)

![](img/index-153_7.png)

![](img/index-153_8.png)

![](img/index-153_9.png)

![](img/index-153_10.png)

![](img/index-153_11.png)

![](img/index-153_12.png)

![](img/index-153_13.png)

![](img/index-153_14.png)

![](img/index-153_15.png)

![](img/index-153_16.png)

![](img/index-153_17.png)

![](img/index-153_18.png)

![](img/index-153_19.png)

![](img/index-153_20.png)

![](img/index-153_21.png)

![](img/index-153_22.png)

![](img/index-153_23.png)



模糊集结构体具有以下字段：

• name
• range(2) （包含最小值和最大值的两元素数组）
• comp *{*: *}* （标签字符串的元胞数组）
• type *{*: *}* （隶属函数名称的元胞数组）
• params *{*: *}* （参数向量的元胞数组）

模糊规则结构体具有以下字段：

• input(:) （输入分量编号的向量）
• output(:) （输出向量）
• operator *{*: *}* （运算符字符串的元胞数组）

需要组织的数据很多。我们通过函数 `BuildFuzzySystem` 来完成。以下代码片段展示了它如何使用参数对将数据分配给数据结构。

```matlab
d = load('SmartWipers');
j = 1;
for k = 1:2:length(varargin)
    switch (lower(varargin{k}))
        case 'id'
            j = varargin{k+1};
        case 'input comp'
            d.input(j).comp = varargin{k+1};
        case 'input type'
            d.input(j).type = varargin{k+1};
        case 'input name'
            d.input(j).name = varargin{k+1};
        case 'input params'
```

此代码继续处理其他情况。如果你不输入任何内容，`BuildFuzzySystem` 会加载如上所示的智能雨刮器演示，并原样返回。例如，如果你只输入一个输入类型，你会得到：

```matlab
SmartWipers = BuildFuzzySystem(...
    'id',1,...
    'input comp',{'Dry'
                  'Drizzle'
                  'Wet'} ,...
    'input type', {'Trapezoid'
                   'Triangle'
                   'Trapezoid'} ,...
    'input params',{[0 0 10 50]
                    [40 50]
                    [50 90 101 101]},...
```

![](img/index-154_1.png)

![](img/index-154_2.png)

![](img/index-154_3.png)

![](img/index-154_4.png)

![](img/index-154_5.png)



```matlab
    'input range',[0 100])
SmartWipers =
struct with fields:
    SmartWipers: [1x1 struct]
    input: [1x1 struct]
```

在此上下文中，模糊集由一组定义变量的语言类别或分量组成。例如，如果变量是“年龄”，分量可能是“年轻”、“中年”和“老年”。每个模糊集都有一个有效范围，例如，“年龄”的一个合适范围可能是 0 到 100。每个分量都有一个隶属函数，描述该集合范围内的某个值属于每个分量的程度。例如，一个 50 岁的人很少被描述为“年轻”，但可能被描述为“中年”或“老年”，这取决于被问的人。

要构建一个模糊集，你必须将其划分为多个分量。提供了以下隶属函数：

1. 三角形
2. 梯形
3. 高斯型
4. 广义钟形
5. S 形

隶属函数的值限制在 0 到 1 之间。这些隶属函数如图 6.1 所示。

三角形隶属函数需要两个参数：三角形的中心以及所需三角形底边的一半宽度。三角形隶属函数仅限于对称三角形。

梯形隶属函数需要四个参数：最左边的点、平台起始点、平台结束点和最右边的点。

高斯隶属函数是一个连续函数，有两个参数：钟形的中心和钟形的宽度（标准差）。高斯隶属函数是对称的。



通用钟形函数也是连续且对称的，但它有三个参数，允许顶部平坦，使其类似于平滑后的梯形。它需要三个参数：钟形中心、在 y = 0.5 点处的钟形宽度，以及在 y = 0.5 点处的函数斜率。

正如钟形函数类似于平滑后的梯形，S 形隶属函数则类似于平滑后的阶跃函数。它需要两个参数：y = 0.5 的点以及函数的斜率。当斜率趋近于无穷大时，S 形函数趋近于阶跃函数。

![](img/index-155_1.png)

![](img/index-155_2.png)

![](img/index-155_3.png)



**图 6.1：**隶属函数。

模糊规则是 if-then 语句。例如，一个空调规则可能是：如果室温高，那么鼓风机档位高。在这种情况下，“室温”是输入模糊集，“高”是该规则中它的分量，“鼓风机档位”是输出模糊集，“高”是它被选中的分量。

**6.2**

**实现模糊逻辑**

**6.2.1 问题**

我们想要实现模糊逻辑。

**6.2.2 解决方案**

构建一个模糊推理引擎。

**6.2.3 工作原理**

让我们重复模糊推理的三个步骤，在去模糊化中增加两个步骤：
1. 模糊化
2. 触发
3. 去模糊化
   (a) 蕴含
   (b) 聚合

![](img/index-156_1.png)

![](img/index-156_2.png)

![](img/index-156_3.png)

![](img/index-156_4.png)

![](img/index-156_5.png)

![](img/index-156_6.png)

![](img/index-156_7.png)

![](img/index-156_8.png)

![](img/index-156_9.png)

![](img/index-156_10.png)

![](img/index-156_11.png)

![](img/index-156_12.png)

![](img/index-156_13.png)

![](img/index-156_14.png)

![](img/index-156_15.png)

![](img/index-156_16.png)

![](img/index-156_17.png)

![](img/index-156_18.png)

![](img/index-156_19.png)

![](img/index-156_20.png)

![](img/index-156_21.png)

![](img/index-156_22.png)

![](img/index-156_23.png)

![](img/index-156_24.png)

![](img/index-156_25.png)

![](img/index-156_26.png)

![](img/index-156_27.png)

![](img/index-156_28.png)

![](img/index-156_29.png)

![](img/index-156_30.png)

![](img/index-156_31.png)

![](img/index-156_32.png)

![](img/index-156_33.png)

![](img/index-156_34.png)

![](img/index-156_35.png)

![](img/index-156_36.png)

![](img/index-156_37.png)

![](img/index-156_38.png)

![](img/index-156_39.png)

![](img/index-156_40.png)

![](img/index-156_41.png)

![](img/index-156_42.png)

![](img/index-156_43.png)

![](img/index-156_44.png)

![](img/index-156_45.png)

![](img/index-156_46.png)

![](img/index-156_47.png)



控制流程位于主函数中，名为 `FuzzyInference`。它只是按顺序调用 `Fuzzify`、`Fire` 和 `Defuzzify`。如果输入不合理，它会调用 `warndlg`。

**function** y = FuzzyInference( x, system )

**if length**(x) == **length**( system.input )

fuzzyX = Fuzzify( x, system.input );

strength = Fire( fuzzyX, system.rules );

y = Defuzzify( strength, system );

**else**

warndlg({'The length of x must be equal to the',...

'number of input sets in the system.'})

**end**

你会注意到使用了 `eval` 来评估作为输入存储为字符串的函数名。你也可以存储指针并做同样的事情。例如，对于函数：**function** y = MyFun(x)

y = x;

`eval` 作用于字符串。本质上，它将 MATLAB 解析器应用于你自己的文本。你可以根据需要使字符串变得复杂，尽管这会牺牲可读性。你还可以做一些事情，比如制作自修改代码。

>> **eval**(['MyFun(', **sprintf**('%f',2),')'])

**ans** =

使用函数指针更简洁，处理时间也更少。

>> **feval**(@MyFun,2)

**ans** =

`feval` 作用于函数指针，通常速度更快。

**提示**

尽可能使用 `feval` 代替 `eval`。

模糊化子函数代码如下所示。它将数据放入各个输入隶属集。一个输入可能属于多个集合。

**function** fuzzyX = Fuzzify( x, sets )

m = **length**(sets);

fuzzyX = **cell**(1,m);

**for** i = 1:m

n = **length**(sets(i).comp);

range = sets(i).range(:);

**if** range(1) <= x(i) <= range(2)

![](img/index-157_1.png)

![](img/index-157_2.png)



**for** j = 1:n



fuzzyX{i}(j) = **eval**([sets(i). **type**{j} 'MF(x(i),[' **num2str**(sets(i).params{j}) '])']);

**end**

**else**

fuzzyX{i}(1:n) = **zeros**(1,n);

**end**

**end**

以下代码触发了模糊规则。该代码应用了“模糊与”或“模糊或”。

“模糊与”是一组隶属度值中的最小值。“模糊或”是一组隶属度值中的最大值。假设我们有一个向量 [1 0 1 0]。最大值是 1，最小值是 0。

>> 1 && 0 && 1 && 0

**ans =**

logical

>> 1 || 0 || 1 || 0

**ans =**

logical

这对应于模糊逻辑中的“与”和“或”。

下一个代码片段展示了 FuzzyInference 中的 Fire 子函数。

**function** strength = Fire( FuzzyX, rules )

m = **length**( rules );

n = **length**( FuzzyX );

strength = **zeros**(1,m);

**for** i = 1:m

method = rules(i).operator;

**for** j = 1:n

comp = rules(i). **input**(j);

**if** comp ˜= 0

dom(j) = FuzzyX{j}(comp);

**else**

dom(j) = inf;

**end**

**end**

strength(i) = **eval**([method '(dom(find(dom<=1)))']);

**end**

![](img/index-158_1.png)

![](img/index-158_2.png)

![](img/index-158_3.png)

![](img/index-158_4.png)

![](img/index-158_5.png)



最后，我们对结果进行去模糊化。此函数首先使用蕴含函数来确定隶属度。然后使用聚合函数（本例中为 max）聚合输出。

**function** result = Defuzzify( strength, system )

rules = system.rules;

output = system.output;

m = **length**( output );

n = **length**( rules );

imp = system.implicate;

agg = system.aggregate;

defuzz = system.defuzzify;

result = **zeros**(1,m);

**for** i = 1:m

range = output(i).range(:);

x = **linspace**( range(1),range(2),200 );

**for** j = 1:n

comp = rules(j).output(i);

**if**( comp ˜= 0 )

mf = [output(i). **type**{comp} 'MF'];

params = output(i).params{comp};

mem(j,:) = **eval**([ imp 'IMP(' mf '(x,params),strength(j))']);

**else**

mem(j,:) = **zeros**(**size**(x));

**end**

**end**

aggregate = **eval**([ agg '(mem)' ]);

result(i) = **eval**([ defuzz 'DF(aggregate, x)']);

**end**

**6.3**

**演示模糊逻辑**

**6.3.1 问题**

我们希望有一个控制系统，能够根据降雨量选择雨刮器的速度和间隔。

**6.3.2 解决方案**

使用我们开发的工具构建一个模糊逻辑控制系统。

![](img/index-159_1.png)

![](img/index-159_2.png)

![](img/index-159_3.png)

![](img/index-159_4.png)

![](img/index-159_5.png)

![](img/index-159_6.png)

![](img/index-159_7.png)

![](img/index-159_8.png)

![](img/index-159_9.png)

![](img/index-159_10.png)

![](img/index-159_11.png)

![](img/index-159_12.png)

![](img/index-159_13.png)

![](img/index-159_14.png)

![](img/index-159_15.png)

![](img/index-159_16.png)

![](img/index-159_17.png)

![](img/index-159_18.png)

![](img/index-159_19.png)

![](img/index-159_20.png)

![](img/index-159_21.png)

![](img/index-159_22.png)

![](img/index-159_23.png)

![](img/index-159_24.png)

![](img/index-159_25.png)

![](img/index-159_26.png)

![](img/index-159_27.png)

![](img/index-159_28.png)

![](img/index-159_29.png)

![](img/index-159_30.png)

![](img/index-159_31.png)

![](img/index-159_32.png)

![](img/index-159_33.png)

![](img/index-159_34.png)

![](img/index-159_35.png)

![](img/index-159_36.png)

![](img/index-159_37.png)

![](img/index-159_38.png)

![](img/index-159_39.png)

![](img/index-159_40.png)

![](img/index-159_41.png)

![](img/index-159_42.png)

![](img/index-159_43.png)

![](img/index-159_44.png)

![](img/index-159_45.png)

![](img/index-159_46.png)

![](img/index-159_47.png)

![](img/index-159_48.png)

![](img/index-159_49.png)

![](img/index-159_50.png)

![](img/index-159_51.png)

![](img/index-159_52.png)

![](img/index-159_53.png)

![](img/index-159_54.png)

![](img/index-159_55.png)

![](img/index-159_56.png)

![](img/index-159_57.png)

![](img/index-159_58.png)

![](img/index-159_59.png)

![](img/index-159_60.png)

![](img/index-159_61.png)

![](img/index-159_62.png)

![](img/index-159_63.png)

![](img/index-159_64.png)

![](img/index-159_65.png)



**6.3.3 工作原理**

要调用模糊系统，请使用函数 y = FuzzyInference( x, system )。

脚本 SmartWipersDemo 实现了降雨量演示。我们只展示调用推理引擎的代码。

如上所述，模糊系统通过 SmartWipers = BuildFuzzySystem() 加载。

% 在 2 个输入中生成规则间隔的数组

x = **linspace**(SmartWipers.input(1).range(1),SmartWipers.input(1).range (2),n);



y = **linspace**(SmartWipers.input(2).range(1),SmartWipers.input(2).range (2),n);

PlotSet(1:n,[x;y],'x label','输入','y label',{'湿度','强度'},...

'figure title','输入','Plot Title',{'湿度','强度'})

h = **waitbar**(0,'智能雨刷演示：绘制规则库'); z1 = **zeros**(n,n);

z2 = **zeros**(n,n);

**for** k = 1:n

**for** j = 1:n

temp = FuzzyInference([x(k),y(j)], SmartWipers);

z1(k,j) = temp(1);

z2(k,j) = temp(2);

**end**

**waitbar**(k/n)

**end**

**close**(h);

NewFigure('基于模糊逻辑的雨刷速度')

智能雨刷是一个用于自动挡风玻璃雨刮器的控制系统[[7].](#index_split_007.html#p349) 首先，该演示将绘制输入和输出的模糊变量。对绘制的每一组清晰输入执行模糊推理。图 6.2 显示了模糊逻辑系统的输入。图 6.3 显示了输出。

在模糊逻辑系统中测试的输入如图 6.4 所示。

图 6.5 给出了曲面图，以显示输出与输入的关系。曲面图由以下代码生成。我们添加了一个颜色条以使图形更易读。

颜色与 z 值相关。我们在第二个图中使用 view，以便于在图中阅读。您可以使用 rotate3d on 来允许您用鼠标旋转图形。

NewFigure('基于模糊逻辑的雨刷速度')

**surf**(x,y,z1)

**xlabel**('雨滴湿度')

**ylabel**('液滴频率')

**zlabel**('雨刷速度')

**colorbar**

![](img/index-160_1.png)

![](img/index-160_2.png)

![](img/index-160_3.png)

![](img/index-160_4.png)

![](img/index-160_5.png)

![](img/index-160_6.png)

![](img/index-160_7.png)

![](img/index-160_8.png)

![](img/index-160_9.png)

![](img/index-160_10.png)

![](img/index-160_11.png)

![](img/index-160_12.png)

![](img/index-160_13.png)

![](img/index-160_14.png)

![](img/index-160_15.png)

![](img/index-160_16.png)

![](img/index-160_17.png)

![](img/index-160_18.png)

![](img/index-160_19.png)

![](img/index-160_20.png)

![](img/index-160_21.png)

![](img/index-160_22.png)

![](img/index-160_23.png)

![](img/index-160_24.png)

![](img/index-160_25.png)

![](img/index-160_26.png)

![](img/index-160_27.png)

![](img/index-160_28.png)



**图 6.2：** 雨水湿度和强度是智能雨刮器控制系统的输入。

**图 6.3：** 雨刷速度和间隔是智能雨刮器控制系统的输出。

**速度**

**间隔**

1.2

1.2

停止

慢速

快速

零

短

长

0.8

0.8

0.6

0.6

隶属度

0.4

隶属度

0.4

0.2

0.2

清晰值

清晰值

NewFigure('基于模糊逻辑的雨刷间隔')

**surf**(x,y,z2)

**xlabel**('雨滴湿度')

**ylabel**('液滴频率')

**zlabel**('雨刷间隔')

**view**([142.5 30])

**提示**

使用 rotate3d on 可以用鼠标旋转图形。

![](img/index-161_1.png)

![](img/index-161_2.png)

![](img/index-161_3.png)

![](img/index-161_4.png)

![](img/index-161_5.jpg)

![](img/index-161_6.jpg)



**图 6.4：** 雨水湿度和强度输入数值。

**图 6.5：** 雨刷速度和间隔与液滴频率和湿度的关系。

![](img/index-162_1.png)

![](img/index-162_2.png)

![](img/index-162_3.png)

![](img/index-162_4.png)

![](img/index-162_5.png)

![](img/index-162_6.png)

![](img/index-162_7.png)

![](img/index-162_8.png)

![](img/index-162_9.png)

![](img/index-162_10.png)

![](img/index-162_11.png)

![](img/index-162_12.png)

![](img/index-162_13.png)

![](img/index-162_14.png)

![](img/index-162_15.png)

![](img/index-162_16.png)

![](img/index-162_17.png)

![](img/index-162_18.png)

![](img/index-162_19.png)

![](img/index-162_20.png)

![](img/index-162_21.png)

![](img/index-162_22.png)

![](img/index-162_23.png)

![](img/index-162_24.png)

![](img/index-162_25.png)

![](img/index-162_26.png)

![](img/index-162_27.png)

![](img/index-162_28.png)

![](img/index-162_29.png)

![](img/index-162_30.png)

![](img/index-162_31.png)

![](img/index-162_32.png)

![](img/index-162_33.png)

![](img/index-162_34.png)

![](img/index-162_35.png)

![](img/index-162_36.png)

![](img/index-162_37.png)

![](img/index-162_38.png)

![](img/index-162_39.png)

![](img/index-162_40.png)

![](img/index-162_41.png)

![](img/index-162_42.png)

![](img/index-162_43.png)

![](img/index-162_44.png)

![](img/index-162_45.png)

![](img/index-162_46.png)

![](img/index-162_47.png)

![](img/index-162_48.png)

![](img/index-162_49.png)



![](img/index-162_50.png)

![](img/index-162_51.png)

![](img/index-162_52.png)

![](img/index-162_53.png)

![](img/index-162_54.png)

![](img/index-162_55.png)

![](img/index-162_56.png)

![](img/index-162_57.png)

![](img/index-162_58.png)

![](img/index-162_59.png)

![](img/index-162_60.png)

![](img/index-162_61.png)

![](img/index-162_62.png)

![](img/index-162_63.png)



**6.4**

**本章小结**

本章演示了模糊逻辑。雨刮器演示给出了一个应用示例。表 6.1 列出了随附代码中包含的函数和脚本。

**表 6.1：**章节代码清单

**文件**

**描述**

BuildFuzzySystem

使用参数对构建模糊逻辑系统（数据结构）。

SmartWipersDemo

演示用于雨刮器的模糊逻辑控制系统。

FuzzyPlot

绘制模糊集合。

TriangleMF

三角形隶属函数。

TrapezoidMF

梯形隶属函数。

SigmoidalMF

显示具有多个层的神经网络。

ScaleIMP

缩放蕴含函数。

ClipIMP

裁剪蕴含函数。

GeneralBellMF

广义钟形隶属函数。

GaussianMF

高斯隶属函数。

FuzzyOR

模糊或（隶属度最大值）。

FuzzAND

模糊与（隶属度最小值）。

FuzzyInference

给定模糊系统和清晰数据 x，执行模糊推理。

CentroidDF

重心去模糊化。

![](img/index-163_1.png)

![](img/index-163_2.png)

![](img/index-163_3.png)

![](img/index-163_4.png)

![](img/index-163_5.png)

