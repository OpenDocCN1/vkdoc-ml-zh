# summarize the model
print(model)
Learning Vector Quantization
10000 samples
5 predictor
4 classes: '1', '2', '3', '4'
No pre-processing
Resampling: Cross-Validated (10 fold, repeated 3 times)
Summary of sample sizes: 9000, 8999, 9001, 9001, 9000, 9000, ...
Resampling results across tuning parameters:
size  k   Accuracy   Kappa
11     1  0.3402322  0.03666635
11     6  0.3402335  0.03518447
11    11  0.3394009  0.04093310
11    16  0.3499678  0.04415707
11    21  0.3444298  0.04208990
13     1  0.3379881  0.03523337
13     6  0.3459702  0.04826571
13    11  0.3464008  0.05010497
13    16  0.3467346  0.05055072
13    21  0.3497683  0.05600358
16     1  0.3313684  0.03813657
16     6  0.3460655  0.05013518
16    11  0.3417672  0.04646887
16    16  0.3502685  0.04977277
16    21  0.3456003  0.04755585
19     1  0.3299696  0.03229510
19     6  0.3392352  0.04576555
19    11  0.3361026  0.03859754
19    16  0.3479016  0.05067015
19    21  0.3451598  0.04997000
22     1  0.3365661  0.03454596
22     6  0.3459982  0.04399154
22    11  0.3441293  0.04592163
22    16  0.3506335  0.05187679
22    21  0.3512329  0.05437707
Accuracy was used to select the optimal model using  the largest value.
The final values used for the model were size = 22 and k = 21.
