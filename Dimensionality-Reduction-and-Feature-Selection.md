---
title: "Dimensionality Reduction and Feature Selection Project"
author: "Mr. Mutai"
date: "7/20/2021"
output: 
  html_document: 
    keep_md: yes
---




# ASSESSMENT QUESTION 

You are a Data analyst at Carrefour Kenya and are currently undertaking a project that will inform the marketing department on the most relevant marketing strategies that will result in the highest no. of sales (total price including tax). Your project has been divided into four parts where you'll explore a recent marketing dataset by performing various unsupervised learning techniques and later providing recommendations based on your insights.

## PART 1 : Dimensionality Reduction

This section of the project entails reducing your dataset to a low dimensional dataset using the t-SNE algorithm or PCA. You will be required to perform your analysis and provide insights gained from your analysis.

# 1. DATA SOURCING

First we load the dataset into our environment.


```r
supermarket <- read.csv("C:/Users/hp/Downloads/Supermarket_Dataset_1 - Sales Data.csv")
```

View the dataset in our environment. 


```r
View(supermarket)
```

# 2. PREVIEWING THE DATA

Preview the top 5 records in our dataset. 


```r
head(supermarket)
```

```
##    Invoice.ID Branch Customer.type Gender           Product.line Unit.price
## 1 750-67-8428      A        Member Female      Health and beauty      74.69
## 2 226-31-3081      C        Normal Female Electronic accessories      15.28
## 3 631-41-3108      A        Normal   Male     Home and lifestyle      46.33
## 4 123-19-1176      A        Member   Male      Health and beauty      58.22
## 5 373-73-7910      A        Normal   Male      Sports and travel      86.31
## 6 699-14-3026      C        Normal   Male Electronic accessories      85.39
##   Quantity     Tax      Date  Time     Payment   cogs gross.margin.percentage
## 1        7 26.1415  1/5/2019 13:08     Ewallet 522.83                4.761905
## 2        5  3.8200  3/8/2019 10:29        Cash  76.40                4.761905
## 3        7 16.2155  3/3/2019 13:23 Credit card 324.31                4.761905
## 4        8 23.2880 1/27/2019 20:33     Ewallet 465.76                4.761905
## 5        7 30.2085  2/8/2019 10:37     Ewallet 604.17                4.761905
## 6        7 29.8865 3/25/2019 18:30     Ewallet 597.73                4.761905
##   gross.income Rating    Total
## 1      26.1415    9.1 548.9715
## 2       3.8200    9.6  80.2200
## 3      16.2155    7.4 340.5255
## 4      23.2880    8.4 489.0480
## 5      30.2085    5.3 634.3785
## 6      29.8865    4.1 627.6165
```

Preview the bottom 5 records in our dataset. 


```r
tail(supermarket)
```

```
##       Invoice.ID Branch Customer.type Gender           Product.line Unit.price
## 995  652-49-6720      C        Member Female Electronic accessories      60.95
## 996  233-67-5758      C        Normal   Male      Health and beauty      40.35
## 997  303-96-2227      B        Normal Female     Home and lifestyle      97.38
## 998  727-02-1313      A        Member   Male     Food and beverages      31.84
## 999  347-56-2442      A        Normal   Male     Home and lifestyle      65.82
## 1000 849-09-3807      A        Member Female    Fashion accessories      88.34
##      Quantity     Tax      Date  Time Payment   cogs gross.margin.percentage
## 995         1  3.0475 2/18/2019 11:40 Ewallet  60.95                4.761905
## 996         1  2.0175 1/29/2019 13:46 Ewallet  40.35                4.761905
## 997        10 48.6900  3/2/2019 17:16 Ewallet 973.80                4.761905
## 998         1  1.5920  2/9/2019 13:22    Cash  31.84                4.761905
## 999         1  3.2910 2/22/2019 15:33    Cash  65.82                4.761905
## 1000        7 30.9190 2/18/2019 13:28    Cash 618.38                4.761905
##      gross.income Rating     Total
## 995        3.0475    5.9   63.9975
## 996        2.0175    6.2   42.3675
## 997       48.6900    4.4 1022.4900
## 998        1.5920    7.7   33.4320
## 999        3.2910    4.1   69.1110
## 1000      30.9190    6.6  649.2990
```

