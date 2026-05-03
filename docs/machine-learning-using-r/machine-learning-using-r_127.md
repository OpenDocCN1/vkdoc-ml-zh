# Map 阶段
map <-function(k,lines) {
words.list <-strsplit(lines, '\\s+')
words <-unlist(words.list)
return( keyval(words, 1) )
}
