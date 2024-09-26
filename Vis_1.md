vis_1
================
2024-09-26

rnoaa package (keep track of all the weather data, interact with the
data that they make that data public) Which weather stations that i want
to use, which variables, and from which dates

Mutate step: creating new variable called name using case match,
re-naming the weather stations tmax are to change since before it was in
10ths of a degree change

``` r
weather_df = 
  rnoaa::meteo_pull_monitors(
    c("USW00094728", "USW00022534", "USS0023B17S"),
    var = c("PRCP", "TMIN", "TMAX"), 
    date_min = "2021-01-01",
    date_max = "2022-12-31") |>
  mutate(
    name = case_match(
      id, 
      "USW00094728" ~ "CentralPark_NY", 
      "USW00022534" ~ "Molokai_HI",
      "USS0023B17S" ~ "Waterhole_WA"),
    tmin = tmin / 10,
    tmax = tmax / 10) |>
  select(name, id, everything())
```

    ## using cached file: /Users/florapang/Library/Caches/org.R-project.R/R/rnoaa/noaa_ghcnd/USW00094728.dly

    ## date created (size, mb): 2024-09-26 10:19:28.845047 (8.651)

    ## file min/max dates: 1869-01-01 / 2024-09-30

    ## using cached file: /Users/florapang/Library/Caches/org.R-project.R/R/rnoaa/noaa_ghcnd/USW00022534.dly

    ## date created (size, mb): 2024-09-26 10:19:37.122774 (3.932)

    ## file min/max dates: 1949-10-01 / 2024-09-30

    ## using cached file: /Users/florapang/Library/Caches/org.R-project.R/R/rnoaa/noaa_ghcnd/USS0023B17S.dly

    ## date created (size, mb): 2024-09-26 10:19:39.66549 (1.036)

    ## file min/max dates: 1999-09-01 / 2024-09-30

Making our first plot - Define the data set (first) - Define aesthetic
mapping (x and y axis) - then geometrics ontop Geom_point(put points for
all of the data points, scatter plot)

``` r
ggplot(weather_df, aes(x = tmin, y = tmax)) + 
  geom_point()
```

    ## Warning: Removed 17 rows containing missing values or values outside the scale range
    ## (`geom_point()`).

![](Vis_1_files/figure-gfm/unnamed-chunk-2-1.png)<!-- -->

can also write like this since sometimes want to establish the data
frame first then do mutate columns before plotting

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax)) +
  geom_point()
```

    ## Warning: Removed 17 rows containing missing values or values outside the scale range
    ## (`geom_point()`).

![](Vis_1_files/figure-gfm/unnamed-chunk-3-1.png)<!-- -->

``` r
ggp_weather_scatterplot = 
  weather_df %>% 
  ggplot(aes(x = tmin, y = tmax)) + 
  geom_point()

ggp_weather_scatterplot
```

    ## Warning: Removed 17 rows containing missing values or values outside the scale range
    ## (`geom_point()`).

![](Vis_1_files/figure-gfm/unnamed-chunk-4-1.png)<!-- -->

Asking which rows are missing values in their tmax column (Check why
some rows are missing)

``` r
weather_df %>% 
  filter(is.na(tmax))
```

    ## # A tibble: 17 × 6
    ##    name         id          date        prcp  tmax  tmin
    ##    <chr>        <chr>       <date>     <dbl> <dbl> <dbl>
    ##  1 Molokai_HI   USW00022534 2022-05-31    NA    NA    NA
    ##  2 Waterhole_WA USS0023B17S 2021-03-09    NA    NA    NA
    ##  3 Waterhole_WA USS0023B17S 2021-12-07    51    NA    NA
    ##  4 Waterhole_WA USS0023B17S 2021-12-31     0    NA    NA
    ##  5 Waterhole_WA USS0023B17S 2022-02-03     0    NA    NA
    ##  6 Waterhole_WA USS0023B17S 2022-08-09    NA    NA    NA
    ##  7 Waterhole_WA USS0023B17S 2022-08-10    NA    NA    NA
    ##  8 Waterhole_WA USS0023B17S 2022-08-11    NA    NA    NA
    ##  9 Waterhole_WA USS0023B17S 2022-08-12    NA    NA    NA
    ## 10 Waterhole_WA USS0023B17S 2022-08-13    NA    NA    NA
    ## 11 Waterhole_WA USS0023B17S 2022-08-14    NA    NA    NA
    ## 12 Waterhole_WA USS0023B17S 2022-08-15    NA    NA    NA
    ## 13 Waterhole_WA USS0023B17S 2022-08-16    NA    NA    NA
    ## 14 Waterhole_WA USS0023B17S 2022-08-17    NA    NA    NA
    ## 15 Waterhole_WA USS0023B17S 2022-08-18    NA    NA    NA
    ## 16 Waterhole_WA USS0023B17S 2022-08-19    NA    NA    NA
    ## 17 Waterhole_WA USS0023B17S 2022-12-31    76    NA    NA

\##Fancier scatterplots

colour is like x and y is a feature of the plot (need to name) alpha
(makes more transparent to see better) geom_smooth smooth line fitting
through the points (SE is standard error)

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax, color = name)) + 
  geom_point(alpha = .3, size = .8) + 
  geom_smooth(se = FALSE)
```

    ## `geom_smooth()` using method = 'loess' and formula = 'y ~ x'

    ## Warning: Removed 17 rows containing non-finite outside the scale range
    ## (`stat_smooth()`).

    ## Warning: Removed 17 rows containing missing values or values outside the scale range
    ## (`geom_point()`).

