# 创建特征列表
features = [feat for feat in list(bike_df_model_ready) if feat not in [outcome, 'instant']]
