Practice Exam
=============

This practice exam asks you to do several code wrangling tasks that we
have done in class so far.

    ## Warning: package 'tidyverse' was built under R version 3.5.2

    ## ── Attaching packages ────────────────────────────────────────────────────────────────── tidyverse 1.3.0 ──

    ## ✓ ggplot2 3.2.1     ✓ purrr   0.3.3
    ## ✓ tibble  2.1.3     ✓ dplyr   0.8.4
    ## ✓ tidyr   1.0.2     ✓ stringr 1.4.0
    ## ✓ readr   1.3.1     ✓ forcats 0.4.0

    ## Warning: package 'ggplot2' was built under R version 3.5.2

    ## Warning: package 'tibble' was built under R version 3.5.2

    ## Warning: package 'tidyr' was built under R version 3.5.2

    ## Warning: package 'purrr' was built under R version 3.5.2

    ## Warning: package 'dplyr' was built under R version 3.5.2

    ## Warning: package 'stringr' was built under R version 3.5.2

    ## Warning: package 'forcats' was built under R version 3.5.2

    ## ── Conflicts ───────────────────────────────────────────────────────────────────── tidyverse_conflicts() ──
    ## x dplyr::filter() masks stats::filter()
    ## x dplyr::lag()    masks stats::lag()

    ## Warning: package 'nycflights13' was built under R version 3.5.2

Make a plot with three facets, one for each airport in the weather data.
The x-axis should be the day of the year (1:365) and the y-axis should
be the mean temperature recorded on that day, at that airport.

    library(lubridate)

    ## 
    ## Attaching package: 'lubridate'

    ## The following object is masked from 'package:base':
    ## 
    ##     date

    weather <- weather %>% 
      mutate(day_of_year = yday(time_hour)) 
    ggplot(data = weather) + 
      geom_point(mapping = aes(x = day_of_year, y = temp)) +
      facet_grid(~ origin)

    ## Warning: Removed 1 rows containing missing values (geom_point).

![](README2_files/figure-markdown_strict/unnamed-chunk-2-1.png)

Make a non-tidy matrix of that data where each row is an airport and
each column is a day of the year.

    library(tidyr)
    library(dplyr)
    weather %>% group_by(origin,day_of_year) %>% summarize(average = mean(temp)) %>% 
          pivot_wider(names_from = origin, values_from = average)

    ## # A tibble: 364 x 4
    ##    day_of_year   EWR   JFK   LGA
    ##          <dbl> <dbl> <dbl> <dbl>
    ##  1           1  36.8  36.9  37.2
    ##  2           2  28.7  28.6  28.8
    ##  3           3  29.6  30.1  30.3
    ##  4           4  34.3  34.7  35.8
    ##  5           5  36.6  36.8  38.3
    ##  6           6  39.9  39.3  41.0
    ##  7           7  40.3  40.1  41.4
    ##  8           8  38.6  39.4  42.3
    ##  9           9  42.1  42.7  44.9
    ## 10          10  43.6  43.6  44.3
    ## # … with 354 more rows

For each (airport, day) contruct a tidy data set of the airport’s
“performance” as the proportion of flights that departed less than an
hour late.

    flights %>% group_by(origin,day)%>% summarize(proportion = sum(dep_delay<60, na.rm = T)/ length(dep_delay)) %>%  pivot_wider(names_from = origin, values_from = proportion)

    ## # A tibble: 31 x 4
    ##      day   EWR   JFK   LGA
    ##    <int> <dbl> <dbl> <dbl>
    ##  1     1 0.874 0.900 0.882
    ##  2     2 0.863 0.902 0.902
    ##  3     3 0.921 0.936 0.903
    ##  4     4 0.948 0.940 0.964
    ##  5     5 0.919 0.945 0.915
    ##  6     6 0.912 0.933 0.935
    ##  7     7 0.863 0.904 0.876
    ##  8     8 0.784 0.809 0.773
    ##  9     9 0.838 0.875 0.839
    ## 10    10 0.823 0.865 0.849
    ## # … with 21 more rows

Construct a tidy data set to that give weather summaries for each
(airport, day). Use the total precipitation, minimum visibility, maximum
wind\_gust, and average wind\_speed.

    weather %>% group_by(origin,day) %>% select(precip,visib,wind_speed) %>% summarize(totalprec = sum(precip), minivis = min(visib), averagespeed = mean(wind_speed)) 

    ## Adding missing grouping variables: `origin`, `day`

    ## # A tibble: 93 x 5
    ## # Groups:   origin [3]
    ##    origin   day totalprec minivis averagespeed
    ##    <chr>  <int>     <dbl>   <dbl>        <dbl>
    ##  1 EWR        1      1.91    1.5          9.47
    ##  2 EWR        2      1.25    2            8.63
    ##  3 EWR        3      1.57    3            9.09
    ##  4 EWR        4      0       4           10.0 
    ##  5 EWR        5      0.08    0.25         7.72
    ##  6 EWR        6      0.94    0.25         9.32
    ##  7 EWR        7      4.46    2           10.0 
    ##  8 EWR        8      3.63    0.5         10.1 
    ##  9 EWR        9      2.4     0.5          9.14
    ## 10 EWR       10      1.72    0.25         8.90
    ## # … with 83 more rows

Construct a linear model to predict the performance of each
(airport,day) using the weather summaries and a “fixed effect” for each
airport. Display the summaries.

    #flights1 <- flights %>% mutate(performance = sum(dep_delay<60, na.rm = T)/ length(dep_delay))
    #model <- weather %>% group_by(origin,day) %>% select(precip,visib,wind_speed) %>% mutate(totalprec = sum(precip), minivis = min(visib), averagespeed = mean(wind_speed))
    #left_join(model,flights1, by="origin")

    #lm(performance~origin + totalprec + minivis + averagespeed, data = model)

Repeat the above, but only for EWR. Obviously, exclude the fixed effect
for each airport.

    #model <- weather %>% group_by(origin,day) %>% select(precip,visib,wind_speed) %>% mutate(totalprec = sum(precip), minivis = min(visib), averagespeed = mean(wind_speed)) %>% left_join(flights1, by= "origin" ) %>% filter(origin = "EWR") %>% lm(performance~origin + totalprec + minivis + averagespeed, data = model)
