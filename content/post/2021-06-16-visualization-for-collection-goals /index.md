---
title: "Visualization for Collection Goals"
author: "Jiaying"
date: '2021-06-16'
slug: visualization-for-collection-goals
diagram: yes
highlight: yes
math: yes
categories: []
tags: []
subtitle: ''
summary: thinking about the most efficient and sustainable way to evaluate Mia collection plan goals
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: []
---

In September 2019, Mia wrote "Mia Collection Development Plan 2020" for both 2020 and 2021. In this document, each department reviewed their current collection and made some goals for new collections for the next 2 years, especially focus on some diverse classifications they would like to have.Now, it's June 2021, Mia starts to think about some ways they could use to evaluate how has their collection development goals have been achieved. 

To quantitative evaluate their progress, Prof. Brianna and I met with Mia data team and thinking about making some generalized visualizations for them to check. We made some pretty simple trails in the beginning to check if visualizations works better for them compared to tabled data. 

Q: can I show graph? 

Q: how to show graph instead of just code
 
```{r}
library(ggplot2)
load("MIACleanFullData.RData")

department.AAA.2019 <- SelectData %>% filter(department == "Art of Africa and the Americas") %>% filter(AccessionISODate >= as.Date("2019-01-01"), AccessionISODate <= as.Date("2019-12-31"))

department.AAA.2019 %>% 
  ggplot(mapping = aes(x = country, fill = continent)) + 
  geom_bar() +
  scale_color_viridis_d() +
  theme_light() +
  theme(axis.text.x = element_text(angle = 45, vjust = 1, hjust = 1)) 
```


After a couple general trails, filtered by different department, classification, accession date, etc, we think it's better to make a shinyApp for these visualizations. 

Why can't we just show each goals in separate visualizations? Isn't that easier? 

First, some departments made very specific collection goals, for example, one department would like to check large-scale statuary from Tanzania. To evaluate this goal, we need to include very specific "Dimensions + Classification + Country of origin" accession data for them. But there are a large number of specific goals. So making a shinyApp would allow each department to filter the date, classification, country, dimension, style... directly. Such easy to use web page would make it easier for each department to check their specific goals.

But most importantly, creating a shinyApp will help Mia to make a sustainable way to evaluate their collection goals in the future, and it might also help Mia to check a longer period time of data in order to generate the next collection plan. In the future, they only need to update their database, instead of rewriting the codes for visualizations.



Now, I have made some progress in thinking about the need of clients

- [x] make easy to read and easy to tell the trend graph for them 📈
- [x] create user friendly visualization for non-data clients 📊
- [x] start to think about sustainable visualization might be re-used in the future ⏱

I will update the process of creating shinyApp soon!

See you guys in the next post... 🙌
