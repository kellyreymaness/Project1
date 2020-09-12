Project 1 Vignette: Reading In and Exploring Data via an API
================
Kelly Baker
9/10/2020

  - [Introduction](#introduction)
      - [Required Packages](#required-packages)
  - [API](#api)

# Introduction

<br> This vignette will walk you through how to connect with data
through an API. Specifically, we will look at how to access certain
endpoints (tables) as well as how join returned datasets, create new
variables, and explore the data. <br>

## Required Packages

<br> To get started, we’ll need to ensure we have the tools installed
and loaded into RStudio to read in, manipulate, summarize, and plot the
data. Below is an outline of packages that are required to for this
project: <br>

  - **dplyr**: for data manipulation
  - **DT**: for creating interactive data tables.
  - **ggplot2**: for plotting and graphing
  - **readr**: for reading in data
  - **tibble**: for creating special data frames with improved printing
    properties
  - **tidyr**: for reshaping data
  - **jsonlite**: for parsing API data
  - **httr**: for streaming data from an API <br>

We’ll read in the packages using the library() function:

``` r
library(dplyr)
library(DT)
library(ggplot2)
library(readr)
library(tibble)
library(tidyr)
library(jsonlite)
```

    ## Warning: package 'jsonlite' was built under R version 4.0.2

    ## 
    ## Attaching package: 'jsonlite'

    ## The following object is masked from 'package:purrr':
    ## 
    ##     flatten

``` r
library(httr)
```

    ## Warning: package 'httr' was built under R version 4.0.2

<br>

# API

``` r
baseURL1 <- "https://records.nhl.com/site/api"
endpt1 <- "franchise"
fullURL1 <- paste0(baseURL1, "/", endpt1)

franGET <- GET(fullURL1)
franGET_text <- content(franGET, "text")
```

    ## No encoding supplied: defaulting to UTF-8.

``` r
franGET_json <- fromJSON(franGET_text, flatten=TRUE)
franGET_json
```

    ## $data
    ##    id firstSeasonId lastSeasonId mostRecentTeamId teamCommonName teamPlaceName
    ## 1   1      19171918           NA                8      Canadiens      Montréal
    ## 2   2      19171918     19171918               41      Wanderers      Montreal
    ## 3   3      19171918     19341935               45         Eagles     St. Louis
    ## 4   4      19191920     19241925               37         Tigers      Hamilton
    ## 5   5      19171918           NA               10    Maple Leafs       Toronto
    ## 6   6      19241925           NA                6         Bruins        Boston
    ## 7   7      19241925     19371938               43        Maroons      Montreal
    ## 8   8      19251926     19411942               51      Americans      Brooklyn
    ## 9   9      19251926     19301931               39        Quakers  Philadelphia
    ## 10 10      19261927           NA                3        Rangers      New York
    ## 11 11      19261927           NA               16     Blackhawks       Chicago
    ## 12 12      19261927           NA               17      Red Wings       Detroit
    ## 13 13      19671968     19771978               49         Barons     Cleveland
    ## 14 14      19671968           NA               26          Kings   Los Angeles
    ## 15 15      19671968           NA               25          Stars        Dallas
    ## 16 16      19671968           NA                4         Flyers  Philadelphia
    ## 17 17      19671968           NA                5       Penguins    Pittsburgh
    ## 18 18      19671968           NA               19          Blues     St. Louis
    ## 19 19      19701971           NA                7         Sabres       Buffalo
    ## 20 20      19701971           NA               23        Canucks     Vancouver
    ## 21 21      19721973           NA               20         Flames       Calgary
    ## 22 22      19721973           NA                2      Islanders      New York
    ## 23 23      19741975           NA                1         Devils    New Jersey
    ## 24 24      19741975           NA               15       Capitals    Washington
    ## 25 25      19791980           NA               22         Oilers      Edmonton
    ## 26 26      19791980           NA               12     Hurricanes      Carolina
    ## 27 27      19791980           NA               21      Avalanche      Colorado
    ## 28 28      19791980           NA               53        Coyotes       Arizona
    ## 29 29      19911992           NA               28         Sharks      San Jose
    ## 30 30      19921993           NA                9       Senators        Ottawa
    ## 31 31      19921993           NA               14      Lightning     Tampa Bay
    ## 32 32      19931994           NA               24          Ducks       Anaheim
    ## 33 33      19931994           NA               13       Panthers       Florida
    ## 34 34      19981999           NA               18      Predators     Nashville
    ## 35 35      19992000           NA               52           Jets      Winnipeg
    ## 36 36      20002001           NA               29   Blue Jackets      Columbus
    ## 37 37      20002001           NA               30           Wild     Minnesota
    ## 38 38      20172018           NA               54 Golden Knights         Vegas
    ## 
    ## $total
    ## [1] 38

<br>

``` r
retrieveFRAN <- function(tab) {
  baseURL1 <- "https://records.nhl.com/site/api"
  tab <- "franchise"
  fullURL1 <- paste0(baseURL1, "/", tab)
  franGET <- GET(fullURL1)
  franGET_text <- content(franGET, "text")
  franGET_json <- fromJSON(franGET_text, flatten=TRUE)
  return(franGET_json)
}

retrieveFRAN()
```

    ## No encoding supplied: defaulting to UTF-8.

    ## $data
    ##    id firstSeasonId lastSeasonId mostRecentTeamId teamCommonName teamPlaceName
    ## 1   1      19171918           NA                8      Canadiens      Montréal
    ## 2   2      19171918     19171918               41      Wanderers      Montreal
    ## 3   3      19171918     19341935               45         Eagles     St. Louis
    ## 4   4      19191920     19241925               37         Tigers      Hamilton
    ## 5   5      19171918           NA               10    Maple Leafs       Toronto
    ## 6   6      19241925           NA                6         Bruins        Boston
    ## 7   7      19241925     19371938               43        Maroons      Montreal
    ## 8   8      19251926     19411942               51      Americans      Brooklyn
    ## 9   9      19251926     19301931               39        Quakers  Philadelphia
    ## 10 10      19261927           NA                3        Rangers      New York
    ## 11 11      19261927           NA               16     Blackhawks       Chicago
    ## 12 12      19261927           NA               17      Red Wings       Detroit
    ## 13 13      19671968     19771978               49         Barons     Cleveland
    ## 14 14      19671968           NA               26          Kings   Los Angeles
    ## 15 15      19671968           NA               25          Stars        Dallas
    ## 16 16      19671968           NA                4         Flyers  Philadelphia
    ## 17 17      19671968           NA                5       Penguins    Pittsburgh
    ## 18 18      19671968           NA               19          Blues     St. Louis
    ## 19 19      19701971           NA                7         Sabres       Buffalo
    ## 20 20      19701971           NA               23        Canucks     Vancouver
    ## 21 21      19721973           NA               20         Flames       Calgary
    ## 22 22      19721973           NA                2      Islanders      New York
    ## 23 23      19741975           NA                1         Devils    New Jersey
    ## 24 24      19741975           NA               15       Capitals    Washington
    ## 25 25      19791980           NA               22         Oilers      Edmonton
    ## 26 26      19791980           NA               12     Hurricanes      Carolina
    ## 27 27      19791980           NA               21      Avalanche      Colorado
    ## 28 28      19791980           NA               53        Coyotes       Arizona
    ## 29 29      19911992           NA               28         Sharks      San Jose
    ## 30 30      19921993           NA                9       Senators        Ottawa
    ## 31 31      19921993           NA               14      Lightning     Tampa Bay
    ## 32 32      19931994           NA               24          Ducks       Anaheim
    ## 33 33      19931994           NA               13       Panthers       Florida
    ## 34 34      19981999           NA               18      Predators     Nashville
    ## 35 35      19992000           NA               52           Jets      Winnipeg
    ## 36 36      20002001           NA               29   Blue Jackets      Columbus
    ## 37 37      20002001           NA               30           Wild     Minnesota
    ## 38 38      20172018           NA               54 Golden Knights         Vegas
    ## 
    ## $total
    ## [1] 38
