# 以下两个命令会移除任何先前为 R 安装的 H2O 包。
if ("package:h2o" %in%search()) { detach("package:h2o", unload=TRUE) }
if ("h2o" %in%rownames(installed.packages())) { remove.packages("h2o") }
