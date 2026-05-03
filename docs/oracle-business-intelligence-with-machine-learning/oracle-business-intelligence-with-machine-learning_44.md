# 假设文件 Wineptobuy.csv 位于调用 ORE CLI 时的工作目录中
> winedata  head(winedata) # 显示数据框 winedata 中的前 6 行数据
id  origin class Alcohol Malic.acid  Ash Alcanility.of.ash Magnesium
1  1 Origin1     1   14.37       1.95 2.50              16.8       113
2  2 Origin1     1   14.20       1.76 2.45              15.2       112
3  3 Origin1     1   14.06       2.15 2.61              17.6       121
4  4 Origin1     1   13.64       3.10 2.56              15.2       116
5  5 Origin1     1   14.06       1.63 2.28              16.0       126
6  6 Origin1     1   12.85       1.60 2.52              17.8        95
Total.phenols Flavanoids Nonflavanoid.phenols Proanthocyanins Color.intensity
1          3.85       3.49              0.24            2.18            7.80
2          3.27       3.39              0.34            1.97            6.75
3          2.60       2.51              0.31            1.25            5.05
4          2.70       3.03              0.17            1.66            5.10
5          3.00       3.17              0.24            2.10            5.65
6          2.48       2.37              0.26            1.46            3.93
Hue OD280.OD315.of.diluted.wines Proline Origin1 Origin2 Origin3  Source
1 0.86                         3.45    1480   1.000   0.000   0.000 Origin1
2 1.05                         2.85    1450   0.994   0.004   0.002 Origin1
3 1.06                         3.58    1295   1.000   0.000   0.000 Origin1
4 0.96                         3.36     845   1.000   0.000   0.000 Origin1
5 1.09                         3.71     780   1.000   0.000   0.000 Origin1
6 1.09                         3.63    1015   1.000   0.000   0.000 Origin1
origin.1 id.1 propensity_to_buy
1  Origin1    1                 1
2  Origin1    2                 1
3  Origin1    3                 1
4  Origin1    4                 1
5  Origin1    5                 1
6  Origin1    6                 1
> summary(winedata) # 给出 winedata 数据框中数据的统计摘要，如下所示，针对每个变量/属性
id          origin        class         Alcohol        Malic.acid
Min.   :  1   Origin1:140   Min.   :1.00   Min.   :11.03   Min.   :0.740
1st Qu.:104   Origin2:162   1st Qu.:1.00   1st Qu.:12.37   1st Qu.:1.530
Median :207   Origin3:111   Median :2.00   Median :13.05   Median :1.830
Mean   :207                 Mean   :1.93   Mean   :13.00   Mean   :2.343
3rd Qu.:310                 3rd Qu.:3.00   3rd Qu.:13.67   3rd Qu.:3.100
Max.   :413                 Max.   :3.00   Max.   :14.75   Max.   :5.800
Ash        Alcanility.of.ash   Magnesium      Total.phenols
Min.   :1.360   Min.   :10.60     Min.   : 70.00   Min.   :0.980
1st Qu.:2.210   1st Qu.:17.00     1st Qu.: 89.00   1st Qu.:1.740
Median :2.360   Median :19.00     Median : 97.00   Median :2.230
Mean   :2.366   Mean   :19.43     Mean   : 99.58   Mean   :2.276
3rd Qu.:2.580   3rd Qu.:21.50     3rd Qu.:107.00   3rd Qu.:2.800
Max.   :3.230   Max.   :30.00     Max.   :162.00   Max.   :3.880
Flavanoids    Nonflavanoid.phenols Proanthocyanins Color.intensity
Min.   :0.340   Min.   :0.1300       Min.   :0.410   Min.   : 1.280
1st Qu.:1.200   1st Qu.:0.2700       1st Qu.:1.140   1st Qu.: 3.300
Median :2.140   Median :0.3400       Median :1.460   Median : 4.600
Mean   :2.011   Mean   :0.3658       Mean   :1.538   Mean   : 4.934
3rd Qu.:2.780   3rd Qu.:0.4500       3rd Qu.:1.870   3rd Qu.: 5.850
Max.   :5.080   Max.   :0.6600       Max.   :3.580   Max.   :13.000
Hue         OD280.OD315.of.diluted.wines    Proline
Min.   :0.5400   Min.   :1.270                Min.   : 278.0
1st Qu.:0.7600   1st Qu.:1.830                1st Qu.: 510.0
Median :0.9600   Median :2.780                Median : 678.0
Mean   :0.9618   Mean   :2.587                Mean   : 749.5
3rd Qu.:1.1300   3rd Qu.:3.140                3rd Qu.: 985.0
Max.   :1.7100   Max.   :4.000                Max.   :1680.0
Origin1          Origin2        Origin3           Source       origin.1
Min.   :0.0000   Min.   :0.0000  Min.   :0.0000   Origin1:140   Origin1:140
1st Qu.:0.0000   1st Qu.:0.0000   1st Qu.:0.0000   Origin2:162   Origin2:162
Median :0.0000   Median :0.0020  Median :0.0000   Origin3:111   Origin3:111
Mean   :0.3395   Mean   :0.3922   Mean   :0.2683
3rd Qu.:1.0000   3rd Qu.:1.0000   3rd Qu.:0.9760
Max.   :1.0000   Max.   :1.0000   Max.   :1.0000
id.1     propensity_to_buy
Min.   :  1   Min.   :0.0000
1st Qu.:104   1st Qu.:0.0000
Median :207   Median :1.0000
Mean   :207   Mean   :0.6077
3rd Qu.:310   3rd Qu.:1.0000
Max.   :413   Max.   :1.0000
> sapply(winedata, sd) # 对数据集 winedata 中的每个变量应用标准差函数 sd
id                       origin
119.3670809                    0.7773550
class                      Alcohol
0.7773550                    0.7667311
Malic.acid                          Ash
1.1720914                    0.2931987
Alcanility.of.ash                    Magnesium
3.4096871                   14.2594176
Total.phenols                   Flavanoids
0.6270436                    0.9700433
Nonflavanoid.phenols              Proanthocyanins
0.1293551                    0.5520653
Color.intensity                          Hue
2.1460253                    0.2289449
OD280.OD315.of.diluted.wines                      Proline
0.7162199                  311.4138632
Origin1                      Origin2
0.4731436                    0.4869368
Origin3                       Source
0.4421105                    0.7773550
origin.1                         id.1
0.7773550                  119.3670809
propensity_to_buy
0.4888445
```

以下两行代码分别显示了响应变量 `propensity_to_buy` 与预测变量 `Source` 和 `origin` 的二维列联表，以确保 `winedata` 数据集中不存在零单元格。换句话说，`xtabs` 函数将分类变量各水平的频数或计数以矩阵或表格形式显示出来——即交叉制表，揭示了 `propensity_to_buy` 与 `Source` 之间，以及 `propensity_to_buy` 与 `origin` 之间的关系。



```
> xtabs(∼propensity_to_buy +Source, data=winedata)
Source
propensity_to_buy Origin1 Origin2 Origin3
0       0     162       0
1     140       0     111
> xtabs(∼propensity_to_buy +origin, data=winedata)
origin
propensity_to_buy Origin1 Origin2 Origin3
0       0     162       0
1     140       0     111
> label  head(label)
[1] 1 1 1 1 1 1
> library(caTools)
Warning message:
package 'caTools' was built under R version 3.2.5
> s  train_set  test_set  head(train_set) # 显示 train_set 中的前 6 行
origin class Alcohol Malic.acid  Ash Alcanility.of.ash Magnesium
1 Origin1     1   14.37       1.95 2.50              16.8       113
3 Origin1     1   14.06       2.15 2.61              17.6       121
4 Origin1     1   13.64       3.10 2.56              15.2       116
5 Origin1     1   14.06       1.63 2.28              16.0       126
6 Origin1     1   12.85       1.60 2.52              17.8        95
7 Origin1     1   13.87       1.90 2.80              19.4       107
Total.phenols Flavanoids Nonflavanoid.phenols Proanthocyanins Color.intensity
1          3.85       3.49              0.24            2.18            7.80
3          2.60       2.51              0.31            1.25            5.05
4          2.70       3.03              0.17            1.66            5.10
5          3.00       3.17              0.24            2.10            5.65
6          2.48       2.37              0.26            1.46            3.93
7          2.95       2.97              0.37            1.76            4.50
Hue OD280.OD315.of.diluted.wines Proline Origin1 Origin2 Origin3  Source
1 0.86                         3.45    1480   1.000   0.000       0 Origin1
3 1.06                         3.58    1295   1.000   0.000       0 Origin1
4 0.96                         3.36     845   1.000   0.000       0 Origin1
5 1.09                         3.71     780   1.000   0.000       0 Origin1
6 1.09                         3.63    1015   1.000   0.000       0 Origin1
7 1.25                         3.40     915   0.996   0.004       0 Origin1
propensity_to_buy
1                 1
3                 1
4                 1
5                 1
6                 1
7                 1
> nrow(train_set) # 显示训练集的行数
[1] 310
> head(test_set) # 显示 test_set 中的前 6 行
origin class Alcohol Malic.acid  Ash Alcanility.of.ash Magnesium
2  Origin1     1   14.20       1.76 2.45              15.2       112
9  Origin1     1   13.51       1.80 2.65              19.0       110
10 Origin1     1   13.05       1.65 2.55              18.0        98
11 Origin1     1   13.88       1.89 2.59              15.0       101
13 Origin1     1   14.38       3.59 2.28              16.0       102
17 Origin1     1   13.83       1.65 2.60              17.2        94
Total.phenols Flavanoids Nonflavanoid.phenols Proanthocyanins
2           3.27       3.39                 0.34            1.97
9           2.35       2.53                 0.29            1.54
10          2.45       2.43                 0.29            1.44
11          3.25       3.56                 0.17            1.70
13          3.25       3.17                 0.27            2.19
17          2.45       2.99                 0.22            2.29
Color.intensity  Hue OD280.OD315.of.diluted.wines Proline Origin1 Origin2
2             6.75 1.05                         2.85    1450   0.994   0.004
9             4.20 1.10                         2.87    1095   1.000   0.000
10            4.25 1.12                         2.51    1105   1.000   0.000
11            5.43 0.88                         3.56    1095   0.996   0.002
13            4.90 1.04                         3.44    1065   1.000   0.000
17            5.60 1.24                         3.37    1265   1.000   0.000
Origin3  Source propensity_to_buy
2    0.002 Origin1                 1
9    0.000 Origin1                 1
10   0.000 Origin1                 1
11   0.002 Origin1                 1
13   0.000 Origin1                 1
17   0.000 Origin1                 1
> nrow(test_set) # 显示测试集的行数
[1] 103