Check for null/missing values. 


```r
colSums(is.na(supermarket))
```

```
##              Invoice.ID                  Branch           Customer.type 
##                       0                       0                       0 
##                  Gender            Product.line              Unit.price 
##                       0                       0                       0 
##                Quantity                     Tax                    Date 
##                       0                       0                       0 
##                    Time                 Payment                    cogs 
##                       0                       0                       0 
## gross.margin.percentage            gross.income                  Rating 
##                       0                       0                       0 
##                   Total 
##                       0
```

- We can conclude that we have no missing values in our dataset. 

Check for duplicate values. 


```r
duplicated_rows <- supermarket[duplicated(supermarket),]
duplicated_rows
```

```
##  [1] Invoice.ID              Branch                  Customer.type          
##  [4] Gender                  Product.line            Unit.price             
##  [7] Quantity                Tax                     Date                   
## [10] Time                    Payment                 cogs                   
## [13] gross.margin.percentage gross.income            Rating                 
## [16] Total                  
## <0 rows> (or 0-length row.names)
```

- We can also conclude that there are no duplicated values in our dataset. 

Selecting the numerical data from our dataset. 


```r
super <- supermarket[,c(6,7,8,12,14:16)]
head(super)
```

```
##   Unit.price Quantity     Tax   cogs gross.income Rating    Total
## 1      74.69        7 26.1415 522.83      26.1415    9.1 548.9715
## 2      15.28        5  3.8200  76.40       3.8200    9.6  80.2200
## 3      46.33        7 16.2155 324.31      16.2155    7.4 340.5255
## 4      58.22        8 23.2880 465.76      23.2880    8.4 489.0480
## 5      86.31        7 30.2085 604.17      30.2085    5.3 634.3785
## 6      85.39        7 29.8865 597.73      29.8865    4.1 627.6165
```

# 3. EXPLORATORY DATA ANALYSIS

## Univariate Data Analysis


```r
library(dplyr)
```

```
## 
## Attaching package: 'dplyr'
```

```
## The following objects are masked from 'package:stats':
## 
##     filter, lag
```

```
## The following objects are masked from 'package:base':
## 
##     intersect, setdiff, setequal, union
```

```r
# Mean 
supermarket %>% summarise_if(is.numeric, mean)
```

```
##   Unit.price Quantity      Tax     cogs gross.margin.percentage gross.income
## 1   55.67213     5.51 15.37937 307.5874                4.761905     15.37937
##   Rating    Total
## 1 6.9727 322.9667
```


```r
# Median
supermarket %>% summarise_if(is.numeric, median)
```

```
##   Unit.price Quantity    Tax   cogs gross.margin.percentage gross.income Rating
## 1      55.23        5 12.088 241.76                4.761905       12.088      7
##     Total
## 1 253.848
```


```r
# Mode 
getmode <- function(v) {
  uniqv <- unique(v)
  uniqv[which.max(tabulate(match(v, uniqv)))]
}  
supermarket %>% summarise_if(is.numeric, getmode)
```

```
##   Unit.price Quantity   Tax  cogs gross.margin.percentage gross.income Rating
## 1      83.77       10 39.48 789.6                4.761905        39.48      6
##    Total
## 1 829.08
```


```r
# Range
supermarket %>% summarise_if(is.numeric, range)
```

```
##   Unit.price Quantity     Tax   cogs gross.margin.percentage gross.income
## 1      10.08        1  0.5085  10.17                4.761905       0.5085
## 2      99.96       10 49.6500 993.00                4.761905      49.6500
##   Rating     Total
## 1      4   10.6785
## 2     10 1042.6500
```


