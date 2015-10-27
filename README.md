# Rossmann
---
title: "Rossmann"
output: html_document
---

Forecast sales using store, promotion, and competitor data

Rossmann operates over 3,000 drug stores in 7 European countries. Currently, Rossmann store managers are tasked with predicting their daily sales for up to six weeks in advance. Store sales are influenced by many factors, including promotions, competition, school and state holidays, seasonality, and locality. With thousands of individual managers predicting sales based on their unique circumstances, the accuracy of results can be quite varied.

In their first Kaggle competition, Rossmann is challenging you to predict 6 weeks of daily sales for 1,115 stores located across Germany. Reliable sales forecasts enable store managers to create effective staff schedules that increase productivity and motivation. By helping Rossmann create a robust prediction model, you will help store managers stay focused on what’s most important to them: their customers and their teams! 

```{r loading/libraries}
setwd("~/Desktop/Kaggle/Rosmann Store Sales")

library(corrgram)

train <- read.csv("train.csv", nrows = 100000)
test <- read.csv ("test.csv",nrows = 100000)

head(train)
names(train)
```

### Exploring the data

```{r exploring}

head(train)
names(train)
summary(train)

# plot(train)

```

As expected there is a relationship between the number of costumers and sates

Let's calculate the correlation matrix
```{r corr}
library(stats)
str(train)

c <-cor(subset(train, select = - c(Date, StateHoliday )))

corrgram(c)

str(train)
```

In additiion to number of costumers Promo has a relatively high correlation with sales as well. 



### Regression

Multiple Linear Regression

```{r MLR}
# remove DayOfWeek before the linear regression
lm.fit <- lm(Sales ~., data = subset(train, 
                          select = -c(DayOfWeek) ))
summary(lm.fit)


```

Let's remove some variables as they effect the overall fit but dont seem important for the end result. 
Store-a unique Id for each store
Open - an indicator for whether the store was open: 0 = closed, 1 = open
(we are interested in predicting sales for when the stores are open)
Date - we might add this later in a different format. There might be a correlation

```{r MLR 2}

lm.fit <- lm(Sales ~., data = subset(train, 
                          select = -c(Store, Date,  Open) ))
summary(lm.fit)
```







```{r tree}
library(tree)
tree.train <- tree(Sales ~ ., subset(train, 
                          select = -c(Store, Date,  Open) ))
plot(tree.train)
text(tree.train)

library(randomForest)
bag.train <- randomForest(Sales ~. ,subset(train, 
                          select = -c(Store, Date,  Open) ))

bag.train
importance(bag.train)


```


