Project 1 Vignette: Reading In and Exploring Data via an API
================
Kelly Baker
9/10/2020

  - [Introduction](#introduction)
      - [Required Packages](#required-packages)
  - [API Function Calls](#api-function-calls)
      - [Return `franchise` data set](#return-franchise-data-set)
      - [Return `franchise` data by Team
        Name](#return-franchise-data-by-team-name)
      - [Return `franchise` data by Team
        ID](#return-franchise-data-by-team-id)
      - [Return `franchise team totals`
        dataset](#return-franchise-team-totals-dataset)
      - [Return `franchise team totals` by Team
        Name](#return-franchise-team-totals-by-team-name)
      - [Return `franchise team totals` by Franchise
        ID](#return-franchise-team-totals-by-franchise-id)
      - [Return `franchise season records` by Franchise
        ID](#return-franchise-season-records-by-franchise-id)
      - [Return `goalie records` by Franchise
        ID](#return-goalie-records-by-franchise-id)
      - [Return `skater records` by Franchise
        ID](#return-skater-records-by-franchise-id)

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

# API Function Calls

<br>

## Return `franchise` data set

``` r
fetchFran <- function(tab) {
  URL <- "https://records.nhl.com/site/api/franchise"
  franGET <- GET(URL)
  franGET_text <- content(franGET, "text")
  franGET_json <- fromJSON(franGET_text, flatten=TRUE)
  franGET_df <- as.data.frame(franGET_json)
  invisible(franGET_df)
  return(franGET_df)
}

fetchFran()
```

    ##    data.id data.firstSeasonId data.lastSeasonId data.mostRecentTeamId
    ## 1        1           19171918                NA                     8
    ## 2        2           19171918          19171918                    41
    ## 3        3           19171918          19341935                    45
    ## 4        4           19191920          19241925                    37
    ## 5        5           19171918                NA                    10
    ## 6        6           19241925                NA                     6
    ## 7        7           19241925          19371938                    43
    ## 8        8           19251926          19411942                    51
    ## 9        9           19251926          19301931                    39
    ## 10      10           19261927                NA                     3
    ## 11      11           19261927                NA                    16
    ## 12      12           19261927                NA                    17
    ## 13      13           19671968          19771978                    49
    ## 14      14           19671968                NA                    26
    ## 15      15           19671968                NA                    25
    ## 16      16           19671968                NA                     4
    ## 17      17           19671968                NA                     5
    ## 18      18           19671968                NA                    19
    ## 19      19           19701971                NA                     7
    ## 20      20           19701971                NA                    23
    ## 21      21           19721973                NA                    20
    ## 22      22           19721973                NA                     2
    ## 23      23           19741975                NA                     1
    ## 24      24           19741975                NA                    15
    ## 25      25           19791980                NA                    22
    ## 26      26           19791980                NA                    12
    ## 27      27           19791980                NA                    21
    ## 28      28           19791980                NA                    53
    ## 29      29           19911992                NA                    28
    ## 30      30           19921993                NA                     9
    ## 31      31           19921993                NA                    14
    ## 32      32           19931994                NA                    24
    ## 33      33           19931994                NA                    13
    ## 34      34           19981999                NA                    18
    ## 35      35           19992000                NA                    52
    ## 36      36           20002001                NA                    29
    ## 37      37           20002001                NA                    30
    ## 38      38           20172018                NA                    54
    ##    data.teamCommonName data.teamPlaceName total
    ## 1            Canadiens           Montréal    38
    ## 2            Wanderers           Montreal    38
    ## 3               Eagles          St. Louis    38
    ## 4               Tigers           Hamilton    38
    ## 5          Maple Leafs            Toronto    38
    ## 6               Bruins             Boston    38
    ## 7              Maroons           Montreal    38
    ## 8            Americans           Brooklyn    38
    ## 9              Quakers       Philadelphia    38
    ## 10             Rangers           New York    38
    ## 11          Blackhawks            Chicago    38
    ## 12           Red Wings            Detroit    38
    ## 13              Barons          Cleveland    38
    ## 14               Kings        Los Angeles    38
    ## 15               Stars             Dallas    38
    ## 16              Flyers       Philadelphia    38
    ## 17            Penguins         Pittsburgh    38
    ## 18               Blues          St. Louis    38
    ## 19              Sabres            Buffalo    38
    ## 20             Canucks          Vancouver    38
    ## 21              Flames            Calgary    38
    ## 22           Islanders           New York    38
    ## 23              Devils         New Jersey    38
    ## 24            Capitals         Washington    38
    ## 25              Oilers           Edmonton    38
    ## 26          Hurricanes           Carolina    38
    ## 27           Avalanche           Colorado    38
    ## 28             Coyotes            Arizona    38
    ## 29              Sharks           San Jose    38
    ## 30            Senators             Ottawa    38
    ## 31           Lightning          Tampa Bay    38
    ## 32               Ducks            Anaheim    38
    ## 33            Panthers            Florida    38
    ## 34           Predators          Nashville    38
    ## 35                Jets           Winnipeg    38
    ## 36        Blue Jackets           Columbus    38
    ## 37                Wild          Minnesota    38
    ## 38      Golden Knights              Vegas    38

## Return `franchise` data by Team Name

``` r
fetchFranName <- function(tab, name) {
  URL <- "https://records.nhl.com/site/api/franchise"
  franGET <- GET(URL)
  franGET_text <- content(franGET, "text")
  franGET_json <- fromJSON(franGET_text, flatten=TRUE)
  franGET_df <- as.data.frame(franGET_json)
  invisible(franGET_df)
  return(franGET_df %>% filter(data.teamCommonName==name))
}

fetchFranName(name="Tigers")
```

    ##   data.id data.firstSeasonId data.lastSeasonId data.mostRecentTeamId
    ## 1       4           19191920          19241925                    37
    ##   data.teamCommonName data.teamPlaceName total
    ## 1              Tigers           Hamilton    38

## Return `franchise` data by Team ID

``` r
fetchFranID <- function(tab, id) {
  URL <- "https://records.nhl.com/site/api/franchise"
  franGET <- GET(URL)
  franGET_text <- content(franGET, "text")
  franGET_json <- fromJSON(franGET_text, flatten=TRUE)
  franGET_df <- as.data.frame(franGET_json)
  invisible(franGET_df)
  return(franGET_df %>% filter(data.id==id))
}

fetchFranID(id=1)
```

    ##   data.id data.firstSeasonId data.lastSeasonId data.mostRecentTeamId
    ## 1       1           19171918                NA                     8
    ##   data.teamCommonName data.teamPlaceName total
    ## 1           Canadiens           Montréal    38

## Return `franchise team totals` dataset

``` r
fetchFranTotal <- function(tab, ...) {
  URL <- "https://records.nhl.com/site/api/franchise-team-totals"
  franGET <- GET(URL)
  franGET_text <- content(franGET, "text")
  franGET_json <- fromJSON(franGET_text, flatten=TRUE)
  franGET_df <- as.data.frame(franGET_json)
  invisible(franGET_df)
  return(franGET_df)
}

head(fetchFranTotal(n))
```

    ## No encoding supplied: defaulting to UTF-8.

    ##   data.id data.activeFranchise data.firstSeasonId data.franchiseId
    ## 1       1                    1           19821983               23
    ## 2       2                    1           19821983               23
    ## 3       3                    1           19721973               22
    ## 4       4                    1           19721973               22
    ## 5       5                    1           19261927               10
    ## 6       6                    1           19261927               10
    ##   data.gameTypeId data.gamesPlayed data.goalsAgainst data.goalsFor
    ## 1               2             2937              8708          8647
    ## 2               3              257               634           697
    ## 3               2             3732             11779         11889
    ## 4               3              292               854           932
    ## 5               2             6504             19863         19864
    ## 6               3              518              1447          1404
    ##   data.homeLosses data.homeOvertimeLosses data.homeTies data.homeWins
    ## 1             507                      82            96           783
    ## 2              53                       0            NA            74
    ## 3             674                      81           170           942
    ## 4              49                       1            NA            90
    ## 5            1132                      73           448          1600
    ## 6             104                       0             1           137
    ##   data.lastSeasonId data.losses data.overtimeLosses data.penaltyMinutes
    ## 1                NA        1181                 162               44397
    ## 2                NA         120                   0                4266
    ## 3                NA        1570                 159               57422
    ## 4                NA         132                   0                5548
    ## 5                NA        2693                 147               85564
    ## 6                NA         266                   0                8181
    ##   data.pointPctg data.points data.roadLosses data.roadOvertimeLosses
    ## 1         0.5330        3131             674                      80
    ## 2         0.0039           2              67                       0
    ## 3         0.5115        3818             896                      78
    ## 4         0.0137           8              83                       0
    ## 5         0.5125        6667            1561                      74
    ## 6         0.0000           0             162                       0
    ##   data.roadTies data.roadWins data.shootoutLosses data.shootoutWins
    ## 1           123           592                  79                78
    ## 2            NA            63                   0                 0
    ## 3           177           714                  67                82
    ## 4            NA            70                   0                 0
    ## 5           360          1256                  66                78
    ## 6             7           107                   0                 0
    ##   data.shutouts data.teamId      data.teamName data.ties data.triCode data.wins
    ## 1           193           1  New Jersey Devils       219          NJD      1375
    ## 2            25           1  New Jersey Devils        NA          NJD       137
    ## 3           167           2 New York Islanders       347          NYI      1656
    ## 4            12           2 New York Islanders        NA          NYI       160
    ## 5           403           3   New York Rangers       808          NYR      2856
    ## 6            44           3   New York Rangers         8          NYR       244
    ##   total
    ## 1   105
    ## 2   105
    ## 3   105
    ## 4   105
    ## 5   105
    ## 6   105

## Return `franchise team totals` by Team Name

``` r
fetchFranNameTotal <- function(tab, name) {
  URL <- "https://records.nhl.com/site/api/franchise-team-totals"
  franGET <- GET(URL)
  franGET_text <- content(franGET, "text")
  franGET_json <- fromJSON(franGET_text, flatten=TRUE)
  franGET_df <- as.data.frame(franGET_json)
  invisible(franGET_df)
  return(franGET_df %>% filter(data.teamName==name))
}

fetchFranNameTotal(name="Boston Bruins")
```

    ## No encoding supplied: defaulting to UTF-8.

    ##   data.id data.activeFranchise data.firstSeasonId data.franchiseId
    ## 1      11                    1           19241925                6
    ## 2      12                    1           19241925                6
    ##   data.gameTypeId data.gamesPlayed data.goalsAgainst data.goalsFor
    ## 1               2             6570             19001         20944
    ## 2               3              664              1875          1923
    ##   data.homeLosses data.homeOvertimeLosses data.homeTies data.homeWins
    ## 1             953                      89           376          1867
    ## 2             149                       2             3           191
    ##   data.lastSeasonId data.losses data.overtimeLosses data.penaltyMinutes
    ## 1                NA        2387                 184               88037
    ## 2                NA         332                   0               10505
    ##   data.pointPctg data.points data.roadLosses data.roadOvertimeLosses
    ## 1         0.5625        7391            1434                      95
    ## 2         0.0301          40             183                       0
    ##   data.roadTies data.roadWins data.shootoutLosses data.shootoutWins
    ## 1           415          1341                  80                64
    ## 2             3           135                   0                 0
    ##   data.shutouts data.teamId data.teamName data.ties data.triCode data.wins
    ## 1           500           6 Boston Bruins       791          BOS      3208
    ## 2            49           6 Boston Bruins         6          BOS       326
    ##   total
    ## 1   105
    ## 2   105

## Return `franchise team totals` by Franchise ID

``` r
fetchFranIDTotal <- function(tab, franID) {
  URL <- "https://records.nhl.com/site/api/franchise-team-totals"
  franGET <- GET(URL)
  franGET_text <- content(franGET, "text")
  franGET_json <- fromJSON(franGET_text, flatten=TRUE)
  franGET_df <- as.data.frame(franGET_json)
  invisible(franGET_df)
  return(franGET_df %>% filter(data.franchiseId==franID))
}

fetchFranIDTotal(franID=8)
```

    ## No encoding supplied: defaulting to UTF-8.

    ##   data.id data.activeFranchise data.firstSeasonId data.franchiseId
    ## 1      83                    0           19251926                8
    ## 2      84                    0           19251926                8
    ## 3      94                    0           19411942                8
    ##   data.gameTypeId data.gamesPlayed data.goalsAgainst data.goalsFor
    ## 1               2              736              2007          1510
    ## 2               3               18                42            32
    ## 3               2               48               175           133
    ##   data.homeLosses data.homeOvertimeLosses data.homeTies data.homeWins
    ## 1             154                      NA            67           147
    ## 2               3                      NA             1             4
    ## 3              12                      NA             2            10
    ##   data.lastSeasonId data.losses data.overtimeLosses data.penaltyMinutes
    ## 1          19401941         373                  NA                6348
    ## 2          19401941          11                  NA                 155
    ## 3          19411942          29                  NA                 425
    ##   data.pointPctg data.points data.roadLosses data.roadOvertimeLosses
    ## 1         0.4090         602             219                      NA
    ## 2         0.0000           0               8                      NA
    ## 3         0.3646          35              17                      NA
    ##   data.roadTies data.roadWins data.shootoutLosses data.shootoutWins
    ## 1            57            92                   0                 0
    ## 2             0             2                   0                 0
    ## 3             1             6                   0                 0
    ##   data.shutouts data.teamId      data.teamName data.ties data.triCode data.wins
    ## 1            84          44 New York Americans       124          NYA       239
    ## 2             3          44 New York Americans         1          NYA         6
    ## 3             1          51 Brooklyn Americans         3          BRK        16
    ##   total
    ## 1   105
    ## 2   105
    ## 3   105

## Return `franchise season records` by Franchise ID

``` r
fetchSeasonRecords <- function(ID, ...) {
  URL <- "https://records.nhl.com/site/api/franchise-season-records?cayenneExp=franchiseId="
  ID <- ID
  fullURL <- paste0(URL,ID)
  franGET <- GET(fullURL)
  franGET_text <- content(franGET, "text")
  franGET_json <- fromJSON(franGET_text, flatten=TRUE)
  return(franGET_json)
}

fetchSeasonRecords(ID=6)
```

    ## No encoding supplied: defaulting to UTF-8.

    ## $data
    ##   id fewestGoals fewestGoalsAgainst fewestGoalsAgainstSeasons
    ## 1  6         147                172              1952-53 (70)
    ##   fewestGoalsSeasons fewestLosses fewestLossesSeasons fewestPoints
    ## 1       1955-56 (70)           13        1971-72 (78)           38
    ##   fewestPointsSeasons fewestTies fewestTiesSeasons fewestWins fewestWinsSeasons
    ## 1        1961-62 (70)          5      1972-73 (78)         14      1962-63 (70)
    ##   franchiseId franchiseName homeLossStreak       homeLossStreakDates
    ## 1           6 Boston Bruins             11 Dec 08 1924 - Feb 17 1925
    ##   homePointStreak      homePointStreakDates homeWinStreak
    ## 1              27 Nov 22 1970 - Mar 20 1971            20
    ##          homeWinStreakDates homeWinlessStreak    homeWinlessStreakDates
    ## 1 Dec 03 1929 - Mar 18 1930                11 Dec 08 1924 - Feb 17 1925
    ##   lossStreak           lossStreakDates mostGameGoals
    ## 1         11 Dec 03 1924 - Jan 05 1925            14
    ##             mostGameGoalsDates mostGoals mostGoalsAgainst
    ## 1 Jan 21 1945 - NYR 3 @ BOS 14       399              306
    ##   mostGoalsAgainstSeasons mostGoalsSeasons mostLosses
    ## 1            1961-62 (70)     1970-71 (78)         47
    ##            mostLossesSeasons mostPenaltyMinutes mostPenaltyMinutesSeasons
    ## 1 1961-62 (70), 1996-97 (82)               2443              1987-88 (80)
    ##   mostPoints mostPointsSeasons mostShutouts mostShutoutsSeasons mostTies
    ## 1        121      1970-71 (78)           15        1927-28 (44)       21
    ##   mostTiesSeasons mostWins mostWinsSeasons pointStreak
    ## 1    1954-55 (70)       57    1970-71 (78)          23
    ##            pointStreakDates roadLossStreak       roadLossStreakDates
    ## 1 Dec 22 1940 - Feb 23 1941             14 Dec 27 1964 - Feb 21 1965
    ##   roadPointStreak      roadPointStreakDates roadWinStreak
    ## 1              16 Jan 11 2014 - Mar 30 2014             9
    ##          roadWinStreakDates roadWinlessStreak
    ## 1 Mar 02 2014 - Mar 30 2014                14
    ##                                                            roadWinlessStreakDates
    ## 1 Oct 12 1963 - Dec 14 1963, Dec 27 1964 - Feb 21 1965, Nov 09 1966 - Jan 07 1967
    ##   winStreak            winStreakDates winlessStreak        winlessStreakDates
    ## 1        14 Dec 03 1929 - Jan 09 1930             5 Dec 05 2019 - Dec 12 2019
    ## 
    ## $total
    ## [1] 1

## Return `goalie records` by Franchise ID

``` r
fetchGoalieRecords <- function(ID, ...) { 
  URL <- "https://records.nhl.com/site/api/franchise-goalie-records?cayenneExp=franchiseId="
  ID <- ID
  fullURL <- paste0(URL,ID)
  franGET <- GET(fullURL)
  franGET_text <- content(franGET, "text")
  franGET_json <- fromJSON(franGET_text, flatten=TRUE)
  return(franGET_json)
}

fetchGoalieRecords(ID=12)
```

    ## No encoding supplied: defaulting to UTF-8.

    ## $data
    ##      id activePlayer firstName franchiseId     franchiseName gameTypeId
    ## 1   254        FALSE     Terry          12 Detroit Red Wings          2
    ## 2   267        FALSE     Chris          12 Detroit Red Wings          2
    ## 3   319         TRUE     Jimmy          12 Detroit Red Wings          2
    ## 4   350        FALSE     Allan          12 Detroit Red Wings          2
    ## 5   377        FALSE     Alain          12 Detroit Red Wings          2
    ## 6   386        FALSE       Joe          12 Detroit Red Wings          2
    ## 7   402        FALSE    Darren          12 Detroit Red Wings          2
    ## 8   404        FALSE       Bob          12 Detroit Red Wings          2
    ## 9   428        FALSE    Gilles          12 Detroit Red Wings          2
    ## 10  443        FALSE      Glen          12 Detroit Red Wings          2
    ## 11  448        FALSE   Dominik          12 Detroit Red Wings          2
    ## 12  464        FALSE     Peter          12 Detroit Red Wings          2
    ## 13  475        FALSE    Curtis          12 Detroit Red Wings          2
    ## 14  492        FALSE       Ron          12 Detroit Red Wings          2
    ## 15  505        FALSE     Peter          12 Detroit Red Wings          2
    ## 16  521        FALSE     Eddie          12 Detroit Red Wings          2
    ## 17  524        FALSE      Greg          12 Detroit Red Wings          2
    ## 18  536        FALSE      Hank          12 Detroit Red Wings          2
    ## 19  544        FALSE    Claude          12 Detroit Red Wings          2
    ## 20  558        FALSE      Alec          12 Detroit Red Wings          2
    ## 21  561        FALSE     Abbie          12 Detroit Red Wings          2
    ## 22  562        FALSE     Roger          12 Detroit Red Wings          2
    ## 23  568        FALSE      Wilf          12 Detroit Red Wings          2
    ## 24  570        FALSE     Denis          12 Detroit Red Wings          2
    ## 25  591        FALSE        Ed          12 Detroit Red Wings          2
    ## 26  598        FALSE     Glenn          12 Detroit Red Wings          2
    ## 27  605        FALSE       Hap          12 Detroit Red Wings          2
    ## 28  624        FALSE     Alfie          12 Detroit Red Wings          2
    ## 29  633        FALSE John Ross          12 Detroit Red Wings          2
    ## 30  645        FALSE    Normie          12 Detroit Red Wings          2
    ## 31  647        FALSE      Tiny          12 Detroit Red Wings          2
    ## 32  668        FALSE      Bill          12 Detroit Red Wings          2
    ## 33  683        FALSE   Vincent          12 Detroit Red Wings          2
    ## 34  694        FALSE       Bob          12 Detroit Red Wings          2
    ## 35  701        FALSE        Al          12 Detroit Red Wings          2
    ## 36  739        FALSE     Rogie          12 Detroit Red Wings          2
    ## 37  745        FALSE      Mike          12 Detroit Red Wings          2
    ## 38  763        FALSE       Ken          12 Detroit Red Wings          2
    ## 39  843        FALSE      Marc          12 Detroit Red Wings          2
    ## 40  856        FALSE     Manny          12 Detroit Red Wings          2
    ## 41 1011        FALSE        Ty          12 Detroit Red Wings          2
    ## 42 1039        FALSE      Joey          12 Detroit Red Wings          2
    ## 43 1148        FALSE     Jonas          12 Detroit Red Wings          2
    ## 44 1233        FALSE       Roy          12 Detroit Red Wings          2
    ## 45 1236        FALSE     Harry          12 Detroit Red Wings          2
    ## 46 1238        FALSE    Johnny          12 Detroit Red Wings          2
    ## 47 1263         TRUE  Jonathan          12 Detroit Red Wings          2
    ## 48 1294         TRUE    Calvin          12 Detroit Red Wings          2
    ##    gamesPlayed   lastName losses
    ## 1          734    Sawchuk    245
    ## 2          565     Osgood    149
    ## 3          543     Howard    196
    ## 4            4     Bester      3
    ## 5            3   Chevrier      2
    ## 6           29      Daley     10
    ## 7            3      Eliot      0
    ## 8           13    Essensa      7
    ## 9           95    Gilbert     48
    ## 10         186     Hanlon     71
    ## 11         176      Hasek     39
    ## 12           3        Ing      2
    ## 13          92     Joseph     29
    ## 14          32        Low     12
    ## 15           4    McDuffe      3
    ## 16          49        Mio     21
    ## 17          10     Millen      2
    ## 18          99     Bassen     37
    ## 19           1    Bourque      1
    ## 20          48    Connell     20
    ## 21           2        Cox      0
    ## 22         310    Crozier    118
    ## 23          29       Cude      6
    ## 24          25    DeJordy     12
    ## 25          71   Giacomin     37
    ## 26         148       Hall     45
    ## 27          85     Holmes     45
    ## 28           1      Moore      1
    ## 29          89      Roach     34
    ## 30         178      Smith     71
    ## 31          85   Thompson     41
    ## 32           4    Ranford      0
    ## 33          32   Riendeau      8
    ## 34          41      Sauve     25
    ## 35          43      Smith     20
    ## 36         109     Vachon     57
    ## 37          95     Vernon     24
    ## 38          29    Wregget     10
    ## 39           2    Lamothe      0
    ## 40         180     Legace     34
    ## 41          55    Conklin     17
    ## 42          37  MacDonald     15
    ## 43          41 Gustavsson     10
    ## 44         221    Edwards     82
    ## 45         324     Lumley    105
    ## 46         152     Mowers     61
    ## 47          81    Bernier     40
    ## 48           3    Pickard      2
    ##                                         mostGoalsAgainstDates
    ## 1                                                  1959-03-07
    ## 2                          2009-03-07, 2008-11-11, 1997-02-06
    ## 3                                                  2015-03-14
    ## 4                                                  1991-03-09
    ## 5                                      1991-01-30, 1990-10-20
    ## 6                                                  1972-02-26
    ## 7                                                  1988-01-23
    ## 8                                                  1994-03-23
    ## 9                          1981-11-23, 1981-10-24, 1980-10-11
    ## 10                                     1989-10-05, 1988-02-23
    ## 11                                                 2007-01-04
    ## 12                                                 1993-10-09
    ## 13                                                 2002-11-29
    ## 14                                                 1978-02-28
    ## 15                                                 1976-01-22
    ## 16                                                 1983-10-12
    ## 17 1992-03-17, 1992-03-03, 1992-02-07, 1992-02-03, 1992-01-03
    ## 18                                                 1962-02-25
    ## 19                                                 1940-02-15
    ## 20                                                 1932-01-12
    ## 21                                                 1933-12-17
    ## 22                                                 1964-12-05
    ## 23                                                 1934-03-03
    ## 24                                     1972-11-29, 1972-11-09
    ## 25                                                 1975-12-03
    ## 26                                                 1955-12-04
    ## 27                                                 1926-12-14
    ## 28                                                 1940-01-09
    ## 29                                                 1933-12-30
    ## 30                                                 1938-03-17
    ## 31                                                 1940-02-13
    ## 32                                                 1999-04-02
    ## 33                                                 1992-12-05
    ## 34                                                 1981-12-26
    ## 35                                     1972-01-16, 1971-10-16
    ## 36                                     1979-03-28, 1978-11-09
    ## 37                                     1996-11-02, 1995-04-16
    ## 38                                                 2000-02-20
    ## 39                                                 2004-04-01
    ## 40                                                 2002-04-06
    ## 41                                                 2011-10-22
    ## 42                                                 2011-03-30
    ## 43                         2014-04-05, 2014-01-28, 2013-03-13
    ## 44                                                 1971-03-16
    ## 45                                                 1946-03-17
    ## 46                                                 1942-01-25
    ## 47                                                 2018-10-13
    ## 48                                                 2019-11-29
    ##    mostGoalsAgainstOneGame         mostSavesDates mostSavesOneGame
    ## 1                       10             1959-11-14               50
    ## 2                        7 2010-12-27, 2000-10-22               46
    ## 3                        7             2010-01-07               51
    ## 4                        6             1991-03-09               36
    ## 5                        5             1991-01-30               26
    ## 6                        8             1971-12-25               37
    ## 7                        4             1988-01-23               26
    ## 8                        5             1994-04-10               35
    ## 9                        8             1981-02-21               39
    ## 10                      10             1988-01-03               48
    ## 11                       8             2001-10-26               40
    ## 12                       8             1993-10-09               32
    ## 13                       6             2003-03-16               41
    ## 14                       9 1978-02-25, 1977-12-27               38
    ## 15                       8             1975-10-15               37
    ## 16                       8             1985-10-14               38
    ## 17                       4             1992-02-03               31
    ## 18                       8             1963-02-03               44
    ## 19                       3                   <NA>               NA
    ## 20                       7                   <NA>               NA
    ## 21                       4                   <NA>               NA
    ## 22                      10             1970-04-05               56
    ## 23                       6                   <NA>               NA
    ## 24                       8             1973-01-25               38
    ## 25                       9             1976-04-03               45
    ## 26                       7             1955-10-15               53
    ## 27                       7                   <NA>               NA
    ## 28                       3                   <NA>               NA
    ## 29                       8                   <NA>               NA
    ## 30                       7                   <NA>               NA
    ## 31                      10                   <NA>               NA
    ## 32                       3             1999-04-02               27
    ## 33                       7 1993-02-17, 1992-12-05               29
    ## 34                       8             1982-03-13               39
    ## 35                       9             1972-02-16               45
    ## 36                       8             1979-12-19               49
    ## 37                       6             1995-03-06               38
    ## 38                       6             1999-11-26               39
    ## 39                       2             2004-02-23               29
    ## 40                       6             2001-11-21               38
    ## 41                       7 2008-12-30, 2008-10-25               36
    ## 42                       7             2011-03-28               38
    ## 43                       5             2013-10-17               38
    ## 44                      11             1969-03-16               51
    ## 45                      11                   <NA>               NA
    ## 46                      11                   <NA>               NA
    ## 47                       8             2018-11-10               49
    ## 48                       6             2019-11-29               29
    ##     mostShotsAgainstDates mostShotsAgainstOneGame mostShutoutsOneSeason
    ## 1              1957-11-07                      53                    12
    ## 2  2010-12-27, 2009-01-12                      49                     6
    ## 3              2010-01-07                      52                     6
    ## 4              1991-03-09                      42                     0
    ## 5              1991-01-30                      31                     0
    ## 6              1971-12-25                      42                     0
    ## 7              1988-01-23                      30                     0
    ## 8              1994-04-10                      39                     1
    ## 9              1982-10-17                      44                     0
    ## 10             1988-01-03                      52                     4
    ## 11             2001-10-26                      45                     8
    ## 12             1993-10-09                      40                     0
    ## 13             2003-03-16                      43                     5
    ## 14             1978-02-28                      45                     1
    ## 15             1976-01-22                      42                     0
    ## 16 1985-10-14, 1983-10-12                      44                     1
    ## 17             1992-02-03                      35                     0
    ## 18             1963-02-03                      50                     3
    ## 19                   <NA>                      NA                     0
    ## 20                   <NA>                      NA                     6
    ## 21                   <NA>                      NA                     0
    ## 22             1970-04-05                      65                     6
    ## 23                   <NA>                      NA                     4
    ## 24 1973-01-25, 1972-11-09                      40                     1
    ## 25             1976-04-03                      51                     3
    ## 26             1955-10-15                      54                    12
    ## 27                   <NA>                      NA                    11
    ## 28                   <NA>                      NA                     0
    ## 29                   <NA>                      NA                    10
    ## 30                   <NA>                      NA                     6
    ## 31                   <NA>                      NA                     4
    ## 32             1999-04-02                      30                     0
    ## 33             1992-12-05                      36                     0
    ## 34             1982-03-13                      44                     0
    ## 35             1971-10-16                      51                     4
    ## 36             1979-12-19                      53                     4
    ## 37             1995-03-06                      40                     3
    ## 38             1999-11-26                      41                     0
    ## 39             2004-02-23                      30                     0
    ## 40             2005-11-09                      41                     7
    ## 41             2008-10-25                      41                     6
    ## 42             2011-03-28                      41                     1
    ## 43             2013-11-16                      41                     1
    ## 44             1969-03-16                      56                     6
    ## 45                   <NA>                      NA                     7
    ## 46                   <NA>                      NA                     6
    ## 47             2018-11-10                      52                     1
    ## 48             2019-11-29                      35                     0
    ##           mostShutoutsSeasonIds mostWinsOneSeason  mostWinsSeasonIds
    ## 1  19511952, 19531954, 19541955                44 19501951, 19511952
    ## 2  19961997, 19971998, 19992000                39           19951996
    ## 3                      20112012                37 20092010, 20102011
    ## 4            19901991, 19911992                 0 19901991, 19911992
    ## 5                      19901991                 0           19901991
    ## 6                      19711972                11           19711972
    ## 7                      19871988                 0           19871988
    ## 8                      19931994                 4           19931994
    ## 9  19801981, 19811982, 19821983                11           19801981
    ## 10                     19871988                22           19871988
    ## 11                     20062007                41           20012002
    ## 12                     19931994                 1           19931994
    ## 13                     20022003                34           20022003
    ## 14                     19771978                 9           19771978
    ## 15                     19751976                 0           19751976
    ## 16                     19831984                 7           19831984
    ## 17                     19911992                 3           19911992
    ## 18                     19611962                14           19601961
    ## 19                     19391940                 0           19391940
    ## 20                     19311932                18           19311932
    ## 21                     19331934                 0           19331934
    ## 22           19641965, 19651966                40           19641965
    ## 23                     19331934                15           19331934
    ## 24                     19721973                 8           19721973
    ## 25                     19761977                12           19751976
    ## 26                     19551956                38           19561957
    ## 27                     19271928                19           19271928
    ## 28                     19391940                 0           19391940
    ## 29                     19321933                25           19321933
    ## 30           19351936, 19361937                25           19361937
    ## 31                     19381939                16 19381939, 19391940
    ## 32                     19981999                 3           19981999
    ## 33 19911992, 19921993, 19931994                13           19921993
    ## 34                     19811982                11           19811982
    ## 35                     19711972                18           19711972
    ## 36                     19791980                20           19791980
    ## 37                     19951996                21           19951996
    ## 38                     19992000                14           19992000
    ## 39           20022003, 20032004                 1           20032004
    ## 40                     20052006                37           20052006
    ## 41                     20082009                25           20082009
    ## 42                     20102011                 8           20112012
    ## 43                     20142015                16           20132014
    ## 44                     19721973                27           19721973
    ## 45           19471948, 19491950                34           19481949
    ## 46                     19421943                25           19421943
    ## 47           20182019, 20192020                15           20192020
    ## 48                     20192020                 0           20192020
    ##    overtimeLosses playerId positionCode rookieGamesPlayed rookieShutouts
    ## 1              NA  8450111            G                70             11
    ## 2              29  8458568            G                41              2
    ## 3              70  8470657            G                63              3
    ## 4              NA  8445458            G                NA             NA
    ## 5              NA  8446052            G                NA             NA
    ## 6              NA  8446290            G                NA             NA
    ## 7              NA  8446637            G                NA             NA
    ## 8              NA  8446719            G                NA             NA
    ## 9              NA  8447170            G                NA             NA
    ## 10             NA  8447505            G                NA             NA
    ## 11              9  8447687            G                NA             NA
    ## 12             NA  8448142            G                NA             NA
    ## 13             NA  8448382            G                NA             NA
    ## 14             NA  8448891            G                NA             NA
    ## 15             NA  8449426            G                NA             NA
    ## 16             NA  8449618            G                NA             NA
    ## 17             NA  8449627            G                NA             NA
    ## 18             NA  8449804            G                NA             NA
    ## 19             NA  8449833            G                NA             NA
    ## 20             NA  8449856            G                NA             NA
    ## 21             NA  8449858            G                NA             NA
    ## 22             NA  8449859            G                70              6
    ## 23             NA  8449861            G                NA             NA
    ## 24             NA  8449866            G                NA             NA
    ## 25             NA  8449979            G                NA             NA
    ## 26             NA  8449988            G                70             12
    ## 27             NA  8449997            G                NA             NA
    ## 28             NA  8450045            G                NA             NA
    ## 29             NA  8450103            G                NA             NA
    ## 30             NA  8450115            G                NA             NA
    ## 31             NA  8450127            G                NA             NA
    ## 32             NA  8450651            G                NA             NA
    ## 33             NA  8450834            G                NA             NA
    ## 34             NA  8451143            G                NA             NA
    ## 35             NA  8451474            G                NA             NA
    ## 36             NA  8452122            G                NA             NA
    ## 37             NA  8452217            G                NA             NA
    ## 38             NA  8452535            G                NA             NA
    ## 39             NA  8459028            G                NA             NA
    ## 40              3  8459308            G                NA             NA
    ## 41              3  8469152            G                NA             NA
    ## 42              5  8469732            G                NA             NA
    ## 43              6  8475361            G                NA             NA
    ## 44             NA  8449915            G                41              0
    ## 45             NA  8450019            G                 2              0
    ## 46             NA  8450047            G                48              4
    ## 47              8  8473541            G                NA             NA
    ## 48              0  8475717            G                NA             NA
    ##    rookieWins seasons shutouts ties wins
    ## 1          44      14       85  132  350
    ## 2          23      14       39   46  317
    ## 3          37      14       24    0  246
    ## 4          NA       2        0    0    0
    ## 5          NA       1        0    0    0
    ## 6          NA       1        0    5   11
    ## 7          NA       1        0    1    0
    ## 8          NA       1        1    2    4
    ## 9          NA       3        0   16   21
    ## 10         NA       5        7   26   65
    ## 11         NA       4       20   10  114
    ## 12         NA       1        0    0    1
    ## 13         NA       2        7    9   50
    ## 14         NA       1        1    9    9
    ## 15         NA       1        0    1    0
    ## 16         NA       3        1    5   10
    ## 17         NA       1        0    3    3
    ## 18         NA       6        3   19   34
    ## 19         NA       1        0    0    0
    ## 20         NA       1        6   10   18
    ## 21         NA       1        0    1    0
    ## 22         40       7       19   43  131
    ## 23         NA       1        4    8   15
    ## 24         NA       2        1    3    8
    ## 25         NA       3        5    7   23
    ## 26         30       4       17   29   74
    ## 27         NA       2       17   10   30
    ## 28         NA       1        0    0    0
    ## 29         NA       3       15   14   41
    ## 30         NA       7       17   31   76
    ## 31         NA       2        7   12   32
    ## 32         NA       1        0    1    3
    ## 33         NA       3        0    2   17
    ## 34         NA       1        0    4   11
    ## 35         NA       1        4    4   18
    ## 36         NA       2        4   19   30
    ## 37         NA       3        4   14   53
    ## 38         NA       1        0    2   14
    ## 39         NA       2        0    1    1
    ## 40         NA       6       13   16  112
    ## 41         NA       2        7   NA   30
    ## 42         NA       3        1   NA   14
    ## 43         NA       3        1   NA   21
    ## 44         14       6       12   31   94
    ## 45          0       7       26   56  163
    ## 46         21       4       15   26   65
    ## 47         NA       2        2    0   24
    ## 48         NA       1        0    0    0
    ## 
    ## $total
    ## [1] 48

## Return `skater records` by Franchise ID

``` r
fetchSkaterRecords <- function(ID, ...) {
  URL <- "https://records.nhl.com/site/api/franchise-skater-records?cayenneExp=franchiseId="
  ID <- ID
  fullURL <- paste0(URL,ID)
  franGET <- GET(fullURL)
  franGET_text <- content(franGET, "text")
  franGET_json <- fromJSON(franGET_text, flatten=TRUE)
  return(franGET_json)
}

fetchSkaterRecords(ID=9)
```

    ## No encoding supplied: defaulting to UTF-8.

    ## $data
    ##       id activePlayer assists firstName franchiseId        franchiseName
    ## 1  16893        FALSE      33    Harold           9 Philadelphia Quakers
    ## 2  16894        FALSE      13      Herb           9 Philadelphia Quakers
    ## 3  16905        FALSE      34       Hib           9 Philadelphia Quakers
    ## 4  16918        FALSE       4    Rodger           9 Philadelphia Quakers
    ## 5  16922        FALSE      13       Tex           9 Philadelphia Quakers
    ## 6  17095        FALSE       0    D'arcy           9 Philadelphia Quakers
    ## 7  17142        FALSE      11       Syd           9 Philadelphia Quakers
    ## 8  17147        FALSE      11     Wally           9 Philadelphia Quakers
    ## 9  17164        FALSE       9      John           9 Philadelphia Quakers
    ## 10 17339        FALSE       8        Ty           9 Philadelphia Quakers
    ## 11 17443        FALSE      11     Cliff           9 Philadelphia Quakers
    ## 12 17507        FALSE       0     Louis           9 Philadelphia Quakers
    ## 13 17607        FALSE       0    Edmond           9 Philadelphia Quakers
    ## 14 17658        FALSE       3    Archie           9 Philadelphia Quakers
    ## 15 17770        FALSE       2     Marty           9 Philadelphia Quakers
    ## 16 18073        FALSE       1      Odie           9 Philadelphia Quakers
    ## 17 18105        FALSE       7    Lionel           9 Philadelphia Quakers
    ## 18 18257        FALSE       7     Baldy           9 Philadelphia Quakers
    ## 19 18459        FALSE       0      Stan           9 Philadelphia Quakers
    ## 20 19286        FALSE       4      Gord           9 Philadelphia Quakers
    ## 21 19292        FALSE      14     Frank           9 Philadelphia Quakers
    ## 22 19936        FALSE       0    Albert           9 Philadelphia Quakers
    ## 23 20080        FALSE       0      Bill           9 Philadelphia Quakers
    ## 24 20145        FALSE      15     James           9 Philadelphia Quakers
    ## 25 20516        FALSE       1   Charlie           9 Philadelphia Quakers
    ## 26 20759        FALSE       0      Fred           9 Philadelphia Quakers
    ## 27 20763        FALSE      31     Gerry           9 Philadelphia Quakers
    ## 28 20789        FALSE       4       Ron           9 Philadelphia Quakers
    ## 29 20810        FALSE       0    Mickey           9 Philadelphia Quakers
    ## 30 20875        FALSE       3  Rennison           9 Philadelphia Quakers
    ## 31 20965        FALSE       8      Bert           9 Philadelphia Quakers
    ## 32 20981        FALSE       0     Eddie           9 Philadelphia Quakers
    ## 33 21052        FALSE      12      Duke           9 Philadelphia Quakers
    ## 34 21103        FALSE       0    Mickey           9 Philadelphia Quakers
    ## 35 22090        FALSE       0       Sam           9 Philadelphia Quakers
    ## 36 22809        FALSE       4        Al           9 Philadelphia Quakers
    ## 37 22856        FALSE       0       Alf           9 Philadelphia Quakers
    ## 38 23017        FALSE       0     Jesse           9 Philadelphia Quakers
    ##    gameTypeId gamesPlayed goals     lastName
    ## 1           2         216    60      Darragh
    ## 2           2         216    24        Drury
    ## 3           2         253    88        Milks
    ## 4           2         210    20        Smith
    ## 5           2         194    32        White
    ## 6           2          28     0      Coulson
    ## 7           2          44     9         Howe
    ## 8           2          44     8       Kilrea
    ## 9           2         206    28     McKinnon
    ## 10          2          47     7       Arbour
    ## 11          2          82    10       Barton
    ## 12          2          30     0 Berlinquette
    ## 13          2          11     0     Bouchard
    ## 14          2          29     4       Briden
    ## 15          2          35     1        Burke
    ## 16          2          22     2     Cleghorn
    ## 17          2          41     9     Conacher
    ## 18          2         144    24       Cotton
    ## 19          2          21     0     Crossett
    ## 20          2          34     6       Fraser
    ## 21          2          40     7  Fredrickson
    ## 22          2          44     4       Holway
    ## 23          2          21     1       Hutton
    ## 24          2          88    16       Jarvis
    ## 25          2          44     6     Langlois
    ## 26          2          16     0       Lowrey
    ## 27          2          99    30       Lowrey
    ## 28          2          22     2        Lyons
    ## 29          2          12     1       MacKay
    ## 30          2          37     3      Manners
    ## 31          2          93    10    McCaffrey
    ## 32          2          16     3     McCalmon
    ## 33          2         148    21      McCurry
    ## 34          2          36     3      McGuire
    ## 35          2           6     0   Rothschild
    ## 36          2          43     7      Shields
    ## 37          2           7     0      Skinner
    ## 38          2          58     6       Spring
    ##                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              mostAssistsGameDates
    ## 1                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      1926-01-19, 1929-11-19, 1929-11-23, 1929-12-10, 1930-01-18
    ## 2                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      1926-02-06, 1926-03-04, 1926-11-20, 1927-03-08, 1927-12-31, 1928-03-17, 1929-02-14, 1929-02-21, 1929-03-05, 1929-03-17, 1930-12-09, 1931-02-05, 1931-02-14
    ## 3                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  1927-01-18, 1928-11-20, 1929-12-05, 1931-03-21
    ## 4                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  1925-12-09, 1929-01-03, 1929-03-05, 1930-01-02
    ## 5                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      1926-02-23, 1927-01-01, 1927-02-15, 1927-03-17, 1927-03-26, 1927-12-22, 1928-01-16, 1928-03-24, 1928-11-18, 1928-12-01, 1929-01-01, 1929-01-24, 1930-01-25
    ## 6                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  1930-11-11, 1930-11-15, 1930-11-16, 1930-11-18, 1930-11-23, 1930-11-25, 1930-11-29, 1930-12-02, 1930-12-04, 1930-12-06, 1930-12-09, 1930-12-13, 1930-12-16, 1930-12-20, 1930-12-23, 1930-12-25, 1930-12-28, 1931-01-01, 1931-01-03, 1931-01-04, 1931-01-08, 1931-01-10, 1931-01-13, 1931-01-17, 1931-01-20, 1931-01-22, 1931-01-24, 1931-01-27, 1931-01-29, 1931-01-31, 1931-02-05, 1931-02-10, 1931-02-14, 1931-02-17, 1931-02-22, 1931-02-24, 1931-02-28, 1931-03-03, 1931-03-07, 1931-03-10, 1931-03-12, 1931-03-14, 1931-03-17, 1931-03-21
    ## 7                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      1931-03-12
    ## 8                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          1931-01-03, 1931-03-12
    ## 9                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      1927-12-22, 1929-11-16, 1929-11-23, 1929-12-03, 1930-01-12, 1930-02-12, 1930-02-18, 1930-02-22, 1931-01-01
    ## 10                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             1926-12-04, 1926-12-25, 1927-01-27
    ## 11                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             1930-01-21, 1930-02-04, 1930-03-13, 1930-03-18, 1930-12-06, 1930-12-13, 1931-02-14, 1931-02-17, 1931-03-07, 1931-03-10, 1931-03-12
    ## 12                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 1925-11-26, 1925-11-28, 1925-12-02, 1925-12-05, 1925-12-09, 1925-12-11, 1925-12-16, 1925-12-18, 1925-12-19, 1925-12-23, 1925-12-26, 1925-12-30, 1926-01-01, 1926-01-05, 1926-01-13, 1926-01-15, 1926-01-19, 1926-01-21, 1926-01-23, 1926-01-27, 1926-01-29, 1926-02-02, 1926-02-06, 1926-02-09, 1926-02-13, 1926-02-16, 1926-02-19, 1926-02-20, 1926-02-23, 1926-02-26, 1926-03-02, 1926-03-04, 1926-03-08, 1926-03-09, 1926-03-12, 1926-03-15
    ## 13                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             1929-02-16, 1929-02-17, 1929-02-19, 1929-02-21, 1929-02-23, 1929-02-26, 1929-03-02, 1929-03-05, 1929-03-09, 1929-03-16, 1929-03-17
    ## 14                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             1929-11-19, 1929-12-17, 1930-01-02
    ## 15                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         1928-02-08, 1928-03-10
    ## 16                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     1925-12-09
    ## 17                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     1926-03-04
    ## 18                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     1928-03-17
    ## 19                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 1930-11-11, 1930-11-15, 1930-11-16, 1930-11-18, 1930-11-23, 1930-11-25, 1930-11-29, 1930-12-02, 1930-12-04, 1930-12-06, 1930-12-09, 1930-12-13, 1930-12-16, 1930-12-20, 1930-12-23, 1930-12-25, 1930-12-28, 1931-01-01, 1931-01-03, 1931-01-04, 1931-01-08, 1931-01-10, 1931-01-13, 1931-01-17, 1931-01-20, 1931-01-22, 1931-01-24, 1931-01-27, 1931-01-29, 1931-01-31, 1931-02-05, 1931-02-10, 1931-02-14, 1931-02-17, 1931-02-22, 1931-02-24, 1931-02-28, 1931-03-03, 1931-03-07, 1931-03-10, 1931-03-12, 1931-03-14, 1931-03-17, 1931-03-21
    ## 20                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     1930-02-01
    ## 21                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     1929-11-19
    ## 22                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 1928-11-15, 1928-11-18, 1928-11-20, 1928-11-22, 1928-11-25, 1928-11-27, 1928-11-29, 1928-12-01, 1928-12-06, 1928-12-08, 1928-12-15, 1928-12-18, 1928-12-20, 1928-12-22, 1928-12-27, 1928-12-29, 1929-01-01, 1929-01-03, 1929-01-05, 1929-01-08, 1929-01-10, 1929-01-12, 1929-01-13, 1929-01-19, 1929-01-20, 1929-01-24, 1929-01-26, 1929-02-02, 1929-02-05, 1929-02-09, 1929-02-10, 1929-02-12, 1929-02-14, 1929-02-16, 1929-02-17, 1929-02-19, 1929-02-21, 1929-02-23, 1929-02-26, 1929-03-02, 1929-03-05, 1929-03-09, 1929-03-16, 1929-03-17
    ## 23                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     1930-12-13, 1930-12-16, 1930-12-20, 1930-12-23, 1930-12-25, 1930-12-28, 1931-01-01, 1931-01-03, 1931-01-04, 1931-01-08, 1931-01-10, 1931-01-13, 1931-01-17, 1931-01-22, 1931-01-24, 1931-01-27, 1931-01-29, 1931-01-31, 1931-02-05, 1931-02-10, 1931-02-14
    ## 24                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     1929-12-14
    ## 25                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     1927-02-22
    ## 26                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 1926-01-13, 1926-01-15, 1926-01-21, 1926-01-23, 1926-01-27, 1926-01-29, 1926-02-02, 1926-02-09, 1926-02-13, 1926-02-26, 1926-03-02, 1926-03-04, 1926-03-08, 1926-03-09, 1926-03-12, 1926-03-15
    ## 27                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     1930-01-18
    ## 28                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     1930-12-28
    ## 29                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 1928-11-15, 1928-11-18, 1928-11-20, 1928-11-22, 1928-11-25, 1928-11-27, 1928-11-29, 1928-12-06, 1928-12-08, 1928-12-15, 1928-12-18, 1928-12-20
    ## 30                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             1929-12-15, 1930-03-01, 1930-03-16
    ## 31                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 1928-01-12, 1928-01-19, 1928-02-08, 1928-02-14, 1929-11-19, 1929-12-08, 1929-12-14, 1929-12-26
    ## 32                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 1930-11-11, 1930-11-15, 1930-11-16, 1930-11-18, 1930-11-23, 1930-11-25, 1930-11-29, 1930-12-02, 1930-12-04, 1930-12-06, 1930-12-09, 1930-12-13, 1930-12-16, 1930-12-20, 1930-12-23, 1930-12-25, 1930-12-28, 1931-01-01, 1931-01-03, 1931-01-04, 1931-01-08, 1931-01-10, 1931-01-13, 1931-01-17, 1931-01-20, 1931-01-22, 1931-01-24, 1931-01-27, 1931-01-29, 1931-01-31, 1931-02-05, 1931-02-10, 1931-02-14, 1931-02-17, 1931-02-22, 1931-02-24, 1931-02-28, 1931-03-03, 1931-03-07, 1931-03-10, 1931-03-12, 1931-03-14, 1931-03-17, 1931-03-21
    ## 33                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 1925-11-28, 1926-01-29, 1926-02-09, 1926-02-16, 1926-03-15, 1926-11-20, 1926-11-27, 1927-01-06, 1927-12-24, 1928-01-31, 1928-02-14, 1929-03-02
    ## 34 1926-11-16, 1926-11-20, 1926-11-25, 1926-11-27, 1926-11-30, 1926-12-04, 1926-12-09, 1926-12-11, 1926-12-18, 1926-12-21, 1926-12-23, 1926-12-25, 1926-12-30, 1927-01-01, 1927-01-04, 1927-01-06, 1927-01-08, 1927-01-13, 1927-01-15, 1927-01-18, 1927-01-20, 1927-01-22, 1927-01-25, 1927-01-27, 1927-01-29, 1927-02-06, 1927-02-08, 1927-02-10, 1927-02-12, 1927-02-15, 1927-02-19, 1927-02-22, 1927-02-26, 1927-03-01, 1927-03-03, 1927-03-05, 1927-03-08, 1927-03-10, 1927-03-15, 1927-03-17, 1927-03-20, 1927-03-22, 1927-03-24, 1927-03-26, 1927-11-15, 1927-11-19, 1927-11-22, 1927-11-26, 1927-11-29, 1927-12-01, 1927-12-06, 1927-12-10, 1927-12-17, 1927-12-18, 1927-12-20, 1927-12-22, 1927-12-24, 1927-12-31, 1928-01-03, 1928-01-05, 1928-01-07, 1928-01-12, 1928-01-14, 1928-01-16, 1928-01-19, 1928-01-22, 1928-01-24, 1928-01-28, 1928-01-31, 1928-02-04, 1928-02-08, 1928-02-11, 1928-02-12, 1928-02-14, 1928-02-16, 1928-02-18, 1928-02-21, 1928-02-23, 1928-02-25, 1928-03-01, 1928-03-03, 1928-03-06, 1928-03-10, 1928-03-12, 1928-03-17, 1928-03-18, 1928-03-22, 1928-03-24
    ## 35                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         1927-11-26, 1927-11-29, 1927-12-01, 1927-12-06, 1927-12-10, 1927-12-22
    ## 36                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 1930-11-23, 1931-01-24, 1931-01-29, 1931-01-31
    ## 37                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 1925-11-26, 1925-11-28, 1925-12-02, 1925-12-05, 1925-12-09, 1925-12-11, 1925-12-16, 1925-12-18, 1925-12-19, 1925-12-23, 1925-12-26, 1925-12-30, 1926-01-01, 1926-01-05, 1926-01-13, 1926-01-15, 1926-01-19, 1926-01-21, 1926-01-23, 1926-01-27, 1926-01-29, 1926-02-02, 1926-02-06, 1926-02-09, 1926-02-13, 1926-02-16, 1926-02-19, 1926-02-20, 1926-02-23, 1926-02-26, 1926-03-02, 1926-03-04, 1926-03-08, 1926-03-09, 1926-03-12, 1926-03-15
    ## 38             1925-11-26, 1925-11-28, 1925-12-02, 1925-12-05, 1925-12-09, 1925-12-11, 1925-12-16, 1925-12-18, 1925-12-19, 1925-12-23, 1925-12-26, 1925-12-30, 1926-01-01, 1926-01-05, 1926-01-13, 1926-01-15, 1926-01-19, 1926-01-21, 1926-01-23, 1926-01-27, 1926-01-29, 1926-02-02, 1926-02-06, 1926-02-09, 1926-02-13, 1926-02-16, 1926-02-19, 1926-02-20, 1926-02-23, 1926-02-26, 1926-03-02, 1926-03-04, 1926-03-08, 1926-03-09, 1926-03-12, 1926-03-15, 1929-02-17, 1929-02-19, 1929-02-21, 1929-02-23, 1929-02-26, 1929-03-02, 1929-03-16, 1929-11-16, 1929-11-19, 1929-11-23, 1929-11-26, 1929-11-30, 1929-12-03, 1929-12-05, 1929-12-08, 1929-12-10, 1929-12-14, 1929-12-15, 1929-12-17, 1929-12-21, 1929-12-22, 1929-12-26, 1929-12-29, 1930-01-02, 1930-01-05, 1930-01-09, 1930-01-11, 1930-01-12, 1930-01-14, 1930-01-18, 1930-01-21, 1930-01-25, 1930-01-26, 1930-01-28, 1930-02-01, 1930-02-04, 1930-02-09, 1930-02-12, 1930-02-13, 1930-02-15, 1930-02-18, 1930-02-20, 1930-02-22, 1930-02-25, 1930-03-01, 1930-03-02, 1930-03-08, 1930-03-11, 1930-03-13, 1930-03-16, 1930-03-18
    ##    mostAssistsOneGame mostAssistsOneSeason         mostAssistsSeasonIds
    ## 1                   2                   17                     19291930
    ## 2                   1                    4                     19281929
    ## 3                   2                   11                     19291930
    ## 4                   1                    2                     19281929
    ## 5                   1                    4           19261927, 19281929
    ## 6                   0                    0                     19301931
    ## 7                   3                   11                     19301931
    ## 8                   2                   11                     19301931
    ## 9                   1                    7                     19291930
    ## 10                  2                    8                     19261927
    ## 11                  1                    7                     19301931
    ## 12                  0                    0                     19251926
    ## 13                  0                    0                     19281929
    ## 14                  1                    3                     19291930
    ## 15                  1                    2                     19271928
    ## 16                  1                    1                     19251926
    ## 17                  2                    6                     19251926
    ## 18                  2                    3                     19271928
    ## 19                  0                    0                     19301931
    ## 20                  2                    4                     19291930
    ## 21                  5                    7           19281929, 19291930
    ## 22                  0                    0                     19281929
    ## 23                  0                    0                     19301931
    ## 24                  3                    8                     19291930
    ## 25                  1                    1                     19261927
    ## 26                  0                    0                     19251926
    ## 27                  3                   14           19291930, 19301931
    ## 28                  2                    4                     19301931
    ## 29                  0                    0                     19281929
    ## 30                  1                    3                     19291930
    ## 31                  1                    4           19271928, 19291930
    ## 32                  0                    0                     19301931
    ## 33                  1                    5                     19251926
    ## 34                  0                    0           19261927, 19271928
    ## 35                  0                    0                     19271928
    ## 36                  1                    4                     19301931
    ## 37                  0                    0                     19251926
    ## 38                  0                    0 19251926, 19281929, 19291930
    ##                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                mostGoalsGameDates
    ## 1                                                                                                                                                                                                                                                                                                                                                                                                                                                              1927-03-20, 1928-03-12, 1928-03-17, 1929-11-16, 1930-01-18, 1930-02-01, 1930-02-22
    ## 2                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              1925-12-09, 1928-02-14, 1930-03-18
    ## 3                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      1926-03-04
    ## 4                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      1926-01-15
    ## 5                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      1929-12-14
    ## 6  1930-11-11, 1930-11-15, 1930-11-16, 1930-11-18, 1930-11-23, 1930-11-25, 1930-11-29, 1930-12-02, 1930-12-04, 1930-12-06, 1930-12-09, 1930-12-13, 1930-12-16, 1930-12-20, 1930-12-23, 1930-12-25, 1930-12-28, 1931-01-01, 1931-01-03, 1931-01-04, 1931-01-08, 1931-01-10, 1931-01-13, 1931-01-17, 1931-01-20, 1931-01-22, 1931-01-24, 1931-01-27, 1931-01-29, 1931-01-31, 1931-02-05, 1931-02-10, 1931-02-14, 1931-02-17, 1931-02-22, 1931-02-24, 1931-02-28, 1931-03-03, 1931-03-07, 1931-03-10, 1931-03-12, 1931-03-14, 1931-03-17, 1931-03-21
    ## 7                                                                                                                                                                                                                                                                                                                                                                                                                                      1930-11-23, 1930-11-29, 1931-01-01, 1931-01-03, 1931-01-27, 1931-02-17, 1931-02-28, 1931-03-10, 1931-03-12
    ## 8                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      1931-03-12
    ## 9                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      1929-11-19
    ## 10                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     1927-02-22
    ## 11                                                                                                                                                                                                                                                                                                                                                                                                                         1930-02-18, 1930-02-22, 1930-03-01, 1930-03-08, 1930-11-18, 1930-12-23, 1931-01-27, 1931-03-10, 1931-03-12, 1931-03-21
    ## 12                                                                                                 1925-11-26, 1925-11-28, 1925-12-02, 1925-12-05, 1925-12-09, 1925-12-11, 1925-12-16, 1925-12-18, 1925-12-19, 1925-12-23, 1925-12-26, 1925-12-30, 1926-01-01, 1926-01-05, 1926-01-13, 1926-01-15, 1926-01-19, 1926-01-21, 1926-01-23, 1926-01-27, 1926-01-29, 1926-02-02, 1926-02-06, 1926-02-09, 1926-02-13, 1926-02-16, 1926-02-19, 1926-02-20, 1926-02-23, 1926-02-26, 1926-03-02, 1926-03-04, 1926-03-08, 1926-03-09, 1926-03-12, 1926-03-15
    ## 13                                                                                                                                                                                                                                                                                                                                                                                                             1929-02-16, 1929-02-17, 1929-02-19, 1929-02-21, 1929-02-23, 1929-02-26, 1929-03-02, 1929-03-05, 1929-03-09, 1929-03-16, 1929-03-17
    ## 14                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 1929-12-29, 1930-01-12, 1930-01-14, 1930-01-26
    ## 15                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     1927-12-18
    ## 16                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         1925-12-11, 1926-01-21
    ## 17                                                                                                                                                                                                                                                                                                                                                                                                                                     1925-11-26, 1925-12-02, 1925-12-09, 1925-12-26, 1926-02-06, 1926-02-09, 1926-02-23, 1926-03-02, 1926-03-15
    ## 18                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     1928-02-04
    ## 19 1930-11-11, 1930-11-15, 1930-11-16, 1930-11-18, 1930-11-23, 1930-11-25, 1930-11-29, 1930-12-02, 1930-12-04, 1930-12-06, 1930-12-09, 1930-12-13, 1930-12-16, 1930-12-20, 1930-12-23, 1930-12-25, 1930-12-28, 1931-01-01, 1931-01-03, 1931-01-04, 1931-01-08, 1931-01-10, 1931-01-13, 1931-01-17, 1931-01-20, 1931-01-22, 1931-01-24, 1931-01-27, 1931-01-29, 1931-01-31, 1931-02-05, 1931-02-10, 1931-02-14, 1931-02-17, 1931-02-22, 1931-02-24, 1931-02-28, 1931-03-03, 1931-03-07, 1931-03-10, 1931-03-12, 1931-03-14, 1931-03-17, 1931-03-21
    ## 20                                                                                                                                                                                                                                                                                                                                                                                                                                                                         1930-01-18, 1930-02-09, 1930-02-12, 1930-02-15, 1930-02-18, 1930-03-08
    ## 21                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     1929-11-19
    ## 22                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 1928-11-29, 1929-01-12, 1929-02-21, 1929-03-17
    ## 23                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     1931-01-10
    ## 24                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     1931-03-21
    ## 25                                                                                                                                                                                                                                                                                                                                                                                                                                                                         1926-12-25, 1927-01-15, 1927-01-18, 1927-01-20, 1927-02-26, 1927-03-10
    ## 26                                                                                                                                                                                                                                                                                                                                                 1926-01-13, 1926-01-15, 1926-01-21, 1926-01-23, 1926-01-27, 1926-01-29, 1926-02-02, 1926-02-09, 1926-02-13, 1926-02-26, 1926-03-02, 1926-03-04, 1926-03-08, 1926-03-09, 1926-03-12, 1926-03-15
    ## 27                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     1929-12-05, 1929-12-10, 1929-12-14, 1930-03-16, 1931-01-10
    ## 28                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         1931-01-03, 1931-01-20
    ## 29                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     1928-11-18
    ## 30                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             1929-12-26, 1930-01-09, 1930-03-11
    ## 31                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         1928-03-24, 1929-12-14
    ## 32                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     1930-12-28
    ## 33                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         1926-03-02, 1928-01-16
    ## 34                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     1927-03-26
    ## 35                                                                                                                                                                                                                                                                                                                                                                                                                                                                         1927-11-26, 1927-11-29, 1927-12-01, 1927-12-06, 1927-12-10, 1927-12-22
    ## 36                                                                                                                                                                                                                                                                                                                                                                                                                                                             1931-01-01, 1931-01-13, 1931-01-17, 1931-02-22, 1931-03-07, 1931-03-10, 1931-03-14
    ## 37                                                                                                 1925-11-26, 1925-11-28, 1925-12-02, 1925-12-05, 1925-12-09, 1925-12-11, 1925-12-16, 1925-12-18, 1925-12-19, 1925-12-23, 1925-12-26, 1925-12-30, 1926-01-01, 1926-01-05, 1926-01-13, 1926-01-15, 1926-01-19, 1926-01-21, 1926-01-23, 1926-01-27, 1926-01-29, 1926-02-02, 1926-02-06, 1926-02-09, 1926-02-13, 1926-02-16, 1926-02-19, 1926-02-20, 1926-02-23, 1926-02-26, 1926-03-02, 1926-03-04, 1926-03-08, 1926-03-09, 1926-03-12, 1926-03-15
    ## 38                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     1926-01-19
    ##    mostGoalsOneGame mostGoalsOneSeason mostGoalsSeasonIds
    ## 1                 2                 15           19291930
    ## 2                 2                  6 19251926, 19271928
    ## 3                 4                 18 19271928, 19301931
    ## 4                 3                  9           19251926
    ## 5                 2                  8           19291930
    ## 6                 0                  0           19301931
    ## 7                 1                  9           19301931
    ## 8                 2                  8           19301931
    ## 9                 4                 13           19261927
    ## 10                3                  7           19261927
    ## 11                1                  6           19301931
    ## 12                0                  0           19251926
    ## 13                0                  0           19281929
    ## 14                1                  4           19291930
    ## 15                1                  1           19271928
    ## 16                1                  2           19251926
    ## 17                1                  9           19251926
    ## 18                3                  9           19271928
    ## 19                0                  0           19301931
    ## 20                1                  6           19291930
    ## 21                2                  4           19291930
    ## 22                1                  4           19281929
    ## 23                1                  1           19301931
    ## 24                2                 11           19291930
    ## 25                1                  6           19261927
    ## 26                0                  0           19251926
    ## 27                2                 16           19291930
    ## 28                1                  2           19301931
    ## 29                1                  1           19281929
    ## 30                1                  3           19291930
    ## 31                2                  6           19271928
    ## 32                2                  3           19301931
    ## 33                2                 13           19251926
    ## 34                2                  3           19261927
    ## 35                0                  0           19271928
    ## 36                1                  7           19301931
    ## 37                0                  0           19251926
    ## 38                2                  5           19251926
    ##    mostPenaltyMinutesOneSeason mostPenaltyMinutesSeasonIds
    ## 1                            8          19271928, 19291930
    ## 2                           54                    19261927
    ## 3                           42                    19301931
    ## 4                           69                    19291930
    ## 5                           54                    19271928
    ## 6                          103                    19301931
    ## 7                           22                    19301931
    ## 8                           26                    19301931
    ## 9                           46                    19301931
    ## 10                          10                    19261927
    ## 11                          21                    19301931
    ## 12                           8                    19251926
    ## 13                           2                    19281929
    ## 14                          20                    19291930
    ## 15                          57                    19271928
    ## 16                           4          19251926, 19271928
    ## 17                          66                    19251926
    ## 18                          40                    19271928
    ## 19                          10                    19301931
    ## 20                          37                    19291930
    ## 21                          26                    19281929
    ## 22                          20                    19281929
    ## 23                           2                    19301931
    ## 24                          32          19291930, 19301931
    ## 25                          30                    19261927
    ## 26                           2                    19251926
    ## 27                          50                    19291930
    ## 28                          11                    19301931
    ## 29                           2                    19281929
    ## 30                          14                    19291930
    ## 31                          36                    19281929
    ## 32                           6                    19301931
    ## 33                          62                    19271928
    ## 34                           6                    19261927
    ## 35                           0                    19271928
    ## 36                         102                    19301931
    ## 37                           2                    19251926
    ## 38                          23                    19251926
    ##                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               mostPointsGameDates
    ## 1                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      1930-01-18
    ## 2                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  1925-12-09, 1926-02-06, 1928-02-14, 1930-03-18
    ## 3                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      1926-03-04
    ## 4                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      1926-01-15
    ## 5                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              1927-03-17, 1927-03-26, 1929-12-14
    ## 6  1930-11-11, 1930-11-15, 1930-11-16, 1930-11-18, 1930-11-23, 1930-11-25, 1930-11-29, 1930-12-02, 1930-12-04, 1930-12-06, 1930-12-09, 1930-12-13, 1930-12-16, 1930-12-20, 1930-12-23, 1930-12-25, 1930-12-28, 1931-01-01, 1931-01-03, 1931-01-04, 1931-01-08, 1931-01-10, 1931-01-13, 1931-01-17, 1931-01-20, 1931-01-22, 1931-01-24, 1931-01-27, 1931-01-29, 1931-01-31, 1931-02-05, 1931-02-10, 1931-02-14, 1931-02-17, 1931-02-22, 1931-02-24, 1931-02-28, 1931-03-03, 1931-03-07, 1931-03-10, 1931-03-12, 1931-03-14, 1931-03-17, 1931-03-21
    ## 7                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      1931-03-12
    ## 8                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      1931-03-12
    ## 9                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      1929-11-19
    ## 10                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         1927-01-27, 1927-02-22
    ## 11                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         1931-03-10, 1931-03-12
    ## 12                                                                                                 1925-11-26, 1925-11-28, 1925-12-02, 1925-12-05, 1925-12-09, 1925-12-11, 1925-12-16, 1925-12-18, 1925-12-19, 1925-12-23, 1925-12-26, 1925-12-30, 1926-01-01, 1926-01-05, 1926-01-13, 1926-01-15, 1926-01-19, 1926-01-21, 1926-01-23, 1926-01-27, 1926-01-29, 1926-02-02, 1926-02-06, 1926-02-09, 1926-02-13, 1926-02-16, 1926-02-19, 1926-02-20, 1926-02-23, 1926-02-26, 1926-03-02, 1926-03-04, 1926-03-08, 1926-03-09, 1926-03-12, 1926-03-15
    ## 13                                                                                                                                                                                                                                                                                                                                                                                                             1929-02-16, 1929-02-17, 1929-02-19, 1929-02-21, 1929-02-23, 1929-02-26, 1929-03-02, 1929-03-05, 1929-03-09, 1929-03-16, 1929-03-17
    ## 14                                                                                                                                                                                                                                                                                                                                                                                                                                                             1929-11-19, 1929-12-17, 1929-12-29, 1930-01-02, 1930-01-12, 1930-01-14, 1930-01-26
    ## 15                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             1927-12-18, 1928-02-08, 1928-03-10
    ## 16                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             1925-12-09, 1925-12-11, 1926-01-21
    ## 17                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         1926-03-02, 1926-03-04
    ## 18                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     1928-02-04
    ## 19 1930-11-11, 1930-11-15, 1930-11-16, 1930-11-18, 1930-11-23, 1930-11-25, 1930-11-29, 1930-12-02, 1930-12-04, 1930-12-06, 1930-12-09, 1930-12-13, 1930-12-16, 1930-12-20, 1930-12-23, 1930-12-25, 1930-12-28, 1931-01-01, 1931-01-03, 1931-01-04, 1931-01-08, 1931-01-10, 1931-01-13, 1931-01-17, 1931-01-20, 1931-01-22, 1931-01-24, 1931-01-27, 1931-01-29, 1931-01-31, 1931-02-05, 1931-02-10, 1931-02-14, 1931-02-17, 1931-02-22, 1931-02-24, 1931-02-28, 1931-03-03, 1931-03-07, 1931-03-10, 1931-03-12, 1931-03-14, 1931-03-17, 1931-03-21
    ## 20                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     1930-02-01
    ## 21                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     1929-11-19
    ## 22                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 1928-11-29, 1929-01-12, 1929-02-21, 1929-03-17
    ## 23                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     1931-01-10
    ## 24                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         1929-12-14, 1931-03-12
    ## 25                                                                                                                                                                                                                                                                                                                                                                                                                                                             1926-12-25, 1927-01-15, 1927-01-18, 1927-01-20, 1927-02-22, 1927-02-26, 1927-03-10
    ## 26                                                                                                                                                                                                                                                                                                                                                 1926-01-13, 1926-01-15, 1926-01-21, 1926-01-23, 1926-01-27, 1926-01-29, 1926-02-02, 1926-02-09, 1926-02-13, 1926-02-26, 1926-03-02, 1926-03-04, 1926-03-08, 1926-03-09, 1926-03-12, 1926-03-15
    ## 27                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     1929-12-14
    ## 28                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     1930-12-28
    ## 29                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     1928-11-18
    ## 30                                                                                                                                                                                                                                                                                                                                                                                                                                                                         1929-12-15, 1929-12-26, 1930-01-09, 1930-03-01, 1930-03-11, 1930-03-16
    ## 31                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     1929-12-14
    ## 32                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     1930-12-28
    ## 33                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         1926-03-02, 1928-01-16
    ## 34                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     1927-03-26
    ## 35                                                                                                                                                                                                                                                                                                                                                                                                                                                                         1927-11-26, 1927-11-29, 1927-12-01, 1927-12-06, 1927-12-10, 1927-12-22
    ## 36                                                                                                                                                                                                                                                                                                                                                                                                             1930-11-23, 1931-01-01, 1931-01-13, 1931-01-17, 1931-01-24, 1931-01-29, 1931-01-31, 1931-02-22, 1931-03-07, 1931-03-10, 1931-03-14
    ## 37                                                                                                 1925-11-26, 1925-11-28, 1925-12-02, 1925-12-05, 1925-12-09, 1925-12-11, 1925-12-16, 1925-12-18, 1925-12-19, 1925-12-23, 1925-12-26, 1925-12-30, 1926-01-01, 1926-01-05, 1926-01-13, 1926-01-15, 1926-01-19, 1926-01-21, 1926-01-23, 1926-01-27, 1926-01-29, 1926-02-02, 1926-02-06, 1926-02-09, 1926-02-13, 1926-02-16, 1926-02-19, 1926-02-20, 1926-02-23, 1926-02-26, 1926-03-02, 1926-03-04, 1926-03-08, 1926-03-09, 1926-03-12, 1926-03-15
    ## 38                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     1926-01-19
    ##    mostPointsOneGame mostPointsOneSeason mostPointsSeasonIds penaltyMinutes
    ## 1                  4                  32            19291930             32
    ## 2                  2                   9            19281929            223
    ## 3                  4                  24  19291930, 19301931            167
    ## 4                  3                  10            19251926            180
    ## 5                  2                   9  19261927, 19291930            135
    ## 6                  0                   0            19301931            103
    ## 7                  4                  20            19301931             22
    ## 8                  4                  19            19301931             26
    ## 9                  4                  17            19291930            195
    ## 10                 3                  15            19261927             10
    ## 11                 2                  13            19301931             25
    ## 12                 0                   0            19251926              8
    ## 13                 0                   0            19281929              2
    ## 14                 1                   7            19291930             20
    ## 15                 1                   3            19271928             57
    ## 16                 1                   3            19251926              8
    ## 17                 2                  15            19251926             78
    ## 18                 3                  12            19271928            128
    ## 19                 0                   0            19301931             10
    ## 20                 2                  10            19291930             66
    ## 21                 7                  11            19291930             46
    ## 22                 1                   4            19281929             20
    ## 23                 1                   1            19301931              2
    ## 24                 3                  19            19291930             64
    ## 25                 1                   7            19261927             40
    ## 26                 0                   0            19251926              2
    ## 27                 4                  30            19291930             83
    ## 28                 2                   6            19301931             11
    ## 29                 1                   1            19281929              2
    ## 30                 1                   6            19291930             14
    ## 31                 3                  10            19271928             60
    ## 32                 2                   3            19301931              6
    ## 33                 2                  18            19251926            121
    ## 34                 2                   3            19261927              6
    ## 35                 0                   0            19271928              0
    ## 36                 1                  11            19301931            102
    ## 37                 0                   0            19251926              2
    ## 38                 2                   5            19251926             47
    ##    playerId points positionCode rookiePoints seasons
    ## 1   8445843     93            L           17       6
    ## 2   8445955     37            D            8       6
    ## 3   8447859    122            L           19       6
    ## 4   8449082     24            D           10       6
    ## 5   8449392     45            R            8       6
    ## 6   8445741      0            D            0       1
    ## 7   8446924     20            C           NA       1
    ## 8   8447183     19            R           NA       1
    ## 9   8447795     37            D           NA       5
    ## 10  8444967     15            L           15       2
    ## 11  8445029     21            R            8       2
    ## 12  8445071      0            L           NA       1
    ## 13  8445131      0            L           NA       1
    ## 14  8445162      7            L           NA       1
    ## 15  8445262      3            D            3       1
    ## 16  8445496      3            R           NA       3
    ## 17  8445514     16            D           15       2
    ## 18  8445648     31            L            9       4
    ## 19  8445818      0            D            0       1
    ## 20  8446489     10            D           NA       2
    ## 21  8446493     21            C           NA       2
    ## 22  8446907      4            D           NA       1
    ## 23  8447012      1            D           NA       1
    ## 24  8447050     31            L           19       2
    ## 25  8447323      7            R           NA       2
    ## 26  8447521      0            R           NA       1
    ## 27  8447523     61            L           NA       3
    ## 28  8447534      6            L            6       1
    ## 29  8447545      1            C           NA       1
    ## 30  8447602      6            C            6       2
    ## 31  8447655     18            R           NA       3
    ## 32  8447671      3            R           NA       1
    ## 33  8447741     33            L           18       4
    ## 34  8447779      3            L            3       2
    ## 35  8448462      0            L           NA       1
    ## 36  8449019     11            D           NA       1
    ## 37  8449049      0            R           NA       1
    ## 38  8449141      6            D           NA       3
    ## 
    ## $total
    ## [1] 38
