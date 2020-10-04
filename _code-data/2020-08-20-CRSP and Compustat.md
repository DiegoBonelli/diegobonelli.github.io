---
title: "CRSP and Compustat"
author: "Diego Bonelli"
date: 2020-08-20
collection: code-data
author_profile: true
permalink: /code-data/CCM/
toc: true
related: false
classes: wide
excerpt: "Learn how to create 25 size and book to market portfolios"
tags:
  - Finance
  - Portfolios
  - R
---

In this post I will quickly explain how to create 25 Size and Book-to-Market (BM) sorted portfolios as in Fama and French (1993).
Firsly, I will remove existing variables from the environment and upload some useful packages.

```r
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

To create the 25 Size-BM portfolios we need an access to the "Wharton Research Data Service" or WRDS, specifically to the Compustat Fundamental Dataset and the CRSP Dataset. The next chucks of code will explain how to automate the download of CRSP and Compustat datasets. In case you have already downloaded the two mentioned datasets you can skip the next 4 chucks of code and just upload your datasets. I strongly suggest you to download the complete datasets once and then save them in an .RData extention to save memory and time, in case of multiple uses.


Here we will establish a connection between the R script and WRDS, which will allow us to download directly from the script.\\


```r
wrds <- dbConnect(Postgres(),
                  host='wrds-pgdata.wharton.upenn.edu',
                  port=9737,
                  user='username', # insert your username
                  password='password', # insert your password
                  dbname='wrds',
                  sslmode='require')