```r
# Quantiles
supermarket %>% summarise_if(is.numeric, quantile)
```

```
##   Unit.price Quantity       Tax     cogs gross.margin.percentage gross.income
## 1     10.080        1  0.508500  10.1700                4.761905     0.508500
## 2     32.875        3  5.924875 118.4975                4.761905     5.924875
## 3     55.230        5 12.088000 241.7600                4.761905    12.088000
## 4     77.935        8 22.445250 448.9050                4.761905    22.445250
## 5     99.960       10 49.650000 993.0000                4.761905    49.650000
##   Rating     Total
## 1    4.0   10.6785
## 2    5.5  124.4224
## 3    7.0  253.8480
## 4    8.5  471.3502
## 5   10.0 1042.6500
```


```r
# Standard Deviation 
supermarket %>% summarise_if(is.numeric, sd)
```

```
##   Unit.price Quantity      Tax     cogs gross.margin.percentage gross.income
## 1   26.49463 2.923431 11.70883 234.1765                       0     11.70883
##    Rating    Total
## 1 1.71858 245.8853
```


```r
# Variance 
supermarket %>% summarise_if(is.numeric, var)
```

```
##   Unit.price Quantity      Tax     cogs gross.margin.percentage gross.income
## 1   701.9653 8.546446 137.0966 54838.64                       0     137.0966
##     Rating   Total
## 1 2.953518 60459.6
```

### Outliers


```r
boxplot(supermarket$Unit.price)
```

![](Dimensionality-Reduction-and-Feature-Selection_files/figure-html/unnamed-chunk-15-1.png)<!-- -->


```r
boxplot(supermarket$Quantity)
```

![](Dimensionality-Reduction-and-Feature-Selection_files/figure-html/unnamed-chunk-16-1.png)<!-- -->


```r
boxplot(supermarket$Tax)
```

![](Dimensionality-Reduction-and-Feature-Selection_files/figure-html/unnamed-chunk-17-1.png)<!-- -->


```r
boxplot.stats(supermarket$Tax)$out
```

```
## [1] 47.790 49.490 49.650 47.720 48.605 49.260 48.750 48.685 48.690
```


```r
 boxplot(supermarket$cogs)
```

![](Dimensionality-Reduction-and-Feature-Selection_files/figure-html/unnamed-chunk-19-1.png)<!-- -->


```r
boxplot.stats(supermarket$cogs)$out
```

```
## [1] 955.8 989.8 993.0 954.4 972.1 985.2 975.0 973.7 973.8
```


```r
boxplot(supermarket$gross.margin.percentage)
```

![](Dimensionality-Reduction-and-Feature-Selection_files/figure-html/unnamed-chunk-21-1.png)<!-- -->


```r
boxplot(supermarket$gross.income)
```

![](Dimensionality-Reduction-and-Feature-Selection_files/figure-html/unnamed-chunk-22-1.png)<!-- -->


```r
boxplot(supermarket$Rating)
```

![](Dimensionality-Reduction-and-Feature-Selection_files/figure-html/unnamed-chunk-23-1.png)<!-- -->


```r
boxplot(supermarket$Total)
```

![](Dimensionality-Reduction-and-Feature-Selection_files/figure-html/unnamed-chunk-24-1.png)<!-- -->


```r
boxplot(supermarket$Total)$out
```

![](Dimensionality-Reduction-and-Feature-Selection_files/figure-html/unnamed-chunk-25-1.png)<!-- -->

```
## [1] 1003.590 1039.290 1042.650 1002.120 1020.705 1034.460 1023.750 1022.385
## [9] 1022.490
```

# 4. IMPLEMENTING THE SOLUTION 

## PART 1 : Dimensionality Reduction

This section of the project entails reducing your dataset to a low dimensional dataset using the t-SNE algorithm or PCA. You will be required to perform your analysis and provide insights gained from your analysis.

