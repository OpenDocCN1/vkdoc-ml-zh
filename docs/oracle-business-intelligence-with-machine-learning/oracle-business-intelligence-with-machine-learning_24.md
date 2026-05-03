# "acc"（准确率）、"err"（错误率）等。
prf1 <- performance(pr1, measure = "tpr", x.measure = "fpr")
class(prf1) # 这会将类显示为 "performance"
pdf("plot_prf1.pdf") # 这会将绘制的图形保存为工作目录中的 PDF 文件
