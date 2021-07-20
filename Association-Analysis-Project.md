---
title: "Association Analysis Project"
author: "Mr. Mutai"
date: "7/17/2021"
output: 
  html_document: 
    keep_md: yes
---



# ASSESSMENT QUESTION

You are a Data analyst at Carrefour Kenya and are currently undertaking a project that will inform the marketing department on the most relevant marketing strategies that will result in the highest no. of sales (total price including tax). Your project has been divided into four parts where you'll explore a recent marketing dataset by performing various unsupervised learning techniques and later providing recommendations based on your insights.

## Part 3: Association Analysis

This section will require that you create association rules that will allow you to identify relationships between variables in the dataset. You are provided with a separate dataset that comprises groups of items that will be associated with others. Just like in the other sections, you will also be required to provide insights for your analysis.

# 1. DEFINING THE QUESTION

## i) Defining the Specific Data Analytic Question 

To create association rules that will allow you to identify relationships between variables in the dataset.

## ii) Defining the Metric for Success

To be able to establish relationships between items in our dataset using the apriori algorithm.

## iii) Understanding the Context 

Association rules analysis is a technique used to uncover how items are associated to each other. There are three common ways to measure association. Assuming we have two items, X and Y ;

* Measure 1: Support. This says how popular an itemset is, as measured by the proportion of transactions in which an itemset appears. 
* Measure 2: Confidence. This says how likely item Y is purchased when item X is purchased, expressed as {X -> Y}. This is measured by the proportion of transactions with item X, in which item Y also appears.
* Measure 3: Lift. This says how likely item Y is purchased when item X is purchased, while controlling for how popular item Y is. A lift value greater than 1 means that item Y is likely to be bought if item X is bought, while a value less than 1 means that item Y is unlikely to be bought if item X is bought.


# 2. IMPLEMENTING THE SOLUTION 

First we load the arules algorithm that will help us create association rules between items in our dataset. 


```r
# Loading the arules library
#
library(arules)
```

```
## Loading required package: Matrix
```

```
## 
## Attaching package: 'arules'
```

```
## The following objects are masked from 'package:base':
## 
##     abbreviate, write
```

Load the data and then convert it to the class transactions which is the kind of data we will require while working with association rules.


```r
sales <- read.transactions("C:/Users/hp/Downloads/Supermarket_Sales_Dataset II.csv")
```

```
## Warning in asMethod(object): removing duplicated items in transactions
```

View the dataset in our environment.


```r
# View(sales)
```

Check and verify the object's class.


```r
class(sales)
```

```
## [1] "transactions"
## attr(,"package")
## [1] "arules"
```

Preview the first five transactions in our dataset.


```r
# Using the inspect function

inspect(sales[1:5])
```

```
##     items                              
## [1] {cheese,energy,                    
##      drink,tomato,                     
##      fat,                              
##      flour,yams,cottage,               
##      grapes,whole,                     
##      juice,frozen,                     
##      juice,low,                        
##      mix,green,                        
##      oil,                              
##      shrimp,almonds,avocado,vegetables,
##      smoothie,spinach,olive,           
##      tea,honey,salad,mineral,          
##      water,salmon,antioxydant,         
##      weat,                             
##      yogurt,green}                     
## [2] {burgers,meatballs,eggs}           
## [3] {chutney}                          
## [4] {turkey,avocado}                   
## [5] {bar,whole,                        
##      mineral,                          
##      rice,green,                       
##      tea,                              
##      water,milk,energy,                
##      wheat}
```

Preview the first 10 items that make up our dataset


```r
items<-as.data.frame(itemLabels(sales))
colnames(items) <- "Item"
head(items, 10)
```

```
##                                      Item
## 1                                       &
## 2                             accessories
## 3                 accessories,antioxydant
## 4             accessories,champagne,fresh
## 5           accessories,champagne,protein
## 6                   accessories,chocolate
## 7  accessories,chocolate,champagne,frozen
## 8            accessories,chocolate,frozen
## 9               accessories,chocolate,low
## 10       accessories,chocolate,pasta,salt
```