Use the prcomp() function to get the principal components in our dataset. 


```r
super.pca <- prcomp(super, center = TRUE, scale. = TRUE)
summary(super.pca)
```

```
## Importance of components:
##                           PC1    PC2    PC3     PC4       PC5       PC6
## Standard deviation     2.2185 1.0002 0.9939 0.30001 2.981e-16 1.493e-16
## Proportion of Variance 0.7031 0.1429 0.1411 0.01286 0.000e+00 0.000e+00
## Cumulative Proportion  0.7031 0.8460 0.9871 1.00000 1.000e+00 1.000e+00
##                              PC7
## Standard deviation     9.831e-17
## Proportion of Variance 0.000e+00
## Cumulative Proportion  1.000e+00
```

We have obtained 7 principal components and can observe the following ;

- PC1, PC2 and PC3 account for 98.71% percent of the total variation in the dataset which means that majority of the information in our dataset has been captured using just the 3 principal components. 
- PC1 accounts for 70.31% of the total variation in the dataset while PC2 accounts for 14.29% thus we can draw some valuable information and insights by just plotting the 2 principal components.

First we can have a look at our super.pca object with the aim of understanding its dimensions and components. 


```r
str(super.pca)
```

```
## List of 5
##  $ sdev    : num [1:7] 2.22 1.00 9.94e-01 3.00e-01 2.98e-16 ...
##  $ rotation: num [1:7, 1:7] -0.292 -0.325 -0.45 -0.45 -0.45 ...
##   ..- attr(*, "dimnames")=List of 2
##   .. ..$ : chr [1:7] "Unit.price" "Quantity" "Tax" "cogs" ...
##   .. ..$ : chr [1:7] "PC1" "PC2" "PC3" "PC4" ...
##  $ center  : Named num [1:7] 55.67 5.51 15.38 307.59 15.38 ...
##   ..- attr(*, "names")= chr [1:7] "Unit.price" "Quantity" "Tax" "cogs" ...
##  $ scale   : Named num [1:7] 26.49 2.92 11.71 234.18 11.71 ...
##   ..- attr(*, "names")= chr [1:7] "Unit.price" "Quantity" "Tax" "cogs" ...
##  $ x       : num [1:1000, 1:7] -2.005 2.306 -0.186 -1.504 -2.8 ...
##   ..- attr(*, "dimnames")=List of 2
##   .. ..$ : NULL
##   .. ..$ : chr [1:7] "PC1" "PC2" "PC3" "PC4" ...
##  - attr(*, "class")= chr "prcomp"
```

For plotting purposes, we will begin by installing the ggbiplot package which will assist us in visualization of the pca object. 


```r
# Installing our ggbiplot visualisation and devtools packages
# then loading the libraries
library(devtools)
```

```
## Loading required package: usethis
```

```r
install_github("vqv/ggbiplot")
```

```
## Skipping install of 'ggbiplot' from a github remote, the SHA1 (7325e880) has not changed since last install.
##   Use `force = TRUE` to force installation
```

```r
library(ggbiplot)
```

```
## Loading required package: ggplot2
```

```
## Loading required package: plyr
```

```
## ------------------------------------------------------------------------------
```

```
## You have loaded plyr after dplyr - this is likely to cause problems.
## If you need functions from both plyr and dplyr, please load plyr first, then dplyr:
## library(plyr); library(dplyr)
```

```
## ------------------------------------------------------------------------------
```

```
## 
## Attaching package: 'plyr'
```

```
## The following objects are masked from 'package:dplyr':
## 
##     arrange, count, desc, failwith, id, mutate, rename, summarise,
##     summarize
```

```
## Loading required package: scales
```

```
## Loading required package: grid
```

Plot the super.pca object. 


```r
ggbiplot(super.pca)
```

![](Dimensionality-Reduction-and-Feature-Selection_files/figure-html/unnamed-chunk-29-1.png)<!-- -->

