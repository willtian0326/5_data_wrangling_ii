Reading Data from the Web
================
Wenxin Tian
2023-10-12

Website = HTML + CSS

HTML controls content, and CSS controls appearance. So extracting
information comes down to choosing the unique selector tag.

Workflow:

- Download HTML using `read_html()`
- Extract elements using `html_elements()` and CSS selector
- Extract data with `html_extract()` and `html_table()`

Load libraries:

``` r
library(tidyverse)
```

    ## ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
    ## ✔ dplyr     1.1.3     ✔ readr     2.1.4
    ## ✔ forcats   1.0.0     ✔ stringr   1.5.0
    ## ✔ ggplot2   3.4.3     ✔ tibble    3.2.1
    ## ✔ lubridate 1.9.2     ✔ tidyr     1.3.0
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
library(httr)
```

## Read Tables from HTML:

``` r
url = "http://samhda.s3-us-gov-west-1.amazonaws.com/s3fs-public/field-uploads/2k15StateFiles/NSDUHsaeShortTermCHG2015.htm"

nsduh_html = read_html(url)

# Imports every table stored as a list:

nsduh_html |>
  html_table()

# Use specific ones:

marj_use_df =
  nsduh_html |>
  html_table() |>
  first() |>
  slice(-1)  # removes first row

marj_use_df
```

## Selector Gadget:

Import Star Wars data, which are not tables:

``` r
starwars_url = "https://www.imdb.com/list/ls070150896/"

starwars_html = read_html(starwars_url)
```

Use Selector Gadget to select css tags on the website and paste it
below:

``` r
starwars_html |>
  html_elements(".lister-item-header a")
```

    ## {xml_nodeset (9)}
    ## [1] <a href="/title/tt0120915/?ref_=ttls_li_tt">Star Wars: Episode I - The Ph ...
    ## [2] <a href="/title/tt0121765/?ref_=ttls_li_tt">Star Wars: Episode II - Attac ...
    ## [3] <a href="/title/tt0121766/?ref_=ttls_li_tt">Star Wars: Episode III - Reve ...
    ## [4] <a href="/title/tt0076759/?ref_=ttls_li_tt">Star Wars: Episode IV - A New ...
    ## [5] <a href="/title/tt0080684/?ref_=ttls_li_tt">Star Wars: Episode V - The Em ...
    ## [6] <a href="/title/tt0086190/?ref_=ttls_li_tt">Star Wars: Episode VI - Retur ...
    ## [7] <a href="/title/tt2488496/?ref_=ttls_li_tt">Star Wars: Episode VII - The  ...
    ## [8] <a href="/title/tt2527336/?ref_=ttls_li_tt">Star Wars: Episode VIII - The ...
    ## [9] <a href="/title/tt2527338/?ref_=ttls_li_tt">Star Wars: The Rise Of Skywal ...

``` r
starwars_html |>
  html_elements(".lister-item-header a") |>
  html_text()
```

    ## [1] "Star Wars: Episode I - The Phantom Menace"     
    ## [2] "Star Wars: Episode II - Attack of the Clones"  
    ## [3] "Star Wars: Episode III - Revenge of the Sith"  
    ## [4] "Star Wars: Episode IV - A New Hope"            
    ## [5] "Star Wars: Episode V - The Empire Strikes Back"
    ## [6] "Star Wars: Episode VI - Return of the Jedi"    
    ## [7] "Star Wars: Episode VII - The Force Awakens"    
    ## [8] "Star Wars: Episode VIII - The Last Jedi"       
    ## [9] "Star Wars: The Rise Of Skywalker"

``` r
starwars_html |>
  html_elements(".text-small:nth-child(7) span:nth-child(5)") |>
  html_text()
```

    ## [1] "$474.54M" "$310.68M" "$380.26M" "$322.74M" "$290.48M" "$309.13M" "$936.66M"
    ## [8] "$620.18M" "$515.20M"

Make a df:

``` r
startwars_df =
  tibble(
    title = 
      starwars_html |>
      html_elements(".lister-item-header a") |>
      html_text(),
    gross_rev = 
      starwars_html |>
      html_elements(".text-small:nth-child(7) span:nth-child(5)") |>
      html_text()
  )
```

## APIs:

``` r
nyc_water = 
  GET("https://data.cityofnewyork.us/resource/ia2d-e54m.csv") |> 
  content()
```

    ## Rows: 44 Columns: 4
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## dbl (4): year, new_york_city_population, nyc_consumption_million_gallons_per...
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
