# -*- coding: utf-8 -*-
"""
Created on Tue Oct 08 19:33:25 2019
@author: PUNEETMATHUR
"""
#importing python libraries
import pandas as pd
from io import StringIO
import requests
import os
os.getcwd()
#Loading dataset
fname="cashcrop_Yield_dataset.csv"
agriculture= pd.read_csv(fname, low_memory=False, index_col=False)
df= pd.DataFrame(agriculture)
print(df.head(1))
#Checking data sanctity
print(df.size)
print(df.shape)
print(df.columns)
df.dtypes
#Check if there are any columns with empty/null dataset
df.isnull().any()
#Checking how many columns have null values
df.info()
#Using individual functions to do EDA
#Checking out Statistical data Mean Median Mode correlation
df.mean()
df.median()
df.mode()
#How is the data distributed and detecting Outliers
df.std()
df.max()
df.min()
df.quantile(0.25)*1.5
df.quantile(0.75)*1.5
#How many Outliers in the Total Food ordered column
df.columns
df.dtypes
df.set_index(['FarmID'])
df['Yield_per_ha'].loc[df['Yield_per_ha'] =159.285].count()
#Visualizing the dataset
df.boxplot(figsize=(17, 10))
df.plot.box(vert=False)
df.kurtosis()
df.skew()
import scipy.stats as sp
sp.skew(df['Yield_per_ha'])
#Visualizing dataset
df.plot()
df.hist(figsize=(10, 6))
df.plot.area()
df.plot.area(stacked=False)
#Now look at correlation and patterns
df.corr()
#Change to dataset columns and look at scatter plots closely
df.plot.scatter(x='Yield_per_ha', y="Soil_pH",s=df['Yield_per_ha']*2)
df.plot.hexbin(x='Yield_per_ha', y="Soil_pH", gridsize=20)
#Data Preparation Steps
#Step 1 Split data into features and target variable
