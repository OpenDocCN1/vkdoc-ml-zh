# 分析评论的情感
analyzer = SentimentIntensityAnalyzer()
scores = analyzer.polarity_scores(review)

