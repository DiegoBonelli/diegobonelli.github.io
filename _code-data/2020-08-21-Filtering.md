---
title: "Explore and merge the CRSP and Compustat databases"
author: "Diego Bonelli"
date: '2020-08-20'
classes: wide
excerpt: Learn how to filter and visualize the CCM database
collection: code-data
permalink: /code-data/Filtering_CCM/
related: no
tags:
- Finance
- WRDS
- R
author_profile: yes
toc: yes

---
# Explore and merge the CRSP and Compustat databases

In this post I will exxplain how to merge and CRSP and Compustat, and I will visualize the data. CRSP contains security pricing and market data, while Compustat instead contains accounting data. If you haven't downloaded the data yet have a look at the previous [post](https://diegobonelli.github.io/code-data/WRDS). Firsly, I will remove existing variables from the environment and upload some useful packages.


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
library(scales) # Nicer axes of figures
library(wesanderson) # Fancy colors for plots
pal <- wes_palette("Darjeeling1",type="discrete") # Define color palette
```

# CRSP
Let's start with the CRSP dataset. First, we will retain only common stocks (shrcd 10 or 11). Then, we will adjust the returns, by first setting the missing values and then adjusting for the delisting. I then adjust the prices, the column prc contains the closing price of the last trading day of the month or the in negative of the average of bid and ask. Thus, I will take the absolute value of price and adjust it for delisting prices. Lastly, we will compute the market capitalization as sum of the security issues from the same company and we will keep only the largest issue in therm of market value each month.


```r
load("CRSP.RData")

CRSP <- CRSP %>% 
  filter(shrcd%in%c(10,11)) %>% # Keep Common stocks in CRSP
  mutate(ret=as.numeric(as.character(ret)),
       dlret= as.numeric(as.character(dlret)))# Returns in numeric

# Adjust returns for "bad" returns
CRSP$ret[which(CRSP$ret == -99) | which(CRSP$ret == "C") | which(CRSP$ret == "B")] <- NA

# Adjust for delisting and absolute value of price
CRSP <- CRSP %>% 
mutate(ret = ifelse(is.na(ret), dlret, ret),
       prc = abs(prc),
       prc = ifelse(is.na(prc), dlprc, prc)) %>% 
  select(-dlret,-dlprc)

# permno = Issue , permco = Company
# Create the market capitalization
CRSP <- CRSP %>% 
mutate(ME=(prc*shrout)/1000) %>% # convert from thousands to millions (consistent with compustat values)
  group_by(date,permco) %>% 
  mutate(MKTCAP = sum(ME,na.rm=T),
        MKTCAP = ifelse(MKTCAP == 0, as.numeric(NA), MKTCAP)) %>% 
arrange(date, permco, desc(ME)) %>%
  group_by(date, permco) %>%
  slice(1) %>% # keep only permno with largest ME
  ungroup
```


## Exchanges 

Let's now find the exchanges from the exchange code variable and plot the number of firms trading on each of them.


```r
# Rename exchange codes
CRSP <- CRSP %>%
  mutate(exchange = case_when(exchcd %in% c(1, 31) ~ "NYSE",
                              exchcd %in% c(2, 32) ~ "AMEX",
                              exchcd %in% c(3, 33) ~ "NASDAQ",
                              TRUE ~ "Other"),
         exchange = factor(exchange, levels = c("NASDAQ", "AMEX", "NYSE","Other"))) %>%
  distinct()

# Plot number of firms in each exchange
CRSP %>%  mutate(date=as.Date(date)) %>% 
        dplyr::count(exchange, date) %>%
       ggplot(aes(x=date, y=n, fill=exchange)) +
  geom_area(alpha=0.5,colour="black")+
  labs(x = "", y = "Number of Firms", 
       color = "Exchange", linetype = "Exchange") + 
  scale_y_continuous(labels = comma, breaks = pretty_breaks()) + 
  scale_x_date(expand = c(0, 0), date_breaks = "10 years", date_labels = "%Y") +
  theme_classic()+
  scale_fill_manual(values=c(pal[1],pal[2],pal[3],pal[5])) 
