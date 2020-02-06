README
================
John Little
2020-02-06

<!-- README.md is generated from README.Rmd. Please edit that file -->

# reprex code

<!-- badges: start -->

<!-- badges: end -->

## Question

How to calculate variables from to differet tibbles and sum the results

## Answer

1.  TRANSFORM: pivot the data from wide to long
2.  TRANSFORM: join the the two tables by the variable ID
3.  ANALYSIS: calculate the score
4.  ANALYSIS: sum the score

## Details

The following is a [Reprex](https://reprex.tidyverse.org/), i.e. a
minimally reproducible example.

## Load Library Package

``` r
library(tidyverse)
```

## Generate data from example procedure

    tl_snp$x1 <- tl_snp$rs10808746 * eff$rs10808746
    tl_snp$x2 <- tl_snp$rs11556505 * eff$rs11556505
    tl_snp$x3 <- tl_snp$rs2075650 *  eff$rs2075650
    tl_snp$x4 <- tl_snp$rs2143571 *  eff$rs2143571
    tl_snp$x5 <- tl_snp$rs2283228 *  eff$rs2283228
    tl_snp$x6 <- tl_snp$rs2896019 *  eff$rs2896019
    tl_snp$x7 <- tl_snp$rs429358  *  eff$rs429358
    tl_snp$x8 <- tl_snp$rs4402960 *  eff$rs4402960
    tl_snp$x9 <- tl_snp$rs6006611 *  eff$rs6006611
    tl_snp$x10 <- tl_snp$rs738409 *  eff$rs738409
    tl_snp$x11 <- tl_snp$rs7412 *    eff$rs7412
    tl_snp$x12 <- tl_snp$rs7901695 * eff$rs7901695
    tl_snp$x13 <- tl_snp$rs8181588 * eff$rs8181588
    tl_snp$x14 <- tl_snp$rs9331888 * eff$rs9331888
    tl_snp$x15 <- tl_snp$rs9331942 * eff$rs9331942
    tl_snp$x16 <- tl_snp$rs985421 *  eff$rs985421
    tl_snp$x17 <- tl_snp$rs9877502 * eff$rs9877502

## Build Reprex data

First, build toy tall table with random data and specific variable
names, so that I can replicate the wide table from the original email.

``` r

var_name <- c("rs10808746", "rs11556505", "rs2075650",
              "rs2143571", "rs2283228", "rs2896019",
              "rs429358", "rs4402960", "rs6006611",
              "rs738409", "rs7412", "rs7901695",
              "rs8181588", "rs9331888", "rs9331942",
              "rs985421", "rs9877502")

var_val_1 <- seq(length.out = 17, by = 18.6)
var_val_2 <- seq(length.out = 17, by = 33.1)

tbl_snp <- tibble(var_name, var_val_1)
tbl_eff <- tibble(var_name, var_val_2)

tbl_snp_wide <- tbl_snp %>% 
  pivot_wider(names_from = var_name,
              values_from = var_val_1)

tbl_eff_wide <- tbl_eff %>% 
  pivot_wider(names_from = var_name,
              values_from = var_val_2)
```

Example data similar to that discussed in the email

``` r
tbl_snp_wide
#> # A tibble: 1 x 17
#>   rs10808746 rs11556505 rs2075650 rs2143571 rs2283228 rs2896019 rs429358
#>        <dbl>      <dbl>     <dbl>     <dbl>     <dbl>     <dbl>    <dbl>
#> 1          1       19.6      38.2      56.8      75.4        94     113.
#> # ... with 10 more variables: rs4402960 <dbl>, rs6006611 <dbl>, rs738409 <dbl>,
#> #   rs7412 <dbl>, rs7901695 <dbl>, rs8181588 <dbl>, rs9331888 <dbl>,
#> #   rs9331942 <dbl>, rs985421 <dbl>, rs9877502 <dbl>
tbl_eff_wide
#> # A tibble: 1 x 17
#>   rs10808746 rs11556505 rs2075650 rs2143571 rs2283228 rs2896019 rs429358
#>        <dbl>      <dbl>     <dbl>     <dbl>     <dbl>     <dbl>    <dbl>
#> 1          1       34.1      67.2      100.      133.      166.     200.
#> # ... with 10 more variables: rs4402960 <dbl>, rs6006611 <dbl>, rs738409 <dbl>,
#> #   rs7412 <dbl>, rs7901695 <dbl>, rs8181588 <dbl>, rs9331888 <dbl>,
#> #   rs9331942 <dbl>, rs985421 <dbl>, rs9877502 <dbl>
```

### Pivot longer

[“Tall” data](https://en.wikipedia.org/wiki/Wide_and_narrow_data) (a.k.a
“long”, a.k.a “narrow”) is often easier or more convenient for
calculations.

``` r
tbl_snp_tall <- tbl_snp_wide %>% 
  pivot_longer(starts_with("rs"), 
               names_to = "snp_var_name", 
               values_to = "snp_value")
tbl_eff_tall <- tbl_eff_wide %>% 
  pivot_longer(starts_with("rs"), 
               names_to = "eff_var_name",
               values_to = "eff_value")

tbl_snp_tall
#> # A tibble: 17 x 2
#>    snp_var_name snp_value
#>    <chr>            <dbl>
#>  1 rs10808746         1  
#>  2 rs11556505        19.6
#>  3 rs2075650         38.2
#>  4 rs2143571         56.8
#>  5 rs2283228         75.4
#>  6 rs2896019         94  
#>  7 rs429358         113. 
#>  8 rs4402960        131. 
#>  9 rs6006611        150. 
#> 10 rs738409         168. 
#> 11 rs7412           187  
#> 12 rs7901695        206. 
#> 13 rs8181588        224. 
#> 14 rs9331888        243. 
#> 15 rs9331942        261. 
#> 16 rs985421         280  
#> 17 rs9877502        299.
tbl_eff_tall
#> # A tibble: 17 x 2
#>    eff_var_name eff_value
#>    <chr>            <dbl>
#>  1 rs10808746         1  
#>  2 rs11556505        34.1
#>  3 rs2075650         67.2
#>  4 rs2143571        100. 
#>  5 rs2283228        133. 
#>  6 rs2896019        166. 
#>  7 rs429358         200. 
#>  8 rs4402960        233. 
#>  9 rs6006611        266. 
#> 10 rs738409         299. 
#> 11 rs7412           332  
#> 12 rs7901695        365. 
#> 13 rs8181588        398. 
#> 14 rs9331888        431. 
#> 15 rs9331942        464. 
#> 16 rs985421         498. 
#> 17 rs9877502        531.
```

## Combine tibbles

Combine the column values from both tables for later manipulation

### Easy

Using the `bind_cols()` function. This is easy to do but each table must
be the same size and the data must be in the same order.

``` r
big_tbl <- bind_cols(tbl_snp_tall, tbl_eff_tall)
big_tbl
#> # A tibble: 17 x 4
#>    snp_var_name snp_value eff_var_name eff_value
#>    <chr>            <dbl> <chr>            <dbl>
#>  1 rs10808746         1   rs10808746         1  
#>  2 rs11556505        19.6 rs11556505        34.1
#>  3 rs2075650         38.2 rs2075650         67.2
#>  4 rs2143571         56.8 rs2143571        100. 
#>  5 rs2283228         75.4 rs2283228        133. 
#>  6 rs2896019         94   rs2896019        166. 
#>  7 rs429358         113.  rs429358         200. 
#>  8 rs4402960        131.  rs4402960        233. 
#>  9 rs6006611        150.  rs6006611        266. 
#> 10 rs738409         168.  rs738409         299. 
#> 11 rs7412           187   rs7412           332  
#> 12 rs7901695        206.  rs7901695        365. 
#> 13 rs8181588        224.  rs8181588        398. 
#> 14 rs9331888        243.  rs9331888        431. 
#> 15 rs9331942        261.  rs9331942        464. 
#> 16 rs985421         280   rs985421         498. 
#> 17 rs9877502        299.  rs9877502        531.
```

### More precise via left\_join

Using a join function allows matching by data identifiers, before
merging variable values.

There are different types of `join` functions (see
[dplyr](https://dplyr.tidyverse.org/reference/join.html). In this case
we want a `left_join()` – most people want to do a left\_join, but it
makes sense to verify which type of join you need.

``` r
join_tbl <- left_join(tbl_snp_tall, tbl_eff_tall, by = c("snp_var_name" = "eff_var_name"))

join_tbl
#> # A tibble: 17 x 3
#>    snp_var_name snp_value eff_value
#>    <chr>            <dbl>     <dbl>
#>  1 rs10808746         1         1  
#>  2 rs11556505        19.6      34.1
#>  3 rs2075650         38.2      67.2
#>  4 rs2143571         56.8     100. 
#>  5 rs2283228         75.4     133. 
#>  6 rs2896019         94       166. 
#>  7 rs429358         113.      200. 
#>  8 rs4402960        131.      233. 
#>  9 rs6006611        150.      266. 
#> 10 rs738409         168.      299. 
#> 11 rs7412           187       332  
#> 12 rs7901695        206.      365. 
#> 13 rs8181588        224.      398. 
#> 14 rs9331888        243.      431. 
#> 15 rs9331942        261.      464. 
#> 16 rs985421         280       498. 
#> 17 rs9877502        299.      531.
```

## Calculate new variable

score = snp\_value \* eff\_value

``` r
join_tbl <- join_tbl %>% 
  mutate(score = snp_value * eff_value)
join_tbl
#> # A tibble: 17 x 4
#>    snp_var_name snp_value eff_value   score
#>    <chr>            <dbl>     <dbl>   <dbl>
#>  1 rs10808746         1         1        1 
#>  2 rs11556505        19.6      34.1    668.
#>  3 rs2075650         38.2      67.2   2567.
#>  4 rs2143571         56.8     100.    5697.
#>  5 rs2283228         75.4     133.   10058.
#>  6 rs2896019         94       166.   15651 
#>  7 rs429358         113.      200.   22475.
#>  8 rs4402960        131.      233.   30530.
#>  9 rs6006611        150.      266.   39817.
#> 10 rs738409         168.      299.   50335.
#> 11 rs7412           187       332    62084 
#> 12 rs7901695        206.      365.   75065.
#> 13 rs8181588        224.      398.   89276.
#> 14 rs9331888        243.      431.  104720.
#> 15 rs9331942        261.      464.  121394.
#> 16 rs985421         280       498.  139300 
#> 17 rs9877502        299.      531.  158437.
```

## Column totals

Row totals

``` r
join_tbl %>% 
  summarize(total_score = sum(score))
#> # A tibble: 1 x 1
#>   total_score
#>         <dbl>
#> 1     928076.
```

    joined_tbl %>% 
      mutate(score = snp_value * eff_value)%>% 
      summarize(total_score = sum(score))
