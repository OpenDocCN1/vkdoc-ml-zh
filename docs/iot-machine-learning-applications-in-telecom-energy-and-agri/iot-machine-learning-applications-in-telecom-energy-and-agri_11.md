# -*- coding: utf-8 -*-
"""
作者: Puneet Mathur
版权 2020
可自由复制本代码，但需保留以下署名文本：作者 Puneet Mathur, www.PMAuthor.com
"""
import pandas as pd
import sqlite3
#更改路径如下：
#在 Raspbian 系统上：更改为你的 sqlite3 数据库文件路径，扩展名为 *.db
conn = sqlite3.connect('/home/pi/sqlite3/powerplant.db')
#df.to_sql(name='tempdata', con=conn)
curr=conn.cursor()
#包含发电厂数据的表名为：TURBINE_INLET_DATASET
df = pd.read_sql_query("select * from TURBINE_INLET_DATASET;", conn)
print(df)
#curr.execute(query)
#conn.commit()
#查看数据
print(df.columns)
print(df.shape)
#查看数据类型
print(df.dtypes)
df.tail(1)
#检查缺失值
print(df.isnull().any())
#EDA - 探索性数据分析
import numpy as np
print("----------EDA 统计信息---------------")
pd.option_context('display.max_columns', 40)
with pd.option_context('display.max_columns', 40):
print(df.describe(include=[np.number]))
#通过图形查看分布
df.hist(figsize=(10, 6))
#相关性结果
print("----------相关性---------------")
with pd.option_context('display.max_columns', 40):
print(df.corr())
#将数据划分为特征和目标
target=df['Defect']
nm=['Voltage','Current','Temperature','InletGap_mm']
features=df[nm]
with pd.option_context('display.max_columns', 40):
features.head(1)
target.head(1)
#构建模型
from sklearn.model_selection import train_test_split
x_train, x_test, y_train, y_test = train_test_split( features, target, test_size=0.25, random_state=0)
from sklearn.linear_model import LogisticRegression
lr  = LogisticRegression()
lr.fit(x_train, y_train)