```

![](Filtering_files/figure-html/unnamed-chunk-3-1.png)<!-- -->

## Market Capitalization

Finally, we can also plot the market capitalization of each exchange by summing the market capitalization of each firm inside each exchange.



```r
# Plot total market cap of firms in each exchange
CRSP %>%  mutate(date=as.Date(date)) %>% 
  group_by(date,exchange) %>% 
  summarise(market_cap_exchange=sum(MKTCAP,na.rm=T)/1000)%>% 
  ggplot(aes(x=date, y=market_cap_exchange ,  fill=exchange))+
  geom_area(alpha=0.5, colour="black")+
  labs(x = "", y = "Market Capitalization", 
       color = "exchange", linetype = "exchange") + 
  scale_y_continuous(labels = comma, breaks = pretty_breaks()) + 
  scale_x_date(expand = c(0, 0), date_breaks = "10 years", date_labels = "%Y") + 
  theme_classic()+
  scale_fill_manual(values=c(pal[1],pal[2],pal[3],pal[5])) 
```

![](Filtering_files/figure-html/unnamed-chunk-4-1.png)<!-- -->

# Industries

Using the 4 digit sic codes we can see in which industry each firm operate. Here I tranform the sic codes in the Fama and French 12 industries classication. Afterwards, I will plot the number of firm in each industry through time.


```r
## define industry labels
CRSP <- CRSP %>%
  mutate(industry = case_when(siccd >= 1 & siccd <= 999 ~ "Agriculture",
                              siccd >= 1000 & siccd <= 1499 ~ "Mining",
                              siccd >= 1500 & siccd <= 1799 ~ "Construction",
                              siccd >= 2000 & siccd <= 3999 ~ "Manufacturing",
                              siccd >= 4000 & siccd <= 4999 ~ "Transportation",
                              siccd >= 5000 & siccd <= 5199 ~ "Wholesale",
                              siccd >= 5200 & siccd <= 5999 ~ "Retail",
                              siccd >= 6000 & siccd <= 6999 ~ "Finance",
                              siccd >= 7000 & siccd <= 8999 ~ "Services",
                              siccd >= 9000 & siccd <= 9999 ~ "Public",
                              TRUE ~ "Missing"))

# Plot number of firms in each industry
CRSP %>%  mutate(date=as.Date(date)) %>% 
        dplyr::count(industry, date)%>%
       ggplot(aes(x=date, y=n,fill=industry)) +
  geom_area(alpha=0.5,colour="black")+
  labs(x = "", y = "Number of Firms", 
       color = "industry", linetype = "industry") + 
  scale_y_continuous(labels = comma, breaks = pretty_breaks()) + 
  scale_x_date(expand = c(0, 0), date_breaks = "10 years", date_labels = "%Y") +
  theme_classic()
```

![](Filtering_files/figure-html/unnamed-chunk-5-1.png)<!-- -->


I can also plot the market capitalization for each industry.




```r
# Plot total market cap of firms in each industry
CRSP %>%  mutate(date=as.Date(date)) %>% 
  group_by(date,industry) %>% 
  summarise(market_cap_industry=sum(MKTCAP,na.rm=T)/1000)%>% 
  ggplot(aes(x=date, y=market_cap_industry ,  fill=industry))+
  geom_area(alpha=0.5, colour="black")+
  labs(x = "", y = "Market Capitalization", 
       color = "industry", linetype = "industry") + 
  scale_y_continuous(labels = comma, breaks = pretty_breaks()) + 
  scale_x_date(expand = c(0, 0), date_breaks = "10 years", date_labels = "%Y") + 
  theme_classic()
```

![](Filtering_files/figure-html/unnamed-chunk-6-1.png)<!-- -->
# Historical Book Values of Equity 

Since the CSRP dataset begins well before the Compustat dataset, in WRDS we do not have accounting data for the years 1926 to 1950. To partially solve this problem, Prof. Kenneth French gift us with some data from Moodys manuals to cover the period. In this chunck of code, we will connect to his website, download the data and modify it to be similar to the Compustat one. Following Fama and French (1993)[https://www.sciencedirect.com/science/article/abs/pii/0304405X93900235] we will shift the accounting values of one year.


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
  mutate(fyear=as.numeric(fyear)) # Set fiscal year
```


# Compustat 

Now, let's load again the Compustat file. Following Kenneth French Website definitions[http://mba.tuck.dartmouth.edu/pages/faculty/ken.french/Data_Library/variable_definitions.html] I create Book to Equity, Operating Profitability, Cash Flow and Investment, while following Novy-Marx (2013[https://www.sciencedirect.com/science/article/abs/pii/S0304405X13000044] I create Gross Profitability. I will shift the fiscal year one year ahead and we will merge the datasets. Then, we will select the book value of equity first from Compustat and if missing from the historical file. I will merge using permno and fiscal year. Using permno in this case is equal to using permco as, in the previous post, I selected only the largest permno per each permco.



