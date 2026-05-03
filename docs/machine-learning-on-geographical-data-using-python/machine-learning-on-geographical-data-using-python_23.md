# 使用巴黎数据设置范围，但保持不可见
ax = paris.plot(figsize=(15,15), edgecolor="none", color="none")
A_and_B_not_C.plot(ax=ax, edgecolor='none', color='green', alpha=0.8)