Considering that PC1 and PC2 account for 84.6% of the total variation in our dataset, we would get little to no insights on plotting the other pca objects against each other. Therefore based on the above plot we can conclude that the important factors that affect the number of sales in Carrefour are ;

- Unit Price of the items in the supermarkets.
- Gross Income
- Quantity of items purchased
- Rating of the items in the supermarkets. 


## Part 2 : Feature Selection

### i) Filter Method 

First install the required packages and load their libraries. 


```r
library(caret)
```

```
## Loading required package: lattice
```

```r
library(corrplot)
```

```
## corrplot 0.90 loaded
```

Calculate the correlation matrix in our supermarket (numerical variables) dataset


```r
correlationMatrix <- cor(super)
```

In order to find the highly correlated attributes, we can set a cutoff value at 0.75% and find the correlation. 


```r
highlyCorrelated <- findCorrelation(correlationMatrix, cutoff=0.75)
names(super[,highlyCorrelated])
```

```
## [1] "cogs"  "Total" "Tax"
```

- Using the filter method for feature selection, we can conclude that the attributes 'cogs' and 'tax' are highly correlated/redundant and thus should be removed from the subset of features in our dataset. 


```r
# Removing Redundant Features 
# ---
# 
s <- super[-highlyCorrelated]

# Performing our graphical comparison
# ---
# 
par(mfrow = c(1, 2))
corrplot(correlationMatrix, order = "hclust")
```

![](Dimensionality-Reduction-and-Feature-Selection_files/figure-html/unnamed-chunk-33-1.png)<!-- -->


```r
# Without redundant features
corrplot(cor(s), order = "hclust")
```

![](Dimensionality-Reduction-and-Feature-Selection_files/figure-html/unnamed-chunk-34-1.png)<!-- -->

From the correlogram above we can see that there are no highly correlated variables. 

Using the filter method, we can establish that the important features are : 

- Unit Price of the items
- Quantity of items purchased
- Gross Income
- Rating of items

### ii) Embedded Methods

First we load the wskm library. 


```r
library(wskm)
```

```
## Loading required package: latticeExtra
```

```
## 
## Attaching package: 'latticeExtra'
```

```
## The following object is masked from 'package:ggplot2':
## 
##     layer
```

```
## Loading required package: fpc
```

```r
library(cluster)
```


```r
set.seed(2)
model <- ewkm(super, 3, lambda=2, maxiter=1000)
```


```r
# Cluster Plot against 1st 2 principal components
# ---
#
clusplot(super, model$cluster, color=TRUE, shade=TRUE,
         labels=2, lines=1,main='Cluster Analysis for Supermarket')
```

![](Dimensionality-Reduction-and-Feature-Selection_files/figure-html/unnamed-chunk-37-1.png)<!-- -->


```r
# Weights are calculated for each variable and cluster. 
# They are a measure of the relative importance of each variable 
# with regards to the membership of the observations to that cluster. 
# The weights are incorporated into the distance function, 
# typically reducing the distance for more important variables.
# Weights remain stored in the model and we can check them as follows:
# 
round(model$weights*100,2)
```

```
##   Unit.price Quantity Tax cogs gross.income Rating Total
## 1          0        0  50    0           50   0.00     0
## 2          0        0   0    0            0  99.99     0
## 3          0        0  50    0           50   0.00     0
```

# 5. CONCLUSIONS

The important features in our dataset that will bring the highest number of sales are ;

- Unit Price of each item 
- Quantity of the items purchased
- Gross Income 
- Rating of the items 

The first 3 principal components account for 84.6% of the total variation in the dataset thus meaning that majority of the information in our dataset has been captured using just the 3 principal components. 

PC1 accounts for 70.31% of the total variation in the dataset while PC2 accounts for 14.29% thus we can draw some valuable information and insights by just plotting the 2 principal components.


























