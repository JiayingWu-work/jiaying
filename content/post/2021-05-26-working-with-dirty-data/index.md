---
title: Working with dirty data
author: Jiaying
date: '2021-05-26'
slug: working-with-dirty-data
diagram: true
highlight: true
math: true
categories: []
tags: []
subtitle: ''
summary: 'Use r package `stringr` clean dirty data to make comparison and matching'
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: []
---

In the summer of 2021, I have started to work at the public available collection data of the Minneapolis Institute of Art with Professor Brianna c. Heggeseth. Unlike doing projects in classes, the history data we have are not clean. Many strings do not have the exhibition location, dates, and objects ID, not in order. And we have found so many ways staff used to keep track of the exhibitions' dates. 

While working on an API matching project, I learn a few tips about working with dirty data, especially on working with dirty strings within a data frame. And I will share them here!

For example, we would see dirty data like (these data are not real, but you can tell how it generally looks like):

```markdown
ID    Exhibitions 
-------------------------------------------------- 
 1   -New York, NY, The Metropolitan Museum of Art, October 1938, Cat# 358  
 2   -New York, NY, The C.T. Loo Galleries, "An exhbition of Chinese  Bronzes", 1939, Cat.#38, pl.13  -Richmond, VA, The Virginia Museum of Fine Arts, November 19, 1954 -  January 2, 1955 
 3   -St.Paul, MN, The Governor's Residence, 1006 Summit Avenue, St. Paul, January 12, 1979 - July 6, 1983, #16
 4   -The Sachs Years, MIA, June 1-August 4, 1985.
 5   -Rotation 2002
 6   -The Minneapolis Institute of Arts, 'Recent Accessions', 6/12/99 - 10/3/99
```

Some exhibitions have a format as $$location \rightarrow museum \rightarrow date$$ 
while some of them has a format as $$museum \rightarrow exhibition title \rightarrow date$$ 
Moreover, many objects (artwork) are matched (exhibited) to more than one exhibition, which is pretty common in the art world as museums often borrow artworks from each other. So in the data, we would see each object has been exhibited in multiple museums and have all records in the same block like shown in object 2. Such a situation makes the matching work harder than usual. So we need to separate and clean each string first before working on a specific exhibition title or date.

We have found a general pattern: most exhibition records have a `\\n` between them in each string. R package `stringr` is pretty helpful while we are working with dirty data. For example, we split these chucked strings according to the pattern `\\n`. 

```markdown
# process and read the exhibition string data
ExhHistory <- ExhHistory %>% mutate(ExhHistoryTextReader = str_split(Exhibitions,pattern = '(; \\n\\n)|(\\n\\n)|(\\n\\t\\n)')) %>% unnest(ExhHistoryTextReader)
ExhHistory <- ExhHistory %>% filter(ExhHistoryTextReader!='')
```

Then it comes to the fun part, read and find the exact date and exhibition museums from the string, to compare and match with data in the Mia API collection. As I mentioned before, dates are in different formats, and we have seen:

```markdown
# this is only a demo, not including all data we have seen

January 12, 1979 - July 6, 1983
6/12/99 - 10/3/99
Nov. 2003-Feb. 2004.
August 15, 1951.
1978-9,
```

In the task, I am also introduced to regular expression for the first time. We first find the general patterns of how year, month, and day formats, and use `str_extract_all` to extract them. Working and finding some common patterns are pretty fun: 

```markdown
SeasonRegex <- '((fall)|(spring)|(summer)|(winter))'
YearRegex <- "((17|18|19|20)[0-9][0-9])"
DayRegex <- "([ ]*[0-9]{0,2}[ ]*[,.]*)"
MonthRegex <- paste0('((',paste0(tolower(month.name),collapse=')|('),')|(',paste0(tolower(c("Sept",month.abb)),collapse='[.]*)|('), "[.]*))[ ,]*")

dateRegex <- paste0(MonthRegex, DayRegex,"[ ]*((to)|(\\-))*[ ]*((",DayRegex,")|(",MonthRegex,DayRegex,"))*",YearRegex,"*")
dateRegex2 <- "[0-9]+(/|\\-)[0-9]+(/|\\-)*[0-9]*"
dateRegex3 <- paste0('(',YearRegex,"[ ]*((to)|(\\-))*[ ]*",YearRegex,')|(','\\(',YearRegex,'\\)'  ,')')
dateRegex4 <- paste0(DayRegex,"[ ]*((to)|(\\-))*[ ]*",MonthRegex,"[ ]*((to)|(\\-))*[ ]*",YearRegex)
dateRegex5 <- paste0("[0-9]{0,2}+(/|\\-)",DayRegex,"(-)+[0-9]{0,2}+(/|\\-)",DayRegex,"(/|\\-)+[0-9][0-9]")
dateRegex6 <- paste0(SeasonRegex,'[ ]',YearRegex)
dateRegex7 <- paste0('(?<!no. )',YearRegex)


ExhHistory$ExhHistoryDates <- str_extract_all(tolower(ExhHistory$ExhExtraText), pattern = paste0('(',dateRegex,')|(',dateRegex2,')|(',dateRegex3,')|(',dateRegex4,')|(',dateRegex5,')|(',dateRegex6,')|(',dateRegex7,')'), simplify = FALSE)
```
After all, we can extract almost all dates based on their patterns using regular expression. And we have found a useful website to check if we get the correct regular expression: https://regex101.com/ 

One of the most essential reflections I have after working with dirty: import clean date!


Now, I have made some progress in cleaning dirty data in R:

- [x] Cleaning strings using `stringr` package
- [x] Create and check `regular expression`

See you guys in the next post  🙌
