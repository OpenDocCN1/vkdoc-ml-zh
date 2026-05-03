# 保存训练好的模型以便部署
[In]: model_dir = "/dbfs/mlp_model"
[In]: mlp_model.write().overwrite().save(model_dir)
