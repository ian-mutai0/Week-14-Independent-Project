---
title: "Anomaly Detection Project"
author: "Mr. Mutai"
date: "7/20/2021"
output: 
  html_document: 
    keep_md: yes
---



# ASSESSMENT QUESTION

You have been requested to check whether there are any anomalies in the given sales dataset. The objective of this task being fraud detection.

# 1. DEFINING THE QUESTION 

## i) Defining the Specific Data Analytic Question 

To be able to detect anomalies in the given foecasting sales dataset.

## ii) Defining the Metric for Success 

To detect and plot any anomalies in our dataset. 

## iii) Understanding the Context 

The package **anomalize** which is open-sourced by Business Science does Time Series Anomaly Detection that goes inline with other Tidyverse packages (or packages supporting tidy data) – with one of the most used Tidyverse functionality – compatibility with the pipe %>% operator to write readable and reproducible data pipeline.

Before starting with Time Series forecasting or Modelling, there is Time Series Decomposition where the Time series data is decomposed into Observed, Seasonal, Trend and Remainder components using the **tidy_decompose() function**. **Anomalize() function** performs anomaly detection on the decomposed data using the remainder column and creates 3 new columns; remainder_l1 (lower limit), remainder_l2 (upper limit) and anomaly (Yes/No Flag). The default method is method = "iqr", which is fast and relatively accurate at detecting anomalies. We then create the lower and upper bounds around the observed values through the use of the **time_recompose() function** which recomposes the lower and upper bounds of the anomalies around the observed values and creates new columns: recomposed_l1 (lower limit) and recomposed_l2 (upper limit). 

# 2. IMPLEMENTING THE SOLUTION 

First we install and load the required packages. 


```r
library(anomalize) # tidy anomaly detectiom
```

```
## == Use anomalize to improve your Forecasts by 50%! =============================
## Business Science offers a 1-hour course - Lab #18: Time Series Anomaly Detection!
## </> Learn more at: https://university.business-science.io/p/learning-labs-pro </>
```

```r
library(tidyverse) # tidyverse packages like dplyr, ggplot, tidyr
```

```
## -- Attaching packages --------------------------------------- tidyverse 1.3.1 --
```

```
## v ggplot2 3.3.5     v purrr   0.3.4
## v tibble  3.1.2     v dplyr   1.0.7
## v tidyr   1.1.3     v stringr 1.4.0
## v readr   1.4.0     v forcats 0.5.1
```

```
## -- Conflicts ------------------------------------------ tidyverse_conflicts() --
## x dplyr::filter() masks stats::filter()
## x dplyr::lag()    masks stats::lag()
```

```r
library(tibble)
library(tibbletime) # time series data
```

```
## 
## Attaching package: 'tibbletime'
```

```
## The following object is masked from 'package:stats':
## 
##     filter
```

```r
library(data.table)
```

```
## 
## Attaching package: 'data.table'
```

```
## The following objects are masked from 'package:dplyr':
## 
##     between, first, last
```

```
## The following object is masked from 'package:purrr':
## 
##     transpose
```

```r
library(dplyr)
```

We read the dataset into our environment.


```r
forecast <- read.csv("C:/Users/hp/Downloads/Supermarket_Sales_Forecasting - Sales.csv")
```

View our forecasting sales dataset in our environment. 


```r
View(forecast)

# Our dataset has two columns; Date and Sales
```

Check the dimensions of the dataset. 


```r
dim(forecast)
```

```
## [1] 1000    2
```

Check for missing values in our dataset. 


```r
colSums(is.na(forecast))
```

```
##  Date Sales 
##     0     0
```

Preview the first 5 columns in our dataset.


```r
head(forecast)
```

```
##        Date    Sales
## 1  1/5/2019 548.9715
## 2  3/8/2019  80.2200
## 3  3/3/2019 340.5255
## 4 1/27/2019 489.0480
## 5  2/8/2019 634.3785
## 6 3/25/2019 627.6165
```

Convert the date column in our dataset into a datetime format


```r
forecast$Date <- as.Date(forecast$Date, format = "%m/%d/%Y")
forecast$Date <- sort(forecast$Date, decreasing = FALSE)
```

Convert the sales into a tibbletime object. 


```r
forecast <- as_tbl_time(forecast, index = Date)

forecast <- forecast %>%
  as_period("daily")
```

Anomaly Detection.


```r
forecast %>%
  time_decompose(Sales) %>%
  anomalize(remainder, alpha = 0.1, max_anoms = 0.2) %>%
  time_recompose() %>%
  plot_anomalies(time_recomposed = TRUE, ncol = 3, alpha_dots = 0.5)
```

```
## frequency = 7 days
```

```
## trend = 30 days
```

```
## Registered S3 method overwritten by 'quantmod':
##   method            from
##   as.zoo.data.frame zoo
```

![](Anomaly-Detection-Project_files/figure-html/unnamed-chunk-9-1.png)<!-- -->

using the default alpha which is 0.05, we did not detect any anomalies but as soon as we changed the alpha to 0.1, we detect a few anomalies which we can see on the above plot. 

We can actually go ahead to determine the exact dates that had anomalies in our dataset. 


```r
forecast %>% 
  time_decompose(Sales) %>%
  anomalize(remainder, alpha = 0.1, max_anoms = 0.2) %>%
  time_recompose() %>%
  filter(anomaly == 'Yes')
```

```
## frequency = 7 days
```

```
## trend = 30 days
```

```
## # A time tibble: 6 x 10
## # Index: Date
##   Date       observed season trend remainder remainder_l1 remainder_l2 anomaly
##   <date>        <dbl>  <dbl> <dbl>     <dbl>        <dbl>        <dbl> <chr>  
## 1 2019-01-15     950.  -31.1  296.      686.        -509.         623. Yes    
## 2 2019-02-07    1021.   20.8  244.      756.        -509.         623. Yes    
## 3 2019-02-16     927.   40.4  223.      664.        -509.         623. Yes    
## 4 2019-02-19    1034.  -31.1  204.      862.        -509.         623. Yes    
## 5 2019-02-27     698. -118.   172.      644.        -509.         623. Yes    
## 6 2019-03-24     834.  -82.8  257.      660.        -509.         623. Yes    
## # ... with 2 more variables: recomposed_l1 <dbl>, recomposed_l2 <dbl>
```

# 3. CONCLUSIONS

- When the alpha value was set at default i.e. 0.05, we did not detect any anomalies in our dataset.

- After setting the alpha value at 0.1, We determined that there were 4 anomalies in the month of Feruary between 7th and 27th. 


# 4. RECOMMENDATIONS 

- We would recommend that the supermarket reviews their February sales so as to investigate why there were anomalies between the 7th and 27th so as to further understand the trend and seasonality in their sales. 






























