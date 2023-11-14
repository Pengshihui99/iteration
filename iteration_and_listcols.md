iteration_and_listcols
================
Shihui Peng
2023-11-14

    ## ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
    ## ✔ dplyr     1.1.3     ✔ readr     2.1.4
    ## ✔ forcats   1.0.0     ✔ stringr   1.5.0
    ## ✔ ggplot2   3.4.4     ✔ tibble    3.2.1
    ## ✔ lubridate 1.9.3     ✔ tidyr     1.3.0
    ## ✔ purrr     1.0.2     
    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()
    ## ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors
    ## 
    ## Attaching package: 'rvest'
    ## 
    ## 
    ## The following object is masked from 'package:readr':
    ## 
    ##     guess_encoding

# lists

``` r
# diff stuffs w same length -- we can use list and tibble. here let's see tibble example:
vec_numeric = 1 : 4
vec_char = c("My", "name", "is", "sherly")
vec_logical = c(TRUE, TRUE, TRUE, FALSE)

tibble(
  num = vec_numeric,
  char = vec_char,
  logical = vec_logical
)
```

    ## # A tibble: 4 × 3
    ##     num char   logical
    ##   <int> <chr>  <lgl>  
    ## 1     1 My     TRUE   
    ## 2     2 name   TRUE   
    ## 3     3 is     TRUE   
    ## 4     4 sherly FALSE

``` r
# diff stuff w diff length -- use list
l = 
  list(
  vec_numeric = 5:8,
  mat = matrix(1:8, 2, 4),
  vec_logical = c(TRUE, FALSE),
  summary = summary(rnorm(1000))
  )

# accessing lines -- we can use $ here!!
l$summary
```

    ##     Min.  1st Qu.   Median     Mean  3rd Qu.     Max. 
    ## -2.77833 -0.59597  0.04622  0.04620  0.68857  3.33073

``` r
l$mat
```

    ##      [,1] [,2] [,3] [,4]
    ## [1,]    1    3    5    7
    ## [2,]    2    4    6    8

# `for` loops

here, i create a list…

``` r
list_norm_samples = 
   list(
    a = rnorm(20, 3, 1),
    b = rnorm(20, 0, 5),
    c = rnorm(20, 10, .2),
    d = rnorm(20, -3, 1)
   )
```

create a function for mean and sd…

``` r
mean_and_sd = function(x) {
  
  if (!is.numeric(x)) {
    stop("Argument x should be numeric")
  } else if (length(x) == 1) {
    stop("Cannot be computed for length 1 vectors")
  }
  
  mean_x = mean(x)
  sd_x = sd(x)

  tibble(
    mean = mean_x, 
    sd = sd_x
  )
}
```

i want to apply this function to each of the element in my list above…

``` r
# for previous...
mean_and_sd(list_norm_samples$a)
```

    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  2.85 0.953

``` r
mean_and_sd(list_norm_samples$b)
```

    ## # A tibble: 1 × 2
    ##     mean    sd
    ##    <dbl> <dbl>
    ## 1 -0.791  4.69

``` r
mean_and_sd(list_norm_samples$c)
```

    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  9.92 0.183

``` r
mean_and_sd(list_norm_samples$d)
```

    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1 -3.11  1.04

``` r
# use a for loop
## creates a list of length 4, where each element of the list is initialized as NULL. This is a way to pre-allocate a list of a specific length.
## Below, I define an output list with the same number of entries as my target dataframe; a sequence to iterate over; and a for loop body that applies the mean_and_sd function for each sequence element and saves the result.
output_mean_sd = vector('list', length = 4)
output_median = vector('list', length = 4)

for (i in 1:4) {
  output_mean_sd[[i]] = mean_and_sd(list_norm_samples[[i]])
  output_median[[i]] = median(list_norm_samples[[i]])
}
```

- `mean_and_sd(list_norms[[1]])`: applying this function to the first
  element of the list

A criticism of for loops is that there’s a lot of overhead – you have to
define your output vector / list, there’s the for loop bookkeeping to
do, etc – that distracts from the purpose of the code. In this case, we
want to apply mean_and_sd to each element of list_norms, but we have to
scan inside the for loop to figure that out.