Generating a summary of the sales dataset to get a sense of the most purchased items. 


```r
summary(sales)
```

```
## transactions as itemMatrix in sparse format with
##  7501 rows (elements/itemsets/transactions) and
##  5729 columns (items) and a density of 0.0005421748 
## 
## most frequent items:
##     tea   wheat mineral     fat  yogurt (Other) 
##     803     645     577     574     543   20157 
## 
## element (itemset/transaction) length distribution:
## sizes
##    1    2    3    4    5    6    7    8    9   10   11   12   13   15   16 
## 1603 2007 1382  942  651  407  228  151   70   39   13    5    1    1    1 
## 
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
##   1.000   2.000   3.000   3.106   4.000  16.000 
## 
## includes extended item information - examples:
##                    labels
## 1                       &
## 2             accessories
## 3 accessories,antioxydant
```

- From the summary above, we can tell the most purchased item in our dataset is tea which was in 803 transactions, followed by wheat, mineral water, fat and yoghurt. 

 Displaying top 10 most common items in the sales dataset and the items whose relative importance is at least 5%.


```r
par(mfrow = c(1, 2))

# plot the frequency of items
itemFrequencyPlot(sales, topN = 10, col="darkgreen")
itemFrequencyPlot(sales, support = 0.05, col="darkred")
```

![](Association-Analysis-Project_files/figure-html/unnamed-chunk-8-1.png)<!-- -->

- From the plot on the right, we can tell that fat, fries, frozen, mineral water, tea, wheat and yoghurt are the items that have a relative importance(support) of 5% and above in our sales dataset.

Building a model based on association rules using the apriori function. It is possible to gauge the sensitivity of our model by tuning the two parameters i.e support and confidence level. In this model, we decided to set the minimum support at 0.1% and the confidence level at 80%. 


```r
# First case, We use Min Support as 0.001 and confidence as 0.8

rules <- apriori (sales, parameter = list(supp = 0.001, conf = 0.8))
```

```
## Apriori
## 
## Parameter specification:
##  confidence minval smax arem  aval originalSupport maxtime support minlen
##         0.8    0.1    1 none FALSE            TRUE       5   0.001      1
##  maxlen target  ext
##      10  rules TRUE
## 
## Algorithmic control:
##  filter tree heap memopt load sort verbose
##     0.1 TRUE TRUE  FALSE TRUE    2    TRUE
## 
## Absolute minimum support count: 7 
## 
## set item appearances ...[0 item(s)] done [0.00s].
## set transactions ...[5729 item(s), 7501 transaction(s)] done [0.02s].
## sorting and recoding items ... [354 item(s)] done [0.00s].
## creating transaction tree ... done [0.00s].
## checking subsets of size 1 2 3 4 done [0.00s].
## writing ... [271 rule(s)] done [0.00s].
## creating S4 object  ... done [0.00s].
```

```r
rules
```

```
## set of 271 rules
```

- Using the minimum support as 0.1% and confidence as 80%, we were able to attain a set of 271 rules. 

Preview the rules built in our first model. 


```r
# First 10 model rules
inspect(rules[1:10])
```

```
##      lhs                                rhs      support     confidence
## [1]  {cookies,low}                   => {yogurt} 0.001066524 1.0       
## [2]  {cookies,low}                   => {fat}    0.001066524 1.0       
## [3]  {extra}                         => {dark}   0.001066524 1.0       
## [4]  {burgers,whole}                 => {wheat}  0.001199840 1.0       
## [5]  {fries,escalope,pasta,mushroom} => {cream}  0.001066524 1.0       
## [6]  {fries,cookies,green}           => {tea}    0.001333156 1.0       
## [7]  {shrimp,whole}                  => {wheat}  0.001066524 1.0       
## [8]  {rice,cake}                     => {wheat}  0.001333156 1.0       
## [9]  {tomatoes,whole}                => {wheat}  0.001066524 0.8       
## [10] {rice,chocolate}                => {wheat}  0.001199840 0.9       
##      coverage    lift      count
## [1]  0.001066524 13.813996  8   
## [2]  0.001066524 13.067944  8   
## [3]  0.001066524 83.344444  8   
## [4]  0.001199840 11.629457  9   
## [5]  0.001066524 47.777070  8   
## [6]  0.001333156  9.341220 10   
## [7]  0.001066524 11.629457  8   
## [8]  0.001333156 11.629457 10   
## [9]  0.001333156  9.303566  8   
## [10] 0.001333156 10.466512  9
```

