# summarize the model
print(model)
Learning Vector Quantization
10000 samples
5 predictor
4 classes: '1', '2', '3', '4'
No pre-processing
Resampling: Cross-Validated (10 fold, repeated 3 times)
Summary of sample sizes: 9001, 9000, 9000, 9001, 8999, 9000, ...
Resampling results across tuning parameters:
size  k  Accuracy   Kappa
5    1  0.3403649  0.01508857
5    2  0.3443983  0.02464750
5    3  0.3582986  0.03118553
5    4  0.3556306  0.02933887
5    5  0.3510002  0.03342766
10    1  0.3375292  0.02790863
10    2  0.3387723  0.03024152
10    3  0.3398577  0.03016096
10    4  0.3484939  0.04030847
10    5  0.3457038  0.04743415
20    1  0.3403710  0.04013057
20    2  0.3321322  0.02956459
20    3  0.3380415  0.03934963
20    4  0.3422641  0.04213952
20    5  0.3449026  0.04611466
50    1  0.3353654  0.03588394
50    2  0.3358704  0.03255331
50    3  0.3428662  0.04369310
50    4  0.3421693  0.04713980
50    5  0.3437377  0.04756819
Accuracy was used to select the optimal model using  the largest value.
The final values used for the model were size = 5 and k = 3.