```r
load("Compustat.RData")

# Create BE 
Compustat <- Compustat %>%
  group_by(permno) %>%
  mutate(BE = coalesce(seq, ceq + pstk, at - lt) + coalesce(txditc, txdb + itcb, 0) - 
              coalesce(pstkrv, pstkl, pstk, 0), # French website variable definition
         OpProf = (revt - coalesce(cogs, 0) - coalesce(xint, 0) - coalesce(xsga,0)),
         OpProf = as.numeric(ifelse(is.na(cogs) & is.na(xint) & is.na(xsga), NA, OpProf)), # FF condition
         GrProf = (revt - cogs), # Following Novy-Marx (2013)
         Cflow = ib + coalesce(txdi, 0) + dp, 
         Inv = (at - lag(at)) / lag(at)) %>% ungroup %>%
  select(fyear, permno, BE,OpProf,GrProf,Cflow,Inv) %>%
  mutate_if(is.numeric, funs(ifelse(is.infinite(.), NA, .))) %>% # replace Inf w/ NA's
mutate_if(is.numeric, funs(round(., 5)))%>% # round to 5 decimal places
  mutate(fyear=fyear+1)

# Data table to merge faster
Compustat=as.data.table(Compustat)
HistoricalBook=as.data.table(HistoricalBook)

# Merge all datasets and create only one BE
CRSP <- CRSP %>%
  filter(exchange%in% c("NYSE","AMEX","NASDAQ")) %>%  # trade on NYSE, AMEX or NASDAQ (exchcd 1 or 2 or 3)
  mutate(fyear=as.integer(date - 6/12 + 1)-1) %>% # Create fiscal year in CRSP
  merge(Compustat,by=c("fyear","permno"),all = T,allow.cartesian=T) %>%
  merge(HistoricalBook,by=c("fyear","permno"),all = T,allow.cartesian=T) %>%
  mutate(BE=coalesce(BE,BEH)) %>% # Select BE first from Compustat if missing from the historical file
  select(-BEH)

save(CRSP,file="CRSP.RData")
```



Now I have a complete dataset, combining both market data and accounting data, and I can start summarizing the data. I create a table containing the monthly average and standard deviations of returns and summarizing the accounting variables for each industry.



```r
  CRSP %>% 
  filter(!is.na(ret)) %>% 
  group_by(industry)%>%
  summarise(Returns=mean(ret,na.rm=T)*100,
            Std = sd(ret,na.rm=T)*100,
            ME=mean(MKTCAP,na.rm=T),
            BE=mean(BE,na.rm=T),
            OpProf=mean(OpProf,na.rm=T),
            GrProf=mean(GrProf,na.rm=T),
            Cflow=mean(Cflow,na.rm=T),
            Inv=mean(Inv,na.rm=T)) %>% 
   kbl(digits = 2,format.args = list(na.encode=T)) %>%
  row_spec(0,bold=T) %>% 
  column_spec(1,bold=T) %>% 
  kable_styling(c("striped", "hover", "condensed", "responsive"),full_width=F)
```

