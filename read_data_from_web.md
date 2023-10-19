Read Data From the Web
================
ASHLEY ROMO
2023-10-19

Load the necessary libraries.

``` r
library(rvest) 
```

    ## 
    ## Attaching package: 'rvest'

    ## The following object is masked from 'package:readr':
    ## 
    ##     guess_encoding

``` r
library(httr) #package to submit http request to get data from api
```

Import NSDUH data

``` r
# first pase the web address where the data exists
nsduh_url = "http://samhda.s3-us-gov-west-1.amazonaws.com/s3fs-public/field-uploads/2k15StateFiles/NSDUHsaeShortTermCHG2015.htm"

#import the data set be using read_html 
nsduh_html = 
  read_html(nsduh_url)
```

``` r
#pull out elements that you need by pull the html table
marj_use_df = 
  nsduh_html |> 
  html_table() |> 
  first() |> #first() takes the first element in a list of things
  slice(-1) # removes the -1 row (aka the first row)
```

Import star wars …

``` r
swm_url = 
  "https://www.imdb.com/list/ls070150896/"

swm_html = 
  read_html(swm_url)
```

``` r
#get the data you need from the html 
swm_title_vec = 
  swm_html |> 
  html_elements(".lister-item-header a") |> #use the gadet tool to get the css code for what you want
  html_text() # extracts the text from html that goes to the css tag you want

swm_gross_rev_vec = 
  swm_html |> 
  html_elements(".text-small:nth-child(7) span:nth-child(5)") |> 
  html_text()
 

swm_df = 
  tibble(
    title = swm_title_vec,
    gross_rev = swm_gross_rev_vec
  )
```

## APIs

Get water data from NYC.

``` r
nyc_water_df = 
  GET("https://data.cityofnewyork.us/resource/ia2d-e54m.csv") |> # use the GET() function to get data from API
  content()
```

    ## Rows: 44 Columns: 4
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## dbl (4): year, new_york_city_population, nyc_consumption_million_gallons_per...
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

BRFSS Data

``` r
brfss_df = 
  GET(
    "https://data.cdc.gov/resource/acme-vg9e.csv",
    query = list("$limit" = 5000)) |> 
  content()
```

    ## Rows: 5000 Columns: 23
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr (16): locationabbr, locationdesc, class, topic, question, response, data...
    ## dbl  (6): year, sample_size, data_value, confidence_limit_low, confidence_li...
    ## lgl  (1): locationid
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

Try it now!

``` r
poke_df = 
  GET("https://pokeapi.co/api/v2/pokemon/ditto") |> 
  content()
```
