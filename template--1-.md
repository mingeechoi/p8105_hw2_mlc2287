Homework 2
================
Mingee Choi
10/05/2022

Load Packages

``` r
library(tidyverse)
library(readxl)
```

# Problem 2

To import Mr. Trash Wheel and Professor Trash Wheel datasets:

``` r
mr_trash_wheel_df=
  read_excel("./data/trash.xlsx", sheet=1, range = "A2:N549")%>%
  janitor::clean_names()%>%
  drop_na(data=., dumpster)%>%
  mutate(sports_balls=as.integer(sports_balls))

professor_trash_wheel_df=
  read_excel("./data/trash.xlsx", sheet=2, range = "A2:M96")%>%
  janitor::clean_names()%>%
  drop_na(data=., dumpster)
```

To combine two datasets together:

# Section 2
