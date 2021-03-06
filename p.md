## Introduction

Using devices such as Jawbone Up, Nike FuelBand, and Fitbit it is now possible to collect a large amount of data about personal activity relatively inexpensively. These type of devices are part of the quantified self movement – a group of enthusiasts who take measurements about themselves regularly to improve their health, to find patterns in their behavior, or because they are tech geeks. One thing that people regularly do is quantify how much of a particular activity they do, but they rarely quantify how well they do it. In this project, your goal will be to use data from accelerometers on the belt, forearm, arm, and dumbell of 6 participants. They were asked to perform barbell lifts correctly and incorrectly in 5 different ways. More information is available from the website here: http://groupware.les.inf.puc-rio.br/har

## Data prepare

Prepare and clean dataset

```
library(caret)
library(glmnet)
library(corrplot)
library(randomForest)
d_train <- read.csv("pml-training.csv", na.strings = c("NA", ""))
d_test <- read.csv("pml-testing.csv", na.strings = c("NA", ""))

dim(d_train)
str(d_train)

```

When there are a lot of NAs in a column, take as incomplete and do not use it in model = Select the variables with NA percentage < 40 %.

```
d_train <- d_train[, colSums(!is.na(d_train)) > nrow(d_train)*0.6]
dim(d_train)
```

- First seven.

```
d_train <- d_train[,8:ncol(d_train)]
dim(d_train)
```

```
a <- cor(d_train[,-ncol(d_train)])
corrplot(a, type="upper", method="circle")
```

Good correlation range so build model.

Classification problem so use decision tree. 70% train and 30% test

```
it <- createDataPartition(y=d_train$classe, p=0.7, list=FALSE)
training <- d_train[it,]
testing <- d_train[-it,]

f <- train(classe~., method="rf", data=training)
```

Test performance

```
a <- predict(f, newdata=testing)
confusionMatrix(a, testing$classe)
```

Model performance on the pml-testing.csv dataset and txt files

```
x <- predict(f, newdata=d_test)
```
```
write = function(x){
  for(i in 1:length(x)){
    n = paste0("prob_",i,".txt")
    write.table(x[i],file=n,quote=FALSE,row.names=FALSE,col.names=FALSE)
  }
}

write(x)
```
