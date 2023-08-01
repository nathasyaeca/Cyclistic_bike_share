Cyclistic_bike_share_case
================
Nathasya Pramudita
2023-06-27

# Introduction

Cyclistic is a bike share company in Chicago. Owned 5,824 bicycles that
are geotracked and locked into a network of 692 stations across Chicago.
The bike can be unlocked from one station and returned to any other
station in the system anytime.

Until now, Cyclistic’s marketing strategy relied on building general
awareness and appealing to broad consumer segments. One approach that
helped make these things possible was the flexibility of its pricing
plans: single-ride passes, full-day passes, and annual memberships.
Customers who purchase single-ride or full-day passes are referred to as
casual riders. While customers who purchase annual memberships are
Cyclistic members

## Stakeholder

1.  Cyclistic: A bike-share program that features more than 5,800
    bicycles and 600 docking stations. There’s other type of bike that
    Cyclistic over to the customer to help people with disabilities and
    riders who can’t use a standard two-wheeled bike.

2.  Lily Moreno: The director of marketing and manager of analytics
    team.

3.  Cyclistic Marketing analytics team: Team who’re responsible for
    collecting, analyzing, and reporting data that helps guide Cyclistic
    marketing strategy.

4.  Cyclistic Executive Team: consist of all the Executive team that
    will decided (take act) whether to approve the recommended or not.

## Key Takeaway

- Cyclistic’s finance analysts have concluded that annual members are
  much more profitable than casual riders. So to attract casual riders
  into members, we need to find the differences of `annual member` and
  `casual rider`, and find the pattern for casual rider to get interest
  to join and be an annual member.

- It’s much more convenient to convert casual riders into annual members
  than attract new user into using our platform.

- The majority of riders opt for traditional bikes; about 8% of riders
  use the assistive options. Cyclistic users are more likely to ride for
  leisure, but about 30% use them to commute to work each day.

# Prepare the data

