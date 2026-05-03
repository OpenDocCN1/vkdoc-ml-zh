# split data into X and y
X = dataset[:,0:4]
Y = dataset[:,4]
label_encoder = LabelEncoder()
label_encoder = label_encoder.fit(Y)
label_encoded_y = label_encoder.transform(Y)
seed = 7
test_size = 0.33
X_train, X_test, y_train, y_test = train_test_split(
X,
label_encoded_y,
test_size=test_size,
random_state=seed
)
xgmodel = XGBClassifier()
xgmodel.fit(
X_train,
y_train
)
print(xgmodel)

