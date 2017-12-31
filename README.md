---
title: "Peer Graded Assignment"
author: "Caio Guimaraes"
date: "30 de dezembro de 2017"
output: html_document
---

```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = TRUE)
```


## Load Libraries and Import Data 

First we must load all necessary packages and import data from working directory
```{r loadPackages, warning=FALSE, message=FALSE}
library(readr)
library(caret)
```


```{r warning=FALSE, message=FALSE}
# Read pml-testing and pml-training csv
testing <- read_csv("~/CAIO GUIMARAES/10. GAMMA/10. Coursera/Practical Machine Learning/Week 4/pml-testing.csv")
training <- read.csv("~/CAIO GUIMARAES/10. GAMMA/10. Coursera/Practical Machine Learning/Week 4/pml-training.csv")
```


## Data preparation
Next, we must clean training and data set by removing NA's and blank columns

```{r warning=FALSE, message=FALSE}
#Remove NA columns
training_clean <- training[, colSums(is.na(training)) == 0] 
testing_clean <- testing[,colSums(is.na(testing))==0]

#Remove non-predictive columns (including blank columns in training set)
training_clean <- training_clean[,-c(1:7,12:20,43:48,52:60,74:82)]
testing_clean <- testing_clean[,-c(1:7)]

```


## Cross-Validation and Running the Model

We will use the caret package to run a Random Forest model through the data set. However, first, we will split the training data set into subsets for training and validation. Although RF will use bootstrapping to attempt to avoid over-fitting, we will take this additional precaution. 

```{r warning=FALSE, message=FALSE, eval=FALSE}
#Create Training / Validation data partition within training set

inTrain <- createDataPartition(training_clean$classe, p = 0.7, list = FALSE)
train <- training_clean[inTrain, ]
validation <- training_clean[-inTrain, ]

#Run Random Forest Model
mod_rf <- train(classe ~ ., data = train, method = "rf")

#Print Random Forest Model
print(mod_rf)

```

We will check for accuracy within our validation subset of our training set by plotting the confusion matrix and statistics.

```{r warning=FALSE, message=FALSE}
prediction <- predict(mod_rf, validation)
confusionMatrix(prediction,validation$classe)


```

We see that the resulting model has a very good accuracy out-of-sampl e(validation subset), and therfore a low out-of-sample error. Accuracy = 0.9937, out-of-sample error = 1 - 0.9937 = 0.0063

The trade-off we have here is that the tuned model is difficult to interpret.


## Predictions on the test set

```{r warning=FALSE, message=FALSE}
testing_clean
final_prediction <- predict(mod_rf, testing_clean)
final_prediction

cbind(testing_clean,final_prediction)
```
