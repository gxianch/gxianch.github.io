---
cssclasses:
  - cards
  - cards-cols-5
  - iframe-wide
  - cards-cover
  - cards-align-bottom
---
Dataview:

```dataview
table without id("![]("+ cover + ")") as Cover, file.link as Name , author as Author, category, readYear as Year
from #re
```
```
table without id("![]("+ cover + ")") as Cover, file.link as Name , author as Author, category, readYear as Year,readingStatus,reviewCount
from #阅读状态/阅读中  where cover != null and readYear=2022 
```


```dataview
table without id("![]("+ cover + ")") as Cover, file.link as Name , author as Author, categories,tags,readingStatus
```


