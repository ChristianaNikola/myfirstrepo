# myfirstrepo

```{r get_data}
library(gapminder)
library(tibble)
library(stringr)
library(lubridate)
library(knitr)
library(forcats)
library(readxl)
library(tidyverse)
library(dplyr)
library(devtools)
library(pacman) 
if(!file.exists("./data")) {dir.create("./data")}

# Import of Data and cleaning up of the Data
TourismData <- "http://api.worldbank.org/v2/en/indicator/ST.INT.ARVL?downloadformat=excel"
download.file(TourismData, destfile = "./data/tmp.xls", mode = "wb")
TourismData <- read_excel("./data/tmp.xls")

TourismData1 <- select(TourismData, "Data Source", "World Development Indicators", "X__38", "X__39", "X__40", "X__41", "X__42", "X__43", "X__44", "X__45", "X__46", "X__47", "X__48", "X__49", "X__50", "X__51", "X__52", "X__53", "X__54", "X__55", "X__56", "X__57", "X__58", "X__59", "X__60") 
names(TourismData1) <- c("country", "country_code", "year1995", "1996", "1997", "1998", "1999", "year2000", "2001", "2002", "2003", "2004", "year2005", "2006", "2007", "2008", "2009", "year2010", "2011", "2012", "2013", "2014", "2015", "year2016", "2017")
TourismData1 = TourismData1 [-1:-4,]

TourismData1 <- TourismData1[-c(5, 34,36, 47, 59, 60, 61, 62, 63, 66, 72, 93, 96, 100, 101, 102, 103, 105, 108, 126, 132, 133, 134, 137, 138, 140, 151, 154, 159, 168, 179, 181, 189, 195, 196, 213, 215, 216, 228,229, 234, 236, 238, 239, 247), ]

TourismData1$year1995 <- as.numeric(TourismData1$year1995)
TourismData1$year2000 <- as.numeric(TourismData1$year2000)
TourismData1$year2005 <- as.numeric(TourismData1$year2005)
TourismData1$year2010 <- as.numeric(TourismData1$year2010)
TourismData1$year2016 <- as.numeric(TourismData1$year2016)

TourismData1
```

```{r}
#Project Plan
#I downloaded data on international arrivals from 1995 to 2017 from a website called "Our World in Data". Originally, the data is from the World Bank. My plan for this project is to show how international tourism has increased or decreased in certain regions over the past years. In my shiny app I plan to show graphs and hopefully maps of the data. 

#Timeline: I already cleaned up the data and I hope to create the shiny app this weekend.
#I will be working alone

#My Plan is to mainly look at the years below and see what the increase/decrease is over a period of 5 years. (I am still trying to figure out how to put it into a graph). 

TourismData1 %>% 
select("country","year1995", "year2000", "year2005", "year2010", "year2016") %>%
filter(country == "United States")

Selectcountries <- TourismData1 %>% 
select(country,"year1995", "year2000", "year2005", "year2010", "year2016") %>%
filter(country %in% c("France", "Ukraine", "Bahamas, The", "Syrian Arab Republic"))
Selectcountries
 
MostVisited <- TourismData1 %>% 
select(country,year2016) %>%
  arrange(desc(year2016)) 
print(MostVisited, n=218)

Graph <- TourismData1 %>%
  select(country,year2016) %>%
  arrange(desc(year2016)) %>%
  group_by(year2016)
  

LeastVisited <- TourismData1 %>%
select(country, year2016) %>%
arrange(year2016)
  print(LeastVisited, n=10)

HighestOverallIncrease <- TourismData1 %>%
  select(country, year1995, year2016) %>%
  mutate(indec = year2016 - year1995) %>%
  arrange(desc(indec))
print(HighestOverallIncrease, n=10)

HighestOverallDecrease <- TourismData1 %>%
  select(country, year1995, year2016) %>%
  mutate(indec = year2016 - year1995) %>%
  arrange(indec)
print(HighestOverallDecrease, n=10)

#What could be the reason behind this? eg. Venezuela: political/economic crisis in recent years

mean(TourismData1$year1995, na.rm=TRUE)
mean(TourismData1$year2016, na.rm=TRUE)

```

