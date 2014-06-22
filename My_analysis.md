PML Write Up

========================================================
NOTE : I had to make my .md file typing and copy pasting data from my logs, If you are reviewing then I request you to  consider this since my knit package was not performing as expected and I am working in Linux, so Rstudio and other options are not available currently.


###Introduction

Strating with the very aim of this project i.e. "To predict the manner in which they did the exercise", we must first understand the situation. Human Activity Recognition focus on discriminating between activities, for such a discrimination we must have different quality sets to compare the activities. As we can see these classes (A,B,C,D,E) explains the variation in execution of tested activities, so we have "classe" as our training variable.

###Flow chart

Clean Data -&gt; Preprocess Data-&gt; Partition Data -&gt; Train Data -&gt; Test Data

###Clean Data

Once we have the data we should analyse different variables in data and based on there stats must rule out few of the columns. We start that by removing zero covariates. We chart out those columns and then create a new data set from old with removed charted data. Then we control the columns with NAs, we simply replace them by 0 rather then fixing them with means beacuse later would smoothen the effect of that variable which may be not correct, for example a dummy variable. So we confined the data with replacing NAs.

###Preprocess Data

With above data we now do Principal Component Analysis which will help us reduce the predictors and will help in ruling out columns majority zeros depending on the preProcess. So we need no to worry about those columns and we eventually get a reduced data set

###Partition Data

Now we have a tidy data set which can be partioned in a standard way. Creating a 70-30 partition we proceed with Training and Testing data to train the model.

###Train Data

Since the data we have is not exactly in linear order we must use a training method that incorporate the variation in data without compromising the fit, such traning methods are neural networks, random forests etc. We will proceed with random forest since random forest is one of the most widely used training algorithms. Then we calculate the predictions from our model for the match back.

###Test Data

We calculate the confusionMatrix for our predictions and the varaible "classe" in our test data partitioned earlier. Below the the outcome.

###Out of Sammple error

Since we are relying on PCA we may have overlooked a few columns due to which current algorithm shows a overfit and neglects class"E", however looking at the source data and research paper there are only 17 major variables which can predict the outcome. So the overfit does not allow class"E" to stay in model and therefore it is not available in prediction as well causing error while testing a case.

###Output

Predictors :41

Confusion Matrix and Statistics

          Reference

Prediction    A    B    C    D

         A 2218   22    3    1

         B   11 1484   17    5

         C    2   11 1346   14

         D    1    1    2  902

Overall Statistics

                                         

               Accuracy : 0.9851         

                 95% CI : (0.9817, 0.988)

    No Information Rate : 0.3695         

    P-Value [Acc &gt; NIR] : &lt; 2.2e-16      

                                         

                  Kappa : 0.9795         

 Mcnemar's Test P-Value : 0.009972       

Statistics by Class:

                     Class: A Class: B Class: C Class: D

Sensitivity            0.9937   0.9776   0.9839   0.9783

Specificity            0.9932   0.9927   0.9942   0.9992

Pos Pred Value         0.9884   0.9782   0.9803   0.9956

Neg Pred Value         0.9963   0.9925   0.9953   0.9961

Prevalence             0.3695   0.2513   0.2265   0.1526

Detection Rate         0.3672   0.2457   0.2228   0.1493

Detection Prevalence   0.3715   0.2512   0.2273   0.1500

Balanced Accuracy      0.9934   0.9852   0.9891   0.9888

```{r}

url&lt;-"http://d396qusza40orc.cloudfront.net/predmachlearn/pml-training.csv"

data&lt;-read.csv(url)

library(caret) 

library(kernlab)

nsv&lt;-nearZeroVar(data,saveMetrics=TRUE)

dim(nsv);

new&lt;-nsv[grep("FALSE",nsv$nzv,ignore.case=T),]

length(row.names(new))

length(row.names(nsv))

goodvar&lt;-row.names(new) 

newdata&lt;-data[,c(goodvar)]

colnames(newdata)

dim(newdata)

dim(data)

set.seed(15625)

inTrain&lt;-createDataPartition(y=newdata$classe,p=0.6,list=FALSE)

 training=newdata[inTrain,]

 testing=newdata[-inTrain,]

 training$classe=factor(training$classe)

 trcontrol&lt;-traincontrol(method="cv",number=5)

modelFit&lt;-train(classe~., method="rf",data=training,preProcess="pca",trcontrol)

predictions&lt;-predict(modelFit,newdata=testing)

confusionMatrix(predictions,testing$classe)

```