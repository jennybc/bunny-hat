
how jenny might do this in a first exploration purposely leaving a few things to change later!

Which libraries does R search for packages?

``` r
.libPaths()
```

    ## [1] "/Users/jenny/resources/R/library"                              
    ## [2] "/Library/Frameworks/R.framework/Versions/3.4/Resources/library"

let's confirm the second element is, in fact, the default library

``` r
.Library
```

    ## [1] "/Library/Frameworks/R.framework/Resources/library"

``` r
library(fs)
path_real(.Library)
```

    ## /Library/Frameworks/R.framework/Versions/3.4/Resources/library

Installed packages

``` r
library(tidyverse)
```

    ## ── Attaching packages ───────────────────────────────────────── tidyverse 1.2.1 ──

    ## ✔ ggplot2 2.2.1          ✔ purrr   0.2.4     
    ## ✔ tibble  1.4.2          ✔ dplyr   0.7.4     
    ## ✔ tidyr   0.7.2          ✔ stringr 1.2.0.9000
    ## ✔ readr   1.1.1.9000     ✔ forcats 0.2.0

    ## ── Conflicts ──────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()

``` r
ipt <- installed.packages() %>%
  as_tibble()
```

how many packages?

``` r
nrow(ipt)
```

    ## [1] 842

Exploring the packages

count some things! inspiration \* tabulate by LibPath, Priority, or both

``` r
ipt %>%
  count(LibPath, Priority)
```

    ## # A tibble: 4 x 3
    ##   LibPath                                                 Priority       n
    ##   <chr>                                                   <chr>      <int>
    ## 1 /Library/Frameworks/R.framework/Versions/3.4/Resources… base          14
    ## 2 /Library/Frameworks/R.framework/Versions/3.4/Resources… recommend…    15
    ## 3 /Users/jenny/resources/R/library                        optional       1
    ## 4 /Users/jenny/resources/R/library                        <NA>         812

-   what proportion need compilation?

``` r
ipt %>%
  count(NeedsCompilation) %>%
  mutate(prop = n / sum(n))
```

    ## # A tibble: 3 x 3
    ##   NeedsCompilation     n  prop
    ##   <chr>            <int> <dbl>
    ## 1 no                 422 0.501
    ## 2 yes                331 0.393
    ## 3 <NA>                89 0.106

-   how break down re: version of R they were built on

``` r
ipt %>%
  count(Built) %>%
  mutate(prop = n / sum(n))
```

    ## # A tibble: 4 x 3
    ##   Built     n   prop
    ##   <chr> <int>  <dbl>
    ## 1 3.4.0   597 0.709 
    ## 2 3.4.1   142 0.169 
    ## 3 3.4.2    90 0.107 
    ## 4 3.4.3    13 0.0154

Reflections

reflect on ^^ and make a few notes to yourself; inspiration \* does the number of base + recommended packages make sense to you? \* how does the result of .libPaths() relate to the result of .Library?

Going further

if you have time to do more ...

is every package in .Library either base or recommended?

``` r
all_default_pkgs <- list.files(.Library)
all_br_pkgs <- ipt %>%
  filter(Priority %in% c("base", "recommended")) %>%
  pull(Package)
setdiff(all_default_pkgs, all_br_pkgs)
```

    ## [1] "translations"

study package naming style (all lower case, contains '.', etc

use `fields` argument to installed.packages() to get more info and use it!

``` r
ipt2 <- installed.packages(fields = "URL") %>%
  as_tibble()
ipt2 %>%
  mutate(github = grepl("github", URL)) %>%
  count(github) %>%
  mutate(prop = n / sum(n))
```

    ## # A tibble: 2 x 3
    ##   github     n  prop
    ##   <lgl>  <int> <dbl>
    ## 1 F        517 0.614
    ## 2 T        325 0.386
