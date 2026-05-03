# Load Packages
require(tm)
require(NLP)
require(openNLP)
#Read the Forbes article into R environment
y <-paste(scan("Dataset/india_after_independence.txt", what="character", sep=" "),collapse=" ")
convert_text_to_sentences <-function(text, lang ="en") {
