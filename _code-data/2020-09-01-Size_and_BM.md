Size and BM
================
Diego Bonelli
2020-09-01

<style type="text/css">

body, td {
   font-size: 14px;
}
code.r{
  font-size: 14px;
}
pre {
  font-size: 14px
}
</style>

In this post I will quickly explain how to create 25 Size and
Book-to-Market (BM) sorted portfolios as in Fama and French (1993).
Firsly, I will remove existing variables from the environment and upload
some useful packages.

``` r
# Clean Environment
rm(list = ls(all.names = TRUE))

# Load Libraries
library(tidyverse) # Tidyverse universe (dplyr, ggplot and )
library(zoo) # Useful package for dates
library(data.table) # Faster way to use dataframes
library(lubridate) # Package for dates
library(matrixStats) # Simple package to do matrix statistics
library(RPostgres) # Package to download data direcly from WRDS
library(kableExtra) # Package to print tables
```

# Download Data

To create the 25 Size-BM portfolios we need an access to the “Wharton
Research Data Service” or WRDS, specifically to the Compustat
Fundamental Dataset and the CRSP Dataset. The next chucks of code will
explain how to automate the download of CRSP and Compustat datasets. In
case you have already downloaded the two mentioned datasets you can skip
the next 4 chucks of code and just upload your datasets. I strongly
suggest you to download the complete datasets once and then save them in
an .RData extention to save memory and time, in case of multiple uses.

Here we will establish a connection between the R script and WRDS, which
will allow us to download directly from the script.\\

``` r
wrds <- dbConnect(Postgres(),
                  host='wrds-pgdata.wharton.upenn.edu',
                  port=9737,
                  user='usr', # insert your username
                  password='psw', # insert your password
                  dbname='wrds',
                  sslmode='require')
```
