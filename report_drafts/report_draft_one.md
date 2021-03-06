Report - first draft
================

-   [REPORT- FIRST DRAFT](#report--first-draft)
    -   [Motivation and Related Work](#motivation-and-related-work)
    -   [Initial Project Questions](#initial-project-questions)
    -   [Collecting and Tidying Data](#collecting-and-tidying-data)
        -   [Weather data - Overview](#weather-data---overview)
        -   [Weather data - Complete pipeline](#weather-data---complete-pipeline)
        -   [Baseball data](#baseball-data)
        -   [Combining Datasets](#combining-datasets)
    -   [Exploratory Analysis](#exploratory-analysis)
        -   [Maximum Temperature](#maximum-temperature)
    -   [Relative Humidity](#relative-humidity)
    -   [Heat Index](#heat-index)
    -   [Additional Anlaysis: Plots](#additional-anlaysis-plots)
    -   [Additional Analysis: Models](#additional-analysis-models)
    -   [Discussion](#discussion)

REPORT- FIRST DRAFT
===================

Motivation and Related Work
---------------------------

Advanced statistical analytics are central to evaluating players, developing teams, and informing in-game decisions throughout professional sports. The growing influence of sport analytics is arguably most evident in Major League Baseball (MLB), where teams that prioritize sabermetrics have won the last three World Series. Baseball is uniquely suited to statistical analysis, as its components – pitching, hitting, and fielding – are discrete events that result in unambiguous decisions, as classified by the official game scorers, and can be attributed to specific players. Baseball also generates a substantial amount of data; the 30 MLB teams play a combined 2,430 games in the regular season, during which over 900,000 pitches are thrown across more than 165,000 at-bats.

Sabermetric analysts sift through this exhaustive amount of data to identify competitive advantages among increasingly similar, dominant players. The arms race between pitchers and hitters has elevated the game, as pitchers are throwing harder and batters are hitting more home runs than at any point in MLB history.

``` r
# TABLE FOR PITCH SPEED OVER TIME 
```

In addition to assessing player matchups, analysts aim to identify external factors that could influence players’ performances, especially weather. Over the course of a six-month MLB season, teams expect to play in a range of weather extremes, from 100-degree days in Arlington, Texas to snowy conditions in Milwaukee, Wisconsin in the early spring. A growing body of sabermetric research has found that game-day weather affects umpiring decisions, pitcher ball control, and distance of hits (and thereby number of homeruns).

Initial Project Questions
-------------------------

Initially, our project aimed to answer the following overarching question:

-   What is the association between weather and the speed and type of baseball pitches? We hypothesized that the speed and type of Major League Baseball (MLB) pitches may be negatively impacted by extreme weather.

To operationalize this, we obtained MLB data and daily weather data for the years 2016, 2017, and 2018, focusing on the following weather data: **maximum (max) temperature (ºC), relative humidity, and heat index (ºC).** We then focused in on fastball pitches in particular, hypothesizing that these pitches may be most affected by temperature, humidity, and heat index.

As our research progressed and it became clear that overall trends relating max temperature and pitch speed were not apparent, we began to investigate whether the relationships between pitch speed and weather variables could be modified by team, specifically whether a team *typically* played in an extreme weather condition. In other words, were teams that played the majority of their games in cooler climates, i.e. the Milwaukee Brewers, more affected by heat when they played in hotter climates, for example at the Texas Rangers stadium? This led to a revised question:

-   Revised question here.

As part of addressing this question, we proposed the following analytic deliverables (AD) in our submitted project proposal:

-   Linear model for the effect of temperature on pitch speed (fastball), adjusted for pitcher and other covariates to be determined. (AD1)

-   Graph of variation in proportion of fastball vs off-speed pitches and temperature (AD2)

-   Graph of speed of fastballs and maximum temperature (AD3)

-   Graph of pitch speed (all pitch types) and relative humidity (AD4)

-   Graph of pitch speed (all pitch types) and heat index (AD5)

Collecting and Tidying Data
---------------------------

**Stephen or Brennan to add intro sentences for data section as a general overview of the process that's detailed below?**

### Weather data - Overview

#### PRISM climate model

We obtained our weather data from the Parameter-elevation Regressions on Independent Slopes Model (PRISM) Daily Spatial Climate Dataset (AN81d) developed by the PRISM Climate Group based at Oregon State University. This is a gridded climate model that assimilates station data from networks around the United States and applies interpolation routines to simulate variations with elevation, coastal effects, temperature inversions, and terrain barriers. The model includes 7 bands of output parameters at a 2.5 arc minute grid resolution (<https://developers.google.com/earth-engine/datasets/catalog/OREGONSTATE_PRISM_AN81d>).

#### Google Earth Engine

Using the code editor in the Google Earth Engine platform (<https://code.earthengine.google.com>), we extracted maximum daily temperature (`tmax`), minimum daily temperature (`tmin`), and mean daily dew point temperature (`tdmean`) from April 2015 through October 2018 at each MLB stadium location. We obtained the latitude and longitude coordinates for each ballpark from a Google Fusion Table (<https://fusiontables.google.com/DataSource?docid=1EXApOoxEgJUFlbMjUodfxBSWlRvgQNpABeddHqiN#rows:id=1>), which we imported to Earth Engine. We used JavaScript code to create arrays that collect each weather parameter mapped over each ballpark location for each day, reduced to the first observation reflecting the grid at the point of interest, which are then flattened to two dimensional tables.

img src = "./gee\_screen\_capture.PNG" height = "500px" width = "400px" /

Figure 1. The image above is a screenshot of the Earth Engine code editor. The map displays maximum daily temperatures across the United States on July 05, 2018 from the PRISM model.

The JavaScript code for downloading PRISM data is provided at (gee.html). A registered Google account is required to access the code editor.

#### Tidy weather data

Next, we imported the PRISM files for `tmax`, `tmin`, and `tdmean` into R, tidied the data from "wide" format to "long" format, and joined each parameter to a single dataframe by team and date. We created daily heat index values (`heat_index`) from mean daily air temperature and mean daily dew point temperature using the `heat.index` function of the `weathermetrics` package, which applies a heat index equation derived from the US National Weather Service's online heat index calculator. We also used the `dewpoint.to.humidity` function to obtain relative humidity (`rh`).

#### Reproducibility

The raw data files from Earth Engine and the stadium index from Fusion Table are available at the links below:

"tdmean\_ballpark.PRISM.csv": <https://drive.google.com/open?id=11Wptk3Eaul1rJEmXB2Xb9T7G_B82i3MK>
"tmin\_ballpark.PRISM.csv" : <https://drive.google.com/open?id=1gtjdciCF4zJmrcyjXnUrj-JNDs437MCa>
"tmax\_ballpark.PRISM.csv" : <https://drive.google.com/open?id=1AYqLA1Qp3tOOG7hltIN9-aXuYUkF3SSq>
"stadium\_index.csv" : <https://drive.google.com/open?id=13mteZpqRSzkeUObmaL6VwYT9s3QNlDOo>

The geocoded stadium index source is: <https://fusiontables.google.com/DataSource?docid=1EXApOoxEgJUFlbMjUodfxBSWlRvgQNpABeddHqiN#rows:id=1>

The joined and cleaned weather dataframe, `weather.csv`, is available at:

"weather.csv" : <https://drive.google.com/open?id=1jr1_sSAzSn8SVKralTHWGdQWH0BEX08W>

### Weather data - Complete pipeline

See the code chunks below for the steps that were discussed above. **Or Stephen, intermingle these code chunks with the text above as appropriate so that we're not repeating oursevles too much??**

``` r
stadium_index = read_csv("./data/stadium_index.csv")
```

    ## Parsed with column specification:
    ## cols(
    ##   index = col_integer(),
    ##   city = col_character(),
    ##   stadium_name = col_character(),
    ##   team_name = col_character()
    ## )

#### Import PRISM variables

Import max and min daily temperatures and mean daily dew points for all MLB stadium locations from April 2015 to October 2018.

``` r
tmax =
  read_csv("./data/tmax_ballpark_PRISM.csv") %>% 
    janitor::clean_names() %>% 
    select(-c("system_index", "geo")) %>% 
    select(team_name, everything()) %>% 
    gather(key = date, value = tmax, "x20150401":"x20181030")
```

    ## Parsed with column specification:
    ## cols(
    ##   .default = col_double(),
    ##   `system:index` = col_character(),
    ##   `Team Name` = col_character(),
    ##   .geo = col_character()
    ## )

    ## See spec(...) for full column specifications.

``` r
tmin =
  read_csv("./data/tmin_ballpark_PRISM.csv") %>% 
    janitor::clean_names() %>% 
    select(-c("system_index", "geo")) %>% 
    select(team_name, everything()) %>% 
    gather(key = date, value = tmin, "x20150401":"x20181030")
```

    ## Parsed with column specification:
    ## cols(
    ##   .default = col_double(),
    ##   `system:index` = col_character(),
    ##   `Team Name` = col_character(),
    ##   .geo = col_character()
    ## )
    ## See spec(...) for full column specifications.

``` r
tdmean =
  read_csv("./data/tdmean_ballpark_PRISM.csv") %>% 
    janitor::clean_names() %>% 
    select(-c("system_index", "geo")) %>% 
    select(team_name, everything()) %>% 
    gather(key = date, value = tdmean, "x20150401":"x20181030")
```

    ## Parsed with column specification:
    ## cols(
    ##   .default = col_double(),
    ##   `system:index` = col_character(),
    ##   `Team Name` = col_character(),
    ##   .geo = col_character()
    ## )
    ## See spec(...) for full column specifications.

#### Join PRISM data

Join `tmax`, `tmin` and `tdmean`.

``` r
weather <- as.tibble(
    full_join(tmax, tmin, by = c("team_name", "date")) %>% 
    full_join(., tdmean, by = c("team_name", "date")) %>% 
    mutate(date = str_replace(date, "x", ""),
           date = as.Date(date, format = "%Y%m%d"))
  )
```

#### Heat Index and Relative Humidity (RH)

This chunk creates daily heat index values from mean daily air temperature and mean daily dew point temperature. The `heat.index` function of the `weathermetrics` package is used. The heat index equation is derived from the US National Weather Service's online heat index calculator. The `dewpoint.to.humidity` function is used to obtain relative humidity.

``` r
weather <-
  weather %>% 
    mutate(tmean = (tmax + tmin)/2,
           heat_index = weathermetrics::heat.index(t = tmean, dp = tdmean, temperature.metric = "celsius", output.metric = "celsius", round = 2),
           rh = weathermetrics::dewpoint.to.humidity(dp = tdmean, t = tmean, temperature.metric = "celsius"))
```

    ## Warning in dewpoint.to.humidity(t = t, dp = dp, temperature.metric =
    ## temperature.metric): For some observations, dew point temperature was
    ## higher than temperature. Since dew point temperature cannot be higher than
    ## air temperature, relative humidty for these observations was set to 'NA'.

    ## Warning in weathermetrics::dewpoint.to.humidity(dp = tdmean, t = tmean, :
    ## For some observations, dew point temperature was higher than temperature.
    ## Since dew point temperature cannot be higher than air temperature, relative
    ## humidty for these observations was set to 'NA'.

``` r
weather
```

    ## # A tibble: 37,961 x 8
    ##    team_name         date        tmax   tmin tdmean tmean heat_index    rh
    ##    <chr>             <date>     <dbl>  <dbl>  <dbl> <dbl>      <dbl> <dbl>
    ##  1 Minnesota Twins   2015-04-01  17.9  0.160  -1.07  9.02       7.27  49.6
    ##  2 Seattle Mariners  2015-04-01  12.3  6.41    4.47  9.34       8.2   71.9
    ##  3 San Francisco Gi~ 2015-04-01  16.3 11.2     6.92 13.8       12.9   63.5
    ##  4 Oakland Athletics 2015-04-01  19.3 10.7     6.31 15.0       14     56.4
    ##  5 Los Angeles Dodg~ 2015-04-01  24.3 13.2    11.1  18.8       18.3   61.2
    ##  6 San Diego Padres  2015-04-01  20.0 15.7    12.2  17.9       17.5   69.7
    ##  7 Los Angeles Ange~ 2015-04-01  23.2 13.6    11.7  18.4       18     65.0
    ##  8 Houston Astros    2015-04-01  28.3 17.6    18.0  23.0       23.2   73.6
    ##  9 Texas Rangers     2015-04-01  28.3 17.8    16.3  23.1       23.1   65.9
    ## 10 Arizona Diamondb~ 2015-04-01  34.1 18.8     1.54 26.4       25.7   19.8
    ## # ... with 37,951 more rows

#### Export tidy weather dataframe

``` r
write_csv(weather, path = "./data/weather.csv") # export tidy dataframe, to be joined with baseball data
```

### Baseball data

#### PITCHf/x data

PITCHf/x is a system of cameras set up at every MLB baseball stadium that collects pitch data including pitch speed, type, and trajectory. The data are stored in XML format and maintained by MLB Advanced Media.

#### Helpful packages

We gathered PITCHf/x data from using the r package “pitchRx” (<https://pitchrx.cpsievert.me/>). The “pitchRx” package relies on the “XML2R” package to easily convert XML tables into data tables in r. In particular, the “scrape” function in the “pitchRx” package allows you to gather all the pitch data between a specified start and end date. The pitchRx also includes

Gathering a large amount of pitch data, however, may use up too much random-access memory. To solve this problem, we used the “dplyr” package to create an SQLite database, which aids in memory management.

#### Gathering pitch data

The data was scraped into an SQlite3 database that was stored on a personal computer so that the user did not have to scrape it at a later time. We brought this data into an R session by creating SQlite3 database representations (called "pitch" and "atbat" below). Finally, we loaded in the weather data, an index of the team names, and their corresponding abbreviations.

``` r
# The first argument is the path to the SQlite database. 
# If create is set to TRUE, the code will create a new SQlite3 database at the specified path if it does not exist. If the path does exist, it will connect to the existing database.

my_db <- src_sqlite("./data/GamedayDB.sqlite3", create = TRUE)

# Only run the code below if you have never scraped the data. This code collects and stores all PITCHf/x data from one date to the next, and saves it as GamedayDB.sqlite3.
####### scrape(start = "2016-04-03", end = Sys.Date() - 1,
####### suffix = "inning/inning_all.xml", connect = my_db$con)


# Create pitch and atbat, which are representations of data in my_db. That is, pitch does not actually pull data from every pitch into memory, but is a portrayal of the relevant data sitting in my_db.
pitch = tbl(my_db$con, "pitch")
atbat = tbl(my_db$con, "atbat")


# Import team names data
team_names = read_csv("./data/team_abbrv.csv")
```

    ## Parsed with column specification:
    ## cols(
    ##   home = col_character(),
    ##   team_name = col_character()
    ## )

``` r
# Import weather data 
weather_db = read_csv("./data/weather.csv")
```

    ## Parsed with column specification:
    ## cols(
    ##   team_name = col_character(),
    ##   date = col_date(format = ""),
    ##   tmax = col_double(),
    ##   tmin = col_double(),
    ##   tdmean = col_double(),
    ##   tmean = col_double(),
    ##   heat_index = col_double(),
    ##   rh = col_double()
    ## )

### Combining Datasets

Below, we bring the pitch data into R from the SQlite3 database using the representations created above. Then we tidy the pitch data and combine it with the weather data:

``` r
# First line collects data into an r dataframe from the pitch and atbat representations established above
pitch_tidy_db = collect(inner_join(pitch, atbat, by = c("num", "url"))) %>% 
  # Extract home and away team information from url link
  separate(gameday_link.x, into = c("remove", "away_home"), sep = ".............._") %>%
  separate(away_home, into = c("away", "home"), sep = "mlb_") %>% 
  # Tidy date
  mutate(date = ymd(date)) %>% 
  # Joining pitch with atbat created redundant columns coded .x or .y. Remove redundant cols ending with .y. Remove spanish columns ending with _es
  select(-ends_with(".y"), -ends_with("_es")) %>% 
  # Remove .x from the end of columns
  rename_at(.vars = vars(ends_with(".x")),
          .funs = funs(sub("[.]x$", "", .))) %>% 
  # Add full team names
  left_join(team_names) %>% 
  # Add weather data
  left_join(weather_db) %>% 
  # Remove stadiums with a roof
  filter(!home %in% c("aas", "nas", "tor", "ari", "sea", "hou", "tba", "mia", "1", "2")) %>%  
  separate(date, c("y", "m", "d"))
```

    ## Warning: Expected 2 pieces. Additional pieces discarded in 2744247 rows [1,
    ## 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, ...].

``` r
  # Retain only XX of 81 variables, to make the size of the dataset easier to work with 
  # select(start_speed, pitch_type, inning_side, inning, event, pitcher_name, y, m, d, team_name, tmax:rh, type) 

  # Remove unneeded intermediary data sets to clean environment
  rm(atbat, my_db, pitch, team_names, weather_db)
```

Our final dataset that combines the pitch and weather data is named `pitch_tidy_db`. After retaining only certain variables of interest, the dataset incudes 2186391 observations of 81.

We examine missing data in our dataset:

``` r
# Examining pitch speed missing data
missing_data = pitch_tidy_db %>% 
  filter(is.na(start_speed)) %>% # we are missing pitch speed for 62,971 pitches 
  group_by(team_name) %>% 
  count() # see if the missing data are (roughly) evenly distributed among the teams 
```

Exploratory Analysis
--------------------

As outlined in our 11/07/2018 project proposal, based on previous reports and anecdotal evidence, our group hypothesized that weather would impact Major League Baseball players' pitch speeds. Specifically, we hypothesized that higher temperatures, humidity, and heat index would be negatively associated with pitch speeds. Therefore, our weather variables of interest were: **maximum temperature (ºC), relative humidity (expressed as a percentage), and heat index (ºC).**

In order to become familiar with the data, a series of graphs were created to explore potential associations between pitch speed (miles per hour) with our weather variables of interest.

### Maximum Temperature

Initially, our team hypothesized that the proportion of pitches that are fastballs on any given day may decrease during warmer temperatures, and so a graph of the proportion of fastballs and maximum temperature was created (AD2, below).

``` r
# Create proportion of ff to all pitch dataset

propff_db = pitch_tidy_db %>% 
  mutate(ff = str_detect(pitch_type, "FF")) %>% 
  group_by(y, m, d, tmax) %>% 
  summarise(n = n(),
            ff_sum = sum(ff, na.rm = TRUE),
            prop_ff = (ff_sum/n)*100) %>% 
  mutate(prop_ff = round(prop_ff, digits = 1))

# tmax and proportion of ff  

propff_db %>%
  filter(prop_ff != 0) %>% 
  ggplot(aes(x = prop_ff, y = tmax)) +
  geom_point() +
  geom_smooth() +
  labs(
    title = "Figure 2: Proportion of Thrown Pitches that are Fastballs and Max Temperature",
    x = "Proportion of Pitches that are Fastballs",
    y = "Max Temperature (C)"
  )
```

    ## `geom_smooth()` using method = 'gam' and formula 'y ~ s(x, bs = "cs")'

<img src="report_draft_one_files/figure-markdown_github/prop. ff and tmax-1.png" width="90%" />

However, the graph indicated that absolutely no association existed between these variables.

Next, several graphs were created to explore the associations between the speed of fastballs and maximum temperature (AD3). Graphs that depicted fastball speed and temperature overall did not suggest specific associations (not shown), so the data was instead faceted by various variables to explore the potential for variation by items such as pitch outcome, team, and pitch type. As one example below, fastball pitch speed against max temperature was plotted, and then faceted by pitch outcome (such as Strikeout, Walk, Single, etc).

``` r
pitch_tidy_db %>% 
  filter(pitch_type == "FF") %>% 
  ggplot(aes(x = tmax, y = start_speed)) +
  geom_point() +
  facet_wrap(~event) +
  labs(
    title = "Figure 3: Fastball Pitch Speed and Max Temperature, by Pitch Outcome",
    x = "Max Temperature (C)",
    y = "Pitch Starting Speed (MPH)"
  )
```

<img src="report_draft_one_files/figure-markdown_github/ff speed and temp, by outcome-1.png" width="90%" />

Interestingly, we can see from the increase in points on the right side of facets such as "Double," "Flyout," "Lineout," "Home Run," and other outcomes that pitch outcome may be affected by temperature. It may be possible that when temperatures are higher, pitches that are slightly slower are more likely to result in hits.

In further exploratory analysis, fastball speed and tempearture was also faceted by home team name (shown below), inning, and fowl/ball/strike status, and a graph of max temperature and pitch speed for all pitches, faceted by pitch type, was created (all not shown). There were some variations by inning and home team, and after discussion, we decided this might in fact be representative of changes in pitcher, and differences in whether or not a pitcher from a specific team typically played in warmer or colder weather. We decided to perform further analysis on this hypothesis; plots are shown in the "Additional Analysis: Plots" section of this report.

``` r
pitch_tidy_db %>% 
  filter(pitch_type == "FF") %>% 
  ggplot(aes(x = tmax, y = start_speed)) +
  geom_point() +
  facet_wrap(~team_name) +
  labs(
    title = "Figure 4: Fastball Pitch Speed and Max Temperature, by Home Team",
    x = "Max Temperature (C)",
    y = "Pitch Starting Speed (MPH)"
  )
```

<img src="report_draft_one_files/figure-markdown_github/ff speed and temp, by home team name-1.png" width="90%" />

Further analysis restricted pitch type to the four-seam fastball, and facetted by top and bottom of the inning, to see if Rangers' pitchers were less effected by heat than visiting pitchers. Additionally, a team from a colder climate, the Boston Red Sox, was selected for the same analysis. Month was restricted to September for two reasons: (1) during this month temperatures are in greater flux across the United States, and it may be reasonable to assume Texas is much hotter than other parts of the US and Boston is much colder, and (2) it has been suggested anecdotally that baseball pitchers may be affected most by changes in temperature, rather than by absolute heat or cold, particularly if they are not acclimatized to that temperature.

The resulting plot is shown below.

``` r
# Texas Rangers Stadium in September

tr9 = pitch_tidy_db %>% 
  filter(pitch_type == "FF" & team_name == "Texas Rangers" & m == "09") %>% 
  ggplot(aes(x = tmax, y = start_speed)) +
  geom_point(aes(color = pitcher_name)) +
  geom_smooth(method = 'lm', color = "black") +
  geom_smooth() +
  facet_wrap(~inning_side) +
  labs(
    title = "Fastball Pitch Speed and Max Temperature at Texas Rangers Stadium", subtitle = "Pitchers = Colors, Month = September",
    x = "Max Temperature (C)",
    y = "Pitch Starting Speed (MPH)"
  ) +
  theme(legend.position = "none")

# Boston Red Sox Stadium in September

brs9 = pitch_tidy_db %>% 
  filter(pitch_type == "FF" & team_name == "Boston Red Sox" & m == "09") %>% 
  ggplot(aes(x = tmax, y = start_speed)) +
  geom_point(aes(color = pitcher_name)) +
  geom_smooth(method = 'lm', color = "black") +
  geom_smooth() +
  facet_wrap(~inning_side) +
  labs(
    title = "Fastball Pitch Speed and Max Temperature at Boston Red Sox Stadium", subtitle = "Pitchers = Colors, Month = September",
    x = "Max Temperature (C)",
    y = "Pitch Starting Speed (MPH)"
  ) +
  theme(legend.position = "none")

#Join Graphs

library(patchwork)
tr9 + brs9
```

    ## `geom_smooth()` using method = 'gam' and formula 'y ~ s(x, bs = "cs")'
    ## `geom_smooth()` using method = 'gam' and formula 'y ~ s(x, bs = "cs")'

<img src="report_draft_one_files/figure-markdown_github/ff speed and tmax in sept, rangers and red sox stadiums-1.png" width="90%" />

In the plot, we can see that in Arlington, Texas, at the Texas Rangers's Stadium, the Rangers appear to be less effected by variations in temperature, whereas visiting pitchers (the bottom of the inning) have a decrease in fastball speed at high temperatures. However, it should be noted that this trend may be driven by one visiting pitcher with particularly slow pitches in hotter weather. In Boston, it appears that visiting pitchers have decreases in fastball speed at colder temperatures, whereas Red Sox pitchers are more consistent regardless of temperature.

Relative Humidity
-----------------

We also explored the relationship between pitching speeds and **relative humidity.** Relative humidity is a measure of the amount of moisture in the air (expressed as a percentage). Sweat does not evaporate as quickly when the air is moist as it does in a dry climate. Since evaporation of sweat from the skin is one of the ways the human body cools itself on a hot day, high humidity reduces our natural cooling potential and we feel hotter. Low humidity can also be a problem for outdoor workers in hot, desert-like climates. Sweat evaporates very rapidly in low humidity, which can lead to severe dehydration if a person does not drink enough water throughout the day.

Upon examining the distribution of relative humidity across stadiums, we found that it would be more informative to examine the distribution grouped by the US region the stadium falls in, so we created a new variable, `US_region`.

``` r
# create new variable for US region
pitch_tidy_db = 
   dplyr::mutate(pitch_tidy_db,
                  US_region =
                  ifelse(team_name == "Baltimore Orioles" |
                           team_name == "Boston Red Sox" |
                           team_name == "New York Yankees" |
                           team_name == "New York Mets" |
                           team_name == "Tampa Bay Rays" |
                           team_name == "Philadelphia Phillies" |
                           team_name == "Pittsburgh Pirates" |
                           team_name == "Washington Nationals", "northeast",
                  ifelse(team_name == "Chicago Cubs" |
                           team_name == "Chicago White Sox" |
                           team_name == "Cleveland Indians" |
                           team_name == "Detroit Tigers" |
                           team_name == "Kansas City Royals" |
                           team_name == "Minnesota Twins" |
                           team_name == "Cincinnati Reds" |
                           team_name == "Milwaukee Brewers" |
                           team_name == "St. Louis Cardinals", "midwest",
                  ifelse(team_name == "Texas Rangers" |
                           team_name == "Houston Astros", "southwest",
                  ifelse(team_name == "Los Angeles Angels of Anaheim" |
                           team_name == "Los Angeles Dodgers" |
                           team_name == "Oakland Athletics" |
                           team_name == "Seattle Mariners" |
                           team_name == "San Diego Padres" |
                           team_name == "San Francisco Giants" |
                           team_name == "Colorado Rockies", "west",
                  ifelse(team_name == "Atlanta Braves", "southeast", ""))))))
```

When examining the association between relative humidity and pitch start speed (all pitches and by pitch type), across all US regions, we found no associations. Heat index incorporates temperature and relative humidity, so we next looked at whether that would be more informative.

Heat Index
----------

We first conducted exploratory analyses looking at the **heat index (ºC).**

For people working outdoors in hot weather, both air temperature and humidity affect how hot they feel. The "heat index" is a single value that takes both temperature and humidity into account. The higher the heat index, the hotter the weather feels, since sweat does not readily evaporate and cool the skin. The heat index is a better measure than air temperature alone for estimating the risk to workers from environmental heat sources. Thus, we also assumed that high heat index would negatively affect pitchers' performance.

In the exploratory analysis of heat index, we first examined missing data.

``` r
# Examining heat index missing data
pitch_tidy_db %>% 
  group_by(team_name) %>% 
  filter(is.na(heat_index)) %>% # we are missing pitch speed for 62,971 pitches 
  count() %>% 
  ungroup()# see if the missing data are (roughly) evenly distributed among the teams, Cubs are missing the most but it still seems pretty even 
```

    ## # A tibble: 4 x 2
    ##   team_name              n
    ##   <chr>              <int>
    ## 1 Chicago Cubs         340
    ## 2 Cincinnati Reds      275
    ## 3 New York Mets        260
    ## 4 Pittsburgh Pirates   320

``` r
# # distribution by stadium
# pitch_tidy_db %>% 
#   ggplot(aes(x = heat_index, fill = team_name)) + 
#   geom_density(alpha = .4, adjust = .5)
# # we can see that there are some teams that have a broader distribution; places like San Diego the heat index is pretty stable; we can break up by regions in other analyses 

# # numerical summary
# skim(pitch_tidy_db$heat_index)
# 
# heat_index_df =
#   pitch_tidy_db %>%
#   group_by(team_name, m) %>%
#   summarize(mean_heat_index = mean(heat_index, na.rm = TRUE),
#             median_heat_index = median(heat_index),
#             sd_heat_index = sd(heat_index))

# # plot the above table/info 
# heat_index_df %>% 
#   ggplot(aes(x = m, y = mean_heat_index, color = team_name)) +
#   geom_point() + geom_line()


# the teams with the greatest range/variability in heat index are the Minnesota Twins, Washington Nationals, Cincinatti Reds, New York Yankees, and Kansas City Royals

# the teams with the lowest range/variability in heat index (excluding values of 0.00) are the Los Angeles Angels of Anaheim, Detroit Tigers, Chicago White Sox, St. Louis Cardinals

# maybe we can do comparisons within certain teams, for example how does someone on the Washington Nationals perform in high vs. low heat index 

# pitch_tidy_db %>% 
#   filter(team_name == "Washington Nationals") %>% 
#   ggplot(aes(x = m, y = start_speed)) +
#   geom_boxplot()
# tried adding geom_jitter but there are too many points 
# more outliers at the warmer months with hotter heat index, but overall start speed looks the same 
```

While the Cubs were missing the most heat index data, the distribution of missing heat index data was fairly evenly distributed across all teams. The distribution of the heat index overall is normally distributed (not shown).

We next explored the distribution of the heat index across US regions the stadiums fall in:

``` r
# heat index distribution by US region 
pitch_tidy_db %>% 
  ggplot(aes(x = heat_index, fill = US_region)) + 
  geom_density(alpha = .4, adjust = .5)
```

    ## Warning: Removed 1195 rows containing non-finite values (stat_density).

<img src="report_draft_one_files/figure-markdown_github/heat index distributions-1.png" width="90%" />

``` r
# southwest followed by southeast has highest heat index, west is most stable/pleasant

pitch_tidy_db %>% 
  ggplot(aes(x = heat_index)) + 
  geom_density(alpha = .4, adjust = .5) +
  facet_wrap(~US_region) +
    labs(
    title = "Distribution of Heat Index by US Region",
    x = "Heat Index (ºC)",
    y = "Density"
  )
```

    ## Warning: Removed 1195 rows containing non-finite values (stat_density).

<img src="report_draft_one_files/figure-markdown_github/heat index distributions-2.png" width="90%" />

``` r
# west has pretty normal distribution, southeast and southwest skewed 

# =======

#Brennan - add shiny plot in here
#>>>>>>> 376b2d6b714edd4f03e49db1f82b4564e9f81571
```

Exploratory analyses examining pitch speed (of all pitches) vs. heat index indicated that pitch start speed may slightly decrease at extreme heat indexes (e.g. over 35 ºC).

``` r
# pitch speed (all types) vs. heat index, colored by US region 
pitch_tidy_db %>% 
  ggplot(aes(x = heat_index, y = start_speed, color = US_region)) +
  geom_point(alpha = .3) +
  facet_wrap(~pitch_type) +
    labs(
    title = "Pitch Start Speed vs. Heat Index", subtitle = "Colored by US Region",
    x = "Heat Index (ºC)",
    y = "Pitch Starting Speed (MPH)"
  )
```

    ## Warning: Removed 64156 rows containing missing values (geom_point).

<img src="report_draft_one_files/figure-markdown_github/pitch speed (all types) vs. heat index-1.png" width="90%" />

``` r
# fastball affected by higher heat index??
# looks like fastball speed decreases with higher heat index (at least in the southwest and west)
```

However, when examining this further by categorizing heat index (e.g. above and below 35) and looking at the relationship between pitch speed and heat index, there was no obvious association.

Additional Anlaysis: Plots
--------------------------

The plot of fastball pitch speed by max temperature, faceted by home team, suggested that temperature may affect pitches thrown at several stadiums. Texas Rangers stadium was selected as an example becuase it had a number of pitches with decreased speed at higher temperatures, and a Shiny plot showing associations between pitch speed and temperature at the Texas Rangers's stadium was created. A user can select the visiting team to see how its pitch speed is affected by temperature when playing @ the Texas Rangers.

Additional Analysis: Models
---------------------------

``` r
#Stephen
```

Discussion
----------

The multitude of available MLB data provides a golden opportunity to use statistical analysis to determine how environmental factors, such as weather, impact sports performance at the very apex of human accomplishment. Using data from more than 2.5 million pitches, this project attempted to answer a small portion of this question, by focusing on the impact of temperature, heat index, and relative humidity on starting pitch speed of four-seam fastballs thrown by MLB pitchers in the 2016, 2017, and 2018 seasons.

Results suggest that there may be a small but measurable effect of certain weather parameters, including temperature and heat index, on the start speed of four-seam fastball pitches, such that increases in heat index and temperature result in slight increases in pitch speed. This pitch type is the most commonly used in the MLB (currently), and represents the extreme in human athletic accomplishment (105 mph!!!). Anecdotally, pitchers have reported feeling looser and being able to throw faster in warmer weather, supporting the statistical analysis found in this report. Additionally, this analysis suggests that associations between temperature, heat index, and pitch speed may vary by region and location, but more advanced statistical analysis needs to be completed to determine what role (if any) this plays. One hypothesis is that pitchers may become acclimatized to weather, so that they are less impacted by extreme temperatures and heat index if they are used to playing in that type of weather. Thus, it would follow that region and more specifically, homefield location, impact the association between temperature, heat index, and pitch speed.

Regardless of the weather, one thing is certain: MLB pitchers throw four-seam fastballs at remarkable speeds, and do so in a variety of sometimes extreme weather conditions over an extensive season. Analysis of how various weather events impact pitching performance will hopefully assist athletes to push the envelope even further, matching their own abilities with weather conditions to shatter new barriers. Here’s to the next few decades of sports performance, backed by advanced statistical analysis and a better understanding of how weather impacts pitch speed!