The map functions in purrr try to make the purpose of your code clear.
Compare the loop above to the line below.

# use `map`

``` r
# map across my list of normal samples, and the function that i want to apply is mean_and_sd
output_map_mean_sd = map(list_norm_samples, mean_and_sd)
output_map_median = map(list_norm_samples, median)

# median & summary are an existed function in r
map(list_norm_samples, summary)
```

    ## $a
    ##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
    ##   1.103   2.202   2.680   2.848   3.508   4.678 
    ## 
    ## $b
    ##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
    ## -8.6798 -4.4509 -0.1932 -0.7909  2.1281  8.2747 
    ## 
    ## $c
    ##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
    ##   9.519   9.780   9.942   9.918  10.075  10.179 
    ## 
    ## $d
    ##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
    ## -4.5036 -4.0657 -3.2319 -3.1096 -2.4225 -0.5429

- Again, both options produce the same output, but the map places the
  focus squarely on the function you want to apply by removing much of
  the bookkeeping.

## map variants

``` r
# we use 'map_dbl' because median outputs a single numeric value each time; the result is a vector instead of a list. Using the '.id' argument keeps the names of the elements in the input list.
# If we tried to use 'map_int' or 'map_lgl', we’d get an error because the output of median isn’t a integer or a logical. This is a good way to help catch mistakes when they arise.
output = map_dbl(list_norm_samples, median, .id = "input")

# we know mean_and_sd produces a data frame, we can use the output-specific 'map_dfr'; this will produce a single data frame.
output = map_dfr(list_norm_samples, mean_and_sd, .id = "input")

# 'map2 (and map2_dbl, etc)' is helpful when your function has two arguments. 
# output = map2(input_1, input_2, \(x,y) func(arg_1 = x, arg_2 = y))
```

# create DF

You will need to be able to manipulate list columns, but usual
operations for columns that might appear in mutate (like mean or recode)
often don’t apply to the entries in a list column. Instead, recognizing
list columns as list columns motivates an approach for working with them

``` r
# it is not printing anything for samp col - it's telling you that these are named lists.
listcol_df =
  tibble(
    name = c("a", "b", "c", "d"),
    samp = list_norm_samples
  )
```

``` r
# this will pull out a list - the 'samp' column is a list column
listcol_df |> pull(samp)
```

    ## $a
    ##  [1] 4.677512 3.079474 2.143573 2.221223 2.619064 1.102642 2.928229 1.969135
    ##  [9] 3.670695 2.638712 2.720694 2.354110 2.484271 1.625444 4.010378 3.453706
    ## [17] 3.274880 1.728209 3.973595 4.280787
    ## 
    ## $b
    ##  [1] -0.1293827 -8.6798189 -4.7258794 -2.3055982  2.7978307  1.0368585
    ##  [7]  2.0025813 -0.2570483  4.1247235 -8.3903375 -4.6163971 -3.2872417
    ## [13]  8.2747090 -5.8196457 -4.3957137  1.1230652  7.1027498  2.5046034
    ## [19]  1.4241480 -3.6028742
    ## 
    ## $c
    ##  [1] 10.179292  9.713506 10.077199  9.777869  9.984899  9.722556 10.054943
    ##  [8] 10.109262  9.892181  9.631700  9.841942  9.781186 10.092739 10.003353
    ## [15] 10.142743 10.074544  9.873716  9.930961  9.953265  9.519458
    ## 
    ## $d
    ##  [1] -3.8410157 -3.5255776 -2.0353239 -1.8313122 -2.9624744 -4.0594504
    ##  [7] -3.2031989 -3.2605225 -4.5035928 -4.1915465 -0.5429258 -4.4214052
    ## [13] -4.1706712 -2.3872712 -2.4959271 -3.0336040 -1.9322116 -4.0843643
    ## [19] -2.4342445 -3.2760908

``` r
mean_and_sd(listcol_df$samp[[1]])
```

    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  2.85 0.953

