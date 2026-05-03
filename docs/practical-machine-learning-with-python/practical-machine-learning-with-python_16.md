# 聚合最终得分
final_score = pos_score - neg_score
norm_final_score = round(float(final_score) / token_count, 2)
final_sentiment = 'positive' if norm_final_score >= 0 else 'negative'
if verbose:
norm_obj_score = round(float(obj_score) / token_count, 2)
norm_pos_score = round(float(pos_score) / token_count, 2)
norm_neg_score = round(float(neg_score) / token_count, 2)

