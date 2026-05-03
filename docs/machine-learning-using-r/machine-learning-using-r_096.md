# 加载库
library(caret)
library(caretEnsemble)
library(MASS)
set.seed(917);
Data <-data[sample(nrow(data),size=10000),];
dataset <-Data;
dataset$choice <-ifelse(dataset$ProductChoice ==1 |dataset$ProductChoice ==2 ,"A","B")
dataset$choice <-as.factor(dataset$choice)
