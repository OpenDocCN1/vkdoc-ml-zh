# get sentiment scores if synsets are found
for word, tag in tagged_text:
ss_set = None
if 'NN' in tag and list(swn.senti_synsets(word, 'n')):
ss_set = list(swn.senti_synsets(word, 'n'))[0]
elif 'VB' in tag and list(swn.senti_synsets(word, 'v')):
ss_set = list(swn.senti_synsets(word, 'v'))[0]
elif 'JJ' in tag and list(swn.senti_synsets(word, 'a')):
ss_set = list(swn.senti_synsets(word, 'a'))[0]
elif 'RB' in tag and list(swn.senti_synsets(word, 'r')):
ss_set = list(swn.senti_synsets(word, 'r'))[0]

