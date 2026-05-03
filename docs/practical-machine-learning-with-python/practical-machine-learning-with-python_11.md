# tokenize and POS tag text tokens
tagged_text = [(token.text, token.tag_) for token in tn.nlp(review)]
pos_score = neg_score = token_count = obj_score = 0