``` r
mean_and_sd(listcol_df$samp[[2]])
```

    ## # A tibble: 1 × 2
    ##     mean    sd
    ##    <dbl> <dbl>
    ## 1 -0.791  4.69

``` r
mean_and_sd(listcol_df$samp[[3]])
```

    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  9.92 0.183

``` r
map(listcol_df$samp, mean_and_sd)
```

    ## $a
    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  2.85 0.953
    ## 
    ## $b
    ## # A tibble: 1 × 2
    ##     mean    sd
    ##    <dbl> <dbl>
    ## 1 -0.791  4.69
    ## 
    ## $c
    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  9.92 0.183
    ## 
    ## $d
    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1 -3.11  1.04

``` r
# The map function returns a list; we could store the results as a new list column … !!!
# We’ve been using mutate to define a new variable in a data frame, especially one that is a function of an existing variable. That’s exactly what we will keep doing.

listcol_df |> 
  mutate(mean_sd = map(samp, mean_and_sd),
         median = map(samp, median)) |>
  pull(mean_sd) 
```

    ## $a
    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  2.85 0.953
    ## 
    ## $b
    ## # A tibble: 1 × 2
    ##     mean    sd
    ##    <dbl> <dbl>
    ## 1 -0.791  4.69
    ## 
    ## $c
    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  9.92 0.183
    ## 
    ## $d
    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1 -3.11  1.04

``` r
listcol_df |> 
  mutate(mean_sd = map(samp, mean_and_sd),
         median = map(samp, median)) |>
  select(name, mean_sd) |> 
  unnest(mean_sd) # unnest a list-column into rows and cols
```

    ## # A tibble: 4 × 3
    ##   name    mean    sd
    ##   <chr>  <dbl> <dbl>
    ## 1 a      2.85  0.953
    ## 2 b     -0.791 4.69 
    ## 3 c      9.92  0.183
    ## 4 d     -3.11  1.04

# revisit nsduh

create a function for further use…

``` r
nsduh_url = "http://samhda.s3-us-gov-west-1.amazonaws.com/s3fs-public/field-uploads/2k15StateFiles/NSDUHsaeShortTermCHG2015.htm"

nsduh_html = read_html(nsduh_url)

nsduh_import = function(html, table_num) {
  
  table = 
    html |> 
    html_table() |> 
    nth(table_num) |>
    slice(-1) |> 
    select(-contains("P Value")) |>
    pivot_longer(
      -State,
      names_to = "age_year", 
      values_to = "percent") |>
    separate(age_year, into = c("age", "year"), sep = "\\(") |>
    mutate(
      year = str_replace(year, "\\)", ""),
      percent = str_replace(percent, "[a-c]$", ""),
      percent = as.numeric(percent))|>
    filter(!(State %in% c("Total U.S.", "Northeast", "Midwest", "South", "West")))

}
```

import data using `for loop`:

``` r
table_input = list(1,4,5)
name_input = list('marj', 'cocaine', 'heroin')
output = vector('list', length = 3)
# length = 3 bc we just want to import 3 of tables, which are table 1, 4, and 5
# i will do 'nsduh_import(nsduh_html, 1, 'marj')' for importing 1st table. do this to help you write for loops.
# i got everything w 3 objects, so i can use (i in 1:3) instead of (i in c(1,4,5))
for (i in 1:3) {
  output[[i]] = nsduh_import(nsduh_html, table_input[[i]])
}

nsduh_df = bind_rows(output)
```

import data using `map`:

