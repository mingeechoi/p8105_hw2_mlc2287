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

After including a new variable “Trash Wheel Type”, “Mr. Trash Wheel” has
547 observations of 15 variables (547x15) and “Professor Trash Wheel”
has 94 observations of 14 variables (94x14). “Mr. Trash Wheel” and
“Professor Trash Wheel” have all the same variables except “Professor
Trash Wheel” does not have the variable “sport balls”. After combining
them together by a full join, I get 641 observations of 15 variables
(641x15).

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
   filter(mr_trash_wheel_df, year==2020, message=FALSE)
```

    ## # A tibble: 0 × 15
    ## # … with 15 variables: dumpster <dbl>, month <chr>, year <chr>, date <dttm>,
    ## #   weight_tons <dbl>, volume_cubic_yards <dbl>, plastic_bottles <dbl>,
    ## #   polystyrene <dbl>, cigarette_butts <dbl>, glass_bottles <dbl>,
    ## #   grocery_bags <dbl>, chip_bags <dbl>, sports_balls <int>,
    ## #   homes_powered <dbl>, trash_wheel_type <chr>

``` r
  sum(pull(mr_trash_wheel_df, sports_balls), na.rm = TRUE)
```

    ## [1] 6869

Total weight of trash collected by professor trash wheel: 190.12 tons
Total number of sports balls collected by mr. trash wheel in 2020: 6869
sports balls

# Problem 3

Import and clean datasets

``` r
pols_month_df=
  read_csv("./fivethirtyeight_datasets/pols-month.csv")%>%
  janitor::clean_names()%>%
  separate(mon, into=c("year", "month", "day"))%>%
  mutate(month = month.abb[as.numeric(month)])%>%
  mutate(president = case_when(prez_gop==1 ~"gop", 
                               prez_dem==1 ~"dem")
  )%>%
  mutate(year=as.numeric(year))%>%
  select(-prez_dem,-prez_gop, -day)
```

    ## Rows: 822 Columns: 9
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## dbl  (8): prez_gop, gov_gop, sen_gop, rep_gop, prez_dem, gov_dem, sen_dem, r...
    ## date (1): mon
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
#cleaning second data
snp_df=
   read_csv("./fivethirtyeight_datasets/snp.csv")%>%
   separate(date, into=c("month", "day", "year"))%>% 
   mutate(month = month.abb[as.numeric(month)])%>% 
   mutate(yearfix = case_when(
     year > 20 ~ 1900,
     year < 20 ~ 2000 ))%>% 
   mutate(year = as.numeric(year))%>%
   mutate(year = year + yearfix)%>%
   relocate(year, month)%>%
   select(-day, -yearfix)
```

    ## Rows: 787 Columns: 2
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr (1): date
    ## dbl (1): close
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
#cleaning third data
unemployment_df=
  read_csv("./fivethirtyeight_datasets/unemployment.csv")%>%
  pivot_longer(
    Jan:Dec,
    names_to = "month",
    values_to = "unemployment_percentage")%>%
    janitor::clean_names()%>%
    mutate(year=as.numeric(year))
```

    ## Rows: 68 Columns: 13
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## dbl (13): Year, Jan, Feb, Mar, Apr, May, Jun, Jul, Aug, Sep, Oct, Nov, Dec
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

Joining datasets by merging snp into pols and merging unemployment into
the result

``` r
join1_data=
  left_join(pols_month_df, snp_df)
```

    ## Joining, by = c("year", "month")

``` r
join2_data=
   left_join(join1_data, unemployment_df)                     
```

    ## Joining, by = c("year", "month")

The “pols_month” after cleaning up contains 822 observations of 9
variables (822x9). It contains data from years 1947-2015 and contains
the following variables:year, month, gov_gop, sen_gop, rep_gop, gov_dem,
sen_dem, and rep_dem. The “snp” after cleaning up contains 787
observations of 3 variables (787x3). It contains data from years
1950-2015 and contains the following variables: year, month, and close.
The “unemployment” after cleaning up contains 641 observations of 15
variables (641x5). It contains data from years 1948-2015 and contains
the following variables: year, month, and unemployment percentage. The
dimension of the combined dataset is 822 observation of 11 variables
(822x11).