From the preview above, we can conclude ; 

- If a customer buys cookies, they are 100% likely to also buy yoghurt as this was observed in 8 transactions within our dataset.  
- If a customer buys rice, they are 100% likely to also buy wheat as this was observed in 10 transactions within our dataset. 

Ordering these rules by criteria such as the level of confidence, lift or support can also help us understand better the relationship between the items in our dataset and help us make more calculated decisions such as aisle placement for better sales.


```r
# Lift value

rules <-sort(rules, by= "lift", decreasing=TRUE)
inspect(rules[1:10])
```

```
##      lhs                 rhs            support     confidence coverage   
## [1]  {&,fresh}        => {tuna,herb}    0.001199840 0.90       0.001333156
## [2]  {parmesan,wheat} => {cheese,whole} 0.001333156 1.00       0.001333156
## [3]  {fat,tea}        => {yogurt,green} 0.004666045 1.00       0.004666045
## [4]  {&,grated}       => {cheese,herb}  0.004666045 1.00       0.004666045
## [5]  {bar,hand}       => {protein}      0.001199840 1.00       0.001199840
## [6]  {flour,green}    => {weat}         0.001199840 1.00       0.001199840
## [7]  {flour}          => {weat}         0.001466471 1.00       0.001466471
## [8]  {flour,french}   => {weat}         0.002133049 1.00       0.002133049
## [9]  {candy}          => {bars}         0.003066258 0.92       0.003332889
## [10] {extra}          => {dark}         0.001066524 1.00       0.001066524
##      lift      count
## [1]  613.71818  9   
## [2]  258.65517 10   
## [3]  197.39474 35   
## [4]  153.08163 35   
## [5]  144.25000  9   
## [6]  107.15714  9   
## [7]  107.15714 11   
## [8]  107.15714 16   
## [9]  100.01333 23   
## [10]  83.34444  8
```

From the above preview, we can conclude the following using the lift value :

- If a customer buys fat and tea, they are 100% likely to also buy yoghurt and greens as this was observed in 35 transactions within our dataset.  
- If a customer buys parmesan and wheat, they are 100% likely to also buy cheese as this was observed in 10 transactions within our dataset.

# 3. CONCLUSIONS

- Tea is the most purchased item in our dataset followed by wheat, mineral water, fat and yoghurt. 
- If a customer buys cookies, we are 100% confident that they will also buy yoghurt as this was observed in 8 transactions within our dataset.  
- If a customer buys rice, we are 100% confident that they will also buy wheat as this was observed in 10 transactions within our dataset.
- If a customer buys fat and tea, they are 100% likely to also buy yoghurt and greens as this was observed in 35 transactions within our dataset.  
- If a customer buys parmesan and wheat, they are 100% likely to also buy cheese as this was observed in 10 transactions within our dataset.


# 4. RECOMMENDATIONS 

- We recommend that the supermarket should stock up on items such as tea, wheat, mineral water, fat and yoghurt as they were the most purchased and thus will guarantee the highest number of sales. 
- We recommend that the supermarket should consider placing rice and wheat in similar or neighbouring aisles as we are 100% confident that a customer would purchase both items and thus this would reduce the time they take looking for the items separately. 
- We recommend that cookies and youghurt should be placed in neighbouring or similar aisles as a customer would be 100% likely to purchase both at the same time thus increasing the number of sales in the supermarket.