![](Vis_1_files/figure-gfm/unnamed-chunk-6-1.png)<!-- -->

Where you define the aesthetics matters

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax)) + 
  geom_point(aes(color = name), alpha = .3, size = .8) + 
  geom_smooth(se = FALSE)
```

    ## `geom_smooth()` using method = 'gam' and formula = 'y ~ s(x, bs = "cs")'

    ## Warning: Removed 17 rows containing non-finite outside the scale range
    ## (`stat_smooth()`).

    ## Warning: Removed 17 rows containing missing values or values outside the scale range
    ## (`geom_point()`).

![](Vis_1_files/figure-gfm/unnamed-chunk-7-1.png)<!-- -->

The first line defines everything that comes out of the data frame
(colour = name is applied to everything that comes after, geom_point and
smooth) But geom_smooth does not know anything about colour therefore
there is only one smooth line Mostly issue is aesthetic mismatch

Use faceting real quick

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax, color = name)) + 
  geom_point(alpha = .3) + 
  geom_smooth(se = FALSE) +
  facet_grid(. ~ name)
```

    ## `geom_smooth()` using method = 'loess' and formula = 'y ~ x'

    ## Warning: Removed 17 rows containing non-finite outside the scale range
    ## (`stat_smooth()`).

    ## Warning: Removed 17 rows containing missing values or values outside the scale range
    ## (`geom_point()`).

![](Vis_1_files/figure-gfm/unnamed-chunk-8-1.png)<!-- -->

dot means that we have don’t have anythign seperating by row but there
is seperating by coloum (selected to seperate by the name column)

Let’s make a somewhat more interesting scatterplot Seeing temperature
trends (since date is the x axis) and the size of the dots are
representative of precipitation

``` r
weather_df %>% 
  ggplot(aes(x = date, y = tmax, colour = name, size = prcp)) +
  geom_point(alpha = .3) + 
  geom_smooth(se = FALSE) + 
  facet_grid(. ~ name)
```

    ## Warning: Using `size` aesthetic for lines was deprecated in ggplot2 3.4.0.
    ## ℹ Please use `linewidth` instead.
    ## This warning is displayed once every 8 hours.
    ## Call `lifecycle::last_lifecycle_warnings()` to see where this warning was
    ## generated.

    ## `geom_smooth()` using method = 'loess' and formula = 'y ~ x'

    ## Warning: Removed 17 rows containing non-finite outside the scale range
    ## (`stat_smooth()`).

    ## Warning: The following aesthetics were dropped during statistical transformation: size.
    ## ℹ This can happen when ggplot fails to infer the correct grouping structure in
    ##   the data.
    ## ℹ Did you forget to specify a `group` aesthetic or to convert a numerical
    ##   variable into a factor?
    ## The following aesthetics were dropped during statistical transformation: size.
    ## ℹ This can happen when ggplot fails to infer the correct grouping structure in
    ##   the data.
    ## ℹ Did you forget to specify a `group` aesthetic or to convert a numerical
    ##   variable into a factor?
    ## The following aesthetics were dropped during statistical transformation: size.
    ## ℹ This can happen when ggplot fails to infer the correct grouping structure in
    ##   the data.
    ## ℹ Did you forget to specify a `group` aesthetic or to convert a numerical
    ##   variable into a factor?

    ## Warning: Removed 19 rows containing missing values or values outside the scale range
    ## (`geom_point()`).

