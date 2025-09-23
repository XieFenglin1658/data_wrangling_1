tidy_data
================

``` r
library(tidyverse)
```

    ## ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
    ## ✔ dplyr     1.1.4     ✔ readr     2.1.5
    ## ✔ forcats   1.0.0     ✔ stringr   1.5.1
    ## ✔ ggplot2   3.5.2     ✔ tibble    3.3.0
    ## ✔ lubridate 1.9.4     ✔ tidyr     1.3.1
    ## ✔ purrr     1.1.0     
    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()
    ## ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors

## `pivot_longer`

load the PULSE data

``` r
pulse_data =
  haven::read_sas("data/public_pulse_data.sas7bdat") |> 
  janitor::clean_names()
```

wide format to longer format.. \##I think: 让数据easy to manipulate

\##大家前缀都一样，则为了更方便read
data，可以将前缀去掉，使用`names_prefix =`

``` r
pulse_data_tidy =
  pulse_data |>
  pivot_longer(
    bdi_score_bl:bdi_score_12m,
    names_to = "visit",
    names_prefix = "bdi_score_",
    values_to = "bdi"
  )
```

rewrite, combine, and extend(to add a mutate)

``` r
pulse_data =
  haven::read_sas("data/public_pulse_data.sas7bdat") |> 
  janitor::clean_names() |>
  pivot_longer(
    bdi_score_bl:bdi_score_12m,
    names_to = "visit",
    names_prefix = "bdi_score_",
    values_to = "bdi"
  ) |>
  relocate(id, visit) |> 
  mutate(visit = recode(visit, "bl" = "00m"))
```

sorry,that’s pivot longer, not pivot wider! just need to change the
commit!

## `pivot_wider`

\##I think : 让数据easy to read

make up some data

``` r
analysis_result =
  tibble(
    group = c("treatment", "treatment", "placebo", "placebo"),
    time = c("pre", "post","pre", "post"),
    mean = c(4, 8, 3.5, 4)
  )

analysis_result |> 
  pivot_wider(
    names_from = "time",
    values_from = "mean"
  )
```

    ## # A tibble: 2 × 3
    ##   group       pre  post
    ##   <chr>     <dbl> <dbl>
    ## 1 treatment   4       8
    ## 2 placebo     3.5     4