``` r
nsduh_import = function(html, table_num) {
  
  table = 
    html |> 
    html_table() |> 
    nth(table_num) |>
    slice(-1) |> 
    select(-contains("P Value")) |>
    pivot_longer(
      -State,
      names_to = "age_year", 
      values_to = "percent") |>
    separate(age_year, into = c("age", "year"), sep = "\\(") |>
    mutate(
      year = str_replace(year, "\\)", ""),
      percent = str_replace(percent, "[a-c]$", ""),
      percent = as.numeric(percent)) |>
    filter(!(State %in% c("Total U.S.", "Northeast", "Midwest", "South", "West")))

}

nsduh_url = "http://samhda.s3-us-gov-west-1.amazonaws.com/s3fs-public/field-uploads/2k15StateFiles/NSDUHsaeShortTermCHG2015.htm"

nsduh_html = read_html(nsduh_url)

nsduh_df =
  tibble(
   name = c("marj", "cocaine", "heroine"),
   number = c(1, 4, 5)
  ) |>  # keep track of tbl name and tbl number in a df
  mutate(table = map(number, nsduh_import, html = nsduh_html)) |> 
  unnest(table)

# this is what we do if we go 1 table by 1
# map(nsduh_df$number, nsduh_import, html = nsduh_html)
```

# rivist weather_df

``` r
weather_df = 
  rnoaa::meteo_pull_monitors(
    c("USW00094728", "USW00022534", "USS0023B17S"),
    var = c("PRCP", "TMIN", "TMAX"), 
    date_min = "2021-01-01",
    date_max = "2022-12-31") |>
  mutate(
    name = recode(
      id, 
      USW00094728 = "CentralPark_NY", 
      USW00022534 = "Molokai_HI",
      USS0023B17S = "Waterhole_WA"),
    tmin = tmin / 10,
    tmax = tmax / 10) |>
  select(name, id, everything())
```

    ## Registered S3 method overwritten by 'hoardr':
    ##   method           from
    ##   print.cache_info httr

    ## using cached file: /Users/peng_/Library/Caches/org.R-project.R/R/rnoaa/noaa_ghcnd/USW00094728.dly

    ## date created (size, mb): 2023-10-12 05:40:09.606797 (8.534)

    ## file min/max dates: 1869-01-01 / 2023-10-31

    ## using cached file: /Users/peng_/Library/Caches/org.R-project.R/R/rnoaa/noaa_ghcnd/USW00022534.dly

    ## date created (size, mb): 2023-10-12 05:40:14.620904 (3.839)

    ## file min/max dates: 1949-10-01 / 2023-10-31

    ## using cached file: /Users/peng_/Library/Caches/org.R-project.R/R/rnoaa/noaa_ghcnd/USS0023B17S.dly

    ## date created (size, mb): 2023-10-12 05:40:16.392605 (0.997)

    ## file min/max dates: 1999-09-01 / 2023-10-31

``` r
weather_nest_df = 
  weather_df |> 
  nest(df = date : tmin)
```

can i regress `tmax` on `tmin` for each of these?

``` r
central_park_df =
  weather_nest_df |> 
  pull(df) |> 
  nth(1)
```

fit a linear reg:

``` r
# what we do if go 1 by 1
# central_park_df =
#  weather_nest_df |> 
#  pull(df) |> 
#  nth(1)
# lm(tmax~tmin, data=central_park_df)

# now do w a function
weather_lm = function(df) {
  lm(tmax~tmin, data=df)
}

weather_lm(weather_nest_df$df[[2]])
```

    ## 
    ## Call:
    ## lm(formula = tmax ~ tmin, data = df)
    ## 
    ## Coefficients:
    ## (Intercept)         tmin  
    ##     21.7547       0.3222

try a for loop for this case and apply the function above:

``` r
input_list = weather_nest_df |> pull(df)
output_list = vector('list', length = 3)

for (i in 1:3){
  output_list[[i]] = weather_lm(input_list[[i]])
}

# now i have an output list floating around, and i can add it back into my data set
# add a col 'models' that will map across col 'df' using 'weather_lm' function
weather_nest_df |> 
  mutate(models = map(df, weather_lm))
```

    ## # A tibble: 3 × 4
    ##   name           id          df                 models
    ##   <chr>          <chr>       <list>             <list>
    ## 1 CentralPark_NY USW00094728 <tibble [730 × 4]> <lm>  
    ## 2 Molokai_HI     USW00022534 <tibble [730 × 4]> <lm>  
    ## 3 Waterhole_WA   USS0023B17S <tibble [730 × 4]> <lm>
