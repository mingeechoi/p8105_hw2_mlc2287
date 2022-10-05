Homework 2
================
Mingee Choi
10/05/2022

Load Packages

``` r
library(tidyverse)
library(readxl)
library(dplyr)
```

# Problem 2

To import and clean Mr. Trash Wheel and Professor Trash Wheel datasets:

``` r
mr_trash_wheel_df=
  read_excel("./data/trash.xlsx", sheet=1, range = "A2:N549")%>%
  janitor::clean_names()%>%
  drop_na(data=., dumpster)%>%
  mutate(sports_balls=as.integer(sports_balls))

professor_trash_wheel_df=
  read_excel("./data/trash.xlsx", sheet=2, range = "A2:M96")%>%
  janitor::clean_names()%>%
  drop_na(data=., dumpster)%>%
  mutate(year=as.character(year))
```

To combine two datasets together:

``` r
mr_trash_wheel_df["trash_wheel_type"] <-"mr_trash_wheel"
professor_trash_wheel_df["trash_wheel_type"] <-"professor_trash_wheel"
trash_df=
   full_join(mr_trash_wheel_df, professor_trash_wheel_df , copy=TRUE)
```

    ## Joining, by = c("dumpster", "month", "year", "date", "weight_tons",
    ## "volume_cubic_yards", "plastic_bottles", "polystyrene", "cigarette_butts",
    ## "glass_bottles", "grocery_bags", "chip_bags", "homes_powered",
    ## "trash_wheel_type")

To find key variables

``` r
round(mean(pull(trash_df, weight_tons), na.rm = TRUE))
```

    ## [1] 3

``` r
round(mean(pull(trash_df, volume_cubic_yards), na.rm = TRUE))
```

    ## [1] 15

``` r
round(mean(pull(trash_df, plastic_bottles), na.rm = TRUE))
```

    ## [1] 2465

After including a new variable “Trash Wheel Type”, Mr. Trash Wheel has
547 observations of 15 variables (547x15) and Professor Trash Wheel has
94 observations of 14 variables (94x14). Mr. Trash Wheel and Professor
Trash Wheel all have the same variables except Professor Trash Wheel
does not have sport balls. After combining them together, I get 641
observations of 15 variables (641x15).

\*The combined mean of weight(tons)=3

\*The combined mean of volume(cubic yards)=15

\*The combined mean of plastic bottles=1958

To find total weight of trash collected by professor trash wheel and the
total number of sports balls collected by mr. trash wheel in 2020

``` r
 sum(pull(professor_trash_wheel_df, weight_tons), na.rm = TRUE)
```

    ## [1] 190.12

``` r
   filter(mr_trash_wheel_df, year==2020)
```

    ## # A tibble: 72 × 15
    ##    dumpster month    year  date                weight_…¹ volum…² plast…³ polys…⁴
    ##       <dbl> <chr>    <chr> <dttm>                  <dbl>   <dbl>   <dbl>   <dbl>
    ##  1      381 January  2020  2020-01-18 00:00:00      2.75      15    3900    2100
    ##  2      382 January  2020  2020-01-18 00:00:00      1.8       15    2900    1800
    ##  3      383 January  2020  1900-01-20 00:00:00      2.9       15    1850    1100
    ##  4      384 January  2020  2020-01-27 00:00:00      2.54      15    3400    2400
    ##  5      385 January  2020  2020-01-27 00:00:00      2.18      15     980     900
    ##  6      386 January  2020  2020-01-27 00:00:00      3.24      15     750     640
    ##  7      387 January  2020  2020-01-27 00:00:00      3.07      15     500     440
    ##  8      388 January  2020  2020-01-30 00:00:00      2.8       15    3200    2400
    ##  9      389 January  2020  2020-01-30 00:00:00      2.8       15    3800    2700
    ## 10      390 February 2020  2020-02-10 00:00:00      1.9       15    1850     980
    ## # … with 62 more rows, 7 more variables: cigarette_butts <dbl>,
    ## #   glass_bottles <dbl>, grocery_bags <dbl>, chip_bags <dbl>,
    ## #   sports_balls <int>, homes_powered <dbl>, trash_wheel_type <chr>, and
    ## #   abbreviated variable names ¹​weight_tons, ²​volume_cubic_yards,
    ## #   ³​plastic_bottles, ⁴​polystyrene

``` r
  sum(pull(mr_trash_wheel_df, sports_balls), na.rm = TRUE)
```

    ## [1] 6869

Total weight of trash collected by professor trash wheel: 190.12 tons
Total number of sports balls collected by mr. trash wheel in 2020: 6869
sports balls

# Problem 3
