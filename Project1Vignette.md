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
      - [Return `team stats` data](#return-team-stats-data)
  - [Data Manipulation](#data-manipulation)
      - [Joins](#joins)
      - [Creating New Variables](#creating-new-variables)
  - [Data Exploration & Visualization](#data-exploration-visualization)
      - [Summarizing Numeric Data](#summarizing-numeric-data)
      - [Summarizing Categorical Data](#summarizing-categorical-data)

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
  - **httr**: for streaming data from an API
  - **knitr**: for creating tables with nic printing properties <br>

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

``` r
library(knitr)
```

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
```

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
```

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
```

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
    ## 4               3              293               855           934
    ## 5               2             6504             19863         19864
    ## 6               3              518              1447          1404
    ##   data.homeLosses data.homeOvertimeLosses data.homeTies data.homeWins
    ## 1             507                      82            96           783
    ## 2              53                       0            NA            74
    ## 3             674                      81           170           942
    ## 4              49                       2            NA            90
    ## 5            1132                      73           448          1600
    ## 6             104                       0             1           137
    ##   data.lastSeasonId data.losses data.overtimeLosses data.penaltyMinutes
    ## 1                NA        1181                 162               44397
    ## 2                NA         120                   0                4266
    ## 3                NA        1570                 159               57422
    ## 4                NA         132                   0                5554
    ## 5                NA        2693                 147               85564
    ## 6                NA         266                   0                8181
    ##   data.pointPctg data.points data.roadLosses data.roadOvertimeLosses
    ## 1         0.5330        3131             674                      80
    ## 2         0.0039           2              67                       0
    ## 3         0.5115        3818             896                      78
    ## 4         0.0137           8              83                       2
    ## 5         0.5125        6667            1561                      74
    ## 6         0.0000           0             162                       0
    ##   data.roadTies data.roadWins data.shootoutLosses data.shootoutWins
    ## 1           123           592                  79                78
    ## 2            NA            63                   0                 0
    ## 3           177           714                  67                82
    ## 4            NA            71                   0                 0
    ## 5           360          1256                  66                78
    ## 6             7           107                   0                 0
    ##   data.shutouts data.teamId      data.teamName data.ties data.triCode data.wins
    ## 1           193           1  New Jersey Devils       219          NJD      1375
    ## 2            25           1  New Jersey Devils        NA          NJD       137
    ## 3           167           2 New York Islanders       347          NYI      1656
    ## 4            12           2 New York Islanders        NA          NYI       161
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
```

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
```

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
```

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
```

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
```

## Return `team stats` data

``` r
roster <- GET("https://statsapi.web.nhl.com/api/v1/teams/?expand=team.roster") %>% content("text") %>% fromJSON(flatten=TRUE)


fetchRosterLess <- function(ID=NULL, ...) { GET(paste0("https://statsapi.web.nhl.com/api/v1/teams/", ID, "/roster")) %>% content("text") %>% fromJSON(flatten=TRUE)
}

fetchPersonData <- GET("https://statsapi.web.nhl.com/api/v1/teams/?expand=person.names") %>% content("text") %>% fromJSON(flatten=TRUE)


fetchNextGame <- GET("https://statsapi.web.nhl.com/api/v1/teams/?expand=team.schedule.next") %>% content("text") %>% fromJSON(flatten=TRUE)

fetchLastGame <- GET("https://statsapi.web.nhl.com/api/v1/teams/?expand=?expand=team.schedule.previous") %>% content("text") %>% fromJSON(flatten=TRUE)


fetchSeasonStats <- GET("https://statsapi.web.nhl.com/api/v1/teams/?expand=?expand=team.stats") %>% content("text") %>% fromJSON(flatten=TRUE)

rosterBySeason <- GET("https://statsapi.web.nhl.com/api/v1/teams/?expand=team.roster&season=20142015") %>% content("text") %>% fromJSON(flatten=TRUE)
rosterBySeason[2]
```

    ## $teams
    ##    id                  name             link abbreviation     teamName
    ## 1   1     New Jersey Devils  /api/v1/teams/1          NJD       Devils
    ## 2   2    New York Islanders  /api/v1/teams/2          NYI    Islanders
    ## 3   3      New York Rangers  /api/v1/teams/3          NYR      Rangers
    ## 4   4   Philadelphia Flyers  /api/v1/teams/4          PHI       Flyers
    ## 5   5   Pittsburgh Penguins  /api/v1/teams/5          PIT     Penguins
    ## 6   6         Boston Bruins  /api/v1/teams/6          BOS       Bruins
    ## 7   7        Buffalo Sabres  /api/v1/teams/7          BUF       Sabres
    ## 8   8    Montréal Canadiens  /api/v1/teams/8          MTL    Canadiens
    ## 9   9       Ottawa Senators  /api/v1/teams/9          OTT     Senators
    ## 10 10   Toronto Maple Leafs /api/v1/teams/10          TOR  Maple Leafs
    ## 11 12   Carolina Hurricanes /api/v1/teams/12          CAR   Hurricanes
    ## 12 13      Florida Panthers /api/v1/teams/13          FLA     Panthers
    ## 13 14   Tampa Bay Lightning /api/v1/teams/14          TBL    Lightning
    ## 14 15   Washington Capitals /api/v1/teams/15          WSH     Capitals
    ## 15 16    Chicago Blackhawks /api/v1/teams/16          CHI   Blackhawks
    ## 16 17     Detroit Red Wings /api/v1/teams/17          DET    Red Wings
    ## 17 18   Nashville Predators /api/v1/teams/18          NSH    Predators
    ## 18 19       St. Louis Blues /api/v1/teams/19          STL        Blues
    ## 19 20        Calgary Flames /api/v1/teams/20          CGY       Flames
    ## 20 21    Colorado Avalanche /api/v1/teams/21          COL    Avalanche
    ## 21 22       Edmonton Oilers /api/v1/teams/22          EDM       Oilers
    ## 22 23     Vancouver Canucks /api/v1/teams/23          VAN      Canucks
    ## 23 24         Anaheim Ducks /api/v1/teams/24          ANA        Ducks
    ## 24 25          Dallas Stars /api/v1/teams/25          DAL        Stars
    ## 25 26     Los Angeles Kings /api/v1/teams/26          LAK        Kings
    ## 26 28       San Jose Sharks /api/v1/teams/28          SJS       Sharks
    ## 27 29 Columbus Blue Jackets /api/v1/teams/29          CBJ Blue Jackets
    ## 28 30        Minnesota Wild /api/v1/teams/30          MIN         Wild
    ## 29 52         Winnipeg Jets /api/v1/teams/52          WPG         Jets
    ## 30 53       Arizona Coyotes /api/v1/teams/53          ARI      Coyotes
    ##    locationName firstYearOfPlay    shortName                    officialSiteUrl
    ## 1    New Jersey            1982   New Jersey    http://www.newjerseydevils.com/
    ## 2      New York            1972 NY Islanders   http://www.newyorkislanders.com/
    ## 3      New York            1926   NY Rangers     http://www.newyorkrangers.com/
    ## 4  Philadelphia            1967 Philadelphia http://www.philadelphiaflyers.com/
    ## 5    Pittsburgh            1967   Pittsburgh     http://pittsburghpenguins.com/
    ## 6        Boston            1924       Boston       http://www.bostonbruins.com/
    ## 7       Buffalo            1970      Buffalo             http://www.sabres.com/
    ## 8      Montréal            1917     Montréal          http://www.canadiens.com/
    ## 9        Ottawa            1992       Ottawa     http://www.ottawasenators.com/
    ## 10      Toronto            1926      Toronto         http://www.mapleleafs.com/
    ## 11     Carolina            1997     Carolina http://www.carolinahurricanes.com/
    ## 12      Florida            1993      Florida    http://www.floridapanthers.com/
    ## 13    Tampa Bay            1992    Tampa Bay  http://www.tampabaylightning.com/
    ## 14   Washington            1974   Washington http://www.washingtoncapitals.com/
    ## 15      Chicago            1926      Chicago  http://www.chicagoblackhawks.com/
    ## 16      Detroit            1932      Detroit    http://www.detroitredwings.com/
    ## 17    Nashville            1998    Nashville http://www.nashvillepredators.com/
    ## 18    St. Louis            1967     St Louis       http://www.stlouisblues.com/
    ## 19      Calgary            1980      Calgary      http://www.calgaryflames.com/
    ## 20     Colorado            1995     Colorado  http://www.coloradoavalanche.com/
    ## 21     Edmonton            1979     Edmonton     http://www.edmontonoilers.com/
    ## 22    Vancouver            1970    Vancouver            http://www.canucks.com/
    ## 23      Anaheim            1993      Anaheim       http://www.anaheimducks.com/
    ## 24       Dallas            1993       Dallas        http://www.dallasstars.com/
    ## 25  Los Angeles            1967  Los Angeles            http://www.lakings.com/
    ## 26     San Jose            1991     San Jose           http://www.sjsharks.com/
    ## 27     Columbus            2000     Columbus        http://www.bluejackets.com/
    ## 28    Minnesota            2000    Minnesota               http://www.wild.com/
    ## 29     Winnipeg            2011     Winnipeg           http://winnipegjets.com/
    ## 30      Arizona            2014      Arizona     http://www.arizonacoyotes.com/
    ##    franchiseId active               venue.name          venue.link   venue.city
    ## 1           23   TRUE        Prudential Center /api/v1/venues/null       Newark
    ## 2           22   TRUE          Barclays Center /api/v1/venues/5026     Brooklyn
    ## 3           10   TRUE    Madison Square Garden /api/v1/venues/5054     New York
    ## 4           16   TRUE       Wells Fargo Center /api/v1/venues/5096 Philadelphia
    ## 5           17   TRUE         PPG Paints Arena /api/v1/venues/5034   Pittsburgh
    ## 6            6   TRUE                TD Garden /api/v1/venues/5085       Boston
    ## 7           19   TRUE           KeyBank Center /api/v1/venues/5039      Buffalo
    ## 8            1   TRUE              Bell Centre /api/v1/venues/5028     Montréal
    ## 9           30   TRUE     Canadian Tire Centre /api/v1/venues/5031       Ottawa
    ## 10           5   TRUE         Scotiabank Arena /api/v1/venues/null      Toronto
    ## 11          26   TRUE                PNC Arena /api/v1/venues/5066      Raleigh
    ## 12          33   TRUE              BB&T Center /api/v1/venues/5027      Sunrise
    ## 13          31   TRUE             AMALIE Arena /api/v1/venues/null        Tampa
    ## 14          24   TRUE        Capital One Arena /api/v1/venues/5094   Washington
    ## 15          11   TRUE            United Center /api/v1/venues/5092      Chicago
    ## 16          12   TRUE     Little Caesars Arena /api/v1/venues/5145      Detroit
    ## 17          34   TRUE        Bridgestone Arena /api/v1/venues/5030    Nashville
    ## 18          18   TRUE        Enterprise Center /api/v1/venues/5076    St. Louis
    ## 19          21   TRUE    Scotiabank Saddledome /api/v1/venues/5075      Calgary
    ## 20          27   TRUE             Pepsi Center /api/v1/venues/5064       Denver
    ## 21          25   TRUE             Rogers Place /api/v1/venues/5100     Edmonton
    ## 22          20   TRUE             Rogers Arena /api/v1/venues/5073    Vancouver
    ## 23          32   TRUE             Honda Center /api/v1/venues/5046      Anaheim
    ## 24          15   TRUE American Airlines Center /api/v1/venues/5019       Dallas
    ## 25          14   TRUE           STAPLES Center /api/v1/venues/5081  Los Angeles
    ## 26          29   TRUE   SAP Center at San Jose /api/v1/venues/null     San Jose
    ## 27          36   TRUE         Nationwide Arena /api/v1/venues/5059     Columbus
    ## 28          37   TRUE       Xcel Energy Center /api/v1/venues/5098     St. Paul
    ## 29          35   TRUE           Bell MTS Place /api/v1/venues/5058     Winnipeg
    ## 30          28   TRUE         Gila River Arena /api/v1/venues/5043     Glendale
    ##    venue.id   venue.timeZone.id venue.timeZone.offset venue.timeZone.tz
    ## 1        NA    America/New_York                    -4               EDT
    ## 2      5026    America/New_York                    -4               EDT
    ## 3      5054    America/New_York                    -4               EDT
    ## 4      5096    America/New_York                    -4               EDT
    ## 5      5034    America/New_York                    -4               EDT
    ## 6      5085    America/New_York                    -4               EDT
    ## 7      5039    America/New_York                    -4               EDT
    ## 8      5028    America/Montreal                    -4               EDT
    ## 9      5031    America/New_York                    -4               EDT
    ## 10       NA     America/Toronto                    -4               EDT
    ## 11     5066    America/New_York                    -4               EDT
    ## 12     5027    America/New_York                    -4               EDT
    ## 13       NA    America/New_York                    -4               EDT
    ## 14     5094    America/New_York                    -4               EDT
    ## 15     5092     America/Chicago                    -5               CDT
    ## 16     5145     America/Detroit                    -4               EDT
    ## 17     5030     America/Chicago                    -5               CDT
    ## 18     5076     America/Chicago                    -5               CDT
    ## 19     5075      America/Denver                    -6               MDT
    ## 20     5064      America/Denver                    -6               MDT
    ## 21     5100    America/Edmonton                    -6               MDT
    ## 22     5073   America/Vancouver                    -7               PDT
    ## 23     5046 America/Los_Angeles                    -7               PDT
    ## 24     5019     America/Chicago                    -5               CDT
    ## 25     5081 America/Los_Angeles                    -7               PDT
    ## 26       NA America/Los_Angeles                    -7               PDT
    ## 27     5059    America/New_York                    -4               EDT
    ## 28     5098     America/Chicago                    -5               CDT
    ## 29     5058    America/Winnipeg                    -5               CDT
    ## 30     5043     America/Phoenix                    -7               MST
    ##    division.id division.name division.nameShort        division.link
    ## 1           18  Metropolitan              Metro /api/v1/divisions/18
    ## 2           18  Metropolitan              Metro /api/v1/divisions/18
    ## 3           18  Metropolitan              Metro /api/v1/divisions/18
    ## 4           18  Metropolitan              Metro /api/v1/divisions/18
    ## 5           18  Metropolitan              Metro /api/v1/divisions/18
    ## 6           17      Atlantic                ATL /api/v1/divisions/17
    ## 7           17      Atlantic                ATL /api/v1/divisions/17
    ## 8           17      Atlantic                ATL /api/v1/divisions/17
    ## 9           17      Atlantic                ATL /api/v1/divisions/17
    ## 10          17      Atlantic                ATL /api/v1/divisions/17
    ## 11          18  Metropolitan              Metro /api/v1/divisions/18
    ## 12          17      Atlantic                ATL /api/v1/divisions/17
    ## 13          17      Atlantic                ATL /api/v1/divisions/17
    ## 14          18  Metropolitan              Metro /api/v1/divisions/18
    ## 15          16       Central                CEN /api/v1/divisions/16
    ## 16          17      Atlantic                ATL /api/v1/divisions/17
    ## 17          16       Central                CEN /api/v1/divisions/16
    ## 18          16       Central                CEN /api/v1/divisions/16
    ## 19          15       Pacific                PAC /api/v1/divisions/15
    ## 20          16       Central                CEN /api/v1/divisions/16
    ## 21          15       Pacific                PAC /api/v1/divisions/15
    ## 22          15       Pacific                PAC /api/v1/divisions/15
    ## 23          15       Pacific                PAC /api/v1/divisions/15
    ## 24          16       Central                CEN /api/v1/divisions/16
    ## 25          15       Pacific                PAC /api/v1/divisions/15
    ## 26          15       Pacific                PAC /api/v1/divisions/15
    ## 27          18  Metropolitan              Metro /api/v1/divisions/18
    ## 28          16       Central                CEN /api/v1/divisions/16
    ## 29          16       Central                CEN /api/v1/divisions/16
    ## 30          15       Pacific                PAC /api/v1/divisions/15
    ##    division.abbreviation conference.id conference.name       conference.link
    ## 1                      M             6         Eastern /api/v1/conferences/6
    ## 2                      M             6         Eastern /api/v1/conferences/6
    ## 3                      M             6         Eastern /api/v1/conferences/6
    ## 4                      M             6         Eastern /api/v1/conferences/6
    ## 5                      M             6         Eastern /api/v1/conferences/6
    ## 6                      A             6         Eastern /api/v1/conferences/6
    ## 7                      A             6         Eastern /api/v1/conferences/6
    ## 8                      A             6         Eastern /api/v1/conferences/6
    ## 9                      A             6         Eastern /api/v1/conferences/6
    ## 10                     A             6         Eastern /api/v1/conferences/6
    ## 11                     M             6         Eastern /api/v1/conferences/6
    ## 12                     A             6         Eastern /api/v1/conferences/6
    ## 13                     A             6         Eastern /api/v1/conferences/6
    ## 14                     M             6         Eastern /api/v1/conferences/6
    ## 15                     C             5         Western /api/v1/conferences/5
    ## 16                     A             6         Eastern /api/v1/conferences/6
    ## 17                     C             5         Western /api/v1/conferences/5
    ## 18                     C             5         Western /api/v1/conferences/5
    ## 19                     P             5         Western /api/v1/conferences/5
    ## 20                     C             5         Western /api/v1/conferences/5
    ## 21                     P             5         Western /api/v1/conferences/5
    ## 22                     P             5         Western /api/v1/conferences/5
    ## 23                     P             5         Western /api/v1/conferences/5
    ## 24                     C             5         Western /api/v1/conferences/5
    ## 25                     P             5         Western /api/v1/conferences/5
    ## 26                     P             5         Western /api/v1/conferences/5
    ## 27                     M             6         Eastern /api/v1/conferences/6
    ## 28                     C             5         Western /api/v1/conferences/5
    ## 29                     C             5         Western /api/v1/conferences/5
    ## 30                     P             5         Western /api/v1/conferences/5
    ##    franchise.franchiseId franchise.teamName        franchise.link
    ## 1                     23             Devils /api/v1/franchises/23
    ## 2                     22          Islanders /api/v1/franchises/22
    ## 3                     10            Rangers /api/v1/franchises/10
    ## 4                     16             Flyers /api/v1/franchises/16
    ## 5                     17           Penguins /api/v1/franchises/17
    ## 6                      6             Bruins  /api/v1/franchises/6
    ## 7                     19             Sabres /api/v1/franchises/19
    ## 8                      1          Canadiens  /api/v1/franchises/1
    ## 9                     30           Senators /api/v1/franchises/30
    ## 10                     5        Maple Leafs  /api/v1/franchises/5
    ## 11                    26         Hurricanes /api/v1/franchises/26
    ## 12                    33           Panthers /api/v1/franchises/33
    ## 13                    31          Lightning /api/v1/franchises/31
    ## 14                    24           Capitals /api/v1/franchises/24
    ## 15                    11         Blackhawks /api/v1/franchises/11
    ## 16                    12          Red Wings /api/v1/franchises/12
    ## 17                    34          Predators /api/v1/franchises/34
    ## 18                    18              Blues /api/v1/franchises/18
    ## 19                    21             Flames /api/v1/franchises/21
    ## 20                    27          Avalanche /api/v1/franchises/27
    ## 21                    25             Oilers /api/v1/franchises/25
    ## 22                    20            Canucks /api/v1/franchises/20
    ## 23                    32              Ducks /api/v1/franchises/32
    ## 24                    15              Stars /api/v1/franchises/15
    ## 25                    14              Kings /api/v1/franchises/14
    ## 26                    29             Sharks /api/v1/franchises/29
    ## 27                    36       Blue Jackets /api/v1/franchises/36
    ## 28                    37               Wild /api/v1/franchises/37
    ## 29                    35               Jets /api/v1/franchises/35
    ## 30                    28            Coyotes /api/v1/franchises/28
    ##                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              roster.roster
    ## 1                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  68, 26, 24, 9, 23, 17, 29, 15, 13, 22, NA, 28, 8, 19, 12, 5, 4, 37, 10, 14, 10, 44, 36, 15, 36, 10, 24, 6, 23, 28, 12, 34, 40, 35, 37, 8448208, 8460542, 8460626, 8464977, 8467351, 8467545, 8467899, 8469462, 8469500, 8469547, 8469622, 8469623, 8470609, 8471233, 8471636, 8471748, 8472382, 8472394, 8472410, 8474641, 8475185, 8475199, 8475274, 8475750, 8476209, 8476227, 8476423, 8476457, 8476870, 8476923, 8477059, 8477127, 8466339, 8471239, 8476234, Jaromir Jagr, Patrik Elias, Bryce Salvador, Dainius Zubrus, Scott Gomez, Michael Ryder, Martin Havlat, Tuomo Ruutu, Michael Cammalleri, Jordin Tootoo, Ryane Clowe, Marek Zidlicky, Steve Bernier, Travis Zajac, Tim Sestito, Mark Fraser, Andy Greene, Peter Harrold, Stephen Gionta, Adam Henrique, Jacob Josefson, Eric Gelinas, Seth Helgeson, Jon Merrill, Mike Sislo, Joe Whitney, Reid Boucher, Adam Larsson, Stefan Matteau, Damon Severson, Damien Brunner, Raman Hrabarenka, Scott Clemmensen, Cory Schneider, Keith Kinkaid, /api/v1/people/8448208, /api/v1/people/8460542, /api/v1/people/8460626, /api/v1/people/8464977, /api/v1/people/8467351, /api/v1/people/8467545, /api/v1/people/8467899, /api/v1/people/8469462, /api/v1/people/8469500, /api/v1/people/8469547, /api/v1/people/8469622, /api/v1/people/8469623, /api/v1/people/8470609, /api/v1/people/8471233, /api/v1/people/8471636, /api/v1/people/8471748, /api/v1/people/8472382, /api/v1/people/8472394, /api/v1/people/8472410, /api/v1/people/8474641, /api/v1/people/8475185, /api/v1/people/8475199, /api/v1/people/8475274, /api/v1/people/8475750, /api/v1/people/8476209, /api/v1/people/8476227, /api/v1/people/8476423, /api/v1/people/8476457, /api/v1/people/8476870, /api/v1/people/8476923, /api/v1/people/8477059, /api/v1/people/8477127, /api/v1/people/8466339, /api/v1/people/8471239, /api/v1/people/8476234, R, C, D, C, C, R, R, L, L, R, L, D, R, C, L, D, D, D, C, C, C, D, D, D, R, R, C, D, C, D, R, D, G, G, G, Right Wing, Center, Defenseman, Center, Center, Right Wing, Right Wing, Left Wing, Left Wing, Right Wing, Left Wing, Defenseman, Right Wing, Center, Left Wing, Defenseman, Defenseman, Defenseman, Center, Center, Center, Defenseman, Defenseman, Defenseman, Right Wing, Right Wing, Center, Defenseman, Center, Defenseman, Right Wing, Defenseman, Goalie, Goalie, Goalie, Forward, Forward, Defenseman, Forward, Forward, Forward, Forward, Forward, Forward, Forward, Forward, Defenseman, Forward, Forward, Forward, Defenseman, Defenseman, Defenseman, Forward, Forward, Forward, Defenseman, Defenseman, Defenseman, Forward, Forward, Forward, Defenseman, Forward, Defenseman, Forward, Defenseman, Goalie, Goalie, Goalie, RW, C, D, C, C, RW, RW, LW, LW, RW, LW, D, RW, C, LW, D, D, D, C, C, C, D, D, D, RW, RW, C, D, C, D, RW, D, G, G, G
    ## 2                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      36, 11, 81, 55, 36, 48, 84, 21, 15, 40, 47, 86, 34, 12, 24, 46, 17, 91, 44, 2, 53, 27, 29, 22, 26, 89, 16, 8, 24, 41, 1, 30, 35, 8460720, 8468598, 8470144, 8470187, 8470644, 8471311, 8471362, 8473449, 8473504, 8473546, 8473565, 8473579, 8474066, 8474573, 8474612, 8474659, 8474709, 8475166, 8475177, 8475181, 8475231, 8475314, 8475754, 8476147, 8476158, 8476195, 8476458, 8476852, 8476429, 8470860, 8473434, 8473607, 8474690, Eric Boulton, Lubomir Visnovsky, Frans Nielsen, Johnny Boychuk, Colin McDonald, Tyler Kennedy, Mikhail Grabovski, Kyle Okposo, Cal Clutterbuck, Michael Grabner, Brian Strait, Nikolay Kulemin, Thomas Hickey, Josh Bailey, Travis Hamonic, Matt Donovan, Matt Martin, John Tavares, Calvin de Haan, Nick Leddy, Casey Cizikas, Anders Lee, Brock Nelson, Kael Mouillierat, Harry Zolnierczyk, Cory Conacher, Ryan Strome, Griffin Reinhart, Scott Mayfield, Jaroslav Halak, Chad Johnson, Michal Neuvirth, Kevin Poulin, /api/v1/people/8460720, /api/v1/people/8468598, /api/v1/people/8470144, /api/v1/people/8470187, /api/v1/people/8470644, /api/v1/people/8471311, /api/v1/people/8471362, /api/v1/people/8473449, /api/v1/people/8473504, /api/v1/people/8473546, /api/v1/people/8473565, /api/v1/people/8473579, /api/v1/people/8474066, /api/v1/people/8474573, /api/v1/people/8474612, /api/v1/people/8474659, /api/v1/people/8474709, /api/v1/people/8475166, /api/v1/people/8475177, /api/v1/people/8475181, /api/v1/people/8475231, /api/v1/people/8475314, /api/v1/people/8475754, /api/v1/people/8476147, /api/v1/people/8476158, /api/v1/people/8476195, /api/v1/people/8476458, /api/v1/people/8476852, /api/v1/people/8476429, /api/v1/people/8470860, /api/v1/people/8473434, /api/v1/people/8473607, /api/v1/people/8474690, L, D, C, D, R, C, C, R, R, L, D, L, D, R, D, D, L, C, D, D, C, L, C, L, L, C, C, D, D, G, G, G, G, Left Wing, Defenseman, Center, Defenseman, Right Wing, Center, Center, Right Wing, Right Wing, Left Wing, Defenseman, Left Wing, Defenseman, Right Wing, Defenseman, Defenseman, Left Wing, Center, Defenseman, Defenseman, Center, Left Wing, Center, Left Wing, Left Wing, Center, Center, Defenseman, Defenseman, Goalie, Goalie, Goalie, Goalie, Forward, Defenseman, Forward, Defenseman, Forward, Forward, Forward, Forward, Forward, Forward, Defenseman, Forward, Defenseman, Forward, Defenseman, Defenseman, Forward, Forward, Defenseman, Defenseman, Forward, Forward, Forward, Forward, Forward, Forward, Forward, Defenseman, Defenseman, Goalie, Goalie, Goalie, Goalie, LW, D, C, D, RW, C, C, RW, RW, LW, D, LW, D, RW, D, D, LW, C, D, D, C, LW, C, LW, LW, C, C, D, D, G, G, G, G
    ## 3                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         26, 22, 12, 20, 61, 8, 18, 15, 48, 18, 3, 5, 45, 10, 55, 27, 62, 21, 38, 21, 20, 27, NA, 36, 24, 13, 20, 17, 9, NA, 10, 30, 39, NA, 8466378, 8467096, 8467988, 8468575, 8470041, 8470630, 8470740, 8470854, 8471436, 8471686, 8471735, 8471958, 8473536, 8473544, 8473589, 8474151, 8474176, 8474497, 8474535, 8474613, 8475184, 8475186, 8475243, 8475692, 8475715, 8475763, 8475795, 8475855, 8476468, 8477214, 8477407, 8468685, 8475660, 8477084, Martin St. Louis, Dan Boyle, Ryan Malone, Dominic Moore, Rick Nash, Kevin Klein, Lee Stempniak, Tanner Glass, Matt Hunwick, Marc Staal, Keith Yandle, Dan Girardi, James Sheppard, Derick Brassard, Chris Summers, Ryan McDonagh, Carl Hagelin, Michael Kostka, Chris Mueller, Derek Stepan, Chris Kreider, John Moore, Ryan Bourque, Mats Zuccarello, Oscar Lindberg, Kevin Hayes, Dylan McIlrath, Jesper Fast, J.T. Miller, Conor Allen, Anthony Duclair, Henrik Lundqvist, Cam Talbot, Mackenzie Skapski, /api/v1/people/8466378, /api/v1/people/8467096, /api/v1/people/8467988, /api/v1/people/8468575, /api/v1/people/8470041, /api/v1/people/8470630, /api/v1/people/8470740, /api/v1/people/8470854, /api/v1/people/8471436, /api/v1/people/8471686, /api/v1/people/8471735, /api/v1/people/8471958, /api/v1/people/8473536, /api/v1/people/8473544, /api/v1/people/8473589, /api/v1/people/8474151, /api/v1/people/8474176, /api/v1/people/8474497, /api/v1/people/8474535, /api/v1/people/8474613, /api/v1/people/8475184, /api/v1/people/8475186, /api/v1/people/8475243, /api/v1/people/8475692, /api/v1/people/8475715, /api/v1/people/8475763, /api/v1/people/8475795, /api/v1/people/8475855, /api/v1/people/8476468, /api/v1/people/8477214, /api/v1/people/8477407, /api/v1/people/8468685, /api/v1/people/8475660, /api/v1/people/8477084, R, D, L, C, L, D, R, L, D, D, D, D, C, C, D, D, L, D, C, C, L, D, C, R, L, C, D, R, C, D, L, G, G, G, Right Wing, Defenseman, Left Wing, Center, Left Wing, Defenseman, Right Wing, Left Wing, Defenseman, Defenseman, Defenseman, Defenseman, Center, Center, Defenseman, Defenseman, Left Wing, Defenseman, Center, Center, Left Wing, Defenseman, Center, Right Wing, Left Wing, Center, Defenseman, Right Wing, Center, Defenseman, Left Wing, Goalie, Goalie, Goalie, Forward, Defenseman, Forward, Forward, Forward, Defenseman, Forward, Forward, Defenseman, Defenseman, Defenseman, Defenseman, Forward, Forward, Defenseman, Defenseman, Forward, Defenseman, Forward, Forward, Forward, Defenseman, Forward, Forward, Forward, Forward, Defenseman, Forward, Forward, Defenseman, Forward, Goalie, Goalie, Goalie, RW, D, LW, C, LW, D, RW, LW, D, D, D, D, C, C, D, D, LW, D, C, C, LW, D, C, RW, LW, C, D, RW, C, D, LW, G, G, G
    ## 4                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       44, 32, 55, 20, 25, 55, 8, 76, 42, 21, 28, 47, 93, 17, 2, 44, 36, 10, 38, 26, NA, 10, 20, 10, 21, 14, 21, 53, 12, 41, 72, 29, NA, 8467329, 8468309, 8468513, 8469469, 8469470, 8470601, 8471269, 8471762, 8471767, 8473466, 8473512, 8473584, 8474161, 8474190, 8474568, 8474584, 8474736, 8475170, 8475342, 8475430, 8475723, 8475729, 8475917, 8476177, 8476393, 8476461, 8476872, 8476906, 8477290, 8477930, 8467972, 8469548, 8473461, Vincent Lecavalier, Mark Streit, Nick Schultz, R.J. Umberger, Carlo Colaiacovo, Braydon Coburn, Nicklas Grossmann, Chris VandeVelde, Blair Jones, Ryan White, Claude Giroux, Andrew MacDonald, Jakub Voracek, Wayne Simmonds, Luke Schenn, Michael Del Zotto, Zac Rinaldo, Brayden Schenn, Oliver Lauridsen, Brandon Manning, Petr Straka, Mark Alt, Jason Akeson, Matt Read, Nick Cousins, Sean Couturier, Scott Laughton, Shayne Gostisbehere, Michael Raffl, Pierre-Edouard Bellemare, Rob Zepp, Ray Emery, Steve Mason, /api/v1/people/8467329, /api/v1/people/8468309, /api/v1/people/8468513, /api/v1/people/8469469, /api/v1/people/8469470, /api/v1/people/8470601, /api/v1/people/8471269, /api/v1/people/8471762, /api/v1/people/8471767, /api/v1/people/8473466, /api/v1/people/8473512, /api/v1/people/8473584, /api/v1/people/8474161, /api/v1/people/8474190, /api/v1/people/8474568, /api/v1/people/8474584, /api/v1/people/8474736, /api/v1/people/8475170, /api/v1/people/8475342, /api/v1/people/8475430, /api/v1/people/8475723, /api/v1/people/8475729, /api/v1/people/8475917, /api/v1/people/8476177, /api/v1/people/8476393, /api/v1/people/8476461, /api/v1/people/8476872, /api/v1/people/8476906, /api/v1/people/8477290, /api/v1/people/8477930, /api/v1/people/8467972, /api/v1/people/8469548, /api/v1/people/8473461, C, D, D, C, D, D, D, C, C, C, C, D, R, R, D, D, C, C, D, D, R, D, R, R, C, C, C, D, L, C, G, G, G, Center, Defenseman, Defenseman, Center, Defenseman, Defenseman, Defenseman, Center, Center, Center, Center, Defenseman, Right Wing, Right Wing, Defenseman, Defenseman, Center, Center, Defenseman, Defenseman, Right Wing, Defenseman, Right Wing, Right Wing, Center, Center, Center, Defenseman, Left Wing, Center, Goalie, Goalie, Goalie, Forward, Defenseman, Defenseman, Forward, Defenseman, Defenseman, Defenseman, Forward, Forward, Forward, Forward, Defenseman, Forward, Forward, Defenseman, Defenseman, Forward, Forward, Defenseman, Defenseman, Forward, Defenseman, Forward, Forward, Forward, Forward, Forward, Defenseman, Forward, Forward, Goalie, Goalie, Goalie, C, D, D, C, D, D, D, C, C, C, C, D, RW, RW, D, D, C, C, D, D, RW, D, RW, RW, C, C, C, D, LW, C, G, G, G
    ## 5                                                                                                                                                                                                                                               27, 9, 7, 7, 57, 55, 14, 40, 71, 15, 26, 87, 17, 44, 58, 72, 25, 21, 12, 28, 20, 16, 57, 41, 23, 6, 8, 78, 17, 33, 20, 46, 4, 44, 6, 51, 12, 29, 1, 35, 8465166, 8466393, 8467452, 8468542, 8469473, 8469555, 8470543, 8470654, 8471215, 8471260, 8471476, 8471675, 8471703, 8471710, 8471724, 8471887, 8473682, 8473933, 8473970, 8474013, 8474091, 8474096, 8474102, 8474145, 8475119, 8475155, 8475208, 8475761, 8475810, 8476062, 8476293, 8476339, 8476449, 8476769, 8476874, 8476884, 8477126, 8470594, 8471306, 8473553, Craig Adams, Pascal Dupuis, Rob Scuderi, Paul Martin, Marcel Goc, Christian Ehrhoff, Chris Kunitz, Maxim Lapierre, Evgeni Malkin, Blake Comeau, Daniel Winnik, Sidney Crosby, Steve Downie, Taylor Chorney, Kris Letang, Patric Hornqvist, Andrew Ebbett, Ben Lovejoy, Rob Klinkhammer, Ian Cole, Brandon Sutter, Nick Spaling, David Perron, Robert Bortuzzo, Zach Sill, Simon Despres, Brian Dumoulin, Beau Bennett, Bryan Rust, Mark Arcobello, Scott Wilson, Dominik Uher, Scott Harrington, Bobby Farnham, Olli Maatta, Derrick Pouliot, Jayson Megna, Marc-Andre Fleury, Thomas Greiss, Jeff Zatkoff, /api/v1/people/8465166, /api/v1/people/8466393, /api/v1/people/8467452, /api/v1/people/8468542, /api/v1/people/8469473, /api/v1/people/8469555, /api/v1/people/8470543, /api/v1/people/8470654, /api/v1/people/8471215, /api/v1/people/8471260, /api/v1/people/8471476, /api/v1/people/8471675, /api/v1/people/8471703, /api/v1/people/8471710, /api/v1/people/8471724, /api/v1/people/8471887, /api/v1/people/8473682, /api/v1/people/8473933, /api/v1/people/8473970, /api/v1/people/8474013, /api/v1/people/8474091, /api/v1/people/8474096, /api/v1/people/8474102, /api/v1/people/8474145, /api/v1/people/8475119, /api/v1/people/8475155, /api/v1/people/8475208, /api/v1/people/8475761, /api/v1/people/8475810, /api/v1/people/8476062, /api/v1/people/8476293, /api/v1/people/8476339, /api/v1/people/8476449, /api/v1/people/8476769, /api/v1/people/8476874, /api/v1/people/8476884, /api/v1/people/8477126, /api/v1/people/8470594, /api/v1/people/8471306, /api/v1/people/8473553, R, R, D, D, C, D, L, C, C, L, L, C, R, D, D, R, C, D, L, D, C, C, L, D, C, D, D, R, R, R, L, C, D, R, D, D, C, G, G, G, Right Wing, Right Wing, Defenseman, Defenseman, Center, Defenseman, Left Wing, Center, Center, Left Wing, Left Wing, Center, Right Wing, Defenseman, Defenseman, Right Wing, Center, Defenseman, Left Wing, Defenseman, Center, Center, Left Wing, Defenseman, Center, Defenseman, Defenseman, Right Wing, Right Wing, Right Wing, Left Wing, Center, Defenseman, Right Wing, Defenseman, Defenseman, Center, Goalie, Goalie, Goalie, Forward, Forward, Defenseman, Defenseman, Forward, Defenseman, Forward, Forward, Forward, Forward, Forward, Forward, Forward, Defenseman, Defenseman, Forward, Forward, Defenseman, Forward, Defenseman, Forward, Forward, Forward, Defenseman, Forward, Defenseman, Defenseman, Forward, Forward, Forward, Forward, Forward, Defenseman, Forward, Defenseman, Defenseman, Forward, Goalie, Goalie, Goalie, RW, RW, D, D, C, D, LW, C, C, LW, LW, C, RW, D, D, RW, C, D, LW, D, C, C, LW, D, C, D, D, RW, RW, RW, LW, C, D, RW, D, D, C, G, G, G
    ## 6                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     33, 12, 22, 44, 9, 25, NA, 21, 37, 34, 46, 54, 64, 63, 17, 55, 44, 33, 19, 22, NA, 15, 10, 5, 86, 51, 76, 19, 70, 25, 47, NA, 88, 40, 35, 30, 8465009, 8467346, 8467967, 8469619, 8470169, 8470171, 8470230, 8470626, 8470638, 8471262, 8471276, 8471717, 8472365, 8473419, 8473473, 8474660, 8474749, 8475150, 8475191, 8475414, 8475671, 8475727, 8475792, 8475902, 8476191, 8476363, 8476435, 8476462, 8476476, 8476495, 8476792, 8477228, 8477956, 8471695, 8476844, 8476876, Zdeno Chara, Simon Gagne, Chris Kelly, Dennis Seidenberg, Gregory Campbell, Max Talbot, Daniel Paille, Loui Eriksson, Patrice Bergeron, Carl Soderberg, David Krejci, Adam McQuaid, Bobby Robins, Brad Marchand, Milan Lucic, David Warsofsky, Matt Bartkowski, Jordan Caron, Reilly Smith, Craig Cunningham, Matt Fraser, Ryan Spooner, Brett Connolly, Zach Trotman, Kevan Miller, Brian Ferlin, Alex Khokhlachev, Dougie Hamilton, Joe Morrow, Seth Griffith, Torey Krug, Matt Lindblad, David Pastrnak, Tuukka Rask, Niklas Svedberg, Malcolm Subban, /api/v1/people/8465009, /api/v1/people/8467346, /api/v1/people/8467967, /api/v1/people/8469619, /api/v1/people/8470169, /api/v1/people/8470171, /api/v1/people/8470230, /api/v1/people/8470626, /api/v1/people/8470638, /api/v1/people/8471262, /api/v1/people/8471276, /api/v1/people/8471717, /api/v1/people/8472365, /api/v1/people/8473419, /api/v1/people/8473473, /api/v1/people/8474660, /api/v1/people/8474749, /api/v1/people/8475150, /api/v1/people/8475191, /api/v1/people/8475414, /api/v1/people/8475671, /api/v1/people/8475727, /api/v1/people/8475792, /api/v1/people/8475902, /api/v1/people/8476191, /api/v1/people/8476363, /api/v1/people/8476435, /api/v1/people/8476462, /api/v1/people/8476476, /api/v1/people/8476495, /api/v1/people/8476792, /api/v1/people/8477228, /api/v1/people/8477956, /api/v1/people/8471695, /api/v1/people/8476844, /api/v1/people/8476876, D, L, C, D, C, C, L, L, C, C, C, D, R, L, L, D, D, R, R, L, R, C, R, D, D, R, C, D, D, C, D, C, R, G, G, G, Defenseman, Left Wing, Center, Defenseman, Center, Center, Left Wing, Left Wing, Center, Center, Center, Defenseman, Right Wing, Left Wing, Left Wing, Defenseman, Defenseman, Right Wing, Right Wing, Left Wing, Right Wing, Center, Right Wing, Defenseman, Defenseman, Right Wing, Center, Defenseman, Defenseman, Center, Defenseman, Center, Right Wing, Goalie, Goalie, Goalie, Defenseman, Forward, Forward, Defenseman, Forward, Forward, Forward, Forward, Forward, Forward, Forward, Defenseman, Forward, Forward, Forward, Defenseman, Defenseman, Forward, Forward, Forward, Forward, Forward, Forward, Defenseman, Defenseman, Forward, Forward, Defenseman, Defenseman, Forward, Defenseman, Forward, Forward, Goalie, Goalie, Goalie, D, LW, C, D, C, C, LW, LW, C, C, C, D, RW, LW, LW, D, D, RW, RW, LW, RW, C, RW, D, D, RW, C, D, D, C, D, C, RW, G, G, G
    ## 7                                                                                                                                                                                                                                                                                                                                  12, 8, 37, 4, 48, 28, NA, 18, 41, 71, 36, NA, 44, 24, 11, 57, 63, 29, 17, 20, 49, 44, 22, 13, 40, 32, 28, 25, 19, 59, 2, 55, 16, 23, 31, 30, 39, 31, 1, 8467407, 8469591, 8470018, 8470324, 8470378, 8470729, 8470852, 8471226, 8471236, 8471338, 8471388, 8473426, 8473485, 8474567, 8474570, 8474574, 8474589, 8474610, 8475220, 8475235, 8475309, 8475321, 8475728, 8475796, 8476469, 8476808, 8476878, 8476888, 8476931, 8477213, 8477244, 8477499, 8477507, 8477933, 8473523, 8473607, 8474765, 8475252, 8477092, Brian Gionta, Cody McCormick, Matt Ellis, Josh Gorges, Andre Benoit, Tyson Strachan, Matt Moulson, Drew Stafford, Andrej Meszaros, Torrey Mitchell, Patrick Kaleta, Mike Weber, Chris Stewart, Zach Bogosian, Cody Hodgson, Tyler Myers, Tyler Ennis, Zac Dalpe, Marcus Foligno, Nicolas Deslauriers, Jerry D'Amigo, Phil Varone, Johan Larsson, Mark Pysyk, Joel Armia, Brian Flynn, Zemgus Girgensons, Mikhail Grigorenko, Jake McCabe, Tim Schaller, Chad Ruhwedel, Rasmus Ristolainen, Nikita Zadorov, Sam Reinhart, Jhonas Enroth, Michal Neuvirth, Anders Lindback, Matt Hackett, Andrey Makarov, /api/v1/people/8467407, /api/v1/people/8469591, /api/v1/people/8470018, /api/v1/people/8470324, /api/v1/people/8470378, /api/v1/people/8470729, /api/v1/people/8470852, /api/v1/people/8471226, /api/v1/people/8471236, /api/v1/people/8471338, /api/v1/people/8471388, /api/v1/people/8473426, /api/v1/people/8473485, /api/v1/people/8474567, /api/v1/people/8474570, /api/v1/people/8474574, /api/v1/people/8474589, /api/v1/people/8474610, /api/v1/people/8475220, /api/v1/people/8475235, /api/v1/people/8475309, /api/v1/people/8475321, /api/v1/people/8475728, /api/v1/people/8475796, /api/v1/people/8476469, /api/v1/people/8476808, /api/v1/people/8476878, /api/v1/people/8476888, /api/v1/people/8476931, /api/v1/people/8477213, /api/v1/people/8477244, /api/v1/people/8477499, /api/v1/people/8477507, /api/v1/people/8477933, /api/v1/people/8473523, /api/v1/people/8473607, /api/v1/people/8474765, /api/v1/people/8475252, /api/v1/people/8477092, R, C, L, D, D, D, L, R, D, C, R, D, R, D, C, D, C, C, L, L, R, C, C, D, R, C, C, C, D, L, D, D, D, C, G, G, G, G, G, Right Wing, Center, Left Wing, Defenseman, Defenseman, Defenseman, Left Wing, Right Wing, Defenseman, Center, Right Wing, Defenseman, Right Wing, Defenseman, Center, Defenseman, Center, Center, Left Wing, Left Wing, Right Wing, Center, Center, Defenseman, Right Wing, Center, Center, Center, Defenseman, Left Wing, Defenseman, Defenseman, Defenseman, Center, Goalie, Goalie, Goalie, Goalie, Goalie, Forward, Forward, Forward, Defenseman, Defenseman, Defenseman, Forward, Forward, Defenseman, Forward, Forward, Defenseman, Forward, Defenseman, Forward, Defenseman, Forward, Forward, Forward, Forward, Forward, Forward, Forward, Defenseman, Forward, Forward, Forward, Forward, Defenseman, Forward, Defenseman, Defenseman, Defenseman, Forward, Goalie, Goalie, Goalie, Goalie, Goalie, RW, C, LW, D, D, D, LW, RW, D, C, RW, D, RW, D, C, D, C, C, LW, LW, RW, C, C, D, RW, C, C, C, D, LW, D, D, D, C, G, G, G, G, G
    ## 8                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         55, 6, 20, 79, 43, 27, 14, 11, 6, 9, 25, 71, 17, 51, 26, 34, 76, 38, 67, 20, 22, 29, 28, 15, 39, 25, 24, 11, 88, 32, 27, 10, 61, 36, 31, 31, 8458951, 8467332, 8467334, 8467496, 8468478, 8468635, 8469521, 8469707, 8470104, 8471283, 8471296, 8471338, 8471504, 8471976, 8473507, 8474025, 8474056, 8474137, 8474157, 8474189, 8474668, 8474688, 8475254, 8475739, 8475749, 8475758, 8475797, 8475848, 8476470, 8476808, 8476851, 8477413, 8477455, 8478137, 8471679, 8474682, Sergei Gonchar, Bryan Allen, Manny Malhotra, Andrei Markov, Mike Weaver, Travis Moen, Tomas Plekanec, PA Parenteau, Tom Gilbert, Brandon Prust, Alexei Emelin, Torrey Mitchell, Rene Bourque, David Desharnais, Jeff Petry, Eric Tangradi, P.K. Subban, Drayson Bowman, Max Pacioretty, Lars Eller, Dale Weise, Greg Pateryn, Gabriel Dumont, Michael Bournival, Christian Thomas, Devante Smith-Pelly, Jarred Tinordi, Brendan Gallagher, Nathan Beaulieu, Brian Flynn, Alex Galchenyuk, Sven Andrighetto, Jacob de la Rose, Jiri Sekac, Carey Price, Dustin Tokarski, /api/v1/people/8458951, /api/v1/people/8467332, /api/v1/people/8467334, /api/v1/people/8467496, /api/v1/people/8468478, /api/v1/people/8468635, /api/v1/people/8469521, /api/v1/people/8469707, /api/v1/people/8470104, /api/v1/people/8471283, /api/v1/people/8471296, /api/v1/people/8471338, /api/v1/people/8471504, /api/v1/people/8471976, /api/v1/people/8473507, /api/v1/people/8474025, /api/v1/people/8474056, /api/v1/people/8474137, /api/v1/people/8474157, /api/v1/people/8474189, /api/v1/people/8474668, /api/v1/people/8474688, /api/v1/people/8475254, /api/v1/people/8475739, /api/v1/people/8475749, /api/v1/people/8475758, /api/v1/people/8475797, /api/v1/people/8475848, /api/v1/people/8476470, /api/v1/people/8476808, /api/v1/people/8476851, /api/v1/people/8477413, /api/v1/people/8477455, /api/v1/people/8478137, /api/v1/people/8471679, /api/v1/people/8474682, D, D, C, D, D, L, C, R, D, L, D, C, R, C, D, L, D, L, L, C, R, D, C, L, L, R, D, R, D, C, C, R, L, L, G, G, Defenseman, Defenseman, Center, Defenseman, Defenseman, Left Wing, Center, Right Wing, Defenseman, Left Wing, Defenseman, Center, Right Wing, Center, Defenseman, Left Wing, Defenseman, Left Wing, Left Wing, Center, Right Wing, Defenseman, Center, Left Wing, Left Wing, Right Wing, Defenseman, Right Wing, Defenseman, Center, Center, Right Wing, Left Wing, Left Wing, Goalie, Goalie, Defenseman, Defenseman, Forward, Defenseman, Defenseman, Forward, Forward, Forward, Defenseman, Forward, Defenseman, Forward, Forward, Forward, Defenseman, Forward, Defenseman, Forward, Forward, Forward, Forward, Defenseman, Forward, Forward, Forward, Forward, Defenseman, Forward, Defenseman, Forward, Forward, Forward, Forward, Forward, Goalie, Goalie, D, D, C, D, D, LW, C, RW, D, LW, D, C, RW, C, D, LW, D, LW, LW, C, RW, D, C, LW, LW, RW, D, RW, D, C, C, RW, LW, LW, G, G
    ## 9                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         4, 17, 25, 18, 16, 33, 9, 38, 2, 27, 8, 15, 65, 90, 74, 68, 39, 29, 61, 11, 44, 93, 54, 83, 27, 41, 90, 60, 36, 8464956, 8467330, 8467493, 8470599, 8470667, 8470760, 8471676, 8471862, 8473468, 8473588, 8474068, 8474250, 8474578, 8474605, 8474697, 8474884, 8475163, 8475174, 8475913, 8476386, 8476419, 8476459, 8476477, 8476879, 8477508, 8467950, 8475215, 8476904, 8477202, Chris Phillips, David Legwand, Chris Neil, Milan Michalek, Clarke MacArthur, Marc Methot, Bobby Ryan, Colin Greening, Eric Gryba, Erik Condra, Kyle Turris, Zack Smith, Erik Karlsson, Patrick Wiercioch, Mark Borowiecki, Mike Hoffman, Alex Chiasson, Jared Cowen, Mark Stone, Shane Prince, Jean-Gabriel Pageau, Mika Zibanejad, Matt Puempel, Cody Ceci, Curtis Lazar, Craig Anderson, Robin Lehner, Chris Driedger, Andrew Hammond, /api/v1/people/8464956, /api/v1/people/8467330, /api/v1/people/8467493, /api/v1/people/8470599, /api/v1/people/8470667, /api/v1/people/8470760, /api/v1/people/8471676, /api/v1/people/8471862, /api/v1/people/8473468, /api/v1/people/8473588, /api/v1/people/8474068, /api/v1/people/8474250, /api/v1/people/8474578, /api/v1/people/8474605, /api/v1/people/8474697, /api/v1/people/8474884, /api/v1/people/8475163, /api/v1/people/8475174, /api/v1/people/8475913, /api/v1/people/8476386, /api/v1/people/8476419, /api/v1/people/8476459, /api/v1/people/8476477, /api/v1/people/8476879, /api/v1/people/8477508, /api/v1/people/8467950, /api/v1/people/8475215, /api/v1/people/8476904, /api/v1/people/8477202, D, C, R, L, L, D, R, C, D, R, C, C, D, D, D, L, R, D, R, L, C, C, L, D, C, G, G, G, G, Defenseman, Center, Right Wing, Left Wing, Left Wing, Defenseman, Right Wing, Center, Defenseman, Right Wing, Center, Center, Defenseman, Defenseman, Defenseman, Left Wing, Right Wing, Defenseman, Right Wing, Left Wing, Center, Center, Left Wing, Defenseman, Center, Goalie, Goalie, Goalie, Goalie, Defenseman, Forward, Forward, Forward, Forward, Defenseman, Forward, Forward, Defenseman, Forward, Forward, Forward, Defenseman, Defenseman, Defenseman, Forward, Forward, Defenseman, Forward, Forward, Forward, Forward, Forward, Defenseman, Forward, Goalie, Goalie, Goalie, Goalie, D, C, RW, LW, LW, D, RW, C, D, RW, C, C, D, D, D, LW, RW, D, RW, LW, C, C, LW, D, C, G, G, G, G
    ## 10                                                                                                                                                                                                                                         12, 13, 2, 28, 15, 19, 3, 40, 71, 17, 25, 45, 26, 11, 47, 81, 22, 23, 25, 39, 39, 51, 27, 21, 23, 44, 37, 91, NA, 14, 67, 14, 8, 39, 17, 10, 44, 37, 47, 45, 8462196, 8466140, 8466142, 8468778, 8470152, 8470207, 8470602, 8470867, 8470920, 8471266, 8471390, 8471392, 8471476, 8471742, 8473463, 8473548, 8473560, 8473942, 8474037, 8474113, 8474192, 8474581, 8474748, 8475098, 8475119, 8475148, 8475154, 8475172, 8475180, 8475209, 8475295, 8475735, 8475774, 8475842, 8476410, 8476478, 8476853, 8478376, 8473503, 8473541, Stephane Robidas, Olli Jokinen, Eric Brewer, Colton Orr, Joakim Lindstrom, Joffrey Lupul, Dion Phaneuf, Troy Bodie, David Clarkson, David Booth, Mike Santorelli, Roman Polak, Daniel Winnik, Cody Franson, Leo Komarov, Phil Kessel, Korbinian Holzer, Trevor Smith, James van Riemsdyk, TJ Brennan, Matt Frattin, Jake Gardiner, Andrew MacWilliam, Tyler Bozak, Zach Sill, Tim Erixon, Carter Ashton, Nazem Kadri, Peter Holland, Richard Panik, Brandon Kozun, Greg McKegg, Petter Granberg, Sam Carrick, Josh Leivo, Stuart Percy, Morgan Rielly, Casey Bailey, James Reimer, Jonathan Bernier, /api/v1/people/8462196, /api/v1/people/8466140, /api/v1/people/8466142, /api/v1/people/8468778, /api/v1/people/8470152, /api/v1/people/8470207, /api/v1/people/8470602, /api/v1/people/8470867, /api/v1/people/8470920, /api/v1/people/8471266, /api/v1/people/8471390, /api/v1/people/8471392, /api/v1/people/8471476, /api/v1/people/8471742, /api/v1/people/8473463, /api/v1/people/8473548, /api/v1/people/8473560, /api/v1/people/8473942, /api/v1/people/8474037, /api/v1/people/8474113, /api/v1/people/8474192, /api/v1/people/8474581, /api/v1/people/8474748, /api/v1/people/8475098, /api/v1/people/8475119, /api/v1/people/8475148, /api/v1/people/8475154, /api/v1/people/8475172, /api/v1/people/8475180, /api/v1/people/8475209, /api/v1/people/8475295, /api/v1/people/8475735, /api/v1/people/8475774, /api/v1/people/8475842, /api/v1/people/8476410, /api/v1/people/8476478, /api/v1/people/8476853, /api/v1/people/8478376, /api/v1/people/8473503, /api/v1/people/8473541, D, C, D, R, C, L, D, R, R, L, C, D, L, D, R, R, D, C, L, D, R, D, D, C, C, D, R, C, C, R, R, C, D, C, L, D, D, R, G, G, Defenseman, Center, Defenseman, Right Wing, Center, Left Wing, Defenseman, Right Wing, Right Wing, Left Wing, Center, Defenseman, Left Wing, Defenseman, Right Wing, Right Wing, Defenseman, Center, Left Wing, Defenseman, Right Wing, Defenseman, Defenseman, Center, Center, Defenseman, Right Wing, Center, Center, Right Wing, Right Wing, Center, Defenseman, Center, Left Wing, Defenseman, Defenseman, Right Wing, Goalie, Goalie, Defenseman, Forward, Defenseman, Forward, Forward, Forward, Defenseman, Forward, Forward, Forward, Forward, Defenseman, Forward, Defenseman, Forward, Forward, Defenseman, Forward, Forward, Defenseman, Forward, Defenseman, Defenseman, Forward, Forward, Defenseman, Forward, Forward, Forward, Forward, Forward, Forward, Defenseman, Forward, Forward, Defenseman, Defenseman, Forward, Goalie, Goalie, D, C, D, RW, C, LW, D, RW, RW, LW, C, D, LW, D, RW, RW, D, C, LW, D, RW, D, D, C, C, D, RW, C, C, RW, RW, C, D, C, LW, D, D, RW, G, G
    ## 11                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  81, 26, 6, 18, NA, 13, 39, 12, 5, 24, 9, 11, 15, 20, 24, 73, 38, 22, 47, 15, 62, 41, 72, 53, 52, 48, 47, 49, 32, 56, 28, 38, 30, 35, 8468493, 8468639, 8469476, 8469508, 8469531, 8470120, 8470302, 8470595, 8471284, 8471804, 8473482, 8473533, 8474052, 8474062, 8474089, 8474135, 8474519, 8474576, 8474661, 8475250, 8475294, 8475732, 8475753, 8475784, 8475826, 8475980, 8476397, 8476437, 8476465, 8476944, 8477496, 8477887, 8470320, 8471418, Ron Hainsey, John-Michael Liles, Tim Gleason, Jay McClement, Jay Harrison, Alexander Semin, Patrick Dwyer, Eric Staal, Andrej Sekera, Nathan Gerbe, Jiri Tlusty, Jordan Staal, Chris Terry, Riley Nash, Brad Malone, Brett Bellemore, Jack Hillen, Zach Boychuk, Michal Jordan, Andrej Nestrasil, Rasmus Rissanen, Danny Biega, Justin Faulk, Jeff Skinner, Justin Shugg, Brody Sutter, Keegan Lowe, Victor Rask, Ryan Murphy, Brendan Woods, Elias Lindholm, Patrick Brown, Cam Ward, Anton Khudobin, /api/v1/people/8468493, /api/v1/people/8468639, /api/v1/people/8469476, /api/v1/people/8469508, /api/v1/people/8469531, /api/v1/people/8470120, /api/v1/people/8470302, /api/v1/people/8470595, /api/v1/people/8471284, /api/v1/people/8471804, /api/v1/people/8473482, /api/v1/people/8473533, /api/v1/people/8474052, /api/v1/people/8474062, /api/v1/people/8474089, /api/v1/people/8474135, /api/v1/people/8474519, /api/v1/people/8474576, /api/v1/people/8474661, /api/v1/people/8475250, /api/v1/people/8475294, /api/v1/people/8475732, /api/v1/people/8475753, /api/v1/people/8475784, /api/v1/people/8475826, /api/v1/people/8475980, /api/v1/people/8476397, /api/v1/people/8476437, /api/v1/people/8476465, /api/v1/people/8476944, /api/v1/people/8477496, /api/v1/people/8477887, /api/v1/people/8470320, /api/v1/people/8471418, D, D, D, C, D, R, R, C, D, C, L, C, L, C, C, D, D, L, D, C, D, D, D, L, R, C, D, C, D, L, C, C, G, G, Defenseman, Defenseman, Defenseman, Center, Defenseman, Right Wing, Right Wing, Center, Defenseman, Center, Left Wing, Center, Left Wing, Center, Center, Defenseman, Defenseman, Left Wing, Defenseman, Center, Defenseman, Defenseman, Defenseman, Left Wing, Right Wing, Center, Defenseman, Center, Defenseman, Left Wing, Center, Center, Goalie, Goalie, Defenseman, Defenseman, Defenseman, Forward, Defenseman, Forward, Forward, Forward, Defenseman, Forward, Forward, Forward, Forward, Forward, Forward, Defenseman, Defenseman, Forward, Defenseman, Forward, Defenseman, Defenseman, Defenseman, Forward, Forward, Forward, Defenseman, Forward, Defenseman, Forward, Forward, Forward, Goalie, Goalie, D, D, D, C, D, RW, RW, C, D, C, LW, C, LW, C, C, D, D, LW, D, C, D, D, D, LW, RW, C, D, C, D, LW, C, C, G, G
    ## 12                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               68, 33, 22, 51, 17, 28, 82, 36, 12, 9, 23, 34, NA, 44, 48, 11, 4, 7, 73, 28, 15, 27, 6, 16, 23, 11, 16, 5, 1, 39, 35, 8448208, 8465185, 8465978, 8466285, 8468001, 8468504, 8468518, 8469638, 8470039, 8470105, 8470176, 8470839, 8471245, 8474000, 8474606, 8474625, 8475153, 8475179, 8475204, 8475253, 8475755, 8475760, 8475790, 8476389, 8476428, 8476456, 8477493, 8477932, 8466141, 8468540, 8471219, Jaromir Jagr, Willie Mitchell, Shawn Thornton, Brian Campbell, Derek MacKenzie, Brad Boyes, Tomas Kopecky, Jussi Jokinen, Tomas Fleischmann, Scottie Upshall, Sean Bergenheim, Shane O'Brien, Dave Bolland, Steven Kampfer, Colby Robak, Jimmy Hayes, Dylan Olsen, Dmitry Kulikov, Brandon Pirri, Garrett Wilson, Alexander Petrovic, Nick Bjugstad, Erik Gudbranson, Vincent Trocheck, Rocco Grimaldi, Jonathan Huberdeau, Aleksander Barkov, Aaron Ekblad, Roberto Luongo, Dan Ellis, Al Montoya, /api/v1/people/8448208, /api/v1/people/8465185, /api/v1/people/8465978, /api/v1/people/8466285, /api/v1/people/8468001, /api/v1/people/8468504, /api/v1/people/8468518, /api/v1/people/8469638, /api/v1/people/8470039, /api/v1/people/8470105, /api/v1/people/8470176, /api/v1/people/8470839, /api/v1/people/8471245, /api/v1/people/8474000, /api/v1/people/8474606, /api/v1/people/8474625, /api/v1/people/8475153, /api/v1/people/8475179, /api/v1/people/8475204, /api/v1/people/8475253, /api/v1/people/8475755, /api/v1/people/8475760, /api/v1/people/8475790, /api/v1/people/8476389, /api/v1/people/8476428, /api/v1/people/8476456, /api/v1/people/8477493, /api/v1/people/8477932, /api/v1/people/8466141, /api/v1/people/8468540, /api/v1/people/8471219, R, D, L, D, C, R, R, L, L, R, L, D, C, D, D, R, D, D, C, L, D, C, D, C, R, L, C, D, G, G, G, Right Wing, Defenseman, Left Wing, Defenseman, Center, Right Wing, Right Wing, Left Wing, Left Wing, Right Wing, Left Wing, Defenseman, Center, Defenseman, Defenseman, Right Wing, Defenseman, Defenseman, Center, Left Wing, Defenseman, Center, Defenseman, Center, Right Wing, Left Wing, Center, Defenseman, Goalie, Goalie, Goalie, Forward, Defenseman, Forward, Defenseman, Forward, Forward, Forward, Forward, Forward, Forward, Forward, Defenseman, Forward, Defenseman, Defenseman, Forward, Defenseman, Defenseman, Forward, Forward, Defenseman, Forward, Defenseman, Forward, Forward, Forward, Forward, Defenseman, Goalie, Goalie, Goalie, RW, D, LW, D, C, RW, RW, LW, LW, RW, LW, D, C, D, D, RW, D, D, C, LW, D, C, D, C, RW, LW, C, D, G, G, G
    ## 13                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           2, 10, 51, 55, 9, 25, 24, 20, 6, 10, 17, NA, 91, 44, 28, 9, 77, 33, 10, 18, 89, 86, 90, 81, 23, 68, 13, 62, 92, 20, 30, 88, 8466142, 8466160, 8470047, 8470601, 8470619, 8470640, 8471339, 8471663, 8471873, 8471916, 8473986, 8474520, 8474564, 8474717, 8474722, 8474870, 8475167, 8475462, 8475792, 8476292, 8476300, 8476453, 8476480, 8476539, 8476806, 8476886, 8476975, 8477205, 8477494, 8460705, 8471750, 8476883, Eric Brewer, Brenden Morrow, Valtteri Filppula, Braydon Coburn, Brian Boyle, Matt Carle, Ryan Callahan, Mike Blunden, Anton Stralman, Mike Angelidis, Alex Killorn, Jason Garrison, Steven Stamkos, Mark Barberio, Luke Witkowski, Tyler Johnson, Victor Hedman, Radko Gudas, Brett Connolly, Ondrej Palat, Nikita Nesterov, Nikita Kucherov, Vladislav Namestnikov, Jonathan Marchessault, JT Brown, Slater Koekkoek, Cedric Paquette, Andrej Sustr, Jonathan Drouin, Evgeni Nabokov, Ben Bishop, Andrei Vasilevskiy, /api/v1/people/8466142, /api/v1/people/8466160, /api/v1/people/8470047, /api/v1/people/8470601, /api/v1/people/8470619, /api/v1/people/8470640, /api/v1/people/8471339, /api/v1/people/8471663, /api/v1/people/8471873, /api/v1/people/8471916, /api/v1/people/8473986, /api/v1/people/8474520, /api/v1/people/8474564, /api/v1/people/8474717, /api/v1/people/8474722, /api/v1/people/8474870, /api/v1/people/8475167, /api/v1/people/8475462, /api/v1/people/8475792, /api/v1/people/8476292, /api/v1/people/8476300, /api/v1/people/8476453, /api/v1/people/8476480, /api/v1/people/8476539, /api/v1/people/8476806, /api/v1/people/8476886, /api/v1/people/8476975, /api/v1/people/8477205, /api/v1/people/8477494, /api/v1/people/8460705, /api/v1/people/8471750, /api/v1/people/8476883, D, L, C, D, C, D, R, R, D, L, L, D, C, D, R, C, D, D, R, L, D, R, C, C, R, D, C, D, L, G, G, G, Defenseman, Left Wing, Center, Defenseman, Center, Defenseman, Right Wing, Right Wing, Defenseman, Left Wing, Left Wing, Defenseman, Center, Defenseman, Right Wing, Center, Defenseman, Defenseman, Right Wing, Left Wing, Defenseman, Right Wing, Center, Center, Right Wing, Defenseman, Center, Defenseman, Left Wing, Goalie, Goalie, Goalie, Defenseman, Forward, Forward, Defenseman, Forward, Defenseman, Forward, Forward, Defenseman, Forward, Forward, Defenseman, Forward, Defenseman, Forward, Forward, Defenseman, Defenseman, Forward, Forward, Defenseman, Forward, Forward, Forward, Forward, Defenseman, Forward, Defenseman, Forward, Goalie, Goalie, Goalie, D, LW, C, D, C, D, RW, RW, D, LW, LW, D, C, D, RW, C, D, D, RW, LW, D, RW, C, C, RW, D, C, D, LW, G, G, G
    ## 14                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          20, 42, 44, 6, 25, 21, 22, 8, 27, 36, 15, 38, 19, 27, 83, 38, 74, 90, NA, NA, 17, 92, 16, 43, 6, 43, 87, 88, 95, 40, 70, 31, 8466251, 8468208, 8468498, 8469476, 8469639, 8470611, 8471185, 8471214, 8471242, 8471426, 8471702, 8472368, 8473563, 8473991, 8474291, 8474519, 8474590, 8475149, 8475161, 8475247, 8475619, 8475744, 8475816, 8476162, 8476799, 8476880, 8477070, 8477220, 8477444, 8471251, 8474651, 8475831, Jason Chimera, Joel Ward, Brooks Orpik, Tim Gleason, Brooks Laich, Eric Fehr, Curtis Glencross, Alex Ovechkin, Mike Green, Troy Brouwer, Matt Niskanen, Chris Conner, Nicklas Backstrom, Karl Alzner, Jay Beagle, Jack Hillen, John Carlson, Marcus Johansson, Chris Brown, Michael Latta, Aaron Volpatti, Evgeny Kuznetsov, Stanislav Galiev, Steve Oleksy, Cameron Schilling, Tom Wilson, Liam O'Brien, Nate Schmidt, Andre Burakovsky, Justin Peters, Braden Holtby, Philipp Grubauer, /api/v1/people/8466251, /api/v1/people/8468208, /api/v1/people/8468498, /api/v1/people/8469476, /api/v1/people/8469639, /api/v1/people/8470611, /api/v1/people/8471185, /api/v1/people/8471214, /api/v1/people/8471242, /api/v1/people/8471426, /api/v1/people/8471702, /api/v1/people/8472368, /api/v1/people/8473563, /api/v1/people/8473991, /api/v1/people/8474291, /api/v1/people/8474519, /api/v1/people/8474590, /api/v1/people/8475149, /api/v1/people/8475161, /api/v1/people/8475247, /api/v1/people/8475619, /api/v1/people/8475744, /api/v1/people/8475816, /api/v1/people/8476162, /api/v1/people/8476799, /api/v1/people/8476880, /api/v1/people/8477070, /api/v1/people/8477220, /api/v1/people/8477444, /api/v1/people/8471251, /api/v1/people/8474651, /api/v1/people/8475831, L, R, D, D, C, C, L, L, D, R, D, R, C, D, C, D, D, L, R, C, L, C, R, D, D, R, C, D, L, G, G, G, Left Wing, Right Wing, Defenseman, Defenseman, Center, Center, Left Wing, Left Wing, Defenseman, Right Wing, Defenseman, Right Wing, Center, Defenseman, Center, Defenseman, Defenseman, Left Wing, Right Wing, Center, Left Wing, Center, Right Wing, Defenseman, Defenseman, Right Wing, Center, Defenseman, Left Wing, Goalie, Goalie, Goalie, Forward, Forward, Defenseman, Defenseman, Forward, Forward, Forward, Forward, Defenseman, Forward, Defenseman, Forward, Forward, Defenseman, Forward, Defenseman, Defenseman, Forward, Forward, Forward, Forward, Forward, Forward, Defenseman, Defenseman, Forward, Forward, Defenseman, Forward, Goalie, Goalie, Goalie, LW, RW, D, D, C, C, LW, LW, D, RW, D, RW, C, D, C, D, D, LW, RW, C, LW, C, RW, D, D, RW, C, D, LW, G, G, G
    ## 15                                                                                                                                                                                                                                                                                                                                                                                                                                                  44, 32, 81, 17, 50, 10, 29, 2, 7, 13, 29, 12, 10, 4, 26, 11, 19, 88, 18, 44, 5, 33, 16, 20, 65, 44, 6, 6, 20, 24, 86, 38, 57, NA, 50, 33, 32, 8459670, 8465058, 8466148, 8467389, 8468535, 8469544, 8469665, 8470281, 8470607, 8470666, 8471254, 8471299, 8471346, 8471769, 8471879, 8471956, 8473604, 8474141, 8474727, 8475148, 8475182, 8475214, 8475323, 8475807, 8476381, 8476394, 8476403, 8476431, 8476438, 8476479, 8476882, 8477451, 8477845, 8478393, 8470645, 8474152, 8477293, Kimmo Timonen, Michal Rozsival, Marian Hossa, Brad Richards, Antoine Vermette, Patrick Sharp, Johnny Oduya, Duncan Keith, Brent Seabrook, Daniel Carcillo, Bryan Bickell, Peter Regin, Kris Versteeg, Niklas Hjalmarsson, Kyle Cumiskey, Andrew Desjardins, Jonathan Toews, Patrick Kane, Ben Smith, Tim Erixon, David Rundblad, Jeremy Morin, Marcus Kruger, Joakim Nordstrom, Andrew Shaw, Michael Paliotta, Klas Dahlbeck, Adam Clendening, Brandon Saad, Phillip Danault, Teuvo Teravainen, Ryan Hartman, Trevor van Riemsdyk, Kyle Baun, Corey Crawford, Scott Darling, Antti Raanta, /api/v1/people/8459670, /api/v1/people/8465058, /api/v1/people/8466148, /api/v1/people/8467389, /api/v1/people/8468535, /api/v1/people/8469544, /api/v1/people/8469665, /api/v1/people/8470281, /api/v1/people/8470607, /api/v1/people/8470666, /api/v1/people/8471254, /api/v1/people/8471299, /api/v1/people/8471346, /api/v1/people/8471769, /api/v1/people/8471879, /api/v1/people/8471956, /api/v1/people/8473604, /api/v1/people/8474141, /api/v1/people/8474727, /api/v1/people/8475148, /api/v1/people/8475182, /api/v1/people/8475214, /api/v1/people/8475323, /api/v1/people/8475807, /api/v1/people/8476381, /api/v1/people/8476394, /api/v1/people/8476403, /api/v1/people/8476431, /api/v1/people/8476438, /api/v1/people/8476479, /api/v1/people/8476882, /api/v1/people/8477451, /api/v1/people/8477845, /api/v1/people/8478393, /api/v1/people/8470645, /api/v1/people/8474152, /api/v1/people/8477293, D, D, R, C, C, L, D, D, D, L, L, C, R, D, D, L, C, R, R, D, D, L, C, C, R, D, D, D, L, C, L, R, D, R, G, G, G, Defenseman, Defenseman, Right Wing, Center, Center, Left Wing, Defenseman, Defenseman, Defenseman, Left Wing, Left Wing, Center, Right Wing, Defenseman, Defenseman, Left Wing, Center, Right Wing, Right Wing, Defenseman, Defenseman, Left Wing, Center, Center, Right Wing, Defenseman, Defenseman, Defenseman, Left Wing, Center, Left Wing, Right Wing, Defenseman, Right Wing, Goalie, Goalie, Goalie, Defenseman, Defenseman, Forward, Forward, Forward, Forward, Defenseman, Defenseman, Defenseman, Forward, Forward, Forward, Forward, Defenseman, Defenseman, Forward, Forward, Forward, Forward, Defenseman, Defenseman, Forward, Forward, Forward, Forward, Defenseman, Defenseman, Defenseman, Forward, Forward, Forward, Forward, Defenseman, Forward, Goalie, Goalie, Goalie, D, D, RW, C, C, LW, D, D, D, LW, LW, C, RW, D, D, LW, C, RW, RW, D, D, LW, C, C, RW, D, D, D, LW, C, LW, RW, D, RW, G, G, G
    ## 16                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   11, 72, NA, 40, 55, 90, 28, 52, 27, 20, 93, 46, 8, 43, 42, 18, 14, 32, 41, 90, 15, 23, 67, 3, 92, 61, 41, 65, 35, 38, 50, 34, 8466149, 8467396, 8467514, 8468083, 8468509, 8469457, 8469623, 8470318, 8470724, 8470778, 8471309, 8471693, 8471716, 8471794, 8474090, 8474168, 8474679, 8474873, 8475157, 8475193, 8475250, 8475772, 8475800, 8476289, 8476430, 8476443, 8476822, 8477215, 8470657, 8474591, 8475361, 8475852, Daniel Cleary, Erik Cole, Pavel Datsyuk, Henrik Zetterberg, Niklas Kronwall, Stephen Weiss, Marek Zidlicky, Jonathan Ericsson, Kyle Quincey, Drew Miller, Johan Franzen, Jakub Kindl, Justin Abdelkader, Darren Helm, Brendan Smith, Joakim Andersson, Gustav Nyquist, Brian Lashoff, Landon Ferraro, Tomas Tatar, Andrej Nestrasil, Riley Sheahan, Teemu Pulkkinen, Alexey Marchenko, Tomas Jurco, Xavier Ouellet, Luke Glendening, Danny DeKeyser, Jimmy Howard, Tom McCollum, Jonas Gustavsson, Petr Mrazek, /api/v1/people/8466149, /api/v1/people/8467396, /api/v1/people/8467514, /api/v1/people/8468083, /api/v1/people/8468509, /api/v1/people/8469457, /api/v1/people/8469623, /api/v1/people/8470318, /api/v1/people/8470724, /api/v1/people/8470778, /api/v1/people/8471309, /api/v1/people/8471693, /api/v1/people/8471716, /api/v1/people/8471794, /api/v1/people/8474090, /api/v1/people/8474168, /api/v1/people/8474679, /api/v1/people/8474873, /api/v1/people/8475157, /api/v1/people/8475193, /api/v1/people/8475250, /api/v1/people/8475772, /api/v1/people/8475800, /api/v1/people/8476289, /api/v1/people/8476430, /api/v1/people/8476443, /api/v1/people/8476822, /api/v1/people/8477215, /api/v1/people/8470657, /api/v1/people/8474591, /api/v1/people/8475361, /api/v1/people/8475852, R, L, C, C, D, C, D, D, D, L, R, D, L, L, D, C, C, D, C, L, C, C, L, D, R, D, C, D, G, G, G, G, Right Wing, Left Wing, Center, Center, Defenseman, Center, Defenseman, Defenseman, Defenseman, Left Wing, Right Wing, Defenseman, Left Wing, Left Wing, Defenseman, Center, Center, Defenseman, Center, Left Wing, Center, Center, Left Wing, Defenseman, Right Wing, Defenseman, Center, Defenseman, Goalie, Goalie, Goalie, Goalie, Forward, Forward, Forward, Forward, Defenseman, Forward, Defenseman, Defenseman, Defenseman, Forward, Forward, Defenseman, Forward, Forward, Defenseman, Forward, Forward, Defenseman, Forward, Forward, Forward, Forward, Forward, Defenseman, Forward, Defenseman, Forward, Defenseman, Goalie, Goalie, Goalie, Goalie, RW, LW, C, C, D, C, D, D, D, LW, RW, D, LW, LW, D, C, C, D, C, LW, C, C, LW, D, RW, D, C, D, G, G, G, G
    ## 17                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         7, 13, 12, 63, 20, 28, 8, 24, 6, 25, 18, 25, 11, 24, 28, 49, 22, 59, 4, 28, 14, 15, 57, 19, 2, 33, 20, 9, 3, 22, 33, 35, 40, 31, 8464989, 8466140, 8467370, 8467371, 8468501, 8468700, 8469485, 8470180, 8470642, 8471390, 8471707, 8471733, 8471742, 8473537, 8473658, 8473921, 8474569, 8474600, 8475176, 8475192, 8475218, 8475225, 8475268, 8475714, 8475868, 8476062, 8476447, 8476887, 8477495, 8477942, 8478042, 8471469, 8475622, 8476992, Matt Cullen, Olli Jokinen, Mike Fisher, Mike Ribeiro, Anton Volchenkov, Paul Gaustad, Derek Roy, Eric Nystrom, Shea Weber, Mike Santorelli, James Neal, Rich Clune, Cody Franson, Viktor Stalberg, Victor Bartley, Joe Piskula, Colin Wilson, Roman Josi, Ryan Ellis, Taylor Beck, Mattias Ekholm, Craig Smith, Gabriel Bourque, Calle Jarnkrok, Anthony Bitetto, Mark Arcobello, Miikka Salomaki, Filip Forsberg, Seth Jones, Kevin Fiala, Viktor Arvidsson, Pekka Rinne, Carter Hutton, Marek Mazanec, /api/v1/people/8464989, /api/v1/people/8466140, /api/v1/people/8467370, /api/v1/people/8467371, /api/v1/people/8468501, /api/v1/people/8468700, /api/v1/people/8469485, /api/v1/people/8470180, /api/v1/people/8470642, /api/v1/people/8471390, /api/v1/people/8471707, /api/v1/people/8471733, /api/v1/people/8471742, /api/v1/people/8473537, /api/v1/people/8473658, /api/v1/people/8473921, /api/v1/people/8474569, /api/v1/people/8474600, /api/v1/people/8475176, /api/v1/people/8475192, /api/v1/people/8475218, /api/v1/people/8475225, /api/v1/people/8475268, /api/v1/people/8475714, /api/v1/people/8475868, /api/v1/people/8476062, /api/v1/people/8476447, /api/v1/people/8476887, /api/v1/people/8477495, /api/v1/people/8477942, /api/v1/people/8478042, /api/v1/people/8471469, /api/v1/people/8475622, /api/v1/people/8476992, C, C, C, C, D, C, C, L, D, C, L, L, D, L, D, D, C, D, D, L, D, R, L, C, D, R, R, L, D, L, R, G, G, G, Center, Center, Center, Center, Defenseman, Center, Center, Left Wing, Defenseman, Center, Left Wing, Left Wing, Defenseman, Left Wing, Defenseman, Defenseman, Center, Defenseman, Defenseman, Left Wing, Defenseman, Right Wing, Left Wing, Center, Defenseman, Right Wing, Right Wing, Left Wing, Defenseman, Left Wing, Right Wing, Goalie, Goalie, Goalie, Forward, Forward, Forward, Forward, Defenseman, Forward, Forward, Forward, Defenseman, Forward, Forward, Forward, Defenseman, Forward, Defenseman, Defenseman, Forward, Defenseman, Defenseman, Forward, Defenseman, Forward, Forward, Forward, Defenseman, Forward, Forward, Forward, Defenseman, Forward, Forward, Goalie, Goalie, Goalie, C, C, C, C, D, C, C, LW, D, C, LW, LW, D, LW, D, D, C, D, D, LW, D, RW, LW, C, D, RW, RW, LW, D, LW, RW, G, G, G
    ## 18                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     13, 5, 33, 92, 57, 4, 19, 15, 20, 40, 21, 29, 7, 26, 77, 25, 75, 10, 28, 22, 4, 41, 27, 15, 56, 91, 17, 8, 23, 48, 30, 37, 34, 8466140, 8467890, 8467917, 8468505, 8469473, 8469760, 8470151, 8470152, 8470257, 8470654, 8470655, 8470662, 8470871, 8471669, 8471698, 8471761, 8471817, 8473534, 8474013, 8474031, 8474125, 8474145, 8474565, 8474627, 8475175, 8475765, 8475768, 8476427, 8476436, 8476989, 8455710, 8470880, 8474596, Olli Jokinen, Barret Jackman, Jordan Leopold, Steve Ott, Marcel Goc, Zbynek Michalek, Jay Bouwmeester, Joakim Lindstrom, Alexander Steen, Maxim Lapierre, David Backes, Colin Fraser, Chris Porter, Paul Stastny, T.J. Oshie, Chris Butler, Ryan Reaves, Patrik Berglund, Ian Cole, Kevin Shattenkirk, Carl Gunnarsson, Robert Bortuzzo, Alex Pietrangelo, Jori Lehtera, Magnus Paajarvi, Vladimir Tarasenko, Jaden Schwartz, Ty Rattie, Dmitrij Jaskin, Petteri Lindbohm, Martin Brodeur, Brian Elliott, Jake Allen, /api/v1/people/8466140, /api/v1/people/8467890, /api/v1/people/8467917, /api/v1/people/8468505, /api/v1/people/8469473, /api/v1/people/8469760, /api/v1/people/8470151, /api/v1/people/8470152, /api/v1/people/8470257, /api/v1/people/8470654, /api/v1/people/8470655, /api/v1/people/8470662, /api/v1/people/8470871, /api/v1/people/8471669, /api/v1/people/8471698, /api/v1/people/8471761, /api/v1/people/8471817, /api/v1/people/8473534, /api/v1/people/8474013, /api/v1/people/8474031, /api/v1/people/8474125, /api/v1/people/8474145, /api/v1/people/8474565, /api/v1/people/8474627, /api/v1/people/8475175, /api/v1/people/8475765, /api/v1/people/8475768, /api/v1/people/8476427, /api/v1/people/8476436, /api/v1/people/8476989, /api/v1/people/8455710, /api/v1/people/8470880, /api/v1/people/8474596, C, D, D, C, C, D, D, C, L, C, R, C, L, C, R, D, R, C, D, D, D, D, D, C, L, R, L, R, R, D, G, G, G, Center, Defenseman, Defenseman, Center, Center, Defenseman, Defenseman, Center, Left Wing, Center, Right Wing, Center, Left Wing, Center, Right Wing, Defenseman, Right Wing, Center, Defenseman, Defenseman, Defenseman, Defenseman, Defenseman, Center, Left Wing, Right Wing, Left Wing, Right Wing, Right Wing, Defenseman, Goalie, Goalie, Goalie, Forward, Defenseman, Defenseman, Forward, Forward, Defenseman, Defenseman, Forward, Forward, Forward, Forward, Forward, Forward, Forward, Forward, Defenseman, Forward, Forward, Defenseman, Defenseman, Defenseman, Defenseman, Defenseman, Forward, Forward, Forward, Forward, Forward, Forward, Defenseman, Goalie, Goalie, Goalie, C, D, D, C, C, D, D, C, LW, C, RW, C, LW, C, RW, D, RW, C, D, D, D, D, D, C, LW, RW, LW, RW, RW, D, G, G, G
    ## 19                                                                                                                                                                                                                                                                                                                                   40, 5, 6, 18, 22, 21, 12, 5, 22, 15, 39, 10, 4, 21, 41, 11, 8, 17, 7, 42, 10, 52, 23, NA, 79, 33, 13, 60, 26, 47, 77, 28, 23, 36, 45, 93, 31, 1, 37, 8467977, 8468674, 8469770, 8470162, 8470201, 8470714, 8470877, 8470966, 8471185, 8471222, 8471664, 8471682, 8471729, 8473673, 8474038, 8474150, 8474577, 8474642, 8474673, 8475213, 8475260, 8475650, 8475733, 8475829, 8475907, 8476244, 8476346, 8476440, 8476452, 8476466, 8476967, 8477486, 8477497, 8477591, 8477913, 8477935, 8471403, 8473972, 8475299, Brian McGrattan, Deryk Engelland, Dennis Wideman, Matt Stajan, Jiri Hudler, Corey Potter, David Jones, Mark Giordano, Curtis Glencross, Ladislav Smid, Mason Raymond, Devin Setoguchi, Kris Russell, David Schlemko, Paul Byron, Mikael Backlund, Joe Colborne, Lance Bouma, TJ Brodie, Drew Shore, Corban Knight, Brandon Bollig, Max Reinhart, John Ramage, Micheal Ferland, Raphael Diaz, Johnny Gaudreau, Markus Granlund, Tyler Wotherspoon, Sven Baertschi, Brett Kulak, Emile Poirier, Sean Monahan, Josh Jooris, David Wolf, Sam Bennett, Karri Ramo, Jonas Hiller, Joni Ortio, /api/v1/people/8467977, /api/v1/people/8468674, /api/v1/people/8469770, /api/v1/people/8470162, /api/v1/people/8470201, /api/v1/people/8470714, /api/v1/people/8470877, /api/v1/people/8470966, /api/v1/people/8471185, /api/v1/people/8471222, /api/v1/people/8471664, /api/v1/people/8471682, /api/v1/people/8471729, /api/v1/people/8473673, /api/v1/people/8474038, /api/v1/people/8474150, /api/v1/people/8474577, /api/v1/people/8474642, /api/v1/people/8474673, /api/v1/people/8475213, /api/v1/people/8475260, /api/v1/people/8475650, /api/v1/people/8475733, /api/v1/people/8475829, /api/v1/people/8475907, /api/v1/people/8476244, /api/v1/people/8476346, /api/v1/people/8476440, /api/v1/people/8476452, /api/v1/people/8476466, /api/v1/people/8476967, /api/v1/people/8477486, /api/v1/people/8477497, /api/v1/people/8477591, /api/v1/people/8477913, /api/v1/people/8477935, /api/v1/people/8471403, /api/v1/people/8473972, /api/v1/people/8475299, R, D, D, C, R, D, R, D, L, D, L, R, D, D, L, C, C, L, D, C, C, L, C, D, L, D, L, C, D, L, D, L, C, R, L, C, G, G, G, Right Wing, Defenseman, Defenseman, Center, Right Wing, Defenseman, Right Wing, Defenseman, Left Wing, Defenseman, Left Wing, Right Wing, Defenseman, Defenseman, Left Wing, Center, Center, Left Wing, Defenseman, Center, Center, Left Wing, Center, Defenseman, Left Wing, Defenseman, Left Wing, Center, Defenseman, Left Wing, Defenseman, Left Wing, Center, Right Wing, Left Wing, Center, Goalie, Goalie, Goalie, Forward, Defenseman, Defenseman, Forward, Forward, Defenseman, Forward, Defenseman, Forward, Defenseman, Forward, Forward, Defenseman, Defenseman, Forward, Forward, Forward, Forward, Defenseman, Forward, Forward, Forward, Forward, Defenseman, Forward, Defenseman, Forward, Forward, Defenseman, Forward, Defenseman, Forward, Forward, Forward, Forward, Forward, Goalie, Goalie, Goalie, RW, D, D, C, RW, D, RW, D, LW, D, LW, RW, D, D, LW, C, C, LW, D, C, C, LW, C, D, LW, D, LW, C, D, LW, D, LW, C, RW, LW, C, G, G, G
    ## 20                                                                                                                                                                                                                                                                                                                                                                                                                           12, 48, 17, 40, 25, 34, 8, 7, 58, 55, 24, 6, 88, 44, 34, 22, 38, 33, 90, 95, 94, 29, 81, 26, 38, 13, 23, 36, 92, 15, 37, 46, 29, 45, 71, 1, 40, 31, 8462042, 8464975, 8467331, 8467338, 8470171, 8470317, 8470699, 8470750, 8471326, 8471657, 8471718, 8473446, 8473465, 8473700, 8474008, 8474207, 8474743, 8475150, 8475158, 8475168, 8475197, 8475206, 8475266, 8475461, 8475767, 8475878, 8475958, 8476043, 8476455, 8476464, 8476536, 8476798, 8477492, 8477902, 8477916, 8473499, 8473575, 8475717, Jarome Iginla, Daniel Briere, Brad Stuart, Alex Tanguay, Max Talbot, Nate Guenin, Jan Hejda, John Mitchell, Patrick Bordeleau, Cody McLeod, Marc-Andre Cliche, Erik Johnson, Jamie McGinn, Ryan Wilson, Paul Carey, Nick Holden, Zach Redmond, Jordan Caron, Ryan O'Reilly, Matt Duchene, Tyson Barrie, Stefan Elliott, Tomas Vincour, Andrew Agozzino, Joey Hishon, Freddie Hamilton, Michael Sgarbossa, Ben Street, Gabriel Landeskog, Duncan Siemens, Colin Smith, Karl Stollery, Nathan MacKinnon, Dennis Everberg, Borna Rendulic, Reto Berra, Semyon Varlamov, Calvin Pickard, /api/v1/people/8462042, /api/v1/people/8464975, /api/v1/people/8467331, /api/v1/people/8467338, /api/v1/people/8470171, /api/v1/people/8470317, /api/v1/people/8470699, /api/v1/people/8470750, /api/v1/people/8471326, /api/v1/people/8471657, /api/v1/people/8471718, /api/v1/people/8473446, /api/v1/people/8473465, /api/v1/people/8473700, /api/v1/people/8474008, /api/v1/people/8474207, /api/v1/people/8474743, /api/v1/people/8475150, /api/v1/people/8475158, /api/v1/people/8475168, /api/v1/people/8475197, /api/v1/people/8475206, /api/v1/people/8475266, /api/v1/people/8475461, /api/v1/people/8475767, /api/v1/people/8475878, /api/v1/people/8475958, /api/v1/people/8476043, /api/v1/people/8476455, /api/v1/people/8476464, /api/v1/people/8476536, /api/v1/people/8476798, /api/v1/people/8477492, /api/v1/people/8477902, /api/v1/people/8477916, /api/v1/people/8473499, /api/v1/people/8473575, /api/v1/people/8475717, R, C, D, L, C, D, D, C, L, L, C, D, L, D, C, D, D, R, C, C, D, D, C, L, C, C, C, C, L, D, C, D, C, R, R, G, G, G, Right Wing, Center, Defenseman, Left Wing, Center, Defenseman, Defenseman, Center, Left Wing, Left Wing, Center, Defenseman, Left Wing, Defenseman, Center, Defenseman, Defenseman, Right Wing, Center, Center, Defenseman, Defenseman, Center, Left Wing, Center, Center, Center, Center, Left Wing, Defenseman, Center, Defenseman, Center, Right Wing, Right Wing, Goalie, Goalie, Goalie, Forward, Forward, Defenseman, Forward, Forward, Defenseman, Defenseman, Forward, Forward, Forward, Forward, Defenseman, Forward, Defenseman, Forward, Defenseman, Defenseman, Forward, Forward, Forward, Defenseman, Defenseman, Forward, Forward, Forward, Forward, Forward, Forward, Forward, Defenseman, Forward, Defenseman, Forward, Forward, Forward, Goalie, Goalie, Goalie, RW, C, D, LW, C, D, D, C, LW, LW, C, D, LW, D, C, D, D, RW, C, C, D, D, C, LW, C, C, C, C, LW, D, C, D, C, RW, RW, G, G, G
    ## 21 21, 15, 8, 27, 86, 67, 5, 48, 26, 6, 13, 9, 12, 17, 22, 57, 7, 4, 51, NA, 52, 70, 18, 91, 21, 33, 41, 26, 85, 93, 77, 77, 64, 72, 41, 25, 82, 29, 32, 40, 34, 30, 35, 8466333, 8468611, 8469485, 8470159, 8471348, 8471678, 8471816, 8472424, 8473507, 8473514, 8473911, 8473962, 8473970, 8473989, 8474048, 8474102, 8474586, 8474602, 8475165, 8475671, 8475716, 8475734, 8475752, 8475791, 8475869, 8476062, 8476179, 8476336, 8476426, 8476454, 8476472, 8476779, 8476855, 8477249, 8477410, 8477498, 8477851, 8477934, 8473614, 8475681, 8475781, 8476316, 8476839, Andrew Ference, Matt Hendricks, Derek Roy, Boyd Gordon, Nikita Nikitin, Benoit Pouliot, Mark Fayne, Ryan Hamilton, Jeff Petry, Jesse Joensuu, Steven Pinizzotto, Teddy Purcell, Rob Klinkhammer, Luke Gazdic, Keith Aulie, David Perron, Jordan Eberle, Justin Schultz, Anton Lander, Matt Fraser, Martin Marincin, Curtis Hamilton, Tyler Pitlick, Taylor Hall, Brandon Davidson, Mark Arcobello, Will Acton, Iiro Pakarinen, David Musil, Ryan Nugent-Hopkins, Oscar Klefbom, Brad Hunt, Nail Yakupov, Andrew Miller, Bogdan Yakimov, Darnell Nurse, Jordan Oesterle, Leon Draisaitl, Richard Bachman, Ben Scrivens, Tyler Bunz, Laurent Brossoit, Viktor Fasth, /api/v1/people/8466333, /api/v1/people/8468611, /api/v1/people/8469485, /api/v1/people/8470159, /api/v1/people/8471348, /api/v1/people/8471678, /api/v1/people/8471816, /api/v1/people/8472424, /api/v1/people/8473507, /api/v1/people/8473514, /api/v1/people/8473911, /api/v1/people/8473962, /api/v1/people/8473970, /api/v1/people/8473989, /api/v1/people/8474048, /api/v1/people/8474102, /api/v1/people/8474586, /api/v1/people/8474602, /api/v1/people/8475165, /api/v1/people/8475671, /api/v1/people/8475716, /api/v1/people/8475734, /api/v1/people/8475752, /api/v1/people/8475791, /api/v1/people/8475869, /api/v1/people/8476062, /api/v1/people/8476179, /api/v1/people/8476336, /api/v1/people/8476426, /api/v1/people/8476454, /api/v1/people/8476472, /api/v1/people/8476779, /api/v1/people/8476855, /api/v1/people/8477249, /api/v1/people/8477410, /api/v1/people/8477498, /api/v1/people/8477851, /api/v1/people/8477934, /api/v1/people/8473614, /api/v1/people/8475681, /api/v1/people/8475781, /api/v1/people/8476316, /api/v1/people/8476839, D, C, C, C, D, L, D, L, D, L, R, L, L, L, D, L, R, D, C, R, D, L, C, L, D, R, C, R, D, C, D, D, R, C, C, D, D, C, G, G, G, G, G, Defenseman, Center, Center, Center, Defenseman, Left Wing, Defenseman, Left Wing, Defenseman, Left Wing, Right Wing, Left Wing, Left Wing, Left Wing, Defenseman, Left Wing, Right Wing, Defenseman, Center, Right Wing, Defenseman, Left Wing, Center, Left Wing, Defenseman, Right Wing, Center, Right Wing, Defenseman, Center, Defenseman, Defenseman, Right Wing, Center, Center, Defenseman, Defenseman, Center, Goalie, Goalie, Goalie, Goalie, Goalie, Defenseman, Forward, Forward, Forward, Defenseman, Forward, Defenseman, Forward, Defenseman, Forward, Forward, Forward, Forward, Forward, Defenseman, Forward, Forward, Defenseman, Forward, Forward, Defenseman, Forward, Forward, Forward, Defenseman, Forward, Forward, Forward, Defenseman, Forward, Defenseman, Defenseman, Forward, Forward, Forward, Defenseman, Defenseman, Forward, Goalie, Goalie, Goalie, Goalie, Goalie, D, C, C, C, D, LW, D, LW, D, LW, RW, LW, LW, LW, D, LW, RW, D, C, RW, D, LW, C, LW, D, RW, C, RW, D, C, D, D, RW, C, C, D, D, C, G, G, G, G, G
    ## 22                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                22, 33, 71, 5, 3, 20, 14, 15, 23, 36, 3, NA, 25, 16, 13, 7, 5, 21, 44, 16, 20, 8, 43, 18, 6, 47, 39, 53, 41, 30, 25, 31, 8467875, 8467876, 8468085, 8469465, 8469598, 8470274, 8470358, 8470755, 8471303, 8471498, 8473415, 8473432, 8473542, 8473574, 8474009, 8474134, 8474579, 8474646, 8475178, 8475223, 8475598, 8475690, 8476214, 8476302, 8476431, 8476466, 8476482, 8477500, 8477589, 8468011, 8474593, 8475663, Daniel Sedin, Henrik Sedin, Radim Vrbata, Dan Hamhuis, Kevin Bieksa, Chris Higgins, Alexandre Burrows, Brad Richardson, Alexander Edler, Jannik Hansen, Alex Biega, Derek Dorsett, Tom Sestito, Shawn Matthias, Nick Bonino, Yannick Weber, Luca Sbisa, Brandon McMillan, Zack Kassian, Linden Vey, Ryan Stanton, Christopher Tanev, Brandon DeFazio, Frank Corrado, Adam Clendening, Sven Baertschi, Nicklas Jensen, Bo Horvat, Ronalds Kenins, Ryan Miller, Jacob Markstrom, Eddie Lack, /api/v1/people/8467875, /api/v1/people/8467876, /api/v1/people/8468085, /api/v1/people/8469465, /api/v1/people/8469598, /api/v1/people/8470274, /api/v1/people/8470358, /api/v1/people/8470755, /api/v1/people/8471303, /api/v1/people/8471498, /api/v1/people/8473415, /api/v1/people/8473432, /api/v1/people/8473542, /api/v1/people/8473574, /api/v1/people/8474009, /api/v1/people/8474134, /api/v1/people/8474579, /api/v1/people/8474646, /api/v1/people/8475178, /api/v1/people/8475223, /api/v1/people/8475598, /api/v1/people/8475690, /api/v1/people/8476214, /api/v1/people/8476302, /api/v1/people/8476431, /api/v1/people/8476466, /api/v1/people/8476482, /api/v1/people/8477500, /api/v1/people/8477589, /api/v1/people/8468011, /api/v1/people/8474593, /api/v1/people/8475663, L, C, R, D, D, L, R, C, D, R, D, R, L, L, C, D, D, L, R, R, D, D, L, D, D, L, R, C, L, G, G, G, Left Wing, Center, Right Wing, Defenseman, Defenseman, Left Wing, Right Wing, Center, Defenseman, Right Wing, Defenseman, Right Wing, Left Wing, Left Wing, Center, Defenseman, Defenseman, Left Wing, Right Wing, Right Wing, Defenseman, Defenseman, Left Wing, Defenseman, Defenseman, Left Wing, Right Wing, Center, Left Wing, Goalie, Goalie, Goalie, Forward, Forward, Forward, Defenseman, Defenseman, Forward, Forward, Forward, Defenseman, Forward, Defenseman, Forward, Forward, Forward, Forward, Defenseman, Defenseman, Forward, Forward, Forward, Defenseman, Defenseman, Forward, Defenseman, Defenseman, Forward, Forward, Forward, Forward, Goalie, Goalie, Goalie, LW, C, RW, D, D, LW, RW, C, D, RW, D, RW, LW, LW, C, D, D, LW, RW, RW, D, D, LW, D, D, LW, RW, C, LW, G, G, G
    ## 23                                                                                                                                                                                                          2, 6, 23, 15, NA, 12, 21, 15, 17, 10, 44, 4, 28, 17, 11, 39, 21, 14, 48, 21, 6, 5, 33, 77, 45, 25, 4, 26, 14, 42, 49, 71, 11, 67, 47, 36, 45, 80, 31, 36, 8466142, 8467332, 8467400, 8468482, 8469490, 8470039, 8470222, 8470612, 8470616, 8470621, 8470775, 8470886, 8471241, 8471504, 8471699, 8473492, 8473933, 8474034, 8474606, 8475151, 8475155, 8475162, 8475164, 8475203, 8475222, 8475758, 8475764, 8475770, 8475780, 8476312, 8476384, 8476448, 8476474, 8476483, 8476854, 8478137, 8467391, 8468524, 8475883, 8476434, Eric Brewer, Bryan Allen, Francois Beauchemin, Dany Heatley, Tim Jackman, Tomas Fleischmann, James Wisniewski, Ryan Getzlaf, Ryan Kesler, Corey Perry, Nate Thompson, Clayton Stoner, Mark Fistric, Rene Bourque, Andrew Cogliano, Matt Beleskey, Ben Lovejoy, Pat Maroon, Colby Robak, Kyle Palmieri, Simon Despres, Mat Clark, Jakob Silfverberg, Jesse Blacker, Sami Vatanen, Devante Smith-Pelly, Cam Fowler, Emerson Etem, Chris Wagner, Josh Manson, Max Friberg, William Karlsson, Stefan Noesen, Rickard Rakell, Hampus Lindholm, Jiri Sekac, Jason LaBarbera, Ilya Bryzgalov, Frederik Andersen, John Gibson, /api/v1/people/8466142, /api/v1/people/8467332, /api/v1/people/8467400, /api/v1/people/8468482, /api/v1/people/8469490, /api/v1/people/8470039, /api/v1/people/8470222, /api/v1/people/8470612, /api/v1/people/8470616, /api/v1/people/8470621, /api/v1/people/8470775, /api/v1/people/8470886, /api/v1/people/8471241, /api/v1/people/8471504, /api/v1/people/8471699, /api/v1/people/8473492, /api/v1/people/8473933, /api/v1/people/8474034, /api/v1/people/8474606, /api/v1/people/8475151, /api/v1/people/8475155, /api/v1/people/8475162, /api/v1/people/8475164, /api/v1/people/8475203, /api/v1/people/8475222, /api/v1/people/8475758, /api/v1/people/8475764, /api/v1/people/8475770, /api/v1/people/8475780, /api/v1/people/8476312, /api/v1/people/8476384, /api/v1/people/8476448, /api/v1/people/8476474, /api/v1/people/8476483, /api/v1/people/8476854, /api/v1/people/8478137, /api/v1/people/8467391, /api/v1/people/8468524, /api/v1/people/8475883, /api/v1/people/8476434, D, D, D, L, R, L, D, C, C, R, C, D, D, R, C, L, D, L, D, R, D, D, R, D, D, R, D, R, R, D, R, C, R, L, D, L, G, G, G, G, Defenseman, Defenseman, Defenseman, Left Wing, Right Wing, Left Wing, Defenseman, Center, Center, Right Wing, Center, Defenseman, Defenseman, Right Wing, Center, Left Wing, Defenseman, Left Wing, Defenseman, Right Wing, Defenseman, Defenseman, Right Wing, Defenseman, Defenseman, Right Wing, Defenseman, Right Wing, Right Wing, Defenseman, Right Wing, Center, Right Wing, Left Wing, Defenseman, Left Wing, Goalie, Goalie, Goalie, Goalie, Defenseman, Defenseman, Defenseman, Forward, Forward, Forward, Defenseman, Forward, Forward, Forward, Forward, Defenseman, Defenseman, Forward, Forward, Forward, Defenseman, Forward, Defenseman, Forward, Defenseman, Defenseman, Forward, Defenseman, Defenseman, Forward, Defenseman, Forward, Forward, Defenseman, Forward, Forward, Forward, Forward, Defenseman, Forward, Goalie, Goalie, Goalie, Goalie, D, D, D, LW, RW, LW, D, C, C, RW, C, D, D, RW, C, LW, D, LW, D, RW, D, D, RW, D, D, RW, D, RW, RW, D, RW, C, RW, LW, D, LW, G, G, G, G
    ## 24                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            55, 72, 22, 27, 19, 83, 83, 83, 18, 33, 39, 21, 14, 7, 55, 4, 26, 20, 7, 11, 4, 22, 91, NA, 3, 16, 23, 18, 2, 13, 32, 31, 39, 40, 8458951, 8467396, 8467423, 8468635, 8469455, 8469466, 8469759, 8470110, 8470622, 8471274, 8471474, 8473673, 8473994, 8474098, 8474218, 8474818, 8474849, 8475236, 8475246, 8475310, 8475455, 8475747, 8475794, 8475896, 8475906, 8476116, 8476279, 8476439, 8476467, 8477501, 8470140, 8473523, 8474765, 8475680, Sergei Gonchar, Erik Cole, Shawn Horcoff, Travis Moen, Jason Spezza, Ales Hemsky, Vernon Fiddler, Trevor Daley, Patrick Eaves, Alex Goligoski, Travis Morin, David Schlemko, Jamie Benn, Colton Sceviour, Jason Demers, Jordie Benn, Antoine Roussel, Cody Eakin, Kevin Connauton, Curtis McKenzie, Brenden Dillon, Patrik Nemeth, Tyler Seguin, Brendan Ranford, John Klingberg, Ryan Garbutt, Jyrki Jokipakka, Brett Ritchie, Jamie Oleksiak, Valeri Nichushkin, Kari Lehtonen, Jhonas Enroth, Anders Lindback, Jussi Rynnas, /api/v1/people/8458951, /api/v1/people/8467396, /api/v1/people/8467423, /api/v1/people/8468635, /api/v1/people/8469455, /api/v1/people/8469466, /api/v1/people/8469759, /api/v1/people/8470110, /api/v1/people/8470622, /api/v1/people/8471274, /api/v1/people/8471474, /api/v1/people/8473673, /api/v1/people/8473994, /api/v1/people/8474098, /api/v1/people/8474218, /api/v1/people/8474818, /api/v1/people/8474849, /api/v1/people/8475236, /api/v1/people/8475246, /api/v1/people/8475310, /api/v1/people/8475455, /api/v1/people/8475747, /api/v1/people/8475794, /api/v1/people/8475896, /api/v1/people/8475906, /api/v1/people/8476116, /api/v1/people/8476279, /api/v1/people/8476439, /api/v1/people/8476467, /api/v1/people/8477501, /api/v1/people/8470140, /api/v1/people/8473523, /api/v1/people/8474765, /api/v1/people/8475680, D, L, C, L, C, R, C, D, R, D, C, D, L, C, D, D, L, C, D, L, D, D, C, L, D, C, D, R, D, R, G, G, G, G, Defenseman, Left Wing, Center, Left Wing, Center, Right Wing, Center, Defenseman, Right Wing, Defenseman, Center, Defenseman, Left Wing, Center, Defenseman, Defenseman, Left Wing, Center, Defenseman, Left Wing, Defenseman, Defenseman, Center, Left Wing, Defenseman, Center, Defenseman, Right Wing, Defenseman, Right Wing, Goalie, Goalie, Goalie, Goalie, Defenseman, Forward, Forward, Forward, Forward, Forward, Forward, Defenseman, Forward, Defenseman, Forward, Defenseman, Forward, Forward, Defenseman, Defenseman, Forward, Forward, Defenseman, Forward, Defenseman, Defenseman, Forward, Forward, Defenseman, Forward, Defenseman, Forward, Defenseman, Forward, Goalie, Goalie, Goalie, Goalie, D, LW, C, LW, C, RW, C, D, RW, D, C, D, LW, C, D, D, LW, C, D, LW, D, D, C, LW, D, C, D, RW, D, RW, G, G, G, G
    ## 25                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          44, 12, 14, 19, 2, 36, 77, 23, 10, 55, 5, 11, 22, 2, 21, 8, 23, 8, 26, 73, 3, 71, 73, 10, 21, 70, 32, 31, 8467344, 8468483, 8468508, 8468526, 8470121, 8470189, 8470604, 8470606, 8470617, 8471240, 8471284, 8471685, 8473453, 8473571, 8474100, 8474162, 8474166, 8474563, 8474594, 8475160, 8475188, 8475325, 8475726, 8476404, 8476406, 8476871, 8471734, 8474889, Robyn Regehr, Marian Gaborik, Justin Williams, Jarret Stoll, Matt Greene, David Van Der Gulik, Jeff Carter, Dustin Brown, Mike Richards, Jeff Schultz, Andrej Sekera, Anze Kopitar, Trevor Lewis, Jamie McBain, Dwight King, Jake Muzzin, Alec Martinez, Drew Doughty, Slava Voynov, Kyle Clifford, Brayden McNabb, Jordan Nolan, Tyler Toffoli, Andy Andreoff, Nicholas Shore, Tanner Pearson, Jonathan Quick, Martin Jones, /api/v1/people/8467344, /api/v1/people/8468483, /api/v1/people/8468508, /api/v1/people/8468526, /api/v1/people/8470121, /api/v1/people/8470189, /api/v1/people/8470604, /api/v1/people/8470606, /api/v1/people/8470617, /api/v1/people/8471240, /api/v1/people/8471284, /api/v1/people/8471685, /api/v1/people/8473453, /api/v1/people/8473571, /api/v1/people/8474100, /api/v1/people/8474162, /api/v1/people/8474166, /api/v1/people/8474563, /api/v1/people/8474594, /api/v1/people/8475160, /api/v1/people/8475188, /api/v1/people/8475325, /api/v1/people/8475726, /api/v1/people/8476404, /api/v1/people/8476406, /api/v1/people/8476871, /api/v1/people/8471734, /api/v1/people/8474889, D, R, R, C, D, L, C, R, C, D, D, C, C, D, L, D, D, D, D, L, D, C, R, C, C, L, G, G, Defenseman, Right Wing, Right Wing, Center, Defenseman, Left Wing, Center, Right Wing, Center, Defenseman, Defenseman, Center, Center, Defenseman, Left Wing, Defenseman, Defenseman, Defenseman, Defenseman, Left Wing, Defenseman, Center, Right Wing, Center, Center, Left Wing, Goalie, Goalie, Defenseman, Forward, Forward, Forward, Defenseman, Forward, Forward, Forward, Forward, Defenseman, Defenseman, Forward, Forward, Defenseman, Forward, Defenseman, Defenseman, Defenseman, Defenseman, Forward, Defenseman, Forward, Forward, Forward, Forward, Forward, Goalie, Goalie, D, RW, RW, C, D, LW, C, RW, C, D, D, C, C, D, LW, D, D, D, D, LW, D, C, RW, C, C, LW, G, G
    ## 26                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   19, 12, 27, 37, 88, 16, 48, 13, 44, 11, 45, 22, 61, 39, 55, 38, 11, 18, 57, 4, 52, 44, 13, 42, 83, NA, 19, 46, 7, 48, 71, 76, 25, 68, 32, 37, 1, 8466138, 8466139, 8466158, 8470063, 8470613, 8470794, 8471311, 8471371, 8471709, 8471956, 8473536, 8473722, 8474027, 8474053, 8474218, 8474230, 8474516, 8474727, 8474739, 8475455, 8475625, 8475775, 8475878, 8476166, 8476442, 8476549, 8476624, 8476798, 8476807, 8476881, 8476919, 8477227, 8477509, 8477922, 8471774, 8474550, 8477234, Joe Thornton, Patrick Marleau, Scott Hannan, Adam Burish, Brent Burns, Joe Pavelski, Tyler Kennedy, Mike Brown, Marc-Edouard Vlasic, Andrew Desjardins, James Sheppard, John Scott, Justin Braun, Logan Couture, Jason Demers, Micheal Haley, Bryan Lerg, Ben Smith, Tommy Wingels, Brenden Dillon, Matt Irwin, Tye McGinn, Freddie Hamilton, Taylor Fedun, Matt Nieto, Daniil Tarasov, Barclay Goodrow, Karl Stollery, Matt Tennyson, Tomas Hertl, Chris Tierney, Eriah Hayes, Mirco Mueller, Melker Karlsson, Alex Stalock, Antti Niemi, Troy Grosenick, /api/v1/people/8466138, /api/v1/people/8466139, /api/v1/people/8466158, /api/v1/people/8470063, /api/v1/people/8470613, /api/v1/people/8470794, /api/v1/people/8471311, /api/v1/people/8471371, /api/v1/people/8471709, /api/v1/people/8471956, /api/v1/people/8473536, /api/v1/people/8473722, /api/v1/people/8474027, /api/v1/people/8474053, /api/v1/people/8474218, /api/v1/people/8474230, /api/v1/people/8474516, /api/v1/people/8474727, /api/v1/people/8474739, /api/v1/people/8475455, /api/v1/people/8475625, /api/v1/people/8475775, /api/v1/people/8475878, /api/v1/people/8476166, /api/v1/people/8476442, /api/v1/people/8476549, /api/v1/people/8476624, /api/v1/people/8476798, /api/v1/people/8476807, /api/v1/people/8476881, /api/v1/people/8476919, /api/v1/people/8477227, /api/v1/people/8477509, /api/v1/people/8477922, /api/v1/people/8471774, /api/v1/people/8474550, /api/v1/people/8477234, C, C, D, R, D, C, C, R, D, L, C, L, D, C, D, C, C, R, C, D, D, L, C, D, L, R, C, D, D, C, C, R, D, C, G, G, G, Center, Center, Defenseman, Right Wing, Defenseman, Center, Center, Right Wing, Defenseman, Left Wing, Center, Left Wing, Defenseman, Center, Defenseman, Center, Center, Right Wing, Center, Defenseman, Defenseman, Left Wing, Center, Defenseman, Left Wing, Right Wing, Center, Defenseman, Defenseman, Center, Center, Right Wing, Defenseman, Center, Goalie, Goalie, Goalie, Forward, Forward, Defenseman, Forward, Defenseman, Forward, Forward, Forward, Defenseman, Forward, Forward, Forward, Defenseman, Forward, Defenseman, Forward, Forward, Forward, Forward, Defenseman, Defenseman, Forward, Forward, Defenseman, Forward, Forward, Forward, Defenseman, Defenseman, Forward, Forward, Forward, Defenseman, Forward, Goalie, Goalie, Goalie, C, C, D, RW, D, C, C, RW, D, LW, C, LW, D, C, D, C, C, RW, C, D, D, LW, C, D, LW, RW, C, D, D, C, C, RW, D, C, G, G, G
    ## 27                                                                                    33, 17, 51, 12, 21, 71, 17, 22, 17, 3, 9, 40, 71, 51, 28, 22, 42, 34, 41, 26, NA, 11, 13, 53, 5, 44, 33, 58, 7, 92, 26, 29, 38, 71, 27, 77, 56, 10, 39, 35, 72, 31, 8467917, 8468486, 8469492, 8470065, 8470222, 8470920, 8471273, 8471490, 8471504, 8471677, 8471681, 8471766, 8473422, 8473573, 8473647, 8473914, 8473992, 8474095, 8474130, 8474597, 8474609, 8474685, 8474715, 8474744, 8474774, 8475148, 8475214, 8475233, 8475246, 8475793, 8475808, 8476207, 8476432, 8476448, 8476850, 8476981, 8477448, 8477505, 8477510, 8470147, 8475683, 8476341, Jordan Leopold, Scott Hartnell, Fedor Tyutin, Ryan Craig, James Wisniewski, David Clarkson, Brandon Dubinsky, Adam Cracknell, Rene Bourque, Jack Johnson, Jack Skille, Jared Boll, Nick Foligno, Artem Anisimov, Frédéric St-Denis, Mark Letestu, Justin Falk, Dana Tyrell, Corey Tropp, Cody Goloubef, Luke Adam, Matt Calvert, Cam Atkinson, Sean Collins, Dalton Prout, Tim Erixon, Jeremy Morin, David Savard, Kevin Connauton, Ryan Johansen, Michael Chaput, Brian Gibbons, Boone Jenner, William Karlsson, Ryan Murray, Josh Anderson, Marko Dano, Alexander Wennberg, Kerby Rychel, Curtis McElhinney, Sergei Bobrovsky, Anton Forsberg, /api/v1/people/8467917, /api/v1/people/8468486, /api/v1/people/8469492, /api/v1/people/8470065, /api/v1/people/8470222, /api/v1/people/8470920, /api/v1/people/8471273, /api/v1/people/8471490, /api/v1/people/8471504, /api/v1/people/8471677, /api/v1/people/8471681, /api/v1/people/8471766, /api/v1/people/8473422, /api/v1/people/8473573, /api/v1/people/8473647, /api/v1/people/8473914, /api/v1/people/8473992, /api/v1/people/8474095, /api/v1/people/8474130, /api/v1/people/8474597, /api/v1/people/8474609, /api/v1/people/8474685, /api/v1/people/8474715, /api/v1/people/8474744, /api/v1/people/8474774, /api/v1/people/8475148, /api/v1/people/8475214, /api/v1/people/8475233, /api/v1/people/8475246, /api/v1/people/8475793, /api/v1/people/8475808, /api/v1/people/8476207, /api/v1/people/8476432, /api/v1/people/8476448, /api/v1/people/8476850, /api/v1/people/8476981, /api/v1/people/8477448, /api/v1/people/8477505, /api/v1/people/8477510, /api/v1/people/8470147, /api/v1/people/8475683, /api/v1/people/8476341, D, L, D, C, D, R, C, R, R, D, R, R, L, C, D, C, D, C, R, D, C, L, R, C, D, D, L, D, D, C, C, C, C, C, D, R, L, C, L, G, G, G, Defenseman, Left Wing, Defenseman, Center, Defenseman, Right Wing, Center, Right Wing, Right Wing, Defenseman, Right Wing, Right Wing, Left Wing, Center, Defenseman, Center, Defenseman, Center, Right Wing, Defenseman, Center, Left Wing, Right Wing, Center, Defenseman, Defenseman, Left Wing, Defenseman, Defenseman, Center, Center, Center, Center, Center, Defenseman, Right Wing, Left Wing, Center, Left Wing, Goalie, Goalie, Goalie, Defenseman, Forward, Defenseman, Forward, Defenseman, Forward, Forward, Forward, Forward, Defenseman, Forward, Forward, Forward, Forward, Defenseman, Forward, Defenseman, Forward, Forward, Defenseman, Forward, Forward, Forward, Forward, Defenseman, Defenseman, Forward, Defenseman, Defenseman, Forward, Forward, Forward, Forward, Forward, Defenseman, Forward, Forward, Forward, Forward, Goalie, Goalie, Goalie, D, LW, D, C, D, RW, C, RW, RW, D, RW, RW, LW, C, D, C, D, C, RW, D, C, LW, RW, C, D, D, LW, D, D, C, C, C, C, C, D, RW, LW, C, LW, G, G, G
    ## 28                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         24, 33, 9, 29, 19, 2, 23, 26, 20, 11, 28, 27, 44, 18, 42, 7, 6, 46, 4, 11, 56, 39, 16, 13, 64, 21, 14, 44, 25, 24, 32, 40, 32, 33, 35, 8465951, 8467917, 8469459, 8469506, 8469542, 8470054, 8470176, 8470598, 8470600, 8470610, 8470803, 8471840, 8473485, 8473646, 8473992, 8474164, 8474618, 8474716, 8474772, 8475147, 8475287, 8475613, 8475722, 8475745, 8475798, 8475799, 8476235, 8476344, 8476463, 8476856, 8477850, 8471227, 8473404, 8474202, 8475311, Matt Cooke, Jordan Leopold, Mikko Koivu, Jason Pominville, Stephane Veilleux, Keith Ballard, Sean Bergenheim, Thomas Vanek, Ryan Suter, Zach Parise, Kyle Brodziak, Brett Sutter, Chris Stewart, Ryan Carter, Justin Falk, Jonathon Blum, Marco Scandella, Jared Spurgeon, Stu Bickel, Jordan Schroeder, Erik Haula, Nate Prosser, Jason Zucker, Charlie Coyle, Mikael Granlund, Nino Niederreiter, Justin Fontaine, Tyler Graovac, Jonas Brodin, Matt Dumba, Christian Folin, Devan Dubnyk, Niklas Backstrom, John Curry, Darcy Kuemper, /api/v1/people/8465951, /api/v1/people/8467917, /api/v1/people/8469459, /api/v1/people/8469506, /api/v1/people/8469542, /api/v1/people/8470054, /api/v1/people/8470176, /api/v1/people/8470598, /api/v1/people/8470600, /api/v1/people/8470610, /api/v1/people/8470803, /api/v1/people/8471840, /api/v1/people/8473485, /api/v1/people/8473646, /api/v1/people/8473992, /api/v1/people/8474164, /api/v1/people/8474618, /api/v1/people/8474716, /api/v1/people/8474772, /api/v1/people/8475147, /api/v1/people/8475287, /api/v1/people/8475613, /api/v1/people/8475722, /api/v1/people/8475745, /api/v1/people/8475798, /api/v1/people/8475799, /api/v1/people/8476235, /api/v1/people/8476344, /api/v1/people/8476463, /api/v1/people/8476856, /api/v1/people/8477850, /api/v1/people/8471227, /api/v1/people/8473404, /api/v1/people/8474202, /api/v1/people/8475311, L, D, C, R, L, D, L, L, D, L, C, L, R, L, D, D, D, D, D, C, L, D, L, C, C, R, R, C, D, D, D, G, G, G, G, Left Wing, Defenseman, Center, Right Wing, Left Wing, Defenseman, Left Wing, Left Wing, Defenseman, Left Wing, Center, Left Wing, Right Wing, Left Wing, Defenseman, Defenseman, Defenseman, Defenseman, Defenseman, Center, Left Wing, Defenseman, Left Wing, Center, Center, Right Wing, Right Wing, Center, Defenseman, Defenseman, Defenseman, Goalie, Goalie, Goalie, Goalie, Forward, Defenseman, Forward, Forward, Forward, Defenseman, Forward, Forward, Defenseman, Forward, Forward, Forward, Forward, Forward, Defenseman, Defenseman, Defenseman, Defenseman, Defenseman, Forward, Forward, Defenseman, Forward, Forward, Forward, Forward, Forward, Forward, Defenseman, Defenseman, Defenseman, Goalie, Goalie, Goalie, Goalie, LW, D, C, RW, LW, D, LW, LW, D, LW, C, LW, RW, LW, D, D, D, D, D, C, LW, D, LW, C, C, RW, RW, C, D, D, D, G, G, G, G
    ## 29                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 22, NA, 19, 5, 18, 39, 33, 16, 26, 18, 5, 24, 18, 9, 67, 85, 21, 16, 14, 15, 7, 24, 57, NA, 28, NA, 9, 8, 49, 17, 55, 8, 9, 31, 35, 8469501, 8469531, 8470289, 8470614, 8470740, 8470828, 8470834, 8471217, 8471218, 8471226, 8471385, 8471482, 8473412, 8473482, 8473564, 8473618, 8474001, 8474061, 8474074, 8474094, 8474184, 8474567, 8474574, 8474603, 8474616, 8475159, 8475169, 8475279, 8475567, 8476392, 8476460, 8476885, 8477429, 8471715, 8474636, Chris Thorburn, Jay Harrison, Jim Slater, Mark Stuart, Lee Stempniak, Toby Enstrom, Dustin Byfuglien, Andrew Ladd, Blake Wheeler, Drew Stafford, Adam Pardy, Grant Clitsome, Bryan Little, Jiri Tlusty, Michael Frolik, Mathieu Perreault, TJ Galiardi, Anthony Peluso, Paul Postma, Matt Halischuk, Keaton Ellerby, Zach Bogosian, Tyler Myers, Eric O'Dell, Patrice Cormier, Carl Klingberg, Evander Kane, Ben Chiarot, Julien Brouillette, Adam Lowry, Mark Scheifele, Jacob Trouba, Andrew Copp, Ondrej Pavelec, Michael Hutchinson, /api/v1/people/8469501, /api/v1/people/8469531, /api/v1/people/8470289, /api/v1/people/8470614, /api/v1/people/8470740, /api/v1/people/8470828, /api/v1/people/8470834, /api/v1/people/8471217, /api/v1/people/8471218, /api/v1/people/8471226, /api/v1/people/8471385, /api/v1/people/8471482, /api/v1/people/8473412, /api/v1/people/8473482, /api/v1/people/8473564, /api/v1/people/8473618, /api/v1/people/8474001, /api/v1/people/8474061, /api/v1/people/8474074, /api/v1/people/8474094, /api/v1/people/8474184, /api/v1/people/8474567, /api/v1/people/8474574, /api/v1/people/8474603, /api/v1/people/8474616, /api/v1/people/8475159, /api/v1/people/8475169, /api/v1/people/8475279, /api/v1/people/8475567, /api/v1/people/8476392, /api/v1/people/8476460, /api/v1/people/8476885, /api/v1/people/8477429, /api/v1/people/8471715, /api/v1/people/8474636, R, D, C, D, R, D, D, L, R, R, D, D, C, L, R, L, L, R, D, R, D, D, D, C, C, L, L, D, D, C, C, D, C, G, G, Right Wing, Defenseman, Center, Defenseman, Right Wing, Defenseman, Defenseman, Left Wing, Right Wing, Right Wing, Defenseman, Defenseman, Center, Left Wing, Right Wing, Left Wing, Left Wing, Right Wing, Defenseman, Right Wing, Defenseman, Defenseman, Defenseman, Center, Center, Left Wing, Left Wing, Defenseman, Defenseman, Center, Center, Defenseman, Center, Goalie, Goalie, Forward, Defenseman, Forward, Defenseman, Forward, Defenseman, Defenseman, Forward, Forward, Forward, Defenseman, Defenseman, Forward, Forward, Forward, Forward, Forward, Forward, Defenseman, Forward, Defenseman, Defenseman, Defenseman, Forward, Forward, Forward, Forward, Defenseman, Defenseman, Forward, Forward, Defenseman, Forward, Goalie, Goalie, RW, D, C, D, RW, D, D, LW, RW, RW, D, D, C, LW, RW, LW, LW, RW, D, RW, D, D, D, C, C, LW, LW, D, D, C, C, D, C, G, G
    ## 30                                                            19, 10, 50, 18, 4, 44, 49, 46, 24, 29, 40, 3, 97, 55, 21, 12, 89, 89, 26, 6, 21, 37, 23, 27, 26, 27, 22, NA, 44, 39, 33, 16, 6, 53, 5, 55, 48, 32, 41, 56, 40, 30, 8462038, 8468064, 8468535, 8469664, 8469760, 8470647, 8470719, 8470798, 8471231, 8471232, 8471691, 8471735, 8471856, 8473589, 8473673, 8473970, 8474040, 8474571, 8474628, 8474638, 8474646, 8474793, 8475171, 8475186, 8475194, 8475224, 8475414, 8475759, 8475775, 8475996, 8476062, 8476356, 8476403, 8476451, 8476473, 8476868, 8476921, 8477715, 8469608, 8470093, 8471227, 8475839, Shane Doan, Martin Erat, Antoine Vermette, David Moss, Zbynek Michalek, B.J. Crombeen, Alexandre Bolduc, Dylan Reese, Kyle Chipchura, Lauri Korpikoski, Martin Hanzal, Keith Yandle, Joe Vitale, Chris Summers, David Schlemko, Rob Klinkhammer, Sam Gagner, Mikkel Boedker, Michael Stone, Andrew Campbell, Brandon McMillan, Justin Hodgman, Oliver Ekman-Larsson, John Moore, Philip Samuelsson, Jordan Szwarz, Craig Cunningham, Brandon Gormley, Tye McGinn, Brendan Shinnimin, Mark Arcobello, Tobias Rieder, Klas Dahlbeck, Lucas Lessio, Connor Murphy, Henrik Samuelsson, Jordan Martinook, Tyler Gaudet, Mike Smith, Mike McKenna, Devan Dubnyk, Louis Domingue, /api/v1/people/8462038, /api/v1/people/8468064, /api/v1/people/8468535, /api/v1/people/8469664, /api/v1/people/8469760, /api/v1/people/8470647, /api/v1/people/8470719, /api/v1/people/8470798, /api/v1/people/8471231, /api/v1/people/8471232, /api/v1/people/8471691, /api/v1/people/8471735, /api/v1/people/8471856, /api/v1/people/8473589, /api/v1/people/8473673, /api/v1/people/8473970, /api/v1/people/8474040, /api/v1/people/8474571, /api/v1/people/8474628, /api/v1/people/8474638, /api/v1/people/8474646, /api/v1/people/8474793, /api/v1/people/8475171, /api/v1/people/8475186, /api/v1/people/8475194, /api/v1/people/8475224, /api/v1/people/8475414, /api/v1/people/8475759, /api/v1/people/8475775, /api/v1/people/8475996, /api/v1/people/8476062, /api/v1/people/8476356, /api/v1/people/8476403, /api/v1/people/8476451, /api/v1/people/8476473, /api/v1/people/8476868, /api/v1/people/8476921, /api/v1/people/8477715, /api/v1/people/8469608, /api/v1/people/8470093, /api/v1/people/8471227, /api/v1/people/8475839, R, R, C, R, D, R, C, D, C, L, C, D, C, D, D, L, C, L, D, D, L, C, D, D, D, C, L, D, L, C, R, C, D, L, D, C, L, C, G, G, G, G, Right Wing, Right Wing, Center, Right Wing, Defenseman, Right Wing, Center, Defenseman, Center, Left Wing, Center, Defenseman, Center, Defenseman, Defenseman, Left Wing, Center, Left Wing, Defenseman, Defenseman, Left Wing, Center, Defenseman, Defenseman, Defenseman, Center, Left Wing, Defenseman, Left Wing, Center, Right Wing, Center, Defenseman, Left Wing, Defenseman, Center, Left Wing, Center, Goalie, Goalie, Goalie, Goalie, Forward, Forward, Forward, Forward, Defenseman, Forward, Forward, Defenseman, Forward, Forward, Forward, Defenseman, Forward, Defenseman, Defenseman, Forward, Forward, Forward, Defenseman, Defenseman, Forward, Forward, Defenseman, Defenseman, Defenseman, Forward, Forward, Defenseman, Forward, Forward, Forward, Forward, Defenseman, Forward, Defenseman, Forward, Forward, Forward, Goalie, Goalie, Goalie, Goalie, RW, RW, C, RW, D, RW, C, D, C, LW, C, D, C, D, D, LW, C, LW, D, D, LW, C, D, D, D, C, LW, D, LW, C, RW, C, D, LW, D, C, LW, C, G, G, G, G
    ##                roster.link
    ## 1   /api/v1/teams/1/roster
    ## 2   /api/v1/teams/2/roster
    ## 3   /api/v1/teams/3/roster
    ## 4   /api/v1/teams/4/roster
    ## 5   /api/v1/teams/5/roster
    ## 6   /api/v1/teams/6/roster
    ## 7   /api/v1/teams/7/roster
    ## 8   /api/v1/teams/8/roster
    ## 9   /api/v1/teams/9/roster
    ## 10 /api/v1/teams/10/roster
    ## 11 /api/v1/teams/12/roster
    ## 12 /api/v1/teams/13/roster
    ## 13 /api/v1/teams/14/roster
    ## 14 /api/v1/teams/15/roster
    ## 15 /api/v1/teams/16/roster
    ## 16 /api/v1/teams/17/roster
    ## 17 /api/v1/teams/18/roster
    ## 18 /api/v1/teams/19/roster
    ## 19 /api/v1/teams/20/roster
    ## 20 /api/v1/teams/21/roster
    ## 21 /api/v1/teams/22/roster
    ## 22 /api/v1/teams/23/roster
    ## 23 /api/v1/teams/24/roster
    ## 24 /api/v1/teams/25/roster
    ## 25 /api/v1/teams/26/roster
    ## 26 /api/v1/teams/28/roster
    ## 27 /api/v1/teams/29/roster
    ## 28 /api/v1/teams/30/roster
    ## 29 /api/v1/teams/52/roster
    ## 30 /api/v1/teams/53/roster

``` r
fetchMultiTeams <- GET("https://statsapi.web.nhl.com/api/v1/teams/?teamId=4,5,29") %>% content("text") %>% fromJSON(flatten=TRUE)


fetchMultiStats <- GET("https://statsapi.web.nhl.com/api/v1/teams/?stats=statsSingleSeasonPlayoffs") %>% content("text") %>% fromJSON(flatten=TRUE)
```

<br> \#\# Wrapper function <br>

# Data Manipulation

<br>

## Joins

<br>

## Creating New Variables

<br> Pulling in franchise data using the fetchFranTotal() function, I
filtered the dataset to only include active franchises then put in order
of first season. From here, I cut the data into 5 chunks representing
roughly 15 to 20 year increments and created a new variable based on age
of the franchises called `franAge`. The categories assigned were Oldest,
Older, Old, New and Newest.

``` r
franData <- fetchFranTotal() %>% filter(data.activeFranchise==1) %>% arrange(data.firstSeasonId)
```

    ## No encoding supplied: defaulting to UTF-8.

``` r
franData <- franData %>% as_tibble() %>% 
  mutate(franAge = ifelse(franData$data.firstSeasonId >= 20012002, "Newest",
       ifelse(franData$data.firstSeasonId >= 19951996, "New",
              ifelse(franData$data.firstSeasonId >= 19801981, "Old",
                     ifelse(franData$data.firstSeasonId >= 19671968, "Older", "Oldest")))))
```

<br> I thought it would also be nice to look at this dataset and be able
to quickly ascertain what the win percentage was for each team. In order
to accomplish this, I created the variable `winPct` that reflects total
wins divided by total games played. This is a particularly interesting
variabile and reveals much even in table form. You can quickly see win
percentages by team by game type (i.e. regualar season vs. playoffs).

``` r
franData <- franData %>% as_tibble() %>% mutate(winPct = (data.wins)/(data.wins + data.ties + data.losses))
```

# Data Exploration & Visualization

## Summarizing Numeric Data

<br> The tables below provide some basic numeric summarization using
data from one of hockey’s most storied franchises: the Montreal
Canadiens. I’ve pulled in Skater Records using our fetchSkaterRecords()
function, setting ID=1, to get started. Because this data is in list
form, I will convert it to a data frame. Next, I’ve written code to
create a basic five number summary of assists and goals by skater
position. Because I’ll need to generate tables across four positions (R
- right wing; C - center; L - left wing; D - defensemen), I’ll create a
function called summByPosition() to help automate this process.

``` r
canadiensSkaterRec <- fetchSkaterRecords(ID=1)
```

    ## No encoding supplied: defaulting to UTF-8.

``` r
canSkateRec <- as.data.frame(canadiensSkaterRec)

summByPosition <- function(position) {
  data <- canSkateRec %>% filter(data.positionCode==position) %>% select(data.assists, data.goals)
  kable(apply(data, 2, summary), col.names=c("Assists", "Goals"), caption = paste0("Historical Summary of Assists & Goals Scored by Position=", position, " for the Montreal Canadiens"))
}

summByPosition("R")
```

|         |   Assists |     Goals |
| ------- | --------: | --------: |
| Min.    |   0.00000 |   0.00000 |
| 1st Qu. |   1.00000 |   0.00000 |
| Median  |   6.00000 |   6.00000 |
| Mean    |  40.67251 |  36.12281 |
| 3rd Qu. |  33.50000 |  28.00000 |
| Max.    | 728.00000 | 544.00000 |

Historical Summary of Assists & Goals Scored by Position=R for the
Montreal Canadiens

``` r
summByPosition("C")
```

|         |   Assists |     Goals |
| ------- | --------: | --------: |
| Min.    |   0.00000 |   0.00000 |
| 1st Qu. |   1.00000 |   0.00000 |
| Median  |   7.00000 |   4.00000 |
| Mean    |  50.32821 |  33.72308 |
| 3rd Qu. |  40.00000 |  25.00000 |
| Max.    | 712.00000 | 507.00000 |

Historical Summary of Assists & Goals Scored by Position=C for the
Montreal Canadiens

``` r
summByPosition("L")
```

|         |   Assists |     Goals |
| ------- | --------: | --------: |
| Min.    |   0.00000 |   0.00000 |
| 1st Qu. |   1.00000 |   0.00000 |
| Median  |   7.00000 |   6.00000 |
| Mean    |  41.03955 |  33.98305 |
| 3rd Qu. |  44.00000 |  38.00000 |
| Max.    | 369.00000 | 408.00000 |

Historical Summary of Assists & Goals Scored by Position=L for the
Montreal Canadiens

``` r
summByPosition("D")
```

|            |   Assists |                                                                                                                                                                                                                                                                                                                                                                                                                                         Goals |
| ---------- | --------: | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------: |
| Min.       |    0.0000 |                                                                                                                                                                                                                                                                                                                                                                                                                                       0.00000 |
| 1st Qu.    |    1.0000 |                                                                                                                                                                                                                                                                                                                                                                                                                                       0.00000 |
| Median     |    6.0000 |                                                                                                                                                                                                                                                                                                                                                                                                                                       1.00000 |
| Mean       |   36.3551 |                                                                                                                                                                                                                                                                                                                                                                                                                                      11.44082 |
| 3rd Qu.    |   32.0000 |                                                                                                                                                                                                                                                                                                                                                                                                                                      11.00000 |
| Max.       |  686.0000 |                                                                                                                                                                                                                                                                                                                                                                                                                                     197.00000 |
| <br>       |           |                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| The tables | generated | above reveal that right wing(R), left wing(L) and centers(C) score, on average, higher than defensemen (D). This aligns with what I would’ve expected given the fact that offensive positions are tasked with being a team’s primary scorers, while defensive positions are not. I was somewhat surprised to see that, though defense trails offense in assists on average, that it wasn’t by much when comparing left wingers to defensemen. |
| <br>       |           |                                                                                                                                                                                                                                                                                                                                                                                                                                               |

Historical Summary of Assists & Goals Scored by Position=D for the
Montreal Canadiens

## Summarizing Categorical Data

<br>

``` r
franData <- fetchFranTotal() %>% filter(data.activeFranchise==1) %>% arrange(data.firstSeasonId)
```

    ## No encoding supplied: defaulting to UTF-8.

``` r
franData %>% as_tibble() %>% 
  mutate(franAge = ifelse(franData$data.firstSeasonId >= 20012002, "Newest",
       ifelse(franData$data.firstSeasonId >= 19951996, "New",
              ifelse(franData$data.firstSeasonId >= 19801981, "Old",
                     ifelse(franData$data.firstSeasonId >= 19671968, "Older", "Oldest")))))
```

    ## # A tibble: 87 x 32
    ##    data.id data.activeFran~ data.firstSeaso~ data.franchiseId data.gameTypeId
    ##      <int>            <int>            <int>            <int>           <int>
    ##  1      15                1         19171918                1               3
    ##  2      16                1         19171918                1               2
    ##  3     101                1         19171918                5               2
    ##  4     102                1         19171918                5               3
    ##  5     103                1         19191920                5               3
    ##  6     104                1         19191920                5               2
    ##  7      11                1         19241925                6               2
    ##  8      12                1         19241925                6               3
    ##  9       5                1         19261927               10               2
    ## 10       6                1         19261927               10               3
    ## # ... with 77 more rows, and 27 more variables: data.gamesPlayed <int>,
    ## #   data.goalsAgainst <int>, data.goalsFor <int>, data.homeLosses <int>,
    ## #   data.homeOvertimeLosses <int>, data.homeTies <int>, data.homeWins <int>,
    ## #   data.lastSeasonId <int>, data.losses <int>, data.overtimeLosses <int>,
    ## #   data.penaltyMinutes <int>, data.pointPctg <dbl>, data.points <int>,
    ## #   data.roadLosses <int>, data.roadOvertimeLosses <int>, data.roadTies <int>,
    ## #   data.roadWins <int>, data.shootoutLosses <int>, data.shootoutWins <int>,
    ## #   data.shutouts <int>, data.teamId <int>, data.teamName <chr>,
    ## #   data.ties <int>, data.triCode <chr>, data.wins <int>, total <int>,
    ## #   franAge <chr>

<br>

\#Graphing <br>