![](Vis_1_files/figure-gfm/unnamed-chunk-9-1.png)<!-- -->

\##Learning Assessment

focuses only on Central Park, converts temperatures to Fahrenheit, makes
a scatterplot of min vs. max temperature, and overlays a linear
regression line

``` r
weather_df %>% 
  filter(name == "CentralPark_NY") %>% 
  mutate(
    tmax_fahr = tmax * (9 / 5) + 32,
    tmin_far = tmin * (9 / 5) + 32
  ) %>% 
  ggplot(aes(x = tmin_far, y = tmax_fahr)) +
  geom_point() +
  geom_smooth(method = "lm", se = FALSE)
```

    ## `geom_smooth()` using formula = 'y ~ x'

![](Vis_1_files/figure-gfm/unnamed-chunk-10-1.png)<!-- -->

The method = lm in geom_smooth give straight line through data points
rather than curvy Data processing piece useful before the visualisation

\##Small Things

Do not need to define everything, could just have a singular smooth line
through

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax)) + 
  geom_smooth(se = FALSE)
```

    ## `geom_smooth()` using method = 'gam' and formula = 'y ~ s(x, bs = "cs")'

    ## Warning: Removed 17 rows containing non-finite outside the scale range
    ## (`stat_smooth()`).

![](Vis_1_files/figure-gfm/unnamed-chunk-11-1.png)<!-- -->

Scatter plot grows less and less useful as the dataframe grows bigger
(eg. 1000 variable)

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax)) +
  geom_hex()
```

    ## Warning: Removed 17 rows containing non-finite outside the scale range
    ## (`stat_binhex()`).

![](Vis_1_files/figure-gfm/unnamed-chunk-12-1.png)<!-- --> Geom_hex does
density

Need to be careful of the order of the aesthetic The points want to be
blue, but if you set colour = blue in the ggplot then ggplot will try
assign ‘blue’ to every data point and then colour with another colour
based on seeing that each data point is a part of the ‘blue’ group

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax)) + 
  geom_point(colour = 'blue')
```

    ## Warning: Removed 17 rows containing missing values or values outside the scale range
    ## (`geom_point()`).

![](Vis_1_files/figure-gfm/unnamed-chunk-13-1.png)<!-- -->

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax, colour = "blue")) + 
  geom_point()
```

    ## Warning: Removed 17 rows containing missing values or values outside the scale range
    ## (`geom_point()`).

![](Vis_1_files/figure-gfm/unnamed-chunk-14-1.png)<!-- -->

variable name do not need ’‘, but not in variable goes into’’

\##Univariate plots

``` r
weather_df %>% 
  ggplot(aes(x = tmin)) + 
  geom_histogram()
```

    ## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.

    ## Warning: Removed 17 rows containing non-finite outside the scale range
    ## (`stat_bin()`).

![](Vis_1_files/figure-gfm/unnamed-chunk-15-1.png)<!-- -->

Histograms are great! But get a little clunky

Colour defines the outline of the histogram bar (so need to use fill and
not colour)

``` r
weather_df %>% 
  ggplot(aes(x = tmin, color = name)) + 
  geom_histogram()
```

    ## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.

    ## Warning: Removed 17 rows containing non-finite outside the scale range
    ## (`stat_bin()`).

![](Vis_1_files/figure-gfm/unnamed-chunk-16-1.png)<!-- -->

``` r
weather_df %>% 
  ggplot(aes(x = tmin, fill = name)) + 
  geom_histogram()
```

    ## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.

    ## Warning: Removed 17 rows containing non-finite outside the scale range
    ## (`stat_bin()`).

![](Vis_1_files/figure-gfm/unnamed-chunk-17-1.png)<!-- -->

Need histograms next to each other bars rather than bars ontop of each
other

``` r
weather_df %>% 
  ggplot(aes(x = tmin, fill = name)) + 
  geom_histogram(position = "dodge")
```

    ## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.

    ## Warning: Removed 17 rows containing non-finite outside the scale range
    ## (`stat_bin()`).

![](Vis_1_files/figure-gfm/unnamed-chunk-18-1.png)<!-- -->

how would i fix this? maybe facet to seperate?

``` r
weather_df %>% 
  ggplot(aes(x = tmin, fill = name)) + 
  geom_histogram() + 
  facet_grid(. ~ name)
```

    ## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.

    ## Warning: Removed 17 rows containing non-finite outside the scale range
    ## (`stat_bin()`).

