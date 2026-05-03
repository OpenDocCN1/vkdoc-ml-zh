![](images/978-1-4842-5992-4_CoverFigure.jpg)

ISBN 978-1-4842-5991-7e-ISBN 978-1-4842-5992-4 [https://doi.org/10.1007/978-1-4842-5992-4](https://doi.org/10.1007/978-1-4842-5992-4) © B V Vishwas and Ashish Patel 2020 Apress Standard The use of general descriptive names, registered names, trademarks, service marks, etc. in this publication does not imply, even in the absence of a specific statement, that such names are exempt from the relevant protective laws and regulations and therefore free for general use. The publisher, the authors and the editors are safe to assume that the advice and information in this book are believed to be true and accurate at the date of publication. Neither the publisher nor the authors or the editors give a warranty, expressed or implied, with respect to the material contained herein or for any errors or omissions that may have been made. The publisher remains neutral with regard to jurisdictional claims in published maps and institutional affiliations. Distributed to the book trade worldwide by Springer Science+Business Media New York, 233 Spring Street, 6th Floor, New York, NY 10013\. Phone 1-800-SPRINGER, fax (201) 348-4505, e-mail orders-ny@springer-sbm.com, or visit www.springeronline.com. Apress Media, LLC is a California LLC and the sole member (owner) is Springer Science + Business Media Finance Inc (SSBM Finance Inc). SSBM Finance Inc is a Delaware corporation. Introduction

This book explains the concepts of time series from traditional to bleeding-edge techniques with full-fledged examples.

The book begins by covering time-series fundamentals and their characteristics, Structure & Components of time series data, preprocessing, and ways of crafting features through data wrangling. Next, it covers traditional time-series techniques such as the smoothing methods ARMA, ARIMA, SARIMA, SARIMAX, VAR, and VARMA using trending frameworks such as [Statsmodels](https://www.statsmodels.org/) and Pmdarima.

Further covers how to leverage advanced deep learning-based techniques such as ANN, CNN, RNN, LSTM, GRU, and Autoencoder to solve time-series problems using Tensorflow. It concludes by explaining how to use the popular framework fbprophet for modeling time-series analysis.

After completion of the book, the reader will have thorough knowledge of concepts and techniques to solve time-series problems. All the code presented in this book is available in Jupyter Notebooks; this allows readers to do hands-on experiments and enhance them in exciting ways.

Acknowledgments

This being my first book, I found transforming idea and real-world experience into its current shape to be a strenuous task. I am grateful to almighty God for blessing and guiding me in all endeavors. I would like to thank my parents (Vijay Kumar and Rathnamma), brother (Shreyas), other family, and friends for helping me sail though the sea of life.

> —B V Vishwas

First and foremost, praises and thanks to God, the almighty, for His showers of blessings throughout the book-writing process. I would like to express my deep and sincere gratitude to my parents (Dinesh Kumar N Patel and Javnika ben D Patel), my sister (Nisha Patel), and my family and friends (Shailesh Patel, Sanket Patel, Nikit Patel, Mansi Patel, Khushboo Shah) for their support and valuable prayers.

> —Ashish Patel

Special thanks to Celestin Suresh John, Aditee Mirashi, James Markham, Alexey Panchekha, and the Apress team for bringing this book to life.

About the Authors About the Technical Reviewer

# 1. Time-Series Characteristics

A *time series* is a collection of data points that are stored with respect to their time. Mathematical and statistical analysis performed on this kind of data to find hidden patterns and meaningful insight is called *time-series analysis* . Time-series modeling techniques are used to understand past patterns from the data and try to forecast future horizons. These techniques and methodologies have been evolving for decades.

Observations with continuous timestamps and target variables are sometimes framed as straightforward regression problems by decomposing dates into minutes, hours, days, weeks, months, years, and so on, which is not the right way to handle such data because the results obtained are poor. In this chapter, you will learn the right approach for handling time-series data.

There are different kinds of data, such as structured, semistructured, and unstructured, and each type should be handled in its own way to gain maximum insight. In this book, we are going to be looking at time-series data that is structured in manner such as data from the stock market, weather, birth rates, traffic, bike-sharing apps, etc.

This chapter is a gentle introduction to the types of time-series data, its components, and ways to decompose it.

## Types of Data

*Time-series analysis* is a statistical technique that measures a sequential set of data points. This is a standard measure in terms of time that comes in three types, as shown in Figure [1-1](#492113_1_En_1_Chapter.xhtml#Fig1).

![../images/492113_1_En_1_Chapter/492113_1_En_1_Fig1_HTML.png](images/492113_1_En_1_Chapter/492113_1_En_1_Fig1_HTML.png)

Figure 1-1

Types of data

### Time-Series Data

A time series contains data points that increase, decrease, or otherwise change in chronological order over a period. A time series that incorporates the records of a single feature or variable is called a *univariate* time series. If the records incorporate more than one feature or variable, the series is called a *multivariate* time series. In addition, a time series can be designated in two ways: continuous or discrete.

In a *continuous* time series, data observation is carried out continuously throughout the period, as with earthquake seismograph magnitude data, speech data, etc. Figure [1-2](#492113_1_En_1_Chapter.xhtml#Fig2) illustrates earthquake data measured continuously from 1975 to 2015.

![../images/492113_1_En_1_Chapter/492113_1_En_1_Fig2_HTML.png](images/492113_1_En_1_Chapter/492113_1_En_1_Fig2_HTML.png)

Figure 1-2

Forty years of earthquake seismograph magnitude data

Figure [1-3](#492113_1_En_1_Chapter.xhtml#Fig3) Illustrates temperature behavior in India over a century and clearly shows that temperature is increasing monotonically.

![../images/492113_1_En_1_Chapter/492113_1_En_1_Fig3_HTML.png](images/492113_1_En_1_Chapter/492113_1_En_1_Fig3_HTML.png)

Figure 1-3

India’s temperature data from 1901 to 2017

In a *discrete* time series , data observation is carried out at a specific time or equally spaced, as with temperature increases or decreases, exchange rates of currencies, air pressure data, etc. Figure [1-2](#492113_1_En_1_Chapter.xhtml#Fig2) illustrates the analysis of the average temperature of India from 1901 to 2017, which either increases or decreases with time. This data behavior is discrete.

### Cross-Section Data

*Cross-section data* is data gathered at a specific point of time for several subjects such as closing prices of a particular group of stocks on a specific date, opinion polls of elections, obesity level in population, etc. Cross-section studies are utilized in many research areas such as medical, economics, psychology, etc. For instance, high blood pressure is one of the significant risk factors for cause of death in India according to a 2017 WHO report. WHO has carried out the study of several risk factors (considered various subjects), which reflects cross-section survey data. Figure [1-4](#492113_1_En_1_Chapter.xhtml#Fig4) illustrates the cross-section data.

![../images/492113_1_En_1_Chapter/492113_1_En_1_Fig4_HTML.png](images/492113_1_En_1_Chapter/492113_1_En_1_Fig4_HTML.png)

Figure 1-4

Number of deaths by risk factor in India

### Panel Data/Longitudinal Data

*Panel data/longitudinal data* contains observations of multiple occurrences collected over various durations of time for the same individuals. It is data that is determined periodically by the number of observations in cross-sectional data units such as individuals, companies, or government agencies. Table [1-1](#492113_1_En_1_Chapter.xhtml#Tab1) provides examples of data available for multiple people over the course of a few years where the data gathered comprises income, age, and sex.

Table 1-1

Example of Panel Data

| Panel Data A |   |   |   |   |
| --- | --- | --- | --- | --- |
| **Name** | **Year** | **Income** | **Age** | **Sex** |
| **Allen** | 2016 | 42145 | 24 | Female |
| **Allen** | 2017 | 47797 | 21 | Female |
| **Allen** | 2018 | 41391 | 23 | Female |
| **Malissa** | 2016 | 41100 | 22 | Male |
| **Malissa** | 2017 | 25800 | 23 | Male |
| **Malissa** | 2018 | 34508 | 22 | Male |
| **Panel Data B** |   |   |   |   |
| **Name** | **Year** | **Income** | **Age** | **Sex** |
| **Malissa** | 2016 | 42688 | 27 | Female |
| **Malissa** | 2017 | 21219 | 25 | Female |
| **Allen** | 2016 | 46340 | 26 | Male |
| **Allen** | 2017 | 22715 | 22 | Male |
| **Allen** | 2018 | 34653 | 21 | Male |
| **Alicia** | 2017 | 31553 | 29 | Female |

In Table [1-1](#492113_1_En_1_Chapter.xhtml#Tab1), datasets A and B (with the attributes income, age, and sex) gathered throughout the years are for different people. Dataset A is a depiction of two people, Allen and Malissa, who were subject to observation over three years (2016, 2017, 2018); this is known as *balanced panel data* . Dataset B is called *unbalanced panel data* because data does not exist for every individual every year.

## Trend

A *trend* is a pattern that is observed over a period of time and represents the mean rate of change with respect to time. A trend usually shows the tendency of the data to increase/uptrend or decrease/downtrend during the long run. It is not always necessary that the increase or decrease is in the same direction throughout the given period of time. A trend line is also drawn using candlestick charts.

For example, you may have heard about an increase or decrease in different market commodities such as gold, silver, stock prices, gas, diesel, etc., or about the rate of interest for banks or home loans increasing or decreasing. These are all commodity market conditions, which may either increase or decrease over time, that show a trend in data.

### Detecting Trend Using a Hodrick-Prescott Filter

The Hodrick-Prescott (HP) filter has become a benchmark for getting rid of trend movements in data. This method is broadly employed for econometric methods in applied macroeconomics research. The technique is nonparametric and is used to dissolve a time series into a trend; it is a cyclical component unaided by economic theory or prior trend specification. Like all nonparametric methods, the HP filter is contingent significantly on a tuning parameter that controls the degree of smoothing. This method is broadly employed in applied macroeconomics utilized in central banks, international economics agencies, industry, and government.

With the following example code, you can see how the EXINUS stock changes over a period of time:

```
import pandas as pd
from statsmodels.tsa.filters.hp_filter import hpfilter
df = pd.read_excel(r'\Data\India_Exchange_Rate_Dataset.xls',\
index_col=0,parse_dates=True)
EXINUS_cycle,EXINUS_trend = hpfilter(df['EXINUS'], lamb=1600)
EXINUS_trend.plot(figsize=(15,6)).autoscale(axis='x',tight=True)
```

Figure [1-5](#492113_1_En_1_Chapter.xhtml#Fig5) shows an upward trend over the period.

![../images/492113_1_En_1_Chapter/492113_1_En_1_Fig5_HTML.jpg](images/492113_1_En_1_Chapter/492113_1_En_1_Fig5_HTML.jpg)

Figure 1-5

EXINUS stock showing an upward trend

### Detrending a Time Series

*Detrending* is the process of removing a trend from time-series data, or it mentions a change in the mean over time. It is continuously increasing or decreasing over the duration of time. Identification, modeling, and even removing trend data from time-series datasets can be beneficial. The following are methods to detrend time-series data:

*   Pandas differencing

*   SciPy signal

*   HP filter

#### Detrending Using Pandas Differencing

The Pandas library has a built-in function to calculate the difference in a dataset. This `diff()` function is used both for series and for DataFrames. It can provide a period value to shift in order to form the difference. The following code is an example of Pandas differencing.

*   `Warning` is a built-in module of Python that handles the warning messages.

*   `Pyplot` is a submodule of Matplotlib that is used to design the graphical representation of the data.

```
import pandas as pd
import matplotlib.pyplot as plt
import warnings
warnings.filterwarnings("ignore")
df = pd.read_excel(r'\Data\India_Exchange_Rate_Dataset.xls',\
index_col=0,parse_dates=True)
diff = df.EXINUS.diff()
plt.figure(figsize=(15,6))
plt.plot(diff)
plt.title('Detrending using Differencing', fontsize=16)
plt.xlabel('Year')
plt.ylabel('EXINUS exchange rate')
plt.show()
```

Figure [1-6](#492113_1_En_1_Chapter.xhtml#Fig6) shows the data without a trend by using Pandas differencing .

![../images/492113_1_En_1_Chapter/492113_1_En_1_Fig6_HTML.jpg](images/492113_1_En_1_Chapter/492113_1_En_1_Fig6_HTML.jpg)

Figure 1-6

Trend removal using differencing

#### Detrending Using a SciPy Signal

A signal is another form of time-series data . Every signal either increases or decreases in a different order. Using the SciPy library, this can be removing the linear trend from the signal data. The following code shows an example of SciPy detrending.

*   `Signal.detrend` is a submodule of SciPy that is used to remove a linear trend along an axis from data.

```
import pandas as pd
import matplotlib.pyplot as plt
from scipy import signal
import warnings
warnings.filterwarnings("ignore")
df = pd.read_excel(r'\Data\India_Exchange_Rate_Dataset.xls',\
index_col=0,parse_dates=True)
detrended = signal.detrend(df.EXINUS.values)
plt.figure(figsize=(15,6))
plt.plot(detrended)
plt.xlabel('EXINUS')
plt.ylabel('Frequency')
plt.title('Detrending using Scipy Signal', fontsize=16)
plt.show()
```

Figure [1-7](#492113_1_En_1_Chapter.xhtml#Fig7) shows the detrended data using SciPy.

![../images/492113_1_En_1_Chapter/492113_1_En_1_Fig7_HTML.jpg](images/492113_1_En_1_Chapter/492113_1_En_1_Fig7_HTML.jpg)

Figure 1-7

Removing a linear trend in a signal using SciPy

#### Detrend Using an HP Filter

An HP filter is also used to detrend a time series and smooth the data. It’s used for removing short-term fluctuations. The following code shows an example of HP filter detrending.

*   `Hpfilter` is a submodule of Statmodels that is used to remove a smooth trend.

```
import pandas as pd
import matplotlib.pyplot as plt
from statsmodels.tsa.filters.hp_filter import hpfilter
import warnings
warnings.filterwarnings("ignore")
df = pd.read_excel(r'\Data\India_Exchange_Rate_Dataset.xls',\
index_col=0,parse_dates=True)
EXINUS_cycle,EXINUS_trend = hpfilter(df['EXINUS'], lamb=1600)
df['trend'] = EXINUS_trend
.
detrended = df.EXINUS - df['trend']
plt.figure(figsize=(15,6))
plt.plot(detrended)
plt.title('Detrending using HP Filter', fontsize=16)
plt.xlabel('Year')
plt.ylabel('EXINUS exchange rate')
plt.show()
```

Figure [1-8](#492113_1_En_1_Chapter.xhtml#Fig8) shows the data after removing a smooth trend.

![../images/492113_1_En_1_Chapter/492113_1_En_1_Fig8_HTML.jpg](images/492113_1_En_1_Chapter/492113_1_En_1_Fig8_HTML.jpg)

Figure 1-8

Trend removal using an HP filter

## Seasonality

Seasonality is a periodical fluctuation where the same pattern occurs at a regular interval of time. It is a characteristic of economics, weather, and stock market time-series data; less often, it’s observed in scientific data. In other industries, many phenomena are characterized by periodically recurring seasonal effects. For example, retail sales tend to increase during Christmas and decrease afterward.

The following methods can be used to detect seasonality:

*   Multiple [box plots](https://www.itl.nist.gov/div898/handbook/eda/section3/boxplot.htm)

*   Autocorrelation plots

### Multiple [Box Plots](https://www.itl.nist.gov/div898/handbook/eda/section3/boxplot.htm)

A *box plot* is an essential graph to depict data spread out over a range. It is a standard approach to showing the minimum, first quartile, middle, third quartile, and maximum. The following code shows an example of detecting seasonality with the help of multiple box plots. See Figure [1-9](#492113_1_En_1_Chapter.xhtml#Fig9).

*   Seaborn is a graphical representation package similar to Matplotlib.

    ```
    import pandas as pd
    import seaborn as sns
    import matplotlib.pyplot as plt
    import warnings
    warnings.filterwarnings("ignore")
    df=pd.read_excel(r'\Data\India_Exchange_Rate_Dataset.xls',\
    parse_dates=True)
    df['month'] = df['observation_date'].dt.strftime('%b')
    df['year'] = [d.year for d in df.observation_date]
    df['month'] = [d.strftime('%b') for d in df.observation_date]
    years = df['year'].unique()
    plt.figure(figsize=(15,6))
    sns.boxplot(x='month', y='EXINUS', data=df).set_title("Multi Month-wise Box Plot")
    plt.show()
    ```

![../images/492113_1_En_1_Chapter/492113_1_En_1_Fig9_HTML.jpg](images/492113_1_En_1_Chapter/492113_1_En_1_Fig9_HTML.jpg)

Figure 1-9

Multiple-box plot to identify seasonality

### Autocorrelation Plot

*Autocorrelation* is used to check randomness in data. It helps to identify types of data where the period is not known. For instance, for the monthly data, if there is a regular seasonal effect, we would hope to see massive peak lags after every 12 months. Figure [1-10](#492113_1_En_1_Chapter.xhtml#Fig10) demonstrates an example of detecting seasonality with the help of an autocorrelation plot.

![../images/492113_1_En_1_Chapter/492113_1_En_1_Fig10_HTML.jpg](images/492113_1_En_1_Chapter/492113_1_En_1_Fig10_HTML.jpg)

Figure 1-10

Autocorrelation plot to identify seasonality

```
from pandas.plotting import autocorrelation_plot
import pandas as pd
import matplotlib.pyplot as plt
df = pd.read_excel(r'\Data\India_Exchange_Rate_Dataset.xls',\
index_col=0,parse_dates=True)
plt.rcParams.update({'figure.figsize':(15,6), 'figure.dpi':220})
autocorrelation_plot(df.EXINUS.tolist())
```

Note

Sometimes identifying seasonality is not easy; in that case, we need to evaluate other plots such as sequence or seasonal subseries plots.

### Deseasoning of Time-Series Data

*Deseasoning* means to remove seasonality from time-series data. It is stripped of the pattern of seasonal effect to deseasonalize the impact. Time-series data contains four main components.

*   **Level** means the average value of the time-series data.

*   **Trend** means an increasing or decreasing value in time-series data.

*   **Seasonality** means repeating the pattern of a cycle in the time-series data.

*   **Noise** means random variance in time-series data.

Note

An *additive model* is when time-series data combines these four components for linear trend and seasonality, and a *multiplicative model* is when components are multiplied to gather for nonlinear trends and seasonality.

### Seasonal Decomposition

*Decomposition* is the process of understanding generalizations and problems related to time-series forecasting. We can leverage seasonal decomposition to remove seasonality from data and check the data only with the trend, cyclic, and irregular variations. Figure [1-11](#492113_1_En_1_Chapter.xhtml#Fig11) illustrates data without seasonality.

![../images/492113_1_En_1_Chapter/492113_1_En_1_Fig11_HTML.jpg](images/492113_1_En_1_Chapter/492113_1_En_1_Fig11_HTML.jpg)

Figure 1-11

Deseasoning using seasonal_decompose from Statsmodels

```
import pandas as pd
import matplotlib.pyplot as plt
from statsmodels.tsa.seasonal import seasonal_decompose
import warnings
warnings.filterwarnings("ignore")
df = pd.read_excel(r'\Data\India_Exchange_Rate_Dataset.xls',
index_col=0,parse_dates=True)
result_mul = seasonal_decompose(df['EXINUS'], model='multiplicative', extrapolate_trend='freq')
deseason = df['EXINUS'] - result_mul.seasonal
plt.figure(figsize=(15,6))
plt.plot(deseason)
plt.title('Deseasoning using seasonal_decompose', fontsize=16)
plt.xlabel('Year')
plt.ylabel('EXINUS exchange rate')
plt.show()
```

## Cyclic Variations

Cyclical components are fluctuations around a long trend observed every few units of time; this behavior is less frequent compared to seasonality. It is a recurrent process in a time series. In the field of business/economics, the following are three distinct types of cyclic variations examples:

*   **Prosperity** **:** As we know, when organizations prosper, prices go up, but the benefits also increase. On the other hand, prosperity also causes over-development, challenges in transportation, increments in wage rate, insufficiency in labor, high rates of returns, deficiency of cash in the market and price concessions, etc., leading to depression

*   **Depression** : As we know, when there is cynicism in exchange and enterprises, processing plants close down, organizations fall flat, joblessness spreads, and the wages and costs are low.

*   **Accessibility**: This causes idealness of money, accessibility of cash at a low interest, an increase in demand for goods or money at a low interest rate, an increase in popular merchandise and ventures described by the circumstance of recuperation that at last prompts for prosperity or boom.

### Detecting Cyclical Variations

The following code shows how to decompose time-series data and visualize only a cyclic pattern:

```
from statsmodels.tsa.filters.hp_filter import hpfilter
import pandas as pd
import matplotlib.pyplot as plt
import warnings
warnings.filterwarnings("ignore")
%matplotlib inline
df = pd.read_excel(r'\Data\India_Exchange_Rate_Dataset.xls',
index_col=0,parse_dates=True)
EXINUS_cycle,EXINUS_trend = hpfilter(df['EXINUS'], lamb=1600)
df['cycle'] =EXINUS_cycle
df['trend'] =EXINUS_trend
df[['cycle']].plot(figsize=(15,6)).autoscale(axis='x',tight=True)
plt.title('Extracting Cyclic Variations', fontsize=16)
plt.xlabel('Year')
plt.ylabel('EXINUS exchange rate')
plt.show()
```

Figure [1-12](#492113_1_En_1_Chapter.xhtml#Fig12) illustrates isolated cyclic behavior from other data.

![../images/492113_1_En_1_Chapter/492113_1_En_1_Fig12_HTML.jpg](images/492113_1_En_1_Chapter/492113_1_En_1_Fig12_HTML.jpg)

Figure 1-12

Extracting cyclic variations using an HP filter

## Errors, Unexpected Variations, and Residuals

When trend and cyclical variations are removed from time-series data, the patterns left behind that cannot be explained are called errors, unexpected variations, or residuals. Various methods are available to check for irregular variations such as probability theory, moving averages, and autoregressive time-series methods. If we can find any cyclic variation in data, it is considered to be part of the residuals. These variations that occur due to unexpected circumstances are called unexpected variations or unpredictable errors.

## Decomposing a Time Series into Its Components

*Decomposition* is a method used to isolate the time-series data into different elements such as trends, seasonality, cyclic variance, and residuals. We can leverage seasonal decomposition from a stats model to decompose the data into its constituent parts, considering series as additive or multiplicative.

*   **Trends(T(t))** means an increase or decrease in the value of *ts* data.

*   **Seasonality(S[t])** means repeating a short-term cycle of *ts* data.

*   **Cyclic variations(c[t])** means a fluctuation in long trends of *ts* data.

*   **Residuals(e[t])** means an irregular variation of *ts* data.

The additive model works with linear trends of time-series data such as changes constantly over time. The additive model formula is as follows:

*Y[t] = T[t] + S[t] + c[t] + e[t]*

The multiplicative model works with a nonlinear type of data such as quadric or exponential. The multiplicative model formula is as follows:

Y[t] = T[t] * S[t] * c[t] * e[t]

The following example depicts the additive and multiplicative models:

```
from statsmodels.tsa.seasonal import seasonal_decompose
import pandas as pd
df = pd.read_excel(r'Data\India_Exchange_Rate_Dataset.xls',
index_col=0,parse_dates=True)
result = seasonal_decompose(df['EXINUS'], model='add')
result.plot()
result = seasonal_decompose(df['EXINUS'], model='mul')
result.plot()
```

Figure [1-13](#492113_1_En_1_Chapter.xhtml#Fig13) and Figure [1-14](#492113_1_En_1_Chapter.xhtml#Fig14) illustrate two methods of time-series decomposition.

![../images/492113_1_En_1_Chapter/492113_1_En_1_Fig14_HTML.jpg](images/492113_1_En_1_Chapter/492113_1_En_1_Fig14_HTML.jpg)

Figure 1-14

Decomposing time-series data using a multiplicative model

![../images/492113_1_En_1_Chapter/492113_1_En_1_Fig13_HTML.jpg](images/492113_1_En_1_Chapter/492113_1_En_1_Fig13_HTML.jpg)

Figure 1-13

Decomposing time-series data using an additive model

## Summary

In this chapter, we discussed what time-series data is and glanced at various ways of isolating time-series components such as trend, seasonality, cyclic variations, and errors using multiple techniques. In the next chapter, you will learn how to perform data wrangling for time-series data.

# 2. Data Wrangling and Preparation for Time Series

The expression “Garbage in, garbage out” certainly applies when it comes to data science.

*Data wrangling* is the art of transformation and mapping raw data into valuable information with the help of preprocessing strategies. It aims to provide rich data that can be utilized to gain maximum insights. It comprises operations such as loading, imputing, applying transformations, treating outliers, cleaning, integrating, dealing with inconsistency, reducing dimensionality, and engineering features. Data wrangling is an integral and main part of machine learning modeling. Real-world data is undoubtedly messy, and it is not reasonable to use data directly for modeling without performing some wrangling.

This chapter introduces how to read multiple data file formats with the help of Pandas. It covers how to do data selection, transformation, and cleansing as well as how to derive basic statistics with help of Pandas and Pandasql.

Notes

The functions and methods introduced in this chapter have a lot more functionalities than covered here. You can explore them in more detail at the official Pandas and Pandasql sites.

## Loading Data into Pandas

Pandas is the most notable framework for data wrangling. It includes data manipulation and analysis tools intended to make data analysis rapid and convenient. In the real world, data is generated via various tools and devices; hence, it comes in different formats such as CSV, Excel, and JSON. In addition, sometimes data needs to be read from a URL. All data comprises several records and variables. In this section, you will learn how various data formats are handled with Pandas.

### Loading Data Using CSV

This dataset depicts the number of female births over time. Pandas has a built-in function to read CSV files. The following code imports the dataset:

```
import pandas as pd
df = pd.read_csv(r'\Data\daily-total-female-births-CA.csv')
df.head(5)
```

The following are the top five records from the CSV files :

![../images/492113_1_En_2_Chapter/492113_1_En_2_Figa_HTML.jpg](images/492113_1_En_2_Chapter/492113_1_En_2_Figa_HTML.jpg)

If files have different separators, use `sep = ‘ ’` and fill in the separator, as in `(;, |,\t,‘ ’)`.

### Loading Data Using Excel

The Istanbul Stock Exchange dataset comprises the returns of the Istanbul Stock Exchange along with seven other international indices (SP, DAX, FTSE, NIKKEI, BOVESPA, MSCE_EU, MSCI_EM) from June 5, 2009, to February 22, 2011\. The data is organized by workday. This data is available in Excel format, and Pandas has a built-in function to read the Excel file. The following code imports the dataset:

```
import pandas as pd
dfExcel = pd.read_excel(r'\Data\istambul_stock_exchange.xlsx', sheet_name = 'Data')
dfExcel.head(5)
```

The following are the top five records from the Excel file:

![../images/492113_1_En_2_Chapter/492113_1_En_2_Figb_HTML.jpg](images/492113_1_En_2_Chapter/492113_1_En_2_Figb_HTML.jpg)

### Loading Data Using JSON

This example dataset is in JSON format . The following code imports the dataset:

```
import pandas as pd
dfJson = pd.read_json(r'\Data\test.json')
dfJson.head(5)
```

The following are the records from the JSON:

![../images/492113_1_En_2_Chapter/492113_1_En_2_Figc_HTML.jpg](images/492113_1_En_2_Chapter/492113_1_En_2_Figc_HTML.jpg)

### Loading Data from a URL

The Abalone dataset comprises 4,177 observations and 9 variables. It is not in any file structure; instead, we can display it as text at the specific URL. The following code imports the dataset:

```
import pandas as pd
dfURL = pd.read_csv(r'https://archive.ics.uci.edu/ml/machine-learning-databases/abalone/abalone.data', names =['Sex', 'Length','Diameter', 'Height','Whole weight', 'Shucked weight','Viscera weight', 'Shell weight', 'Rings'])
dfURL.head(5)
```

The following are the records from the URL:

![../images/492113_1_En_2_Chapter/492113_1_En_2_Figd_HTML.jpg](images/492113_1_En_2_Chapter/492113_1_En_2_Figd_HTML.jpg)

## Exploring Pandasql and Pandas Side by Side

Pandasql is a Python framework for running SQL queries on Pandas DataFrames. It has plenty of essential attributes and a similar purpose as the sqldf framework in R. It helps us to query Pandas DataFrames using SQL syntax. It provides a SQL interface to perform data wrangling on a Pandas DataFrame. Pandasql helps analysts and data engineers who are masters of SQL to transition into Python (Pandas) smoothly.

Use `pip install pandasql` to install the library. In this chapter, we will explore ways to use both Pandas and Pandasql for solving different problems, such as selecting data, transforming data, and getting a basic summary of the data.

### Selecting the Top Five Records

With the help of the `pandas.head()` function , we can fetch the first N records from the dataset. We can do the same operation with the help of Pandassql. The example illustrates how to do it with Pandas and Pandasql.

Here is how to do it with Pandas:

```
import pandas as pd
dfp = pd.read_excel(r'\Data\Absenteeism_at_work.xls')
dfp.head(5)
```

![../images/492113_1_En_2_Chapter/492113_1_En_2_Fige_HTML.jpg](images/492113_1_En_2_Chapter/492113_1_En_2_Fige_HTML.jpg)

Here is how to do it with Pandasql:

```
from pandasql import sqldf
dfpsql = pd.read_excel(r'\Data\Absenteeism_at_work.xls')
Query_string = """ select * from dfpsql limit 5 """
sqldf(Query_string, globals())
```

The main function used in Pandasql is `sqldf` . It accepts two parameters: a SQL query string and a set of session/environment variables (`locals()` or `globals()`).

![../images/492113_1_En_2_Chapter/492113_1_En_2_Figf_HTML.jpg](images/492113_1_En_2_Chapter/492113_1_En_2_Figf_HTML.jpg)

### Applying a Filter

Data filtering is a significant part of data preprocessing. With filtering, we can choose a smaller partition of the dataset and use that subset for viewing and munging; we need specific criteria or a rule to filter the data. This is also known as *subsetting data* or *drill-down data* . The following example illustrates how to apply a filter with Pandas and Pandasql.

Here is how to do it with Pandas:

```
import pandas as pd
dfp = pd.read_excel(r'\Data\Absenteeism_at_work.xls')
dfp[(dfp['Age'] >=30) & (dfp['Age'] <=45)]
```

![../images/492113_1_En_2_Chapter/492113_1_En_2_Figg_HTML.jpg](images/492113_1_En_2_Chapter/492113_1_En_2_Figg_HTML.jpg)

Here is how to do it with Pandasql:

```
from pandasql import sqldf
dfpsql = pd.read_excel(r'\Data\Absenteeism_at_work.xls')
Query_string = """ select * from dfpsql where age>=30 and age<=45 """
sqldf(Query_string, globals())
```

![../images/492113_1_En_2_Chapter/492113_1_En_2_Figh_HTML.jpg](images/492113_1_En_2_Chapter/492113_1_En_2_Figh_HTML.jpg)

### Distinct (Unique)

Several duplicate records exist in the dataset. If we want to select the number of unique values for the specific variable, then we can use the `unique()` function of Pandas. The following example illustrates how to do this with Pandas and Pandasql.

Here is how to do it with Pandas:

```
import pandas as pd
dfp = pd.read_excel(r'\Data\Absenteeism_at_work.xls')
dfp['ID'].unique()
```

![../images/492113_1_En_2_Chapter/492113_1_En_2_Figi_HTML.jpg](images/492113_1_En_2_Chapter/492113_1_En_2_Figi_HTML.jpg)

Here is how to do it with Pandasql:

```
from pandasql import sqldf
dfpsql = pd.read_excel(r'\Data\Absenteeism_at_work.xls')
Query_string = """ select distinct ID from dfpsql;"""
sqldf(Query_string, globals())
```

![../images/492113_1_En_2_Chapter/492113_1_En_2_Figj_HTML.jpg](images/492113_1_En_2_Chapter/492113_1_En_2_Figj_HTML.jpg)

### IN

Data filtering is a process of extracting essential data from a dataset by using some condition. There are several methods to do filtering on a dataset. Sometimes we want to investigate whether the data has been associated with a particular DataFrame or Series. In such an event, we can use Pandas’ `isin()` function , which checks whether values are present in the sequence. The procedure can also be carried out in Pandasql. The following example illustrates how to do it with Pandas and Pandasql.

Here is how to do it with Pandas:

```
import pandas as pd
dfp = pd.read_excel(r'\Data\Absenteeism_at_work.xls')
dfp[dfp.Age.isin([20,30,40])]
```

![../images/492113_1_En_2_Chapter/492113_1_En_2_Figl_HTML.jpg](images/492113_1_En_2_Chapter/492113_1_En_2_Figl_HTML.jpg)

Here is how to do it with Pandasql:

```
from pandasql import sqldf
dfpsql = pd.read_excel(r'\Data\Absenteeism_at_work.xls')
Query_string = """ select * from dfpsql where Age in (20,30,40);"""
sqldf(Query_string, globals())
```

![../images/492113_1_En_2_Chapter/492113_1_En_2_Figk_HTML.jpg](images/492113_1_En_2_Chapter/492113_1_En_2_Figk_HTML.jpg)

### NOT IN

The `NOT IN` operation is used for a similar purpose as explained earlier. If we want to check whether a value is not part of a sequence, we can use the tilde (`~`) symbol to perform a `NOT IN` operation.

Here is the example with Pandas:

```
import pandas as pd
dfp = pd.read_excel(r'\Data\Absenteeism_at_work.xls')
dfp[~dfp.Age.isin([20,30,40])]
```

![../images/492113_1_En_2_Chapter/492113_1_En_2_Figm_HTML.jpg](images/492113_1_En_2_Chapter/492113_1_En_2_Figm_HTML.jpg)

Here is the example with Pandasql:

```
from pandasql import sqldf
dfpsql = pd.read_excel(r'\Data\Absenteeism_at_work.xls')
Query_string = """ select * from dfpsql where Age not in(20,30,40);"""
sqldf(Query_string, globals())
```

![../images/492113_1_En_2_Chapter/492113_1_En_2_Fign_HTML.jpg](images/492113_1_En_2_Chapter/492113_1_En_2_Fign_HTML.jpg)

## Ascending Data Order

`ORDER BY` sorts the result-set in ascending or descending order based on the value selected. Pandas has a `sort_value()` function that can use different sorting algorithms, such as quicksort, mergesort, and heapsort. The default value is ascending order, whose Boolean value is `True`. Except for that axis-wise, we do perform sorting such as 0 for index and 1 for columns. SQL has an `ORDER BY` clause to perform a similar sorting operation.

Here is the example using Pandas:

```
import pandas as pd
dfp = pd.read_excel(r'\Data\Absenteeism_at_work.xls')
dfp.sort_values(['Age','Service_time'], ascending= True)
```

![../images/492113_1_En_2_Chapter/492113_1_En_2_Figo_HTML.jpg](images/492113_1_En_2_Chapter/492113_1_En_2_Figo_HTML.jpg)

Here is the example using Pandasql:

`ORDER BY` in SQLite by default is ascending.

```
from pandasql import sqldf
dfpsql = pd.read_excel(r'\Data\Absenteeism_at_work.xls')
Query_string = """ select * from dfpsql order by Age,Service_time;"""
sqldf(Query_string, globals())
```

![../images/492113_1_En_2_Chapter/492113_1_En_2_Figp_HTML.jpg](images/492113_1_En_2_Chapter/492113_1_En_2_Figp_HTML.jpg)

## Descending Data Order

As mentioned, the `sort_value()` function can sort results in ascending or descending order. It needs the parameter `ascending = False` to sort values in descending order. You can also update some other parameters, as explained for `ORDER BY` ascending.

Here is the example with Pandas:

```
import pandas as pd
dfp = pd.read_excel(r'\Data\Absenteeism_at_work.xls')
dfp.sort_values(['Age','Service_time'], ascending= False)
```

![../images/492113_1_En_2_Chapter/492113_1_En_2_Figq_HTML.jpg](images/492113_1_En_2_Chapter/492113_1_En_2_Figq_HTML.jpg)

You can sort results in descending order by using a `desc` keyword next to the column names, as shown here:

```
from pandasql import sqldf
dfpsql = pd.read_excel(r'\Data\Absenteeism_at_work.xls')
Query_string = """ select * from dfpsql order by Age Desc,Service_time Desc;"""
sqldf(Query_string, globals())
```

![../images/492113_1_En_2_Chapter/492113_1_En_2_Figr_HTML.jpg](images/492113_1_En_2_Chapter/492113_1_En_2_Figr_HTML.jpg)

## Aggregation

*Aggregation* is the process of mining data so the data can be investigated, collected, and presented in a summarized manner. It allows you to perform a calculation on single or multiple vectors, usually accompanied by the `group by()` function s in Pandas. It performs numerous operations such as splitting, applying, and combining. This example illustrates the aggregation operation in Pandas and Pandasql.

For the following example, we are leveraging `.agg` in Pandas to achieve the required result:

```
import pandas as pd
dfp = pd.read_excel(r'\Data\Absenteeism_at_work.xls')
dfp.agg({'Transportation_expense': ['count','min', 'max', 'mean']})
```

![../images/492113_1_En_2_Chapter/492113_1_En_2_Figs_HTML.jpg](images/492113_1_En_2_Chapter/492113_1_En_2_Figs_HTML.jpg)

There is also an option to aggregate directly on a column, as shown here:

```
dfp.Transportation_expense.min()
```

Here is the example with Pandasql:

```
from pandasql import sqldf
dfpsql = pd.read_excel(r'\Data\Absenteeism_at_work.xls')
Query_string = """ select count(Transportation_expense) as count, min(Transportation_expense) as min, max(Transportation_expense) as max, avg(Transportation_expense) as mean from dfp;"""
sqldf(Query_string, globals())
```

![../images/492113_1_En_2_Chapter/492113_1_En_2_Figt_HTML.jpg](images/492113_1_En_2_Chapter/492113_1_En_2_Figt_HTML.jpg)

### GROUP BY

We can use `GROUP BY` to arrange identical data into groups based on the aggregation function used. In other words, it groups rows that have similar values into summary rows. We perform this operation with the `groupby()` function in Pandas. SQL has its `GROUP BY` clause to perform a similar operation. The example illustrates the `GROUP BY` operation in Pandas and Pandasql.

Here is the example with Pandas:

```
import pandas as pd
dfp = pd.read_excel(r'\Data\Absenteeism_at_work.xls')
dfp.groupby('ID')['Service_time'].sum()
```

![../images/492113_1_En_2_Chapter/492113_1_En_2_Figu_HTML.jpg](images/492113_1_En_2_Chapter/492113_1_En_2_Figu_HTML.jpg)

Here is the example with Pandasql:

```
from pandasql import sqldf
dfpsql = pd.read_excel(r'\Data\Absenteeism_at_work.xls')
Query_string = """ select ID , sum(Service_time) as Sum_Service_time from dfp
group by ID;"""
sqldf(Query_string, globals())
```

![../images/492113_1_En_2_Chapter/492113_1_En_2_Figv_HTML.jpg](images/492113_1_En_2_Chapter/492113_1_En_2_Figv_HTML.jpg)

### GROUP BY with Aggregation

The `groupby()` function provides a group of similar data based on a selected feature with that data; we can perform different aggregation operations with the `.agg()` function on other features in Pandas. In SQL, we use `GROUP BY` to apply aggregate functions on groups of data returned from a query. `FILTER` is a modifier used with an aggregate function to bound the values used in an aggregation.

Here is the example with Pandas:

```
import pandas as pd
dfp = pd.read_excel(r'\Data\Absenteeism_at_work.xls')
dfp.groupby('Reason_for_absence').agg({'Age': ['mean','min','max']})
```

![../images/492113_1_En_2_Chapter/492113_1_En_2_Figw_HTML.jpg](images/492113_1_En_2_Chapter/492113_1_En_2_Figw_HTML.jpg)

Here is the example with Pandasql:

```
from pandasql import sqldf
dfpsql = pd.read_excel(r'\Data\Absenteeism_at_work.xls')
Query_string = """ select Reason_for_absence , avg(Age) as mean, min(Age) as min, max(Age) as max from dfp
group by Reason_for_absence;"""
sqldf(Query_string, globals())
```

![../images/492113_1_En_2_Chapter/492113_1_En_2_Figx_HTML.jpg](images/492113_1_En_2_Chapter/492113_1_En_2_Figx_HTML.jpg)

## Join (Merge)

The terms *join* and *merge* mean the same thing in Pandas, Python, and other languages like SQL and R. In reality, their fundamental operation is equivalent, but their way of executing an operation is different. A *join* in Pandas utilizes an index to consolidate two data sources, while a *merge* looks for overlapping columns to merge. In Pandas, the `merge()` and `join()` functions are used. In SQL, the `MERGE` and `JOIN` operators perform the same operation as in Pandas. This example illustrates the aggregation operation **i**n Pandas and Pandasql.

We’ve created Sample Employee and Department tables to illustrate the join examples.

```
import pandas as pd
data1 = {
'Empid': [1011, 1012, 1013, 1014, 1015],
'Name': ['John', 'Rahul', 'Rick', 'Morty', 'Tim'],
'Designation': ['Manager', 'Research Engineer', 'Research Engineer', 'VP', 'Delivery Manager'],
'Date_of_joining': ['01-Jan-2000', '23-sep-2006',
'11-Jan-2012','21-Jan-1991','12-Jan-1990']}
Emp_df = pd.DataFrame(data1, columns = ['Empid',
'Name', 'Designation','Date_of_joining'])
print(Emp_df.head(5))
```

![../images/492113_1_En_2_Chapter/492113_1_En_2_Figy_HTML.jpg](images/492113_1_En_2_Chapter/492113_1_En_2_Figy_HTML.jpg)

```
import pandas as pddata2 = {
'Empid': [1011, 1017, 1013, 1019, 1015],
'Deptartment': ['Management', 'Research',
'Research', 'Management', 'Delivery'],
'Total_Experience': [18, 10, 10, 28, 22]}
Dept_df = pd.DataFrame(data2, columns = ['Empid',
'Deptartment', 'Total_Experience'])
print(Dept_df.head(5))
```

![../images/492113_1_En_2_Chapter/492113_1_En_2_Figz_HTML.jpg](images/492113_1_En_2_Chapter/492113_1_En_2_Figz_HTML.jpg)

### INNER JOIN

An inner merge/inner join keeps rows where the merge value contains an “on” value in both the DataFrames. It selects the records that have matching values (joining columns) in one or more tables.

![../images/492113_1_En_2_Chapter/492113_1_En_2_Figaa_HTML.jpg](images/492113_1_En_2_Chapter/492113_1_En_2_Figaa_HTML.jpg)

Here is the example with Pandas:

```
import pandas as pd
data1 = {
'Empid': [1011, 1012, 1013, 1014, 1015],
'Name': ['John', 'Rahul', 'Rick', 'Morty', 'Tim'],
'Designation': ['Manager', 'Research Engineer', 'Research Engineer', 'VP', 'Delivery Manager'],
'Date_of_joining': ['01-Jan-2000', '23-sep-2006',
'11-Jan-2012','21-Jan-1991','12-Jan-1990']}
Emp_df = pd.DataFrame(data1, columns = ['Empid',
'Name', 'Designation','Date_of_joining'])
data2 = {
'Empid': [1011, 1017, 1013, 1019, 1015],
'Deptartment': ['Management', 'Research',
'Research', 'Management', 'Delivery'],
'Total_Experience': [18, 10, 10, 28, 22]}
Dept_df = pd.DataFrame(data2, columns = ['Empid',
'Deptartment', 'Total_Experience'])
pd.merge(Emp_df, Dept_df, left_on="Empid",right_on='Empid', how="inner")
```

![../images/492113_1_En_2_Chapter/492113_1_En_2_Figab_HTML.jpg](images/492113_1_En_2_Chapter/492113_1_En_2_Figab_HTML.jpg)

Here is the example with Pandasql:

```
import pandas as pd
data1 = {
'Empid': [1011, 1012, 1013, 1014, 1015],
'Name': ['John', 'Rahul', 'Rick', 'Morty', 'Tim'],
'Designation': ['Manager', 'Research Engineer', 'Research Engineer', 'VP', 'Delivery Manager'],
'Date_of_joining': ['01-Jan-2000', '23-sep-2006',
'11-Jan-2012','21-Jan-1991','12-Jan-1990']}
Emp_df = pd.DataFrame(data1, columns = ['Empid',
'Name', 'Designation','Date_of_joining'])
data2 = {
'Empid': [1011, 1017, 1013, 1019, 1015],
'Deptartment': ['Management', 'Research',
'Research', 'Management', 'Delivery'],
'Total_Experience': [18, 10, 10, 28, 22]}
Dept_df = pd.DataFrame(data2, columns = ['Empid',
'Deptartment', 'Total_Experience'])
Query_string = """ select * from Emp_df a INNER JOIN Dept_df b ON  a.Empid = b.Empid;"""
sqldf(Query_string, globals())
```

![../images/492113_1_En_2_Chapter/492113_1_En_2_Figac_HTML.jpg](images/492113_1_En_2_Chapter/492113_1_En_2_Figac_HTML.jpg)

### LEFT JOIN

A left merge or left join keeps a row on the left DataFrame when the missing value finds the “on” variable in the right DataFrame and adds the empty or NaN value in that result. All the records from the left table and selected matching records based on the joining condition from one or more tables.

![../images/492113_1_En_2_Chapter/492113_1_En_2_Figad_HTML.jpg](images/492113_1_En_2_Chapter/492113_1_En_2_Figad_HTML.jpg)

Here is the example with Pandas:

```
import pandas as pd
data1 = {
'Empid': [1011, 1012, 1013, 1014, 1015],
'Name': ['John', 'Rahul', 'Rick', 'Morty', 'Tim'],
'Designation': ['Manager', 'Research Engineer', 'Research Engineer', 'VP', 'Delivery Manager'],
'Date_of_joining': ['01-Jan-2000', '23-sep-2006',
'11-Jan-2012','21-Jan-1991','12-Jan-1990']}
Emp_df = pd.DataFrame(data1, columns = ['Empid',
'Name', 'Designation','Date_of_joining'])
data2 = {
'Empid': [1011, 1017, 1013, 1019, 1015],
'Deptartment': ['Management', 'Research',
'Research', 'Management', 'Delivery'],
'Total_Experience': [18, 10, 10, 28, 22]}
Dept_df = pd.DataFrame(data2, columns = ['Empid',
'Deptartment', 'Total_Experience'])
pd.merge(Emp_df, Dept_df,left_on='Empid',right_on='Empid', how="left")
```

![../images/492113_1_En_2_Chapter/492113_1_En_2_Figae_HTML.jpg](images/492113_1_En_2_Chapter/492113_1_En_2_Figae_HTML.jpg)

Here is the example with Pandasql:

```
import pandas as pd
data1 = {
'Empid': [1011, 1012, 1013, 1014, 1015],
'Name': ['John', 'Rahul', 'Rick', 'Morty', 'Tim'],
'Designation': ['Manager', 'Research Engineer', 'Research Engineer', 'VP', 'Delivery Manager'],
'Date_of_joining': ['01-Jan-2000', '23-sep-2006',
'11-Jan-2012','21-Jan-1991','12-Jan-1990']}
Emp_df = pd.DataFrame(data1, columns = ['Empid',
'Name', 'Designation','Date_of_joining'])
data2 = {
'Empid': [1011, 1017, 1013, 1019, 1015],
'Deptartment': ['Management', 'Research',
'Research', 'Management', 'Delivery'],
'Total_Experience': [18, 10, 10, 28, 22]}
Dept_df = pd.DataFrame(data2, columns = ['Empid',
'Deptartment', 'Total_Experience'])
Query_string = """ select * from Emp_df a LEFT JOIN Dept_df b ON  a.Empid = b.Empid;"""
sqldf(Query_string, globals())
```

![../images/492113_1_En_2_Chapter/492113_1_En_2_Figaf_HTML.jpg](images/492113_1_En_2_Chapter/492113_1_En_2_Figaf_HTML.jpg)

### RIGHT JOIN

A right merge or right join keeps every row on the right DataFrame. Where the missing values find the “on” variable in the left columns, you add the empty/NaN values to the result. All the records from the left table and selected matching records based on the joining condition from one or more tables.

![../images/492113_1_En_2_Chapter/492113_1_En_2_Figag_HTML.jpg](images/492113_1_En_2_Chapter/492113_1_En_2_Figag_HTML.jpg)

Here is the example with Pandas:

```
import pandas as pd
data1 = {
'Empid': [1011, 1012, 1013, 1014, 1015],
'Name': ['John', 'Rahul', 'Rick', 'Morty', 'Tim'],
'Designation': ['Manager', 'Research Engineer', 'Research Engineer', 'VP', 'Delivery Manager'],
'Date_of_joining': ['01-Jan-2000', '23-sep-2006',
'11-Jan-2012','21-Jan-1991','12-Jan-1990']}
Emp_df = pd.DataFrame(data1, columns = ['Empid',
'Name', 'Designation','Date_of_joining'])
data2 = {
'Empid': [1011, 1017, 1013, 1019, 1015],
'Deptartment': ['Management', 'Research',
'Research', 'Management', 'Delivery'],
'Total_Experience': [18, 10, 10, 28, 22]}
Dept_df = pd.DataFrame(data2, columns = ['Empid',
'Deptartment', 'Total_Experience'])
pd.merge(Emp_df, Dept_df,left_on='Empid',right_on='Empid', how="right")
```

![../images/492113_1_En_2_Chapter/492113_1_En_2_Figah_HTML.jpg](images/492113_1_En_2_Chapter/492113_1_En_2_Figah_HTML.jpg)

Here is the example with Pandasql :

```
import pandas as pd
data1 = {
'Empid': [1011, 1012, 1013, 1014, 1015],
'Name': ['John', 'Rahul', 'Rick', 'Morty', 'Tim'],
'Designation': ['Manager', 'Research Engineer', 'Research Engineer', 'VP', 'Delivery Manager'],
'Date_of_joining': ['01-Jan-2000', '23-sep-2006',
'11-Jan-2012','21-Jan-1991','12-Jan-1990']}
Emp_df = pd.DataFrame(data1, columns = ['Empid',
'Name', 'Designation','Date_of_joining'])
data2 = {
'Empid': [1011, 1017, 1013, 1019, 1015],
'Deptartment': ['Management', 'Research',
'Research', 'Management', 'Delivery'],
'Total_Experience': [18, 10, 10, 28, 22]}
Dept_df = pd.DataFrame(data2, columns = ['Empid',
'Deptartment', 'Total_Experience'])
Query_string = """ select a.Empid,Name,Designation,Date_of_joining,Deptartment,Total_Experience from Dept_df a LEFT JOIN Emp_df b ON  a.Empid = b.Empid;"""
sqldf(Query_string, globals())
```

![../images/492113_1_En_2_Chapter/492113_1_En_2_Figai_HTML.jpg](images/492113_1_En_2_Chapter/492113_1_En_2_Figai_HTML.jpg)

### OUTER JOIN

An outer merge/full outer join returns all the rows from left and right DataFrames only where it matches or returns NaNs or empty values. It returns all the records from both the tables based on the joining condition regardless of whether they match or not. Not matching ones will be nulls.

![../images/492113_1_En_2_Chapter/492113_1_En_2_Figaj_HTML.jpg](images/492113_1_En_2_Chapter/492113_1_En_2_Figaj_HTML.jpg)

Here is the example with Pandas:

```
import pandas as pd
data1 = {
'Empid': [1011, 1012, 1013, 1014, 1015],
'Name': ['John', 'Rahul', 'Rick', 'Morty', 'Tim'],
'Designation': ['Manager', 'Research Engineer', 'Research Engineer', 'VP', 'Delivery Manager'],
'Date_of_joining': ['01-Jan-2000', '23-sep-2006',
'11-Jan-2012','21-Jan-1991','12-Jan-1990']}
Emp_df = pd.DataFrame(data1, columns = ['Empid',
'Name', 'Designation','Date_of_joining'])
data2 = {
'Empid': [1011, 1017, 1013, 1019, 1015],
'Deptartment': ['Management', 'Research',
'Research', 'Management', 'Delivery'],
'Total_Experience': [18, 10, 10, 28, 22]}
Dept_df = pd.DataFrame(data2, columns = ['Empid',
'Deptartment', 'Total_Experience'])
pd.merge(Emp_df, Dept_df,left_on='Empid',right_on='Empid', how="outer")
```

![../images/492113_1_En_2_Chapter/492113_1_En_2_Figak_HTML.jpg](images/492113_1_En_2_Chapter/492113_1_En_2_Figak_HTML.jpg)

Here is the example with Pandasql:

```
import pandas as pd
data1 = {
'Empid': [1011, 1012, 1013, 1014, 1015],
'Name': ['John', 'Rahul', 'Rick', 'Morty', 'Tim'],
'Designation': ['Manager', 'Research Engineer', 'Research Engineer', 'VP', 'Delivery Manager'],
'Date_of_joining': ['01-Jan-2000', '23-sep-2006',
'11-Jan-2012','21-Jan-1991','12-Jan-1990']}
Emp_df = pd.DataFrame(data1, columns = ['Empid',
'Name', 'Designation','Date_of_joining'])
data2 = {
'Empid': [1011, 1017, 1013, 1019, 1015],
'Deptartment': ['Management', 'Research',
'Research', 'Management', 'Delivery'],
'Total_Experience': [18, 10, 10, 28, 22]}
Dept_df = pd.DataFrame(data2, columns = ['Empid',
'Deptartment', 'Total_Experience'])
# OUTER join in not currenlty supported in SQL lite
Query_string = """ select * from Emp_df a left OUTER JOIN Dept_df b ON  a.Empid = b.Empid;"""
sqldf(Query_string, globals())
```

![../images/492113_1_En_2_Chapter/492113_1_En_2_Figal_HTML.jpg](images/492113_1_En_2_Chapter/492113_1_En_2_Figal_HTML.jpg)

## Summary of the DataFrame

*Descriptive statistics* is a method used to depict data in a meaningful way to represent either the entire population or a sample. It has two sections: the *measure of central tendency* , which is used to measure the summary of a sample and population (e.g., mean, median, and mode), and the *measure of variability* , which is used to measure the spread or dispersion in the dataset (e.g., range, interquartile, variance, and standard deviation). In Pandas, it provides a built-in function called `describe()`. The following example illustrates the detailed result in Pandas.

In Pandas, the `describe()` function returns a number of descriptive statistics values (e.g., mean, standard deviation, min, median, max, first quartile, third quartile).

```
import pandas as pd
dfsumm = pd.read_csv(r'https://archive.ics.uci.edu/ml/machine-learning-databases/abalone/abalone.data', names =['Sex',
'Length','Diameter', 'Height','Whole weight', 'Shucked weight','Viscera weight', 'Shell weight', 'Rings'])
dfsumm.head(5)
```

![../images/492113_1_En_2_Chapter/492113_1_En_2_Figam_HTML.jpg](images/492113_1_En_2_Chapter/492113_1_En_2_Figam_HTML.jpg)

```
dfsumm.describe()
```

![../images/492113_1_En_2_Chapter/492113_1_En_2_Figan_HTML.jpg](images/492113_1_En_2_Chapter/492113_1_En_2_Figan_HTML.jpg)

## Resampling

*Resampling* is a method to convert the time-based observed data into a different interval. In other words, it is used to change the time frequency into another time-frequency format. For instance, say we want to change monthly data into a year-wise format or upsample week data into hours. We would perform either upsample or downsample operations. For that, every data object must have a DateTime-like `index(Datetimeindex, PeriodIdnex, TimedeltaIndex)`. The following example illustrates the result of resampling operations in Pandas:

```
import pandas as pd
df = pd.read_csv(r'\Data\daily-total-female-births-CA.csv',index_col =0,
parse_dates=['date'])
df.head(5)
```

![../images/492113_1_En_2_Chapter/492113_1_En_2_Figao_HTML.jpg](images/492113_1_En_2_Chapter/492113_1_En_2_Figao_HTML.jpg)

### Resampling by Month

Here is how to resample by month:

```
df.births.resample('M').mean()
```

![../images/492113_1_En_2_Chapter/492113_1_En_2_Figap_HTML.jpg](images/492113_1_En_2_Chapter/492113_1_En_2_Figap_HTML.jpg)

### Resampling by Quarter

Here is how to resample by quarter:

```
df.births.resample('Q').mean()
```

![../images/492113_1_En_2_Chapter/492113_1_En_2_Figaq_HTML.jpg](images/492113_1_En_2_Chapter/492113_1_En_2_Figaq_HTML.jpg)

### Resampling by Year

Here is how to resample by year:

```
df.births.resample('Y').mean()
```

![../images/492113_1_En_2_Chapter/492113_1_En_2_Figar_HTML.jpg](images/492113_1_En_2_Chapter/492113_1_En_2_Figar_HTML.jpg)

### Resampling by Week

Here is how to resample by week:

```
df.births.resample('W').mean()
```

![../images/492113_1_En_2_Chapter/492113_1_En_2_Figas_HTML.jpg](images/492113_1_En_2_Chapter/492113_1_En_2_Figas_HTML.jpg)

### Resampling on a Semimonthly Basis

Here is how to resample on a semimonthly basis:

```
df.births.resample('SM').mean()
```

![../images/492113_1_En_2_Chapter/492113_1_En_2_Figat_HTML.jpg](images/492113_1_En_2_Chapter/492113_1_En_2_Figat_HTML.jpg)

## Windowing Function

A *windowing function* is used to calculate the number of operations, such as rolling count, rolling sum, rolling mean, rolling median, rolling variance, rolling standard deviation, rolling min, rolling max, rolling correlation, rolling covariance, rolling skewness, rolling kurtosis, rolling quantile, etc. In addition, we can perform some other operations such as expanding window and exponential weighted moving window. The following example illustrates the result of windowing operations in Pandas:

```
import pandas as pd
dfExcelwin = pd.read_excel(r'\Data\istambul_stock_exchange.xlsx', sheet_name = 'Data',index_col =0,parse_dates=['date'])
dfExcelwin.head(5)
```

![../images/492113_1_En_2_Chapter/492113_1_En_2_Figau_HTML.jpg](images/492113_1_En_2_Chapter/492113_1_En_2_Figau_HTML.jpg)

### Rolling Window

The rolling window feature supports the following methods: `count`, `sum`, `mean`, `median`, `var`, `std`, `min`, `max`, `corr`, `cov`, `skew`, `kurt`, `quantile`, `sum`, and `aggregate`.

```
import pandas as pd
dfExcelwin = pd.read_excel(r'\Data\istambul_stock_exchange.xlsx', sheet_name = 'Data',index_col =0,parse_dates=['date'])
dfExcelwin.rolling(window=4).mean().head(10)
```

![../images/492113_1_En_2_Chapter/492113_1_En_2_Figav_HTML.jpg](images/492113_1_En_2_Chapter/492113_1_En_2_Figav_HTML.jpg)

### Expanding Window

Expanding window supports the following methods: `count`, `sum`, `mean`, `median`, `var`, `std`, `min`, `max`, `corr`, `cov`, `skew`, `kurt`, `quantile`, `sum`, `aggregate`, and `quantile`.

```
import pandas as pd
dfExcelwin = pd.read_excel(r'\Data\istambul_stock_exchange.xlsx', sheet_name = 'Data',index_col =0,parse_dates=['date'])
dfExcelwin.expanding(min_periods=4).mean().head(10)
```

![../images/492113_1_En_2_Chapter/492113_1_En_2_Figaw_HTML.jpg](images/492113_1_En_2_Chapter/492113_1_En_2_Figaw_HTML.jpg)

### Exponentially Weighted Moving Window

Expanding window supports the following methods : `mean`, `std`, `var`, `corr`, and `cov`.

```
import pandas as pd
dfExcelwin = pd.read_excel(r'\Data\istambul_stock_exchange.xlsx', sheet_name = 'Data',index_col =0,parse_dates=['date'])dfExcelwin.ewm(com=0.5).mean().head(10)
```

![../images/492113_1_En_2_Chapter/492113_1_En_2_Figax_HTML.jpg](images/492113_1_En_2_Chapter/492113_1_En_2_Figax_HTML.jpg)

## Shifting

*Shifting* is also known as “lag” and moves a value back or forward in time. Pandas has a `df.shift()` function to shift an index by the desired number of periods with an optional time frequency. The following example illustrates the shifting operations result in Pandas:

```
import pandas as pd
dfshift = pd.read_excel(r'\Data\istambul stock exchange.xlsx', sheet_name = 'Data',index_col =0, parse_dates=['date'])
dfshift.head(5)
```

![../images/492113_1_En_2_Chapter/492113_1_En_2_Figay_HTML.jpg](images/492113_1_En_2_Chapter/492113_1_En_2_Figay_HTML.jpg)

```
dfshift.shift(periods=3).head(7)
```

![../images/492113_1_En_2_Chapter/492113_1_En_2_Figaz_HTML.jpg](images/492113_1_En_2_Chapter/492113_1_En_2_Figaz_HTML.jpg)

```
dfshift.shift(periods=-1).head(7)
```

![../images/492113_1_En_2_Chapter/492113_1_En_2_Figba_HTML.jpg](images/492113_1_En_2_Chapter/492113_1_En_2_Figba_HTML.jpg)

```
dfshift.shift(periods=3, axis =1).head(7)
```

![../images/492113_1_En_2_Chapter/492113_1_En_2_Figbb_HTML.jpg](images/492113_1_En_2_Chapter/492113_1_En_2_Figbb_HTML.jpg)

```
dfshift.shift(periods=3,fill_value=0).head(7)
```

![../images/492113_1_En_2_Chapter/492113_1_En_2_Figbc_HTML.jpg](images/492113_1_En_2_Chapter/492113_1_En_2_Figbc_HTML.jpg)

## Handling Missing Data

A missing value or missing data occurs when no data value is found at a respective number of places in either a DataFrame or dataset. A missing value presents many problems in the dataset. It reduces the statistical power of data, and the lost data can increase the bias in the dataset. So, it is essential to handle this missing value to maintain the characteristics of the data. Pandas has a number of methods to handle the missing values such as `bfill()`, `ffill()`, `interpolate()`, and `fillna()`. The following example illustrates the missing value–handling operations result in Pandas:

```
import pandas as pd
dfmiss = pd.read_csv(r'\Data\daily-total-female-births-CA-with_nulls.csv',index_col =0, parse_dates=['date'])
dfmiss
```

![../images/492113_1_En_2_Chapter/492113_1_En_2_Figbd_HTML.jpg](images/492113_1_En_2_Chapter/492113_1_En_2_Figbd_HTML.jpg)

*   `Isnull()` is a function to detect missing values for an array-like object.

```
dfmiss.isnull().sum()
```

![../images/492113_1_En_2_Chapter/492113_1_En_2_Figbe_HTML.jpg](images/492113_1_En_2_Chapter/492113_1_En_2_Figbe_HTML.jpg)

### BFILL

The backward method fills the missing values in the dataset and uses the next valid observation to fill the gap.

```
dfmiss.bfill()
```

![../images/492113_1_En_2_Chapter/492113_1_En_2_Figbf_HTML.jpg](images/492113_1_En_2_Chapter/492113_1_En_2_Figbf_HTML.jpg)

### FFILL

The forward fill method propagates the last valid observation forward to the next valid one.

```
dfmiss.ffill()
```

![../images/492113_1_En_2_Chapter/492113_1_En_2_Figbg_HTML.jpg](images/492113_1_En_2_Chapter/492113_1_En_2_Figbg_HTML.jpg)

### FILLNA

This replaces NA/NaN values using a constant value.

```
dfmiss.fillna(10)
```

![../images/492113_1_En_2_Chapter/492113_1_En_2_Figbh_HTML.jpg](images/492113_1_En_2_Chapter/492113_1_En_2_Figbh_HTML.jpg)

### INTERPOLATE

This method interpolates values linearly in a forward direction.

```
dfmiss.interpolate(method='linear',limit_direction='forward')
```

![../images/492113_1_En_2_Chapter/492113_1_En_2_Figbi_HTML.jpg](images/492113_1_En_2_Chapter/492113_1_En_2_Figbi_HTML.jpg)

## Summary

In this chapter, we discussed different techniques for performing data wrangling with the help of Pandas and Pandasql. In the next chapter, you will learn some simple, double, and triple exponential smoothing techniques.

# 3. Smoothing Methods

Smoothing is a statistical method we can use to create an approximation function to remove irregularities in data and attempt to capture significant patterns. Robert Goodell Brown was the father of exponential smoothing, and in 1956 he published “Exponential Smoothing for Predicting Demand” ([`https://industrydocuments.library.ucsf.edu/tobacco/docs/#id=jzlc0130`](https://industrydocuments.library.ucsf.edu/tobacco/docs/%2523id%253Djzlc0130)). In 1957, professor Charles C. Holt was working at CMU on forecasting and published a paper called “Forecasting Seasonals and Trends by Exponentially Weighted Moving Averages” that discussed double exponential smoothing. Three years, in 1960, a student of Holt’s, Peter R. Winters, developed an algorithm by uniting seasonality and published “Forecasting Sales by Exponentially Weighted Moving Averages” ([`https://pubsonline.informs.org/doi/abs/10.1287/mnsc.6.3.324`](https://pubsonline.informs.org/doi/abs/10.1287/mnsc.6.3.324)).

The smoothing technique is a family of time-series forecasting algorithms, which utilizes the weighted averages of a previous observation to predict or forecast a new value. The main idea of this technique is to overweight recent values in a time series. It is utilized for short-term forecasting models. This technique is more efficient when time-series data is moving slowly over time. It harmonizes errors, trends, and seasonal components into computing smoothening parameters. These components are pooled either additively or multiplicatively.

This chapter introduces the math behind simple, double, and triple exponential smoothing and how to leverage them efficiently to solve time-series problems.

## Introduction to Simple Exponential Smoothing

Simple exponential smoothing (SES) is one of the minimal models of the exponential smoothing algorithm. Such techniques are employed for a univariate observation (data), which has no clear trend or seasonal patterns. It involves a parameter called *alpha* , which is the smoothing parameter. The core idea is to employ a weighted moving average, including exponentially decreasing weights, which designates higher weight to the most recent observation. The concept behind SES is forecasting future values by using a weighted average of all the previous values in the series. This method can be used to predict series that do not have trends or seasonality.

In this section, let’s look at the high-level math behind simple exponential smoothing.

Assume that a series has the following:

*   Level (L[t])

*   No trends

*   No seasonality

*   Noise (somewhat constant)

SES is the forecast estimate level at a most recent point in time.

F[t+k] = L[t]

Let’s estimate and update the level (L[t]).

Here is the level updating equation:

L[t =] αY[t] + (1-α) L[t-1]

We are taking the level at time L[t] and updating the previous level, L[t-1], by integrating information from most recent data point, Y[t]. We can see that it is a weighted average, where α and 1-α are the weights.

The smoothing constant equals α.

The 0≤α≤1 alpha value lies between 0 and 1.

The algorithm learns a new level from the new data that it sees. One of the possible ways of initializing the whole system is to assign L1 equal to the first record in the series.

F[1] = L[1] = Y[1]

Why is it called exponential smoothing?

Here is the level update equation:

L[t =] αY[t] + (1-α) L[t-1]

Substitute L[t] with its formula, as shown here:

L[t =] αY[t] + (1-α) [(αY[t-1] +(1-α)L[t-2]] =

= αY[t]+ α(1-α) Y[t-1]+(1-α)² L[t-2 = …]

= αY[t]+ α(1-α) Y[t-1]+ α (1-α)² Y[t-2] + […]

We can see that we are ending up with an average of all the values in a series, but they have weights that are decaying exponentially into the past. Hence, this is called *exponential smoothing* when α = 1 and past values do not influence the forecast. It’s called *under-smoothing* when α = 0 and past values have equal weight in the average. In this case, we do not give more weight to recent information, which is called *over-smoothing* .

Here is the effect of alpha:

| **α** | **α (1- α)** | **α (1- α)**^(**2**) | **α (1- α)**^(**3**) |
| --- | --- | --- | --- |
| 0.9 | 0.089 | 0.0089 | 0.00089 |
| 0.5 | 0.25 | 0.125 | 0.0625 |
| 0.1 | 0.09 | 0.081 | 0.0729 |

In all cases, decay as we go into the past with alpha is fast, whereas with a smaller alpha decay it is slower.

## Simple Exponential Smoothing in Action

In the previous section, you learned about the math behind simple exponential smoothing. Now let’s implement it on a time-series dataset.

Import the required libraries and load the CSV data, which is Facebook stock data from 2014 to 2019.

*   **NumPy** is the core library for scientific computing in Python. It provides a high-performance multidimensional array object and tools for working with these arrays.

*   **sklearn** is a machine learning library for the Python programming language.

```
from statsmodels.tsa.api import SimpleExpSmoothing
import pandas as pd
import numpy as np
from sklearn import metrics
df = pd.read_csv(r'\Data\FB.csv')
```

Figure [3-1](#492113_1_En_3_Chapter.xhtml#Fig1) provides a sneak peek at the data.

![../images/492113_1_En_3_Chapter/492113_1_En_3_Fig1_HTML.jpg](images/492113_1_En_3_Chapter/492113_1_En_3_Fig1_HTML.jpg)

Figure 3-1

First ten rows of dataset

```
Print(df)
```

Modeling will be done only for the Close column in the dataset. Make a copy of the data to perform the train/test split, where the train is used for training, and after getting satisfactory results, we will evaluate the results on the test data.

The train will have all the data expected for the last 30 days, and the test contains only the last 30 days to evaluate against predictions.

```
X = df['Close']
test = X.iloc[-30:]
train = X.iloc[:-30]
```

Let’s look at the simple exponential smoothing algorithm’s hyperparameters for reference.

The `SimpleExpSmoothing` parameter specifies the following:

*   **smoothing_level (float, optional)**: This is the `smoothing_level` value of the simple exponential smoothing. If the value is set, then this value will be used as the value.

*   **optimized (bool)**: This specifies whether the values that have not been set earlier should be optimized automatically.

Create a function that has all the required evaluation metrics, which will give us the results in one go. This function helps us understand how far off our forecasts are against the actuals.

*   **Mean squared error (MSE)** tells you how close a regression line is to a set of points. It does this by taking the distances from the points to the regression line (these distances are the errors) and squaring them. The closer to zero the error is, the better the model.

*   **Mean absolute error (MAE)** measures the average magnitude of the errors in a set of predictions, without considering their direction. The closer to zero the error is, the better the model.

*   **Root mean square error (RMSE)** is a quadratic scoring rule that also measures the average magnitude of the error. It’s the square root of the average of squared differences between prediction and actual observation. The closer to zero the error is, the better the model.

*   **Mean absolute percentage error (MAPE)** is a statistical measure of how accurate a forecast system is. It is a measure in terms of percentage. It is mostly used for time-series forecasting. The closer to zero the error is, the better the model.

*   **R**-**squared** determines the proportion of variance in the dependent variable that can be explained by the [independent variable](https://corporatefinanceinstitute.com/resources/knowledge/modeling/independent-variable/). The closer to zero the error is, the better the model.

MAPE and RMSE are notable statistical measures used to check the accuracy of the forecasting model.

```
def timeseries_evaluation_metrics_func(y_true, y_pred):
def mean_absolute_percentage_error(y_true, y_pred):
y_true, y_pred = np.array(y_true), np.array(y_pred)
return np.mean(np.abs((y_true - y_pred) / y_true)) * 100
print('Evaluation metric results:-')
print(f'MSE is : {metrics.mean_squared_error(y_true, y_pred)}')
print(f'MAE is : {metrics.mean_absolute_error(y_true, y_pred)}')
print(f'RMSE is : {np.sqrt(metrics.mean_squared_error(y_true, y_pred))}')
print(f'MAPE is : {mean_absolute_percentage_error(y_true, y_pred)}')
print(f'R2 is : {metrics.r2_score(y_true, y_pred)}',end='\n\n')
```

The following snippet is used to find the best smoothing parameter, which ranges from 0 to 1\. The smoothing level model will be fit, and its results will be captured in `temp_df`. The following results show that 1.0 gives the least RMSE.

The `SimpleExpSmoothing` parameter was imported from the Statsmodels package. Here we are using it to train the data. The training data is converted into a NumPy array. See Figure [3-2](#492113_1_En_3_Chapter.xhtml#Fig2) for the results.

![../images/492113_1_En_3_Chapter/492113_1_En_3_Fig2_HTML.jpg](images/492113_1_En_3_Chapter/492113_1_En_3_Fig2_HTML.jpg)

Figure 3-2

Optimal parameter search results

```
resu = []
temp_df = pd.DataFrame()
for i in [0, 0.10, 0.20, 0.30, 0.40, 0.50, 0.60, 0.70, 0.80, 0.90,1]:
print(f'Fitting for smoothing level= {i}')
fit_v = SimpleExpSmoothing(np.asarray(train)).fit(i)
fcst_pred_v= fit_v.forecast(30)
timeseries_evaluation_metrics_func(test,fcst_pred_v)
rmse = np.sqrt(metrics.mean_squared_error(test, fcst_pred_v))
df3 = {'smoothing parameter':i, 'RMSE': rmse}
temp_df = temp_df.append(df3, ignore_index=True)
temp_df.sort_values(by=['RMSE']).head(3)
```

![../images/492113_1_En_3_Chapter/492113_1_En_3_Figa_HTML.jpg](images/492113_1_En_3_Chapter/492113_1_En_3_Figa_HTML.jpg)

From the previous search, the least RMSE was achieved with `smoothing_level` equal to 0.1\. Let’s use the same value and train the model.

```
fitSES = SimpleExpSmoothing(np.asarray(train)).fit(smoothing_level = 0.1,optimized= False)
fcst_gs_pred = fitSES.forecast(30)
timeseries_evaluation_metrics_func(test,fcst_gs_pred)
```

![../images/492113_1_En_3_Chapter/492113_1_En_3_Figb_HTML.jpg](images/492113_1_En_3_Chapter/492113_1_En_3_Figb_HTML.jpg)

Let’s check if `SimpleExpSmoothing` is able to find the best parameters by itself by adjusting a few settings. See Figure [3-3](#492113_1_En_3_Chapter.xhtml#Fig3).

![../images/492113_1_En_3_Chapter/492113_1_En_3_Fig3_HTML.jpg](images/492113_1_En_3_Chapter/492113_1_En_3_Fig3_HTML.jpg)

Figure 3-3

Summary of best parameter selected

*   `optimized = True` estimates the model parameters by maximizing the log likelihood.

*   `use_brute= True` searches for good starting values using a brute-force (grid) optimizer.

```
fitSESauto = SimpleExpSmoothing(np.asarray(train)).fit( optimized= True, use_brute = True)
fcst_auto_pred = fitSESauto.forecast(30)
timeseries_evaluation_metrics_func(test,fcst_auto_pred)
```

![../images/492113_1_En_3_Chapter/492113_1_En_3_Figc_HTML.jpg](images/492113_1_En_3_Chapter/492113_1_En_3_Figc_HTML.jpg)

```
fitSESauto.summary()
```

Now that we have the results, let’s set the index similar to the test index, such that plots align properly. See Figure [3-4](#492113_1_En_3_Chapter.xhtml#Fig4).

![../images/492113_1_En_3_Chapter/492113_1_En_3_Fig4_HTML.jpg](images/492113_1_En_3_Chapter/492113_1_En_3_Fig4_HTML.jpg)

Figure 3-4

Line plot depicting results comparision between different configurations of simple exponential smoothing

```
df_fcst_gs_pred = pd.DataFrame(fcst_gs_pred, columns=['Close_grid_Search'])
df_fcst_gs_pred["new_index"] = range(1229, 1259)
df_fcst_gs_pred = df_fcst_gs_pred.set_index("new_index")
df_fcst_auto_pred = pd.DataFrame(fcst_auto_pred, columns=['Close_auto_search'])
df_fcst_auto_pred["new_index"] = range(1229, 1259)
df_fcst_auto_pred = df_fcst_auto_pred.set_index("new_index")
import matplotlib.pyplot as plt
get_ipython().run_line_magic('matplotlib', 'inline')
plt.rcParams["figure.figsize"] = [16,9]
plt.plot( train, label='Train')
plt.plot(test, label='Test')
plt.plot(fcst_auto_pred, label='Simple Exponential Smoothing using optimized =True')
plt.plot(fcst_gs_pred, label='Simple Exponential Smoothing using custom grid search')
plt.legend(loc='best')
plt.show()
```

We can clearly see that the simple exponential smoothing is not performing well as the stock market data, which will have trends and seasonality. Our basic model will not be able to capture these details.

In the next section, we will learn the math behind double exponential smoothing and also how to use it efficiently.

## Introduction to Double Exponential Smoothing

As you saw in the previous section, simple exponential smoothing does not function well when the data has trends. In those cases, we can use double exponential smoothing. This is a more reliable method for handling data that consumes trends without seasonality than compared to other methods. This method adds a time trend equation in the formulation. Two different weights, or smoothing parameters, are used to update these two components at a time.

Holt’s exponential smoothing is also sometimes called *double exponential smoothing*. The main idea here is to use SES and advance it to capture the trend component.

Assume that a series has the following:

*   Level

*   Trend s

*   No seasonality

*   Noise

Forecast = estimate level + trend at the most recent time point.

F[t+k]= l[t] + kT[t]

I is the most recent level.

T[t] is the most recent time point.

K is how many steps into the future we are trying to forecast.

We will have two updated equations, level and trends, and we learn both from the data.

Here is the updated equation for level:

L[t =] αY[t] + (1-α) (L[t-1] + T[t-1])

L[t-1] adjusts the previous level.

T[t-1] adds the trend.

(L[t-1] + T[t-1]) adjusts the previous level by adding a trend.

Here is the updated equation for trends:

T[t] = β (L[t]-L[t-1]) + (1-β) T[t-1]

Take the trend in the previous time period, T[t-1], and update it by showing the difference in most recent level estimates: L[t]-L[t-1]. This setup allows the trend to vary over time adaptively.

**β** is controlling the speed of adjusting the trend. If the trend changes quickly in the series, we want it to learn faster, so we have this:

**α >0 <1**

**β>0 <1**

We can adjust Holt’s exponential smoothing forecast formula to capture either an additive or multiplicative trend.

Here is the additive trend:

Forecast = most recent estimate level + estimate trend.

F[t+k] =L[t] + KT[t]

Here is the multiplicative trend:

Forecast = most recent estimate level + estimate trend.

F[t+k] =L[t] x ( T[t] )^K

*Dampening* means decreasing future time trends on a straight line (no trend). The predictions generated by Holt’s linear method show a constant trend (increasing or decreasing) in the future.

### Double Exponential Smoothing in Action

In the previous section, you learned about the math behind double exponential smoothing. Now let’s implement it on a time-series dataset.

Import the required libraries and load the CSV data, which is Facebook stock data from 2014 to 2019.

```
import pandas as pd
import numpy as np
from sklearn import metrics
from timeit import default_timer as timer
from statsmodels.tsa.api import ExponentialSmoothing, SimpleExpSmoothing, Holt
df = pd.read_csv(r'\Data\FB.csv')
```

Let’s take a sneak peek at the data (see Figure [3-5](#492113_1_En_3_Chapter.xhtml#Fig5)).

![../images/492113_1_En_3_Chapter/492113_1_En_3_Fig5_HTML.jpg](images/492113_1_En_3_Chapter/492113_1_En_3_Fig5_HTML.jpg)

Figure 3-5

First ten rows of loaded dataset

```
Print(df)
```

Modeling will be done only for the Close column in the dataset. Let’s perform a test train split.

The train will have all the data except the last 30 days. The test contains only the last 30 days to evaluate against predictions.

```
train = df.Close[0:-30]
test = df.Close[-30:]
```

Create a function that has all the required evaluation metrics, which will give us results in one go.

```
def timeseries_evaluation_metrics_func(y_true, y_pred):
def mean_absolute_percentage_error(y_true, y_pred):
y_true, y_pred = np.array(y_true), np.array(y_pred)
return np.mean(np.abs((y_true - y_pred) / y_true)) * 100
print('Evaluation metric results:-')
print(f'MSE is : {metrics.mean_squared_error(y_true, y_pred)}')
print(f'MAE is : {metrics.mean_absolute_error(y_true, y_pred)}')
print(f'RMSE is : {np.sqrt(metrics.mean_squared_error(y_true, y_pred))}')
print(f'MAPE is : {mean_absolute_percentage_error(y_true, y_pred)}')
print(f'R2 is : {metrics.r2_score(y_true, y_pred)}',end='\n\n')
```

The following snippet is a simple grid search. First we are defining a range of parameters for `smoothing_level`, `smoothing_slope`, `damping_slope`, and `damped`, and then we are trying to find the best possible combination that yields at least RMSE and R2.

```
from sklearn.model_selection import ParameterGrid
param_grid = {'smoothing_level': [0.10, 0.20,.30,.40,.50,.60,.70,.80,.90], 'smoothing_slope':[0.10, 0.20,.30,.40,.50,.60,.70,.80,.90],
'damping_slope': [0.10, 0.20,.30,.40,.50,.60,.70,.80,.90],'damped' : [True, False]}
pg = list(ParameterGrid(param_grid))
```

Let’s discuss the double explanation smoothing algorithm’s hyperparameters for reference.

Here are the Holt parameters:

*   **endog (array-like)**: Time series

*   **exponential (bool, optional)**: Type of trend component

*   **damped (bool, optional)**: Whether the trend component should be damped

Here are the fit parameters:

*   **smoothing_level (float, optional)**: This is the alpha value of the simple exponential smoothing. If the value is set, then this setting will be used as the value.

*   **smoothing_slope (float, optional)**: This is the beta value of the Holt trend method. If the value is set, then this setting will be used as the value.

*   **damping_slope (float, optional)**: This is the phi value of the damped method. If the value is set, then this setting will be used as the value.

*   **Optimized (bool, optional)**. This specifies whether the values that have not been set be optimized automatically.

```
df_results_moni = pd.DataFrame(columns=['smoothing_level', 'smoothing_slope', 'damping_slope','damped','RMSE','r2'])
start = timer()
for a,b in enumerate(pg):
smoothing_level = b.get('smoothing_level')
smoothing_slope = b.get('smoothing_slope')
damping_slope = b.get('damping_slope')
damped = b.get('damped')
print(smoothing_level, smoothing_slope, damping_slope,damped)
fit1 = Holt(train,damped =damped ).fit(smoothing_level=smoothing_level, smoothing_slope=smoothing_slope, damping_slope = damping_slope, optimized=False)
#fit1.summary
z = fit1.forecast(30)
print(z)
df_pred = pd.DataFrame(z, columns=['Forecasted_result'])
RMSE = np.sqrt(metrics.mean_squared_error(test, df_pred.Forecasted_result))
r2 = metrics.r2_score(test, df_pred.Forecasted_result)
print( f' RMSE is {np.sqrt(metrics.mean_squared_error(test, df_pred.Forecasted_result))}')
df_results_moni = df_results_moni.append({'smoothing_level' :smoothing_level, 'smoothing_slope':smoothing_slope, 'damping_slope' :damping_slope,'damped':damped,'RMSE': RMSE,'r2':r2}, ignore_index=True)
end = timer()
print(f' Total time taken to complete grid search in seconds: {(end - start)}')
print(f' Below mentioned parameter gives least RMSE and r2')
df_results_moni.sort_values(by=['RMSE','r2']).head(1)
```

See Figure [3-6](#492113_1_En_3_Chapter.xhtml#Fig6) and Figure [3-7](#492113_1_En_3_Chapter.xhtml#Fig7).

![../images/492113_1_En_3_Chapter/492113_1_En_3_Fig7_HTML.jpg](images/492113_1_En_3_Chapter/492113_1_En_3_Fig7_HTML.jpg)

Figure 3-7

Custom grid search model summary

![../images/492113_1_En_3_Chapter/492113_1_En_3_Fig6_HTML.jpg](images/492113_1_En_3_Chapter/492113_1_En_3_Fig6_HTML.jpg)

Figure 3-6

Best parameter selected from custom grid search

```
fit1 = Holt(train,damped =False ).fit(smoothing_level=0.9, smoothing_slope=0.6, damping_slope = 0.1, optimized=False)
Forecast_custom_pred = fit1.forecast(30)
fit1.summary()
```

Now let’s fit the double exponential smoothing model custom grid search results and evaluate the results. See Figure [3-8](#492113_1_En_3_Chapter.xhtml#Fig8).

![../images/492113_1_En_3_Chapter/492113_1_En_3_Fig8_HTML.jpg](images/492113_1_En_3_Chapter/492113_1_En_3_Fig8_HTML.jpg)

Figure 3-8

Results after using custom grid search parameters

```
timeseries_evaluation_metrics_func(test,Forecast_custom_pred)
```

Let’s check whether the double exponential smoothing is able to find the best parameters by itself by adjusting a few settings, as follows:

*   `optimized = True` estimates model parameters by maximizing the log likelihood.

*   `use_brute= True` searches for good starting values using a brute-force (grid) optimizer.

```
fitESAUTO = Holt(train).fit(optimized= True, use_brute = True)
fitESAUTO.summary()
```

See Figure [3-9](#492113_1_En_3_Chapter.xhtml#Fig9), Figure [3-10](#492113_1_En_3_Chapter.xhtml#Fig10), and Figure [3-11](#492113_1_En_3_Chapter.xhtml#Fig11).

![../images/492113_1_En_3_Chapter/492113_1_En_3_Fig11_HTML.jpg](images/492113_1_En_3_Chapter/492113_1_En_3_Fig11_HTML.jpg)

Figure 3-11

Line plot depicting results comparision between different configurations of double exponential smoothing

![../images/492113_1_En_3_Chapter/492113_1_En_3_Fig10_HTML.jpg](images/492113_1_En_3_Chapter/492113_1_En_3_Fig10_HTML.jpg)

Figure 3-10

Results after using automated grid search parameters

![../images/492113_1_En_3_Chapter/492113_1_En_3_Fig9_HTML.jpg](images/492113_1_En_3_Chapter/492113_1_En_3_Fig9_HTML.jpg)

Figure 3-9

Automated grid search model summary

```
fitESAUTOpred = fitESAUTO.forecast(30)
timeseries_evaluation_metrics_func(test,fitESAUTOpred)
```

```
import matplotlib.pyplot as plt
get_ipython().run_line_magic('matplotlib', 'inline')
plt.rcParams["figure.figsize"] = [16,9]
plt.plot( train, label='Train')
plt.plot(test, label='Test')
plt.plot(fitESAUTOpred, label='Automated grid search')
plt.plot(Forecast_custom_pred, label='Double Exponential Smoothing with custom grid search')
plt.legend(loc='best')
plt.show()
```

From the evaluation metrics and graph, we can see that the double exponential smoothing performed significantly better than simple exponential smoothing.

In the next section, we will learn the math behind triple exponential smoothing and how to use it efficiently.

## Introduction to Triple Exponential Smoothing

Triple exponential smoothing is a forecasting method that enforces exponential smoothing three times. This method can be applied when the data consumes trends and seasonality over time. It includes all smoothing component equations such as trends and seasonality. Seasonality comprises two different types, such as additive and multiplicative, which is a similar operation in mathematics. The Winter method uses the idea of the Holt method and adds seasonality. Winter was a student of Holt and extended his approach by adding an additional equation to update seasonality. This triple exponential smoothing is also known as the Holt-Winters method.

Let’s assume that a series has the following:

*   Level

*   Trend

*   Seasonality

*   Noise

The forecasting equation can be adjusted to handle additive or multiplicative trends and seasonality.

This is the additive seasonality model:

Forecast = estimate level + trend + seasonality at most recent time point.

F[t+k]= l[t] + kT[t] + S[t+k-M]

This is the multiplicative seasonality model with the additive trend:

Forecast = estimate (level × trend) × seasonality at most recent time point.

F[t+k]= (l[t] + kT[t] ) S[t+k-M]

Assume our series contains the level, trend, and seasonality with M seasons with noise.

In Holt-Winter exponential smoothing, we have three smoothing constants.

**Level: Lt = α (y**[**t**]**/S**[**t-M**]**) + (1- α) (L**[**t-1**] **+T**[**t-1**]**)**

S is a seasonal component. When we y[t]/S[t-M], we are the deseasonalizing value of y.

In the level equation, we are updating the previous level by L[t-1], adding Tt[-1], and then combining and then combining the deseasonalizing value of y[t].

**Trend: T**[**t**] **= β (L**[**t**]**- L**[**t-1**]**) + (1- β) T**[**t-1**] **( Additive trend)**

We can update the previous trend by considering the latest difference between levels.

**Seasonality: St =** ***γ*** **(Y**[**t**]**/L**[**t**]**) + (1+** ***γ)*** **S**[**t-M**] **(multiplicative seasonality)**

Y[t] is divided by level component L[t]. This gives the detrended value of Y.

So, the seasonality is updated by combining the most recent seasonal component St-M with the detrended value of Yt.

### Triple Exponential Smoothing in Action

In the previous section, you learned about the math behind triple exponential smoothing. Now let’s implement it on a time-series dataset.

Import the required libraries and load the CSV data, which is Facebook stock data from 2014 to 2019.

```
from statsmodels.tsa.api import ExponentialSmoothing
import pandas as pd
import numpy as np
from sklearn import metrics
from timeit import default_timer as timer
df = pd.read_csv(r'\Data\FB.csv')
```

Let’s take a sneak peek at the data (Figure [3-12](#492113_1_En_3_Chapter.xhtml#Fig12)).

![../images/492113_1_En_3_Chapter/492113_1_En_3_Fig12_HTML.jpg](images/492113_1_En_3_Chapter/492113_1_En_3_Fig12_HTML.jpg)

Figure 3-12

First ten rows of dataset

```
Print(df)
```

Modeling will be done only for the Close column in the dataset. Make a copy of the data, and let’s perform a test train split.

The train will have all the data except the last 30 days of data. The test contains only the last 30 days of data to evaluate against predictions.

```
X = df['Close']
test = X.iloc[-30:]
train = X.iloc[:-30]
```

Let’s use the triple exponential smoothing algorithm’s hyperparameters for reference.

Here are Holt-Winter’s exponential smoothing parameters:

*   **endog (array-like)**: Time series

*   **trend ({“add”, “mul”, “additive”, “multiplicative”, None}, optional)**: Type of trend component

*   **damped (bool, optional)**: Whether the trend component should be damped

*   **seasonal ({“add”, “mul”, “additive”, “multiplicative”, None}, optional)**: Type of seasonal component

*   **seasonal_periods (int, optional)**: The number of seasons to consider for Holt-Winters

Here are the fit parameters:

*   **smoothing_level (float, optional)**: This is the alpha value of the simple exponential smoothing. If the value is set, then this setting will be used as the value.

*   **smoothing_slope (float, optional)**: This is the beta value of the Holt trend method. If the value is set, then this setting will be used as the value.

*   **smoothing_seasonal (float, optional)**: This is the gamma value of the Holt-Winter seasonal method. If the value is set, then this setting will be used as the value.

*   **damping_slope (float, optional)**: This is the phi value of the damped method. If the value is set, then this setting will be used as the value.

*   **optimized (bool, optional)**: This specifies whether the values that have not been set earlier should be optimized automatically.

*   **use_boxcox ({True, False, ‘log’, float}, optional)**: This specifies whether the box cox transform should be applied to the data first. If `log`, then it applies the log. If `float`, then it uses a lambda equal to `float`.

*   **remove_bias (bool, optional)**: This specifies whether the bias should be removed from the forecast values and fitted values before being returned. It does this by enforcing average residuals equal to zero.

*   **use_basinhopping (bool, optional)**: This specifies whether the optimizer should try harder using basin hopping to find optimal values.

Create a function that has all the required evaluation metrics, which will give us results in one go.

```
def timeseries_evaluation_metrics_func(y_true, y_pred):
def mean_absolute_percentage_error(y_true, y_pred):
y_true, y_pred = np.array(y_true), np.array(y_pred)
return np.mean(np.abs((y_true - y_pred) / y_true)) * 100
print('Evaluation metric results:-')
print(f'MSE is : {metrics.mean_squared_error(y_true, y_pred)}')
print(f'MAE is : {metrics.mean_absolute_error(y_true, y_pred)}')
print(f'RMSE is : {np.sqrt(metrics.mean_squared_error(y_true, y_pred))}')
print(f'MAPE is : {mean_absolute_percentage_error(y_true, y_pred)}')
print(f'R2 is : {metrics.r2_score(y_true, y_pred)}',end='\n\n')
```

The following snippet is a simple grid search. First we are defining the range of parameters for `seasonal`, `trend`, `seasonal_periods`, `smoothing_level`, `smoothing_slope`, `damping_slope`, `damped`, `use_boxcox`, `remove_bias`, and `use_basinhopping`. Then we are trying to find the best possible combination that yields us at least RMSE and R2.

Executing the following code will take some time as our search space is larger:

```
from sklearn.model_selection import ParameterGrid
param_grid = {'trend': ['add', 'mul'],'seasonal' :['add', 'mul'],'seasonal_periods':[3,6,12], 'smoothing_level': [0.10, 0.20,.30,.40,.50,.60,.70,.80,.90], 'smoothing_slope':[0.10, 0.20,.30,.40,.50,.60,.70,.80,.90],
'damping_slope': [0.10, 0.20,.30,.40,.50,.60,.70,.80,.90],'damped' : [True, False], 'use_boxcox':[True, False],
'remove_bias':[True, False],'use_basinhopping':[True, False]}
pg = list(ParameterGrid(param_grid))
df_results_moni = pd.DataFrame(columns=['trend','seasonal_periods','smoothing_level', 'smoothing_slope',
'damping_slope','damped','use_boxcox','remove_bias','use_basinhopping','RMSE','r2'])
start = timer()
print('Starting Grid Search..')
for a,b in enumerate(pg):
trend = b.get('trend')
smoothing_level = b.get('smoothing_level')
seasonal_periods = b.get('seasonal_periods')
smoothing_level = b.get('smoothing_level')
smoothing_slope = b.get('smoothing_slope')
damping_slope = b.get('damping_slope')
damped = b.get('damped')
use_boxcox = b.get('use_boxcox')
remove_bias = b.get('remove_bias')
use_basinhopping = b.get('use_basinhopping')
#print(trend,smoothing_level, smoothing_slope,damping_slope,damped,use_boxcox,remove_bias,use_basinhopping)
fit1 = ExponentialSmoothing(train,trend=trend, damped=damped,seasonal_periods=seasonal_periods ).fit(smoothing_level=smoothing_level,
smoothing_slope=smoothing_slope, damping_slope = damping_slope,use_boxcox=use_boxcox,optimized=False)
#fit1.summary
z = fit1.forecast(30)
#print(z)
df_pred = pd.DataFrame(z, columns=['Forecasted_result'])
RMSE = np.sqrt(metrics.mean_squared_error(test, df_pred.Forecasted_result))
r2 = metrics.r2_score(test, df_pred.Forecasted_result)
#print( f' RMSE is {np.sqrt(metrics.mean_squared_error(test, df_pred.Forecasted_result))}')
df_results_moni = df_results_moni.append({'trend':trend,'seasonal_periods':seasonal_periods,'smoothing_level':smoothing_level, 'smoothing_slope':smoothing_slope,
'damping_slope':damping_slope,'damped':damped,'use_boxcox':use_boxcox,'use_basinhopping':use_basinhopping,'RMSE':RMSE,'r2':r2}, ignore_index=True)
print('End of Grid Search')
end = timer()
print(f' Total time taken to complete grid search in seconds: {(end - start)}')
```

![../images/492113_1_En_3_Chapter/492113_1_En_3_Figd_HTML.jpg](images/492113_1_En_3_Chapter/492113_1_En_3_Figd_HTML.jpg)

After completing the custom grid search, all the possible combinations with their results are stored in a `df_results_moni` DataFrame (Figure [3-13](#492113_1_En_3_Chapter.xhtml#Fig13)).

![../images/492113_1_En_3_Chapter/492113_1_En_3_Fig13_HTML.jpg](images/492113_1_En_3_Chapter/492113_1_En_3_Fig13_HTML.jpg)

Figure 3-13

Best parameters from custom grid search

```
print(f' Below mentioned parameter gives least RMSE and r2')
df_results_moni.sort_values(by=['RMSE','r2']).head(1)
```

Fit the model with grid search parameters and evaluate the results. See Figure [3-14](#492113_1_En_3_Chapter.xhtml#Fig14).

![../images/492113_1_En_3_Chapter/492113_1_En_3_Fig14_HTML.jpg](images/492113_1_En_3_Chapter/492113_1_En_3_Fig14_HTML.jpg)

Figure 3-14

Results after using custom grid search parameters

```
fit1 = ExponentialSmoothing(train,trend='mul', damped=False,seasonal_periods=3 ).fit(smoothing_level=0.9,
smoothing_slope=0.6, damping_slope = 0.6,use_boxcox=False,use_basinhopping = True,optimized=False)
Forecast_custom_pred = fit1.forecast(30)
fit1.summary()
timeseries_evaluation_metrics_func(test,Forecast_custom_pred)
```

Let’s check whether triple exponential smoothing is able to find the best parameters by itself by adjusting a few settings such as the following (see Figure [3-15](#492113_1_En_3_Chapter.xhtml#Fig15)):

![../images/492113_1_En_3_Chapter/492113_1_En_3_Fig15_HTML.jpg](images/492113_1_En_3_Chapter/492113_1_En_3_Fig15_HTML.jpg)

Figure 3-15

Automated grid search model summary

*   `optimized = True` estimates model parameters by maximizing the log likelihood.

*   `use_brute = True` searches for good starting values using a brute-force (grid) optimizer.

```
fitESAUTO = ExponentialSmoothing(train).fit(optimized= True, use_brute = True)
fitESAUTO.summary()
```

See Figure [3-16](#492113_1_En_3_Chapter.xhtml#Fig16) and Figure [3-17](#492113_1_En_3_Chapter.xhtml#Fig17).

![../images/492113_1_En_3_Chapter/492113_1_En_3_Fig17_HTML.jpg](images/492113_1_En_3_Chapter/492113_1_En_3_Fig17_HTML.jpg)

Figure 3-17

Line plot depicting results comparision between different configurations of triple exponential smoothing

![../images/492113_1_En_3_Chapter/492113_1_En_3_Fig16_HTML.jpg](images/492113_1_En_3_Chapter/492113_1_En_3_Fig16_HTML.jpg)

Figure 3-16

Results after using automated grid search parameters

```
fitESAUTOpred = fitESAUTO.forecast(30)
timeseries_evaluation_metrics_func(test,fitESAUTOpred)
```

```
import matplotlib.pyplot as plt
get_ipython().run_line_magic('matplotlib', 'inline')
plt.rcParams["figure.figsize"] = [16,9]
plt.plot( train, label='Train')
plt.plot(test, label='Test')
plt.plot(fitESAUTOpred, label='Automated grid search')
plt.plot(Forecast_custom_pred, label='Triple Exponential Smoothing with custom grid search')
plt.legend(loc='best')
plt.show()
```

By carefully observing evaluation metrics and graphs, we can conclude that triple exponential smoothing slightly outperforms double exponential smoothing.

Therefore, you can conclude the following:

*   If there is no presence of trend or seasonality, then use simple exponential smoothing.

*   If there is a presence of trend and no seasonality, use double exponential smoothing.

*   If there is a presence of trend and seasonality, then use triple exponential smoothing.

## Summary

In this chapter, you learned about the math behind simple, double, and triple exponential smoothing and how to use custom and automated grid search to find the best hyperparameters so that models can yield the best results. In the next chapter, you will learn about various regression extension techniques for time-series data.

# 4. Regression Extension Techniques for Time-Series Data

In this chapter, you will learn how to leverage regression techniques to solve time-series problems efficiently. *Regression* is a supervised learning technique in machine learning where you try to estimate target variables using one or multiple regressors. In this chapter, you will learn what *stationary* means in time-series data as well as how to make data stationery, how to interpret p-values, and how to test whether a series is stationary. Finally, we’ll explore the AR, MA, ARIMA, SARIMA, SARIMAX, VAR, and VARMA techniques and solve some problems with real-world datasets.

## Types of Stationary Behavior in a Time Series

*Stationary* data means that the statistical properties of the particular process do not vary with time. There are five forms of stationary time-series data.

1.  **Strict or strong stationary** : A stochastic process is an unconditional joint probability distribution that does not change when shifted in time. This means that the distribution is the same through time.

2.  **First-order stationary** : This series has constant means that never change with time. Other statistical properties may be changing, such as variance.

3.  **Weak (second-order) stationary** : In weak stationery data, mean, variance, and covariance are constant throughout the time series. It is also known as covariance/mean stationarity.

4.  **Trend stationary** : This series varies around the trends (the statistical property mean is varied). These trends may be linear or quadric.

5.  **Difference stationary** : This type of series has one or more differences when the data is in a time series.

So, if data exhibits the behaviors of constant mean, variance, and autocorrelation, then it is said to be *stationary*. It is mandatory to convert your data into a stationery format to train a time-series forecasting model. For this reason, you need to know how to check whether a time series is stationary or not. Figure [4-1](#492113_1_En_4_Chapter.xhtml#Fig1) and Figure [4-2](#492113_1_En_4_Chapter.xhtml#Fig2) show some data before and after making it stationary.

![../images/492113_1_En_4_Chapter/492113_1_En_4_Fig2_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Fig2_HTML.jpg)

Figure 4-2

After stationary operation

![../images/492113_1_En_4_Chapter/492113_1_En_4_Fig1_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Fig1_HTML.jpg)

Figure 4-1

Before stationary operation

## Making Data Stationery

When time-series data is nonstationary, it means it has trends and seasonality patterns that should be removed. By making data stationary, our data will have a constant mean and variance, and most of the statistical concepts can be applied. To check whether a time series is stationary or not, we can use three types of methods.

*   Plots

*   Summary statistics

*   Statistics tests

### Using Plots

The previous graphs show the time-series data in a visual form. This helps us understand whether a time series is linearly increasing or decreasing with time, which is known as a *nonstationary* time series. A plot that is not increasing or decreasing and shows constant growth over time is known as a *stationary* time series.

### Using Summary Statistics

Summary statistics determine all the essential factors such as mean, standard deviation, variance, min, max, and IQR, which can assist in distinguishing behavior from the data. It helps us to evaluate the seasons or random patterns in the data so we can check for significant differences. Whether the time series is stationary or not, we can split the data in a time series into different groups, where we can compare the difference factors such as mean, variance, and standard deviation. If the factor that is observed varies and is statistically significant, we say that time series is *nonstationary*. There are many domains, such as sales, finance, retail, healthcare, etc., where we will find irregular behavior patterns with mean and variance continuously varying because of seasonality and increasing trends.

### Using Statistics Unit Root Tests

A statistical test makes a strong assumption about the data . Likewise, we can make some assumptions about time-series data. For this purpose, we have to check that a time-series null hypothesis has been rejected or not. So, we can elucidate the result in order for a particular problem to be meaningful. Statistical unit root tests contain the following five methods, each of which contains a unit root stationary test:

*   **Dickey-Fuller (DF) test** : This is based on linear regression. Serial correlation is a big issue of this method.

*   **Augmented Dickey-Fuller test** : This solves the serial correlation problem of a DF test and handles big and complex models. This method is widely used.

*   **The** **Schmidt-Phillips test** : This comprises the coefficients of the deterministic variables in the null and alternative hypotheses. Substitutes of this method are ro-test and tau-test.

*   **The** **Phillips-Perron (PP) test** : This test is a betterment of the Dickey-Fuller test, and it embellishes the test for autocorrelation and heteroscedasticity in the errors.

*   **KPSS test**: This is the reverse of the ADF test where the null hypothesis is the process of stationary trends and an alternative hypothesis for unit roots.

The concept behind the unit root stationary test is that the statistical property of a given time series is not constant with time. In this case, we are using this statistical test to check whether a time series is either stationary or nonstationary.

Here we’ll explain the unit root mathematical equations .

*   Mathematically, the equation denotes the following:

**Y**[**t**] **= a * Y**[**t-1**] **+ €**[**t**]

**Y**[**t-1**] **= a * y**[**t-2**] **+ €**[**t-1**]

where Y[t] is a time instance of t, and €[t] is an error term. To calculate Y[t], we have to calculate Y[t-1.]

*   For all observations, the following applies:

**Y**[**t**] **= a**^(**n**) *** y**[**t-n**] **+ €**[**t-1**] *** a**^(**i**)

*   If the amount of a contains 1 in the previous formulation, the predication will be Yt-n, and it is a sum of all errors for t-n to t (this indicates that variance is increasing with time). This is known as a *unit root* in the domain of time series.

*   For a stationary time series, a variance must not be the functional part of the time. Therefore, unit root tests check for the existence of a unit root in the time series by monitoring the amount of a = 1.

Next you will learn how to interpret p-value.

## Interpreting the P-value

In the unit root stationary test, we have to deal with the null hypothesis (H0), which is not the stationary and alternative hypothesis (H1), which is the stationary time series. When we perform this unit root stationary test, the results will be in p-value format. So, it is essential to understand how to interpret the p-value. The p-value helps to determine the significance of the outcome.

If the p-value is below the threshold, then we reject the null hypothesis, which means that the time series is stationary. If the p-value exceeds the threshold and we fail to reject the null hypothesis, it means that the time series is nonstationary. An ADF test looks at the test statistic, the p-value, and the critical values found at 1%, 2.5%, 5%, and 10% confidence intervals.

The following shows that if the significance level is set to 0.05, then the corresponding confidence level is 95%:

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figa_HTML.gif](images/492113_1_En_4_Chapter/492113_1_En_4_Figa_HTML.gif)

## Augmented Dickey-Fuller Test

The augmented Dickey-Fuller (ADF) test is a notable and plausible statistical test for stationary checking. It can be utilized to decide on the existence of the unit root in a domain of the series, and in addition, it helps us to understand whether the time series is stationary. The ADF test includes the null hypothesis (H0) that the unit root is present in a time series. The alternative hypothesis is utilized for the stationary version of the time series. It is an augmented version of the Dickey-Fuller test and is best used for a large and more complex set of time-series data.

This test uses a negative number. The more negative the number is, the higher the chance of rejecting the hypothesis that there is a unit root at some level of confidence.

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figb_HTML.png](images/492113_1_En_4_Chapter/492113_1_En_4_Figb_HTML.png)

## Kwiatkowski-Phillips-Schmidt-Shin Test

Kwiatkowski-Phillips-Schmidt-Shin (KPSS) tests are utilized to examine the null hypothesis that the time series is stationary around a conclusive trend (i.e., trend stationary) compared to the alternative of a unit root. The presence of a unit root is not the null hypothesis but the alternative hypothesis, which is a reverse form of the ADF test. Furthermore, in a KPSS test, the nonappearance of a unit root is not proof of stationarity but, by design, of trend stationarity. This is mostly utilized for trend stationary tests. *Trend stationary* data means a series that has no unit root but shows a trend.

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figc_HTML.png](images/492113_1_En_4_Chapter/492113_1_En_4_Figc_HTML.png)

Here is an interpretation of a KPSS test:

The test statistic, the p-value, and the critical value were found at 1%, 2.5%, 5%, and 10% confidence intervals.

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figd_HTML.png](images/492113_1_En_4_Chapter/492113_1_En_4_Figd_HTML.png)

## Using Stationary Data Techniques

In the previous section, you learned how to interpret p-values with the help of ADF tests and KPSS tests; in this section, let’s look at various ways of making data stationary.

### Differencing

*Differencing* (lag difference) is the process of transforming a time series to stabilize the mean. We have several ways to identify the time series, such as a line plot, which represents the series over time. Within these trends, seasonality and random walk can be observed, which is a change over a period of time, and this behavior is considered a nonstationary time series. The clear rule states that it needs to remove trends and seasonality in the data from the training time series forecasting model.

The following are the reasons for differencing:

*   To convert nonstationary data into a stationary time series

*   To remove seasonal trends
    *   Take the fourth difference for quarterly data

    *   Take the 12^(th) difference for monthly data

### Random Walk

A random walk is the behavior when the time series shows irregular growth. When the growth is not constant over time, it is better to predict the change that occurs from one period (Y[t]) to the next (Y[t-1]). In a different timestamp, moving from left to right, the movement that is independent of the variable takes a random step up or down. A so-called random walk illustrates that time-series future steps or directions can vary in a stochastic manner (Figure [4-3](#492113_1_En_4_Chapter.xhtml#Fig3)).

![../images/492113_1_En_4_Chapter/492113_1_En_4_Fig3_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Fig3_HTML.jpg)

Figure 4-3

Random walk one-dimension representation

Here are the steps of random walks :

1.  Start with a random walk from ***t***[***0***.]

2.  Randomly select the next movement, ***t***[***n-1***].

3.  Repeat step 2.

### First-Order Differencing (Trend Differencing)

So, a change between two consecutive observations in the time series can be written as follows:

**Y’**[**t**] **= Y**[**t**] **- Y**[**t-1**]

When the differenced series contains white noise (εt), the formula can be written as follows:

**Y**[**t**] **- Y**[**t-1**] **=** εt

**Y**[**t**] **= Y**[**t-1**] **+** εt

where εt = white noise. This is known as a *normal random walk* .

A random walk represents that a time series is nonstationary. This is mostly seen in financial, economic, and microeconomics data. A random walk has mostly long durations of trend ups and down and uncertain and unpredictable changes. Figure [4-4](#492113_1_En_4_Chapter.xhtml#Fig4) illustrates the random walk behavior of a stock price .

![../images/492113_1_En_4_Chapter/492113_1_En_4_Fig4_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Fig4_HTML.jpg)

Figure 4-4

Random walk behavior of Infosys Ltd Stock price (source: Money Control)

For instance, we can see that the stock price of Infosys Ltd. has gone up and down for the last six months. This is random walk behavior. Any future moment is not predictable because of the haphazard ups and downs.

So, here it is clear that the random time series has nonzero mean values. In that case, the final formula is known as *random walk with drift*, as shown here:

**Y**[**t**] **= c + Y**[**t-1**] **+ ε**[**t**]

where c is the average of change between two observations.

*   If c is positive, then the average change will rise in *Y*[*t*], and Y[t] will move upward.

*   If c is negative, the *Y*[*t*] value will move downward.

**Notes:**

*   First-order differencing in a time series will remove a linear trend (i.e., differences=1).

*   Second-order differencing will remove a quadratic trend (i.e., differences=2).

*   In addition, first-order differencing in a time series at a lag equal to the period will remove a seasonal trend.

### Second-Order Differencing (Trend Differencing)

Second-order differencing is a technique used to make first-order differencing data stationary when the first-order differencing has failed. So**,** it’s necessary to apply second-order differencing to obtain a stationary series.

**Y”**[**t**] **= Y’**[**t**] **– Y’**[**t-1**]

**= (Y**[**t**] **- Y**[**t-1**]**) – (Y**[**t-1**] **- Y**[**t-2**]**)**

**= Y**[**t**] **- 2Y**[**t-1**] **+ Y**[**t-2**]

This is second-order differencing, Y”, which has t-2 values. This is known as *double changes* in the original series.

### Seasonal Differencing

A *seasonal difference* is the difference between an observation and the corresponding observation from the previous year.

#### First-Order Differencing for Seasonal Data

Seasonal differencing is similar to first-order differencing in that we are calculating current and previous observation differences for the same season. It can be written as follows:

**Y’**[**t**] **= Y**[**t**] **- Y**[**t-s**]

where s is the number of seasons, which is known as *lag-s* differencing. We can make a difference in observation after s-lag periods.

If we observed white noise in seasonal differencing, then the model can be written as follows:

**Y**[**t**] **= Y**[**t-s**] **+** εt

When we try to forecast the value with this approach, it is equal to the last observation from that season. This model gives seasonal naïve forecasting results.

#### Second-Order Differencing for Seasonal Data

To classify the seasonal differences from regular differences, we sometimes allude to average differences as *first differences*, which signifies a difference at lag1\. In the real world, it tends to occur after applying the first-order differencing. A time series may not get the state of the stationary series, so in that event, it is vital to use second-order differencing, with differencing specified as lag2\. It looks like this:

**Y”**[**t**] **= Y’**[**t**] **– Y’**[**t-s**]

**= (Y**[**t**] **- Y**[**t-s**]**) – (Y**[**t-1**] **- Y**[**t-s-1**]**)**

**= Y**[**t**] **- Y**[**t-1**] **- Y**[**t-s**] **+ Y**[**t-s-1**]

When applying regular and first-order differences, there are no distinctions from second-order that are done first. The outcome will be the same as first-order differencing.

## Autoregressive Models

An autoregression model (AR) predicts future behavior based on its past data. It is when data is correlated with a consecutive sequence of a time series and the values before and after the sequence. The *autoregressive model* uses only past behavior data to forecast the value.

AR models use past values to forecast as shown here:

**Ŷt = μ + ϕ**[**1**]**Y**[**t-1 +**] **ϕ**[**2**]**Y**[**t-2 ... +**] **ϕ**[**p**]**Y**[**t-p**] **+ ɛ**[**t**]

where ɛ[t] is white noise. This model is known as the AR(p) model, where p is the order for the autoregressive model. The AR model is easy to use to handle a wide range of time-series models. Figure [4-5](#492113_1_En_4_Chapter.xhtml#Fig5) illustrates AR (1) and AR (2) behavior.

![../images/492113_1_En_4_Chapter/492113_1_En_4_Fig5_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Fig5_HTML.jpg)

Figure 4-5

Plot after autoregressive 1 and 2

Table [4-1](#492113_1_En_4_Chapter.xhtml#Tab1) shows the AR (1) model.

Table 4-1

ϕ and u Values’ Influence on Data

| ϕ Value | u Value | Result |
| --- | --- | --- |
| 0 | - | White noise |
| 1 | 0 | Equivalent to a random walk |
| 1 | Not 0 | Similar to a random walk with drift |
| < 0 | - | Oscillates around the mean |

Autoregressive models are restricted to stationary data, and some parameters are required, as defined here:

*   **AR (1) model:** -1 < ϕ[1] < 1

*   **AR (2) model:** -1 < ϕ[1] < 1, ϕ[1] + ϕ[2] < 1, ϕ[1] - ϕ[2] < 1

## Moving Average

A *moving average* (MA) is a method to get all the trends in a time series. It is the average of any subcategory of numbers. It is utilized for long-term forecasting trends. Basically, a moving average forecasts future points by using an average of several past data points.

The moving average model practices past forecast errors:

Ŷt = μ[0]+ εt – ω[1] ε[t-2] - ω[1] ε[t-2 …] ω[q] ε[t-q]

where ɛ[t] is white noise. This model is known as the MA(q) model, where q is ordered for the moving average model. The MA model is easy to use to handle a wide range of time-series models. Figure [4-6](#492113_1_En_4_Chapter.xhtml#Fig6) illustrates MA (1) and MA (2) behavior.

![../images/492113_1_En_4_Chapter/492113_1_En_4_Fig6_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Fig6_HTML.jpg)

Figure 4-6

Plot after moving averages 1 and 2

The weight value has impact on the behavior of the time series. Table [4-2](#492113_1_En_4_Chapter.xhtml#Tab2) provides the different variations in the weight and its impact on the model.

Table 4-2

ω Value’s Influence on Data

| ω value | Result |
| --- | --- |
| &#124; ω &#124; > 1 | Weights increase as the lags increase. The more distance in the data, the greater the influence on the current error. |
| &#124; ω &#124; = 1 | Weights are persistent in size, and the distant data has the same influence as the recent data. |
| &#124; ω &#124; < 1 | The most recent data has a higher weight than data from the more distant past. |
| < 0 | Oscillates around the mean. |

*   Here is the MA (1) model: -1 < ω[1] < 1

*   Here is the MA (2) model: -1 < ω [1] < 1, ω [1] + ω [2] < 1, ω [1] - ω [2] < 1

    **Note** Here are some definitions you should understand:

    **ACF:** The correlation of a variable with its lagged values.

    **PAC:** The correlation of a variable with its lagged values, but after removing the effects of in-between time lags.

    **Correlograms:** The plots of ACF and PACF against the lag length. This can give you an idea about the relation of autocorrelation between variables.

## Autocorrelation and Partial Autocorrelation Functions

An *autocorrelation function* (ACF) is a method to determine the linear relationship between time t and t-1\. After checking the ACF, helps in determining if differencing is required or not.

The data is independent in the regression model but fails when it is gathered over time, and a regression algorithm fails to catch any time trends. In this situation, the random error is positively correlated with time. So, each random error is more gradual to be similar to the last random error. This incident is called *autocorrelation* (or *serial correlation* ).

If we are using the autoregressive model AR(k), then we have to determine the only correlation between Y[t] and Y[t-1] and check for a direct (linear) influence between the random variables that lie in the time series, which requires differencing and transforming the time series. After transforming the time series, we calculate the correlation, which is known as a *partial autocorrelation function* (PACF). Figure [4-7](#492113_1_En_4_Chapter.xhtml#Fig7) shows a plot of each function.

![../images/492113_1_En_4_Chapter/492113_1_En_4_Fig7_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Fig7_HTML.jpg)

Figure 4-7

Representation of ACF and PACF plots

Say we have time-series Y[t], which represents a Y measure over time t. The autoregressive model is when a value of the time-series data is regressed on the previous value. For instance, here is the equation for Y[t] on Y[t-1]:

Y[t] = μ + ϕ[1]Y[t-1] + ɛ

The coefficient of correlation among two values in time-series data is known as the ACF. For instance, the ACF for the time-series Y[t] given is as follows:

**Corr(Y**[**t,**]**Y**[**t-1**]**), k = 1,2,…**

The value of k is the time gap considering the lag. The lag 1(k=1) autocorrelation is a correlation between values that are one time period away from each other.

## Introduction to ARMA

ARMA uses information obtained from the variable itself to forecast its trend, and the variable is regressed on its own past values (Table [4-3](#492113_1_En_4_Chapter.xhtml#Tab3)).

Table 4-3

ARMA Estimating Those Modes

| Model | ACF Pattern | PACF Pattern |
| --- | --- | --- |
| AR(p) | Exponential decay or damped sine wave pattern or both | Significant spike through first lag |
| MA(q) | Significant spike through first lag | Exponential decay |
| ARMA (1,1) | Exponential decay from lag 1 | Exponential decay from lag 1 |
| ARMA (p,q) | Exponential decay | Exponential decay |

Here are the steps to identify whether data is showing an AR or MA signature:

1.  Plot or use an ADF test to check whether a series is stationary.

2.  If a time series does not have a stationary difference, check for stationary.

3.  Plot ACF and PACF and use Table [4-3](#492113_1_En_4_Chapter.xhtml#Tab3) to determine p and q for the model.

ARIMA has three components: the autoregression part (AR), the integration part (I), and the moving average part (MA).

### Autoregressive Model

The autoregressive model is a lagged dependent variable, which contains an autoregressive term, which perhaps corrects on the grounds of habit resolve. AR is part of a time series Y[t], which contains a value that depends on some linear grouping of the previous value, which defined maximum lags (signified p). It also contains an arbitrary error term εt, given as follows:

First-order AR:                           **Ŷ**[**t**] **= α + b**[**1**]**Y**[**t-1**]

Second-order AR:              **Ŷ**[**t**]**= α + b**[**1**]**Y**[**t-1**] **+ b**[**2**]**Y**[**t-2**]

Third-order AR:            **Ŷ**[**t**]**= α + b**[**1**]**Y**[**t-1**] **+ b**[**2**]**Y**[**t-2**] **+ b**[**3**]**Y**[**t-3**]

**y**[**t**] **= φ**[**1**]**y**[**t-1**] **+ φ**[**2**]**y**[**t-2**] **+ …… + φ**[**p**]**y**[**t-p**] **+ ε**[**t**]

where the parameters φ[t] are constants.

### Moving Average

The MA part of a time-series Y[t], which is an observed value in terms of a random error and some linear grouping of previous arbitrary error terms, up to a described maximum lag (signified q).

First order MA(1):                        **Ŷ**[**t**] **= γ+ d**[**0**]**u**[**t**] **+ d**[**1**]**u**[**t-1**]

Second order MA(2):             **Ŷ**[**t**] **= γ+ d**[**0**]**u**[**t**] **+ d**[**1**]**u**[**t-1**] **+ d**[**2**]**u**[**t-2**]

Third order MA(2):            **Ŷ**[**t**] **= γ+ d**[**0**]**u**[**t**] **+ d**[**1**]**u**[**t-1**] **+ d**[**2**]**u**[**t-2**]**+ d**[**3**]**u**[**t-3**]

**y**[**t**] **= Z**[**t**] **+ θ**[**1**]**Z**[**t-1**] **+ θ**[**2**]**Z**[**t-2**] **+ …… + θ**[**q**]**Z**[**t-q**]

where the parameters θ[t] are constants.

**let’s combine AR and MA**

**ARMA (1,1):               Ŷt = μ + ϕ**[**1**]**Y**[**t-1**]**+ d**[**0**]**u**[**t**]**+ d**[**1**]**u**[**t-1**]

## Introduction to Autoregressive Integrated Moving Average

Autoregressive integrated moving average—also called ARIMA(p,d,q)—is a forecasting equation that can make time series stationary with the help of differencing and log techniques when required. A time series that should be differentiated to be stationary is an integrated (d) (I) series. Lags of the stationary series are classified as autoregressive (p), which is designated in (AR) terms. Lags of the forecast errors are classified as moving averages (q), which are identified in (MA) terms.

![../images/492113_1_En_4_Chapter/492113_1_En_4_Fige_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Fige_HTML.jpg)

A nonseasonal ARIMA model is called an ARIMA(p,d,q) model, where:

*   **p** is the number of autoregressive terms.

*   **d** is the number of nonseasonal differences needed for stationarity.

*   **q** is the number of lagged forecast errors in the prediction equation.

Table [4-4](#492113_1_En_4_Chapter.xhtml#Tab4) shows the different ARIMA model behavior with p, d, and q.

Table 4-4

Representation of p, d, q and Its Relevant Methods

|   | p | d | q | Differencing | Method |
| --- | --- | --- | --- | --- | --- |
| ARIMA (0, 0, 0) | 0 | 0 | 0 | y[t] = Y[t] | White noise |
| ARIMA (0, 1, 0) | 0 | 1 | 0 | y[t] = Y[t] - Y[t-1] | Random walk |
| ARIMA (0, 2, 0) | 0 | 2 | 0 | y[t] = Yt - 2Yt-1 + Yt-2 | Constant |
| ARIMA (1, 0, 0) | 1 | 0 | 0 | Yˆt = μ + ϕ[1]Y[t-1] + ɛ | AR(1): First-order regression model |
| ARIMA (2, 0, 0) | 2 | 0 | 0 | Yˆt = ϕ[0] + ϕ[1]Y[t-1] + ϕ[2]Y[t-2] + ɛ | AR(2): Second-order regression model |
| ARIMA (1, 1, 0) | 1 | 1 | 0 | Yˆt = μ + Yt-1 + ϕ1 (Yt-1 - Yt-2) | Differenced first-order autoregressive model |
| ARIMA (0, 1, 1) | 0 | 1 | 1 | Yˆt = Yt-1 - ϕ1et-1 | Simple exponential smoothing |
| ARIMA (0, 0, 1) | 0 | 0 | 1 | Yˆt = μ[0]+ ɛt – ω[1] ɛ[t-1] | MA(1): First-order regression model |
| ARIMA (0, 0, 2) | 0 | 0 | 2 | Yˆt = μ[0]+ ɛt – ω[1] ɛ[t-1] – ω[2] ɛ[t-2] | MA(1): Second-order regression model |
| ARIMA (1, 0, 1) | 1 | 0 | 1 | Yˆt = ϕ[0] + ϕ[1]Y[t-1]+ ɛt – ω[1] ɛ[t-1] | ARMA model |
| ARIMA (1, 1, 1) | 1 | 1 | 1 | ΔY[t] = ϕ[1]Y[t-1] + ɛt - ω[1] ɛ[t-1] | ARIMA model |
| ARIMA (1, 1, 2) | 1 | 1 | 2 | Yˆ[t] = Y[t-1] + ϕ[1] (Y[t-1] - Y[t-2] ) - θ[1]e[t-1] - θ[1]e[t-1] | Damped-trend linearExponential smoothing |
| ARIMA (0, 2, 1) OR (0,2,2) | 0 | 2 | 1 | Yˆ[t] = 2 Y[t-1] - Y[t-2] - θ[1]e[t-1] - θ[2]e[t-2] | Linear exponential smoothing |

ARIMA is a method among several used for forecasting univariate variables, which uses information obtained from the variable itself to predict its trend. The variables are regressed on its own past values.

AR(p) is where p equals the order of autocorrelation (designates weighted moving average over past observations) z I (d), where d is the order of integration (differencing), which indicates linear trend or polynomial trend z.

MA(q) is where q equals the order of moving averages (designates weighted moving average over past errors).

ARIMA is made up of two models: AR and MA.

### The Integration (I)

Time-series data is often nonstationary, and to make time-series stationary, the series needs to be differentiated. This process is known as the *integration* part (I), and the order of differencing is signified as d. Differencing eradicates signals with time, which contains trends and seasonality, so this series contains noise and an irregular component, which will be modeled only.

Table [4-5](#492113_1_En_4_Chapter.xhtml#Tab5) illustrates how d(I) can be articulated algebraically.

Table 4-5

Interpreting d(I) Value

| Integral d Value | Formula(y[t]) |
| --- | --- |
| d = 0 | **Y**[**t**] |
| d = 1 | **Y**[**t**] **- Y**[**t-1**] |
| d = 2 | **Y**[**t**] **- 2Y**[**t-1**] **+ Y**[**t-2**] |

### ARIMA in Action

In the previous section, you learned about AR and MA and the interpretation of AR, I, and MA. Now let’s implement it on a time-series dataset.

Import the required libraries and load the CSV data, which is Facebook stock data from 2014 to 2019.

*   **Pmdarima** (for py + arima) is a statistical library designed to fill the void in Python’s time-series analysis capabilities, which is the equivalent of R’s `auto.arima`.

```
import warnings
import matplotlib.pyplot as plt
import numpy as np
import pandas as pd
import statsmodels.api as sm
from pmdarima import auto_arima
from sklearn import metrics
from statsmodels.tsa.seasonal import seasonal_decompose
from statsmodels.tsa.stattools import adfuller
warnings.filterwarnings("ignore")
df = pd.read_csv(r'\Data\FB.csv', parse_dates = True)
```

Let’s take a sneak peek at the Facebook stock data.

```
df.head(10)
```

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figf_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figf_HTML.jpg)

Perform some basic exploratory data analysis using line, histogram, and [kernel density estimation](https://en.wikipedia.org/wiki/Kernel_density_estimation) of the closing price of stocks. Exploratory data analysis is the process of using a graphical representation to discover and investigate patterns within data.

Kernel density estimation (KDE) is a nonparametric way to estimate the probability density function (PDF) of a random variable.

```
df["Close"].plot(figsize=(15, 6))
plt.xlabel("Date")
plt.ylabel("Close")
plt.title("Closing price of Facebook stocks")
plt.show()
```

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figg_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figg_HTML.jpg)

```
plt.figure(1, figsize=(15,6))
plt.subplot(211)
df["Close"].hist()
plt.subplot(212)
df["Close"].plot(kind='kde')
plt.show()
```

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figh_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figh_HTML.jpg)

Define the time-series evaluation function as follows:

```
def timeseries_evaluation_metrics_func(y_true, y_pred):
def mean_absolute_percentage_error(y_true, y_pred):
y_true, y_pred = np.array(y_true), np.array(y_pred)
return np.mean(np.abs((y_true - y_pred) / y_true)) * 100
print('Evaluation metric results:-')
print(f'MSE is : {metrics.mean_squared_error(y_true, y_pred)}')
print(f'MSE is : {metrics.mean_absolute_error(y_true, y_pred)}')
print(f'RMSE is : {np.sqrt(metrics.mean_squared_error(y_true, y_pred))}')
print(f'MAPE is : {mean_absolute_percentage_error(y_true, y_pred)}')
print(f'R2 is : {metrics.r2_score(y_true, y_pred)}',end='\n\n')
```

Here is the ADF test function to check for stationary data:

```
def Augmented_Dickey_Fuller_Test_func(series , column_name):
print (f'Results of Dickey-Fuller Test for column: {column_name}')
dftest = adfuller(series, autolag='AIC')
dfoutput = pd.Series(dftest[0:4], index=['Test Statistic','p-value','No Lags Used','Number of Observations Used'])
for key,value in dftest[4].items():
dfoutput['Critical Value (%s)'%key] = value
print (dfoutput)
if dftest[1] ")
print("Reject the null hypothesis")
print("Data is stationary")
else:
print("Conclusion:====>")
print("Fail to reject the null hypothesis")
print("Data is non-stationary")
```

We can see that Close is nonstationary, and auto-arima handles this internally.

```
Augmented_Dickey_Fuller_Test_func(df['Close' ],'Close')
```

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figi_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figi_HTML.jpg)

Model training will be done only for the Close column from the dataset. Make a copy of the data, and let’s perform the test/train split.

The train will have all the data except the last 30 days, and the test will contain only the last 30 days to evaluate against predictions.

```
X = df[['Close' ]]
train, test = X[0:-30], X[-30:]
```

The pmdarima module will help us identify p, d, and q without the hassle of looking at the plot.

```
stepwise_model = auto_arima(train,start_p=1, start_q=1,
max_p=7, max_q=7, seasonal=False,
d=None, trace=True,error_action='ignore',suppress_warnings=True, stepwise=True)
stepwise_model.summary()
```

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figj_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figj_HTML.jpg)

Auto-arima says ARIMA(1,1,1) is the optimal selection for the dataset.

Forecast both results and the confidence for the next 30 days and store it in a DataFrame, as shown here:

```
forecast,conf_int = stepwise_model.predict(n_periods=30,return_conf_int=True)
forecast = pd.DataFrame(forecast,columns=['close_pred'])
df_conf = pd.DataFrame(conf_int,columns= ['Upper_bound','Lower_bound'])
df_conf["new_index"] = range(1229, 1259)
df_conf = df_conf.set_index("new_index")
timeseries_evaluation_metrics_func(test, forecast)
```

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figk_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figk_HTML.jpg)

Rearrange the indexes for the plots to align, as shown here:

```
forecast["new_index"] = range(1229, 1259)
forecast = forecast.set_index("new_index")
```

Plot the results with confidence bounds, as shown here:

```
import matplotlib.pyplot as plt
get_ipython().run_line_magic('matplotlib', 'inline')
plt.rcParams["figure.figsize"] = [15,7]
plt.plot( train, label='Train ')
plt.plot(test, label='Test ')
plt.plot(forecast, label='Predicted ')
plt.plot(df_conf['Upper_bound'], label='Confidence Interval Upper bound ')
plt.plot(df_conf['Lower_bound'], label='Confidence Interval Lower bound ')
plt.legend(loc='best')
plt.show()
```

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figl_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figl_HTML.jpg)

Here is a diagnostic plot:

```
stepwise_model.plot_diagnostics();
```

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figm_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figm_HTML.jpg)

## Introduction to Seasonal ARIMA

Seasonal ARIMA (SARIMA) is a technique of ARIMA, where the seasonal component can be handled in univariate time-series data. It adjoins three new hyperparameters to lay down AR(P), I(D), and MA(Q) for the seasonality component of a time series. SARIMA allows for the occurrence of seasonality in a series.

The seasonal ARIMA model combines both nonseasonal and seasonal components in a multiplicative model. The notation can be defined as follows:

**ARIMA (p, d, q) X (P, D, Q)**[**m**]

m is the number of observations per year.

Three trend elements need to be configured. It is same as the ARIMA model, as shown here:

(p, d, q) is a nonseasonal component, as shown here:

*   **p**: Trend autoregressive order

*   **d**: Trend differencing order

*   **q**: Trend moving average order

(P, Q, D) is a seasonal component.

There are four seasonal components that are not part of the ARIMA model that are essential to be configured.

*   **P**: Seasonal autoregressive order

*   **D**: Seasonal differencing order

*   **Q**: Seasonal moving average order

*   **m**: Timestamp for single-season order

Without differencing operations, the model could be written as described next.

The seasonal part of the model contains terms that are like nonseasonal components of the model but that involve backshifts of the seasonal period. Here’s an example:

**ARIMA (1,1,1) X (1,1,1)** [**m**]

**(1−ϕ**[**1**]**B) (1−Φ**[**1**]**B**^(**m**)**) (1−B) (1−B**^(**m**)**) y**[**t**] **= (1+θ**[**1**]**B) (1+Θ**[**1**]**B**^(**m**)**) ε**[**t**].

Table [4-6](#492113_1_En_4_Chapter.xhtml#Tab6) illustrates the nonseasonal and seasonal components of AR and MA.

Table 4-6

Interpreting Nonseasonal and Seasonal Components

| Nonseasonal Component | Seasonal Component |
| --- | --- |
| AR: Φ(B) = 1- Φ[1]B^P | AR: Φ(B) = 1- Φ[1]B^(pm) |
| MA: θ(B) = 1 + θ[1]B^q | MA: Θ(B) = 1 + Θ[1]B^(qm) |

The additional term simply multiplies by the nonseasonal terms.

For example, ARIMA (0,0,1) X (0,0,1)[12].

*   Nonseasonal MA (1): *θ*(B) = 1 + *θ*[1]B

*   Seasonal MA (1): Θ(B) = 1 + *θ*[1]B^(12)

Here is the final model: Θ(B) x *θ*(B) = (1 + *θ*[1]B^(12)) (1 + *θ*[1]B).

![../images/492113_1_En_4_Chapter/492113_1_En_4_Fign_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Fign_HTML.jpg)

### SARIMA in Action

In the previous section, you learned about the high-level math behind SARIMA where you can perform the data modeling on seasonal components. Now let’s implement it on a time-series dataset.

```
import warnings
import matplotlib.pyplot as plt
import numpy as np
import pandas as pd
import statsmodels.api as sm
from pmdarima import auto_arima
from sklearn import metrics
from statsmodels.tsa.seasonal import seasonal_decompose
from statsmodels.tsa.stattools import adfuller
warnings.filterwarnings("ignore")
df = pd.read_csv(r'\Data\FB.csv')
```

Let’s take a sneak peek at the data.

```
df.head(10)
```

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figo_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figo_HTML.jpg)

Define the time-series evaluation function, as shown here:

```
def timeseries_evaluation_metrics_func(y_true, y_pred):
def mean_absolute_percentage_error(y_true, y_pred):
y_true, y_pred = np.array(y_true), np.array(y_pred)
return np.mean(np.abs((y_true - y_pred) / y_true)) * 100
print('Evaluation metric results:-')
print(f'MSE is : {metrics.mean_squared_error(y_true, y_pred)}')
print(f'MSE is : {metrics.mean_absolute_error(y_true, y_pred)}')
print(f'RMSE is : {np.sqrt(metrics.mean_squared_error(y_true, y_pred))}')
print(f'MAPE is : {mean_absolute_percentage_error(y_true, y_pred)}')
print(f'R2 is : {metrics.r2_score(y_true, y_pred)}',end='\n\n')
```

Here is the ADF test function to check for stationary data:

```
def Augmented_Dickey_Fuller_Test_func(series , column_name):
print (f'Results of Dickey-Fuller Test for column: {column_name}')
dftest = adfuller(series, autolag='AIC')
dfoutput = pd.Series(dftest[0:4], index=['Test Statistic','p-value','No Lags Used','Number of Observations Used'])
for key,value in dftest[4].items():
dfoutput['Critical Value (%s)'%key] = value
print (dfoutput)
if dftest[1] ")
print("Reject the null hypothesis")
print("Data is stationary")
else:
print("Conclusion:====>")
print("Fail to reject the null hypothesis")
print("Data is non-stationary")
```

We can see that Close is nonstationary, and auto-arima handles this internally, as shown here:

```
Augmented_Dickey_Fuller_Test_func(df['Close' ],'Close')
```

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figp_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figp_HTML.jpg)

Modeling will be done only for the Close column from the dataset. Please make a copy of the data, and let’s perform the test train split.

The train will have all the data except the last 30 days, and the test will contain only the last 30 days to evaluate against the predictions.

```
X = df[['Close' ]]
train, test = X[0:-30], X[-30:]
```

Let’s configure and run seasonal arima for the parameters given in the `for` loop and check the optimal number of periods in each season suitable for our dataset.

Here is the search space:

*   **p** → 1 to 7.

*   **q** → 1 to 7.

*   **d** → None means find the optimal value.

*   **P** → 1 to 7.

*   **Q** → 1 to 7.

*   **D** → None means find the optimal value.

m refers to the number of periods in each season.

*   **7** → Daily

*   **12** → Monthly

*   **52** → Weekly

*   **4** → Quarterly

*   **1** → Annual (non-seasonal)

```
for m in [1, 4,7,12,52]:
print("="*100)
print(f' Fitting SARIMA for Seasonal value m = {str(m)}')
stepwise_model = auto_arima(train, start_p=1, start_q=1,
max_p=7, max_q=7, seasonal=True, start_P=1, start_Q=1, max_P=7, max_D=7, max_Q=7, m=m,
d=None, D=None, trace=True, error_action='ignore', suppress_warnings=True, stepwise=True)
print(f'Model summary for  m = {str(m)}')
print("-"*100)
stepwise_model.summary()
forecast ,conf_int= stepwise_model.predict(n_periods=30,return_conf_int=True)
df_conf = pd.DataFrame(conf_int,columns= ['Upper_bound','Lower_bound'])
df_conf["new_index"] = range(1229, 1259)
df_conf = df_conf.set_index("new_index")
forecast = pd.DataFrame(forecast, columns=['close_pred'])
forecast["new_index"] = range(1229, 1259)
forecast = forecast.set_index("new_index")
timeseries_evaluation_metrics_func(test, forecast)
import matplotlib.pyplot as plt
get_ipython().run_line_magic('matplotlib', 'inline')
plt.rcParams["figure.figsize"] = [15, 7]
plt.plot(train, label='Train ')
plt.plot(test, label='Test ')
plt.plot(forecast, label=f'Predicted with m={str(m)} ')
plt.plot(df_conf['Upper_bound'], label='Confidence Interval Upper bound ')
plt.plot(df_conf['Lower_bound'], label='Confidence Interval Lower bound ')
plt.legend(loc='best')
plt.show()
print("-"*100)
print(f' Diagnostic plot for Seasonal value m = {str(m)}')
display(stepwise_model.plot_diagnostics());
print("-"*100)
```

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figq_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figq_HTML.jpg)

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figr_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figr_HTML.jpg)

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figs_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figs_HTML.jpg)

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figt_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figt_HTML.jpg)

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figu_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figu_HTML.jpg)

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figv_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figv_HTML.jpg)

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figw_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figw_HTML.jpg)

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figx_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figx_HTML.jpg)

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figy_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figy_HTML.jpg)

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figz_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figz_HTML.jpg)

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figaa_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figaa_HTML.jpg)

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figab_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figab_HTML.jpg)

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figac_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figac_HTML.jpg)

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figad_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figad_HTML.jpg)

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figae_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figae_HTML.jpg)

After checking for different m values, we can see that m does not have any influence on the results.

## Introduction to SARIMAX

The SARIMAX model is a SARIMA model with external influencing variables, called SARIMAX (p, d, q) X(P,D,Q)[m] (X), where X is the vector of exogenous variables. The exogenous variables perhaps modeled by the multilinear regression equation are articulated as follows:

(1−ϕ[1]B) (1−Φ[1]B^m) (1−B) (1−B^m) y[t] = (1+θ[1]B) (1+Θ[1]B^m) ε[t] (X[k],[t]).

where X[1,], X[2,𝑡], …. X[𝑘,] are observations of k number of exogenous variables corresponding to the dependent variable.

### SARIMAX in Action

In the previous section, you learned about the high-level math behind SARIMAX. Now let’s implement it on a time-series dataset.

```
import warnings
import matplotlib.pyplot as plt
import numpy as np
import pandas as pd
import statsmodels.api as sm
from pmdarima import auto_arima
from sklearn import metrics
from statsmodels.tsa.seasonal import seasonal_decompose
from statsmodels.tsa.stattools import adfuller
warnings.filterwarnings("ignore")
df = pd.read_csv(r'\Data\FB.csv')
```

Let’s take a sneak peek at the data.

```
df.head(10)
```

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figaf_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figaf_HTML.jpg)

Define the time-series evaluation function, as shown here:

```
def timeseries_evaluation_metrics_func(y_true, y_pred):
def mean_absolute_percentage_error(y_true, y_pred):
y_true, y_pred = np.array(y_true), np.array(y_pred)
return np.mean(np.abs((y_true - y_pred) / y_true)) * 100
print('Evaluation metric results:-')
print(f'MSE is : {metrics.mean_squared_error(y_true, y_pred)}')
print(f'MSE is : {metrics.mean_absolute_error(y_true, y_pred)}')
print(f'RMSE is : {np.sqrt(metrics.mean_squared_error(y_true, y_pred))}')
print(f'MAPE is : {mean_absolute_percentage_error(y_true, y_pred)}')
print(f'R2 is : {metrics.r2_score(y_true, y_pred)}',end='\n\n')
```

Here is the ADF test function to check for stationary data:

```
def Augmented_Dickey_Fuller_Test_func(series , column_name):
print (f'Results of Dickey-Fuller Test for column: {column_name}')
dftest = adfuller(series, autolag='AIC')
dfoutput = pd.Series(dftest[0:4], index=['Test Statistic','p-value','No Lags Used','Number of Observations Used'])
for key,value in dftest[4].items():
dfoutput['Critical Value (%s)'%key] = value
print (dfoutput)
if dftest[1] ")
print("Reject the null hypothesis")
print("Data is stationary")
else:
print("Conclusion:====>")
print("Fail to reject the null hypothesis")
print("Data is non-stationary")
```

We can see that Close is nonstationary and auto-arima handles this internally.

```
for name, column in df[['Close' ,'Open' ,'High','Low']].iteritems():
Augmented_Dickey_Fuller_Test_func(df[name],name)
print('\n')
```

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figag_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figag_HTML.jpg)

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figah_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figah_HTML.jpg)

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figai_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figai_HTML.jpg)

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figaj_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figaj_HTML.jpg)

Modeling will be done only for the Close column from the dataset, and Open will be used as exogenous variables. Please make a copy of the data, and let’s perform the test train split.

The train will have all the data except the last 30 days, and the test will contain only the last 30 days to evaluate against predictions.

```
X = df[['Close' ]]
actualtrain, actualtest = X[0:-30], X[-30:]
exoX = df[['Open' ]]
exotrain, exotest = exoX[0:-30], exoX[-30:]
```

Let’s configure and run seasonal arima with an exogenous variable.

```
for m in [1, 4,7,12,52]:
print("="*100)
print(f' Fitting SARIMAX for Seasonal value m = {str(m)}')
stepwise_model = auto_arima(actualtrain,exogenous =exotrain ,start_p=1, start_q=1,max_p=7, max_q=7, seasonal=True,
start_P=1,start_Q=1,max_P=7,max_D=7,max_Q=7,m=m, d=None,D=None,
trace=True,error_action='ignore',suppress_warnings=True, stepwise=True)
print(f'Model summary for  m = {str(m)}')
print("-"*100)
stepwise_model.summary()
forecast,conf_int = stepwise_model.predict(n_periods=30,exogenous =exotest,return_conf_int=True)
df_conf = pd.DataFrame(conf_int,columns= ['Upper_bound','Lower_bound'])
df_conf["new_index"] = range(1229, 1259)
df_conf = df_conf.set_index("new_index")
forecast = pd.DataFrame(forecast, columns=['close_pred'])
forecast["new_index"] = range(1229, 1259)
forecast = forecast.set_index("new_index")
timeseries_evaluation_metrics_func(actualtest, forecast)
import matplotlib.pyplot as plt
get_ipython().run_line_magic('matplotlib', 'inline')
plt.rcParams["figure.figsize"] = [15, 7]
plt.plot(actualtrain, label='Train ')
plt.plot(actualtest, label='Test ')
plt.plot(forecast, label=f'Predicted with m={str(m)} ')
plt.plot(df_conf['Upper_bound'], label='Confidence Interval Upper bound ')
plt.plot(df_conf['Lower_bound'], label='Confidence Interval Lower bound ')
plt.legend(loc='best')
plt.show()
print("-"*100)
print(f' Diagnostic plot for Seasonal value m = {str(m)}')
display(stepwise_model.plot_diagnostics());
print("-"*100)
```

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figak_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figak_HTML.jpg)

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figal_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figal_HTML.jpg)

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figam_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figam_HTML.jpg)

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figan_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figan_HTML.jpg)

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figao_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figao_HTML.jpg)

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figap_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figap_HTML.jpg)

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figaq_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figaq_HTML.jpg)

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figar_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figar_HTML.jpg)

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figas_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figas_HTML.jpg)

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figat_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figat_HTML.jpg)

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figau_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figau_HTML.jpg)

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figav_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figav_HTML.jpg)

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figaw_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figaw_HTML.jpg)

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figax_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figax_HTML.jpg)

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figay_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figay_HTML.jpg)

We can see that the exogenous variable open is contributing to increased model accuracy, and we notice that m does not have any influence on prediction.

## Introduction to Vector Autoregression

*Vector autoregression* (VAR) is a stochastic process model utilized to seize the linear relation among the multiple variables of time-series data. In other words, it is a multivariate forecasting method utilized when two or more time-series variables have a strong internal relationship with each other. VAR is a bidirectional model, while others are unidirectional models. In a unidirectional model, a predictor influences the target, but not vice versa. In a bidirectional model, variables influence each other.

The normal AR(p) model equation looks like this:

**Ŷt = μ + ϕ**[**1**]**Y**[**t-1**] **+ ϕ**[**2**]**Y**[**t-2 … +**] **ϕ**[**p**]**Y**[**t-p**] **+ ɛ**[**t**]

where μ is intercepting, and ϕ[1,] ϕ[2….] ϕ[n] are the coefficient of the lags of Y. In the VAR model, every single variable is modeled as a linear grouping of its past values and the past values of other variables in the time series. If you have multiple time series, which is determined to each other. So, one variable per equation will be designed. For instance, imagine that we have two variables of a time series, Y[1,] Y[2]. We want to forecast the value of these at time (t).

Here is the VAR (1) model with two time series (Y[1] and Y[2]):

**Ŷ**[**1,t**] **= μ**[**1**] **+ ϕ**[**11**]**Y**[**1,****t-1**] **+ ϕ**[**12**]**Y**[**1,****t-2**] **+ ɛ**[**1,****t**]

**Ŷ**[**2,t**] **= μ**[**2**] **+ ϕ**[**21**]**Y**[**2,****t-1**] **+ ϕ**[**22**]**Y**[**2,****t-2**] **+ ɛ**[**2,****t**]

where y[1,t-1,] y[2,t-1] are the first lag of the time series Y[1] and Y[2.]

The VAR model follows the same rules for the design model as the univariate time series. It is utilizing the corresponding evaluation matrices such as AIC, BIC, FPE, and HQIC.

### VAR in Action

In the previous section, you learned about the high-level math behind VAR. Now let’s implement it on a time-series dataset.

```
import pandas as pd
import matplotlib.pyplot as plt
from statsmodels.tsa.api import VAR
import numpy as np
from statsmodels.tsa.stattools import adfuller
from sklearn import metrics
from timeit import default_timer as timer
import warnings
warnings.filterwarnings("ignore")
```

Let’s take a sneak peek at the data.

```
df = pd.read_csv(r'\Data\Dow_Jones_Industrial_Average.csv', parse_dates= True)
df = df[(df['Date'] > '2016-01-14') & (df['Date'] <= '2017-01-30')]
df.head(10)
```

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figaz_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figaz_HTML.jpg)

Perform EDA using line, histogram, and KDE plots, as shown here:

```
for c in df[['Open', 'High', 'Low', 'Close']]:
df[str(c)].plot(figsize=(15, 6))
plt.xlabel("Date")
plt.ylabel(c)
plt.title(f"{str(c)} price of Facebook stocks before stationary")
plt.show()
```

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figba_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figba_HTML.jpg)

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figbb_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figbb_HTML.jpg)

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figbc_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figbc_HTML.jpg)

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figbd_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figbd_HTML.jpg)

The following loop will plot a histogram and KDE for all the columns before making it stationary:

```
for c in df[['Open', 'High', 'Low', 'Close']]:
plt.figure(1, figsize=(15,6))
plt.subplot(211)
plt.title(f"{str(c)} Histogram before stationary")
df[str(c)].hist()
plt.subplot(212)
df[str(c)].plot(kind='kde')
plt.title(f"{str(c)} Kernal Density Estimator before stationary")
plt.show()
```

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figbe_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figbe_HTML.jpg)

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figbf_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figbf_HTML.jpg)

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figbg_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figbg_HTML.jpg)

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figbh_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figbh_HTML.jpg)

Define a time-series evaluation function, as shown here:

```
def timeseries_evaluation_metrics_func(y_true, y_pred):
def mean_absolute_percentage_error(y_true, y_pred):
y_true, y_pred = np.array(y_true), np.array(y_pred)
return np.mean(np.abs((y_true - y_pred) / y_true)) * 100
#print('Evaluation metric results:-')
print(f'MSE is : {metrics.mean_squared_error(y_true, y_pred)}')
print(f'MAE is : {metrics.mean_absolute_error(y_true, y_pred)}')
print(f'RMSE is : {np.sqrt(metrics.mean_squared_error(y_true, y_pred))}')
print(f'MAPE is : {mean_absolute_percentage_error(y_true, y_pred)}')
print(f'R2 is : {metrics.r2_score(y_true, y_pred)}',end='\n\n')
return
```

Here is the ADF test function to check for stationary data:

```
def Augmented_Dickey_Fuller_Test_func(series , column_name):
print (f'Results of Dickey-Fuller Test for column: {column_name}')
dftest = adfuller(series, autolag='AIC')
dfoutput = pd.Series(dftest[0:4], index=['Test Statistic','p-value','No Lags Used','Number of Observations Used'])
for key,value in dftest[4].items():
dfoutput['Critical Value (%s)'%key] = value
print (dfoutput)
if dftest[1] ")
print("Reject the null hypothesis")
print("Data is stationary")
else:
print("Conclusion:====>")
print("Fail to reject the null hypothesis")
print("Data is non-stationary")
```

Here is how to check whether the variables are stationary:

```
for name, column in df[['Open', 'High', 'Low', 'Close']].iteritems():
Augmented_Dickey_Fuller_Test_func(df[name],name)
print('\n')
```

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figbi_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figbi_HTML.jpg)

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figbj_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figbj_HTML.jpg)

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figbk_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figbk_HTML.jpg)

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figbl_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figbl_HTML.jpg)

Make a copy of the data, and let’s perform the test train split.

The train will have all the data except the last 30 days, and the test will contain only the last 30 days to evaluate against the predictions.

```
X = df[['Open', 'High', 'Low', 'Close' ]]
train, test = X[0:-30], X[-30:]
```

Make the data stationary by using Pandas differencing, as shown here:

```
train_diff = train.diff()
train_diff.dropna(inplace = True)
```

Check whether the variables are stationary after first differencing, as shown here:

```
for name, column in train_diff[['Open', 'High', 'Low', 'Close' ]].iteritems():
Augmented_Dickey_Fuller_Test_func(train_diff[name],name)
print('\n')
```

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figbm_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figbm_HTML.jpg)

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figbn_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figbn_HTML.jpg)

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figbo_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figbo_HTML.jpg)

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figbp_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figbp_HTML.jpg)

Create the plots after making the data stationary, as shown here:

```
for c in train_diff[['Open', 'High', 'Low', 'Close']]:
train_diff[str(c)].plot(figsize=(15, 6))
plt.xlabel("Date")
plt.ylabel(c)
plt.title(f"{str(c)} price of Facebook stocks after stationary")
plt.show()
```

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figbq_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figbq_HTML.jpg)

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figbr_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figbr_HTML.jpg)

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figbs_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figbs_HTML.jpg)![../images/492113_1_En_4_Chapter/492113_1_En_4_Figbt_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figbt_HTML.jpg)

The following loop will plot a histogram and KDE for all the columns after making it stationary:

```
for c in train_diff[['Open', 'High', 'Low', 'Close']]:
plt.figure(1, figsize=(15,6))
plt.subplot(211)
plt.title(f"{str(c)} Histogram after stationary")
train_diff[str(c)].hist()
plt.subplot(212)
train_diff[str(c)].plot(kind='kde')
plt.title(f"{str(c)} Kernal Density Estimator after stationary")
plt.show()
```

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figbu_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figbu_HTML.jpg)

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figbv_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figbv_HTML.jpg)

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figbw_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figbw_HTML.jpg)

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figbx_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figbx_HTML.jpg)

A *cointegration test* is the co-movement among underlying variables over the long run. This long-run estimation feature distinguishes it from correlation. Two or more variables are cointegrated if and only if they share common trends.

*Correlation* is simply a measure of the degree of mutual association between two or more variables.

We can see that the test says that there is the presence of a long-run relationship between features.

```
from statsmodels.tsa.vector_ar.vecm import coint_johansen
def cointegration_test(df):
res = coint_johansen(df,-1,5)
d = {'0.90':0, '0.95':1, '0.99':2}
traces = res.lr1
cvts = res.cvt[:, d[str(1-0.05)]]
def adjust(val, length= 6):
return str(val).ljust(length)
print('Column Name   >  Test Stat > C(95%)    =>   Signif  \n', '--'*20)
for col, trace, cvt in zip(df.columns, traces, cvts):
print(adjust(col), '> ', adjust(round(trace,2), 9), ">", adjust(cvt, 8), ' =>  ' , trace > cvt)
cointegration_test(train_diff[['Open', 'High', 'Low', 'Close']])
```

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figby_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figby_HTML.jpg)

Fit the VAR model for the AR term between 1 to 9 and choose the best AR component, as shown here:

```
for i in [1,2,3,4,5,6,7,8,9]:
model = VAR(train_diff)
results = model.fit(i)
print(f'Order : {i}, AIC:  {results.aic}, BIC: { results.bic}')
```

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figbz_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figbz_HTML.jpg)

To make data stationary, we used Pandas differencing after forecasting the results. We need to inverse the result to the original scale. As we don’t have a Pandas function for this, let’s define a custom function to inverse Pandas differencing .

```
def inverse_diff(actual_df, pred_df):
df_res = pred_df.copy()
columns = actual_df.columns
for col in columns:
df_res[str(col)+'_1st_inv_diff'] = actual_df[col].iloc[-1] + df_res[str(col)].cumsum()
return df_res
```

Autoregressive AR(4) appears to be providing the least Akaike Information Criterion (AIC) and Bayesian Information Criterion (BIC), so let’s fit and forecast.

AIC and BIC are two ways of scoring a model based on its log likelihood and complexity.

```
results = model.fit(4)
display(results.summary())
z = results.forecast(y=train_diff[['Open', 'High', 'Low', 'Close' ]].values, steps=30)
df_pred = pd.DataFrame(z, columns=['Open', 'High', 'Low', 'Close'  ])
```

Arrange an index for aligning plots, as shown here:

```
df_pred["new_index"] = range(233, 263)
df_pred = df_pred.set_index("new_index")
```

Let’s inverse the differenced prediction, as shown here:

```
res = inverse_diff(df[['Open', 'High', 'Low', 'Close' ]],df_pred)
```

Evaluate the results individually, as shown here:

```
for i in ['Open', 'High', 'Low', 'Close' ]:
print(f'Evaluation metric for {i}')
timeseries_evaluation_metrics_func(test[str(i)], res[str(i)+'_1st_inv_diff'])
```

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figca_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figca_HTML.jpg)

Plot the results, as shown here:

```
import matplotlib.pyplot as plt
get_ipython().run_line_magic('matplotlib', 'inline')
for i in ['Open', 'High', 'Low', 'Close' ]:
plt.rcParams["figure.figsize"] = [10,7]
plt.plot( train[str(i)], label='Train '+str(i))
plt.plot(test[str(i)], label='Test '+str(i))
plt.plot(res[str(i)+'_1st_inv_diff'], label='Predicted '+str(i))
plt.legend(loc='best')
plt.show()
```

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figcb_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figcb_HTML.jpg)

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figcc_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figcc_HTML.jpg)

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figcd_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figcd_HTML.jpg)

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figce_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figce_HTML.jpg)

## Introduction to VARMA

The VARMA model is another extension of the ARMA model for a multivariate time-series model that contains a vector autoregressive (VAR) component, as well as the vector moving average (VMA). VARMA is an inductive version of ARMA for multiple parallel time series. The ARMA notation for the model comprises the individually ordered AR(p), and the MA(q) models are the parameters to a VARMA model. For instance, VARMA(p, q) is an example. A VARMA model will be able to develop VAR or VMA models. The method is used for multivariate time-series data deprived of trend and seasonal components.

Here are the VAR (1) and VMA(1) models with two time-series datasets (Y[1] and Y[2]):

**Ŷ**[**1,t**] **= μ**[**1**] **+ ϕ**[**11**]**Y**[**1,****t-1**]**+ d**[**1,****0**]**u**[**1,****t**]**+ d**[**11**]**u**[**1,t-1**]

**Ŷ**[**2,t**] **= μ**[**2**] **+ ϕ**[**21**]**Y**[**2,****t-1**]**+ d**[**2,****0**]**u**[**1,****t**]**+ d**[**22**]**u**[**2,t-1**]

where y[1,t-1,] y[2,t-1] are the first lag of time series Y[1] and Y[2.]

The VARMA model follows the same rules for the design model similar to the univariate time series. It is utilizing the corresponding evaluation matrices such as AIC, BIC, FPE, and HQIC.

### VARMA in Action

In the previous section, you learned about the high-level math behind VARMA. Now let’s implement it on a time-series dataset.

```
import pandas as pd
import matplotlib.pyplot as plt
from statsmodels.tsa.statespace.varmax import VARMAX
import numpy as np
from statsmodels.tsa.stattools import adfuller
from sklearn import metrics
from timeit import default_timer as timer
import warnings
warnings.filterwarnings("ignore")
df = pd.read_csv(r'Data\Dow_Jones_Industrial_Average.csv', parse_dates= True)
df = df[(df['Date'] > '2016-01-14') & (df['Date'] <= '2017-01-30')]
```

Let’s take a sneak peek at the data.

```
df.head(10)
```

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figcf_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figcf_HTML.jpg)

Define a time-series evaluation function, as shown here:

```
def timeseries_evaluation_metrics_func(y_true, y_pred):
def mean_absolute_percentage_error(y_true, y_pred):
y_true, y_pred = np.array(y_true), np.array(y_pred)
return np.mean(np.abs((y_true - y_pred) / y_true)) * 100
print('Evaluation metric results:-')
print(f'MSE is : {metrics.mean_squared_error(y_true, y_pred)}')
print(f'MAE is : {metrics.mean_absolute_error(y_true, y_pred)}')
print(f'RMSE is : {np.sqrt(metrics.mean_squared_error(y_true, y_pred))}')
print(f'MAPE is : {mean_absolute_percentage_error(y_true, y_pred)}')
print(f'R2 is : {metrics.r2_score(y_true, y_pred)}',end='\n\n')
```

Here is the ADF test function to check for stationary data, as shown here:

```
def Augmented_Dickey_Fuller_Test_func(series , column_name):
print (f'Results of Dickey-Fuller Test for column: {column_name}')
dftest = adfuller(series, autolag='AIC')
dfoutput = pd.Series(dftest[0:4], index=['Test Statistic','p-value','No Lags Used','Number of Observations Used'])
for key,value in dftest[4].items():
dfoutput['Critical Value (%s)'%key] = value
print (dfoutput)
if dftest[1] ")
print("Reject the null hypothesis")
print("Data is stationary")
else:
print("Conclusion:====>")
print("Fail to reject the null hypothesis")
print("Data is non-stationary")
```

Check whether the variables are stationary, as shown here:

```
for name, column in df[['Open', 'High', 'Low', 'Close']].iteritems():
Augmented_Dickey_Fuller_Test_func(df[name],name)
print('\n')
```

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figcg_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figcg_HTML.jpg)

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figch_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figch_HTML.jpg)

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figci_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figci_HTML.jpg)

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figcj_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figcj_HTML.jpg)

Make a copy of the data, and let’s perform the test train split.

The train will have all the data except the last 30 days, and the test will contain only the last 30 days to evaluate against predictions.

```
X = df[['Open', 'High', 'Low', 'Close' ]]
train, test = X[0:-30], X[-30:]
```

Make the data stationary by using Pandas differencing.

```
train_diff = train.diff()
train_diff.dropna(inplace = True)
```

Check whether the variables are stationary after first differencing.

```
for name, column in train_diff[['Open', 'High', 'Low', 'Close' ]].iteritems():
Augmented_Dickey_Fuller_Test_func(train_diff[name],name)
print('\n')
```

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figck_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figck_HTML.jpg)

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figcl_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figcl_HTML.jpg)

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figcm_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figcm_HTML.jpg)

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figcn_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figcn_HTML.jpg)

Cointegration is used to check for the existence of a long-run relationship between two or more variables. However, the correlation does not necessarily mean “long run.”

We can see the test says that there is the presence of a long-run relationship between features.

```
from statsmodels.tsa.vector_ar.vecm import coint_johansen
def cointegration_test(df):
res = coint_johansen(df,-1,5)
d = {'0.90':0, '0.95':1, '0.99':2}
traces = res.lr1
cvts = res.cvt[:, d[str(1-0.05)]]
def adjust(val, length= 6):
return str(val).ljust(length)
print('Column Name   >  Test Stat > C(95%)    =>   Signif  \n', '--'*20)
for col, trace, cvt in zip(df.columns, traces, cvts):
print(adjust(col), '> ', adjust(round(trace,2), 9), ">", adjust(cvt, 8), ' =>  ' , trace > cvt)
cointegration_test(train_diff[['Open', 'High', 'Low', 'Close']])
```

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figco_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figco_HTML.jpg)

Define the inverse differencing function .

```
def inverse_diff(actual_df, pred_df):
df_res = pred_df.copy()
columns = actual_df.columns
for col in columns:
df_res[str(col)+'_1st_inv_diff'] = actual_df[col].iloc[-1] + df_res[str(col)].cumsum()
return df_res
```

Let’s define a parameter grid for selecting p, q, and trend.

```
from sklearn.model_selection import ParameterGrid
param_grid = {'p': [1,2,3], 'q':[1,2,3], 'tr': ['n','c','t','ct']}
pg = list(ParameterGrid(param_grid))
print(pg)
```

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figcp_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figcp_HTML.jpg)

The following logic evaluates all possible combinations of p, q, and trend, and stores the results in a `df_results_moni` DataFrame for further reference. A grid search typically takes time.

```
df_results_moni = pd.DataFrame(columns=['p', 'q', 'tr','RMSE open','RMSE high','RMSE low','RMSE close'])
print('starting grid search')
start = timer()
for a,b in enumerate(pg):
p = b.get('p')
q = b.get('q')
tr = b.get('tr')
model = VARMAX(train_diff, order=(p,q), trend=tr).fit()
z = model.forecast(y=train_diff[['Open', 'High', 'Low', 'Close']].values, steps=30)
df_pred = pd.DataFrame(z, columns=[ 'Open', 'High', 'Low', 'Close'])
res = inverse_diff(df[['Open', 'High', 'Low', 'Close']],df_pred)
openrmse = np.sqrt(metrics.mean_squared_error(test.Open, res.Open_1st_inv_diff))
highrmse = np.sqrt(metrics.mean_squared_error(test.High, res.High_1st_inv_diff))
lowrmse = np.sqrt(metrics.mean_squared_error(test.Low, res.Low_1st_inv_diff))
closermse = np.sqrt(metrics.mean_squared_error(test.Close, res.Close_1st_inv_diff))
df_results_moni = df_results_moni.append({'p': p, 'q': q, 'tr': tr,'RMSE open': openrmse,'RMSE high':highrmse,'RMSE low':lowrmse,'RMSE close':closermse }, ignore_index=True)
end = timer()
print(f' Total time taken to complete grid search in seconds: {(end - start)}')
Print DataFrame values in the sorted order of each feature RMSE.
df_results_moni.sort_values(by = ['RMSE open','RMSE high','RMSE low','RMSE close'] )
```

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figcq_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figcq_HTML.jpg)

From the previous example, we can see that p=1, q=2, tr=n gives the least RMSE.

Let’s fit and forecast, as shown here:

```
model = VARMAX(train_diff[[ 'Open', 'High', 'Low', 'Close' ]], order=(1,2),trends = 'n').fit( disp=False)
result = model.forecast(steps = 30)
```

Now let’s inverse the forecasted results, as shown here:

```
res = inverse_diff(df[['Open', 'High', 'Low', 'Close' ]],result)
res.head(5)
```

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figcr_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figcr_HTML.jpg)

Evaluate the results individually, as shown here:

```
for i in ['Open', 'High', 'Low', 'Close' ]:
print(f'Evaluation metric for {i}')
timeseries_evaluation_metrics_func(test[str(i)], res[str(i)+'_1st_inv_diff'])
```

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figcs_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figcs_HTML.jpg)

Plot the results, as shown here:

```
import matplotlib.pyplot as plt
get_ipython().run_line_magic('matplotlib', 'inline')
for i in ['Open', 'High', 'Low', 'Close' ]:
plt.rcParams["figure.figsize"] = [10,7]
plt.plot( train[str(i)], label='Train '+str(i))
plt.plot(test[str(i)], label='Test '+str(i))
plt.plot(res[str(i)+'_1st_inv_diff'], label='Predicted '+str(i))
plt.legend(loc='best')
plt.show()
```

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figct_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figct_HTML.jpg)

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figcu_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figcu_HTML.jpg)

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figcv_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figcv_HTML.jpg)

![../images/492113_1_En_4_Chapter/492113_1_En_4_Figcw_HTML.jpg](images/492113_1_En_4_Chapter/492113_1_En_4_Figcw_HTML.jpg)

We have observed the same problem and used auto-arima to identify p and q. Please check the code notebook VARMA with `Auto Arima.ipynb`.

## Summary

In this chapter, you learned about stationary data, including types of stationary data, interpreting p-values, and ways to make data stationary. We also delved into the math and implementation of AR, MA, ARIMA, SARIMA, SARIMAX, VAR, and VARMA. In the next chapter, you will learn some bleeding-edge techniques for time-series data.

# 5. Bleeding-Edge Techniques

This chapter will focus on theory behind some bleeding-edge techniques and will prepare you to solve problems using univarient and multivarient data for time-series models using deep learning (covered in Chapters [6](#492113_1_En_6_Chapter.xhtml) and [7](#492113_1_En_7_Chapter.xhtml)). In this chapter, we’ll start with an introduction to Neural Networks by reviewing Perceptrons, Activation functions, Backpropagation, types of Gradient Descent, Recurrent Neural networks, Long Short-Term Memory, Gated Recurrent Units, Convolutional Neural Networks, and Auto-Encoders.

## Introduction to Neural Networks

In 1958, Cornell University’s Frank Rosenblatt developed a system called a *perceptron* with the support of the US Navy. This was probably the earliest prototype of a neural network. His system could identify simple geometric shapes from pictures of size 20x20 px. In fact, Rosenblatt was not trying to build a system to recognize images but to study how the brain worked by using a computer simulation of the brain. As a result, his results attracted the interest of many scientists. As the *New York Times* reported in July 1958, “Today the Navy has demonstrated a prototype of an electronic system that is expected to be able to walk, speak, look, self-replicate, and have self-awareness.”

A human brain has billions of neurons. *Neurons* are interconnected cells in the human brain that are responsible for processing and transforming human thoughts, which contain electrical and chemical signals. *Dendrites* are branches that receive information from other neurons.

In Figure [5-1](#492113_1_En_5_Chapter.xhtml#Fig1), the left side shows the biological brain architectures that contain different neurons (vertices), which connect with different dendrites (branches) through axons (edges). The right side of the figure shows the human-made structure of a neural network, which includes inputs X[1], X[2], … X[n], that includes an activation function and generates the final output.

![../images/492113_1_En_5_Chapter/492113_1_En_5_Fig1_HTML.jpg](images/492113_1_En_5_Chapter/492113_1_En_5_Fig1_HTML.jpg)

Figure 5-1

Biological neurons versus artificial neural network (source: Mwandau, Brian & Nyanchama, Matunda, 2018; Investigating Keystroke Dynamics as a Two-Factor Biometric Security)

## Perceptron

A *perceptron* is a single-layer neural network architecture with one neuron. It is a mathematical architecture in which each neuron takes inputs, weighs them separately, sums them up, and passes the sum through a nonlinear function (activation function) to produce output. Figure [5-2](#492113_1_En_5_Chapter.xhtml#Fig2) illustrates a perceptron.

![../images/492113_1_En_5_Chapter/492113_1_En_5_Fig2_HTML.jpg](images/492113_1_En_5_Chapter/492113_1_En_5_Fig2_HTML.jpg)

Figure 5-2

Perceptron mathematical representation

The following is the perceptron model:

**Input**: X[1], X[2], X[3]

**Weights**: w[1], w[2], w[3]

**Bias**: b

**Outputs**: Y

Here are the steps:

**Step 1**: Multiply all inputs, X[1], X[2], X[3], to all weights, w[1], w[2], w[3].

![$$ f=\sum \limits_{j=0}^k{w}_i\;{x}_j+b $$](images/492113_1_En_5_Chapter/492113_1_En_5_Chapter_TeX_Equa.png)

![$$ {y}^{\prime }=f(y)=w1x1+w2x2+w3x3+b $$](images/492113_1_En_5_Chapter/492113_1_En_5_Chapter_TeX_Equb.png)

**Step 2**: Apply the activation function sigmoid.

![$$ {y}^{\hbox{'}}=\left\{\begin{array}{l}1\kern0.24em \mathrm{if}\;f&gt;z\\ {}0\kern0.24em \mathrm{otherwise}\end{array}\right. $$](images/492113_1_En_5_Chapter/492113_1_En_5_Chapter_TeX_Equc.png)

## Activation Function

The activation function is also known as the *radial basis function* . It is a mathematical function or formula that gives the output of the model. It gives a result usually from 0 to 1 and from -1 to 1.

The following are reasons why you should use an activation function:

![../images/492113_1_En_5_Chapter/492113_1_En_5_Fig3_HTML.png](images/492113_1_En_5_Chapter/492113_1_En_5_Fig3_HTML.png)

Figure 5-3

Types of activation function

*   If the activation function is not used, the output of each layer is a linear function of the input of the upper layer. It doesn’t matter how many layers the neural network has, but the output is a linear combination of inputs.

*   If the activation function is used, it introduces nonlinear factors to the neuron so that the neural network can approach any nonlinear function arbitrarily and the neural network can be applied to many nonlinear models. Figure [5-3](#492113_1_En_5_Chapter.xhtml#Fig3) illustrates the types of activation.

### Binary Step Function

The binary step function (Figure [5-4](#492113_1_En_5_Chapter.xhtml#Fig4)) is a threshold based activation function. If there is an output value above and below a certain threshold, neurons will activate and send precisely the same signal to the next layers.

![../images/492113_1_En_5_Chapter/492113_1_En_5_Fig4_HTML.jpg](images/492113_1_En_5_Chapter/492113_1_En_5_Fig4_HTML.jpg)

Figure 5-4

Binary step function

### Linear Activation Function

The linear activation function (Figure [5-5](#492113_1_En_5_Chapter.xhtml#Fig5)) is a simple mathematical formulation where the value of the input is multiplied with some assigned weight and produces an output. It is more preferable than the binary step function because it supports multiple outputs.

![../images/492113_1_En_5_Chapter/492113_1_En_5_Fig5_HTML.jpg](images/492113_1_En_5_Chapter/492113_1_En_5_Fig5_HTML.jpg)

Figure 5-5

Linear activation function

The following are drawbacks of the linear activation function:

*   **Backpropagation is challenging to use**: Derivation of the linear activation function is constant. So, it’s hard to take backpropagation on a constant value.

*   **All layers of the neural network merge into one**: When you design a neural network with the help of a linear activation function, the last layer will be the same linear activation function of the first layer. So, this linear activation function turns the neural network into one layer.

So, a linear activation function with a neural network results in a standard linear regression model that doesn’t have the robust strength to maintain the complexity of data.

### Nonlinear Activation Function

A traditional neural network utilizes nonlinear activation functions because they handle the complex input and output mapping needed. Nonlinear activation functions are necessary for training models on complex data types, such as raw data, images, audio, and video, that are nonlinear and higher dimensional in shape.

![../images/492113_1_En_5_Chapter/492113_1_En_5_Fig6_HTML.png](images/492113_1_En_5_Chapter/492113_1_En_5_Fig6_HTML.png)

Figure 5-6

Types of nonlinear activation function

*   A nonlinear activation function allows backpropagation, which contains a derivation function related to input values.

*   A nonlinear activation function allows multiple layers to stack and helps to produce the neural network. This network comprises numerous hidden layers that are responsible for handling the complexity of data and predicting an accurate result. Figure [5-6](#492113_1_En_5_Chapter.xhtml#Fig6) illustrates the types of nonactivation function.

#### Sigmoid

A sigmoid activation function (Figure [5-7](#492113_1_En_5_Chapter.xhtml#Fig7)) is bound to the output values range [0, 1]. In other words, we can say it normalizes the output values of every layer. It has a smooth gradient. It cannot handle the vanishing gradient problem when the high and low amounts of the input value. It is computationally expensive.

![../images/492113_1_En_5_Chapter/492113_1_En_5_Fig7_HTML.jpg](images/492113_1_En_5_Chapter/492113_1_En_5_Fig7_HTML.jpg)

Figure 5-7

Sigmoid activation function

#### TanH

The tanH activation function (Figure [5-8](#492113_1_En_5_Chapter.xhtml#Fig8)) is bound to the output values range [-1, 1]. This is the zero-model, where a model can handle strong negative, neutral, and strongly positive values. It is similar to the sigmoid function except for the range.

![../images/492113_1_En_5_Chapter/492113_1_En_5_Fig8_HTML.jpg](images/492113_1_En_5_Chapter/492113_1_En_5_Fig8_HTML.jpg)

Figure 5-8

TanH activation function

#### Rectified Linear Unit

The rectified linear unit (ReLU) , as shown in Figure [5-9](#492113_1_En_5_Chapter.xhtml#Fig9), is an essential and widely used activation function in neural networks. A ReLU is a de facto nonsaturation of its gradient, which extensively accelerates the convergence of stochastic gradient descent associated with the sigmoid/tanH functions (according to a paper by Krizhevsky et al). It is capable of handling computation, and its expressions are similar to a linear activation function, but it allows backpropagation. When the input value is zero or negative, it cannot execute backpropagation and stop learning; this is known as the *dying ReLU problem* .

![../images/492113_1_En_5_Chapter/492113_1_En_5_Fig9_HTML.jpg](images/492113_1_En_5_Chapter/492113_1_En_5_Fig9_HTML.jpg)

Figure 5-9

ReLU

#### Leaky ReLU

Leaky ReLU (Figure [5-10](#492113_1_En_5_Chapter.xhtml#Fig10)) has two types of benefits. First, it solves the dying ReLU problem, as it doesn’t have a zero-slope part. It speeds up the training. Its function has a small negative slope (approximately 0.01). Its result is not consistent with negative values.

![../images/492113_1_En_5_Chapter/492113_1_En_5_Fig10_HTML.jpg](images/492113_1_En_5_Chapter/492113_1_En_5_Fig10_HTML.jpg)

Figure 5-10

Leaky ReLU

#### Parametric ReLU

Parametric ReLU (Figure [5-11](#492113_1_En_5_Chapter.xhtml#Fig11)) is a kind of leaky ReLU activation function, but it doesn’t have a predefined slope like 0.01; it creates parameters for the neural network to figure out when the input value is negative. It permits a negative slope rate to be learned. It is possible to perform backpropagation and determine the most suitable amount of α. This may be implemented differently for different kinds of problems.

![../images/492113_1_En_5_Chapter/492113_1_En_5_Fig11_HTML.jpg](images/492113_1_En_5_Chapter/492113_1_En_5_Fig11_HTML.jpg)

Figure 5-11

Parametric ReLU

#### Softmax

A softmax activation function transforms logits into probabilities that contain a range of the sum to 1, and it is the output vector that signifies the probability distributions of a list of probable outcomes (Figure [5-12](#492113_1_En_5_Chapter.xhtml#Fig12)). It can handle multiple classes. Mostly the softmax layer is utilized for the output layer.

![../images/492113_1_En_5_Chapter/492113_1_En_5_Fig12_HTML.jpg](images/492113_1_En_5_Chapter/492113_1_En_5_Fig12_HTML.jpg)

Figure 5-12

Softmax activation function

Swish The Google Brain team proposed a swish activation function in 2017 (Figure [5-13](#492113_1_En_5_Chapter.xhtml#Fig13)) that works better than ReLU. Swish is x times the sigmoid activation. Swish works better on a deeper model across complex data.

![../images/492113_1_En_5_Chapter/492113_1_En_5_Fig13_HTML.jpg](images/492113_1_En_5_Chapter/492113_1_En_5_Fig13_HTML.jpg)

Figure 5-13

Swish activation

## Forward Propagation

F*orward propagation* (Figure [5-14](#492113_1_En_5_Chapter.xhtml#Fig14)) is the process of taking X input values as initial information and then propagating to hidden units at each layer in the neural network to produce the final output (y).

![../images/492113_1_En_5_Chapter/492113_1_En_5_Fig14_HTML.jpg](images/492113_1_En_5_Chapter/492113_1_En_5_Fig14_HTML.jpg)

Figure 5-14

Representation of forward propagation

**Step 1**: Provide input (X) to each neuron in the neural network and calculate the two functions; one is linear multiplication.

![$$ f=\sum \limits_{j=0}^k{w}_i\;{x}_j+b $$](images/492113_1_En_5_Chapter/492113_1_En_5_Chapter_TeX_Equd.png)

**Step 2**: Apply the activation function.

![$$ \phi = ReLU(f) $$](images/492113_1_En_5_Chapter/492113_1_En_5_Chapter_TeX_Eque.png)

We can use different activation functions. Then it will forward through every layer, and we will get the predicted output. But a neural network is not learning the information in single forward propagation. So, we calculate the information gap every time it learns, which is known as the *cost function* (error calculation). We can try to reduce that error by applying backpropagation in the neural network.

![$$ C=\frac{1}{2}\;{\left({y}_{pred}-{y}_{actual}\right)}^2 $$](images/492113_1_En_5_Chapter/492113_1_En_5_Chapter_TeX_Equf.png)

In the next section, we will look at backward propagation.

## Backward Propagation

Backward propagation, or *backpropagation*, is the process of propagating the error and going back to the input layer from the hidden layer to regulate the weights (Figure [5-15](#492113_1_En_5_Chapter.xhtml#Fig15))**.**

![../images/492113_1_En_5_Chapter/492113_1_En_5_Fig15_HTML.jpg](images/492113_1_En_5_Chapter/492113_1_En_5_Fig15_HTML.jpg)

Figure 5-15

Representation of backward propagation

You can adjust the weight in two different ways.

![../images/492113_1_En_5_Chapter/492113_1_En_5_Fig16_HTML.jpg](images/492113_1_En_5_Chapter/492113_1_En_5_Fig16_HTML.jpg)

Figure 5-16

Brute-force approach

*   **Brute-force approach** : In this approach, the neural network tries to design all the possible combinations of weights and indicates only those combinations that would be best to apply. The biggest problem of this method is the curse of dimensionality when the inputs increase. It will take more computational time, and the result might decrease inefficiency. It used only for single-layer networks like perceptrons. Figure [5-16](#492113_1_En_5_Chapter.xhtml#Fig16) illustrates the brute-force approach.

*   **Gradient descent** : Gradient descent is a method for identifying the rate of change every time a value updates. In this method, we can apply the first-order derivation of iterative optimization methods to obtain the minimum cost function. This is the best way to find out the optimal weight.
    *   If the gradient has a negative slope, then it represents the left side of the global minimum.

    *   If the gradient has a positive slope, then it represents the right side of the global minimum.

    *   If the gradient is zero, then it represents a global minima point.

Here are the mathematical representations of error analysis.

Here is the cost function:

![$$ J\left({\theta}_0,{\theta}_1\right)=\frac{1}{2m}\;\sum \limits_{i=1}^m{\left({h}_{\theta}\left({x}^{(i)}\right)-{y}^{(i)}\right)}^2 $$](images/492113_1_En_5_Chapter/492113_1_En_5_Chapter_TeX_Equg.png)

Here are the update rules:

![$$ {\displaystyle \begin{array}{l}{\theta}_0:= {\theta}_0-\alpha\;\frac{\partial }{\partial {\theta}_0}\;J\left({\theta}_0,{\theta}_1\right)\\ {}{\theta}_1:= {\theta}_1-\alpha\;\frac{\partial }{\partial {\theta}_1}\;J\left({\theta}_0,{\theta}_1\right)\end{array}} $$](images/492113_1_En_5_Chapter/492113_1_En_5_Chapter_TeX_Equh.png)

Here are the derivatives:

![$$ {\displaystyle \begin{array}{l}\frac{\partial }{\partial {\theta}_0}J\left({\theta}_0,{\theta}_1\right)=\frac{1}{m}\;\sum \limits_{i=1}^m\left({h}_{\theta}\left({x}^{(i)}\right)-{y}^{(i)}\right)\\ {}\frac{\partial }{\partial {\theta}_1}J\left({\theta}_0,{\theta}_1\right)=\frac{1}{m}\;\sum \limits_{i=1}^m\left({h}_{\theta}\left({x}^{(i)}\right)-{y}^{(i)}\right)\cdot {x}^{(i)}\end{array}} $$](images/492113_1_En_5_Chapter/492113_1_En_5_Chapter_TeX_Equi.png)

Figure [5-17](#492113_1_En_5_Chapter.xhtml#Fig17) illustrates the different types of gradient descent methodologies.

![../images/492113_1_En_5_Chapter/492113_1_En_5_Fig17_HTML.jpg](images/492113_1_En_5_Chapter/492113_1_En_5_Fig17_HTML.jpg)

Figure 5-17

Taxonomy of gradient descent

In the next section, we will look at different types of gradient descent optimization algorithms.

### Gradient Descent Optimization Algorithms

Gradient descent is a [first-order](https://en.wikipedia.org/wiki/Category:First_order_methods) [optimization](https://en.wikipedia.org/wiki/Mathematical_optimization) [algorithm](https://en.wikipedia.org/wiki/Algorithm) that is used iteratively for finding a [local minimum](https://en.wikipedia.org/wiki/Local_minimum) of a differentiable function. This method minimizes convex functions. In its basic form, gradient descent finds an approximate solution to the unconstrained problem.

The gradient descent optimizer works in three main ways.

*   Updates the learning rate (*α*)

*   Update*s* a gradient component*,* ∂L/∂w

*   *Uses b*oth of the previous ways

![$$ {w}_{new}=w-\alpha\;\frac{\partial L}{\partial w} $$](images/492113_1_En_5_Chapter/492113_1_En_5_Chapter_TeX_Equj.png)

### Learning Rate vs. Gradient Descent Optimizers

The learning rate (α*)* is multiplied by the gradient descent optimizer function (∂L/∂w).

1.  The optimizer is multiplied with the positive factor to a learning rate (α), so gradient will become smaller*,* which represent RMSprop.

2.  Optimizers usually take the moving average of a gradient, called *momentum*, instead of just taking one value like vanilla gradient descent.

Figure [5-18](#492113_1_En_5_Chapter.xhtml#Fig18) illustrates the different optimizers.

![../images/492113_1_En_5_Chapter/492113_1_En_5_Fig18_HTML.png](images/492113_1_En_5_Chapter/492113_1_En_5_Fig18_HTML.png)

Figure 5-18

Evolutionary timeline of optimization methods

Figure [5-19](#492113_1_En_5_Chapter.xhtml#Fig19) and Figure [5-20](#492113_1_En_5_Chapter.xhtml#Fig20) show the types of gradient descent.

![../images/492113_1_En_5_Chapter/492113_1_En_5_Fig20_HTML.jpg](images/492113_1_En_5_Chapter/492113_1_En_5_Fig20_HTML.jpg)

Figure 5-20

Brief chart of optimization methods with formulas

![../images/492113_1_En_5_Chapter/492113_1_En_5_Fig19_HTML.png](images/492113_1_En_5_Chapter/492113_1_En_5_Fig19_HTML.png)

Figure 5-19

Types of optimization methods

In the next section, we will look at recurrent neural networks, which are used for sequence-to-sequence learning.

## Recurrent Neural Networks

A *recurrent neural network* (RNN) is a sequential class of neural network where the output from the previous state is served as input to the current state. In a neural network, all inputs and outputs are also independent of each other, but when it is required to predict things in sequence, the RNN works well.

The RNN handles data that is sequential in fashion. So, when order matters in the data, an RNN should be used. Examples of such data are natural language data, speech data, time-series data, video data, music data, and stock market data. These all are examples of real-world data that contain a sequence. Figure [5-21](#492113_1_En_5_Chapter.xhtml#Fig21) illustrates the difference between RNNs versus normal NNs.

![../images/492113_1_En_5_Chapter/492113_1_En_5_Fig21_HTML.jpg](images/492113_1_En_5_Chapter/492113_1_En_5_Fig21_HTML.jpg)

Figure 5-21

Regular RNN versus NN

The neural network does not hold this recurrent unit, where RNN does, which helps to handle the sequential pattern found in the data.

RNN has the following models, all illustrated in Figures [5-22](#492113_1_En_5_Chapter.xhtml#Fig22) through [5-24](#492113_1_En_5_Chapter.xhtml#Fig24).

![../images/492113_1_En_5_Chapter/492113_1_En_5_Fig24_HTML.jpg](images/492113_1_En_5_Chapter/492113_1_En_5_Fig24_HTML.jpg)

Figure 5-24

Many-to-many relationship

![../images/492113_1_En_5_Chapter/492113_1_En_5_Fig23_HTML.jpg](images/492113_1_En_5_Chapter/492113_1_En_5_Fig23_HTML.jpg)

Figure 5-23

Many-to-one relationship

![../images/492113_1_En_5_Chapter/492113_1_En_5_Fig22_HTML.jpg](images/492113_1_En_5_Chapter/492113_1_En_5_Fig22_HTML.jpg)

Figure 5-22

One-to-many relationship

*   One to many

*   Many to one

*   Many to many

We have given an input image (one) and get the result sequence of text (many).

We have given text (many) as input and get a sentiment (one) as output.

We have given text (many) as input and get a translated result (many) as output.

### Feed-Forward Recurrent Neural Network

The feed-forward RNN is a similar structure to a standard neural network. A fundamental difference is the input sequence. In an RNN, the input sequence is the data coming in. In Figure [5-25](#492113_1_En_5_Chapter.xhtml#Fig25), X[t-1], X[t], X[t+1], X[t+2] is the input sequence. The input passes through the hidden layer and takes the previous time-step values and weights (*W*) at the hidden layers.

![../images/492113_1_En_5_Chapter/492113_1_En_5_Fig25_HTML.jpg](images/492113_1_En_5_Chapter/492113_1_En_5_Fig25_HTML.jpg)

Figure 5-25

Represenation of feed-forward recurrent neural network

Now let’s look at the mathematical view of a feed-forward recurrent neural network.

At timestep (t), here is the equation:

![$$ {\displaystyle \begin{array}{l}{h}_t=\sigma\;\left(U\cdot {X}_t+W.{h}_{t-1}\right)\\ {}{y}_t=\mathrm{softmax}\;\left(V\cdot {h}_t\right)\end{array}} $$](images/492113_1_En_5_Chapter/492113_1_En_5_Chapter_TeX_Equk.png)

where:

σ = TanH activation function

U = Weight (vector) for the hidden layer

V = Weight (vector) for the output layer

W = Same weight vector for different timesteps

X = Input vector with sequence

Y = Output vector with sequence

Here is the cost function:

![$$ J\left(\theta \right)=-\sum \limits_{j=1}^{\left|M\right|}{y}_{t,j}\;\log\;{\overline{y}}_{t,j} $$](images/492113_1_En_5_Chapter/492113_1_En_5_Chapter_TeX_Equl.png)

Here is the cross-entropy:

![$$ J\left(\theta \right)=-\frac{1}{t}\;\sum \limits_{t=1}^T\sum \limits_{j=1}^{\left|M\right|}{y}_{t,j}\;\log\;{\overline{y}}_{t,j} $$](images/492113_1_En_5_Chapter/492113_1_En_5_Chapter_TeX_Equm.png)

In Figure [5-25](#492113_1_En_5_Chapter.xhtml#Fig25), we can see the number of inputs, hidden layers, and output layers .

Here is the sequence of steps to calculate the output:

1.  It calculates h[t-1] with weight U and input X.

2.  It calculates y[t-1] from h[t-1] and output weight V.

3.  It calculates the h[t] value with the previous layer parameters of U, X, W, and h[t-1.]

4.  It calculates y[t] from V and h[t], and it continues until the final step.

Note

We are using sigmoid, tanH, and softmax activation for the RNN.

In the next section, we will look at the backpropagation approach in RNNs.

## Backpropagation Through Time in RNN

The backpropagation through time approach (BPTT) , as shown in Figure [5-26](#492113_1_En_5_Chapter.xhtml#Fig26), is a kind of gradient descent algorithm that is used to update the weight and minimize errors with the help of a derived chain rule. This is basically applied to the sequence data, and it is different from algorithms such as RNN, LSTM, GRU, etc. We have two questions here.

![../images/492113_1_En_5_Chapter/492113_1_En_5_Fig26_HTML.jpg](images/492113_1_En_5_Chapter/492113_1_En_5_Fig26_HTML.jpg)

Figure 5-26

Representation of backpropagation through time in an RNN

*   How much is the total error reduced concerning the hidden and output units?

*   How does the output vary concerning the weights (U, V, W)?

Backpropagation is a similar operation to the one we applied in a neural network. The only difference is that the current timesteps are calculated based on the previous one. So, we have to traverse all the paths and use the chain rule, which is shown here:

![$$ {\displaystyle \begin{array}{l}\frac{dj_t}{dv}=\sum \limits_t\frac{dj}{dy_t}\;{\left(\frac{dy}{dv}\right)}^{h_t}\\ {}\frac{dj_t}{dw}=\sum \limits_t\frac{dj}{dh_t}\;{\left(\frac{dh_t}{dw}\right)}^{\left(1-{h}_t^2\right)\cdot {h}_{t-1}}\\ {}\frac{dj_t}{du}=\sum \limits_t\frac{dj}{dh_t}\;{\left(\frac{dh_t}{du}\right)}^{\left(1-{h}_t^2\right)\cdot {X}_t}\end{array}} $$](images/492113_1_En_5_Chapter/492113_1_En_5_Chapter_TeX_Equn.png)

The weights are the same for all the timesteps.

If

|W| < 1 (vanishing gradient problem)

|W| > 1 (exploding gradient problem)

The biggest problem with an RNN is the vanishing gradient problem (VGP) or exploding gradient problem. During the backpropagation, the weights are the same for all timesteps as we continuously update the weight, so the gradient becomes either too weak or too strong with the updates, which causes either the vanishing gradient problem or the exploding problem.

The following are solutions of the vanishing gradient problem:

![../images/492113_1_En_5_Chapter/492113_1_En_5_Fig27_HTML.jpg](images/492113_1_En_5_Chapter/492113_1_En_5_Fig27_HTML.jpg)

Figure 5-27

Representation of vanishing and exploding gradients

*   Parameters are weights and biases.

*   Hyperparameters are the learning rate, epochs, batches, etc.

*   If values contract exponentially, it leads to a vanishing gradient.

*   If values get larger exponentially, it leads to an exploding gradient.

    Figure [5-27](#492113_1_En_5_Chapter.xhtml#Fig27) illustrates vanishing and exploding gradients.

    Therefore, the deep learning model needs time to train and learn from the data, and in some cases, it may not train thoroughly. This results in either less or no convergence of the neural network.

    Because of the vanishing gradient problem , the gradient becomes too small and regularly decreases to a minimal value (sometimes negative). This makes the model performance weak with low accuracy. The model may fail to predict or classify what it is supposed to do.

    In the next section, we will learn about long short-term memory.

*   **Using LSTM**: To handle this VGP

*   **Using faster hardware**: Switching from a CPU to a GPU (Nvidea)

*   **Using another activation function**: Such as ReLU, which suffers less from this problem

*   **Using a residual network**: To avoid the problem

    **Note**  The vanishing gradient problem occurs while training an RNN. This primarily transpires when the network parameters and hyperparameters are not correctly customized.

## Long Short-Term Memory

Long short-term memory (LSTM) was first mentioned in 1997 by Sepp Hoch Reiter and Jürgen Schmid Huber. By using constant error carousel (CEC) units, LSTM treats the exploding and vanishing gradient problems. The preliminary version of the LSTM block incorporated cells, input, and output gates. It is famous for its design, which is a specific type of recurrent neural network, utilizing many different real-world applications that the standard version doesn’t. An RNN’s biggest problem is that it only holds the previous state information, causing the vanishing gradient problem. This problem has been solved by LSTM.

LSTM was constructed to avoid the issue of long-term dependencies. Remembering information for a long duration is basically its default behavior. All RNNs have repeating chain modules of the neural network. This repeating module is a simple structure, such as the tanH layer in an RNN. LSTM also has an identical chaining structure instead of having a single neural network layer (Figure [5-28](#492113_1_En_5_Chapter.xhtml#Fig28)).

![../images/492113_1_En_5_Chapter/492113_1_En_5_Fig28_HTML.jpg](images/492113_1_En_5_Chapter/492113_1_En_5_Fig28_HTML.jpg)

Figure 5-28

RNN versus LSTM

LSTM has an extra feature as compared to RNNs, which is called *memory*.

*   Forget gate f (a neural network with sigmoid activation)

*   Candidate layer C (a neural network with tanH activation)

*   Input gate I (a neural network with sigmoid activation)

*   Output gate O (a neural network with sigmoid activation)

*   Hidden state h

*   Memory cell C

Figure [5-29](#492113_1_En_5_Chapter.xhtml#Fig29) illustrates that it has four different gates: forget gate, candidate cate, input gate, and output gate. All gates are single-layer neural networks with the sigmoid activation function, excluding the candidate gate, which is using the tanH activation function.

![../images/492113_1_En_5_Chapter/492113_1_En_5_Fig29_HTML.jpg](images/492113_1_En_5_Chapter/492113_1_En_5_Fig29_HTML.jpg)

Figure 5-29

LSTM cell architecture with formulas

Figure [5-29](#492113_1_En_5_Chapter.xhtml#Fig29) shows LSTM at the T time step.

*   The input cell contains x (input vector), h[t-1] (previous hidden state), and C (previous memory state).

*   The output cell contains h (current hidden cell) and C (current memory cell).

*   W and U are weight vectors.

### Step-by-Step Explanation of LSTM

Here is a step-by-step explanation of LSTM.

**Step 1**: Let’s say we must predict an upcoming sequence based on all the forthcoming timestamps. In such a problem, the cell state can store all the information for the present input so that the correct prediction can be made. When we get new input data, we link it to the previous pattern in the sequence time-series data.

Here is the forget gate equation:

![$$ {f}_t=\sigma\;\left({W}_f\cdot \left[{h}_{t-1},{x}_t\right]+{b}_f\right) $$](images/492113_1_En_5_Chapter/492113_1_En_5_Chapter_TeX_Equo.png)

**Step 2**: The next step is to make a decision on which information is important to us so we can store it. This has been classified into two parts. The first input gate layer, which contains the sigmoid layer, makes a decision on which values to update. Next, a tanH layer produces a vector for the new candidate values; this vector is called ![../images/492113_1_En_5_Chapter/492113_1_En_5_Figb_HTML.gif](images/492113_1_En_5_Chapter/492113_1_En_5_Figb_HTML.gif). That could be further to the state. We can associate these two gates with each other to create an updated state.

Here are the input and candidate gate equations:

![$$ {\displaystyle \begin{array}{l}{i}_t=\sigma\;\left({W}_i\cdot \left[{h}_{t-1},{x}_t\right]+{b}_i\right)\\ {}{\overset{\sim }{C}}_t=\tanh \kern0.24em \left({W}_C\cdot \left[{h}_{t-1},{x}_t\right]+{b}_C\right)\end{array}} $$](images/492113_1_En_5_Chapter/492113_1_En_5_Chapter_TeX_Equp.png)

**Step 3**: It is time to update the old cell state, C[t-1], into the new cell state, C[t.] We multiply the old state by *ft*, forgetting the unnecessary parts. Then we multiply the input gate (i[t]) by the candidate gate (![../images/492113_1_En_5_Chapter/492113_1_En_5_Figc_HTML.gif](images/492113_1_En_5_Chapter/492113_1_En_5_Figc_HTML.gif)), and this becomes the new candidate value, scaled by how much we decided to update each state value.

Here is the cell state equation:

![$$ {C}_t={f}_t\ast {C}_{t-1}+{i}_t\ast {\overset{\sim }{C}}_t $$](images/492113_1_En_5_Chapter/492113_1_En_5_Chapter_TeX_Equq.png)

**Step 4**: We have to make a decision about the output state. This is based on a cell state, but it can be a filtered version. The first sigmoid layer makes the decision about which part of the output of the cell state we will produce; then we put a cell state to tanH and multiply it by the output of the sigmoid gate. So, we can only generate the output we make a decision on.

Here are the equations for the output state and hidden state:

![$$ {\displaystyle \begin{array}{l}{o}_t=\sigma\;\left({W}_o\;\left[{h}_{t-1},{x}_t\right]+{b}_o\right)\\ {}{h}_t={o}_t\ast \tanh\;\left({C}_t\right)\end{array}} $$](images/492113_1_En_5_Chapter/492113_1_En_5_Chapter_TeX_Equr.png)

LSTM has two types of variants, peephole and peephole convolutional, which we’ll look at now.

### Peephole LSTM

The LSTM cell receives a connection from the input and output cells but doesn’t have a direct link with the constant error carousel, which is meant to control. We can directly observe the output, which is close to zero until the output gate is closed. The same problem happens with all memory blocks. When the output gate is locked, no single gate can access the CEC control. A simple remedy for this solution is the peephole connection from the CEC to the gates to the same memory block. The peephole connections allow all gates to inspect the current cell state even when the output gate is closed.

Figure [5-30](#492113_1_En_5_Chapter.xhtml#Fig30) shows an LSTM unit with peephole connections (i.e., a peephole LSTM). Peephole connections permit the gates to approach the CEC, whose activation is the cell state. h[t-1] is not used. c[t-1] is used in most places.

![../images/492113_1_En_5_Chapter/492113_1_En_5_Fig30_HTML.jpg](images/492113_1_En_5_Chapter/492113_1_En_5_Fig30_HTML.jpg)

Figure 5-30

Representation of peephole LSTM

### Peephole Convolutional LSTM

LSTM has proven able to handle temporal correlation. It contains too much redundancy for spatial data. Convolutional LSTM (ConvLSTM) has solved this problem, so now we can solve the spatiotemporal sequence forecasting problem. Figure [5-31](#492113_1_En_5_Chapter.xhtml#Fig31) illustrates the transformation of a 2D image into a 3D tensor.

![../images/492113_1_En_5_Chapter/492113_1_En_5_Fig31_HTML.jpg](images/492113_1_En_5_Chapter/492113_1_En_5_Fig31_HTML.jpg)

Figure 5-31

Transforming a 2D image into a 3D tensor

Figure [5-32](#492113_1_En_5_Chapter.xhtml#Fig32) illustrates the inner structure of ConvLSTM mathematically.

![../images/492113_1_En_5_Chapter/492113_1_En_5_Fig32_HTML.jpg](images/492113_1_En_5_Chapter/492113_1_En_5_Fig32_HTML.jpg)

Figure 5-32

Inner structure of ConvLSTM (source: Xingjian, S. H. I., et al. “Convolutional LSTM network: A machine learning approach for precipitation nowcasting,” 2015, Advances in neural information processing systems)

In the next section, we will look at the gated recurrent unit and its variant.

## Gated Recurrent Units

A *gated recurrent unit* (GRU) is a special kind of recurrent neural network using a gating mechanism (Figure [5-33](#492113_1_En_5_Chapter.xhtml#Fig33)). It was introduced by Kyunghyun Cho in 2014\. The GRU is an identical structure to LSTM with a forget gate but with fewer parameters; it excludes the output gate. GRU performs well on some tasks such as audio acoustic classification and speech classification. GRU is even better on small datasets. GRU is not good for training simple languages that are learnable by LSTM because LSTM performs unbounded counting while GRU doesn’t.

![../images/492113_1_En_5_Chapter/492113_1_En_5_Fig33_HTML.jpg](images/492113_1_En_5_Chapter/492113_1_En_5_Fig33_HTML.jpg)

Figure 5-33

Representation of GRU

Several variances have been introduced with different combinations of the previous hidden state and the bias, and its simplified version is known as a *minimal gated unit* .

GRU has two variants. One is a fully gated unit, shown in Figure [5-33](#492113_1_En_5_Chapter.xhtml#Fig33).

Initially, for t = 0, the output vector is h[0] = 0.

![$$ {\displaystyle \begin{array}{l}{z}_t={\sigma}_g\;\left({W}_z{x}_t+{U}_z{h}_{t-1}+{b}_z\right)\\ {}{r}_t={\sigma}_g\;\left({W}_r{x}_t+{U}_r{h}_{t-1}+{b}_r\right)\\ {}{h}_t={z}_t\odot {h}_{t-1}+\left(1-{z}_t\right)\odot {\phi}_h\;\left({W}_h\;{x}_t+{U}_h\;\left({r}_t\odot {h}_{t-1}\right)+{b}_h\right)\end{array}} $$](images/492113_1_En_5_Chapter/492113_1_En_5_Chapter_TeX_Equs.png)

*   x[t] is the input data, h[t] is the output data, z[t] is the update gate, and r[t] is the reset gate.

*   W, U, and b are parameter matrices and vectors, and two activations are used: sigmoid (**σ**[**g**]) and tanH (**Φ**[**h**]).

Figure [5-34](#492113_1_En_5_Chapter.xhtml#Fig34) shows three variants.

![../images/492113_1_En_5_Chapter/492113_1_En_5_Fig34_HTML.jpg](images/492113_1_En_5_Chapter/492113_1_En_5_Fig34_HTML.jpg)

Figure 5-34

Repsentation of GRU variants

The second kind is the *minimal gated unit* , which is identical to the fully gated unit except a reset gate is merged into one forget gate (Figure [5-35](#492113_1_En_5_Chapter.xhtml#Fig35)). This suggests that the output vector will be changed.

![../images/492113_1_En_5_Chapter/492113_1_En_5_Fig35_HTML.jpg](images/492113_1_En_5_Chapter/492113_1_En_5_Fig35_HTML.jpg)

Figure 5-35

Minimal gated unit

![$$ {\displaystyle \begin{array}{l}{f}_t={\sigma}_g\;\left({W}_f{x}_t+{U}_f\;{h}_{t-1}+{b}_f\right)\\ {}{h}_t={f}_t\odot {h}_{t-1}+\left(1-{f}_t\right)\odot {\phi}_h\;\left({W}_h\;{x}_t+{U}_h\;\left({f}_t\odot {h}_{t-1}\right)+{b}_h\right)\end{array}} $$](images/492113_1_En_5_Chapter/492113_1_En_5_Chapter_TeX_Equt.png)

*   x[t] is the input data, h[t] is the output data, and f[t] is the forget gate.

*   W, U, and b are parameter matrices and vector.

In the next section, we will look at convolution neural networks.

## Convolution Neural Networks

Convolution neural networks (CNNs) were first published in 1995 by LeCun and Bengio. A convolutional neural network structure is based on the neurobiological observation that neurons in our visual cortex can be recognized without problems, even when the direction and location of objects change. Unlike a multilayer perceptron, you can learn the unique characteristics of an object regardless of their positions or orientation.

A CNN comprises a convolutional layer that extracts features and contains pooling that compresses information. The convolutions and pooling layers are repeated among the input and output layers. The number of iterations is taken from the experiments to get the best results. A CNN uses nonpixel filters to solve the problem of multilayer perceptron. Convolution filters consist of matrices. The filter scans the image from the top left to the bottom right in the convolutions layer, reading the features of the image to generate a feature map. In Figure [5-36](#492113_1_En_5_Chapter.xhtml#Fig36), the kernel matrix is known as a *filter* .

![../images/492113_1_En_5_Chapter/492113_1_En_5_Fig36_HTML.jpg](images/492113_1_En_5_Chapter/492113_1_En_5_Fig36_HTML.jpg)

Figure 5-36

Convolution operation

In Figure [5-36](#492113_1_En_5_Chapter.xhtml#Fig36), the left image is the image matrix, which is composed of three primary colors; it is represented in three dimensions with depth, and the filter is represented by a small brown box. The filter is served by the matrix of weights. As filters move from left to right, the feature map searches for the same features at different locations in the input image. If the part passed by the filter matches the feature, it will output a high value to increase the likelihood of image classification. For example, if you scan a car image and then scan the shape of the wheel, the car is likely to be a car and will output a high value.

List the feature maps read by the filter vertically and horizontally to create one sheet, as shown in the middle image. If multiple filters are used, numerous feature maps will be created. A CNN’s polling layer compresses feature maps, reducing the number of parameters (Figure [5-37](#492113_1_En_5_Chapter.xhtml#Fig37)). Split a 4×4 feature map into 2×2-unit bundles to represent each bundle. There is a maximum pooling that takes the maximum and an average pooling that takes the average.

![../images/492113_1_En_5_Chapter/492113_1_En_5_Fig37_HTML.jpg](images/492113_1_En_5_Chapter/492113_1_En_5_Fig37_HTML.jpg)

Figure 5-37

Representation of pooling

Repeat the process of creating another image by reapplying the filter based on the image. If you repeat the process of creating multiple filters and images and its characteristics are compressed, the result for the aspects of the image drops to one number. This value is a stochastic estimate. The accuracy of the CNN model is determined by how sophisticated the filter is.

### Generalized CNN Formula

The following is the original mathematical representation of the LeNet 5 CNN family of algorithms designed by LeCun.

1.  **Convolution formula** :

    ![$$ {\displaystyle \begin{array}{l}\mathrm{Input}:\left(n\;x\;n\;x\;{n}_c\right)\\ {}\mathrm{Filter}:\left(f\;x\;f\;x\;{n}_c\right)\\ {}\mathrm{Output}:\left(\left[\frac{n+2p-f}{s}+1\right]\;x\;\left[\frac{n+2p-f}{s}+1\right]\kern0.24em {xn}_c^{\hbox{'}}\right)\;\end{array}} $$](images/492113_1_En_5_Chapter/492113_1_En_5_Chapter_TeX_Equu.png)

    This includes the filter size (f), stride (s), pad (p), and input size (n), with n[c]’ as the number of filters.

2.  **Pooling layer** :

    Pooling has two types.
    1.  **Max pooling**: The max value will be taken from all the cells.

    2.  **Average pooling**: The average value will be taken from all the cells.

3.  **Batch normalization** : This is used to normalize the input layer by fine-tuning and scaling the activations. It makes the model training speed fast.

4.  **Dropouts** : This is a method where randomly selected neurons are ignored during training. They are “dropped out” randomly. It is used to prevent model overfitting.

Table [5-1](#492113_1_En_5_Chapter.xhtml#Tab1) illustrates the different layers and their mathematical formulas.

Table 5-1

Formulas of CNN Different Layers

| -![../images/492113_1_En_5_Chapter/492113_1_En_5_Figa_HTML.jpg](images/492113_1_En_5_Chapter/492113_1_En_5_Figa_HTML.jpg) |

In this next section, we will look at one-dimensional CNNs.

### One-Dimensional CNNs

A CNN is really good at identifying simple patterns in data and then using them to form more complex patterns in higher layers. One-dimensional CNNs are useful when you want to get exciting features from shorter (fixed-length) fragments of the entire dataset and when the location of the features in that fragment is irrelevant.

This is ideal for analyzing time series of sensor data, such as gyroscope data or accelerometer data. It is also suitable for analyzing any kind of signal data (such as audio signals) over a fixed length of time. Another application is NLP (although LSTM networks are more promising here, because the proximity of words may not always be a good indication of trainable patterns).

In the next section, we will look at an auto-encoder, which is one of the popular unsupervised deep learning methods.

## Auto-encoders

An *auto-encoder* is an unsupervised deep learning neural network model. It has the capability to learn the hidden features of the input data, which is called *encoding* . At the same time, it can reconstruct the original input data with the new generated features learned, which is called *decoding* .

Intuitively, auto-encoders can be used for feature dimensionality reduction, such as principal component analysis (PCA), but their performance is stronger than PCA because neural network models can extract more efficient new features. In addition to feature dimensionality reduction, new features learned by the auto-encoder can be fed into a supervised learning model, so the auto-encoder can function as a feature extractor. As an unsupervised deep learning model, auto-encoders can also be used to generate new data that is different from the training samples. In this way, auto-encoders (variational auto-encoders) are generative models.

Figure [5-38](#492113_1_En_5_Chapter.xhtml#Fig38) shows the basic structure of an auto-encoder. It includes two processes: encoding and decoding.

![../images/492113_1_En_5_Chapter/492113_1_En_5_Fig38_HTML.jpg](images/492113_1_En_5_Chapter/492113_1_En_5_Fig38_HTML.jpg)

Figure 5-38

Representation of auto-encoder

The encoder input is x encoded to obtain new features y, and input x can form a new unique feature y reconstructed. The encoding process is as follows:

![$$ y=f\;\left( Wx+b\right) $$](images/492113_1_En_5_Chapter/492113_1_En_5_Chapter_TeX_Equv.png)

It can be seen that, in the neural network structure, the encoding is a linear combination followed by a nonlinear activation function. If there is no nonlinear structure, then the auto-encoder is no different from ordinary PCA. With the new features ***y***, may enter ***x*** reconstruction, i.e., the decoding process:

![$$ {x}^{\hbox{'}}=f\;\left({W}^{\hbox{'}}\;x+{b}^{\hbox{'}}\right) $$](images/492113_1_En_5_Chapter/492113_1_En_5_Chapter_TeX_Equw.png)

We hope the reconstructed formula is as consistent as possible and you can use the loss function to minimize the negative log likelihood to train the model, as shown here:

![$$ L=-\log\;P\;\left(x\;\left|{x}^{\hbox{'}}\right.\right) $$](images/492113_1_En_5_Chapter/492113_1_En_5_Chapter_TeX_Equx.png)

For Gaussian distribution data, it is better to use the mean square error, and for Bernoulli distribution, cross-entropy can be used, which can be derived from the likelihood function. Under normal circumstances, we will add some restrictions to the *W*^’ = *W*^(*T*) auto-encoder, which is commonly used. This is called *tied weights* .

## Summary

In this chapter, you learned about activation functions, backpropagation, types of gradient descent, recurrent neural networks, long short-term memory, gated recurrent unis, convolutional neural networks, and auto-encoders. In the next chapter, you will learn how to solve univariate time-series problems using bleeding-edge techniques.

# 6. Bleeding-Edge Techniques for Univariate Time Series

In the previous chapter, you learned about using traditional techniques to work with time-series data. In this chapter, you will learn how to solve univariate time-series problems using bleeding-edge techniques. A *univariate time series* is a time series that consists of single (scalar) observations recorded sequentially over equally spaced time periods. In this chapter, you will look at single-step time-series forecasting and horizon-style time-series forecasting.

## Single-Step Data Preparation for Time-Series Forecasting

Single-step time-series forecasting is a technique where the model is exposed to one window of data at a time, such as days, weeks, months, quarters, or years, and attempts to predict the next consecutive step, as illustrated in Figure [6-1](#492113_1_En_6_Chapter.xhtml#Fig1).

![../images/492113_1_En_6_Chapter/492113_1_En_6_Fig1_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Fig1_HTML.jpg)

Figure 6-1

Representation of single-step time-series series forecasting

**Example 1**: Data is at the monthly level. The model is shown the first window from the 1^(st) to the 48^(th), which means four years of data, and it predicts the 49^(th) month. Then, for the next iteration, the model looks at the 2^(nd) to 49^(th) months for training and tries to predict the 50^(th) month.

**Example 2**: Data is at the daily level. The model is shown the first window from the 1^(st) to the 90^(th) day (i.e., three months of data) and predicts the 91^(st) day’s value. Then, in the next iteration (the 2^(nd) to 91^(st) day) for training, it tries to predict the 92^(nd) day.

## Horizon-Style Data Preparation for Time-Series Forecasting

Horizon-style time-series forecasting is a technique where the model is exposed to one window of data at a time, such as days, weeks, months, quarters, or years, and attempts to predict the next *n* consecutive steps based on selection, as illustrated in Figure [6-2](#492113_1_En_6_Chapter.xhtml#Fig2).

![../images/492113_1_En_6_Chapter/492113_1_En_6_Fig2_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Fig2_HTML.jpg)

Figure 6-2

Representation of horizon-style time-series forecasting

**Example 1:** Data is at the monthly level. The model is shown the first window from the 1^(st) to the 48^(th) (which means four years of data) and predicts the values for the 49^(th) to 59^(th) months. Then, in the next iteration (the 2^(nd) to 49^(th) months) for training, it tries predicting the 50^(th) to 60^(th) months.

**Example 2:** Data is at the daily level. The model is shown the first window from the 1^(st) to 90^(th) day (i.e., three months of data) and tries to predict the values for the 91^(th) to 101^(st) days. Then, in the next iteration (the 2^(nd) to 91^(st) days) for training, it tries to predict the 92^(nd) to 102^(nd) days.

## LSTM Univariate Single-Step Style in Action

In the previous section, you learned about single-step and horizon-style time series data preparation for forecasting. In this section, let’s use single-step data preparation and LSTM to solve univariate time-series problems.

Import the required libraries and load the CSV data, which contains hourly Interstate 94 westbound traffic volumes for MN DoT ATR station 301, roughly midway between Minneapolis and St Paul, Minnesota. Hourly weather features and holidays are included that impact the traffic volume.

*   **Tensorflow** is an open source artificial intelligence library that uses data flow graphs to build models. It allows developers to create large-scale neural networks with many layers. The following code implementation has been tested and works best on Tensorflow and Tensorflow GPU version 2.0 and onward.

Let’s use this data and forecast the traffic volume for the next ten hours.

```
import pandas as pd
import numpy as np
import tensorflow as tf
from sklearn import preprocessing
import matplotlib.pyplot as plt
tf.random.set_seed(123)
np.random.seed(123)
```

Let’s take a sneak peek at the data by checking the five-point summary.

```
df = pd.read_csv(r'\Data\Metro_Interstate_Traffic_Volume.csv')
df.head()
```

![../images/492113_1_En_6_Chapter/492113_1_En_6_Figa_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Figa_HTML.jpg)

```
df.describe()
```

![../images/492113_1_En_6_Chapter/492113_1_En_6_Figb_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Figb_HTML.jpg)

Let’s drop duplicates, as the same hour has different weather conditions.

```
df.drop_duplicates(subset=['date_time'], keep=False,inplace=True)
```

For the train/test split, let’s hold back ten hours of data (i.e., ten records), which we can use to validate the data after training on the past data.

```
validate = df['traffic_volume'].tail(10)
df.drop(df['traffic_volume'].tail(10).index,inplace=True)
uni_data = df['traffic_volume']
uni_data.index = df['date_time']
uni_data.head()
```

![../images/492113_1_En_6_Chapter/492113_1_En_6_Figc_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Figc_HTML.jpg)

Let’s rescale the data because neural networks are known to converge sooner with better accuracy when features are on the same scale.

```
uni_data = uni_data.values
scaler_x = preprocessing.MinMaxScaler()
x_rescaled = scaler_x.fit_transform(uni_data.reshape(-1, 1))
```

Define a function to prepare univariate data that is suitable for a time series.

```
def custom_ts_univariate_data_prep(dataset, start, end, window, horizon):
X = []
y = []
start = start + window
if end is None:
end = len(dataset) - horizon
for i in range(start, end):
indicesx = range(i-window, i)
X.append(np.reshape(dataset[indicesx], (window, 1)))
indicesy = range(i,i+horizon)
y.append(dataset[indicesy])
return np.array(X), np.array(y)
```

As we are doing a single-step forecast, let’s allow the model to see/train on the past 48 hours of data and try to forecast the 49^(th) hour. Hence, use `horizon` = 1.

```
univar_hist_window = 48
horizon = 1
TRAIN_SPLIT = 30000
x_train_uni, y_train_uni = custom_ts_univariate_data_prep(x_rescaled, 0, TRAIN_SPLIT,univar_hist_window, horizon)
x_val_uni, y_val_uni = custom_ts_univariate_data_prep(x_rescaled, TRAIN_SPLIT, None,univar_hist_window,horizon)
print ('Single window of past history')
print (x_train_uni[0])
print ('\n Target horizon')
print (y_train_uni[0])
Single window of past history
[[0.76167582]
[0.62032967]
[0.65480769]
[0.69038462]
[0.67554945]
[0.71167582]
[0.76703297]
[0.82623626]
[0.79546703]
[0.65521978]
[0.48612637]
[0.38241758]
[0.32431319]
[0.21002747]
[0.13228022]
[0.06950549]
[0.04409341]
[0.0375    ]
[0.05041209]
[0.11181319]
[0.37335165]
[0.77925824]
[0.89436813]
[0.75151099]
[0.70013736]
[0.67129121]
[0.7331044 ]
[0.78186813]
[0.84299451]
[0.63502747]
[0.49326923]
[0.39807692]
[0.36222527]
[0.24409341]
[0.13942308]
[0.08214286]
[0.05068681]
[0.04285714]
[0.05041209]
[0.1146978 ]
[0.37445055]
[0.78145604]
[0.96016484]
[0.82211538]
[0.72925824]
[0.63228022]
[0.67087912]
[0.7010989 ]]
Target horizon
[[0.71126374]]
```

Prepare the training and validation time-series data using the `tf.data` function, which is a much faster and more efficient way of feeding data to the model.

```
BATCH_SIZE = 256
BUFFER_SIZE = 150
train_univariate = tf.data.Dataset.from_tensor_slices((x_train_uni, y_train_uni))
train_univariate = train_univariate.cache().shuffle(BUFFER_SIZE).batch(BATCH_SIZE).repeat()
val_univariate = tf.data.Dataset.from_tensor_slices((x_val_uni, y_val_uni))
val_univariate = val_univariate.batch(BATCH_SIZE).repeat()
```

The best weights are stored at `model_path`.

```
model_path = r'\Chapter 6\LSTM_Univarient_2.h5'
```

Define the LSTM model, as shown here:

```
lstm_model = tf.keras.models.Sequential([
tf.keras.layers.LSTM(100, input_shape=x_train_uni.shape[-2:],return_sequences=True),
tf.keras.layers.Dropout(0.2),
tf.keras.layers.LSTM(units=50,return_sequences=False),
tf.keras.layers.Dropout(0.2),
tf.keras.layers.Dense(units=1),
])
lstm_model.compile(optimizer='adam', loss='mse')
Configure the model and start training with early stopping and checkpointing.
Early stopping stops training when monitored loss starts to increase above patience.
Checkpointing saves model weights as it reached minimum loss.
EVALUATION_INTERVAL = 100
EPOCHS = 150
history = lstm_model.fit(train_univariate, epochs=EPOCHS,steps_per_epoch=EVALUATION_INTERVAL,validation_data=val_univariate, validation_steps=50,verbose =1,callbacks =[tf.keras.callbacks.EarlyStopping(monitor='val_loss', min_delta=0, patience=10, verbose=1, mode='min'),
tf.keras.callbacks.ModelCheckpoint(model_path,monitor='val_loss', save_best_only=True, mode='min', verbose=0)])
```

![../images/492113_1_En_6_Chapter/492113_1_En_6_Figd_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Figd_HTML.jpg)

![../images/492113_1_En_6_Chapter/492113_1_En_6_Fige_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Fige_HTML.jpg)

The previous example shows that the model stopped within the 79^(th) epoch instead of running for 150\. Load the best weights into the model.

```
Trained_model = tf.keras.models.load_model(model_path)
```

Check the model summary.

```
Trained_model.summary()
```

![../images/492113_1_En_6_Chapter/492113_1_En_6_Figf_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Figf_HTML.jpg)

Plot the `loss` and `val_loss` against the epoch.

```
plt.plot(history.history['loss'])
plt.plot(history.history['val_loss'])
plt.title('Model loss')
plt.ylabel('loss')
plt.xlabel('epoch')
plt.legend(['train loss', 'validation loss'], loc='upper left')
plt.rcParams["figure.figsize"] = [16,9]
plt.show()
```

In machine and deep learning, there are three scenarios.

*   **Underfitting**, when the validation loss is less than the training loss

*   **Overfitting**, when the validation loss is higher than the training loss

*   **Good fit**, when the validation loss is equal to the training loss

Figure [6-3](#492113_1_En_6_Chapter.xhtml#Fig3) depicts a line plot that helps us understand how a good model is able to generalize.

![../images/492113_1_En_6_Chapter/492113_1_En_6_Fig3_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Fig3_HTML.jpg)

Figure 6-3

Representation of training and validation loss as the number of epochs increases

We need to forecast the next ten steps, but our model predicts only one time step, so let’s take the last 48 hours of data from the training and increment one prediction at a time and forecast the next values.

```
uni = df['traffic_volume']
validatehori = uni.tail(48)
validatehist = validatehori.values
result = []
# Define Forecast length here
window_len = 10
val_rescaled = scaler_x.fit_transform(validatehist.reshape(-1, 1))
for i in range(1, window_len+1):
val_rescaled = val_rescaled.reshape((1, val_rescaled.shape[0], 1))
Predicted_results = Trained_model.predict(val_rescaled)
print(f'predicted : {Predicted_results}')
result.append(Predicted_results[0])
val_rescaled = np.append(val_rescaled[:,1:],[[Predicted_results]])
print(val_rescaled)
```

![../images/492113_1_En_6_Chapter/492113_1_En_6_Figg_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Figg_HTML.jpg)

Rescale the predicted values back to the original scale.

```
result_inv_trans = scaler_x.inverse_transform(result)
result_inv_trans
```

![../images/492113_1_En_6_Chapter/492113_1_En_6_Figh_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Figh_HTML.jpg)

Define the time-series evaluation function.

```
from sklearn import metrics
def timeseries_evaluation_metrics_func(y_true, y_pred):
def mean_absolute_percentage_error(y_true, y_pred):
y_true, y_pred = np.array(y_true), np.array(y_pred)
return np.mean(np.abs((y_true - y_pred) / y_true)) * 100
print('Evaluation metric results:-')
print(f'MSE is : {metrics.mean_squared_error(y_true, y_pred)}')
print(f'MAE is : {metrics.mean_absolute_error(y_true, y_pred)}')
print(f'RMSE is : {np.sqrt(metrics.mean_squared_error(y_true, y_pred))}')
print(f'MAPE is : {mean_absolute_percentage_error(y_true, y_pred)}')
print(f'R2 is : {metrics.r2_score(y_true, y_pred)}',end='\n\n')
timeseries_evaluation_metrics_func(validate,result_inv_trans)
```

![../images/492113_1_En_6_Chapter/492113_1_En_6_Figi_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Figi_HTML.jpg)

Plot the actual versus predicted values.

```
plt.plot( list(validate))
plt.plot( list(result_inv_trans))
plt.title("Actual vs Predicted")
plt.ylabel("Traffic volume")
plt.legend(('Actual','predicted'))
plt.show()
```

In perfect prediction, you would have the predicted values equal the actual values, so the line plot you see shows how much the prediction deviates from the actual value (the prediction error).

The line plot in Figure [6-4](#492113_1_En_6_Chapter.xhtml#Fig4) depicts how the actual and predicted values change through time.

![../images/492113_1_En_6_Chapter/492113_1_En_6_Fig4_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Fig4_HTML.jpg)

Figure 6-4

Representation of actual versus predicted values

## LSTM Univariate Horizon Style in Action

In this section, let’s use horizon-style data preparation and LSTM to solve univariate time-series problems.

Import the required libraries and load the CSV files .

```
import pandas as pd
import numpy as np
import tensorflow as tf
from sklearn import preprocessing
import matplotlib.pyplot as plt
tf.random.set_seed(123)
np.random.seed(123)
```

Let’s use this data and forecast the traffic volume for the next ten hours.

```
df = pd.read_csv(r'\Data\Metro_Interstate_Traffic_Volume.csv')
df.head()
```

![../images/492113_1_En_6_Chapter/492113_1_En_6_Figj_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Figj_HTML.jpg)

Let’s take a sneak peek at the data by checking the five-point summary.

```
df.describe()
```

![../images/492113_1_En_6_Chapter/492113_1_En_6_Figk_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Figk_HTML.jpg)

Let’s drop duplicates, as the same hour has different weather conditions.

```
df.drop_duplicates(subset=['date_time'], keep=False,inplace=True)
```

Now train/test the split. Let’s hold back ten hours of data (i.e., ten records), which we can use to validate the data after training on the past data.

```
validate = df['traffic_volume'].tail(10)
df.drop(df['traffic_volume'].tail(10).index,inplace=True)
uni_data = df['traffic_volume']
uni_data.index = df['date_time']
uni_data.head()
```

Let’s rescale the data as neural networks are known to converge sooner with better accuracy when features are on the same scale.

![../images/492113_1_En_6_Chapter/492113_1_En_6_Figl_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Figl_HTML.jpg)

Let’s rescale the data as neural networks are known to converge sooner with better accuracy when features are on the same scale.

```
uni_data = uni_data.values
scaler_x = preprocessing.MinMaxScaler()
x_rescaled = scaler_x.fit_transform(uni_data.reshape(-1, 1))
```

Define a function to prepare univariate data suitable for a time series.

As we are doing horizon-style forecasts, let’s allow the model to see/train on the past 48 hours of data and try forecasting the next ten hours of results; hence, use `horizon = 10`.

```
def custom_ts_univariate_data_prep(dataset, start, end, window, horizon):
X = []
y = []
start = start + window
if end is None:
end = len(dataset) - horizon
for i in range(start, end):
indicesx = range(i-window, i)
X.append(np.reshape(dataset[indicesx], (window, 1)))
indicesy = range(i, i+horizon)
y.append(dataset[indicesy])
return np.array(X), np.array(y)
univar_hist_window = 48
horizon = 10
TRAIN_SPLIT = 30000
x_train_uni, y_train_uni = custom_ts_univariate_data_prep(x_rescaled, 0, TRAIN_SPLIT,univar_hist_window, horizon)
x_val_uni, y_val_uni = custom_ts_univariate_data_prep(x_rescaled, TRAIN_SPLIT, None,univar_hist_window,horizon)
print ('Single window of past history')
print (x_train_uni[0])
print ('\n Target horizon')
print (y_train_uni[0])
Single window of past history
[[0.76167582]
[0.62032967]
[0.65480769]
[0.69038462]
[0.67554945]
[0.71167582]
[0.76703297]
[0.82623626]
[0.79546703]
[0.65521978]
[0.48612637]
[0.38241758]
[0.32431319]
[0.21002747]
[0.13228022]
[0.06950549]
[0.04409341]
[0.0375    ]
[0.05041209]
[0.11181319]
[0.37335165]
[0.77925824]
[0.89436813]
[0.75151099]
[0.70013736]
[0.67129121]
[0.7331044 ]
[0.78186813]
[0.84299451]
[0.63502747]
[0.49326923]
[0.39807692]
[0.36222527]
[0.24409341]
[0.13942308]
[0.08214286]
[0.05068681]
[0.04285714]
[0.05041209]
[0.1146978 ]
[0.37445055]
[0.78145604]
[0.96016484]
[0.82211538]
[0.72925824]
[0.63228022]
[0.67087912]
[0.7010989 ]]
Target horizon
[[0.71126374]
[0.75563187]
[0.78475275]
[0.86428571]
[0.83200549]
[0.67403846]
[0.48118132]
[0.41717033]
[0.38763736]
[0.27362637]]
```

Prepare the training and validation time-series data using the `tf.data` function, which is a much faster and more efficient way of feeding data to the model.

```
BATCH_SIZE = 256
BUFFER_SIZE = 150
train_univariate = tf.data.Dataset.from_tensor_slices((x_train_uni, y_train_uni))
train_univariate = train_univariate.cache().shuffle(BUFFER_SIZE).batch(BATCH_SIZE).repeat()
val_univariate = tf.data.Dataset.from_tensor_slices((x_val_uni, y_val_uni))
val_univariate = val_univariate.batch(BATCH_SIZE).repeat()
```

The best weights are stored at `model_path`.

```
model_path = r'\Chapter 6\LSTM_Univarient_1.h5'
```

Define the LSTM model.

```
lstm_model = tf.keras.models.Sequential([
tf.keras.layers.LSTM(100, input_shape=x_train_uni.shape[-2:],return_sequences=True),
tf.keras.layers.Dropout(0.2),
tf.keras.layers.LSTM(units=50,return_sequences=False),
tf.keras.layers.Dropout(0.2),
tf.keras.layers.Dense(units=horizon),
])
lstm_model.compile(optimizer='adam', loss='mse')
```

Configure the model and start training with early stopping and checkpointing.

Early stopping stops the training when the monitored loss starts to increase above the patience.

Checkpointing saves the model weights as it reaches the minimum loss.

```
EVALUATION_INTERVAL = 100
EPOCHS = 150
history = lstm_model.fit(train_univariate, epochs=EPOCHS,steps_per_epoch=EVALUATION_INTERVAL,validation_data=val_univariate, validation_steps=50,verbose =1,callbacks =[tf.keras.callbacks.
EarlyStopping(monitor='val_loss', min_delta=0, patience=10, verbose=1, mode='min'),tf.keras.callbacks.ModelCheckpoint(model_path,monitor='val_loss', save_best_only=True, mode='min', verbose=0)])
```

![../images/492113_1_En_6_Chapter/492113_1_En_6_Figm_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Figm_HTML.jpg)

![../images/492113_1_En_6_Chapter/492113_1_En_6_Fign_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Fign_HTML.jpg)

The previous example shows that the model stopped within the 72^(nd) epoch instead of running for 150\. Load the best weights into the model.

```
Trained_model = tf.keras.models.load_model(model_path)
Trained_model.summary()
```

Check the model summary.

![../images/492113_1_En_6_Chapter/492113_1_En_6_Figo_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Figo_HTML.jpg)

Plot the `loss` and `val_loss` against the epoch.

```
plt.plot(history.history['loss'])
plt.plot(history.history['val_loss'])
plt.title('Model loss')
plt.ylabel('loss')
plt.xlabel('epoch')
plt.legend(['train loss', 'validation loss'], loc='upper left')
plt.rcParams["figure.figsize"] = [16,9]
plt.show()
```

Figure [6-5](#492113_1_En_6_Chapter.xhtml#Fig5) depicts a line plot that helps us to understand how a good model is able to generalize.

![../images/492113_1_En_6_Chapter/492113_1_En_6_Fig5_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Fig5_HTML.jpg)

Figure 6-5

Representation of training and validation loss as the number of epochs increases

We need to forecast the next ten steps, so let’s take the last 48 hours of data from the training model and forecast the next ten hours of values.

```
uni = df['traffic_volume']
validatehori = uni.tail(48)
validatehist = validatehori.values
scaler_val = preprocessing.MinMaxScaler()
val_rescaled = scaler_x.fit_transform(validatehist.reshape(-1, 1))
val_rescaled = val_rescaled.reshape((1, val_rescaled.shape[0], 1))
Predicted_results = Trained_model.predict(val_rescaled)
Predicted_results
```

![../images/492113_1_En_6_Chapter/492113_1_En_6_Figp_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Figp_HTML.jpg)

Rescale the predicted values back to the original scale, as shown here:

```
Predicted_inver_res = scaler_x.inverse_transform(Predicted_results)
Predicted_inver_res
```

![../images/492113_1_En_6_Chapter/492113_1_En_6_Figq_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Figq_HTML.jpg)

Define the time-series evaluation function, as shown here:

```
from sklearn import metrics
def timeseries_evaluation_metrics_func(y_true, y_pred):
def mean_absolute_percentage_error(y_true, y_pred):
y_true, y_pred = np.array(y_true), np.array(y_pred)
return np.mean(np.abs((y_true - y_pred) / y_true)) * 100
print('Evaluation metric results:-')
print(f'MSE is : {metrics.mean_squared_error(y_true, y_pred)}')
print(f'MAE is : {metrics.mean_absolute_error(y_true, y_pred)}')
print(f'RMSE is : {np.sqrt(metrics.mean_squared_error(y_true, y_pred))}')
print(f'MAPE is : {mean_absolute_percentage_error(y_true, y_pred)}')
print(f'R2 is : {metrics.r2_score(y_true, y_pred)}',end='\n\n')
timeseries_evaluation_metrics_func(validate,Predicted_inver_res[0])
```

![../images/492113_1_En_6_Chapter/492113_1_En_6_Figr_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Figr_HTML.jpg)

```
plt.plot( list(validate))
plt.plot( list(Predicted_inver_res[0]))
plt.title("Actual vs Predicted")
plt.ylabel("Traffic volume")
plt.legend(('Actual','predicted'))
plt.show()
```

Figure [6-6](#492113_1_En_6_Chapter.xhtml#Fig6) shows a line plot that depicts how the actual and predicted values change through time.

![../images/492113_1_En_6_Chapter/492113_1_En_6_Fig6_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Fig6_HTML.jpg)

Figure 6-6

*Representation of actual versus predicted values*

## Bidirectional LSTM Univariate Single-Step Style in Action

In this section, let’s use single-step data preparation and bidirectional LSTM to solve univariate time-series problems.

Import the required libraries and load the CSV files.

```
import pandas as pd
import numpy as np
import tensorflow as tf
from sklearn import preprocessing
import matplotlib.pyplot as plt
tf.random.set_seed(123)
np.random.seed(123)
```

Let’s load and take a sneak peek at the data by checking the five-point summary.

```
df = pd.read_csv(r'\Data\Metro_Interstate_Traffic_Volume.csv')
df.head()
```

![../images/492113_1_En_6_Chapter/492113_1_En_6_Figs_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Figs_HTML.jpg)

```
df.describe()
```

![../images/492113_1_En_6_Chapter/492113_1_En_6_Figt_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Figt_HTML.jpg)

Let’s drop duplicates, as the same hour has different weather conditions.

```
df.drop_duplicates(subset=['date_time'], keep=False,inplace=True)
```

Train/test the split. Let’s hold back ten hours of data (i.e., ten records), which we can use to validate the data after training on the past data.

```
validate = df['traffic_volume'].tail(10)
df.drop(df['traffic_volume'].tail(10).index,inplace=True
uni_data = df['traffic_volume']
uni_data.index = df['date_time']
uni_data.head()
```

![../images/492113_1_En_6_Chapter/492113_1_En_6_Figu_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Figu_HTML.jpg)

Let’s rescale the data as neural networks are known to converge sooner with better accuracy when features are on the same scale.

```
uni_data = uni_data.values
scaler_x = preprocessing.MinMaxScaler()
x_rescaled = scaler_x.fit_transform(uni_data.reshape(-1, 1))
```

Define a function to prepare univariate data suitable for time-series problems.

```
def custom_ts_univariate_data_prep(dataset, start, end, window, horizon):
X = []
y = []
start = start + window
if end is None:
end = len(dataset) - horizon
for i in range(start, end):
indicesx = range(i-window, i)
X.append(np.reshape(dataset[indicesx], (window, 1)))
indicesy = range(i,i+horizon)
y.append(dataset[indicesy])
return np.array(X), np.array(y)
```

As we are doing single-step forecasting, let’s allow the model to see/train on the past 48 hours of data and try to forecast the 49^(th) hour; hence, use `horizon` = 1.

```
univar_hist_window = 48
horizon = 1
TRAIN_SPLIT = 30000
x_train_uni, y_train_uni = custom_ts_univariate_data_prep(x_rescaled, 0, TRAIN_SPLIT,univar_hist_window, horizon)
x_val_uni, y_val_uni = custom_ts_univariate_data_prep(x_rescaled, TRAIN_SPLIT, None,univar_hist_window,horizon)
```

Prepare the training and validation time-series data using the `tf.data` function, which is a much faster and more efficient way of feeding data to the model.

```
BATCH_SIZE = 256
BUFFER_SIZE = 150
train_univariate = tf.data.Dataset.from_tensor_slices((x_train_uni, y_train_uni))
train_univariate = train_univariate.cache().shuffle(BUFFER_SIZE).batch(BATCH_SIZE).repeat()
val_univariate = tf.data.Dataset.from_tensor_slices((x_val_uni, y_val_uni))
val_univariate = val_univariate.batch(BATCH_SIZE).repeat()
```

Define the bidirectional LSTM model.

```
Bi_lstm_model = tf.keras.models.Sequential([
tf.keras.layers.Bidirectional(tf.keras.layers.LSTM(100, return_sequences=True), input_shape=x_train_uni.shape[-2:]),
tf.keras.layers.Bidirectional(tf.keras.layers.LSTM(50)),
tf.keras.layers.Dense(20, activation='softmax'),
tf.keras.layers.Dropout(0.2),
tf.keras.layers.Dense(units=1),
])
Bi_lstm_model.compile(optimizer='adam', loss='mse')
```

The best weights are stored at `model_path`.

```
model_path = r'\Chapter 6\Bi_directional_LSTM_Univarient_2.h5'
```

Configure the model and start training with early stopping and checkpointing.

Early stopping stops training when the monitored loss starts to increase above patience.

Checkpointing saves the model weights as it reaches the minimum loss.

```
EVALUATION_INTERVAL = 100
EPOCHS = 150
history = Bi_lstm_model.fit(train_univariate, epochs=EPOCHS,steps_per_epoch=EVALUATION_INTERVAL,validation_data=val_univariate, validation_steps=50,verbose =1,
callbacks =[tf.keras.callbacks.EarlyStopping(monitor='val_loss', min_delta=0, patience=10, verbose=1, mode='min'),tf.keras.callbacks.ModelCheckpoint(model_path,monitor='val_loss', save_best_only=True, mode='min', verbose=0)])
```

![../images/492113_1_En_6_Chapter/492113_1_En_6_Figv_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Figv_HTML.jpg)

![../images/492113_1_En_6_Chapter/492113_1_En_6_Figw_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Figw_HTML.jpg)

The previous example shows the model stopped within 86 epochs instead of running for 150\. Load the best weights into the model.

```
Trained_model = tf.keras.models.load_model(model_path)
```

Check the model summary.

```
Trained_model.summary()
```

![../images/492113_1_En_6_Chapter/492113_1_En_6_Figx_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Figx_HTML.jpg)

Check the model summary.

```
plt.plot(history.history['loss'])
plt.plot(history.history['val_loss'])
plt.title('Model loss')
plt.ylabel('loss')
plt.xlabel('epoch')
plt.legend(['train loss', 'validation loss'], loc='upper left')
plt.rcParams["figure.figsize"] = [16,9]
plt.show()
```

Figure [6-7](#492113_1_En_6_Chapter.xhtml#Fig7) depicts a line plot that helps us understand how a good model is able to generalize.

![../images/492113_1_En_6_Chapter/492113_1_En_6_Fig7_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Fig7_HTML.jpg)

Figure 6-7

Representation of training and validation loss as the number of epochs increases

We need to forecast the next ten steps, but our model predicts only one time step, so let’s take the last 48 hours of data from the training and increment one prediction at a time and forecast the next values.

```
uni = df['traffic_volume']
validatehori = uni.tail(48)
validatehist = validatehori.values
result = []
# Define Forecast length here
window_len = 10
val_rescaled = scaler_x.fit_transform(validatehist.reshape(-1, 1))
for i in range(1, window_len+1):
val_rescaled = val_rescaled.reshape((1, val_rescaled.shape[0], 1))
Predicted_results = Trained_model.predict(val_rescaled)
print(f'predicted : {Predicted_results}')
result.append(Predicted_results[0])
val_rescaled = np.append(val_rescaled[:,1:],[[Predicted_results]])
print(val_rescaled)
```

![../images/492113_1_En_6_Chapter/492113_1_En_6_Figy_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Figy_HTML.jpg)

Rescale the predicted values back to the original scale.

```
result_inv_trans = scaler_x.inverse_transform(result)
```

Define the time-series evaluation function.

```
from sklearn import metrics
def timeseries_evaluation_metrics_func(y_true, y_pred):
def mean_absolute_percentage_error(y_true, y_pred):
y_true, y_pred = np.array(y_true), np.array(y_pred)
return np.mean(np.abs((y_true - y_pred) / y_true)) * 100
print('Evaluation metric results:-')
print(f'MSE is : {metrics.mean_squared_error(y_true, y_pred)}')
print(f'MAE is : {metrics.mean_absolute_error(y_true, y_pred)}')
print(f'RMSE is : {np.sqrt(metrics.mean_squared_error(y_true, y_pred))}')
print(f'MAPE is : {mean_absolute_percentage_error(y_true, y_pred)}')
print(f'R2 is : {metrics.r2_score(y_true, y_pred)}',end='\n\n')
timeseries_evaluation_metrics_func(validate,result_inv_trans)
```

![../images/492113_1_En_6_Chapter/492113_1_En_6_Figz_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Figz_HTML.jpg)

Plot the actual versus predicted values.

```
plt.plot( list(validate))
plt.plot( list(result_inv_trans))
plt.title("Actual vs Predicted")
plt.ylabel("Traffic volume")
plt.legend(('Actual','predicted'))
plt.show()
```

Figure [6-8](#492113_1_En_6_Chapter.xhtml#Fig8) shows a line plot that depicts how the actual and predicted values change through time.

![../images/492113_1_En_6_Chapter/492113_1_En_6_Fig8_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Fig8_HTML.jpg)

Figure 6-8

Representation of actual versus predicted values

## Bidirectional LSTM Univariate Horizon Style in Action

In this section, let’s use horizon-style data preparation and bidirectional LSTM to solve univariate times-series problems.

Import the required libraries and load the CSV files.

```
import pandas as pd
import numpy as np
import tensorflow as tf
from sklearn import preprocessing
import matplotlib.pyplot as plt
tf.random.set_seed(123)
np.random.seed(123)
```

Let’s take a sneak peek at the data by checking the five-point summary.

```
df = pd.read_csv(r'\Data\Metro_Interstate_Traffic_Volume.csv')
df.head()
```

![../images/492113_1_En_6_Chapter/492113_1_En_6_Figaa_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Figaa_HTML.jpg)

```
df.describe()
```

![../images/492113_1_En_6_Chapter/492113_1_En_6_Figab_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Figab_HTML.jpg)

Let’s drop duplicates, as the same hour has different weather conditions.

```
df.drop_duplicates(subset=['date_time'], keep=False,inplace=True)
```

Train/test the split. Let’s hold back ten hours of data (i.e., ten records), which we can use to validate the data after training on the past data.

```
validate = df['traffic_volume'].tail(10)
df.drop(df['traffic_volume'].tail(10).index,inplace=True)
uni_data = df['traffic_volume']
uni_data.index = df['date_time']
uni_data.head()
```

![../images/492113_1_En_6_Chapter/492113_1_En_6_Figac_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Figac_HTML.jpg)

Let’s rescale the data as neural networks are known to converge sooner with better accuracy when features are on the same scale.

```
uni_data = uni_data.values
scaler_x = preprocessing.MinMaxScaler()
x_rescaled = scaler_x.fit_transform(uni_data.reshape(-1, 1))
```

Define a function to prepare univariate data suitable for a time series.

As we are doing horizon-style forecasts, let’s allow the model to see/train on the past 48 hours of data and try forecast the next ten hours of results. Hence, use `horizon = 10`.

```
def custom_ts_univariate_data_prep(dataset, start, end, window, horizon):
X = []
y = []
start = start + window
if end is None:
end = len(dataset) - horizon
for i in range(start, end):
indicesx = range(i-window, i)
X.append(np.reshape(dataset[indicesx], (window, 1)))
indicesy = range(i,i+horizon)
y.append(dataset[indicesy])
return np.array(X), np.array(y)
univar_hist_window = 48
horizon = 10
TRAIN_SPLIT = 30000
x_train_uni, y_train_uni = custom_ts_univariate_data_prep(x_rescaled, 0, TRAIN_SPLIT,univar_hist_window, horizon)
x_val_uni, y_val_uni = custom_ts_univariate_data_prep(x_rescaled, TRAIN_SPLIT, None,univar_hist_window,horizon)
```

Prepare the training and validation time-series data using the `tf.data` function, which is a much faster and more efficient way of feeding data to the model.

```
BATCH_SIZE = 256
BUFFER_SIZE = 150
train_univariate = tf.data.Dataset.from_tensor_slices((x_train_uni, y_train_uni))
train_univariate = train_univariate.cache().shuffle(BUFFER_SIZE).batch(BATCH_SIZE).repeat()
val_univariate = tf.data.Dataset.from_tensor_slices((x_val_uni, y_val_uni))
val_univariate = val_univariate.batch(BATCH_SIZE).repeat()
```

Here is the bidirectional LSTM model:

```
Bi_lstm_model = tf.keras.models.Sequential([
tf.keras.layers.Bidirectional(tf.keras.layers.LSTM(100, return_sequences=True),
input_shape=x_train_uni.shape[-2:]),
tf.keras.layers.Bidirectional(tf.keras.layers.LSTM(50)),
tf.keras.layers.Dense(20, activation='softmax'),
tf.keras.layers.Dropout(0.2),
tf.keras.layers.Dense(units=horizon),
])
Bi_lstm_model.compile(optimizer='adam', loss='mse')
```

The best weights are stored at `model_path`.

```
model_path = r'\Chapter 6\Bidirectional_LSTM_Univarient_1.h5'
```

Configure the model and start training with early stopping and checkpointing.

Early stopping stops the training when the monitored loss starts to increase above the patience.

Checkpointing saves the model weights as they reach the minimum loss.

```
EVALUATION_INTERVAL = 100
EPOCHS = 100
history = Bi_lstm_model.fit(train_univariate, epochs=EPOCHS,steps_per_epoch=EVALUATION_INTERVAL,validation_data=val_univariate, validation_steps=50,verbose =1,
callbacks =[tf.keras.callbacks.EarlyStopping(monitor='val_loss', min_delta=0, patience=10, verbose=1, mode='min'),tf.keras.callbacks.ModelCheckpoint(model_path,monitor='val_loss', save_best_only=True, mode='min', verbose=0)])
```

![../images/492113_1_En_6_Chapter/492113_1_En_6_Figad_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Figad_HTML.jpg)

![../images/492113_1_En_6_Chapter/492113_1_En_6_Figae_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Figae_HTML.jpg)

The previous example shows that the model stopped within 79 epochs instead of running for 150\. Load the best weights into the model.

```
Trained_model = tf.keras.models.load_model(model_path)
```

Check the model summary.

```
Trained_model.summary()
```

![../images/492113_1_En_6_Chapter/492113_1_En_6_Figaf_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Figaf_HTML.jpg)

Plot the `loss` and `val_loss` against the epoch.

```
plt.plot(history.history['loss'])
plt.plot(history.history['val_loss'])
plt.title('Model loss')
plt.ylabel('loss')
plt.xlabel('epoch')
plt.legend(['train loss', 'validation loss'], loc='upper left')
plt.rcParams["figure.figsize"] = [16,9]
plt.show()
```

Figure [6-9](#492113_1_En_6_Chapter.xhtml#Fig9) depicts a line plot that helps us understand how a good model is able to generalize.

![../images/492113_1_En_6_Chapter/492113_1_En_6_Fig9_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Fig9_HTML.jpg)

Figure 6-9

Representation of training and validation loss as the number of epochs increases

We need to forecast the next ten steps, so let’s take the last 48 hours of data from the training model and forecast the next ten hours of values.

```
uni = df['traffic_volume']
validatehori = uni.tail(48)
validatehist = validatehori.values
scaler_val = preprocessing.StandardScaler()
val_rescaled = scaler_x.fit_transform(validatehist.reshape(-1, 1))
val_rescaled = val_rescaled.reshape((1, val_rescaled.shape[0], 1))
Predicted_results = Trained_model.predict(val_rescaled)
Predicted_results
```

![../images/492113_1_En_6_Chapter/492113_1_En_6_Figag_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Figag_HTML.jpg)

Rescale the predicted values back to the original scale.

```
Predicted_inver_res = scaler_x.inverse_transform(Predicted_results)
Predicted_inver_res
```

![../images/492113_1_En_6_Chapter/492113_1_En_6_Figah_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Figah_HTML.jpg)

Define the time-series evaluation function.

```
from sklearn import metrics
def timeseries_evaluation_metrics_func(y_true, y_pred):
def mean_absolute_percentage_error(y_true, y_pred):
y_true, y_pred = np.array(y_true), np.array(y_pred)
return np.mean(np.abs((y_true - y_pred) / y_true)) * 100
print('Evaluation metric results:-')
print(f'MSE is : {metrics.mean_squared_error(y_true, y_pred)}')
print(f'MAE is : {metrics.mean_absolute_error(y_true, y_pred)}')
print(f'RMSE is : {np.sqrt(metrics.mean_squared_error(y_true, y_pred))}')
print(f'MAPE is : {mean_absolute_percentage_error(y_true, y_pred)}')
print(f'R2 is : {metrics.r2_score(y_true, y_pred)}',end='\n\n')
timeseries_evaluation_metrics_func(validate,Predicted_inver_res[0])
```

![../images/492113_1_En_6_Chapter/492113_1_En_6_Figai_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Figai_HTML.jpg)

Plot the actual versus predicted values.

```
plt.plot( list(validate))
plt.plot( list(Predicted_inver_res[0]))
plt.title("Actual vs Predicted")
plt.ylabel("Traffic volume")
plt.legend(('Actual','predicted'))
plt.show()
```

Figure [6-10](#492113_1_En_6_Chapter.xhtml#Fig10) shows a line plot that depicts how the actual and predicted values change through time.

![../images/492113_1_En_6_Chapter/492113_1_En_6_Fig10_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Fig10_HTML.jpg)

Figure 6-10

Representation of actual versus predicted values

## GRU Univariate Single-Step Style in Action

In this section, let’s use single-step data preparation and GRU to solve univariate time-series problems.

Import the required libraries and load the CSV files.

```
import pandas as pd
import numpy as np
import tensorflow as tf
from sklearn import preprocessing
import matplotlib.pyplot as plt
tf.random.set_seed(123)
np.random.seed(123)
```

Let’s load and take a sneak peek at the data by checking the five-point summary.

```
df = pd.read_csv(r'\Data\Metro_Interstate_Traffic_Volume.csv')
df.head()
```

![../images/492113_1_En_6_Chapter/492113_1_En_6_Figaj_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Figaj_HTML.jpg)

```
df.describe()
```

![../images/492113_1_En_6_Chapter/492113_1_En_6_Figak_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Figak_HTML.jpg)

Let’s drop duplicates, as the same hour contains different weather conditions.

```
df.drop_duplicates(subset=['date_time'], keep=False,inplace=True)
```

Train/test the split. Let’s hold back ten hours of data (i.e., ten records), which we can use to validate the data after training on the past data.

```
validate = df['traffic_volume'].tail(10)
df.drop(df['traffic_volume'].tail(10).index,inplace=True)
uni_data = df['traffic_volume']
uni_data.index = df['date_time']
uni_data.head()
```

![../images/492113_1_En_6_Chapter/492113_1_En_6_Figal_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Figal_HTML.jpg)

Let’s rescale the data as neural networks are known to converge sooner with better accuracy when features are on the same scale.

```
uni_data = uni_data.values
scaler_x = preprocessing.MinMaxScaler()
x_rescaled = scaler_x.fit_transform(uni_data.reshape(-1, 1))
```

Define a function to prepare univariate data suitable for a time series.

```
def custom_ts_univariate_data_prep(dataset, start, end, window, horizon):
X = []
y = []
start = start + window
if end is None:
end = len(dataset) - horizon
for i in range(start, end):
indicesx = range(i-window, i)
X.append(np.reshape(dataset[indicesx], (window, 1)))
indicesy = range(i,i+horizon)
y.append(dataset[indicesy])
return np.array(X), np.array(y)
```

As we are doing single-step forecasting, let’s allow the model to see/train on the past 48 hours of data and try to forecast the 49^(th) hour; hence, use `horizon = 1`.

```
univar_hist_window = 48
horizon = 1
TRAIN_SPLIT = 30000
x_train_uni, y_train_uni = custom_ts_univariate_data_prep(x_rescaled, 0, TRAIN_SPLIT,univar_hist_window, horizon)
x_val_uni, y_val_uni = custom_ts_univariate_data_prep(x_rescaled, TRAIN_SPLIT, None,univar_hist_window,horizon)
```

Prepare the training and validation time-series data using the `tf.data` function, which is a much faster and more efficient way of feeding data to the model.

```
BATCH_SIZE = 256
BUFFER_SIZE = 150
train_univariate = tf.data.Dataset.from_tensor_slices((x_train_uni, y_train_uni))
train_univariate = train_univariate.cache().shuffle(BUFFER_SIZE).batch(BATCH_SIZE).repeat()
val_univariate = tf.data.Dataset.from_tensor_slices((x_val_uni, y_val_uni))
val_univariate = val_univariate.batch(BATCH_SIZE).repeat()
```

Define the GRU model.

```
GRU_model = tf.keras.models.Sequential([
tf.keras.layers.GRU(100, input_shape=x_train_uni.shape[-2:],return_sequences=True),
tf.keras.layers.Dropout(0.2),
tf.keras.layers.GRU(units=50,return_sequences=False),
tf.keras.layers.Dropout(0.2),
tf.keras.layers.Dense(units=1),
])
GRU_model.compile(optimizer='adam', loss='mse')
```

The best weights are stored at `model_path`.

```
model_path = r'\Chapter 6\GRU_Univarient_2.h5'
```

Configure the model and start training with early stopping and checkpointing.

Early stopping stops the training when the monitored loss starts to increase above the patience.

Checkpointing saves the model weights as it reach the minimum loss.

```
EVALUATION_INTERVAL = 100
EPOCHS = 150
history = GRU_model.fit(train_univariate, epochs=EPOCHS,steps_per_epoch=EVALUATION_INTERVAL,validation_data=val_univariate, validation_steps=50,verbose =1, callbacks =[tf.keras.callbacks.
EarlyStopping(monitor='val_loss', min_delta=0, patience=10, verbose=1, mode='min'),tf.keras.callbacks.ModelCheckpointDmodel_path,monitor='val_loss', save_best_only=True, mode='min', verbose=0)])
```

![../images/492113_1_En_6_Chapter/492113_1_En_6_Figam_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Figam_HTML.jpg)

![../images/492113_1_En_6_Chapter/492113_1_En_6_Figan_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Figan_HTML.jpg)

The previous example shows the model stopped within the 86^(th) epoch instead of running for 150\. Load the best weights into the model.

```
Trained_model = tf.keras.models.load_model(model_path)
```

Check the model summary.

```
Trained_model.summary()
```

![../images/492113_1_En_6_Chapter/492113_1_En_6_Figao_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Figao_HTML.jpg)

Plot the `loss` and `val_loss` against the epoch.

```
plt.plot(history.history['loss'])
plt.plot(history.history['val_loss'])
plt.title('Model loss')
plt.ylabel('loss')
plt.xlabel('epoch')
plt.legend(['train loss', 'validation loss'], loc='upper left')
plt.rcParams["figure.figsize"] = [16,9]
plt.show()
```

Figure [6-11](#492113_1_En_6_Chapter.xhtml#Fig11) depicts a line plot that helps us to understand how a good model is able to generalize.

![../images/492113_1_En_6_Chapter/492113_1_En_6_Fig11_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Fig11_HTML.jpg)

Figure 6-11

Representation of training and validation loss as the number of epochs increases

We need to forecast the next ten steps, but our model predicts only one time step, so let’s take the last 48 hours of data from the training and increment one prediction at a time and forecast the next values.

```
uni = df['traffic_volume']
validatehori = uni.tail(48)
validatehist = validatehori.values
result = []
# Define Forecast length here
window_len = 10
val_rescaled = scaler_x.fit_transform(validatehist.reshape(-1, 1))
for i in range(1, window_len+1):
val_rescaled = val_rescaled.reshape((1, val_rescaled.shape[0], 1))
Predicted_results = Trained_model.predict(val_rescaled)
print(f'predicted : {Predicted_results}')
result.append(Predicted_results[0])
val_rescaled = np.append(val_rescaled[:,1:],[[Predicted_results]])
print(val_rescaled)
```

![../images/492113_1_En_6_Chapter/492113_1_En_6_Figap_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Figap_HTML.jpg)

Rescale the predicted values back to the original scale.

```
result_inv_trans = scaler_x.inverse_transform(result)
```

Define the time-series evaluation function.

```
from sklearn import metrics
def timeseries_evaluation_metrics_func(y_true, y_pred):
def mean_absolute_percentage_error(y_true, y_pred):
y_true, y_pred = np.array(y_true), np.array(y_pred)
return np.mean(np.abs((y_true - y_pred) / y_true)) * 100
print('Evaluation metric results:-')
print(f'MSE is : {metrics.mean_squared_error(y_true, y_pred)}')
print(f'MAE is : {metrics.mean_absolute_error(y_true, y_pred)}')
print(f'RMSE is : {np.sqrt(metrics.mean_squared_error(y_true, y_pred))}')
print(f'MAPE is : {mean_absolute_percentage_error(y_true, y_pred)}')
print(f'R2 is : {metrics.r2_score(y_true, y_pred)}',end='\n\n')
timeseries_evaluation_metrics_func(validate,result_inv_trans)
```

![../images/492113_1_En_6_Chapter/492113_1_En_6_Figaq_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Figaq_HTML.jpg)

Plot the actual versus predicted values.

```
plt.plot( list(validate))
plt.plot( list(result_inv_trans))
plt.title("Actual vs Predicted")
plt.ylabel("Traffic volume")
plt.legend(('Actual','predicted'))
plt.show()
```

Figure [6-12](#492113_1_En_6_Chapter.xhtml#Fig12) shows a line plot that depicts how the actual and predicted values change through time.

![../images/492113_1_En_6_Chapter/492113_1_En_6_Fig12_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Fig12_HTML.jpg)

Figure 6-12

Representation of actual versus predicted values

## GRU Univariate Horizon Style in Action

In this section, let’s use horizon-style data preparation and GRU to solve univariate time-series problems.

Import the required libraries and load the CSV files.

```
import pandas as pd
import numpy as np
import tensorflow as tf
from sklearn import preprocessing
import matplotlib.pyplot as plt
tf.random.set_seed(123)
np.random.seed(123)
```

Let’s load and take a sneak peek at the data by checking the five-point summary.

```
df = pd.read_csv(r'\Data\Metro_Interstate_Traffic_Volume.csv')
df.head()
```

![../images/492113_1_En_6_Chapter/492113_1_En_6_Figar_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Figar_HTML.jpg)

```
df.describe()
```

![../images/492113_1_En_6_Chapter/492113_1_En_6_Figas_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Figas_HTML.jpg)

Let’s drop duplicates, as the same hour contains different weather conditions.

```
df.drop_duplicates(subset=['date_time'], keep=False,inplace=True)
```

Train/test the split. Let’s hold back ten hours of data (i.e., ten records), which we can use to validate the data after training on the past data.

```
validate = df['traffic_volume'].tail(10)
df.drop(df['traffic_volume'].tail(10).index,inplace=True)
uni_data = df['traffic_volume']
uni_data.index = df['date_time']
uni_data.head()
```

![../images/492113_1_En_6_Chapter/492113_1_En_6_Figat_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Figat_HTML.jpg)

Define a function to prepare univariate data suitable for a time series.

As we are doing horizon-style forecasting, let’s allow the model to see/train on the past 48 hours of data and try to forecast the next ten hours of results; hence, use `horizon = 10`.

```
uni_data = uni_data.values
scaler_x = preprocessing.MinMaxScaler()
x_rescaled = scaler_x.fit_transform(uni_data.reshape(-1, 1))
def custom_ts_univariate_data_prep(dataset, start, end, window, horizon):
X = []
y = []
start = start + window
if end is None:
end = len(dataset) - horizon
for i in range(start, end):
indicesx = range(i-window, i)
X.append(np.reshape(dataset[indicesx], (window, 1)))
indicesy = range(i,i+horizon)
y.append(dataset[indicesy])
return np.array(X), np.array(y)
univar_hist_window = 48
horizon = 10
TRAIN_SPLIT = 30000
x_train_uni, y_train_uni = custom_ts_univariate_data_prep(x_rescaled, 0, TRAIN_SPLIT,univar_hist_window, horizon)
x_val_uni, y_val_uni = custom_ts_univariate_data_prep(x_rescaled, TRAIN_SPLIT, None,univar_hist_window,horizon)
```

Prepare the training and validation time-series data using the `tf.data` function, which is a much faster and more efficient way of feeding data to the model.

```
BATCH_SIZE = 256
BUFFER_SIZE = 150
train_univariate = tf.data.Dataset.from_tensor_slices((x_train_uni, y_train_uni))
train_univariate = train_univariate.cache().shuffle(BUFFER_SIZE).batch(BATCH_SIZE).repeat()
val_univariate = tf.data.Dataset.from_tensor_slices((x_val_uni, y_val_uni))
val_univariate = val_univariate.batch(BATCH_SIZE).repeat()
```

Define the GRU model.

```
GRU_model = tf.keras.models.Sequential([
tf.keras.layers.GRU(100, input_shape=x_train_uni.shape[-2:],return_sequences=True),
tf.keras.layers.Dropout(0.2),
tf.keras.layers.GRU(units=50,return_sequences=False),
tf.keras.layers.Dropout(0.2),
tf.keras.layers.Dense(units=horizon),
])
GRU_model.compile(optimizer='adam', loss='mse')
```

The best weights are stored at `model_path`.

```
model_path = r'\Chapter 6\GRU_Univarient_1.h5'
```

Configure the model and start training with early stopping and checkpointing.

Early stopping stops the training when the monitored loss starts to increase above the patience.

Checkpointing saves the model weights as it reaches the minimum loss.

```
EVALUATION_INTERVAL = 100
EPOCHS = 150
history = GRU_model.fit(train_univariate, epochs=EPOCHS,steps_per_epoch=EVALUATION_INTERVAL,validation_data=val_univariate, validation_steps=50,verbose =1,callbacks =[tf.keras.callbacks.
EarlyStopping(monitor='val_loss', min_delta=0, patience=10, verbose=1, mode='min'),tf.keras.callbacks.ModelCheckpoint(model_path,monitor='val_loss', save_best_only=True, mode='min', verbose=0)])
```

![../images/492113_1_En_6_Chapter/492113_1_En_6_Figau_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Figau_HTML.jpg)

![../images/492113_1_En_6_Chapter/492113_1_En_6_Figav_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Figav_HTML.jpg)

The previous example shows the model stopped within 17 epochs instead of running for 150.

Load the best weights into the model.

```
Trained_model = tf.keras.models.load_model(model_path)
```

Check the model summary.

```
Trained_model.summary()
```

![../images/492113_1_En_6_Chapter/492113_1_En_6_Figaw_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Figaw_HTML.jpg)

Plot the `loss` and `val_loss` against the epoch.

```
plt.plot(history.history['loss'])
plt.plot(history.history['val_loss'])
plt.title('Model loss')
plt.ylabel('loss')
plt.xlabel('epoch')
plt.legend(['train loss', 'validation loss'], loc='upper left')
plt.rcParams["figure.figsize"] = [16,9]
plt.show()
```

Figure [6-13](#492113_1_En_6_Chapter.xhtml#Fig13) depicts a line plot that helps us understand how a good model is able to generalize.

![../images/492113_1_En_6_Chapter/492113_1_En_6_Fig13_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Fig13_HTML.jpg)

Figure 6-13

Representation of training and validation loss as the number of epochs increases

We need to forecast the next ten steps, so let’s take the last 48 hours of data from the training model and forecast the next ten hours of values.

```
uni = df['traffic_volume']
validatehori = uni.tail(48)
validatehist = validatehori.values
scaler_val = preprocessing.MinMaxScaler()
val_rescaled = scaler_x.fit_transform(validatehist.reshape(-1, 1))
val_rescaled = val_rescaled.reshape((1, val_rescaled.shape[0], 1))
```

Rescale the predicted values back to the original scale.

```
Predicted_results = Trained_model.predict(val_rescaled)
Predicted_results
```

![../images/492113_1_En_6_Chapter/492113_1_En_6_Figax_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Figax_HTML.jpg)

```
Predicted_inver_res = scaler_x.inverse_transform(Predicted_results)
Predicted_inver_res
```

![../images/492113_1_En_6_Chapter/492113_1_En_6_Figay_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Figay_HTML.jpg)

Define the time-series evaluation function.

```
from sklearn import metrics
def timeseries_evaluation_metrics_func(y_true, y_pred):
def mean_absolute_percentage_error(y_true, y_pred):
y_true, y_pred = np.array(y_true), np.array(y_pred)
return np.mean(np.abs((y_true - y_pred) / y_true)) * 100
print('Evaluation metric results:-')
print(f'MSE is : {metrics.mean_squared_error(y_true, y_pred)}')
print(f'MAE is : {metrics.mean_absolute_error(y_true, y_pred)}')
print(f'RMSE is : {np.sqrt(metrics.mean_squared_error(y_true, y_pred))}')
print(f'MAPE is : {mean_absolute_percentage_error(y_true, y_pred)}')
print(f'R2 is : {metrics.r2_score(y_true, y_pred)}',end='\n\n')
timeseries_evaluation_metrics_func(validate,Predicted_inver_res[0])
```

![../images/492113_1_En_6_Chapter/492113_1_En_6_Figaz_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Figaz_HTML.jpg)

Plot the actual versus predicted values.

```
plt.plot( list(validate))
plt.plot( list(Predicted_inver_res[0]))
plt.title("Actual vs Predicted")
plt.ylabel("Traffic volume")
plt.legend(('Actual','predicted'))
plt.show()
```

Figure [6-14](#492113_1_En_6_Chapter.xhtml#Fig14) shows a line plot that depicts how the actual and predicted values change through time.

![../images/492113_1_En_6_Chapter/492113_1_En_6_Fig14_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Fig14_HTML.jpg)

Figure 6-14

Representation of actual versus predicted values

## Auto-encoder LSTM Univariate Single-Step Style in Action

In this section, let’s use single-step data preparation and auto-encoder LSTM to solve univariate time-series problems.

Import the required libraries and load the CSV files.

```
import pandas as pd
import numpy as np
import tensorflow as tf
from sklearn import preprocessing
import matplotlib.pyplot as plt
tf.random.set_seed(123)
np.random.seed(123)
```

Let’s take a sneak peek at the data by checking the five-point summary.

```
df = pd.read_csv(r'\Data\Metro_Interstate_Traffic_Volume.csv')
df.head()
```

![../images/492113_1_En_6_Chapter/492113_1_En_6_Figba_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Figba_HTML.jpg)

```
df.describe()
```

![../images/492113_1_En_6_Chapter/492113_1_En_6_Figbb_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Figbb_HTML.jpg)

Let’s drop duplicates, as the same hour contains different weather conditions.

```
df.drop_duplicates(subset=['date_time'], keep=False,inplace=True)
```

Train/test the split. Let’s hold back ten hours of data (i.e., ten records), which we can use to validate the data after training on the past data.

```
validate = df['traffic_volume'].tail(10)
df.drop(df['traffic_volume'].tail(10).index,inplace=True)
uni_data = df['traffic_volume']
uni_data.index = df['date_time']
uni_data.head()
```

![../images/492113_1_En_6_Chapter/492113_1_En_6_Figbc_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Figbc_HTML.jpg)

Let’s rescale the data as neural networks are known to converge sooner with better accuracy when features are on the same scale.

```
uni_data = uni_data.values
scaler_x = preprocessing.MinMaxScaler()
x_rescaled = scaler_x.fit_transform(uni_data.reshape(-1, 1))
```

Define a function to prepare univariate data suitable for a time series.

```
def custom_ts_univariate_data_prep(dataset, start, end, window, horizon):
X = []
y = []
start = start + window
if end is None:
end = len(dataset) - horizon
for i in range(start, end):
indicesx = range(i-window, i)
X.append(np.reshape(dataset[indicesx], (window, 1)))
indicesy = range(i,i+horizon)
y.append(dataset[indicesy])
return np.array(X), np.array(y)
```

As we are doing single-step forecasting, let’s allow the model to see/train on past 48 hours of data and try to forecast the 49^(th) hour; hence, use `horizon = 1`.

```
univar_hist_window = 48
horizon = 1
TRAIN_SPLIT = 30000
x_train_uni, y_train_uni = custom_ts_univariate_data_prep(x_rescaled, 0, TRAIN_SPLIT,univar_hist_window, horizon)
x_val_uni, y_val_uni = custom_ts_univariate_data_prep(x_rescaled, TRAIN_SPLIT, None,univar_hist_window,horizon)
```

Prepare the training and validation time-series data using the `tf.data` function, which is a much faster and more efficient way of feeding data to the model.

```
BATCH_SIZE = 256
BUFFER_SIZE = 150
train_univariate = tf.data.Dataset.from_tensor_slices((x_train_uni, y_train_uni))
train_univariate = train_univariate.cache().shuffle(BUFFER_SIZE).batch(BATCH_SIZE).repeat()
val_univariate = tf.data.Dataset.from_tensor_slices((x_val_uni, y_val_uni))
val_univariate = val_univariate.batch(BATCH_SIZE).repeat()
```

Define the encode-decoder model.

```
ED_lstm_model = tf.keras.models.Sequential([
tf.keras.layers.LSTM(100, input_shape=x_train_uni.shape[-2:], return_sequences=True),
tf.keras.layers.LSTM(units=50,return_sequences=True),
tf.keras.layers.LSTM(units=15),
tf.keras.layers.RepeatVector(y_train_uni.shape[1]),
tf.keras.layers.LSTM(units=100,return_sequences=True),
tf.keras.layers.LSTM(units=50,return_sequences=True),
tf.keras.layers.TimeDistributed(tf.keras.layers.Dense(units=1))
])
ED_lstm_model.compile(optimizer='adam', loss='mse')
```

The best weights are stored at `model_path`.

```
model_path = r'\Chapter 6\LSTM_encoder_decoder_Univarient_2.h5'
```

Configure the model and start training with early stopping and checkpointing.

Early stopping stops training when the monitored loss starts to increase above the patience.

Checkpointing saves the model weights as they reach the minimum loss.

```
EVALUATION_INTERVAL = 100
EPOCHS = 150
history = ED_lstm_model.fit(train_univariate, epochs=EPOCHS,steps_per_epoch=EVALUATION_INTERVAL,validation_data=val_univariate, validation_steps=50,verbose =1,
callbacks =[tf.keras.callbacks.EarlyStopping(monitor='val_loss', min_delta=0, patience=10, verbose=1, mode='min'),tf.keras.callbacks.ModelCheckpoint(model_path,monitor='val_loss', save_best_only=True, mode='min', verbose=0)])
```

![../images/492113_1_En_6_Chapter/492113_1_En_6_Figbd_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Figbd_HTML.jpg)

![../images/492113_1_En_6_Chapter/492113_1_En_6_Figbe_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Figbe_HTML.jpg)

The previous example shows that the model stopped within 64 epochs instead of running for 150\. Load the best weights into the model.

```
Trained_model = tf.keras.models.load_model(model_path)
```

Check the model summary.

```
Trained_model.summary()
```

![../images/492113_1_En_6_Chapter/492113_1_En_6_Figbf_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Figbf_HTML.jpg)

Plot the `loss` and `val_loss` against the epoch.

```
plt.plot(history.history['loss'])
plt.plot(history.history['val_loss'])
plt.title('Model loss')
plt.ylabel('loss')
plt.xlabel('epoch')
plt.legend(['train loss', 'validation loss'], loc='upper left')
plt.rcParams["figure.figsize"] = [16,9]
plt.show()
```

Figure [6-15](#492113_1_En_6_Chapter.xhtml#Fig15) depicts a line plot that helps us understand how a good model is able to generalize.

![../images/492113_1_En_6_Chapter/492113_1_En_6_Fig15_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Fig15_HTML.jpg)

Figure 6-15

Representation of training and validation loss as the number of epochs increases

We need to forecast the next ten steps, but our model predicts only one time step, so let’s take the last 48 hours of data from the training and increment one prediction at a time and forecast the next values.

```
uni = df['traffic_volume']
validatehori = uni.tail(48)
validatehist = validatehori.values
result = []
# Define Forecast length here
window_len = 10
val_rescaled = scaler_x.fit_transform(validatehist.reshape(-1, 1))
for i in range(1, window_len+1):
val_rescaled = val_rescaled.reshape((1, val_rescaled.shape[0], 1))
Predicted_results = Trained_model.predict(val_rescaled)
print(f'predicted : {Predicted_results}')
result.append(Predicted_results[0])
val_rescaled = np.append(val_rescaled[:,1:],[[Predicted_results]])
print(val_rescaled)
```

![../images/492113_1_En_6_Chapter/492113_1_En_6_Figbg_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Figbg_HTML.jpg)

Rescale the predicted values back to the original scale.

```
result_inv_trans = scaler_x.inverse_transform(np.array(result).reshape(-1,1))
```

Define the time-series evaluation function.

```
from sklearn import metrics
def timeseries_evaluation_metrics_func(y_true, y_pred):
def mean_absolute_percentage_error(y_true, y_pred):
y_true, y_pred = np.array(y_true), np.array(y_pred)
return np.mean(np.abs((y_true - y_pred) / y_true)) * 100
print('Evaluation metric results:-')
print(f'MSE is : {metrics.mean_squared_error(y_true, y_pred)}')
print(f'MAE is : {metrics.mean_absolute_error(y_true, y_pred)}')
print(f'RMSE is : {np.sqrt(metrics.mean_squared_error(y_true, y_pred))}')
print(f'MAPE is : {mean_absolute_percentage_error(y_true, y_pred)}')
print(f'R2 is : {metrics.r2_score(y_true, y_pred)}',end='\n\n')
timeseries_evaluation_metrics_func(validate,result_inv_trans)
```

![../images/492113_1_En_6_Chapter/492113_1_En_6_Figbh_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Figbh_HTML.jpg)

Plot the actual versus the predicted values.

```
plt.plot( list(validate))
plt.plot( list(result_inv_trans))
plt.title("Actual vs Predicted")
plt.ylabel("Traffic volume")
plt.legend(('Actual','predicted'))
plt.show()
```

Figure [6-16](#492113_1_En_6_Chapter.xhtml#Fig16) shows the line plot that depicts how actual and predicted values change through time.

![../images/492113_1_En_6_Chapter/492113_1_En_6_Fig16_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Fig16_HTML.jpg)

Figure 6-16

Representation of actual versus predicted values

## Auto-encoder LSTM Univariate Horizon Style in Action

In this section, let’s use horizon-style data preparation and auto-encoder LSTM to solve univariate time-series problems.

Import the required libraries and load the CSV files.

```
import pandas as pd
import numpy as np
import tensorflow as tf
from sklearn import preprocessing
import matplotlib.pyplot as plt
tf.random.set_seed(123)
np.random.seed(123)
```

Let’s take a sneak peek at the data by checking the five-point summary.

```
df = pd.read_csv(r'\Data\Metro_Interstate_Traffic_Volume.csv')
df.head()
```

![../images/492113_1_En_6_Chapter/492113_1_En_6_Figbi_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Figbi_HTML.jpg)

```
df.describe()
```

![../images/492113_1_En_6_Chapter/492113_1_En_6_Figbj_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Figbj_HTML.jpg)

Let’s drop duplicates, as the same hour contains different weather conditions.

```
df.drop_duplicates(subset=['date_time'], keep=False,inplace=True)
validate = df['traffic_volume'].tail(10)
df.drop(df['traffic_volume'].tail(10).index,inplace=True)
uni_data = df['traffic_volume']
uni_data.index = df['date_time']
uni_data.head()
```

![../images/492113_1_En_6_Chapter/492113_1_En_6_Figbk_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Figbk_HTML.jpg)

Let’s rescale the data as neural networks are known to converge sooner with better accuracy when features are on the same scale.

```
uni_data = uni_data.values
scaler_x = preprocessing.MinMaxScaler()
x_rescaled = scaler_x.fit_transform(uni_data.reshape(-1, 1))
```

Define a function to prepare univariate data suitable for a time series.

As we are doing horizon-style forecasting, let’s allow the model to see/train on past 48 hours of data and try to forecast next the ten hours of results. Hence, use `horizon = 10`.

```
def custom_ts_univariate_data_prep(dataset, start, end, window, horizon):
X = []
y = []
start = start + window
if end is None:
end = len(dataset) - horizon
for i in range(start, end):
indicesx = range(i-window, i)
X.append(np.reshape(dataset[indicesx], (window, 1)))
indicesy = range(i,i+horizon)
y.append(dataset[indicesy])
return np.array(X), np.array(y)
univar_hist_window = 48
horizon = 10
TRAIN_SPLIT = 30000
x_train_uni, y_train_uni = custom_ts_univariate_data_prep(x_rescaled, 0, TRAIN_SPLIT,univar_hist_window, horizon)
x_val_uni, y_val_uni = custom_ts_univariate_data_prep(x_rescaled, TRAIN_SPLIT, None,univar_hist_window,horizon)
```

Prepare the training and validation time-series data using the `tf.data` function, which is a much faster and more efficient way of feeding data to the model.

```
BATCH_SIZE = 256
BUFFER_SIZE = 150
train_univariate = tf.data.Dataset.from_tensor_slices((x_train_uni, y_train_uni))
train_univariate = train_univariate.cache().shuffle(BUFFER_SIZE).batch(BATCH_SIZE).repeat()
val_univariate = tf.data.Dataset.from_tensor_slices((x_val_uni, y_val_uni))
val_univariate = val_univariate.batch(BATCH_SIZE).repeat()
```

Define the auto-encoder model.

```
ED_lstm_model = tf.keras.models.Sequential([
tf.keras.layers.LSTM(100, input_shape=x_train_uni.shape[-2:], return_sequences=True),
tf.keras.layers.LSTM(units=50,return_sequences=True),
tf.keras.layers.LSTM(units=15),
tf.keras.layers.RepeatVector(y_train_uni.shape[1]),
tf.keras.layers.LSTM(units=100,return_sequences=True),
tf.keras.layers.LSTM(units=50,return_sequences=True),
tf.keras.layers.TimeDistributed(tf.keras.layers.Dense(units=1))
])
ED_lstm_model.compile(optimizer='adam', loss='mse')
```

The best weights are stored at `model_path`.

```
model_path = r'\Chapter 6\LSTM_encoder_decoder_Univarient_1.h5'
```

Configure the model and start training with early stopping and checkpointing.

Early stopping stops training when monitored loss starts to increase above the patience.

Checkpointing saves the model weights as they reach the minimum loss.

```
EVALUATION_INTERVAL = 100
EPOCHS = 150
history = ED_lstm_model.fit(train_univariate, epochs=EPOCHS,steps_per_epoch=EVALUATION_INTERVAL,validation_data=val_univariate, validation_steps=50,verbose =1,
callbacks =[tf.keras.callbacks.EarlyStopping(monitor='val_loss', min_delta=0, patience=10, verbose=1, mode='min'),tf.keras.callbacks.ModelCheckpoint(model_path,monitor='val_loss', save_best_only=True, mode='min', verbose=0)])
```

![../images/492113_1_En_6_Chapter/492113_1_En_6_Figbl_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Figbl_HTML.jpg)

![../images/492113_1_En_6_Chapter/492113_1_En_6_Figbm_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Figbm_HTML.jpg)

The previous example shows that the model stopped within 72 epochs instead of running for 150.

Load the best weights into the model.

```
Trained_model = tf.keras.models.load_model(model_path)
```

Check the model summary.

```
Trained_model.summary()
```

![../images/492113_1_En_6_Chapter/492113_1_En_6_Figbn_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Figbn_HTML.jpg)

Plot the `loss` and `val_loss` against the epoch.

```
plt.plot(history.history['loss'])
plt.plot(history.history['val_loss'])
plt.title('Model loss')
plt.ylabel('loss')
plt.xlabel('epoch')
plt.legend(['train loss', 'validation loss'], loc='upper left')
plt.rcParams["figure.figsize"] = [16,9]
plt.show()
```

Figure [6-17](#492113_1_En_6_Chapter.xhtml#Fig17) depicts a line plot that helps us understand how a good model is able to generalize.

![../images/492113_1_En_6_Chapter/492113_1_En_6_Fig17_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Fig17_HTML.jpg)

Figure 6-17

Representation of training and validation loss as the number of epochs increases

We need to forecast the next ten steps, so let’s take the last 48 hours of data from the training model and forecast the next ten hours of values.

```
uni = df['traffic_volume']
validatehori = uni.tail(48)
validatehist = validatehori.values
scaler_val = preprocessing.MinMaxScaler()
val_rescaled = scaler_x.fit_transform(validatehist.reshape(-1, 1))
val_rescaled = val_rescaled.reshape((1, val_rescaled.shape[0], 1))
Predicted_results = Trained_model.predict(val_rescaled)
Predicted_results
```

![../images/492113_1_En_6_Chapter/492113_1_En_6_Figbo_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Figbo_HTML.jpg)

Rescale the predicted values back to the original scale.

```
Predicted_inver_res = scaler_x.inverse_transform(Predicted_results[0])
Predicted_inver_res
```

![../images/492113_1_En_6_Chapter/492113_1_En_6_Figbp_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Figbp_HTML.jpg)

Define the time-series evaluation function.

```
from sklearn import metrics
def timeseries_evaluation_metrics_func(y_true, y_pred):
def mean_absolute_percentage_error(y_true, y_pred):
y_true, y_pred = np.array(y_true), np.array(y_pred)
return np.mean(np.abs((y_true - y_pred) / y_true)) * 100
print('Evaluation metric results:-')
print(f'MSE is : {metrics.mean_squared_error(y_true, y_pred)}')
print(f'MAE is : {metrics.mean_absolute_error(y_true, y_pred)}')
print(f'RMSE is : {np.sqrt(metrics.mean_squared_error(y_true, y_pred))}')
print(f'MAPE is : {mean_absolute_percentage_error(y_true, y_pred)}')
print(f'R2 is : {metrics.r2_score(y_true, y_pred)}',end='\n\n')
timeseries_evaluation_metrics_func(validate,Predicted_inver_res)
```

![../images/492113_1_En_6_Chapter/492113_1_En_6_Figbq_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Figbq_HTML.jpg)

Plot the actual versus the predicted values.

```
plt.plot( list(validate))
plt.plot( list(Predicted_inver_res))
plt.title("Actual vs Predicted")
plt.ylabel("Traffic volume")
plt.legend(('Actual','predicted'))
plt.show()
```

Figure [6-18](#492113_1_En_6_Chapter.xhtml#Fig18) shows a line plot that depicts how actual and predicted values change through time.

![../images/492113_1_En_6_Chapter/492113_1_En_6_Fig18_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Fig18_HTML.jpg)

Figure 6-18

Representation of actual versus predicted values

## CNN Univariate Single-Step Style in Action

In this section, let’s use single-step data preparation and CNNs to solve univariate time-series problems.

Import the required libraries and load the CSV files.

```
import pandas as pd
import numpy as np
import tensorflow as tf
from sklearn import preprocessing
import matplotlib.pyplot as plt
from tensorflow.keras.models import Sequential
from tensorflow.keras.layers import Dense
from tensorflow.keras.layers import Flatten
from tensorflow.keras.layers import Conv1D
from tensorflow.keras.layers import MaxPool1D
from tensorflow.keras.layers import Dropout
tf.random.set_seed(123)
np.random.seed(123)
```

Let’s take a sneak peek at the data by checking the five-point summary.

```
df = pd.read_csv(r'\Data\Metro_Interstate_Traffic_Volume.csv')
df.head()
```

![../images/492113_1_En_6_Chapter/492113_1_En_6_Figbr_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Figbr_HTML.jpg)

```
df.describe()
```

![../images/492113_1_En_6_Chapter/492113_1_En_6_Figbs_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Figbs_HTML.jpg)

Let’s drop duplicates, as the same hour contains different weather conditions.

```
df.drop_duplicates(subset=['date_time'], keep=False,inplace=True)
```

Train/test the split. Let’s hold back ten hours of data (i.e., ten records), which we can use to validate after training on the past data.

```
validate = df['traffic_volume'].tail(10)
df.drop(df['traffic_volume'].tail(10).index,inplace=True)
uni_data = df['traffic_volume']
uni_data.index = df['date_time']
uni_data.head()
```

![../images/492113_1_En_6_Chapter/492113_1_En_6_Figbt_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Figbt_HTML.jpg)

Let’s rescale the data as neural networks are known to converge sooner with better accuracy when features are on the same scale.

```
uni_data = uni_data.values
scaler_x = preprocessing.MinMaxScaler()
x_rescaled = scaler_x.fit_transform(uni_data.reshape(-1, 1))
```

Define a function to prepare univariate data suitable for a time series.

```
def custom_ts_univariate_data_prep(dataset, start, end, window, horizon):
X = []
y = []
start = start + window
if end is None:
end = len(dataset) - horizon
for i in range(start, end):
indicesx = range(i-window, i)
X.append(np.reshape(dataset[indicesx], (window, 1)))
indicesy = range(i,i+horizon)
y.append(dataset[indicesy])
return np.array(X), np.array(y)
```

As we are doing single-step forecasting, let’s allow the model to see/train on past 48 hours of data and try to forecast the 49^(th) hour. Hence, use `horizon = 1`.

```
univar_hist_window = 48
horizon = 1
TRAIN_SPLIT = 30000
x_train_uni, y_train_uni = custom_ts_univariate_data_prep(x_rescaled, 0, TRAIN_SPLIT,univar_hist_window, horizon)
x_val_uni, y_val_uni = custom_ts_univariate_data_prep(x_rescaled, TRAIN_SPLIT, None,univar_hist_window,horizon)
```

Prepare the training and validation time-series data using the `tf.data` function, which is a much faster and more efficient way of feeding data to the model.

```
BATCH_SIZE = 256
BUFFER_SIZE = 150
train_univariate = tf.data.Dataset.from_tensor_slices((x_train_uni, y_train_uni))
train_univariate = train_univariate.cache().shuffle(BUFFER_SIZE).batch(BATCH_SIZE).repeat()
val_univariate = tf.data.Dataset.from_tensor_slices((x_val_uni, y_val_uni))
val_univariate = val_univariate.batch(BATCH_SIZE).repeat()
```

Define the CNN model.

```
model = Sequential()
model.add(Conv1D(filters=64, kernel_size=3, activation='relu', input_shape=(x_train_uni.shape[1], x_train_uni.shape[2])))
model.add(MaxPool1D(pool_size=2))
model.add(Dropout(0.2))
model.add(Flatten())
model.add(Dense(30, activation='relu'))
model.add(Dropout(0.2))
model.add(Dense(1))
model.compile(optimizer='adam', loss='mse')
```

The best weights stored at `model_path`.

```
model_path = r'\Chapter 6\CNN_Univarient_2.h5'
```

Configure the model and start training with early stopping and checkpointing.

Early stopping stops training when the monitored loss starts to increase above the patience.

Checkpointing saves the model weights as they reach the minimum loss.

```
EVALUATION_INTERVAL = 100
EPOCHS = 150
history = model.fit(train_univariate, epochs=EPOCHS,steps_per_epoch=EVALUATION_INTERVAL,validation_data=val_univariate, validation_steps=50,verbose =1,callbacks =[tf.keras.callbacks.
EarlyStopping(monitor='val_loss', min_delta=0, patience=10, verbose=1, mode='min'),tf.keras.callbacks.ModelCheckpoint(model_path,monitor='val_loss', save_best_only=True, mode='min', verbose=0)])
```

![../images/492113_1_En_6_Chapter/492113_1_En_6_Figbu_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Figbu_HTML.jpg)

![../images/492113_1_En_6_Chapter/492113_1_En_6_Figbv_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Figbv_HTML.jpg)

The previous example shows that the model stopped within 45 epochs instead of running for 150\. Load the best weights into the model.

```
Trained_model = tf.keras.models.load_model(model_path)
```

Check the model summary.

```
Trained_model.summary()
```

![../images/492113_1_En_6_Chapter/492113_1_En_6_Figbw_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Figbw_HTML.jpg)

Plot the `loss` and `val_loss` against the epoch.

```
plt.plot(history.history['loss'])
plt.plot(history.history['val_loss'])
plt.title('Model loss')
plt.ylabel('loss')
plt.xlabel('epoch')
plt.legend(['train loss', 'validation loss'], loc='upper left')
plt.rcParams["figure.figsize"] = [16,9]
plt.show()
```

Figure [6-19](#492113_1_En_6_Chapter.xhtml#Fig19) depicts a line plot that helps us understand how a good model is able to generalize.

![../images/492113_1_En_6_Chapter/492113_1_En_6_Fig19_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Fig19_HTML.jpg)

Figure 6-19

Representation of training and validation loss as the number of epoch increases

We need to forecast the next ten steps, but our model predicts only one time step, so let’s take the last 48 hours of data from the training and increment one prediction at a time and forecast the next values.

```
uni = df['traffic_volume']
validatehori = uni.tail(48)
validatehist = validatehori.values
result = []
# Define Forecast length here
window_len = 10
val_rescaled = scaler_x.fit_transform(validatehist.reshape(-1, 1))
for i in range(1, window_len+1):
val_rescaled = val_rescaled.reshape((1, val_rescaled.shape[0], 1))
Predicted_results = Trained_model.predict(val_rescaled)
print(f'predicted : {Predicted_results}')
result.append(Predicted_results[0])
val_rescaled = np.append(val_rescaled[:,1:],[[Predicted_results]])
print(val_rescaled)
```

![../images/492113_1_En_6_Chapter/492113_1_En_6_Figbx_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Figbx_HTML.jpg)

Rescale the predicted values back to the original scale.

```
result_inv_trans = scaler_x.inverse_transform(result)
```

Define the time-series evaluation function.

```
from sklearn import metrics
def timeseries_evaluation_metrics_func(y_true, y_pred):
def mean_absolute_percentage_error(y_true, y_pred):
y_true, y_pred = np.array(y_true), np.array(y_pred)
return np.mean(np.abs((y_true - y_pred) / y_true)) * 100
print('Evaluation metric results:-')
print(f'MSE is : {metrics.mean_squared_error(y_true, y_pred)}')
print(f'MAE is : {metrics.mean_absolute_error(y_true, y_pred)}')
print(f'RMSE is : {np.sqrt(metrics.mean_squared_error(y_true, y_pred))}')
print(f'MAPE is : {mean_absolute_percentage_error(y_true, y_pred)}')
print(f'R2 is : {metrics.r2_score(y_true, y_pred)}',end='\n\n')
timeseries_evaluation_metrics_func(validate,result_inv_trans)
```

![../images/492113_1_En_6_Chapter/492113_1_En_6_Figby_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Figby_HTML.jpg)

Plot the actual versus predicted values.

```
plt.plot( list(validate))
plt.plot( list(result_inv_trans))
plt.title("Actual vs Predicted")
plt.ylabel("Traffic volume")
plt.legend(('Actual','predicted'))
plt.show()
```

Figure [6-20](#492113_1_En_6_Chapter.xhtml#Fig20) shows a line plot that depicts how actual and predicted values change through time.

![../images/492113_1_En_6_Chapter/492113_1_En_6_Fig20_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Fig20_HTML.jpg)

Figure 6-20

Representation of actual versus predicted values

## CNN Univariate Horizon Style in Action

In this section, let’s use horizon-style data preparation and CNNs to solve univariate time-series problems.

Import the required libraries and load the CSV files.

```
import pandas as pd
import numpy as np
import tensorflow as tf
from sklearn import preprocessing
import matplotlib.pyplot as plt
from tensorflow.keras.models import Sequential
from tensorflow.keras.layers import Dense
from tensorflow.keras.layers import Flatten
from tensorflow.keras.layers import Conv1D
from tensorflow.keras.layers import MaxPool1D
from tensorflow.keras.layers import Dropout
tf.random.set_seed(123)
np.random.seed(123)
```

Let’s load and take a sneak peek at the data by checking the five-point summary.

```
df = pd.read_csv(r'\Data\Metro_Interstate_Traffic_Volume.csv')
df.head()
```

![../images/492113_1_En_6_Chapter/492113_1_En_6_Figbz_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Figbz_HTML.jpg)

```
df.describe()
```

![../images/492113_1_En_6_Chapter/492113_1_En_6_Figca_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Figca_HTML.jpg)

Let’s drop duplicates, as the same hour contains different weather conditions.

```
df.drop_duplicates(subset=['date_time'], keep=False,inplace=True)
```

Train/test the split. Let’s hold back ten hours of data (i.e., ten records), which we can use to validate the data after training on the past data.

```
validate = df['traffic_volume'].tail(10)
df.drop(df['traffic_volume'].tail(10).index,inplace=True)
```

Let’s rescale the data as neural networks are known to converge sooner with better accuracy when features are on the same scale.

```
uni_data = df['traffic_volume']
uni_data.index = df['date_time']
uni_data.head()
```

![../images/492113_1_En_6_Chapter/492113_1_En_6_Figcb_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Figcb_HTML.jpg)

```
uni_data = uni_data.values
scaler_x = preprocessing.MinMaxScaler()
x_rescaled = scaler_x.fit_transform(uni_data.reshape(-1, 1))
```

Define a function to prepare univariate data suitable for a time series.

As we are doing horizon-style forecasts, let’s allow the model to see/train on the past 48 hours of data and try to forecast the next ten hours of results. Hence, use `horizon = 10`.

```
def custom_ts_univariate_data_prep(dataset, start, end, window, horizon):
X = []
y = []
start = start + window
if end is None:
end = len(dataset) - horizon
for i in range(start, end):
indicesx = range(i-window, i)
X.append(np.reshape(dataset[indicesx], (window, 1)))
indicesy = range(i,i+horizon)
y.append(dataset[indicesy])
return np.array(X), np.array(y)
univar_hist_window = 48
horizon = 10
TRAIN_SPLIT = 30000
x_train_uni, y_train_uni = custom_ts_univariate_data_prep(x_rescaled, 0, TRAIN_SPLIT,univar_hist_window, horizon)
x_val_uni, y_val_uni = custom_ts_univariate_data_prep(x_rescaled, TRAIN_SPLIT, None,univar_hist_window,horizon)
```

Prepare the training and validation time-series data using the `tf.data` function, which is a much faster and more efficient way of feeding data to the model.

```
BATCH_SIZE = 256
BUFFER_SIZE = 150
train_univariate = tf.data.Dataset.from_tensor_slices((x_train_uni, y_train_uni))
train_univariate = train_univariate.cache().shuffle(BUFFER_SIZE).batch(BATCH_SIZE).repeat()
val_univariate = tf.data.Dataset.from_tensor_slices((x_val_uni, y_val_uni))
val_univariate = val_univariate.batch(BATCH_SIZE).repeat()
```

Define the CNN model.

```
model = Sequential()
model.add(Conv1D(filters=64, kernel_size=3, activation='relu', input_shape=(x_train_uni.shape[1], x_train_uni.shape[2])))
model.add(MaxPool1D(pool_size=2))
model.add(Dropout(0.2))
model.add(Flatten())
model.add(Dense(30, activation='relu'))
model.add(Dropout(0.2))
model.add(Dense(horizon))
model.compile(optimizer='adam', loss='mse')
```

The best weights are stored at `model_path`.

```
model_path = r'\Chapter 6\CNN_Univarient_1.h5'
```

Configure the model and start training with early stopping and checkpointing.

Early stopping stops training when the monitored loss starts to increase above the patience.

Checkpointing saves the model weights as they reach the minimum loss.

```
EVALUATION_INTERVAL = 100
EPOCHS = 150
history = model.fit(train_univariate, epochs=EPOCHS,steps_per_epoch=EVALUATION_INTERVAL,validation_data=val_univariate, validation_steps=50,verbose =1,callbacks =[tf.keras.callbacks.
EarlyStopping(monitor='val_loss', min_delta=0, patience=10, verbose=1, mode='min'),tf.keras.callbacks.ModelCheckpoint(model_path,monitor='val_loss', save_best_only=True, mode='min', verbose=0)])
```

![../images/492113_1_En_6_Chapter/492113_1_En_6_Figcc_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Figcc_HTML.jpg)

![../images/492113_1_En_6_Chapter/492113_1_En_6_Figcd_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Figcd_HTML.jpg)

The previous example shows the model stopped within 66 epochs instead of running for 150.

Load the best weights into the model.

```
Trained_model = tf.keras.models.load_model(model_path)
```

Check the model summary.

```
Trained_model.summary()
```

![../images/492113_1_En_6_Chapter/492113_1_En_6_Figce_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Figce_HTML.jpg)

Plot the `loss` and `val_loss` against the epoch.

```
plt.plot(history.history['loss'])
plt.plot(history.history['val_loss'])
plt.title('Model loss')
plt.ylabel('loss')
plt.xlabel('epoch')
plt.legend(['train loss', 'validation loss'], loc='upper left')
plt.rcParams["figure.figsize"] = [16,9]
plt.show()
```

Figure [6-21](#492113_1_En_6_Chapter.xhtml#Fig21) depicts a line plot that helps us understand how a good model is able to generalize.

![../images/492113_1_En_6_Chapter/492113_1_En_6_Fig21_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Fig21_HTML.jpg)

Figure 6-21

Representation of training and validation loss as the number of epochs increases

We need to forecast the next ten steps, so let’s take the last 48 hours of data from the training model and forecast the next ten hours of values.

```
uni = df['traffic_volume']
validatehori = uni.tail(48)
validatehist = validatehori.values
scaler_val = preprocessing.MinMaxScaler()
val_rescaled = scaler_x.fit_transform(validatehist.reshape(-1, 1))
val_rescaled = val_rescaled.reshape((1, val_rescaled.shape[0], 1))
Predicted_results = Trained_model.predict(val_rescaled)
Predicted_results
```

![../images/492113_1_En_6_Chapter/492113_1_En_6_Figcf_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Figcf_HTML.jpg)

Rescale the predicted values back to the original scale.

```
Predicted_inver_res = scaler_x.inverse_transform(Predicted_results)
```

Define the time-series evaluation function.

```
from sklearn import metrics
def timeseries_evaluation_metrics_func(y_true, y_pred):
def mean_absolute_percentage_error(y_true, y_pred):
y_true, y_pred = np.array(y_true), np.array(y_pred)
return np.mean(np.abs((y_true - y_pred) / y_true)) * 100
print('Evaluation metric results:-')
print(f'MSE is : {metrics.mean_squared_error(y_true, y_pred)}')
print(f'MAE is : {metrics.mean_absolute_error(y_true, y_pred)}')
print(f'RMSE is : {np.sqrt(metrics.mean_squared_error(y_true, y_pred))}')
print(f'MAPE is : {mean_absolute_percentage_error(y_true, y_pred)}')
print(f'R2 is : {metrics.r2_score(y_true, y_pred)}',end='\n\n')
timeseries_evaluation_metrics_func(validate,Predicted_inver_res[0])
```

![../images/492113_1_En_6_Chapter/492113_1_En_6_Figcg_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Figcg_HTML.jpg)

Plot the actual versus predicted values.

```
plt.plot( list(validate))
plt.plot( list(Predicted_inver_res[0]))
plt.title("Actual vs Predicted")
plt.ylabel("Traffic volume")
plt.legend(('Actual','predicted'))
plt.show()
```

Figure [6-22](#492113_1_En_6_Chapter.xhtml#Fig22) shows a line plot that depicts how actual and predicted values change through time.

![../images/492113_1_En_6_Chapter/492113_1_En_6_Fig22_HTML.jpg](images/492113_1_En_6_Chapter/492113_1_En_6_Fig22_HTML.jpg)

Figure 6-22

Representation of actual versus predicted values

## Summary

In this chapter, you learned how to use single-step and horizon-style data preparation for a time series and solve univariate time-series problems using LSTM, bidirectional LSTM, GRU, auto-encoders, and CNNs. In the next chapter, you will learn how to solve multivariate time-series problems using bleeding-edge techniques.

# 7. Bleeding-Edge Techniques for Multivariate Time Series

In the previous chapter, you learned how to perform univariate time-series forecasting. In this chapter, you will look at single-step and horizon-style time-series forecasting data preparation and solve some multivariate time-series problems. A multivariate time series is a method where more than one variable is time dependent, and we use these variables to try to estimate a target variable.

## LSTM Multivariate Horizon Style in Action

In this section, let’s use horizon-style data preparation and LSTM to solve multivariate time-series problems.

Import the required libraries and load the CSV data.

```
import pandas as pd
import numpy as np
import tensorflow as tf
from sklearn import preprocessing
import matplotlib.pyplot as plt
tf.random.set_seed(123)
np.random.seed(123)
```

Let’s load and take a sneak peek at data by checking the five-point summary.

```
df = pd.read_csv(r'\Data\Metro_Interstate_Traffic_Volume.csv')
df.head()
```

![../images/492113_1_En_7_Chapter/492113_1_En_7_Figa_HTML.jpg](images/492113_1_En_7_Chapter/492113_1_En_7_Figa_HTML.jpg)

```
df.describe()
```

![../images/492113_1_En_7_Chapter/492113_1_En_7_Figb_HTML.jpg](images/492113_1_En_7_Chapter/492113_1_En_7_Figb_HTML.jpg)

Let’s drop duplicates, as the same hour has different weather conditions.

```
df.drop_duplicates(subset=['date_time'], keep=False,inplace=True)
```

Apply label encoding on the categorical features.

```
holiday_le = preprocessing.LabelEncoder()
df['holiday_le'] = holiday_le.fit_transform(df['holiday'])
weather_main_le = preprocessing.LabelEncoder()
df['weather_main_le'] = weather_main_le.fit_transform(df['weather_main'])
weather_description_le = preprocessing.LabelEncoder()
df['weather_description_le'] = weather_description_le.fit_transform(df['weather_description'])
```

XGBoost is an optimized distributed gradient boosting library designed to be highly efficient, flexible, and portable. Let’s use it to check feature importance. Figure [7-1](#492113_1_En_7_Chapter.xhtml#Fig1) shows the bar plot representation.

![../images/492113_1_En_7_Chapter/492113_1_En_7_Fig1_HTML.jpg](images/492113_1_En_7_Chapter/492113_1_En_7_Fig1_HTML.jpg)

Figure 7-1

Representing feature importance using a bar plot

```
from numpy import loadtxt
from xgboost import XGBRegressor
from matplotlib import pyplot
model = XGBRegressor()
model.fit(df[['temp', 'rain_1h', 'snow_1h', 'clouds_all', 'holiday_le','weather_main_le', 'weather_description_le']],
df[['traffic_volume']])
(pd.Series(model.feature_importances_, index=df[['temp', 'rain_1h', 'snow_1h', 'clouds_all', 'holiday_le',
'weather_main_le', 'weather_description_le']].columns)
.nlargest(7)
.plot(kind='barh'))
```

Let’s take all features into model training. Define a function to prepare multivariate data so that it is suitable for a time series.

```
def custom_ts_multi_data_prep(dataset, target, start, end, window, horizon):
X = []
y = []
start = start + window
if end is None:
end = len(dataset) - horizon
for i in range(start, end):
indices = range(i-window, i)
X.append(dataset[indices])
indicey = range(i+1, i+1+horizon)
y.append(target[indicey])
return np.array(X), np.array(y)
```

Train/test the split, and let’s hold back ten hours of data (i.e., ten records), which we can use to validate the results after training on past data.

```
validate = df[['rain_1h','temp', 'snow_1h', 'clouds_all', 'holiday_le','weather_main_le', 'weather_description_le',
'traffic_volume']].tail(10)
df.drop(df.tail(10).index,inplace=True)
```

Let’s rescale the data as neural networks are known to converge sooner with better accuracy when features are on the same scale.

```
x_scaler = preprocessing.MinMaxScaler()
y_scaler = preprocessing.MinMaxScaler()
dataX = x_scaler.fit_transform(df[['rain_1h','temp', 'snow_1h', 'clouds_all', 'holiday_le','weather_main_le', ‘weather_description_le','traffic_volume']])
dataY = y_scaler.fit_transform(df[['traffic_volume']])
```

As we are doing horizon-style forecasting, let’s allow the model to see/train on the past 48 hours of data and try to forecast the next ten hours of results. Hence, use `horizon = 10`.

```
hist_window = 48
horizon = 10
TRAIN_SPLIT = 30000
x_train_multi, y_train_multi = custom_ts_multi_data_prep(
dataX, dataY, 0, TRAIN_SPLIT, hist_window, horizon)
x_val_multi, y_val_multi= custom_ts_multi_data_prep(
dataX, dataY, TRAIN_SPLIT, None, hist_window, horizon)
print ('Single window of past history')
print(x_train_multi[0])
print ('\n Target horizon')
print (y_train_multi[0])
Single window of past history
[[0\.         0.92972555 0\.         0.4        0.63636364 0.1
0.64864865 0.76167582]
[0\.         0.93320863 0\.         0.75       0.63636364 0.1
0.05405405 0.62032967]
[0\.         0.93391815 0\.         0.9        0.63636364 0.1
0.51351351 0.65480769]
[0\.         0.93569194 0\.         0.9        0.63636364 0.1
0.51351351 0.69038462]
[0\.         0.93894927 0\.         0.75       0.63636364 0.1
0.05405405 0.67554945]
[0\.         0.94081981 0\.         0.01       0.63636364 0.
0.72972973 0.71167582]
[0\.         0.94549618 0\.         0.01       0.63636364 0.
[0\.         0.90847228 0\.         0.01       0.63636364 0.
0.72972973 0.1146978 ]
[0\.         0.907279   0\.         0.01       0.63636364 0.
0.72972973 0.37445055]
...
[0\.         0.90540846 0\.         0.01       0.63636364 0.
0.72972973 0.78145604]
[0\.         0.90486019 0\.         0.01       0.63636364 0.
0.72972973 0.96016484]
[0\.         0.90902054 0\.         0.01       0.63636364 0.
0.72972973 0.82211538]
[0\.         0.91908279 0\.         0.01       0.63636364 0.
0.72972973 0.72925824]
[0\.         0.93262812 0\.         0.01       0.63636364 0.
0.72972973 0.63228022]
[0\.         0.94027155 0\.         0.01       0.63636364 0.
0.72972973 0.67087912]
[0\.         0.95130132 0\.         0.01       0.63636364 0.
0.72972973 0.7010989 ]]
Target horizon
[[0.75563187]
[0.78475275]
[0.86428571]
[0.83200549]
[0.67403846]
[0.48118132]
[0.41717033]
[0.38763736]
[0.27362637]
[0.16016484]]
```

Prepare the training and validation time-series data using the `tf.data` function, which is a much faster and more efficient way of feeding data to the model.

```
BATCH_SIZE = 256
BUFFER_SIZE = 150
train_data_multi = tf.data.Dataset.from_tensor_slices((x_train_multi, y_train_multi))
train_data_multi = train_data_multi.cache().shuffle(BUFFER_SIZE).batch(BATCH_SIZE).repeat()
val_data_multi = tf.data.Dataset.from_tensor_slices((x_val_multi, y_val_multi))
val_data_multi = val_data_multi.batch(BATCH_SIZE).repeat()
```

Define the LSTM model.

```
lstm_multi = tf.keras.models.Sequential()
lstm_multi.add(tf.keras.layers.LSTM(150,input_shape=x_train_multi.shape[-2:],return_sequences=True))
lstm_multi.add(tf.keras.layers.Dropout(0.2)),
lstm_multi.add(tf.keras.layers.LSTM(units=100,return_sequences=False)),
lstm_multi.add(tf.keras.layers.Dropout(0.2)),
lstm_multi.add(tf.keras.layers.Dense(units=horizon)),
lstm_multi.compile(optimizer='adam', loss='mse')
```

The best weights are stored at `model_path`.

```
model_path = r'\Chapter 7\LSTM_Multivariate.h5'
```

Configure the model and start training with early stopping and checkpointing.

Early stopping stops training when the monitored loss starts to increase above the desired limit.

Checkpointing saves the model weights as they reach minimum loss.

```
EVALUATION_INTERVAL = 150
EPOCHS = 100
history = lstm_multi.fit(train_data_multi, epochs=EPOCHS,steps_per_epoch=EVALUATION_INTERVAL,validation_data=val_data_multi, validation_steps=50,verbose =1,callbacks =[tf.keras.callbacks.
EarlyStopping(monitor='val_loss', min_delta=0, patience=10, verbose=1, mode='min'),tf.keras.callbacks.ModelCheckpoint(model_path,monitor='val_loss', save_best_only=True, mode='min', verbose=0)])
```

![../images/492113_1_En_7_Chapter/492113_1_En_7_Figc_HTML.jpg](images/492113_1_En_7_Chapter/492113_1_En_7_Figc_HTML.jpg)

![../images/492113_1_En_7_Chapter/492113_1_En_7_Figd_HTML.jpg](images/492113_1_En_7_Chapter/492113_1_En_7_Figd_HTML.jpg)

The previous example shows that the model stopped within 49 epochs instead of running for 150.

Load the best weights into the model.

```
Trained_model = tf.keras.models.load_model(model_path)
```

Plot the `loss` and `val_loss` against the epoch.

```
plt.plot(history.history['loss'])
plt.plot(history.history['val_loss'])
plt.title('Model loss')
plt.ylabel('loss')
plt.xlabel('epoch')
plt.legend(['train loss', 'validation loss'], loc='upper left')
plt.rcParams["figure.figsize"] = [16,9]
plt.show()
```

Figure [7-2](#492113_1_En_7_Chapter.xhtml#Fig2) depicts a line plot that helps us understand how a good model is able to generalize.

![../images/492113_1_En_7_Chapter/492113_1_En_7_Fig2_HTML.jpg](images/492113_1_En_7_Chapter/492113_1_En_7_Fig2_HTML.jpg)

Figure 7-2

Representation of training and validation loss as the number of epochs increases

Check the model summary.

```
Trained_model.summary()
```

![../images/492113_1_En_7_Chapter/492113_1_En_7_Fige_HTML.jpg](images/492113_1_En_7_Chapter/492113_1_En_7_Fige_HTML.jpg)

We need to forecast the next ten steps, so let’s take the last 48 hours of data from the training model and forecast the next ten hours of values.

```
data_val = x_scaler.fit_transform(df[['rain_1h','temp', 'snow_1h', 'clouds_all', 'holiday_le', 'weather_main_le',
'weather_description_le','traffic_volume']].tail(48))
val_rescaled = data_val.reshape(1, data_val.shape[0], data_val.shape[1])
Predicted_results = Trained_model.predict(val_rescaled)
Predicted_results
```

![../images/492113_1_En_7_Chapter/492113_1_En_7_Figf_HTML.jpg](images/492113_1_En_7_Chapter/492113_1_En_7_Figf_HTML.jpg)

Rescale the predicted values back to the original scale.

```
Predicted_results_Inv_trans = y_scaler.inverse_transform(Predicted_results)
Predicted_results_Inv_trans
```

![../images/492113_1_En_7_Chapter/492113_1_En_7_Figg_HTML.jpg](images/492113_1_En_7_Chapter/492113_1_En_7_Figg_HTML.jpg)

Define the time-series evaluation function.

```
from sklearn import metrics
def timeseries_evaluation_metrics_func(y_true, y_pred):
def mean_absolute_percentage_error(y_true, y_pred):
y_true, y_pred = np.array(y_true), np.array(y_pred)
return np.mean(np.abs((y_true - y_pred) / y_true)) * 100
print('Evaluation metric results:-')
print(f'MSE is : {metrics.mean_squared_error(y_true, y_pred)}')
print(f'MAE is : {metrics.mean_absolute_error(y_true, y_pred)}')
print(f'RMSE is : {np.sqrt(metrics.mean_squared_error(y_true, y_pred))}')
print(f'MAPE is : {mean_absolute_percentage_error(y_true, y_pred)}')
print(f'R2 is : {metrics.r2_score(y_true, y_pred)}',end='\n\n')
timeseries_evaluation_metrics_func(validate['traffic_volume'],Predicted_results_Inv_trans[0])
```

![../images/492113_1_En_7_Chapter/492113_1_En_7_Figh_HTML.jpg](images/492113_1_En_7_Chapter/492113_1_En_7_Figh_HTML.jpg)

Plot the actual versus predicted values.

```
plt.plot( list(validate['traffic_volume']))
plt.plot( list(Predicted_results_Inv_trans[0]))
plt.title("Actual vs Predicted")
plt.ylabel("Traffic volume")
plt.legend(('Actual','predicted'))
plt.show()
```

Figure [7-3](#492113_1_En_7_Chapter.xhtml#Fig3) shows a line plot that depicts how the actual and predicted values change through time.

![../images/492113_1_En_7_Chapter/492113_1_En_7_Fig3_HTML.jpg](images/492113_1_En_7_Chapter/492113_1_En_7_Fig3_HTML.jpg)

Figure 7-3

Representation of actual versus predicted values

## Bidirectional LSTM Multivariate Horizon Style in Action

In this section, let’s use horizon-style data preparation and bidirectional LSTM to solve multivariate time-series problems.

Import the required libraries and load the CSV data.

```
import pandas as pd
import numpy as np
import tensorflow as tf
from sklearn import preprocessing
import matplotlib.pyplot as plt
tf.random.set_seed(123)
np.random.seed(123)
```

Let’s load and take a sneak peek at the data by checking the five-point summary.

```
df = pd.read_csv(r'\Data\Metro_Interstate_Traffic_Volume.csv')
df.head()
```

![../images/492113_1_En_7_Chapter/492113_1_En_7_Figi_HTML.jpg](images/492113_1_En_7_Chapter/492113_1_En_7_Figi_HTML.jpg)

```
df.describe()
```

![../images/492113_1_En_7_Chapter/492113_1_En_7_Figj_HTML.jpg](images/492113_1_En_7_Chapter/492113_1_En_7_Figj_HTML.jpg)

Let’s drop duplicates, as the same hour has different weather conditions.

```
df.drop_duplicates(subset=['date_time'], keep=False,inplace=True)
```

Apply label encoding on the categorical features, and convert to numeric.

```
holiday_le = preprocessing.LabelEncoder()
df['holiday_le'] = holiday_le.fit_transform(df['holiday'])
weather_main_le = preprocessing.LabelEncoder()
df['weather_main_le'] = weather_main_le.fit_transform(df['weather_main'])
weather_description_le = preprocessing.LabelEncoder()
df['weather_description_le'] = weather_description_le.fit_transform(df['weather_description'])
```

Train/test the split, and let’s hold back ten hours of data (i.e., ten records), which we can use to validate the results after training on past data.

```
validate = df[['rain_1h','temp', 'snow_1h', 'clouds_all', 'holiday_le','weather_main_le', 'weather_description_le',
'traffic_volume']].tail(10)
df.drop(df.tail(10).index,inplace=True)
```

Define a function to prepare multivariate data suitable for a time series.

```
def custom_ts_multi_data_prep(dataset, target, start, end, window, horizon):
X = []
y = []
start = start + window
if end is None:
end = len(dataset) - horizon
for i in range(start, end):
indices = range(i-window, i)
X.append(dataset[indices])
indicey = range(i+1, i+1+horizon)
y.append(target[indicey])
return np.array(X), np.array(y)
validate = df[['rain_1h','temp', 'snow_1h', 'clouds_all', 'holiday_le','weather_main_le', 'weather_description_le',
'traffic_volume']].tail(10)
df.drop(df.tail(10).index,inplace=True)
```

Let’s rescale the data as neural networks are known to converge sooner with better accuracy when features are on the same scale.

```
x_scaler = preprocessing.MinMaxScaler()
y_scaler = preprocessing.MinMaxScaler()
dataX = x_scaler.fit_transform(df[['rain_1h','temp', 'snow_1h', 'clouds_all', 'holiday_le','weather_main_le', 'weather_
description_le','traffic_volume']])
dataY = y_scaler.fit_transform(df[['traffic_volume']])
```

As we are doing horizon-style forecasting, let’s allow the model to see/train on the past 48 hours of data and try to forecast the next ten hours of results. Hence, use `horizon = 10`.

```
hist_window = 48
horizon = 10
TRAIN_SPLIT = 30000
x_train_multi, y_train_multi = custom_ts_multi_data_prep(
dataX, dataY, 0, TRAIN_SPLIT, hist_window, horizon)
x_val_multi, y_val_multi = custom_ts_multi_data_prep(
dataX, dataY, TRAIN_SPLIT, None, hist_window, horizon)
```

Prepare the training and validation time-series data using the `tf.data` function, which is a much faster and more efficient way of feeding data to the model.

```
BATCH_SIZE = 256
BUFFER_SIZE = 150
train_data_multi = tf.data.Dataset.from_tensor_slices((x_train_multi, y_train_multi))
train_data_multi = train_data_multi.cache().shuffle(BUFFER_SIZE).batch(BATCH_SIZE).repeat()
val_data_multi = tf.data.Dataset.from_tensor_slices((x_val_multi, y_val_multi))
val_data_multi = val_data_multi.batch(BATCH_SIZE).repeat()
```

Define the bidirectional LSTM model.

```
Bi_lstm_model = tf.keras.models.Sequential([
tf.keras.layers.Bidirectional(tf.keras.layers.LSTM(150, return_sequences=True), input_shape=x_train_multi.shape[-2:]),
tf.keras.layers.Bidirectional(tf.keras.layers.LSTM(50)),
tf.keras.layers.Dense(20, activation='tanh'),
tf.keras.layers.Dropout(0.2),
tf.keras.layers.Dense(units=horizon),
])
Bi_lstm_model.compile(optimizer='adam', loss='mse')
```

The best weights are stored at `model_path`.

```
model_path = r'\Chapter 7\Bidirectional_LSTM_Multivariate.h5'
```

Configure the model and start training with early stopping and checkpointing.

Early stopping stops training when the monitored loss starts to increase above the desired limit.

Checkpointing saves the model weights as they reach minimum loss.

```
EVALUATION_INTERVAL = 100
EPOCHS = 150
history = Bi_lstm_model.fit(train_data_multi, epochs=EPOCHS, steps_per_epoch=EVALUATION_INTERVAL, validation_data=val_data_multi, validation_steps=50, verbose=1,callbacks=[tf.keras.
callbacks.EarlyStopping(monitor='val_loss', min_delta=0, patience=10, verbose=1, mode='min'), tf.keras.callbacks.ModelCheckpoint(model_path, monitor='val_loss', save_best_only=True, mode='min', verbose=0)])
```

![../images/492113_1_En_7_Chapter/492113_1_En_7_Figk_HTML.jpg](images/492113_1_En_7_Chapter/492113_1_En_7_Figk_HTML.jpg)

![../images/492113_1_En_7_Chapter/492113_1_En_7_Figl_HTML.jpg](images/492113_1_En_7_Chapter/492113_1_En_7_Figl_HTML.jpg)

The previous example shows that the model stopped within 45 epochs instead of running for 150.

Load the best weights into the model.

```
Trained_model = tf.keras.models.load_model(model_path)
```

Check the model summary.

```
Trained_model.summary()
```

![../images/492113_1_En_7_Chapter/492113_1_En_7_Figm_HTML.jpg](images/492113_1_En_7_Chapter/492113_1_En_7_Figm_HTML.jpg)

Plot the `loss` and `val_loss` against the epoch.

```
plt.plot(history.history['loss'])
plt.plot(history.history['val_loss'])
plt.title('Model loss')
plt.ylabel('loss')
plt.xlabel('epoch')
plt.legend(['train loss', 'validation loss'], loc='upper left')
plt.rcParams["figure.figsize"] = [16,9]
plt.show()
```

Figure [7-4](#492113_1_En_7_Chapter.xhtml#Fig4) depicts a line plot that helps us understand how a good model is able to generalize.

![../images/492113_1_En_7_Chapter/492113_1_En_7_Fig4_HTML.jpg](images/492113_1_En_7_Chapter/492113_1_En_7_Fig4_HTML.jpg)

Figure 7-4

Representation of training and validation loss as the number of epochs increases

We need to forecast the next ten steps, so let’s take the last 48 hours of data from the training model and forecast the next ten hours of values.

```
data_val = x_scaler.fit_transform(df[['rain_1h','temp', 'snow_1h', 'clouds_all', 'holiday_le',
'weather_main_le', 'weather_description_le','traffic_volume']].tail(48))
val_rescaled = data_val.reshape(1, data_val.shape[0], data_val.shape[1])
Predicted_results = Trained_model.predict(val_rescaled)
Predicted_results
```

![../images/492113_1_En_7_Chapter/492113_1_En_7_Fign_HTML.jpg](images/492113_1_En_7_Chapter/492113_1_En_7_Fign_HTML.jpg)

Rescale predicted values back to the original scale.

```
Predicted_results_Inv_trans = y_scaler.inverse_transform(Predicted_results)
Predicted_results_Inv_trans
```

![../images/492113_1_En_7_Chapter/492113_1_En_7_Figo_HTML.jpg](images/492113_1_En_7_Chapter/492113_1_En_7_Figo_HTML.jpg)

Define the time-series evaluation function.

```
from sklearn import metrics
def timeseries_evaluation_metrics_func(y_true, y_pred):
def mean_absolute_percentage_error(y_true, y_pred):
y_true, y_pred = np.array(y_true), np.array(y_pred)
return np.mean(np.abs((y_true - y_pred) / y_true)) * 100
print('Evaluation metric results:-')
print(f'MSE is : {metrics.mean_squared_error(y_true, y_pred)}')
print(f'MAE is : {metrics.mean_absolute_error(y_true, y_pred)}')
print(f'RMSE is : {np.sqrt(metrics.mean_squared_error(y_true, y_pred))}')
print(f'MAPE is : {mean_absolute_percentage_error(y_true, y_pred)}')
print(f'R2 is : {metrics.r2_score(y_true, y_pred)}',end='\n\n')
timeseries_evaluation_metrics_func(validate['traffic_volume'],Predicted_results_Inv_trans[0])
```

![../images/492113_1_En_7_Chapter/492113_1_En_7_Figp_HTML.jpg](images/492113_1_En_7_Chapter/492113_1_En_7_Figp_HTML.jpg)

Plot the actual versus predicted values.

```
plt.plot( list(validate['traffic_volume']))
plt.plot( list(Predicted_results_Inv_trans[0]))
plt.title("Actual vs Predicted")
plt.ylabel("Traffic volume")
plt.legend(('Actual','predicted'))
plt.show()
```

Figure [7-5](#492113_1_En_7_Chapter.xhtml#Fig5) shows a line plot that depicts how actual and predicted values change through time.

![../images/492113_1_En_7_Chapter/492113_1_En_7_Fig5_HTML.jpg](images/492113_1_En_7_Chapter/492113_1_En_7_Fig5_HTML.jpg)

Figure 7-5

Representation of actual versus predicted values

## Auto-encoder LSTM Multivariate Horizon Style in Action

In this section, let’s use horizon-style data preparation and auto-encoder LSTM to solve multivariate time-series problems.

Import the required libraries and load the CSV data.

```
import pandas as pd
import numpy as np
import tensorflow as tf
from sklearn import preprocessing
import matplotlib.pyplot as plt
tf.random.set_seed(123)
np.random.seed(123)
```

Let’s load and take a sneak peek at the data by checking the five-point summary.

```
df = pd.read_csv(r'Data\Metro_Interstate_Traffic_Volume.csv')
df.head()
```

![../images/492113_1_En_7_Chapter/492113_1_En_7_Figq_HTML.jpg](images/492113_1_En_7_Chapter/492113_1_En_7_Figq_HTML.jpg)

```
df.describe()
```

![../images/492113_1_En_7_Chapter/492113_1_En_7_Figr_HTML.jpg](images/492113_1_En_7_Chapter/492113_1_En_7_Figr_HTML.jpg)

Let’s drop duplicates, as the same hour has different weather conditions.

```
df.drop_duplicates(subset=['date_time'], keep=False,inplace=True)
```

Apply label encoding on categorical features, and convert to numeric.

```
holiday_le = preprocessing.LabelEncoder()
df['holiday_le'] = holiday_le.fit_transform(df['holiday'])
weather_main_le = preprocessing.LabelEncoder()
df['weather_main_le'] = weather_main_le.fit_transform(df['weather_main'])
weather_description_le = preprocessing.LabelEncoder()
df['weather_description_le'] = weather_description_le.fit_transform(df['weather_description'])
```

Define a function to prepare multivariate data suitable for a time series.

```
def custom_ts_multi_data_prep(dataset, target, start, end, window, horizon):
X = []
y = []
start = start + window
if end is None:
end = len(dataset) - horizon
for i in range(start, end):
indices = range(i-window, i)
X.append(dataset[indices])
indicey = range(i+1, i+1+horizon)
y.append(target[indicey])
return np.array(X), np.array(y)
```

Train/test the split, and let’s hold back ten hours of data (i.e., ten records), which we can use to validate the results after training on past data.

```
validate = df[['rain_1h','temp', 'snow_1h', 'clouds_all', 'holiday_le','weather_main_le', 'weather_description_le',
'traffic_volume']].tail(10)
df.drop(df.tail(10).index,inplace=True)
```

Let’s rescale the data as neural networks are known to converge sooner with better accuracy when features are on the same scale.

```
x_scaler = preprocessing.MinMaxScaler()
y_scaler = preprocessing.MinMaxScaler()
dataX = x_scaler.fit_transform(df[['rain_1h','temp', 'snow_1h', 'clouds_all', 'holiday_le','weather_main_le', 'weather_
description_le','traffic_volume']])
dataY = y_scaler.fit_transform(df[['traffic_volume']])
```

As we are doing horizon-style forecasting, let’s allow the model to see/train on the past 48 hours of data and try to forecast the next ten hours of results. Hence, use `horizon = 10`.

```
hist_window = 48
horizon = 10
TRAIN_SPLIT = 30000
x_train_multi, y_train_multi = custom_ts_multi_data_prep(
dataX, dataY, 0, TRAIN_SPLIT, hist_window, horizon)
x_val_multi, y_val_multi= custom_ts_multi_data_prep(
dataX, dataY, TRAIN_SPLIT, None, hist_window, horizon)
```

Prepare the training and validation time-series data using the `tf.data` function, which is a much faster and more efficient way of feeding data to the model.

```
BATCH_SIZE = 256
BUFFER_SIZE = 150
train_data_multi = tf.data.Dataset.from_tensor_slices((x_train_multi, y_train_multi))
train_data_multi = train_data_multi.cache().shuffle(BUFFER_SIZE).batch(BATCH_SIZE).repeat()
val_data_multi = tf.data.Dataset.from_tensor_slices((x_val_multi, y_val_multi))
val_data_multi = val_data_multi.batch(BATCH_SIZE).repeat()
```

Define the auto-encoder LSTM model.

```
ED_lstm_model = tf.keras.models.Sequential([
tf.keras.layers.LSTM(40, input_shape=x_train_multi.shape[-2:], return_sequences=True),
tf.keras.layers.LSTM(units=20,return_sequences=True),
tf.keras.layers.LSTM(units=15),
tf.keras.layers.RepeatVector(y_train_multi.shape[1]),
tf.keras.layers.LSTM(units=40,return_sequences=True),
tf.keras.layers.LSTM(units=25,return_sequences=True),
tf.keras.layers.TimeDistributed(tf.keras.layers.Dense(units=1))
])
ED_lstm_model.compile(optimizer='adam', loss='mse')
```

The best weights are stored at `model_path`.

```
model_path = r'\Chapter 7\Encoder_Decoder_LSTM_Multivariate.h5'
```

Configure the model and start training with early stopping and checkpointing.

Early stopping stops training when the monitored loss starts to increase above the desired limit.

Checkpointing saves the model weights as they reach minimum loss.

```
EVALUATION_INTERVAL = 100
EPOCHS = 150
history = ED_lstm_model.fit(train_data_multi, epochs=EPOCHS,steps_per_epoch=EVALUATION_INTERVAL,validation_data=val_data_multi, validation_steps=50,verbose =1,
callbacks =[tf.keras.callbacks.EarlyStopping(monitor='val_loss', min_delta=0, patience=10, verbose=1, mode='min'),tf.keras.callbacks.ModelCheckpoint(model_path,monitor='val_loss', save_best_only=True, mode='min', verbose=0)])
```

![../images/492113_1_En_7_Chapter/492113_1_En_7_Figs_HTML.jpg](images/492113_1_En_7_Chapter/492113_1_En_7_Figs_HTML.jpg)

![../images/492113_1_En_7_Chapter/492113_1_En_7_Figt_HTML.jpg](images/492113_1_En_7_Chapter/492113_1_En_7_Figt_HTML.jpg)

The previous example shows that the model stopped within 79 epochs instead of running for 150.

Load the best weights into the model.

```
Trained_model = tf.keras.models.load_model(model_path)
```

Check the model summary.

```
Trained_model.summary()
```

![../images/492113_1_En_7_Chapter/492113_1_En_7_Figu_HTML.jpg](images/492113_1_En_7_Chapter/492113_1_En_7_Figu_HTML.jpg)

Plot the `loss` and `val_loss` against the epoch.

```
plt.plot(history.history['loss'])
plt.plot(history.history['val_loss'])
plt.title('Model loss')
plt.ylabel('loss')
plt.xlabel('epoch')
plt.legend(['train loss', 'validation loss'], loc='upper left')
plt.rcParams["figure.figsize"] = [16,9]
plt.show()
```

Figure [7-6](#492113_1_En_7_Chapter.xhtml#Fig6) depicts a line plot that helps us understand how a good model is able to generalize.

![../images/492113_1_En_7_Chapter/492113_1_En_7_Fig6_HTML.jpg](images/492113_1_En_7_Chapter/492113_1_En_7_Fig6_HTML.jpg)

Figure 7-6

Representation of training and validation loss as the number of epochs increases

We need to forecast the next ten steps, so let’s take the last 48 hours of data from the training model and forecast the next ten hours of values.

```
data_val = x_scaler.fit_transform(df[['rain_1h','temp', 'snow_1h', 'clouds_all', 'holiday_le','weather_main_le',
'weather_description_le','traffic_volume']].tail(48))
val_rescaled = data_val.reshape(1, data_val.shape[0], data_val.shape[1])
Predicted_results = Trained_model.predict(val_rescaled)
Predicted_results
```

![../images/492113_1_En_7_Chapter/492113_1_En_7_Figv_HTML.jpg](images/492113_1_En_7_Chapter/492113_1_En_7_Figv_HTML.jpg)

Rescale the predicted values back to the original scale.

```
Predicted_results_Inv_trans = y_scaler.inverse_transform(Predicted_results.reshape(-1,1))
Predicted_results_Inv_trans
```

![../images/492113_1_En_7_Chapter/492113_1_En_7_Figw_HTML.jpg](images/492113_1_En_7_Chapter/492113_1_En_7_Figw_HTML.jpg)

Define the time-series evaluation function.

```
from sklearn import metrics
def timeseries_evaluation_metrics_func(y_true, y_pred):
def mean_absolute_percentage_error(y_true, y_pred):
y_true, y_pred = np.array(y_true), np.array(y_pred)
return np.mean(np.abs((y_true - y_pred) / y_true)) * 100
print('Evaluation metric results:-')
print(f'MSE is : {metrics.mean_squared_error(y_true, y_pred)}')
print(f'MAE is : {metrics.mean_absolute_error(y_true, y_pred)}')
print(f'RMSE is : {np.sqrt(metrics.mean_squared_error(y_true, y_pred))}')
print(f'MAPE is : {mean_absolute_percentage_error(y_true, y_pred)}')
print(f'R2 is : {metrics.r2_score(y_true, y_pred)}',end='\n\n')
timeseries_evaluation_metrics_func(validate['traffic_volume'],Predicted_results_Inv_trans)
```

![../images/492113_1_En_7_Chapter/492113_1_En_7_Figx_HTML.jpg](images/492113_1_En_7_Chapter/492113_1_En_7_Figx_HTML.jpg)

Plot the actual versus predicted values.

```
plt.plot( list(validate['traffic_volume']))
plt.plot( list(Predicted_results_Inv_trans))
plt.title("Actual vs Predicted")
plt.ylabel("Traffic volume")
plt.legend(('Actual','predicted'))
plt.show()
```

Figure [7-7](#492113_1_En_7_Chapter.xhtml#Fig7) shows a line plot that depicts how actual and predicted values change through time.

![../images/492113_1_En_7_Chapter/492113_1_En_7_Fig7_HTML.jpg](images/492113_1_En_7_Chapter/492113_1_En_7_Fig7_HTML.jpg)

Figure 7-7

Representation of actual versus predicted values

## GRU Multivariate Horizon Style in Action

In this section, let’s use horizon-style data preparation and GRU to solve multivariate time-series problems.

Import the required libraries and load the CSV data.

```
import pandas as pd
import numpy as np
import tensorflow as tf
from sklearn import preprocessing
import matplotlib.pyplot as plt
tf.random.set_seed(123)
np.random.seed(123)
```

Let’s load and take a sneak peek at the data by checking the five-point summary.

```
df = pd.read_csv(r'\Data\Metro_Interstate_Traffic_Volume.csv')
df.head()
```

![../images/492113_1_En_7_Chapter/492113_1_En_7_Figy_HTML.jpg](images/492113_1_En_7_Chapter/492113_1_En_7_Figy_HTML.jpg)

```
df.describe()
```

![../images/492113_1_En_7_Chapter/492113_1_En_7_Figz_HTML.jpg](images/492113_1_En_7_Chapter/492113_1_En_7_Figz_HTML.jpg)

Let’s drop duplicates, as the same hour has different weather conditions.

```
df.drop_duplicates(subset=['date_time'], keep=False,inplace=True)
```

Apply label encoding on categorical features, and convert to numeric.

```
holiday_le = preprocessing.LabelEncoder()
df['holiday_le'] = holiday_le.fit_transform(df['holiday'])
weather_main_le = preprocessing.LabelEncoder()
df['weather_main_le'] = weather_main_le.fit_transform(df['weather_main'])
weather_description_le = preprocessing.LabelEncoder()
df['weather_description_le'] = weather_description_le.fit_transform(df['weather_description'])
```

Define a function to prepare multivariate data suitable for a time series.

```
def custom_ts_multi_data_prep(dataset, target, start, end, window, horizon):
X = []
y = []
start = start + window
if end is None:
end = len(dataset) - horizon
for i in range(start, end):
indices = range(i-window, i)
X.append(dataset[indices])
indicey = range(i+1, i+1+horizon)
y.append(target[indicey])
return np.array(X), np.array(y)
```

Train/test the split, and let’s hold back ten hours of data (i.e., ten records), which we can use to validate the results after training on past data.

```
validate = df[['rain_1h','temp', 'snow_1h', 'clouds_all', 'holiday_le','weather_main_le', 'weather_description_le',
'traffic_volume']].tail(10)
df.drop(df.tail(10).index,inplace=True)
```

Let’s rescale the data as neural networks are known to converge sooner with better accuracy when features are on the same scale.

```
x_scaler = preprocessing.MinMaxScaler()
y_scaler = preprocessing.MinMaxScaler()
dataX = x_scaler.fit_transform(df[['rain_1h','temp', 'snow_1h', 'clouds_all', 'holiday_le','weather_main_le', 'weather_
description_le','traffic_volume']])
dataY = y_scaler.fit_transform(df[['traffic_volume']])
```

As we are doing horizon-style forecasting, let’s allow the model to see/train on the past 48 hours of data and try to forecast the next ten hours of results. Hence, use `horizon = 10`.

```
hist_window = 48
horizon = 10
TRAIN_SPLIT = 30000
x_train_multi, y_train_multi = custom_ts_multi_data_prep(
dataX, dataY, 0, TRAIN_SPLIT, hist_window, horizon)
x_val_multi, y_val_multi= custom_ts_multi_data_prep(
dataX, dataY, TRAIN_SPLIT, None, hist_window, horizon)
```

Prepare the training and validation time-series data using the `tf.data` function, which is a much faster and more efficient way of feeding data to the model.

```
BATCH_SIZE = 256
BUFFER_SIZE = 150
train_data_multi = tf.data.Dataset.from_tensor_slices((x_train_multi, y_train_multi))
train_data_multi = train_data_multi.cache().shuffle(BUFFER_SIZE).batch(BATCH_SIZE).repeat()
val_data_multi = tf.data.Dataset.from_tensor_slices((x_val_multi, y_val_multi))
val_data_multi = val_data_multi.batch(BATCH_SIZE).repeat()
```

Define the GRU model.

```
GRU_model = tf.keras.models.Sequential([
tf.keras.layers.GRU(100, input_shape=x_train_multi.shape[-2:],return_sequences=True),
tf.keras.layers.Dropout(0.2),
tf.keras.layers.GRU(units=50,return_sequences=False),
tf.keras.layers.Dropout(0.2),
tf.keras.layers.Dense(units=horizon),
])
GRU_model.compile(optimizer='adam', loss='mse')
```

The best weights are stored at `model_path`.

```
model_path = r'\Chapter 7\GRU_Multivariate.h5'
```

Configure the model and start training with early stopping and checkpointing.

Early stopping stops training when the monitored loss starts to increase above the desired limit.

Checkpointing saves the model weights as they reach the minimum loss.

```
EVALUATION_INTERVAL = 100
EPOCHS = 150
history = GRU_model.fit(train_data_multi, epochs=EPOCHS,steps_per_epoch=EVALUATION_INTERVAL,validation_data=val_data_multi, validation_steps=50,verbose =1,callbacks =[tf.keras.callbacks.
EarlyStopping(monitor='val_loss', min_delta=0, patience=10, verbose=1, mode='min'),tf.keras.callbacks.ModelCheckpoint(model_path,monitor='val_loss', save_best_only=True, mode='min', verbose=0)])
```

![../images/492113_1_En_7_Chapter/492113_1_En_7_Figaa_HTML.jpg](images/492113_1_En_7_Chapter/492113_1_En_7_Figaa_HTML.jpg)

![../images/492113_1_En_7_Chapter/492113_1_En_7_Figab_HTML.jpg](images/492113_1_En_7_Chapter/492113_1_En_7_Figab_HTML.jpg)

The previous example shows that the model stopped within 52 epochs instead of running for 150.

Load the best weights into the model.

```
Trained_model = tf.keras.models.load_model(model_path)
```

Check the model summary.

```
Trained_model.summary()
```

![../images/492113_1_En_7_Chapter/492113_1_En_7_Figac_HTML.jpg](images/492113_1_En_7_Chapter/492113_1_En_7_Figac_HTML.jpg)

Plot the `loss` and `val_loss` against the epoch.

```
plt.plot(history.history['loss'])
plt.plot(history.history['val_loss'])
plt.title('Model loss')
plt.ylabel('loss')
plt.xlabel('epoch')
plt.legend(['train loss', 'validation loss'], loc='upper left')
plt.rcParams["figure.figsize"] = [16,9]
plt.show()
```

Figure [7-8](#492113_1_En_7_Chapter.xhtml#Fig8) depicts a line plot that helps us understand how a good model is able to generalize.

![../images/492113_1_En_7_Chapter/492113_1_En_7_Fig8_HTML.jpg](images/492113_1_En_7_Chapter/492113_1_En_7_Fig8_HTML.jpg)

Figure 7-8

Representation of training and validation loss as the number of epochs increases

We need to forecast the next ten steps, so let’s take the last 48 hours of data from the training model and forecast the next ten hours of values.

```
data_val = x_scaler.fit_transform(df[['rain_1h','temp', 'snow_1h', 'clouds_all', 'holiday_le',
'weather_main_le', 'weather_description_le','traffic_volume']].tail(48))
val_rescaled = data_val.reshape(1, data_val.shape[0], data_val.shape[1])
Predicted_results = Trained_model.predict(val_rescaled)
Predicted_results
```

![../images/492113_1_En_7_Chapter/492113_1_En_7_Figad_HTML.jpg](images/492113_1_En_7_Chapter/492113_1_En_7_Figad_HTML.jpg)

Rescale the predicted values back to the original scale.

```
Predicted_results_Inv_trans = y_scaler.inverse_transform(Predicted_results)
Predicted_results_Inv_trans
```

![../images/492113_1_En_7_Chapter/492113_1_En_7_Figae_HTML.jpg](images/492113_1_En_7_Chapter/492113_1_En_7_Figae_HTML.jpg)

Define the time-series evaluation function.

```
from sklearn import metrics
def timeseries_evaluation_metrics_func(y_true, y_pred):
def mean_absolute_percentage_error(y_true, y_pred):
y_true, y_pred = np.array(y_true), np.array(y_pred)
return np.mean(np.abs((y_true - y_pred) / y_true)) * 100
print('Evaluation metric results:-')
print(f'MSE is : {metrics.mean_squared_error(y_true, y_pred)}')
print(f'MAE is : {metrics.mean_absolute_error(y_true, y_pred)}')
print(f'RMSE is : {np.sqrt(metrics.mean_squared_error(y_true, y_pred))}')
print(f'MAPE is : {mean_absolute_percentage_error(y_true, y_pred)}')
print(f'R2 is : {metrics.r2_score(y_true, y_pred)}',end='\n\n')
timeseries_evaluation_metrics_func(validate['traffic_volume'],Predicted_results_Inv_trans[0])
```

![../images/492113_1_En_7_Chapter/492113_1_En_7_Figaf_HTML.jpg](images/492113_1_En_7_Chapter/492113_1_En_7_Figaf_HTML.jpg)

Plot the actual versus predicted values.

```
plt.plot( list(validate['traffic_volume']))
plt.plot( list(Predicted_results_Inv_trans[0]))
plt.title("Actual vs Predicted")
plt.ylabel("Traffic volume")
plt.legend(('Actual','predicted'))
plt.show()
```

Figure [7-9](#492113_1_En_7_Chapter.xhtml#Fig9) shows a line plot that depicts how actual and predicted values change through time.

![../images/492113_1_En_7_Chapter/492113_1_En_7_Fig9_HTML.jpg](images/492113_1_En_7_Chapter/492113_1_En_7_Fig9_HTML.jpg)

Figure 7-9

Representation of actual versus predicted values

## CNN Multivariate Horizon Style in Action

In this section, let’s use horizon-style data preparation and CNNs to solve multivariate time-series problems.

Import the required libraries and load the CSV data.

```
import tensorflow as tf
import matplotlib.pyplot as plt
import numpy as np
import pandas as pd
from sklearn import preprocessing
from tensorflow.keras.models import Sequential
from tensorflow.keras.layers import Dense
from tensorflow.keras.layers import Flatten,TimeDistributed,RepeatVector
from tensorflow.keras.layers import Conv1D
from tensorflow.keras.layers import MaxPool1D
from tensorflow.keras.layers import Dropout
tf.random.set_seed(123)
np.random.seed(123)
```

Let’s load and take a sneak peek at data by checking the five-point summary.

```
df = pd.read_csv(r'\Data\Metro_Interstate_Traffic_Volume.csv')
df.head()
```

![../images/492113_1_En_7_Chapter/492113_1_En_7_Figag_HTML.jpg](images/492113_1_En_7_Chapter/492113_1_En_7_Figag_HTML.jpg)

```
df.describe()
```

![../images/492113_1_En_7_Chapter/492113_1_En_7_Figah_HTML.jpg](images/492113_1_En_7_Chapter/492113_1_En_7_Figah_HTML.jpg)

Let’s drop duplicates, as the same hour has different weather conditions.

```
df.drop_duplicates(subset=['date_time'], keep=False,inplace=True)
```

Apply label encoding on categorical features, and convert to numeric.

```
holiday_le = preprocessing.LabelEncoder()
df['holiday_le'] = holiday_le.fit_transform(df['holiday'])
weather_main_le = preprocessing.LabelEncoder()
df['weather_main_le'] = weather_main_le.fit_transform(df['weather_main'])
weather_description_le = preprocessing.LabelEncoder()
df['weather_description_le'] = weather_description_le.fit_transform(df['weather_description'])
```

Define a function to prepare multivariate data suitable for a time series.

```
def custom_ts_multi_data_prep(dataset, target, start, end, window, horizon):
X = []
y = []
start = start + window
if end is None:
end = len(dataset) - horizon
for i in range(start, end):
indices = range(i-window, i)
X.append(dataset[indices])
indicey = range(i+1, i+1+horizon)
y.append(target[indicey])
return np.array(X), np.array(y)
```

Train/test the split, and let’s hold back ten hours of data (i.e., ten records), which we can use to validate the results after training on past data.

```
validate = df[['rain_1h','temp', 'snow_1h', 'clouds_all', 'holiday_le','weather_main_le', 'weather_description_le',
'traffic_volume']].tail(10)
df.drop(df.tail(10).index,inplace=True)
```

Let’s rescale the data as neural networks are known to converge sooner with better accuracy when features are on the same scale.

```
x_scaler = preprocessing.MinMaxScaler()
y_scaler = preprocessing.MinMaxScaler()
dataX = x_scaler.fit_transform(df[['rain_1h','temp', 'snow_1h', 'clouds_all', 'holiday_le',
'weather_main_le', 'weather_description_le','traffic_volume']])
dataY = y_scaler.fit_transform(df[['traffic_volume']])
```

As we are doing horizon-style forecasting, let’s allow the model to see/train on the past 48 hours of data and try to forecast the next ten hours of results. Hence, use `horizon = 10`.

```
hist_window = 48
horizon = 10
TRAIN_SPLIT = 30000
x_train_multi, y_train_multi = custom_ts_multi_data_prep(
dataX, dataY, 0, TRAIN_SPLIT, hist_window, horizon)
x_val_multi, y_val_multi= custom_ts_multi_data_prep(
dataX, dataY, TRAIN_SPLIT, None, hist_window, horizon)
```

Prepare the training and validation time-series data using the `tf.data` function, which is a much faster and more efficient way of feeding data to the model.

```
BATCH_SIZE = 256
BUFFER_SIZE = 150
train_data_multi = tf.data.Dataset.from_tensor_slices((x_train_multi, y_train_multi))
train_data_multi = train_data_multi.cache().shuffle(BUFFER_SIZE).batch(BATCH_SIZE).repeat()
val_data_multi = tf.data.Dataset.from_tensor_slices((x_val_multi, y_val_multi))
val_data_multi = val_data_multi.batch(BATCH_SIZE).repeat()
```

Define the CNN model.

```
model = Sequential()
model.add(Conv1D(filters=64, kernel_size=3, activation='relu', input_shape=(x_train_multi.shape[1], x_train_multi.shape[2])))
model.add(MaxPool1D(pool_size=2))
model.add(Dropout(0.2))
model.add(Flatten())
model.add(Dense(30, activation='relu'))
model.add(Dropout(0.2))
model.add(Dense(horizon))
model.compile(optimizer='adam', loss='mse')
```

The best weights are stored at `model_path`.

```
model_path = r'\Chapter 7\CNN_Multivariate.h5'
```

Configure the model and start training with early stopping and checkpointing.

Early stopping stops training when the monitored loss starts to increase above the desired limit.

Checkpointing saves the model weights as they reach minimum loss.

```
EVALUATION_INTERVAL = 100
EPOCHS = 150
history = model.fit(train_data_multi, epochs=EPOCHS, steps_per_epoch=EVALUATION_INTERVAL, validation_data=val_data_multi, validation_steps=50, verbose=1,callbacks=[tf.keras.callbacks.EarlyStopping(monitor='val_loss', min_delta=0, patience=10, verbose=1, mode='min'), tf.keras.callbacks.ModelCheckpoint(model_path, monitor='val_loss', save_best_only=True, mode='min', verbose=0)])
```

![../images/492113_1_En_7_Chapter/492113_1_En_7_Figai_HTML.jpg](images/492113_1_En_7_Chapter/492113_1_En_7_Figai_HTML.jpg)

![../images/492113_1_En_7_Chapter/492113_1_En_7_Figaj_HTML.jpg](images/492113_1_En_7_Chapter/492113_1_En_7_Figaj_HTML.jpg)

The previous example shows that the model stopped within 59 epochs instead of running for 150.

Load the best weights into the model.

```
Trained_model = tf.keras.models.load_model(model_path)
```

Check the model summary.

```
Trained_model.summary()
```

![../images/492113_1_En_7_Chapter/492113_1_En_7_Figak_HTML.jpg](images/492113_1_En_7_Chapter/492113_1_En_7_Figak_HTML.jpg)

Plot the `loss` and `val_loss` against the epoch.

```
plt.plot(history.history['loss'])
plt.plot(history.history['val_loss'])
plt.title('Model loss')
plt.ylabel('loss')
plt.xlabel('epoch')
plt.legend(['train loss', 'validation loss'], loc='upper left')
plt.rcParams["figure.figsize"] = [16,9]
plt.show()
```

Figure [7-10](#492113_1_En_7_Chapter.xhtml#Fig10) depicts a line plot that helps us understand how a good model is able to generalize.

![../images/492113_1_En_7_Chapter/492113_1_En_7_Fig10_HTML.jpg](images/492113_1_En_7_Chapter/492113_1_En_7_Fig10_HTML.jpg)

Figure 7-10

Representation of training and validation loss as the number of epochs increases

We need to forecast the next ten steps, so let’s take the last 48 hours of data from the training model and forecast the next ten hours of values.

```
data_val = x_scaler.fit_transform(df[['rain_1h','temp', 'snow_1h', 'clouds_all', 'holiday_le',
'weather_main_le', 'weather_description_le','traffic_volume']].tail(48))
val_rescaled = data_val.reshape(1, data_val.shape[0], data_val.shape[1])
Predicted_results = Trained_model.predict(val_rescaled)
Predicted_results
```

![../images/492113_1_En_7_Chapter/492113_1_En_7_Figal_HTML.jpg](images/492113_1_En_7_Chapter/492113_1_En_7_Figal_HTML.jpg)

Rescale the predicted values back to the original scale.

```
Predicted_results_Inv_trans = y_scaler.inverse_transform(Predicted_results)
```

Define the time-series evaluation function.

```
from sklearn import metrics
def timeseries_evaluation_metrics_func(y_true, y_pred):
def mean_absolute_percentage_error(y_true, y_pred):
y_true, y_pred = np.array(y_true), np.array(y_pred)
return np.mean(np.abs((y_true - y_pred) / y_true)) * 100
print('Evaluation metric results:-')
print(f'MSE is : {metrics.mean_squared_error(y_true, y_pred)}')
print(f'MAE is : {metrics.mean_absolute_error(y_true, y_pred)}')
print(f'RMSE is : {np.sqrt(metrics.mean_squared_error(y_true, y_pred))}')
print(f'MAPE is : {mean_absolute_percentage_error(y_true, y_pred)}')
print(f'R2 is : {metrics.r2_score(y_true, y_pred)}',end='\n\n')
timeseries_evaluation_metrics_func(validate['traffic_volume'],Predicted_results_Inv_trans[0])
```

![../images/492113_1_En_7_Chapter/492113_1_En_7_Figam_HTML.jpg](images/492113_1_En_7_Chapter/492113_1_En_7_Figam_HTML.jpg)

Plot the actual versus predicted values.

```
plt.plot( list(validate['traffic_volume']))
plt.plot( list(Predicted_results_Inv_trans[0]))
plt.title("Actual vs Predicted")
plt.ylabel("Traffic volume")
plt.legend(('Actual','predicted'))
plt.show()
```

Figure [7-11](#492113_1_En_7_Chapter.xhtml#Fig11) shows a line plot that depicts how the actual and predicted values change through time.

![../images/492113_1_En_7_Chapter/492113_1_En_7_Fig11_HTML.jpg](images/492113_1_En_7_Chapter/492113_1_En_7_Fig11_HTML.jpg)

Figure 7-11

Representation of actual versus predicted values

We evaluated the same data using different styles of data preparation and model architectures and observed the power of neural networks. To improve accuracy, try hyper-parameter tuning using Hyperopt, Optune, or Keras Tuner, which can increase accuracy significantly.

## Summary

In this chapter, you learned how to use single-step and horizon-style data preparation for a time series and how to solve multivariate time-series problems using LSTM, bidirectional LSTM, GRU, auto-encoders, and CNNs. In the next chapter, you will learn how to solve univariate and multivariate problems using Prophet.

# 8. Prophet

Prophet is an open source framework from Facebook used for framing and forecasting time series. It focuses on an additive model where nonlinear trends fit with daily, weekly, and yearly seasonality and additional holiday effects. Prophet is powerful at handling missing data and shifts within the trends and generally handles outliers well. It also allows you to accumulate exogenous variables to the model.

## The Prophet Model

Prophet uses a decomposable time-series model.

**y(*****t*****) = g(*****t*****) + s(*****t*****) + h(*****t*****) + ϵ**[**t**]

where

**g(t)** = Trend (linear/logistic)

**s(t)** = Periodic change/seasonality

![$$ s(t)=\sum \limits_{n=1}^N\left({a}_n\cos \left(\frac{2\pi nt}{P}\right)+{b}_n\sin \left(\frac{2\pi nt}{P}\right)\right) $$](images/492113_1_En_8_Chapter/492113_1_En_8_Chapter_TeX_IEq1.png)

Here, *P* is the consistent period we anticipate the time series to have (e.g., P = 365.25 for yearly data or P = 7 for weekly data, when we are scaling our time variable in days).

Fitting seasonality requires calculating the 2*N* parameters for β = [a1, b1,…., a*N*, b*N*]^T by developing a matrix of seasonality vectors for each and every value of *t* in our historical data and future data.

**h(t)** = Effect of holiday

For each holiday *i*, let D *i* be the set of past and future dates for that holiday, as shown here:

Z(*t*) = [1(*t* ϵ D[1]),…,1(*t* ϵ D[L])]

and taking the following:

h(*t*) = Z(*t*)κ

As with seasonality, we use a prior κ∼Normal(0,ν²).

**ϵt** = Changes that are not adopted by the model

Prophet’s core procedure is implemented using Stan (a probabilistic programming language). Stan performs map optimization to find parameters and facilitates estimating parameter uncertainty using the [Hamiltonian Monte Carlo](https://en.wikipedia.org/wiki/Hybrid_Monte_Carlo) algorithm.

In next section, you will learn how to use Prophet and solve time-series problems.

## Implementing Prophet

In the previous section, you learned about the high-level math behind Prophet; now let’s implement it on a time-series dataset.

Note

The input to the Prophet model should always be a DataFrame with the columns ds and y, with ds being a date field (YYYY-MM-DD or YYYY-MM-DD HH:MM:SS) with a corresponding y variable.

Import the required libraries and load the CSV data , which contains bike-sharing data. The data is from January 2011 to December 2012.

Each day comprises 24 observations as data is at the hourly level. Using this dataset, let’s try to forecast the count (the target variable) for the next 48 hours, which is two days.

```
import warnings
import matplotlib.pyplot as plt
import numpy as np
import pandas as pd
from sklearn import metrics
from fbprophet import Prophet
```

Let’s take a sneak peek at the data.

```
df = pd.read_csv('Data\Bike_Sharing_Demand.csv',parse_dates = True)
df.head()
```

![../images/492113_1_En_8_Chapter/492113_1_En_8_Figa_HTML.jpg](images/492113_1_En_8_Chapter/492113_1_En_8_Figa_HTML.jpg)

Define the time-series evaluation function.

```
def timeseries_evaluation_metrics_func(y_true, y_pred):
def mean_absolute_percentage_error(y_true, y_pred):
y_true, y_pred = np.array(y_true), np.array(y_pred)
return np.mean(np.abs((y_true - y_pred) / y_true)) * 100
#print('Evaluation metric results:-')
print(f'MSE is : {metrics.mean_squared_error(y_true, y_pred)}')
print(f'MAE is : {metrics.mean_absolute_error(y_true, y_pred)}')
print(f'RMSE is : {np.sqrt(metrics.mean_squared_error(y_true, y_pred))}')
print(f'MAPE is : {mean_absolute_percentage_error(y_true, y_pred)}')
print(f'R2 is : {metrics.r2_score(y_true, y_pred)}',end='\n\n')
```

Change the data into a format that Prophet accepts.

```
df = df.rename(columns={'datetime': 'ds', 'count': 'y'})
```

Train/test the split, and let’s hold back two days of data (i.e., 48 records), which we can use to validate the results after training on past data.

```
validate = df[['ds','y']].tail(48)
df.drop(df[['ds','y']].tail(48).index,inplace=True)
train = df[['ds','y']]
```

Let’s initialize the `Prophet` class and then fit it with the default parameter, which will internally find the best parameters for most parameters.

```
m = Prophet(yearly_seasonality=False)
m.fit(train)
```

Prophet requires a future DataFrame to do any forecasting, so let’s create one with a frequency of `H` for the next 48 hours.

Other frequency options available are `day`, `week`, `month`, `quarter`, `year`, `1` (1 second), `60` (1 minute), `3600` (1 hour), and `H` (hourly).

```
p = 48
future =m.make_future_dataframe(periods=p,freq='H',include_history=False)
forecast = m.predict(future)
timeseries_evaluation_metrics_func(validate.y,forecast.yhat)
```

![../images/492113_1_En_8_Chapter/492113_1_En_8_Figb_HTML.jpg](images/492113_1_En_8_Chapter/492113_1_En_8_Figb_HTML.jpg)

Figure [8-1](#492113_1_En_8_Chapter.xhtml#Fig1) shows the forecast plot.

![../images/492113_1_En_8_Chapter/492113_1_En_8_Fig1_HTML.jpg](images/492113_1_En_8_Chapter/492113_1_En_8_Fig1_HTML.jpg)

Figure 8-1

Representation of forecast

```
fig1 = m.plot(forecast)
```

Let’s plot the components of the forecast (Figure [8-2](#492113_1_En_8_Chapter.xhtml#Fig2)).

![../images/492113_1_En_8_Chapter/492113_1_En_8_Fig2_HTML.jpg](images/492113_1_En_8_Chapter/492113_1_En_8_Fig2_HTML.jpg)

Figure 8-2

Representation of forecasted components

```
fig2 = m.plot_components(forecast)
```

The `add_changepoints_to_plot` function adds red lines; the vertical dashed lines are changepoints where Prophet has identified that the trend has changed (Figure [8-3](#492113_1_En_8_Chapter.xhtml#Fig3)).

![../images/492113_1_En_8_Chapter/492113_1_En_8_Fig3_HTML.jpg](images/492113_1_En_8_Chapter/492113_1_En_8_Fig3_HTML.jpg)

Figure 8-3

Representation of trend changepoints

```
from fbprophet.plot import add_changepoints_to_plot
fig = m.plot(forecast)
a = add_changepoints_to_plot(fig.gca(), m, forecast)
```

### Adding Log Transformation

In the previous section, you learned how to model time-series problems using Prophet. In this section, let’s apply log transformations on the data and check whether the accuracy increases.

*Log-transformed* data follows a normal or near-normal distribution. Import the required libraries and load the CSV data.

```
import warnings
import matplotlib.pyplot as plt
import numpy as np
import pandas as pd
from sklearn import metrics
from fbprophet import Prophet
```

Let’s take a sneak peek at the data.

```
df = pd.read_csv('Data\Bike_Sharing_Demand.csv',parse_dates = True)
df.head()
```

![../images/492113_1_En_8_Chapter/492113_1_En_8_Figc_HTML.jpg](images/492113_1_En_8_Chapter/492113_1_En_8_Figc_HTML.jpg)

Define the time-series evaluation function.

```
def timeseries_evaluation_metrics_func(y_true, y_pred):
def mean_absolute_percentage_error(y_true, y_pred):
y_true, y_pred = np.array(y_true), np.array(y_pred)
return np.mean(np.abs((y_true - y_pred) / y_true)) * 100
#print('Evaluation metric results:-')
print(f'MSE is : {metrics.mean_squared_error(y_true, y_pred)}')
print(f'MAE is : {metrics.mean_absolute_error(y_true, y_pred)}')
print(f'RMSE is : {np.sqrt(metrics.mean_squared_error(y_true, y_pred))}')
print(f'MAPE is : {mean_absolute_percentage_error(y_true, y_pred)}')
print(f'R2 is : {metrics.r2_score(y_true, y_pred)}',end='\n\n')
```

Change the data into a format that Prophet accepts.

```
df = df.rename(columns={'datetime': 'ds', 'count': 'y'})
```

Train/test the split. Let’s hold back two days of data (i.e., 48 records), which we can use to validate the results after training on past data.

```
validate = df[['ds','y']].tail(48)
df.drop(df[['ds','y']].tail(48).index,inplace=True)
train = df[['ds','y']]
```

We are applying log transformation to make the data normally distributed.

```
train['y']= np.log(train['y'])
```

Let’s initialize the `Prophet` class and then fit it.

```
m = Prophet(yearly_seasonality=False)
m.fit(train)
```

Prophet requires a future DataFrame to do any forecasting, so let’s create one with a frequency of `H` for the next 48 hours.

```
p = 48
future = make_future_dataframe(periods=p,freq='H',include_history=False)
forecast = m.predict(future)
```

Like we applied a log transformation on input data, we need to apply an inverse log transformation on the results to bring them back to the original scale.

```
forecast['yhat'] = np.exp(forecast['yhat'])
timeseries_evaluation_metrics_func(validate.y,forecast['yhat'])
```

![../images/492113_1_En_8_Chapter/492113_1_En_8_Figd_HTML.jpg](images/492113_1_En_8_Chapter/492113_1_En_8_Figd_HTML.jpg)

Figure [8-4](#492113_1_En_8_Chapter.xhtml#Fig4) shows the forecast plot.

![../images/492113_1_En_8_Chapter/492113_1_En_8_Fig4_HTML.jpg](images/492113_1_En_8_Chapter/492113_1_En_8_Fig4_HTML.jpg)

Figure 8-4

Representation of forecast

```
fig1 = m.plot(forecast)
```

Let’s plot the components of the forecast (Figure [8-5](#492113_1_En_8_Chapter.xhtml#Fig5)).

![../images/492113_1_En_8_Chapter/492113_1_En_8_Fig5_HTML.jpg](images/492113_1_En_8_Chapter/492113_1_En_8_Fig5_HTML.jpg)

Figure 8-5

Representation of forecasted components

```
fig2 = m.plot_components(forecast)
```

Create the changepoints plot (Figure [8-6](#492113_1_En_8_Chapter.xhtml#Fig6)).

![../images/492113_1_En_8_Chapter/492113_1_En_8_Fig6_HTML.jpg](images/492113_1_En_8_Chapter/492113_1_En_8_Fig6_HTML.jpg)

Figure 8-6

Representation of trend changepoints

```
from fbprophet.plot import add_changepoints_to_plot
fig = m.plot(forecast)
a = add_changepoints_to_plot(fig.gca(), m, forecast)
```

By applying a log transformation, we didn’t see an increase in the model accuracy; rather, it was reduced.

### Adding Built-in Country Holidays

In this section, let’s add US country holidays, which is built in to Prophet, and check whether the accuracy increases.

Prophet includes holidays for these countries: Brazil (BR), Indonesia (ID), India (IN), Malaysia (MY), Vietnam (VN), Thailand (TH), Philippines (PH), Turkey (TU), Pakistan (PK), Bangladesh (BD), Egypt (EG), China (CN), and Russia (RU).

Import the required libraries and load the CSV data.

```
import warnings
import matplotlib.pyplot as plt
import numpy as np
import pandas as pd
from sklearn import metrics
from fbprophet import Prophet
```

Let’s take a sneak peek at the data.

```
df = pd.read_csv(r'Data\Bike_Sharing_Demand.csv',parse_dates = True)
df.head()
```

![../images/492113_1_En_8_Chapter/492113_1_En_8_Fige_HTML.jpg](images/492113_1_En_8_Chapter/492113_1_En_8_Fige_HTML.jpg)

Define the time-series evaluation function.

```
def timeseries_evaluation_metrics_func(y_true, y_pred):
def mean_absolute_percentage_error(y_true, y_pred):
y_true, y_pred = np.array(y_true), np.array(y_pred)
return np.mean(np.abs((y_true - y_pred) / y_true)) * 100
#print('Evaluation metric results:-')
print(f'MSE is : {metrics.mean_squared_error(y_true, y_pred)}')
print(f'MAE is : {metrics.mean_absolute_error(y_true, y_pred)}')
print(f'RMSE is : {np.sqrt(metrics.mean_squared_error(y_true, y_pred))}')
print(f'MAPE is : {mean_absolute_percentage_error(y_true, y_pred)}')
print(f'R2 is : {metrics.r2_score(y_true, y_pred)}',end='\n\n')
```

Change the data into a format that Prophet accepts.

```
df = df.rename(columns={'datetime': 'ds', 'count': 'y'})
```

Train/test the split, and let’s hold back two days of data (i.e., 48 records), which we can use to validate the data after training on past data.

```
validate = df[['ds','y']].tail(48)
df.drop(df[['ds','y']].tail(48).index,inplace=True)
train = df[['ds','y']]
```

Let’s initialize the `Prophet` class, add country holidays using `add_country_holidays`, and then fit the data with the default parameters.

```
m = Prophet(yearly_seasonality=False)
m.add_country_holidays(country_name='US')
m.fit(train)
```

Prophet requires a future DataFrame to do any forecasting, so let’s create one with a frequency of `H` for the next 48 hours.

```
p = 48
future = m.make_future_dataframe(periods=p,freq='H',include_history=False)
forecast = m.predict(future)
timeseries_evaluation_metrics_func(validate.y,forecast.yhat)
```

![../images/492113_1_En_8_Chapter/492113_1_En_8_Figf_HTML.jpg](images/492113_1_En_8_Chapter/492113_1_En_8_Figf_HTML.jpg)

Create the changepoints plot (Figure [8-7](#492113_1_En_8_Chapter.xhtml#Fig7)).

![../images/492113_1_En_8_Chapter/492113_1_En_8_Fig7_HTML.jpg](images/492113_1_En_8_Chapter/492113_1_En_8_Fig7_HTML.jpg)

Figure 8-7

Representation of trend changepoints

```
from fbprophet.plot import add_changepoints_to_plot
fig = m.plot(forecast)
a = add_changepoints_to_plot(fig.gca(), m, forecast)
```

## Adding Exogenous variables using add_regressors(function)

In this section, let’s add exogenous variables , which might influence target variables, and check whether the accuracy increases. We can call this problem a multivariant time-series problem too.

```
import warnings
import matplotlib.pyplot as plt
import numpy as np
import pandas as pd
from sklearn import metrics
from fbprophet import Prophet
```

Import the required libraries and load the CSV data.

```
df = pd.read_csv('Data\Bike_Sharing_Demand.csv',parse_dates = True)
df.head()
```

![../images/492113_1_En_8_Chapter/492113_1_En_8_Figg_HTML.jpg](images/492113_1_En_8_Chapter/492113_1_En_8_Figg_HTML.jpg)

```
df['datetime'] = pd.to_datetime(df['datetime'])
```

Define the time-series evaluation function.

```
def timeseries_evaluation_metrics_func(y_true, y_pred):
def mean_absolute_percentage_error(y_true, y_pred):
y_true, y_pred = np.array(y_true), np.array(y_pred)
return np.mean(np.abs((y_true - y_pred) / y_true)) * 100
#print('Evaluation metric results:-')
print(f'MSE is : {metrics.mean_squared_error(y_true, y_pred)}')
print(f'MAE is : {metrics.mean_absolute_error(y_true, y_pred)}')
print(f'RMSE is : {np.sqrt(metrics.mean_squared_error(y_true, y_pred))}')
print(f'MAPE is : {mean_absolute_percentage_error(y_true, y_pred)}')
print(f'R2 is : {metrics.r2_score(y_true, y_pred)}',end='\n\n')
```

Change the data into a format that Prophet accepts .

```
df = df.rename(columns={'datetime': 'ds', 'count': 'y'})
```

Train/test the split, and let’s hold back two days of data (i.e., 48 records), which we can use to validate the results after training on past data. We need to do the same with exogenous and add it to the original DataFrame. Then we can use it further to train and validate the data.

```
validate = df[['ds','y','season','holiday','weather','temp','humidity','windspeed']].tail(48)
df.drop(df[['ds','y','season','holiday','weather','temp','humidity','windspeed']].tail(48).index,inplace=True)
train = df[['ds','y','season','holiday','weather','temp','humidity','windspeed']]
```

Let’s initialize the `Prophet` class, then add exogenous variables one by one, and finally fit the model on the training data.

```
m = Prophet(yearly_seasonality=False)
m.add_regressor('season')
m.add_regressor('holiday')
m.add_regressor('weather')
m.add_regressor('temp')
m.add_regressor('humidity')
m.add_regressor('windspeed')
m.fit(train)
```

Prophet requires a future DataFrame to do any forecasting, so let’s create one with a 48 frequency of `H` for the next 48 hours.

```
p = 48
future = m.make_future_dataframe(periods=p,freq='H',include_history=False)
```

Add exogenous into the forecast DataFrame so that the model can use them while forecasting.

```
future['season'] = validate['season'].values
future['holiday'] = validate['holiday'].values
future['weather'] =  validate['weather'].values
future['temp'] = validate['temp'].values
future['humidity'] =  validate['humidity'].values
future['windspeed'] = validate['windspeed'].values
forecast = m.predict(future)
timeseries_evaluation_metrics_func(validate.y,forecast.yhat)
```

![../images/492113_1_En_8_Chapter/492113_1_En_8_Figh_HTML.jpg](images/492113_1_En_8_Chapter/492113_1_En_8_Figh_HTML.jpg)

Forecast the plot (Figure [8-8](#492113_1_En_8_Chapter.xhtml#Fig8)).

![../images/492113_1_En_8_Chapter/492113_1_En_8_Fig8_HTML.jpg](images/492113_1_En_8_Chapter/492113_1_En_8_Fig8_HTML.jpg)

Figure 8-8

Representation of forecast

```
fig1 = m.plot(forecast)
```

Let’s plot the components of the forecast (Figure [8-9](#492113_1_En_8_Chapter.xhtml#Fig9)).

![../images/492113_1_En_8_Chapter/492113_1_En_8_Fig9_HTML.jpg](images/492113_1_En_8_Chapter/492113_1_En_8_Fig9_HTML.jpg)

Figure 8-9

Representation of forecasted components

```
fig2 = m.plot_components(forecast)
```

Plot the changepoints (Figure [8-10](#492113_1_En_8_Chapter.xhtml#Fig10)).

![../images/492113_1_En_8_Chapter/492113_1_En_8_Fig10_HTML.jpg](images/492113_1_En_8_Chapter/492113_1_En_8_Fig10_HTML.jpg)

Figure 8-10

Representation of trend changepoints

```
from fbprophet.plot import add_changepoints_to_plot
fig = m.plot(forecast)
a = add_changepoints_to_plot(fig.gca(), m, forecast)
```

## Summary

In this chapter, you learned about the high-level math behind Prophet, how to create a basic model, how to apply log transformations to time-series data, how to add country holidays, and how to handle multivariate time-series data using `add_regressor`.

Index A Activation function binary step function linear activation function nonlinear SeeNonlinear activation functions types Additive model Additive seasonality model add_regressor .agg() function Aggregation, GROUP BY Akaike Information Criterion (AIC) Alpha ARMA model Ascending data order Augmented Dickey-Fuller (ADF) test Auto-arima Autocorrelation function (ACF) Autocorrelation plot Auto-encoder Auto-encoder LSTM multivariate horizon style actual *vs.* predicted values best weights drop duplicates early stopping and checkpointing five-point summary function, defining import libraries model summary rescale predicted values time-series evaluation function training and validation loss training and validation time-series data train/test split Auto-encoder LSTM univariate horizon style actual *vs.* predicted values auto-encoder model best weights early stopping and checkpointing function, defining import libraries line plot loss and val_loss model summary, checking predicted values rescale data sneak peek, data time-series evaluation function training and validation loss training and validation time-series data Auto-encoder LSTM univariate single-step style actual *vs.* predicted values, plot best weights early stopping and checkpointing encode-decoder model function, defining import libraries loss and val_loss model summary, checking predicted values rescale data sneak peek, data time-series evaluation function training and validation loss training and validation time-series data train/test Autoregressive (AR) models Autoregressive integrated moving average (ARIMA) ADF test function confidence bounds DataFrame diagnostic plot exploratory data analysis Facebook stock data integration (I) libraries and load CSV data model training nonseasonal pmdarima module representation of p, d, q time-series evaluation function Autoregressive model B Backpropagation through time approach (BPTT) Backward propagation Brute-force approach gradient descent learning rate *vs.* gradient descent optimizers representation Balanced panel data Bayesian Information Criterion (BIC) Bernoulli distribution Bidirectional LSTM multivariate horizon style actual *vs.* predicted values best weights early stopping and checkpointing five-point summary function, defining import libraries load CSV data neural networks rescale predicted values time-series evaluation function Trained_model**.**summary () training and validation loss training and validation time-series data train/test Bidirectional LSTM univariate horizon style Bidirectional LSTM univariate single-step style actual *vs.* predicted values function, defining model summary, check neural networks predicted values, rescale required libraries and load CSV files see /train sneak peek, data stopping and checkpointing time-series evaluation function training and validation loss training and validation time-series data train/test, split Binary step function Biological neurons *vs.* artificial neural network Box plot Brute-force approach C Candlestick charts CNN multivariate horizon style actual *vs.* predicted values, plot best weights drop duplicates early stopping and checkpointing five-point summary function, defining import libraries label encoding loss and val_loss model summary rescale predicted values time-series evaluation function training and validation loss training and validation time-series data train/test split CNN univariate horizon style actual *vs.* predicted values best weights early stopping and checkpointing five-point summary function, defining import libraries model summary predicted values time-series evaluation function training and validation loss training and validation time-series data train/test CNN univariate single-step style actual *vs.* predicted values checkpointing configuration five-point summary function, defining import libraries line plot model summary predicted values rescale data see /train time-series evaluation function training and validation loss training and validation time-series data train/test Cointegration test Constant error carousel (CEC) units Continuous time series Convolutional LSTM (ConvLSTM) Convolution neural networks (CNNs) batch normalization convolutional layer convolution formula convolution operation dropouts filter formulas one-dimensional pooling Correlation Cost function Cross-entropy Cross-section data CSV files Cyclical components Cyclic variations accessibility depression detecting prosperity D Dampening Data filtering Data stationery plots statistics unit root tests summary statistics Data types cross-section panel data/longitudinal data time series Data wrangling Pandas SeePandas Decoding Decomposition Dendrites Descending data order describe() function Descriptive statistics Deseasoning Detrending HP filter Pandas differencing SciPy signal time-series data df.shift() function Dickey-Fuller (DF) test diff() function Difference stationary Differencing (lag difference) Discrete time series Double exponential smoothing additive/multiplicative trend automated grid search model summary best parameters custom grid search model custom grid search parameters fit parameters Holt parameters Holt’s exponential smoothing forecast formula line plot depicting results loaded dataset sneak peek trend component Drill-down data Dying ReLU problem E Encoding Expanding window Exploratory data analysis Exponentially weighted moving window Exponential smoothing F Feed-forward recurrent neural network First-order differencing (trend differencing) First-order stationary Forward propagation Full outer join G Gated recurrent unit (GRU) Gaussian distribution data Gradient descent Gradient descent optimization algorithms groupby() function GRU multivariate horizon style actual *vs.* predicted values, plot best weights drop duplicates early stopping and checkpointing five-point summary function, defining import libraries label encoding line plot model summary rescale predicted values time-series evaluation function training and validation loss training and validation time-series data train/test split GRU univariate horizon style GRU univariate single-step style actual *vs.* predicted values, plot best weights early stopping and checkpointing function, defining import libraries loss and val_loss model summary checking predicted values rescale data see /train sneak peek, data time-series evaluation function training and validation loss training and validation time-series data train/test H Hodrick-Prescott (HP) filter Holt method Holt-Winter exponential smoothing Holt-Winters method Horizon-style time-series forecasting HP filter detrending I Inner join Inner merge Inverse differencing function J Join full outer inner left right JSON format K Kernel density estimation (KDE) Kwiatkowski-Phillips-Schmidt-Shin (KPSS) tests L Leaky ReLU Learning rate *vs.* gradient descent optimizers Left join Left merge Linear activation function Loading data into Pandas CSV files Excel file JSON format URL Log-transformed data Long short-term memory (LSTM) cell architecture with formulas convolutional exploding and vanishing gradient problems gates long-term dependencies peephole *vs.* RNN step-by-step explanation T time step LSTM multivariate horizon style actual *vs.* predicted values bar plot representation best weights early stopping and checkpointing five-point summary, checking import libraries label encoding line plot neural networks predicted values single window of past history time-series evaluation function Trained_model**.**summary () training and validation loss training and validation time-series data train/test LSTM univariate horizon style actual *vs.* predicted values checkpointing data and forecast function, defining loss and val_loss model summary predicted values required libraries and load CSV files rescale data single window, past history sneak peek, data target horizon time-series evaluation function training and validation loss training and validation time-series data training model and forecast LSTM univariate single-step style actual *vs.* predicted values data and forecast drop duplicates function defining loss and val_loss machine and deep learning model summary single window of past history Tensorflow time-series evaluation function training and validation loss training and validation time-series data M Mean absolute error (MAE) Mean absolute percentage error (MAPE) Mean squared error (MSE) Measure of central tendency Measure of variability Minimal gated unit Missing data handling backward fills method FILLNA forward fill method interpolates values Isnull() Missing value/data Moving average (MA) Multiplicative model Multivariate time series Multivariate forecasting method N Neural networks Neurons Nonlinear activation functions leaky ReLU parametric ReLU ReLU sigmoid softmax tanH training models Nonstationary time series Normal random walk NOT IN operation NumPy O One-dimensional CNNs Optimization methods Optimizers ORDER BY sorts Outer merge Over-smoothing P, Q Pandas DataFrames describe() function filter, apply group by() function GROUP BY operation isin() function join (merge) SeeJoin NOT IN operation shifting operations unique() function Pandas differencing pandas.head() function Pandasql GROUP BY operation filter, apply Python framework sqldf Panel data/longitudinal data Parametric ReLU Partial autocorrelation function (PACF) Peephole LSTM Perceptron Phillips-Perron (PP) test Pmdarima Principal component analysis (PCA) Probability density function (PDF) Prophet adding built-in country holidays adding exogenous/add_regressors apply log transformations implementation add_changepoints_to_plot function forecasted components forecast plot future DataFrame import libraries load CSV data time-series evaluation function train/test split trend changepoints open source framework time-series model P-value, interpret R R-squared Radial basis function Random walk Rectified linear unit (ReLU) Recurrent neural network (RNN) BPTT SeeBackpropagation through time approach (BPTT feed-forward *vs.* LSTM many-to-many relationship many-to-one relationship *vs.* neural network one-to-many relationship Regression model Resampling by month by quarter by week by year operations in Pandas semimonthly basis Right merge Rolling window Root mean square error (RMSE) S SARIMAX model Schmidt-Phillips test SciPy detrending SciPy library Seasonal ARIMA (SARIMA) ADF test function interpreting nonseasonal and seasonal components modeling multiplicative model nonseasonal components nonstationary and auto-arima number of periods search space sneak peek, data time-series dataset time-series evaluation function trend elements univariate time-series data Seasonal differencing first-order differencing second-order differencing Seasonality autocorrelation plot decomposition deseasoning, time-series data multiple box plots periodical fluctuation Second-order differencing Serial correlation Shifting Sigmoid activation function Simple exponential smoothing (SES) algorithm’s hyperparameters alpha forecast estimate level high-level math behind line plot depicting results mean absolute percentage error (MAPE) NumPy optimal parameter search results root mean square error (RMSE) rows of dataset SimpleExpSmoothing parameters sklearn time-series dataset Simple grid search Single-step time-series forecasting sklearn Softmax activation function sort_value() function SQL syntax Stationary time series Stationary data techniques differencing first-order differencing (trend differencing) random walk seasonal difference SeeSeasonal differencing second-order differencing (trend differencing) Stationary time-series data Statistics unit root tests Strict or strong stationary Subsetting data Summary statistics Swish activation T tanH activation function Tensorflow Tied weights Time-series analysis Time-series data Time-series decomposition Time-series modeling techniques Trend definition detrending SeeDetrending Hodrick-Prescott (HP) filter line stationary Triple exponential smoothing additive trend algorithm’s hyperparameters for reference automated grid search model summary best parameters, finding custom grid search evaluation metrics fit parameters forecasting equation grid search parameters line plot depicting results rows of dataset simple grid search sneak peek U Unbalanced panel data Unexpected variations unique() function Univariate time series Unpredictable errors V Vanishing gradient problem (VGP) VARMA model ADF test function cointegration data stationary, Pandas differencing df_results_moni DataFrame grid search inverse differencing function inverse forecasted results multivariate time-series data parameter grid print DataFrame values results, plot sneak peek, data time-series dataset time-series evaluation function variables are stationary Vector autoregression (VAR) ADF test function aligning plots bidirectional model cointegration test data stationary data stationary, Pandas differencing histogram and KDE line, histogram, and KDE plots multivariate forecasting method Pandas differencing plot, histogram and KDE plots creation results sneak peek, data stochastic process model test train split time-series dataset time-series evaluation function univariate time series variables are stationary Vector moving average (VMA) W Weak (second-order) stationary Windowing function expanding window exponentially weighted moving window rolling window Winter method X, Y, Z XGBoost