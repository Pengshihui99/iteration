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
