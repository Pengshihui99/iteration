writing functions
================
Shihui Peng
2023-11-14

set seed for reproducibility

``` r
set.seed(12345)
```

- we will generate random number today, this code help us get exactly
  the same random number every single time. this code tell it where to
  start.
  - to make sure random number generation starts at the same point and
    give us the same value.

# Z score function

z score subtract the mean and divide by the sd. generate a vec of random
samples:

``` r
x_vec = rnorm(20, mean = 5, sd = .3)
```

- take a sample from a normal distribution. do 20 of this and set mean
  as 5 and sd as 0.3

compute z score for x_vec

``` r
(x_vec - mean(x_vec)) / sd(x_vec)
```

    ##  [1]  0.6103734  0.7589907 -0.2228232 -0.6355576  0.6347861 -2.2717259
    ##  [7]  0.6638185 -0.4229355 -0.4324994 -1.1941438 -0.2311505  2.0874460
    ## [13]  0.3526784  0.5320552 -0.9917420  0.8878182 -1.1546150 -0.4893597
    ## [19]  1.2521303  0.2664557

suppose i have many vectors and want to quickly compute z scores for
them. \* write a function to do this!

``` r
z_score = function(x) {
# check these
    if (!is.numeric(x)) {
    stop("Argument x should be numeric")
  } else if (length(x) < 2) {
    stop("you need at least 2 numbers to get z scores")
  }

# if neither of above are true, then go as below:  
  z = (x - mean(x) )/ sd(x)
  z
}
```

- If I want to repeat this process for lots of samples, I need a
  function that takes the sample as an argument, computes the vector of
  Z scores in the body, and returns the result. I define such a function
  below.
  - name what that input is – call it `x` inside `function()` (generic
    vector).
  - use that to produce z – `z = (x - mean(x) / sd(x))`
  - return z – `z`

check if this works:

``` r
z_score(x = x_vec)
```

    ##  [1]  0.6103734  0.7589907 -0.2228232 -0.6355576  0.6347861 -2.2717259
    ##  [7]  0.6638185 -0.4229355 -0.4324994 -1.1941438 -0.2311505  2.0874460
    ## [13]  0.3526784  0.5320552 -0.9917420  0.8878182 -1.1546150 -0.4893597
    ## [19]  1.2521303  0.2664557

``` r
z_score(x = rnorm(10, mean = 5))
```

    ##  [1]  0.5952213  1.1732833 -0.6221352 -1.3990896 -1.4371950  1.4719158
    ##  [7] -0.4830567  0.4590828  0.4520244 -0.2100512

``` r
# return NA, bc no sd when only 1 number (w/o the 'if' part in our function)
z_score(x = 3)
```

    ## Error in z_score(x = 3): you need at least 2 numbers to get z scores

``` r
# return error bc no sd or mean for character variables (w/o the 'if' part in our function)
z_score(c('my', 'name', 'is', 'sheryl'))
```

    ## Error in z_score(c("my", "name", "is", "sheryl")): Argument x should be numeric

``` r
# return 0.5  0.5 -1.5  0.5 bc r converts these to 1 and 0 (w/o the 'if' part in our function)
z_score(c(TRUE, TRUE, FALSE, TRUE))
```

    ## Error in z_score(c(TRUE, TRUE, FALSE, TRUE)): Argument x should be numeric

``` r
# similar as above (w/o the 'if' part in our function)
z_score(sample(c(TRUE, FALSE), 25, replace = TRUE))
```

    ## Error in z_score(sample(c(TRUE, FALSE), 25, replace = TRUE)): Argument x should be numeric

``` r
# return error bc cannot get mean and sd for the entire data frame (w/o the 'if' part in our function)
z_score(iris)
```

    ## Error in z_score(iris): Argument x should be numeric

# multiple outputs

write a function returning the mean and sd from a sample of numbers

``` r
mean_and_sd = function(x) {

      if (!is.numeric(x)) {
    stop("Argument x should be numeric")
  } else if (length(x) < 2) {
    stop("you need at least 2 numbers to get z scores")
  }
  
  mean_x = mean(x)
  sd_x = sd(x)
  # if stop here, r will only return the last thing that happens
  
  # 1 of options here is to return a data frame
  tibble(
    mean = mean_x,
    sd = sd_x
  )
}
```

- we can also make a list: `list(mean = mean_x, sd = sd_x)`
- which to choose:
  - to return the original sample along with the computed values – a
    list might make sense.
  - to run your function a lot and study the results – having a data
    frame is easier to use other tools.

double check if i did right:

``` r
mean_and_sd(x_vec)
```

    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  5.02 0.250

# multiple input

``` r
x_vec = rnorm(n = 30, mean = 5, sd = 0.5)

tibble(
  mean = mean(x_vec),
  sd = sd(x_vec)
)
```

    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  5.10 0.354

what if i want to try this w diff smaple sizes, means, and sds? \* let’s
write a function using `n`, a true mean, and a true sd as inputs.

``` r
sim_mean_sd = function(n_obs, mu, sigma) {
  
  x_vec = rnorm(n = n_obs, mean = mu, sd = sigma)

  tibble(
   mean = mean(x_vec),
   sd = sd(x_vec)
  )
}

# in this case, position does not matter
sim_mean_sd(n_obs = 3000, mu = 5, sigma = 12.3)
```

    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  4.99  12.2

``` r
# here r is doing position matching, by assuming 3000 is for n_obs, 5 is for mu, and 12.3 is for sigma
sim_mean_sd(3000, 5, 12.3)
```

    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  4.92  12.3

# revisit past examples

## LoTR data

