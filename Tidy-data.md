Tidy data
================

``` r
library(tidyverse)
```

    ## ── Attaching packages ─────────────────────────────────────── tidyverse 1.3.2 ──
    ## ✔ ggplot2 3.3.6      ✔ purrr   0.3.4 
    ## ✔ tibble  3.1.7      ✔ dplyr   1.0.10
    ## ✔ tidyr   1.2.0      ✔ stringr 1.4.0 
    ## ✔ readr   2.1.2      ✔ forcats 0.5.2 
    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()

## `pivot_longer`

Load the PULSE data

``` r
pulse_data = 
  haven::read_sas("./data_import_examples/public_pulse_data.sas7bdat") %>% 
  janitor::clean_names()
```

Wide format to long format

``` r
pulse_data_tidy = 
  pulse_data %>% 
  pivot_longer(
    bdi_score_bl:bdi_score_12m, 
    names_to = "visit",
    names_prefix = "bdi_score_",
    values_to = "bdi"
  )
```

rewrite, combine and extend (to add a mutate)

``` r
pulse_data = 
  haven::read_sas("./data_import_examples/public_pulse_data.sas7bdat") %>% 
  janitor::clean_names() %>% 
  pivot_longer(
    bdi_score_bl:bdi_score_12m, 
    names_to = "visit",
    names_prefix = "bdi_score_",
    values_to = "bdi"
  ) %>% 
  relocate(id,visit) %>% 
  mutate(visit = recode(visit,"bl" = "00m"))
```

## `pivot_wider`

Wake up some data!

``` r
analysis_result = 
  tibble(
    group = c("treatment","treatment","placebo","placebo"),
    time = c("pre","post","pre","post"),
    mean = c(4,8,3.5,4)
  )

analysis_result %>% 
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

## Binding rows

Using the LotR data.

First step: import each table.

``` r
fellowship_ring =
  readxl::read_excel("./data_import_examples/LotR_Words.xlsx", range = "B3:D6") %>% 
  mutate(movie = "fellowship_ring")

Two_towers =
  readxl::read_excel("./data_import_examples/LotR_Words.xlsx", range = "F3:H6") %>% 
  mutate(movie = "Two_towers")

return_king =
  readxl::read_excel("./data_import_examples/LotR_Words.xlsx", range = "J3:L6") %>% 
  mutate(movie = "return_king")
```

Bind all the rows together

``` r
lotr_tidy = 
  bind_rows(fellowship_ring, Two_towers, return_king) %>% 
  janitor::clean_names() %>% 
  relocate(movie) %>% 
  pivot_longer(
    female:male,
    names_to = "gender",
    values_to = "words"
  )
```