The data that we get is from [Motivate International
Inc](https://divvy-tripdata.s3.amazonaws.com/index.html). This data
contain from 2014-2023. But for the sake of saving time, I just used
data from July 2022 to October 2022.

## Install package

Some packages that need to get install to complete this case study is:

1.  Tidyverse
2.  Lubridate
3.  Skimr
4.  Geosphere

``` r
library(tidyverse)
library(lubridate)
library(skimr)
library(geosphere)
```

### Import the Dataset

After that we need to import all the dataset that we need, which is the
data of Cyclistic historical trip from July 2022 to October 2022.

``` r
tripdata_july2022 <- read.csv("~/Case Study/Cyclistic Case Study/202207-divvy-tripdata.csv")
tripdata_august2022 <- read.csv("~/Case Study/Cyclistic Case Study/202208-divvy-tripdata.csv")
tripdata_september2022 <- read.csv("~/Case Study/Cyclistic Case Study/202209-divvy-tripdata.csv")
tripdata_october2022 <- read.csv("~/Case Study/Cyclistic Case Study/202210-divvy-tripdata.csv")
```

Before we dive deep into the data, we need to see metadata and preview
of our data frame first.

``` r
head(tripdata_july2022)
```

    ##            ride_id rideable_type          started_at            ended_at
    ## 1 954144C2F67B1932  classic_bike 2022-07-05 08:12:47 2022-07-05 08:24:32
    ## 2 292E027607D218B6  classic_bike 2022-07-26 12:53:38 2022-07-26 12:55:31
    ## 3 57765852588AD6E0  classic_bike 2022-07-03 13:58:49 2022-07-03 14:06:32
    ## 4 B5B6BE44314590E6  classic_bike 2022-07-31 17:44:21 2022-07-31 18:42:50
    ## 5 A4C331F2A00E79E0  classic_bike 2022-07-13 19:49:06 2022-07-13 20:15:24
    ## 6 579D73BE2ED880B3 electric_bike 2022-07-01 17:04:35 2022-07-01 17:13:18
    ##            start_station_name start_station_id               end_station_name
    ## 1  Ashland Ave & Blackhawk St            13224       Kingsbury St & Kinzie St
    ## 2  Buckingham Fountain (Temp)            15541          Michigan Ave & 8th St
    ## 3  Buckingham Fountain (Temp)            15541          Michigan Ave & 8th St
    ## 4  Buckingham Fountain (Temp)            15541         Woodlawn Ave & 55th St
    ## 5      Wabash Ave & Grand Ave     TA1307000117 Sheffield Ave & Wellington Ave
    ## 6 Desplaines St & Randolph St            15535      Clinton St & Roosevelt Rd
    ##   end_station_id start_lat start_lng  end_lat   end_lng member_casual
    ## 1   KA1503000043  41.90707 -87.66725 41.88918 -87.63851        member
    ## 2            623  41.86962 -87.62398 41.87277 -87.62398        casual
    ## 3            623  41.86962 -87.62398 41.87277 -87.62398        casual
    ## 4   TA1307000164  41.86962 -87.62398 41.79526 -87.59647        casual
    ## 5   TA1307000052  41.89147 -87.62676 41.93625 -87.65266        member
    ## 6         WL-008  41.88461 -87.64456 41.86712 -87.64109        member

``` r
glimpse(tripdata_july2022)
```

    ## Rows: 823,488
    ## Columns: 13
    ## $ ride_id            <chr> "954144C2F67B1932", "292E027607D218B6", "5776585258…
    ## $ rideable_type      <chr> "classic_bike", "classic_bike", "classic_bike", "cl…
    ## $ started_at         <chr> "2022-07-05 08:12:47", "2022-07-26 12:53:38", "2022…
    ## $ ended_at           <chr> "2022-07-05 08:24:32", "2022-07-26 12:55:31", "2022…
    ## $ start_station_name <chr> "Ashland Ave & Blackhawk St", "Buckingham Fountain …
    ## $ start_station_id   <chr> "13224", "15541", "15541", "15541", "TA1307000117",…
    ## $ end_station_name   <chr> "Kingsbury St & Kinzie St", "Michigan Ave & 8th St"…
    ## $ end_station_id     <chr> "KA1503000043", "623", "623", "TA1307000164", "TA13…
    ## $ start_lat          <dbl> 41.90707, 41.86962, 41.86962, 41.86962, 41.89147, 4…
    ## $ start_lng          <dbl> -87.66725, -87.62398, -87.62398, -87.62398, -87.626…
    ## $ end_lat            <dbl> 41.88918, 41.87277, 41.87277, 41.79526, 41.93625, 4…
    ## $ end_lng            <dbl> -87.63851, -87.62398, -87.62398, -87.59647, -87.652…
    ## $ member_casual      <chr> "member", "casual", "casual", "casual", "member", "…

``` r
head(tripdata_august2022)
```

    ##            ride_id rideable_type          started_at            ended_at
    ## 1 550CF7EFEAE0C618 electric_bike 2022-08-07 21:34:15 2022-08-07 21:41:46
    ## 2 DAD198F405F9C5F5 electric_bike 2022-08-08 14:39:21 2022-08-08 14:53:23
    ## 3 E6F2BC47B65CB7FD electric_bike 2022-08-08 15:29:50 2022-08-08 15:40:34
    ## 4 F597830181C2E13C electric_bike 2022-08-08 02:43:50 2022-08-08 02:58:53
    ## 5 0CE689BB4E313E8D electric_bike 2022-08-07 20:24:06 2022-08-07 20:29:58
    ## 6 BFA7E7CC69860C20 electric_bike 2022-08-08 13:06:08 2022-08-08 13:19:09
    ##   start_station_name start_station_id end_station_name end_station_id start_lat
    ## 1                                                                         41.93
    ## 2                                                                         41.89
    ## 3                                                                         41.97
    ## 4                                                                         41.94
    ## 5                                                                         41.85
    ## 6                                                                         41.79
    ##   start_lng end_lat end_lng member_casual
    ## 1    -87.69   41.94  -87.72        casual
    ## 2    -87.64   41.92  -87.64        casual
    ## 3    -87.69   41.97  -87.66        casual
    ## 4    -87.65   41.97  -87.69        casual
    ## 5    -87.65   41.84  -87.66        casual
    ## 6    -87.72   41.82  -87.69        casual

``` r
glimpse(tripdata_august2022)
```

    ## Rows: 785,932
    ## Columns: 13
    ## $ ride_id            <chr> "550CF7EFEAE0C618", "DAD198F405F9C5F5", "E6F2BC47B6…
    ## $ rideable_type      <chr> "electric_bike", "electric_bike", "electric_bike", …
    ## $ started_at         <chr> "2022-08-07 21:34:15", "2022-08-08 14:39:21", "2022…
    ## $ ended_at           <chr> "2022-08-07 21:41:46", "2022-08-08 14:53:23", "2022…
    ## $ start_station_name <chr> "", "", "", "", "", "", "", "", "", "", "", "", "",…
    ## $ start_station_id   <chr> "", "", "", "", "", "", "", "", "", "", "", "", "",…
    ## $ end_station_name   <chr> "", "", "", "", "", "", "", "", "", "", "", "", "",…
    ## $ end_station_id     <chr> "", "", "", "", "", "", "", "", "", "", "", "", "",…
    ## $ start_lat          <dbl> 41.93, 41.89, 41.97, 41.94, 41.85, 41.79, 41.89, 41…
    ## $ start_lng          <dbl> -87.69, -87.64, -87.69, -87.65, -87.65, -87.72, -87…
    ## $ end_lat            <dbl> 41.94, 41.92, 41.97, 41.97, 41.84, 41.82, 41.89, 41…
    ## $ end_lng            <dbl> -87.72, -87.64, -87.66, -87.69, -87.66, -87.69, -87…
    ## $ member_casual      <chr> "casual", "casual", "casual", "casual", "casual", "…

``` r
head(tripdata_september2022)
```

    ##            ride_id rideable_type          started_at            ended_at
    ## 1 5156990AC19CA285 electric_bike 2022-09-01 08:36:22 2022-09-01 08:39:05
    ## 2 E12D4A16BF51C274 electric_bike 2022-09-01 17:11:29 2022-09-01 17:14:45
    ## 3 A02B53CD7DB72DD7 electric_bike 2022-09-01 17:15:50 2022-09-01 17:16:12
    ## 4 C82E05FEE872DF11 electric_bike 2022-09-01 09:00:28 2022-09-01 09:10:32
    ## 5 4DEEB4550A266AE1 electric_bike 2022-09-01 07:30:11 2022-09-01 07:32:36
    ## 6 B1721F8C7C3AC6BF electric_bike 2022-09-01 12:04:25 2022-09-01 12:21:08
    ##   start_station_name start_station_id               end_station_name
    ## 1                                     California Ave & Milwaukee Ave
    ## 2                                                                   
    ## 3                                                                   
    ## 4                                                                   
    ## 5                                                                   
    ## 6                                                                   
    ##   end_station_id start_lat start_lng  end_lat   end_lng member_casual
    ## 1          13084     41.93    -87.69 41.92269 -87.69715        casual
    ## 2                    41.87    -87.62 41.87000 -87.62000        casual
    ## 3                    41.87    -87.62 41.87000 -87.62000        casual
    ## 4                    41.93    -87.69 41.94000 -87.67000        casual
    ## 5                    41.92    -87.73 41.92000 -87.73000        casual
    ## 6                    41.89    -87.65 41.92000 -87.67000        casual

``` r
glimpse(tripdata_september2022)
```

    ## Rows: 701,339
    ## Columns: 13
    ## $ ride_id            <chr> "5156990AC19CA285", "E12D4A16BF51C274", "A02B53CD7D…
    ## $ rideable_type      <chr> "electric_bike", "electric_bike", "electric_bike", …
    ## $ started_at         <chr> "2022-09-01 08:36:22", "2022-09-01 17:11:29", "2022…
    ## $ ended_at           <chr> "2022-09-01 08:39:05", "2022-09-01 17:14:45", "2022…
    ## $ start_station_name <chr> "", "", "", "", "", "", "", "", "", "", "", "", "",…
    ## $ start_station_id   <chr> "", "", "", "", "", "", "", "", "", "", "", "", "",…
    ## $ end_station_name   <chr> "California Ave & Milwaukee Ave", "", "", "", "", "…
    ## $ end_station_id     <chr> "13084", "", "", "", "", "", "", "", "", "", "", ""…
    ## $ start_lat          <dbl> 41.93000, 41.87000, 41.87000, 41.93000, 41.92000, 4…
    ## $ start_lng          <dbl> -87.69000, -87.62000, -87.62000, -87.69000, -87.730…
    ## $ end_lat            <dbl> 41.92269, 41.87000, 41.87000, 41.94000, 41.92000, 4…
    ## $ end_lng            <dbl> -87.69715, -87.62000, -87.62000, -87.67000, -87.730…
    ## $ member_casual      <chr> "casual", "casual", "casual", "casual", "casual", "…

``` r
head(tripdata_october2022)
```

    ##            ride_id rideable_type          started_at            ended_at
    ## 1 A50255C1E17942AB  classic_bike 2022-10-14 17:13:30 2022-10-14 17:19:39
    ## 2 DB692A70BD2DD4E3 electric_bike 2022-10-01 16:29:26 2022-10-01 16:49:06
    ## 3 3C02727AAF60F873 electric_bike 2022-10-19 18:55:40 2022-10-19 19:03:30
    ## 4 47E653FDC2D99236 electric_bike 2022-10-31 07:52:36 2022-10-31 07:58:49
    ## 5 8B5407BE535159BF  classic_bike 2022-10-13 18:41:03 2022-10-13 19:26:18
    ## 6 A177C92E9F021B99 electric_bike 2022-10-13 15:53:27 2022-10-13 15:59:17
    ##          start_station_name start_station_id
    ## 1  Noble St & Milwaukee Ave            13290
    ## 2 Damen Ave & Charleston St            13288
    ## 3  Hoyne Ave & Balmoral Ave              655
    ## 4        Rush St & Cedar St     KA1504000133
    ## 5         900 W Harrison St            13028
    ## 6         900 W Harrison St            13028
    ##                       end_station_name end_station_id start_lat start_lng
    ## 1            Larrabee St & Division St   KA1504000079  41.90068 -87.66260
    ## 2             Damen Ave & Cullerton St          13089  41.92004 -87.67794
    ## 3             Western Ave & Leland Ave   TA1307000140  41.97988 -87.68190
    ## 4 Orleans St & Chestnut St (NEXT Apts)            620  41.90227 -87.62769
    ## 5                    Adler Planetarium          13431  41.87475 -87.64981
    ## 6             Loomis St & Lexington St          13332  41.87472 -87.64983
    ##    end_lat   end_lng member_casual
    ## 1 41.90349 -87.64335        member
    ## 2 41.85497 -87.67570        casual
    ## 3 41.96640 -87.68870        member
    ## 4 41.89820 -87.63754        member
    ## 5 41.86610 -87.60727        casual
    ## 6 41.87219 -87.66150        casual

``` r
glimpse(tripdata_september2022)
```

    ## Rows: 701,339
    ## Columns: 13
    ## $ ride_id            <chr> "5156990AC19CA285", "E12D4A16BF51C274", "A02B53CD7D…
    ## $ rideable_type      <chr> "electric_bike", "electric_bike", "electric_bike", …
    ## $ started_at         <chr> "2022-09-01 08:36:22", "2022-09-01 17:11:29", "2022…
    ## $ ended_at           <chr> "2022-09-01 08:39:05", "2022-09-01 17:14:45", "2022…
    ## $ start_station_name <chr> "", "", "", "", "", "", "", "", "", "", "", "", "",…
    ## $ start_station_id   <chr> "", "", "", "", "", "", "", "", "", "", "", "", "",…
    ## $ end_station_name   <chr> "California Ave & Milwaukee Ave", "", "", "", "", "…
    ## $ end_station_id     <chr> "13084", "", "", "", "", "", "", "", "", "", "", ""…
    ## $ start_lat          <dbl> 41.93000, 41.87000, 41.87000, 41.93000, 41.92000, 4…
    ## $ start_lng          <dbl> -87.69000, -87.62000, -87.62000, -87.69000, -87.730…
    ## $ end_lat            <dbl> 41.92269, 41.87000, 41.87000, 41.94000, 41.92000, 4…
    ## $ end_lng            <dbl> -87.69715, -87.62000, -87.62000, -87.67000, -87.730…
    ## $ member_casual      <chr> "casual", "casual", "casual", "casual", "casual", "…

# Cleaning Proses

## Check unique and duplicate date

After reviewing the data frame, we need to know how much unique
`ride_id` variable on every data frame that we import.

    ## [1] 823488

    ## [1] 785932

    ## [1] 701339

    ## [1] 558685

They have different unique `ride_id` it’s because their total user in
the end of each month is always different.

Next step is we remove duplicate and every NA (*null*) value in each
variable (because they can interfere the process of analyze and
visualize the data later)

``` r
# deleted duplicated and NA value on all dataframe

tripdata_july2022 <- tripdata_july2022 %>%
  distinct() %>%
  drop_na()

tripdata_august2022 <- tripdata_august2022 %>%
  distinct() %>%
  drop_na()

tripdata_september2022 <- tripdata_september2022 %>%
  distinct() %>%
  drop_na()

tripdata_october2022 <- tripdata_october2022 %>%
  distinct() %>%
  drop_na()
```

## Changing class in our data frame

Knowing that we don’t have any duplicate and NA value in our data, we
can move to the next steps. There’s some inconsistency in our data type
if we see variable `started_at` and `ended_at` it’s suppose to be
date-time data type, but R read it as `chr` or character, so we need to
changed all that variable into date-time or POXISct it first.

``` r
tripdata_july2022$started_at <- as.POSIXct(tripdata_july2022$started_at, format = "%Y-%m-%d %H:%M:%S")
tripdata_july2022$ended_at <- as.POSIXct(tripdata_july2022$ended_at, format = "%Y-%m-%d %H:%M:%S")
tripdata_august2022$started_at <- as.POSIXct(tripdata_august2022$started_at, format = "%Y-%m-%d %H:%M:%S")
tripdata_august2022$ended_at <- as.POSIXct(tripdata_august2022$ended_at, format = "%Y-%m-%d %H:%M:%S")
tripdata_september2022$started_at <- as.POSIXct(tripdata_september2022$started_at, format = "%Y-%m-%d %H:%M:%S")
tripdata_september2022$ended_at <- as.POSIXct(tripdata_september2022$ended_at, format = "%Y-%m-%d %H:%M:%S")
tripdata_october2022$started_at <- as.POSIXct(tripdata_october2022$started_at, format = "%Y-%m-%d %H:%M:%S")
tripdata_october2022$ended_at <- as.POSIXct(tripdata_october2022$ended_at, format = "%Y-%m-%d %H:%M:%S")
```

## Calculate distance (m) from longitude and latitude

To find if there’s any differences in distance between Casual user and
annual Member, we need to changes the longitude and latitude two point
coordinates into single distance in metric system. To change it, we used
`geosphere` package. After we calculate it to meter, we delete four
variable that contain longitude and latitude coordinates, which is
`start_lng`, `start_lat`, `end_lng`, and `end_lat`.

``` r
# calculating longitude and latitude into meter
tripdata_july2022$dist <- distGeo(matrix(c(tripdata_july2022$start_long, tripdata_july2022$start_lat, tripdata_july2022$end_long, tripdata_july2022$end_lat), ncol = 2))

tripdata_august2022$dist <- distGeo(matrix(c(tripdata_august2022$start_long, tripdata_august2022$start_lat, tripdata_august2022$end_long, tripdata_august2022$end_lat), ncol = 2))

tripdata_september2022$dist <- distGeo(matrix(c(tripdata_september2022$start_long, tripdata_september2022$start_lat, tripdata_september2022$end_long, tripdata_september2022$end_lat), ncol = 2))

tripdata_october2022$dist <- distGeo(matrix(c(tripdata_october2022$start_long, tripdata_october2022$start_lat, tripdata_october2022$end_long, tripdata_october2022$end_lat), ncol = 2))


# delete longitude and latitude coordination
tripdata_july2022 <- tripdata_july2022 %>%
  select(-(start_lat:end_lng))
tripdata_august2022 <- tripdata_august2022 %>%
  select(-(start_lat:end_lng))
tripdata_september2022 <- tripdata_september2022 %>%
  select(-(start_lat:end_lng))
tripdata_october2022 <- tripdata_october2022 %>%
  select(-(start_lat:end_lng))
```

## Make new variable to count total trip

``` r
tripdata_july2022$total_trip <- difftime(tripdata_july2022$ended_at, tripdata_july2022$started_at, units = "mins")

tripdata_august2022$total_trip <- difftime(tripdata_august2022$ended_at, tripdata_august2022$started_at, units = "mins")

tripdata_september2022$total_trip <- difftime(tripdata_september2022$ended_at, tripdata_september2022$started_at, units = "mins")

tripdata_october2022$total_trip <- difftime(tripdata_october2022$ended_at, tripdata_october2022$started_at, units = "mins")
```

After that we’ll see if our data is ready to get analyze by checking our
data frame again.

``` r
str(tripdata_july2022)
```

    ## 'data.frame':    822541 obs. of  11 variables:
    ##  $ ride_id           : chr  "954144C2F67B1932" "292E027607D218B6" "57765852588AD6E0" "B5B6BE44314590E6" ...
    ##  $ rideable_type     : chr  "classic_bike" "classic_bike" "classic_bike" "classic_bike" ...
    ##  $ started_at        : POSIXct, format: "2022-07-05 08:12:47" "2022-07-26 12:53:38" ...
    ##  $ ended_at          : POSIXct, format: "2022-07-05 08:24:32" "2022-07-26 12:55:31" ...
    ##  $ start_station_name: chr  "Ashland Ave & Blackhawk St" "Buckingham Fountain (Temp)" "Buckingham Fountain (Temp)" "Buckingham Fountain (Temp)" ...
    ##  $ start_station_id  : chr  "13224" "15541" "15541" "15541" ...
    ##  $ end_station_name  : chr  "Kingsbury St & Kinzie St" "Michigan Ave & 8th St" "Michigan Ave & 8th St" "Woodlawn Ave & 55th St" ...
    ##  $ end_station_id    : chr  "KA1503000043" "623" "623" "TA1307000164" ...
    ##  $ member_casual     : chr  "member" "casual" "casual" "casual" ...
    ##  $ dist              : num  3603 0 8609 15764 7700 ...
    ##  $ total_trip        : 'difftime' num  11.75 1.88333333333333 7.71666666666667 58.4833333333333 ...
    ##   ..- attr(*, "units")= chr "mins"

``` r
str(tripdata_august2022)
```

    ## 'data.frame':    785089 obs. of  11 variables:
    ##  $ ride_id           : chr  "550CF7EFEAE0C618" "DAD198F405F9C5F5" "E6F2BC47B65CB7FD" "F597830181C2E13C" ...
    ##  $ rideable_type     : chr  "electric_bike" "electric_bike" "electric_bike" "electric_bike" ...
    ##  $ started_at        : POSIXct, format: "2022-08-07 21:34:15" "2022-08-08 14:39:21" ...
    ##  $ ended_at          : POSIXct, format: "2022-08-07 21:41:46" "2022-08-08 14:53:23" ...
    ##  $ start_station_name: chr  "" "" "" "" ...
    ##  $ start_station_id  : chr  "" "" "" "" ...
    ##  $ end_station_name  : chr  "" "" "" "" ...
    ##  $ end_station_id    : chr  "" "" "" "" ...
    ##  $ member_casual     : chr  "casual" "casual" "casual" "casual" ...
    ##  $ dist              : num  3993 8652 2487 16256 5457 ...
    ##  $ total_trip        : 'difftime' num  7.51666666666667 14.0333333333333 10.7333333333333 15.05 ...
    ##   ..- attr(*, "units")= chr "mins"

``` r
str(tripdata_september2022)
```

    ## 'data.frame':    700627 obs. of  11 variables:
    ##  $ ride_id           : chr  "5156990AC19CA285" "E12D4A16BF51C274" "A02B53CD7DB72DD7" "C82E05FEE872DF11" ...
    ##  $ rideable_type     : chr  "electric_bike" "electric_bike" "electric_bike" "electric_bike" ...
    ##  $ started_at        : POSIXct, format: "2022-09-01 08:36:22" "2022-09-01 17:11:29" ...
    ##  $ ended_at          : POSIXct, format: "2022-09-01 08:39:05" "2022-09-01 17:14:45" ...
    ##  $ start_station_name: chr  "" "" "" "" ...
    ##  $ start_station_id  : chr  "" "" "" "" ...
    ##  $ end_station_name  : chr  "California Ave & Milwaukee Ave" "" "" "" ...
    ##  $ end_station_id    : chr  "13084" "" "" "" ...
    ##  $ member_casual     : chr  "casual" "casual" "casual" "casual" ...
    ##  $ dist              : num  7684 0 9232 2371 2489 ...
    ##  $ total_trip        : 'difftime' num  2.71666666666667 3.26666666666667 0.366666666666667 10.0666666666667 ...
    ##   ..- attr(*, "units")= chr "mins"

``` r
str(tripdata_october2022)
```

    ## 'data.frame':    558210 obs. of  11 variables:
    ##  $ ride_id           : chr  "A50255C1E17942AB" "DB692A70BD2DD4E3" "3C02727AAF60F873" "47E653FDC2D99236" ...
    ##  $ rideable_type     : chr  "classic_bike" "electric_bike" "electric_bike" "electric_bike" ...
    ##  $ started_at        : POSIXct, format: "2022-10-14 17:13:30" "2022-10-01 16:29:26" ...
    ##  $ ended_at          : POSIXct, format: "2022-10-14 17:19:39" "2022-10-01 16:49:06" ...
    ##  $ start_station_name: chr  "Noble St & Milwaukee Ave" "Damen Ave & Charleston St" "Hoyne Ave & Balmoral Ave" "Rush St & Cedar St" ...
    ##  $ start_station_id  : chr  "13290" "13288" "655" "KA1504000133" ...
    ##  $ end_station_name  : chr  "Larrabee St & Division St" "Damen Ave & Cullerton St" "Western Ave & Leland Ave" "Orleans St & Chestnut St (NEXT Apts)" ...
    ##  $ end_station_id    : chr  "KA1504000079" "13089" "TA1307000140" "620" ...
    ##  $ member_casual     : chr  "member" "casual" "member" "member" ...
    ##  $ dist              : num  5624 13336 9940 4235 677 ...
    ##  $ total_trip        : 'difftime' num  6.15 19.6666666666667 7.83333333333333 6.21666666666667 ...
    ##   ..- attr(*, "units")= chr "mins"

# Analyze and share phase

After we conclude that our data is clean, and we can hold the integrity
of our dataset. We jump to the next crucial step, which is analyze and
make visualization out of it, so our stakeholder (especially our high
rank Executive) can implement something out of our finding (data-driven
decision making), for the benefit of their company.

## Find key difference of Casual and annual Member of Cyclist

### Average mean distance and trip time of Casual and Member user

``` r
# See difference in distance between Casual and Member
# Disclaimer : mean_dist column is in meter
avg_dist_july <- tripdata_july2022 %>%
  group_by(member_casual) %>%
  drop_na %>%
  summarize(mean_dist = mean(dist), mean_total_trip = mean(total_trip))

avg_dist_august <- tripdata_august2022 %>%
  group_by(member_casual) %>%
  drop_na() %>%
  summarize(mean_dist = mean(dist), mean_total_trip = mean(total_trip))

avg_dist_sept <- tripdata_september2022 %>%
  group_by(member_casual) %>%
  drop_na %>%
  summarize(mean_dist = mean(dist), mean_total_trip = mean(total_trip))

avg_dist_oct <- tripdata_october2022 %>%
  group_by(member_casual) %>%
  drop_na() %>%
  summarize(mean_dist = mean(dist), mean_total_trip = mean(total_trip))
```

### Visualizing different distance between Casual and Member

Now we see it from visualization point of view, so it’s easier for us to
understand it.

![](Cyclistic_bike_share_files/figure-gfm/viz%20of%20AVG%20usage%20user%20in%20July-1.png)<!-- -->

![](Cyclistic_bike_share_files/figure-gfm/viz%20of%20AVG%20usage%20user%20in%20August-1.png)<!-- -->

![](Cyclistic_bike_share_files/figure-gfm/viz%20of%20AVG%20usage%20user%20in%20September-1.png)<!-- -->

![](Cyclistic_bike_share_files/figure-gfm/viz%20of%20AVG%20usage%20user%20in%20October-1.png)<!-- -->

There’s consistency in our data, with each month `member` user is using
shorted distance than `casual` user. It’s seems odd if we think that
“Becoming member is much more inexpensive than only using it casually”.
We need to finding out more in other sector.

### Visualizing total trip using Cyclist platform

Now we see how much time average user using Cyclist platform based on
different user type.

![](Cyclistic_bike_share_files/figure-gfm/Visualizing%20total%20trip%20user%20in%20July-1.png)<!-- -->

![](Cyclistic_bike_share_files/figure-gfm/Visualizing%20total%20trip%20user%20in%20august-1.png)<!-- -->

![](Cyclistic_bike_share_files/figure-gfm/Visualizing%20total%20trip%20user%20in%20September-1.png)<!-- -->

![](Cyclistic_bike_share_files/figure-gfm/Visualizing%20total%20trip%20user%20in%20October-1.png)<!-- -->

Our finding is still consistent. Between `casual` and `member` user,
`casual users` total trip is much more long if we compare to
`annual member` user.

## Frequent station that our user used

Other question is I want to see is there correlation of station that
`member` user frequently used, rather than other station. If there’s, we
can put ads between that station to pull `casual` user using our
platform.

``` r
tripdata_july2022 %>%
  group_by(member_casual, start_station_name) %>%
  summarize(count=n()) %>%
  top_n(3, count) %>% 
  ungroup() %>%
  arrange(desc(count)) %>%
  as.data.frame()
```

    ##   member_casual                 start_station_name count
    ## 1        casual                                    56645
    ## 2        member                                    55386
    ## 3        casual            Streeter Dr & Grand Ave 11746
    ## 4        casual  DuSable Lake Shore Dr & Monroe St  6134
    ## 5        member DuSable Lake Shore Dr & North Blvd  3178
    ## 6        member            Streeter Dr & Grand Ave  3013

``` r
tripdata_august2022 %>%
  group_by(member_casual, start_station_name) %>%
  summarize(count=n()) %>%
  top_n(3, count) %>% 
  ungroup() %>%
  arrange(desc(count)) %>%
  as.data.frame()
```

    ##   member_casual                start_station_name count
    ## 1        member                                   58225
    ## 2        casual                                   53812
    ## 3        casual           Streeter Dr & Grand Ave  9602
    ## 4        casual DuSable Lake Shore Dr & Monroe St  4570
    ## 5        member          Kingsbury St & Kinzie St  2963
    ## 6        member             Wells St & Concord Ln  2920

``` r
tripdata_september2022 %>%
  group_by(member_casual, start_station_name) %>%
  summarize(count=n()) %>%
  top_n(3, count) %>% 
  ungroup() %>%
  arrange(desc(count)) %>%
  as.data.frame()
```

    ##   member_casual                start_station_name count
    ## 1        member                                   57508
    ## 2        casual                                   46272
    ## 3        casual           Streeter Dr & Grand Ave  6989
    ## 4        casual DuSable Lake Shore Dr & Monroe St  3600
    ## 5        member          Loomis St & Lexington St  3148
    ## 6        member          Kingsbury St & Kinzie St  2554

``` r
tripdata_october2022 %>%
  group_by(member_casual, start_station_name) %>%
  summarize(count=n()) %>%
  top_n(3, count) %>%
  ungroup() %>%
  arrange(desc(count)) %>%
  as.data.frame()
```

    ##   member_casual                start_station_name count
    ## 1        member                                   55552
    ## 2        casual                                   35803
    ## 3        casual           Streeter Dr & Grand Ave  4362
    ## 4        member               Ellis Ave & 60th St  3781
    ## 5        member          University Ave & 57th St  3593
    ## 6        casual DuSable Lake Shore Dr & Monroe St  2947

By knowing what frequently our different type of user starting to used
our platform. We can put some ads in that particular station that Casual
user frequently come, and offer them the benefit of changing from Casual
user to annual Member.

## Frequently bike type that used based of user type

``` r
tripdata_july2022 %>%
  group_by(member_casual, rideable_type) %>%
  summarize(count=n()) %>%
  top_n(1, count) %>%
  ungroup() %>%
  as.data.frame()
```

    ##   member_casual rideable_type  count
    ## 1        casual electric_bike 218905
    ## 2        member  classic_bike 216998

``` r
tripdata_august2022 %>%
  group_by(member_casual, rideable_type) %>%
  summarize(count=n()) %>%
  top_n(1, count) %>%
  ungroup() %>%
  as.data.frame()
```

    ##   member_casual rideable_type  count
    ## 1        casual electric_bike 203966
    ## 2        member  classic_bike 215328

``` r
tripdata_september2022 %>%
  group_by(member_casual, rideable_type) %>%
  summarize(count=n()) %>%
  top_n(1, count) %>%
  ungroup() %>%
  as.data.frame()
```

    ##   member_casual rideable_type  count
    ## 1        casual electric_bike 171496
    ## 2        member electric_bike 203875

``` r
tripdata_october2022 %>%
  group_by(member_casual, rideable_type) %>%
  summarize(count=n()) %>%
  top_n(1, count) %>%
  ungroup() %>%
  as.data.frame()
```

    ##   member_casual rideable_type  count
    ## 1        casual electric_bike 134807
    ## 2        member electric_bike 197704

# Key Finding

- `Casual` rider is more likely to ride and spend more time in the
  platform compared to `Member`. With average of `casual` rider distance
  is **4.8 Kilometer** compare to annual `member` **4.3 Kilometer**. And
  the total trip of `casual` rider is **20 minutes** while annual
  `member` is only **12 minutes**

- Frequent station `casual` user used is **Streeter Dr & Grand Ave**
  with total count of 3.2699^{4} times.

- The type of bicycle that `casual` rider usually ride is
  `electric bike`, with total of casual riders choose electric bike
  compare to other type of bike. And meanwhile `member` user splited
  into two type of bike, electric bike with total count and classic bike

# Conclusion

From our analysis before, there some pattern that we can take. `Casual`
user tend to cyclist with long distance compare to `annual member`, and
they considering take much more time using the platform if you compare
them to `annual member`. This can be:

1.  Using platform as single-ride passes or full-day passes is more
    inexpensive compare to becoming annual member.

2.  Cyclistic already give benefit if `casual` user want to convert to
    ‘member’ user. But this information is not widely know to all the
    user.

**For this problem, we provide some solution:**

1.  Give some advantage if our user want to change into annual member.
    And make a list of some benefit between `casual rider` and
    `member rider` so our user can compared it more clearly.

2.  Promote it distinctly, by commercialize it by using ads in one of
    our frequently station that our user used.

3.  Put ads in one of frequent station that ‘casual’ rider tend to be.
    So the message would be directly to the target audience.

------------------------------------------------------------------------