```{r cache=TRUE}
library(ggmap)
library(maptools)
library(maps)

visited <- c("USA", "Austria","Bahamas", "Costa Rica", "Jamaica", "Brazil", "Ireland", "UK", "France", "Spain", "Germany", "Belgium", "Netherlands", "Luxemburg", "Italy", "Switzerland", "Lichtenstein", "Denmark", "Sweden", "Czech Republic", "Croatia", "Hungary", "Slovenia", "Slovakia", "Boasnia and Herzegowina", "Montenegro", "Albania", "Bulgaria", "Greece", "Cyprus", "Turkey", "United Arab Emirates", "Lebanon", "Syria", "Isreal", "Palestine", "Sri Lanka", "Malaysia", "Indonesia", "Taiwan", "Singapore", "Thailand", "Mauritius", "Kenya", "Tanzania", "Australia", "New Zealand", "Fiji", "Malta", "Vatican City", " San Marino", "Monaco", "Canada", "Iceland", "Pakistan")
ll.visited <- geocode(visited) 

# I created a map with all 55 countries that I have visited so far. The code is from https://www.r-bloggers.com/r-beginners-plotting-locations-on-to-a-world-map/ and a modified it so it shows the places I visited. I have not yet decided if I use the map with maps package or the one with ggplot for the presenation.
# I am also planning to use the code of the maps for something of the data above. Maybe show the top 10 countries visited in 2016.

visit.x <- ll.visited$lon
visit.y <- ll.visited$lat

map("world", fill=TRUE, col="white", bg="lightblue", ylim=c(-80, 100), mar=c(0,0,0,0))
points(visit.x,visit.y, col="red", pch=16)

mp <- NULL
mapWorld <- borders("world", colour="gray50", fill="gray50") 
mp <- ggplot() +   mapWorld
mp <- mp+ geom_point(aes(x=visit.x, y=visit.y) ,color="blue", size=1) 
mp
```

```{r}
#Just an idea: not sure if I will use it in the presentation because I have not completely figured it out. Inspiration from: http://stat545.com/block010_dplyr-end-single-table.html

Data1 <- select(TourismData, "Data Source", "World Development Indicators", "X__38", "X__39", "X__40", "X__41", "X__42", "X__43", "X__44", "X__45", "X__46", "X__47", "X__48", "X__49", "X__50", "X__51", "X__52", "X__53", "X__54", "X__55", "X__56", "X__57", "X__58", "X__59", "X__60") 
names(Data1) <- c("country", "country_code", "year1995", "year1996", "year1997", "year1998", "year1999", "year2000", "year2001", "year2002", "year2003", "year2004", "year2005", "year2006", "year2007", "year2008", "year2009", "year2010", "year2011", "year2012", "year2013", "year2014", "year2015", "year2016", "year2017")
Data1 = Data1 [-1:-4,]

Data1 <- Data1[-c(5, 34,36, 47, 59, 60, 61, 62, 63, 66, 72, 93, 96, 100, 101, 102, 103, 105, 108, 126, 132, 133, 134, 137, 138, 140, 151, 154, 159, 168, 179, 181, 189, 195, 196, 213, 215, 216, 228,229, 234, 236, 238, 239, 247), ]

Data1$year1995 <- as.numeric(Data1$year1995)
Data1$year2000 <- as.numeric(Data1$year2000)
Data1$year2005 <- as.numeric(Data1$year2005)
Data1$year2010 <- as.numeric(Data1$year2010)
Data1$year2016 <- as.numeric(Data1$year2016)

gap <- gapminder 

das <- gap %>%
  left_join(Data1,
            by = c("country")) %>%
select(country, country_code, year1997, year2002, year2007, continent, year, lifeExp, pop, gdpPercap) %>%
  rename(visitors = year2007)%>%
  mutate(gdp = pop * gdpPercap) %>%
  filter(year > 1995)

VisGdp <- das %>%
  select(country, visitors, continent, year, lifeExp, pop, gdpPercap, gdp) %>%
  filter(year == 2007) %>%
  arrange(visitors, gdp)
  print(VisGdp, n=5)
  
  
```
