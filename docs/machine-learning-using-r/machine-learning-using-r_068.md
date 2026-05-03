# CART
set.seed(917);
tunegrid <-expand.grid(.cp=seq(0,0.1,by=0.01))
fit.cart <-train(factor(ProductChoice) ~MembershipPoints +CustomerAge +PurchaseTenure +CustomerPropensity +LastPurchaseDuration, data=dataset, method="rpart", metric="Accuracy", tuneGrid=tunegrid, trControl=control)
Loading required package: rpart
fit.cart
CART
10000 samples
5 predictor
4 classes: '1', '2', '3', '4'
No pre-processing
Resampling: Cross-Validated (10 fold, repeated 3 times)
Summary of sample sizes: 9000, 8999, 9001, 9001, 9000, 9000, ...
Resampling results across tuning parameters:
cp    Accuracy   Kappa
0.00  0.3557312  0.05943192
0.01  0.4014336  0.04179296
0.02  0.3966989  0.02481739
0.03  0.3907000  0.00000000
0.04  0.3907000  0.00000000
0.05  0.3907000  0.00000000
0.06  0.3907000  0.00000000
0.07  0.3907000  0.00000000
0.08  0.3907000  0.00000000
0.09  0.3907000  0.00000000
0.10  0.3907000  0.00000000
Accuracy was used to select the optimal model using  the largest value.
The final value used for the model was cp = 0.01.
