# 获取聚合得分和最终情感
agg_score = scores['compound']
final_sentiment = 'positive' if agg_score >= threshold\
else 'negative'
if verbose:



