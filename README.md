
<!-- README.md is generated from README.Rmd. Please edit the README.Rmd file -->

# Lab report \#4 - instructions

Follow the instructions posted at
<https://ds202-at-isu.github.io/labs.html> for the lab assignment. The
work is meant to be finished during the lab time, but you have time
until Monday (after Thanksgiving) to polish things.

All submissions to the github repo will be automatically uploaded for
grading once the due date is passed. Submit a link to your repository on
Canvas (only one submission per team) to signal to the instructors that
you are done with your submission.

# Lab 4: Scraping (into) the Hall of Fame

    ## package 'Lahman' successfully unpacked and MD5 sums checked
    ## 
    ## The downloaded binary packages are in
    ##  C:\Users\gizmo\AppData\Local\Temp\RtmpYrxvAg\downloaded_packages

![](README_files/figure-gfm/unnamed-chunk-1-1.png)<!-- --> \# Hall of
Fame Dataset

## Scraping and Cleaning

``` r
library(tidyverse)
library(rvest)
```

    ## 
    ## Attaching package: 'rvest'

    ## The following object is masked from 'package:readr':
    ## 
    ##     guess_encoding

``` r
library(janitor)
```

    ## 
    ## Attaching package: 'janitor'

    ## The following objects are masked from 'package:stats':
    ## 
    ##     chisq.test, fisher.test

``` r
library(Lahman)

website <- read_html("https://www.baseball-reference.com/awards/hof_2026.shtml")

df <- website %>% html_element("#hof_BBWAA") %>% html_table() 
df <- df %>% row_to_names(1) %>% clean_names %>% select(1:10)
```

    ## Warning: Row 1 does not provide unique names. Consider running clean_names()
    ## after row_to_names().

``` r
df <- df %>% rename("year_on_ballot" = yo_b, "rank" = rk)

df <- df %>% mutate(votes = as.numeric(votes), percent_vote = as.numeric(str_remove(percent_vote, "%")), yo_b = as.numeric(str_remove(year_on_ballot, "st|th|rd|nd")), ho_fm = as.numeric(ho_fm), ho_fs = as.numeric(ho_fs),yrs = as.numeric(yrs), inducted = percent_vote >= 75)

df <- df %>% select(-c(ho_fm, ho_fs, yrs, war, war7, yo_b))

df <- df %>% mutate(inducted = if_else(inducted, "Y", "N"))

write_csv(df, "HallOfFame_2026.csv")
```




    ``` r
    df

    ## # A tibble: 27 × 6
    ##    rank  name            year_on_ballot votes percent_vote inducted
    ##    <chr> <chr>           <chr>          <dbl>        <dbl> <chr>   
    ##  1 1     Carlos Beltrán  4th              358         84.2 Y       
    ##  2 2     Andruw Jones    9th              333         78.4 Y       
    ##  3 3     Chase Utley     3rd              251         59.1 N       
    ##  4 4     Andy Pettitte   8th              206         48.5 N       
    ##  5 5     Félix Hernández 2nd              196         46.1 N       
    ##  6 6     Alex Rodriguez  5th              170         40   N       
    ##  7 7     X-Manny Ramirez 10th             165         38.8 N       
    ##  8 8     Bobby Abreu     7th              131         30.8 N       
    ##  9 9     Jimmy Rollins   5th              108         25.4 N       
    ## 10 10    Cole Hamels     1st              101         23.8 N       
    ## # ℹ 17 more rows

``` r
final_df <- df %>% select(1:5)
```