![](Vis_1_files/figure-gfm/unnamed-chunk-19-1.png)<!-- -->

maybe a density plot instead (based on what you want to see in the data
can use different plot to viz) Density plots are like smooth histograms

``` r
weather_df %>% 
  ggplot(aes(x = tmin, fill = name)) +
  geom_density(alpha = .3)
```

    ## Warning: Removed 17 rows containing non-finite outside the scale range
    ## (`stat_density()`).

![](Vis_1_files/figure-gfm/unnamed-chunk-20-1.png)<!-- -->

\##Boxplots

``` r
weather_df %>% 
  ggplot(aes(x = name, y = tmin, fill = name)) + 
  geom_boxplot()
```

    ## Warning: Removed 17 rows containing non-finite outside the scale range
    ## (`stat_boxplot()`).

![](Vis_1_files/figure-gfm/unnamed-chunk-21-1.png)<!-- -->

\##Violin plot

``` r
weather_df %>% 
  ggplot(aes(x = name, y = tmin, fill = name)) + 
  geom_violin()
```

    ## Warning: Removed 17 rows containing non-finite outside the scale range
    ## (`stat_ydensity()`).

![](Vis_1_files/figure-gfm/unnamed-chunk-22-1.png)<!-- --> Taking
densities too Can be better than boxplots since can see when things are
skewed, uni/multi modal Plots with a lot of data, making a violin plot
can see if something is weird going on (more insight into the actual
data distribution)

\##Ridge plot

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = name)) +
  geom_density_ridges()
```

    ## Picking joint bandwidth of 1.41

    ## Warning: Removed 17 rows containing non-finite outside the scale range
    ## (`stat_density_ridges()`).

![](Vis_1_files/figure-gfm/unnamed-chunk-23-1.png)<!-- -->

Ridge plots work when there are multiple groups that you need to make
comparisons between The y-axis is always seperating between categorical
variables (different groups)

\##Learning assessment

Make plots that compare precipitation across locations. Try a histogram,
a density plot, a boxplot, a violin plot, and a ridgeplot; use aesthetic
mappings to make your figure readable

Not histogram (looking skewed not clear)

``` r
weather_df %>% 
  ggplot(aes(x = prcp, fill = name)) + 
  geom_histogram() + 
  facet_grid(. ~ name)
```

    ## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.

    ## Warning: Removed 15 rows containing non-finite outside the scale range
    ## (`stat_bin()`).

![](Vis_1_files/figure-gfm/unnamed-chunk-24-1.png)<!-- -->

NOT density

``` r
weather_df %>% 
  ggplot(aes(x = prcp, fill = name)) +
  geom_density(alpha = .3)
```

    ## Warning: Removed 15 rows containing non-finite outside the scale range
    ## (`stat_density()`).

![](Vis_1_files/figure-gfm/unnamed-chunk-25-1.png)<!-- -->

Boxplot is no, since data is heavily skewed (there are a lot of
outliers, better than density plots)

``` r
weather_df %>% 
  ggplot(aes(x = name, y = prcp, fill = name)) + 
  geom_boxplot()
```

    ## Warning: Removed 15 rows containing non-finite outside the scale range
    ## (`stat_boxplot()`).

![](Vis_1_files/figure-gfm/unnamed-chunk-26-1.png)<!-- -->

``` r
weather_df %>% 
  ggplot(aes(x = name, y = prcp, fill = name)) + 
  geom_violin()
```

    ## Warning: Removed 15 rows containing non-finite outside the scale range
    ## (`stat_ydensity()`).

![](Vis_1_files/figure-gfm/unnamed-chunk-27-1.png)<!-- -->

``` r
weather_df %>% 
  ggplot(aes(x = prcp, y = name)) +
  geom_density_ridges()
```

    ## Picking joint bandwidth of 9.22

    ## Warning: Removed 15 rows containing non-finite outside the scale range
    ## (`stat_density_ridges()`).

![](Vis_1_files/figure-gfm/unnamed-chunk-28-1.png)<!-- -->

Realise that most days that it isn’t raining, so filter just on days
that it is raining

``` r
weather_df %>% 
  filter(prcp > 10) %>% 
  ggplot(aes(x = prcp, fill = name)) +
  geom_density(alpha = .3)
```

![](Vis_1_files/figure-gfm/unnamed-chunk-29-1.png)<!-- -->
