# momascrape
#dataset uit collectie


library(XML)
library(scrapeR)
library(stringr)

#de hele collectie van MOMA in dataframe gieten
# structuur van url ziet er als volgt uit:
# http://www.moma.org/collection/browse_results.php?artistFilterInitial=&criteria=G%3AHI%3AE%3A1&page_number=1&template_id=1&sort_order=2

#STAP 1: make list with all links to objects in collection (total of 47849 on 22/04/2015)

links <- list()

for (i in 1:47849) {
  templink<-paste("http://www.moma.org/collection/browse_results.php?artistFilterInitial=&criteria=G%3AHI%3AE%3A1&page_number=", i, "&template_id=1&sort_order=2", sep="")  
  links <- c(links, templink)
}

#STAP 2: SCRAPING
#al wat we willen weten staat in "<div class="info box">" object. 
#eerst lege dataframe maken met variabelen die we willen sparen

dataset <- data.frame(
  timestamp = numeric(0),
  artist = character(0),
  nationality = character(0),
  born = numeric(0),
  died = numeric(0),
  medium = character(0),
  dimensions = character(0),
  credit = character(0),
  momanr = character(0),
  copyright = character(0))
  
#find the tags that are interesting to save in html doc
#all data we want to save is in the <div id="omniture_caption">
#artist names and birth/deaths are in subcat div/h4; titles are under <h3>; characteristics are a bit trickier under several dl/<dd>tags

#2.1. artists names in artist.strings vector
artists.strings <- list()

for(i in 1:10) {
  parse.temp <- htmlParse(links[i])
  scrape.temp <- xpathSApply(parse.temp, "//*/div[@id='omniture_caption']/h4", xmlValue)
  artists.strings <- c(artists.strings, scrape.temp)
}

artists.strings

#2.2. characteristiscs of object in collection are stored under a <dt> tag on html page
#2.2.1. title of the work (simply stored under div/h3)

title.strings <- list()

for(i in 1:5) {
  parsetitle.temp <- htmlParse(links[i])
  scrapetitle.temp <- xpathSApply(parsetitle.temp, "//*/div[@id='omniture_caption']/h3", xmlValue)
  title.strings <- c(title.strings, scrapetitle.temp)
}

title.strings

#2.2.2. characteristics of the work
#dd[1] verwijst naar eerste dd tag (in dit geval jaartal), enz. 
#we moeten dus eerst de length() van de scrapekenm.temp bepalen, de sub-for loop moet dan lopen tot length(scrapekenm.temp)

#KLOPT NOG LANGS GEEN KANTEN!!! HIER GEËINDIGD

parsekenm.temp <- htmlParse(links[1])
scrapekenm.temp <- xpathSApply(parsekenm.temp, "//*/div[@id='omniture_caption']/dl/dd", xmlValue)

for(k in 1:length(scrapekenm.temp)) {
parsekenm.temp <- htmlParse(links[1])
scrapekenm.temp <- xpathSApply(parsekenm.temp, "//*/div[@id='omniture_caption']/dl/dd[2]", xmlValue)
scrapekenm.temp
}

#xpathSApply returnt een vector met een aantal items, dit aantal is afhankelijk van hoeveel /dd's er zijn op de html page
#we willen eigenlijk gestructureerde data in dataframe kolommen en hebben dus de titels van de /dd files ook nodig (medium, year, etc.)

kenm.strings <- list()
for(i in 1:5) {
  parsekenm.temp <- htmlParse(links[i])
  scrapekenm.temp <- xpathSApply(parsekenm.temp, "//*/div[@id='omniture_caption']/dl/dd", xmlValue)
  kenm.strings <- c(kenm.strings, scrapekenm.temp)
}


#these commands can be used to clean the data.strings files: 

for(i in 1:10) {
  data.strings[i] <- gsub("[^ :ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz1234567890]","", data.strings[i])
  data.strings[i] <- gsub("  ", "", data.strings[i])
}

data.strings
