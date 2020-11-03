week4 assignment
Description
Using devices such as Jawbone Up, Nike FuelBand, and Fitbit it is now possible to collect a large amount of data about personal activity relatively inexpensively. These type of devices are part of the quantified self movement – a group of enthusiasts who take measurements about themselves regularly to improve their health, to find patterns in their behavior, or because they are tech geeks. One thing that people regularly do is quantify how much of a particular activity they do, but they rarely quantify how well they do it. In this project, your goal will be to use data from accelerometers on the belt, forearm, arm, and dumbell of 6 participants. They were asked to perform barbell lifts correctly and incorrectly in 5 different ways. More information is available from the website here: http://web.archive.org/web/20161224072740/http:/groupware.les.inf.puc-rio.br/har (see the section on the Weight Lifting Exercise Dataset).

set.seed(31120)
library(caret)
library(randomForest)
library(e1071)
Read training dataset
trainDsUrl <- "https://d396qusza40orc.cloudfront.net/predmachlearn/pml-training.csv"
trainDs <- read.csv(trainDsUrl, na.strings = c("", "NA"))
Remove NA columns
colname <- colnames(trainDs)[!colSums(is.na(trainDs)) > 0]
Create new dataset based on the test dataset without first 7 unimportant columns
colname <- colname[8: length(colname)]
cleanedTrainDs <- trainDs[colname]
Partition the data
partitionedTrainDs = createDataPartition(cleanedTrainDs$classe, p = 3/4)[[1]]
trainingDs = cleanedTrainDs[ partitionedTrainDs,]
testingDs = cleanedTrainDs[-partitionedTrainDs,]
Apply random forest, it has the highest accuracy (it takes long time to train data!)
modelRF <- train(classe ~ ., data = trainingDs, method = "rf")
predictedDs <- predict(modelRF, testingDs)
confusionMatrix(table(testingDs$classe, predictedDs))
## Confusion Matrix and Statistics
## 
##    predictedDs
##        A    B    C    D    E
##   A 1395    0    0    0    0
##   B    2  943    4    0    0
##   C    0    5  849    1    0
##   D    0    0   20  784    0
##   E    0    0    0    0  901
## 
## Overall Statistics
##                                           
##                Accuracy : 0.9935          
##                  95% CI : (0.9908, 0.9955)
##     No Information Rate : 0.2849          
##     P-Value [Acc > NIR] : < 2.2e-16       
##                                           
##                   Kappa : 0.9917          
##                                           
##  Mcnemar's Test P-Value : NA              
## 
## Statistics by Class:
## 
##                      Class: A Class: B Class: C Class: D Class: E
## Sensitivity            0.9986   0.9947   0.9725   0.9987   1.0000
## Specificity            1.0000   0.9985   0.9985   0.9951   1.0000
## Pos Pred Value         1.0000   0.9937   0.9930   0.9751   1.0000
## Neg Pred Value         0.9994   0.9987   0.9941   0.9998   1.0000
## Prevalence             0.2849   0.1933   0.1780   0.1601   0.1837
## Detection Rate         0.2845   0.1923   0.1731   0.1599   0.1837
## Detection Prevalence   0.2845   0.1935   0.1743   0.1639   0.1837
## Balanced Accuracy      0.9993   0.9966   0.9855   0.9969   1.0000
Course Project Prediction Quiz Portion
Apply your machine learning algorithm to the 20 test cases available in the test data above and submit your predictions in appropriate format to the Course Project Prediction Quiz for automated grading.

Download 20 test cases
testCasesDsUrl <- "https://d396qusza40orc.cloudfront.net/predmachlearn/pml-testing.csv"
testCaseDs <- read.csv(testCasesDsUrl, na.strings = c("", "NA"))
Predict data for 20 cases
projectQuizTestCasesDs<- predict(modelRF, testCaseDs)
projectQuizTestCasesDs
##  [1] B A B A A E D B A A B C B A E E A B B B
## Levels: A B C D E