In tidy data, we broke the “only copy code twice” rule when we used the
code below:

``` r
fellowship_ring = readxl::read_excel("data/LotR_Words.xlsx", range = "B3:D6") |>
  mutate(movie = "fellowship_ring")

two_towers = readxl::read_excel("data/LotR_Words.xlsx", range = "F3:H6") |>
  mutate(movie = "two_towers")

return_king = readxl::read_excel("data/LotR_Words.xlsx", range = "J3:L6") |>
  mutate(movie = "return_king")

lotr_tidy = bind_rows(fellowship_ring, two_towers, return_king) |>
  janitor::clean_names() |>
  pivot_longer(
    female:male,
    names_to = "sex",
    values_to = "words") |> 
  mutate(race = str_to_lower(race)) |> 
  select(movie, everything()) 
```

now we learned function…

``` r
lotr_load_and_tidy = function(path ="data/LotR_Words.xlsx", cell_range, movie_name) {
  movie_df = 
    readxl::read_excel(path, range = cell_range) |> 
    janitor::clean_names() |>
    pivot_longer(
      female:male,
      names_to = "sex",
      values_to = "words") |>
    mutate(
      race = str_to_lower(race),
      movie = movie_name) |> 
    select(movie, everything())
  
  movie_df
}

lotr_tidy = bind_rows(
  lotr_load_and_tidy(cell_range = 'B3:D6', movie_name = 'fellowship_ring'),
  lotr_load_and_tidy(cell_range = 'F3:H6', movie_name = 'two_towers'),
  lotr_load_and_tidy(cell_range = 'J3:L6', movie_name = 'return_king')
)
```

## nsduh data

previous way…

``` r
# here is a general steps for importing all tables
nsduh_url = "http://samhda.s3-us-gov-west-1.amazonaws.com/s3fs-public/field-uploads/2k15StateFiles/NSDUHsaeShortTermCHG2015.htm"

nsduh_html = read_html(nsduh_url)

# here is our previous steps to tidy data
data_marj = 
  nsduh_html |> 
  html_table() |> 
  nth(1) |>
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

# then copy&paste for the rest tables, only change the name and nth()
```

try to write a function…

``` r
nsduh_import = function(html, table_number, outcome_name){
  
  html |> 
  html_table() |> 
  nth(table_number) |>
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
    percent = as.numeric(percent),
    # here we add this
    outcome = outcome_name) |>
  filter(!(State %in% c("Total U.S.", "Northeast", "Midwest", "South", "West")))
}

nsduh_import(html = nsduh_html, table_number = 1, outcome_name = 'marj')
```

    ## # A tibble: 510 × 5
    ##    State   age   year      percent outcome
    ##    <chr>   <chr> <chr>       <dbl> <chr>  
    ##  1 Alabama 12+   2013-2014    9.98 marj   
    ##  2 Alabama 12+   2014-2015    9.6  marj   
    ##  3 Alabama 12-17 2013-2014    9.9  marj   
    ##  4 Alabama 12-17 2014-2015    9.71 marj   
    ##  5 Alabama 18-25 2013-2014   27.0  marj   
    ##  6 Alabama 18-25 2014-2015   26.1  marj   
    ##  7 Alabama 26+   2013-2014    7.1  marj   
    ##  8 Alabama 26+   2014-2015    6.81 marj   
    ##  9 Alabama 18+   2013-2014    9.99 marj   
    ## 10 Alabama 18+   2014-2015    9.59 marj   
    ## # ℹ 500 more rows

``` r
nsduh_import(html = nsduh_html, table_number = 4, outcome_name = 'cocaine')
```

    ## # A tibble: 510 × 5
    ##    State   age   year      percent outcome
    ##    <chr>   <chr> <chr>       <dbl> <chr>  
    ##  1 Alabama 12+   2013-2014    1.23 cocaine
    ##  2 Alabama 12+   2014-2015    1.22 cocaine
    ##  3 Alabama 12-17 2013-2014    0.42 cocaine
    ##  4 Alabama 12-17 2014-2015    0.41 cocaine
    ##  5 Alabama 18-25 2013-2014    3.09 cocaine
    ##  6 Alabama 18-25 2014-2015    3.2  cocaine
    ##  7 Alabama 26+   2013-2014    1.01 cocaine
    ##  8 Alabama 26+   2014-2015    0.99 cocaine
    ##  9 Alabama 18+   2013-2014    1.31 cocaine
    ## 10 Alabama 18+   2014-2015    1.31 cocaine
    ## # ℹ 500 more rows

``` r
nsduh_results = 
  bind_rows(
    nsduh_import(nsduh_html, 1, "marj_one_year"),
    nsduh_import(nsduh_html, 4, "cocaine_one_year")
  )
```

# functions as arguments

``` r
x_vec = rnorm(25, 0, 1)

my_summary = function(x, summ_func) {
  summ_func(x)
}

my_summary(x_vec, sd)
```

    ## [1] 1.20173

``` r
my_summary(x_vec, IQR)
```

    ## [1] 1.550333

``` r
my_summary(x_vec, var)
```

    ## [1] 1.444154

# scoping and names

``` r
f = function(x) {
  z = x + y
  z
}

x = 1
y = 2

f(x = y)
```

    ## [1] 4

Examples like this are tricky, but emphasize an issue that comes up a
lot in writing functions: \* you define a variable in your global
environment and use it in your function, but it isn’t passed as an
argument. \* This is easy to miss, especially when you go from code
written in chunks to a function, and can be hard to track down if you
empty your working directory or change a variable name. \* The best
advice I have is to give your arguments useful names and think carefully
about where everything is defined, and to periodically restart R and try
everything again!