```



Now let's try to understand. There are:

Libraries
Dataset
Columns
See here and here for further information


```r
res <- dbSendQuery(wrds, "select distinct table_schema
                   from information_schema.tables
                   where table_type ='VIEW'
                   or table_type ='FOREIGN TABLE'
                   order by table_schema")
data <- dbFetch(res, n=-1)
dbClearResult(res)

# Printing example
data %>%
  kbl(format="html") %>%  
  kable_styling(bootstrap_options = c("striped", "hover")) %>% 
  scroll_box(width = "500px", height = "200px")
```

<div style="border: 1px solid #ddd; padding: 0px; overflow-y: scroll; height:200px; overflow-x: scroll; width:500px; "><table class="table table-striped table-hover" style="margin-left: auto; margin-right: auto;">
 <thead>
  <tr>
   <th style="text-align:left;position: sticky; top:0; background-color: #FFFFFF;"> table_schema </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> aha </td>
  </tr>
  <tr>
   <td style="text-align:left;"> ahasamp </td>
  </tr>
  <tr>
   <td style="text-align:left;"> audit </td>
  </tr>
  <tr>
   <td style="text-align:left;"> blab </td>
  </tr>
  <tr>
   <td style="text-align:left;"> block </td>
  </tr>
  <tr>
   <td style="text-align:left;"> boardex </td>
  </tr>
  <tr>
   <td style="text-align:left;"> boardsmp </td>
  </tr>
  <tr>
   <td style="text-align:left;"> bvd </td>
  </tr>
  <tr>
   <td style="text-align:left;"> bvdsamp </td>
  </tr>
  <tr>
   <td style="text-align:left;"> calcbnch </td>
  </tr>
  <tr>
   <td style="text-align:left;"> cboe </td>
  </tr>
  <tr>
   <td style="text-align:left;"> centris </td>
  </tr>
  <tr>
   <td style="text-align:left;"> ciq </td>
  </tr>
  <tr>
   <td style="text-align:left;"> ciqsamp </td>
  </tr>
  <tr>
   <td style="text-align:left;"> cisdm </td>
  </tr>
  <tr>
   <td style="text-align:left;"> cisdmsmp </td>
  </tr>
  <tr>
   <td style="text-align:left;"> clrvt </td>
  </tr>
  <tr>
   <td style="text-align:left;"> clrvtsmp </td>
  </tr>
  <tr>
   <td style="text-align:left;"> comp </td>
  </tr>
  <tr>
   <td style="text-align:left;"> compa </td>
  </tr>
  <tr>
   <td style="text-align:left;"> compb </td>
  </tr>
  <tr>
   <td style="text-align:left;"> compdcur </td>
  </tr>
  <tr>
   <td style="text-align:left;"> compg </td>
  </tr>
  <tr>
   <td style="text-align:left;"> comph </td>
  </tr>
  <tr>
   <td style="text-align:left;"> compm </td>
  </tr>
  <tr>
   <td style="text-align:left;"> compmcur </td>
  </tr>
  <tr>
   <td style="text-align:left;"> compsamp </td>
  </tr>
  <tr>
   <td style="text-align:left;"> compseg </td>
  </tr>
  <tr>
   <td style="text-align:left;"> compsnap </td>
  </tr>
  <tr>
   <td style="text-align:left;"> comscore </td>
  </tr>
  <tr>
   <td style="text-align:left;"> contrib </td>
  </tr>
  <tr>
   <td style="text-align:left;"> crsp </td>
  </tr>
  <tr>
   <td style="text-align:left;"> crspm </td>
  </tr>
  <tr>
   <td style="text-align:left;"> crspq </td>
  </tr>
  <tr>
   <td style="text-align:left;"> crspsamp </td>
  </tr>
  <tr>
   <td style="text-align:left;"> csmar </td>
  </tr>
  <tr>
   <td style="text-align:left;"> dealscan </td>
  </tr>
  <tr>
   <td style="text-align:left;"> djones </td>
  </tr>
  <tr>
   <td style="text-align:left;"> dmef </td>
  </tr>
  <tr>
   <td style="text-align:left;"> doe </td>
  </tr>
  <tr>
   <td style="text-align:left;"> emdb </td>
  </tr>
  <tr>
   <td style="text-align:left;"> etfg </td>
  </tr>
  <tr>
   <td style="text-align:left;"> etfgsamp </td>
  </tr>
  <tr>
   <td style="text-align:left;"> eureka </td>
  </tr>
  <tr>
   <td style="text-align:left;"> execcomp </td>
  </tr>
  <tr>
   <td style="text-align:left;"> factset </td>
  </tr>
  <tr>
   <td style="text-align:left;"> ff </td>
  </tr>
  <tr>
   <td style="text-align:left;"> fisd </td>
  </tr>
  <tr>
   <td style="text-align:left;"> fisdsamp </td>
  </tr>
  <tr>
   <td style="text-align:left;"> frb </td>
  </tr>
  <tr>
   <td style="text-align:left;"> fssamp </td>
  </tr>
  <tr>
   <td style="text-align:left;"> ftse </td>
  </tr>
  <tr>
   <td style="text-align:left;"> ginsight </td>
  </tr>
  <tr>
   <td style="text-align:left;"> gmi </td>
  </tr>
  <tr>
   <td style="text-align:left;"> govpx </td>
  </tr>
  <tr>
   <td style="text-align:left;"> govpxsmp </td>
  </tr>
  <tr>
   <td style="text-align:left;"> gsi </td>
  </tr>
  <tr>
   <td style="text-align:left;"> hbase </td>
  </tr>
  <tr>
   <td style="text-align:left;"> hbsamp </td>
  </tr>
  <tr>
   <td style="text-align:left;"> hfr </td>
  </tr>
  <tr>
   <td style="text-align:left;"> hfrsamp </td>
  </tr>
  <tr>
   <td style="text-align:left;"> ibes </td>
  </tr>
  <tr>
   <td style="text-align:left;"> ibescorp </td>
  </tr>
  <tr>
   <td style="text-align:left;"> ibeskpi </td>
  </tr>
  <tr>
   <td style="text-align:left;"> ifgr </td>
  </tr>
  <tr>
   <td style="text-align:left;"> ifgrsamp </td>
  </tr>
  <tr>
   <td style="text-align:left;"> ims </td>
  </tr>
  <tr>
   <td style="text-align:left;"> imssamp </td>
  </tr>
  <tr>
   <td style="text-align:left;"> information_schema </td>
  </tr>
  <tr>
   <td style="text-align:left;"> iri </td>
  </tr>
  <tr>
   <td style="text-align:left;"> kld </td>
  </tr>
  <tr>
   <td style="text-align:left;"> ktmine </td>
  </tr>
  <tr>
   <td style="text-align:left;"> ktsamp </td>
  </tr>
  <tr>
   <td style="text-align:left;"> levin </td>
  </tr>
  <tr>
   <td style="text-align:left;"> lspd </td>
  </tr>
  <tr>
   <td style="text-align:left;"> lspdsamp </td>
  </tr>
  <tr>
   <td style="text-align:left;"> lvnsamp </td>
  </tr>
  <tr>
   <td style="text-align:left;"> macrofin </td>
  </tr>
  <tr>
   <td style="text-align:left;"> markit </td>
  </tr>
  <tr>
   <td style="text-align:left;"> mfl </td>
  </tr>
  <tr>
   <td style="text-align:left;"> mrktsamp </td>
  </tr>
  <tr>
   <td style="text-align:left;"> msfanly </td>
  </tr>
  <tr>
   <td style="text-align:left;"> msfinst </td>
  </tr>
  <tr>
   <td style="text-align:left;"> msrb </td>
  </tr>
  <tr>
   <td style="text-align:left;"> msrbsamp </td>
  </tr>
  <tr>
   <td style="text-align:left;"> omtrial </td>
  </tr>
  <tr>
   <td style="text-align:left;"> optionm </td>
  </tr>
  <tr>
   <td style="text-align:left;"> otc </td>
  </tr>
  <tr>
   <td style="text-align:left;"> pg_catalog </td>
  </tr>
  <tr>
   <td style="text-align:left;"> phlx </td>
  </tr>
  <tr>
   <td style="text-align:left;"> ppublica </td>
  </tr>
  <tr>
   <td style="text-align:left;"> ppubsamp </td>
  </tr>
  <tr>
   <td style="text-align:left;"> pwt </td>
  </tr>
  <tr>
   <td style="text-align:left;"> rent </td>
  </tr>
  <tr>
   <td style="text-align:left;"> reprisk </td>
  </tr>
  <tr>
   <td style="text-align:left;"> repsamp </td>
  </tr>
  <tr>
   <td style="text-align:left;"> revere </td>
  </tr>
  <tr>
   <td style="text-align:left;"> risk </td>
  </tr>
  <tr>
   <td style="text-align:left;"> risksamp </td>
  </tr>
  <tr>
   <td style="text-align:left;"> rpna </td>
  </tr>
  <tr>
   <td style="text-align:left;"> rpnasamp </td>
  </tr>
  <tr>
   <td style="text-align:left;"> sdc </td>
  </tr>
  <tr>
   <td style="text-align:left;"> sdcsamp </td>
  </tr>
  <tr>
   <td style="text-align:left;"> secsamp </td>
  </tr>
  <tr>
   <td style="text-align:left;"> snapsamp </td>
  </tr>
  <tr>
   <td style="text-align:left;"> snl </td>
  </tr>
  <tr>
   <td style="text-align:left;"> snlsamp </td>
  </tr>
  <tr>
   <td style="text-align:left;"> sprat </td>
  </tr>
  <tr>
   <td style="text-align:left;"> sustain </td>
  </tr>
  <tr>
   <td style="text-align:left;"> sustsamp </td>
  </tr>
  <tr>
   <td style="text-align:left;"> taqm_2003 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> taqm_2004 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> taqm_2005 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> taqm_2006 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> taqm_2007 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> taqm_2008 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> taqm_2009 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> taqm_2010 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> taqm_2011 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> taqm_2012 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> taqm_2013 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> taqm_2014 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> taqm_2015 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> taqm_2016 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> taqm_2017 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> taqm_2018 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> taqm_2019 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> taqm_2020 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> taqmsamp </td>
  </tr>
  <tr>
   <td style="text-align:left;"> taqmsec </td>
  </tr>
  <tr>
   <td style="text-align:left;"> taqsamp </td>
  </tr>
  <tr>
   <td style="text-align:left;"> tass </td>
  </tr>
  <tr>
   <td style="text-align:left;"> tfn </td>
  </tr>
  <tr>
   <td style="text-align:left;"> totalq </td>
  </tr>
  <tr>
   <td style="text-align:left;"> trace </td>
  </tr>
  <tr>
   <td style="text-align:left;"> trown </td>
  </tr>
  <tr>
   <td style="text-align:left;"> trsamp </td>
  </tr>
  <tr>
   <td style="text-align:left;"> trsdcgs </td>
  </tr>
  <tr>
   <td style="text-align:left;"> trws </td>
  </tr>
  <tr>
   <td style="text-align:left;"> twoiq </td>
  </tr>
  <tr>
   <td style="text-align:left;"> twoiqsmp </td>
  </tr>
  <tr>
   <td style="text-align:left;"> wappsamp </td>
  </tr>
  <tr>
   <td style="text-align:left;"> wrdsapps </td>
  </tr>
  <tr>
   <td style="text-align:left;"> wrdssec </td>
  </tr>
  <tr>
   <td style="text-align:left;"> zacks </td>
  </tr>
  <tr>
   <td style="text-align:left;"> zacksamp </td>
  </tr>
</tbody>
</table></div>


```r
res <- dbSendQuery(wrds, "select distinct table_name
                   from information_schema.columns
                   where table_schema='crsp'
                   order by table_name")
data <- dbFetch(res, n=-1)
dbClearResult(res)

# Printing example
data %>%
  kbl(format="html") %>%  
  kable_styling(bootstrap_options = c("striped", "hover")) %>% 
  scroll_box(width = "500px", height = "200px")
```

<div style="border: 1px solid #ddd; padding: 0px; overflow-y: scroll; height:200px; overflow-x: scroll; width:500px; "><table class="table table-striped table-hover" style="margin-left: auto; margin-right: auto;">
 <thead>
  <tr>
   <th style="text-align:left;position: sticky; top:0; background-color: #FFFFFF;"> table_name </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> acti </td>
  </tr>
  <tr>
   <td style="text-align:left;"> asia </td>
  </tr>
  <tr>
   <td style="text-align:left;"> asib </td>
  </tr>
  <tr>
   <td style="text-align:left;"> asic </td>
  </tr>
  <tr>
   <td style="text-align:left;"> asio </td>
  </tr>
  <tr>
   <td style="text-align:left;"> asix </td>
  </tr>
  <tr>
   <td style="text-align:left;"> bmdebt </td>
  </tr>
  <tr>
   <td style="text-align:left;"> bmheader </td>
  </tr>
  <tr>
   <td style="text-align:left;"> bmpaymts </td>
  </tr>
  <tr>
   <td style="text-align:left;"> bmquotes </td>
  </tr>
  <tr>
   <td style="text-align:left;"> bmyield </td>
  </tr>
  <tr>
   <td style="text-align:left;"> bndprt06 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> bndprt12 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> bxcalind </td>
  </tr>
  <tr>
   <td style="text-align:left;"> bxdlyind </td>
  </tr>
  <tr>
   <td style="text-align:left;"> bxmthind </td>
  </tr>
  <tr>
   <td style="text-align:left;"> bxquotes </td>
  </tr>
  <tr>
   <td style="text-align:left;"> bxyield </td>
  </tr>
  <tr>
   <td style="text-align:left;"> cap </td>
  </tr>
  <tr>
   <td style="text-align:left;"> ccm_lookup </td>
  </tr>
  <tr>
   <td style="text-align:left;"> ccm_qvards </td>
  </tr>
  <tr>
   <td style="text-align:left;"> ccmxpf_linktable </td>
  </tr>
  <tr>
   <td style="text-align:left;"> ccmxpf_lnkhist </td>
  </tr>
  <tr>
   <td style="text-align:left;"> ccmxpf_lnkrng </td>
  </tr>
  <tr>
   <td style="text-align:left;"> ccmxpf_lnkused </td>
  </tr>
  <tr>
   <td style="text-align:left;"> comphead </td>
  </tr>
  <tr>
   <td style="text-align:left;"> comphist </td>
  </tr>
  <tr>
   <td style="text-align:left;"> compmaster </td>
  </tr>
  <tr>
   <td style="text-align:left;"> contact_info </td>
  </tr>
  <tr>
   <td style="text-align:left;"> crsp_cik_map </td>
  </tr>
  <tr>
   <td style="text-align:left;"> crsp_daily_data </td>
  </tr>
  <tr>
   <td style="text-align:left;"> crsp_header </td>
  </tr>
  <tr>
   <td style="text-align:left;"> crsp_monthly_data </td>
  </tr>
  <tr>
   <td style="text-align:left;"> crsp_names </td>
  </tr>
  <tr>
   <td style="text-align:left;"> crsp_portno_map </td>
  </tr>
  <tr>
   <td style="text-align:left;"> crsp_ziman_daily_index </td>
  </tr>
  <tr>
   <td style="text-align:left;"> crsp_ziman_monthly_index </td>
  </tr>
  <tr>
   <td style="text-align:left;"> cs20yr </td>
  </tr>
  <tr>
   <td style="text-align:left;"> cs5yr </td>
  </tr>
  <tr>
   <td style="text-align:left;"> cs90d </td>
  </tr>
  <tr>
   <td style="text-align:left;"> cst_hist </td>
  </tr>
  <tr>
   <td style="text-align:left;"> daily_nav </td>
  </tr>
  <tr>
   <td style="text-align:left;"> daily_nav_ret </td>
  </tr>
  <tr>
   <td style="text-align:left;"> daily_returns </td>
  </tr>
  <tr>
   <td style="text-align:left;"> dividends </td>
  </tr>
  <tr>
   <td style="text-align:left;"> dport1 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> dport2 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> dport3 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> dport4 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> dport5 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> dport6 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> dport7 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> dport8 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> dport9 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> dsbc </td>
  </tr>
  <tr>
   <td style="text-align:left;"> dsbo </td>
  </tr>
  <tr>
   <td style="text-align:left;"> dse </td>
  </tr>
  <tr>
   <td style="text-align:left;"> dse62 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> dse62delist </td>
  </tr>
  <tr>
   <td style="text-align:left;"> dse62dist </td>
  </tr>
  <tr>
   <td style="text-align:left;"> dse62exchdates </td>
  </tr>
  <tr>
   <td style="text-align:left;"> dse62names </td>
  </tr>
  <tr>
   <td style="text-align:left;"> dse62nasdin </td>
  </tr>
  <tr>
   <td style="text-align:left;"> dse62shares </td>
  </tr>
  <tr>
   <td style="text-align:left;"> dseall </td>
  </tr>
  <tr>
   <td style="text-align:left;"> dseall62 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> dsedelist </td>
  </tr>
  <tr>
   <td style="text-align:left;"> dsedist </td>
  </tr>
  <tr>
   <td style="text-align:left;"> dseexchdates </td>
  </tr>
  <tr>
   <td style="text-align:left;"> dsenames </td>
  </tr>
  <tr>
   <td style="text-align:left;"> dsenasdin </td>
  </tr>
  <tr>
   <td style="text-align:left;"> dseshares </td>
  </tr>
  <tr>
   <td style="text-align:left;"> dsf </td>
  </tr>
  <tr>
   <td style="text-align:left;"> dsf62 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> dsfhdr </td>
  </tr>
  <tr>
   <td style="text-align:left;"> dsfhdr62 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> dsi </td>
  </tr>
  <tr>
   <td style="text-align:left;"> dsi62 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> dsia </td>
  </tr>
  <tr>
   <td style="text-align:left;"> dsib </td>
  </tr>
  <tr>
   <td style="text-align:left;"> dsic </td>
  </tr>
  <tr>
   <td style="text-align:left;"> dsio </td>
  </tr>
  <tr>
   <td style="text-align:left;"> dsir </td>
  </tr>
  <tr>
   <td style="text-align:left;"> dsix </td>
  </tr>
  <tr>
   <td style="text-align:left;"> dsiy </td>
  </tr>
  <tr>
   <td style="text-align:left;"> dsp500 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> dsp500list </td>
  </tr>
  <tr>
   <td style="text-align:left;"> dsp500p </td>
  </tr>
  <tr>
   <td style="text-align:left;"> dssc </td>
  </tr>
  <tr>
   <td style="text-align:left;"> dsso </td>
  </tr>
  <tr>
   <td style="text-align:left;"> eod_cap </td>
  </tr>
  <tr>
   <td style="text-align:left;"> eod_sector </td>
  </tr>
  <tr>
   <td style="text-align:left;"> eod_vg </td>
  </tr>
  <tr>
   <td style="text-align:left;"> erdport1 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> erdport2 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> erdport3 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> erdport4 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> erdport5 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> erdport6 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> erdport7 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> erdport8 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> erdport9 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> ermport1 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> ermport2 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> ermport3 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> ermport4 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> ermport5 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> fbpri </td>
  </tr>
  <tr>
   <td style="text-align:left;"> fbyld </td>
  </tr>
  <tr>
   <td style="text-align:left;"> front_load </td>
  </tr>
  <tr>
   <td style="text-align:left;"> front_load_det </td>
  </tr>
  <tr>
   <td style="text-align:left;"> front_load_grp </td>
  </tr>
  <tr>
   <td style="text-align:left;"> fund_fees </td>
  </tr>
  <tr>
   <td style="text-align:left;"> fund_flows </td>
  </tr>
  <tr>
   <td style="text-align:left;"> fund_hdr </td>
  </tr>
  <tr>
   <td style="text-align:left;"> fund_hdr_hist </td>
  </tr>
  <tr>
   <td style="text-align:left;"> fund_names </td>
  </tr>
  <tr>
   <td style="text-align:left;"> fund_style </td>
  </tr>
  <tr>
   <td style="text-align:left;"> fund_summary </td>
  </tr>
  <tr>
   <td style="text-align:left;"> fund_summary2 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> fwdask06 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> fwdask12 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> fwdave06 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> fwdave12 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> fwdbid06 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> fwdbid12 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> hldask06 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> hldask12 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> hldave06 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> hldave12 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> hldbid06 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> hldbid12 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> holdings </td>
  </tr>
  <tr>
   <td style="text-align:left;"> index_descriptions </td>
  </tr>
  <tr>
   <td style="text-align:left;"> index_type_map </td>
  </tr>
  <tr>
   <td style="text-align:left;"> mbi </td>
  </tr>
  <tr>
   <td style="text-align:left;"> mbmdat </td>
  </tr>
  <tr>
   <td style="text-align:left;"> mbmhdr </td>
  </tr>
  <tr>
   <td style="text-align:left;"> mbx </td>
  </tr>
  <tr>
   <td style="text-align:left;"> mbxid </td>
  </tr>
  <tr>
   <td style="text-align:left;"> mcti </td>
  </tr>
  <tr>
   <td style="text-align:left;"> mfdbname </td>
  </tr>
  <tr>
   <td style="text-align:left;"> mhista </td>
  </tr>
  <tr>
   <td style="text-align:left;"> mhistn </td>
  </tr>
  <tr>
   <td style="text-align:left;"> mhistq </td>
  </tr>
  <tr>
   <td style="text-align:left;"> monthly_nav </td>
  </tr>
  <tr>
   <td style="text-align:left;"> monthly_returns </td>
  </tr>
  <tr>
   <td style="text-align:left;"> monthly_tna </td>
  </tr>
  <tr>
   <td style="text-align:left;"> monthly_tna_ret_nav </td>
  </tr>
  <tr>
   <td style="text-align:left;"> mport1 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> mport2 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> mport3 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> mport4 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> mport5 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> mse </td>
  </tr>
  <tr>
   <td style="text-align:left;"> mse62 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> mse62delist </td>
  </tr>
  <tr>
   <td style="text-align:left;"> mse62dist </td>
  </tr>
  <tr>
   <td style="text-align:left;"> mse62exchdates </td>
  </tr>
  <tr>
   <td style="text-align:left;"> mse62names </td>
  </tr>
  <tr>
   <td style="text-align:left;"> mse62nasdin </td>
  </tr>
  <tr>
   <td style="text-align:left;"> mse62shares </td>
  </tr>
  <tr>
   <td style="text-align:left;"> mseall </td>
  </tr>
  <tr>
   <td style="text-align:left;"> mseall62 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> msedelist </td>
  </tr>
  <tr>
   <td style="text-align:left;"> msedist </td>
  </tr>
  <tr>
   <td style="text-align:left;"> mseexchdates </td>
  </tr>
  <tr>
   <td style="text-align:left;"> msenames </td>
  </tr>
  <tr>
   <td style="text-align:left;"> msenasdin </td>
  </tr>
  <tr>
   <td style="text-align:left;"> mseshares </td>
  </tr>
  <tr>
   <td style="text-align:left;"> msf </td>
  </tr>
  <tr>
   <td style="text-align:left;"> msf62 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> msfhdr </td>
  </tr>
  <tr>
   <td style="text-align:left;"> msfhdr62 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> msi </td>
  </tr>
  <tr>
   <td style="text-align:left;"> msi62 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> msia </td>
  </tr>
  <tr>
   <td style="text-align:left;"> msib </td>
  </tr>
  <tr>
   <td style="text-align:left;"> msic </td>
  </tr>
  <tr>
   <td style="text-align:left;"> msio </td>
  </tr>
  <tr>
   <td style="text-align:left;"> msir </td>
  </tr>
  <tr>
   <td style="text-align:left;"> msix </td>
  </tr>
  <tr>
   <td style="text-align:left;"> msiy </td>
  </tr>
  <tr>
   <td style="text-align:left;"> msp500 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> msp500list </td>
  </tr>
  <tr>
   <td style="text-align:left;"> msp500p </td>
  </tr>
  <tr>
   <td style="text-align:left;"> portnomap </td>
  </tr>
  <tr>
   <td style="text-align:left;"> priask06 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> priask12 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> priave06 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> priave12 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> pribid06 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> pribid12 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> price_type </td>
  </tr>
  <tr>
   <td style="text-align:left;"> property_type </td>
  </tr>
  <tr>
   <td style="text-align:left;"> qcti </td>
  </tr>
  <tr>
   <td style="text-align:left;"> qsia </td>
  </tr>
  <tr>
   <td style="text-align:left;"> qsib </td>
  </tr>
  <tr>
   <td style="text-align:left;"> qsic </td>
  </tr>
  <tr>
   <td style="text-align:left;"> qsio </td>
  </tr>
  <tr>
   <td style="text-align:left;"> qsix </td>
  </tr>
  <tr>
   <td style="text-align:left;"> rear_load </td>
  </tr>
  <tr>
   <td style="text-align:left;"> rear_load_det </td>
  </tr>
  <tr>
   <td style="text-align:left;"> rear_load_grp </td>
  </tr>
  <tr>
   <td style="text-align:left;"> rebala </td>
  </tr>
  <tr>
   <td style="text-align:left;"> rebaln </td>
  </tr>
  <tr>
   <td style="text-align:left;"> rebalq </td>
  </tr>
  <tr>
   <td style="text-align:left;"> reit_type </td>
  </tr>
  <tr>
   <td style="text-align:left;"> riskfree </td>
  </tr>
  <tr>
   <td style="text-align:left;"> s6z_del </td>
  </tr>
  <tr>
   <td style="text-align:left;"> s6z_dind </td>
  </tr>
  <tr>
   <td style="text-align:left;"> s6z_dis </td>
  </tr>
  <tr>
   <td style="text-align:left;"> s6z_dp_dly </td>
  </tr>
  <tr>
   <td style="text-align:left;"> s6z_ds_dly </td>
  </tr>
  <tr>
   <td style="text-align:left;"> s6z_hdr </td>
  </tr>
  <tr>
   <td style="text-align:left;"> s6z_indhdr </td>
  </tr>
  <tr>
   <td style="text-align:left;"> s6z_mdel </td>
  </tr>
  <tr>
   <td style="text-align:left;"> s6z_mind </td>
  </tr>
  <tr>
   <td style="text-align:left;"> s6z_mth </td>
  </tr>
  <tr>
   <td style="text-align:left;"> s6z_nam </td>
  </tr>
  <tr>
   <td style="text-align:left;"> s6z_ndi </td>
  </tr>
  <tr>
   <td style="text-align:left;"> s6z_shr </td>
  </tr>
  <tr>
   <td style="text-align:left;"> saz_del </td>
  </tr>
  <tr>
   <td style="text-align:left;"> saz_dind </td>
  </tr>
  <tr>
   <td style="text-align:left;"> saz_dis </td>
  </tr>
  <tr>
   <td style="text-align:left;"> saz_dp_dly </td>
  </tr>
  <tr>
   <td style="text-align:left;"> saz_ds_dly </td>
  </tr>
  <tr>
   <td style="text-align:left;"> saz_hdr </td>
  </tr>
  <tr>
   <td style="text-align:left;"> saz_indhdr </td>
  </tr>
  <tr>
   <td style="text-align:left;"> saz_mdel </td>
  </tr>
  <tr>
   <td style="text-align:left;"> saz_mind </td>
  </tr>
  <tr>
   <td style="text-align:left;"> saz_mth </td>
  </tr>
  <tr>
   <td style="text-align:left;"> saz_nam </td>
  </tr>
  <tr>
   <td style="text-align:left;"> saz_ndi </td>
  </tr>
  <tr>
   <td style="text-align:left;"> saz_shr </td>
  </tr>
  <tr>
   <td style="text-align:left;"> sechead </td>
  </tr>
  <tr>
   <td style="text-align:left;"> sechist </td>
  </tr>
  <tr>
   <td style="text-align:left;"> sector </td>
  </tr>
  <tr>
   <td style="text-align:left;"> sfz_dind </td>
  </tr>
  <tr>
   <td style="text-align:left;"> sfz_indhdr </td>
  </tr>
  <tr>
   <td style="text-align:left;"> sfz_mbr </td>
  </tr>
  <tr>
   <td style="text-align:left;"> sfz_mind </td>
  </tr>
  <tr>
   <td style="text-align:left;"> sfz_portd </td>
  </tr>
  <tr>
   <td style="text-align:left;"> sfz_portm </td>
  </tr>
  <tr>
   <td style="text-align:left;"> sfz_rb </td>
  </tr>
  <tr>
   <td style="text-align:left;"> stocknames </td>
  </tr>
  <tr>
   <td style="text-align:left;"> stocknames62 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> stock_qvards </td>
  </tr>
  <tr>
   <td style="text-align:left;"> sub_property_type </td>
  </tr>
  <tr>
   <td style="text-align:left;"> tfz_dly </td>
  </tr>
  <tr>
   <td style="text-align:left;"> tfz_dly_cd </td>
  </tr>
  <tr>
   <td style="text-align:left;"> tfz_dly_cpi </td>
  </tr>
  <tr>
   <td style="text-align:left;"> tfz_dly_ft </td>
  </tr>
  <tr>
   <td style="text-align:left;"> tfz_dly_rf2 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> tfz_dly_ts2 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> tfz_idx </td>
  </tr>
  <tr>
   <td style="text-align:left;"> tfz_iss </td>
  </tr>
  <tr>
   <td style="text-align:left;"> tfz_mast </td>
  </tr>
  <tr>
   <td style="text-align:left;"> tfz_mth </td>
  </tr>
  <tr>
   <td style="text-align:left;"> tfz_mth_bp </td>
  </tr>
  <tr>
   <td style="text-align:left;"> tfz_mth_cd </td>
  </tr>
  <tr>
   <td style="text-align:left;"> tfz_mth_cpi </td>
  </tr>
  <tr>
   <td style="text-align:left;"> tfz_mth_fb </td>
  </tr>
  <tr>
   <td style="text-align:left;"> tfz_mth_ft </td>
  </tr>
  <tr>
   <td style="text-align:left;"> tfz_mth_rf </td>
  </tr>
  <tr>
   <td style="text-align:left;"> tfz_mth_rf2 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> tfz_mth_ts </td>
  </tr>
  <tr>
   <td style="text-align:left;"> tfz_mth_ts2 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> tfz_pay </td>
  </tr>
  <tr>
   <td style="text-align:left;"> vg </td>
  </tr>
  <tr>
   <td style="text-align:left;"> yldask06 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> yldask12 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> yldave06 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> yldave12 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> yldbid06 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> yldbid12 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> ziman_reit_info </td>
  </tr>
  <tr>
   <td style="text-align:left;"> zr_hdrnames </td>
  </tr>
</tbody>
</table></div>




```r
res <- dbSendQuery(wrds, "select column_name
                   from information_schema.columns
                   where table_schema='crsp'
                   and table_name='msf'
                   order by column_name")
data <- dbFetch(res, n=-1)
dbClearResult(res)


# Printing example
data %>%
  kbl(format="html") %>%  
  kable_styling(bootstrap_options = c("striped", "hover")) %>% 
  scroll_box(width = "500px", height = "200px")
```

<div style="border: 1px solid #ddd; padding: 0px; overflow-y: scroll; height:200px; overflow-x: scroll; width:500px; "><table class="table table-striped table-hover" style="margin-left: auto; margin-right: auto;">
 <thead>
  <tr>
   <th style="text-align:left;position: sticky; top:0; background-color: #FFFFFF;"> column_name </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> altprc </td>
  </tr>
  <tr>
   <td style="text-align:left;"> altprcdt </td>
  </tr>
  <tr>
   <td style="text-align:left;"> ask </td>
  </tr>
  <tr>
   <td style="text-align:left;"> askhi </td>
  </tr>
  <tr>
   <td style="text-align:left;"> bid </td>
  </tr>
  <tr>
   <td style="text-align:left;"> bidlo </td>
  </tr>
  <tr>
   <td style="text-align:left;"> cfacpr </td>
  </tr>
  <tr>
   <td style="text-align:left;"> cfacshr </td>
  </tr>
  <tr>
   <td style="text-align:left;"> cusip </td>
  </tr>
  <tr>
   <td style="text-align:left;"> date </td>
  </tr>
  <tr>
   <td style="text-align:left;"> hexcd </td>
  </tr>
  <tr>
   <td style="text-align:left;"> hsiccd </td>
  </tr>
  <tr>
   <td style="text-align:left;"> issuno </td>
  </tr>
  <tr>
   <td style="text-align:left;"> permco </td>
  </tr>
  <tr>
   <td style="text-align:left;"> permno </td>
  </tr>
  <tr>
   <td style="text-align:left;"> prc </td>
  </tr>
  <tr>
   <td style="text-align:left;"> ret </td>
  </tr>
  <tr>
   <td style="text-align:left;"> retx </td>
  </tr>
  <tr>
   <td style="text-align:left;"> shrout </td>
  </tr>
  <tr>
   <td style="text-align:left;"> spread </td>
  </tr>
  <tr>
   <td style="text-align:left;"> vol </td>
  </tr>
</tbody>
</table></div>


## Download CRSP

After we have connected, we can start download datasets. As these datasets are not particularly small it is vital to indicate which variables to download in order to reduce downloading time. You can explore all the available variables reading the data description guides (Be aware that the naming of the variables might vary from the ones used on WRDS webpage, it is always better to double check on the relative guides).


```r
# BE aware the code is slow (10 mins)
ToDownLoad <- dbSendQuery(wrds, "select DATE, PERMNO, PERMCO, SHROUT, PRC, RETX, BID,ASK,VOL
                   from CRSP.MSF")
crsp <- dbFetch(ToDownLoad, n = -1) 

ToDownLoad <- dbSendQuery(wrds, "select DATE, PERMNO, SHRCD, EXCHCD
                   from CRSP.MSE")
crsp_codes <- dbFetch(ToDownLoad, n = -1)

ToDownLoad <- dbSendQuery(wrds, "select DLSTDT, PERMNO, DLRET
                   from crsp.dsedelist")
delisting_returns <- dbFetch(ToDownLoad, n = -1)

crsp=as.data.table(crsp)
crsp_codes=as.data.table(crsp_codes)
delisting_returns=as.data.table(delisting_returns)

# Clean Data
crsp <- crsp %>%
  filter(!is.na(prc)) %>%# Remove data with missing prices
  mutate(date = as.yearmon(as.Date(date)))

crsp_codes <- crsp_codes %>%# Remove data with missing sharecodes
  filter(!is.na(shrcd)) %>%
  mutate(date = as.yearmon(as.Date(date)))

delisting_returns <- delisting_returns %>%# Keep only days with delisting returns
  filter(!is.na(dlret)) %>%
  mutate(date = as.yearmon(as.Date(dlstdt))) %>%
  select(-dlstdt)

# Merge datasets
CRSP <- crsp %>%
  merge(crsp_codes, by=c("date", "permno"), all=TRUE, allow.cartesian=TRUE) %>%
  merge(delisting_returns, by=c("date", "permno"), all=TRUE, allow.cartesian=TRUE) 

# Fill share code and exchange code observations  
CRSP=CRSP%>%group_by(permno)%>%arrange(date)%>%fill(shrcd,.direction = "downup")
CRSP=CRSP%>%group_by(permno)%>%arrange(date)%>%fill(exchcd,.direction = "downup")
CRSP=as.data.table(CRSP)

# retreive treasury bonds for risk free rate
res <- dbSendQuery(wrds, "select *
                   from crsp.mcti")
rf <- dbFetch(res, n=-1)

# retreive stock indexes
res <- dbSendQuery(wrds, "select *
                   from crsp.msi")
stockindexes <- dbFetch(res, n=-1)

# Save variables
save(rf, file = "riskfree.RData")
save(stockindexes, file = "stockindexes.RData")
save(CRSP, file = "CRSP.RData")
rm(crsp,crsp_codes,delisting_returns,res,data,ToDownLoad)
```

## Download Compustat

Let's now download the Compustat dataset. With it lets download the CRSP\Compustat linking table. The linking table is provided by WRDS and contains all the links between CRSP and Compustat datasets. We merge the linking table with Compustat and we will only use valid links (linktype) as defined in WRDS and only primary issues (linkprim). Second, we check that the link is contained in the linking dates. (the linking table provides first day of linking and last day).


```r
# Download Compustat and Compustat/Crsp merging link from WRDS
ToDownLoad <- dbSendQuery(wrds,"select CONM,EXCHG ,GVKEY, CUSIP, DATADATE, FYR, FYEAR, SICH, NAICSH, AT, LT, SEQ, CEQ, PSTKL, PSTKRV, PSTK, TXDITC, TXDB, ITCB,
                            REVT, COGS, XINT, XSGA, IB, TXDI, DVC, ACT, CHE, LCT, DT, NI,
                            DLC, TXP, DP, PPEGT, INVT, CSHO, PRCC_F,PRCC_C,DLTT ,CIK,TIC
                    from COMPM.FUNDA
                    where INDFMT='INDL' and DATAFMT='STD' and CONSOL='C' and POPSRC='D'") 
Compustat_Yearly_noncomplete <- dbFetch(ToDownLoad, n = -1) # n=-1 retrieve all record 


# Retrieve Merged Compustat/CRSP link table
ToDownLoad <- dbSendQuery(wrds,"select GVKEY, LPERMCO, LINKDT, LINKENDDT, LINKTYPE, LINKPRIM , LPERMNO
                    from crsp.ccmxpf_lnkhist")
LinkingTable <- dbFetch(ToDownLoad, n = -1) 

# Define Datatables
LinkingTable=as.data.table(LinkingTable)
Compustat_Yearly_noncomplete=as.data.table(Compustat_Yearly_noncomplete)

# Merge the linked Permno onto Compustat dataset
# omitting non-primary linktypes led to 1% fewer obs (2,000) but cleaner data (datadate<="2013-12-31" for comparability)
Compustat <-  LinkingTable %>%
  filter(linktype %in% c("LU", "LC", "LS")) %>%  
  filter(linkprim %in% c("P", "C", "J")) %>% # use only primary links
  merge(Compustat_Yearly_noncomplete, by="gvkey")%>% 
  mutate(date = as.Date(datadate), 
         permco=as.numeric(lpermco),
         permno=as.numeric(lpermno),
         linkdt = as.Date(linkdt),
         linkenddt = as.Date(linkenddt),
         linktype = factor(linktype, levels=c("LC", "LU", "LS")),
         linkprim = factor(linkprim, levels=c("P", "C", "J"))) %>%
  filter(date >= linkdt & (date <= linkenddt | is.na(linkenddt))) %>% # remove compustat fiscal ends that do not fall within linked period; linkenddt=NA (from .E) means ongoing  
  arrange(date, permco, linktype, linkprim) %>%# prioritize linktype, linkprim based on order of preference/primary if duplicate
  distinct(date, permco, .keep_all = TRUE) %>% 
  select(-datadate,-lpermco,-lpermno,-linkenddt,-linkdt,-linkprim,-linktype,-gvkey,-permco) %>% 
  mutate(fyear=as.integer(as.yearmon(date) - 6/12 + 1)-1) # Arrange fiscal year

# Save variable
save(Compustat, file = "Compustat.RData")
rm(LinkingTable,Compustat_Yearly_noncomplete,ToDownLoad,wrds)
```

## Download Historical Book Values of Equity 

Since the CSRP dataset begins well before the Compustat dataset, in WRDS we do not have accounting data for the years 1926 to 1950. To partially solve this problem, Prof. Kenneth French gift us with some data from Moodys manuals to cover the period. In this chunck of code, we will connect to his website, download the data and modify it to be similar to the Compustat one. Following Fama and French (1993) we will shift the accounting values of one year.


```r
# Define temporary directory, download and upload data
temp <- tempfile()
download.file("http://mba.tuck.dartmouth.edu/pages/faculty/ken.french/ftp/Historical_BE_Data.zip",temp)
HistoricalBook <- read.table(unz(temp, "DFF_BE_With_Nonindust.txt"))
unlink(temp)

# Adjust names of columns
x=seq(1926,2001,1)
colnames(HistoricalBook)=c("permno","FirstY","LastY",x)

# Set missing values to NA
HistoricalBook[HistoricalBook == -999 | HistoricalBook == -99.99] <- NA

# Adjust data to same format as compustat data
HistoricalBook <- HistoricalBook %>%
  select(-FirstY, -LastY) %>%
  gather(fyear, BEH, -permno, na.rm=TRUE) %>% 
   mutate(fyear=as.numeric(fyear))# set date June of SAME year when data would have been known (based on French website notes)
```


# Filters
## CRSP
Now we have three complete dataset and we need to clean them. Let's start with the CRSP dataset. First, we will retain only common stocks (shrcd 10 or 11) which trade on NYSE, AMEX or NASDAQ (exchcd 1 or 2 or 3). Then, we will adjust the returns, by first setting the missing values and then adjusting for the delisting returns.
Lastly, we will compute the market capitalization as sum of the security issues from the same company and we will keep only the largest issue in therm of market value each month.


```r
CRSP <- CRSP %>% 
  filter(exchcd %in% c(1, 2, 3)) %>% # Filter NYSE AMEX and NASDAQ
  filter(shrcd%in%c(10,11)) %>% # Keep Common stocks in CRSP
  mutate(ret=as.numeric(as.character(retx)),
       dlret= as.numeric(as.character(dlret)))# Returns in numeric

# Adjust returns for "bad" returns
CRSP$ret[which(CRSP$ret == -99) | which(CRSP$ret == "C") | which(CRSP$ret == "B")] <- NA

# Adjust for delisting and absolute value of price
CRSP <- CRSP %>% 
mutate(ret = ifelse(is.na(ret), dlret, ret),
       prc = abs(prc)) %>% 
  select(-dlret,-retx)

# PERMNO = Issue , permco = Company
# Create the market capitalization
CRSP <- CRSP %>% 
mutate(ME=(prc*shrout)/1000) %>% # convert from thousands to millions (consistent with compustat values)
  group_by(date,permco) %>% 
  mutate(MKTCAP = sum(ME,na.rm=T),
        MKTCAP = ifelse(MKTCAP == 0, as.numeric(NA), MKTCAP))
# %>% 
# arrange(date, permco, desc(ME)) %>%
#   group_by(date, permco) %>%
#   slice(1) %>% # keep only permno with largest ME
#   ungroup
CRSP=as.data.table(CRSP)

save(CRSP,file="CRSP.RData")
```

## Compustat 
We will create the book value of equity as defined by French website and we will select only stocks with positive book value of equity. We will shift the fiscal year one year ahead and we will merge the datasets. Then, we will select the book value of equity first from Compustat and if missing from the historical file.



```r
# Create BE, filter existing and positive BE and adjust fiscal year
Compustat <- Compustat %>%
  group_by(permno) %>%
  mutate(BE = coalesce(seq, ceq + pstk, at - lt) + coalesce(txditc, txdb + itcb, 0) - 
              coalesce(pstkrv, pstkl, pstk, 0), # consistent w/ French website variable definitions
         OpProf = (revt - coalesce(cogs, 0) - coalesce(xint, 0) - coalesce(xsga,0)),
         OpProf = as.numeric(ifelse(is.na(cogs) & is.na(xint) & is.na(xsga), NA, OpProf)), # FF condition
         GrProf = (revt - cogs),
         Cflow = ib + coalesce(txdi, 0) + dp,  # operating; consistent w/ French website variable definitions
         Inv = (coalesce(ppegt - lag(ppegt), 0) + coalesce(invt - lag(invt), 0)) / lag(at),
         AstChg = (at - lag(at)) / lag(at)) %>% ungroup %>%
  select(fyear, permno, BE) %>%
  mutate_if(is.numeric, funs(ifelse(is.infinite(.), NA, .))) %>% # replace Inf w/ NA's
  mutate_if(is.numeric, funs(round(., 5)))%>% # round to 5 decimal places (for some reason, 0's not properly coded in some instances)
  mutate(fyear=fyear+1)

Compustat=as.data.table(Compustat)
HistoricalBook=as.data.table(HistoricalBook)

# Merge all datasets and create only one BE
CRSP <- CRSP %>%
  mutate(fyear=as.integer(date - 6/12 + 1)-1) %>% # Create fiscal year in CRSP
  merge(Compustat,by=c("fyear","permno"),all = T,allow.cartesian=T) %>%
  merge(HistoricalBook,by=c("fyear","permno"),all = T,allow.cartesian=T) %>%
  mutate(BE=coalesce(BE,BEH)) %>% # Select BE first from Compustat if missing from the historical file
  select(-BEH)
```


## BM and Size

We will create market capitalization in December to be used to create the BM and in June as the Size following the indication of Fama and French (1993). Then we will create the BM as book value of equity divided by market capitalization in December, and we will select only stock with existing and positive book value of equity, market capitalization in June and market capitalization in December.



```r
# Create MRKCAP from BM/ME in December 
MRK <- CRSP %>% 
  filter(month(date) == 12) %>% 
  group_by(permno,fyear) %>% 
  transmute(ME_Dec = MKTCAP) %>% 
  filter(!is.na(ME_Dec)&!is.infinite(ME_Dec)) %>% # Filter existing December ME before merging
  mutate(fyear=fyear+1)

CRSP <- merge(CRSP, MRK, by = c("permno", "fyear"),all.x = T)

# Create MRKCAP for Size in June
MRK <- CRSP %>% 
  filter(month(date) == 6) %>% 
  group_by(permno,fyear) %>% 
  transmute(Size = MKTCAP) %>% 
  filter(!is.na(Size)&!is.infinite(Size)) %>% # Filter existing December ME before merging
  mutate(fyear=fyear+1)

CRSP <- merge(CRSP, MRK, by = c("permno", "fyear"),all.x=T)
rm(MRK)

# Create BM/ME
CRSP <- CRSP %>% 
  mutate(BM=BE/ME_Dec) %>% 
  filter(BM>0&!is.na(BM)&!is.na(Size))
```