<table class="table table-striped table-hover table-condensed table-responsive" style="width: auto !important; margin-left: auto; margin-right: auto;">
 <thead>
  <tr>
   <th style="text-align:left;font-weight: bold;"> industry </th>
   <th style="text-align:right;font-weight: bold;"> Returns </th>
   <th style="text-align:right;font-weight: bold;"> Std </th>
   <th style="text-align:right;font-weight: bold;"> ME </th>
   <th style="text-align:right;font-weight: bold;"> BE </th>
   <th style="text-align:right;font-weight: bold;"> OpProf </th>
   <th style="text-align:right;font-weight: bold;"> GrProf </th>
   <th style="text-align:right;font-weight: bold;"> Cflow </th>
   <th style="text-align:right;font-weight: bold;"> Inv </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;font-weight: bold;"> Agriculture </td>
   <td style="text-align:right;"> 0.81 </td>
   <td style="text-align:right;"> 19.27 </td>
   <td style="text-align:right;"> 275.43 </td>
   <td style="text-align:right;"> 111.36 </td>
   <td style="text-align:right;"> 26.89 </td>
   <td style="text-align:right;"> 67.36 </td>
   <td style="text-align:right;"> 20.10 </td>
   <td style="text-align:right;"> 0.15 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;"> Construction </td>
   <td style="text-align:right;"> 1.08 </td>
   <td style="text-align:right;"> 19.20 </td>
   <td style="text-align:right;"> 530.46 </td>
   <td style="text-align:right;"> 323.68 </td>
   <td style="text-align:right;"> 49.04 </td>
   <td style="text-align:right;"> 154.73 </td>
   <td style="text-align:right;"> 38.97 </td>
   <td style="text-align:right;"> 0.15 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;"> Finance </td>
   <td style="text-align:right;"> 1.15 </td>
   <td style="text-align:right;"> 16.32 </td>
   <td style="text-align:right;"> 1537.15 </td>
   <td style="text-align:right;"> 1278.12 </td>
   <td style="text-align:right;"> 287.96 </td>
   <td style="text-align:right;"> 555.77 </td>
   <td style="text-align:right;"> 183.18 </td>
   <td style="text-align:right;"> 0.16 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;"> Manufacturing </td>
   <td style="text-align:right;"> 1.25 </td>
   <td style="text-align:right;"> 17.84 </td>
   <td style="text-align:right;"> 1517.62 </td>
   <td style="text-align:right;"> 541.86 </td>
   <td style="text-align:right;"> 192.56 </td>
   <td style="text-align:right;"> 451.41 </td>
   <td style="text-align:right;"> 146.63 </td>
   <td style="text-align:right;"> 0.16 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;"> Mining </td>
   <td style="text-align:right;"> 0.95 </td>
   <td style="text-align:right;"> 20.96 </td>
   <td style="text-align:right;"> 1052.34 </td>
   <td style="text-align:right;"> 652.32 </td>
   <td style="text-align:right;"> 142.98 </td>
   <td style="text-align:right;"> 225.43 </td>
   <td style="text-align:right;"> 123.53 </td>
   <td style="text-align:right;"> 0.28 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;"> Missing </td>
   <td style="text-align:right;"> -1.05 </td>
   <td style="text-align:right;"> 25.65 </td>
   <td style="text-align:right;"> 56.04 </td>
   <td style="text-align:right;"> 21.69 </td>
   <td style="text-align:right;"> 4.96 </td>
   <td style="text-align:right;"> 12.93 </td>
   <td style="text-align:right;"> 2.14 </td>
   <td style="text-align:right;"> 0.12 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;"> Public </td>
   <td style="text-align:right;"> 0.67 </td>
   <td style="text-align:right;"> 24.40 </td>
   <td style="text-align:right;"> 1566.17 </td>
   <td style="text-align:right;"> 464.80 </td>
   <td style="text-align:right;"> 59.59 </td>
   <td style="text-align:right;"> 214.30 </td>
   <td style="text-align:right;"> 31.37 </td>
   <td style="text-align:right;"> 0.30 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;"> Retail </td>
   <td style="text-align:right;"> 1.02 </td>
   <td style="text-align:right;"> 16.80 </td>
   <td style="text-align:right;"> 1546.44 </td>
   <td style="text-align:right;"> 489.37 </td>
   <td style="text-align:right;"> 196.03 </td>
   <td style="text-align:right;"> 750.23 </td>
   <td style="text-align:right;"> 142.03 </td>
   <td style="text-align:right;"> 0.16 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;"> Services </td>
   <td style="text-align:right;"> 1.18 </td>
   <td style="text-align:right;"> 22.21 </td>
   <td style="text-align:right;"> 1634.63 </td>
   <td style="text-align:right;"> 401.71 </td>
   <td style="text-align:right;"> 112.29 </td>
   <td style="text-align:right;"> 267.48 </td>
   <td style="text-align:right;"> 79.41 </td>
   <td style="text-align:right;"> 0.26 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;"> Transportation </td>
   <td style="text-align:right;"> 1.11 </td>
   <td style="text-align:right;"> 15.64 </td>
   <td style="text-align:right;"> 2033.21 </td>
   <td style="text-align:right;"> 1335.02 </td>
   <td style="text-align:right;"> 368.41 </td>
   <td style="text-align:right;"> 650.20 </td>
   <td style="text-align:right;"> 311.80 </td>
   <td style="text-align:right;"> 0.19 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;"> Wholesale </td>
   <td style="text-align:right;"> 1.08 </td>
   <td style="text-align:right;"> 19.13 </td>
   <td style="text-align:right;"> 755.35 </td>
   <td style="text-align:right;"> 293.06 </td>
   <td style="text-align:right;"> 79.06 </td>
   <td style="text-align:right;"> 278.64 </td>
   <td style="text-align:right;"> 54.70 </td>
   <td style="text-align:right;"> 0.26 </td>
  </tr>
</tbody>
</table>

