# Clean Data From Several Raw Data Sets
In this project, we are given by a bunch of data sets describing activity features of human from Sumsung.
The sets are connected by some variables. To get some summary from the raw data, we need to clean the data first.

## Getting Started
Include useful packages from R. E.g,

```
library(plyr)
library(dplyr)
```
Then read useful files into R and rename them the way you can easily remember.  

```
featureNames <- read.table("features.txt")
activityNames <- read.table("activity_labels.txt")
trainData <- read.table("X_train.txt")
testData <- read.table("X_test.txt")
trainPeople <- read.table("subject_train.txt")
testPeople <- read.table("subject_test.txt")
trainActivity<-read.table("y_train.txt")
testActivity<-read.table("y_test.txt")
```


## Question 1: Merges the training and the test sets to create one data set.
### Rename column names before merging. Make sure that the column names in training set and testing set
are same since they will be combined together.
```
colnames(trainPeople) <- "People"
colnames(testPeople) <- "People"
colnames(trainActivity) <- "Activity"
colnames(testActivity) <- "Activity"
```
Notice that there is only 1 column in above variables, so we can rename their column name by above code.

### Merge train data sets with other train information sets by combining their columns.
```
Train <- cbind(trainPeople,trainActivity,trainData)
```

### Merge test data sets with other test information sets by combining their columns.
```
Test <- cbind(testPeople,testActivity,testData)
```

### Now merge Train and Test by combining their rows to get the whole data set.
```
DATA <- rbind(Train,Test)
```
Now DATA is the data set combining all useful information we want.
## Question 2: Extracts only the measurements on the mean and standard deviation for each measurement.
```
Measure <- data.frame(meanV=sapply(DATA[,3:563],mean),sdV=sapply(DATA[,3:563],sd))
```
Notice that the measurements are in the column 3 to 563. 

## Question3: Uses descriptive activity names to name the activities in the data set.
```
for (i in 1:nrow(DATA)) 
{
   for (j in 1:6) {
        if (DATA$Activity[i]==activityNames[[1]][j])
        { DATA$Activity[i]<-as.character(activityNames[[2]][j])}
}
}
```
loop i through the Activity column in DATA and loop j through the first column (number 1 through 6)
in the activityNames file and rename the DATA with specific activity names (second column in activityNames.)

## Question4: Appropriately labels the data set with descriptive variable names.
```
colnames(DATA)[3:563]<-as.character(featureNames[[2]])
```
Rename colnames in DATA by feature names in featureNames.

## Question5: Q5: From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each person.
```
colName <-colnames(DATA)[3:563]
colNameData2 <- sapply("Mean",paste,colName,sep="_")
colnames(DATA)[3:563] <- colnames(Test)[3:563]
byPeopleActivity <- group_by( DATA, People, Activity)
DATA2 <- summarise_each(byPeopleActivity,funs(mean))
colnames(DATA2)[3:563] <- colNameData2
write.table(DATA2,"DATA2.txt",row.names=F)
```

Notice in this question, it is asked to group by two variables (in class we only see group by one variable).
Also notice that it is asked to summarize more than one (561) variables (in class we only see summarize one variable).
I use summarise_each function to handle summary more than one variable, but it gives me error if I use colnames in DATA
But if I rename the columns by V1 to V561, it works. That's why I rename them. After collecting the means we want (stored
in DATA2 file), I give new descriptive column names to DATA2 (using "paste") and write it to "DATA2.txt" file.








