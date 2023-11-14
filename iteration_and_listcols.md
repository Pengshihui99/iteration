iteration_and_listcols
================
Shihui Peng
2023-11-14

``` r
library(tidyverse)
```

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

``` r
library(rvest)
```

    ## 
    ## Attaching package: 'rvest'
    ## 
    ## The following object is masked from 'package:readr':
    ## 
    ##     guess_encoding

``` r
set.seed(12345)
```

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
