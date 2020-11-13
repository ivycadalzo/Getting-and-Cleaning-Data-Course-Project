The R script **run\_analysis.R** involves the following processes:

1.  This part reads the necessary files from the current directory:

``` r
testactivity <- read.table("./Y_test.txt", header=FALSE)
trainactivity <- read.table("./Y_train.txt", header=FALSE)

testsubject <- read.table("./subject_test.txt", header=FALSE)
trainsubject <- read.table("./subject_train.txt", header=FALSE)

testfeature <- read.table("./X_test.txt", header=FALSE)
trainfeature <- read.table("./X_train.txt", header=FALSE)
```

1.  This part merges the training and the test datasets to create one
    dataset using rbind:

``` r
mergeactivity <- rbind(testactivity, trainactivity)
mergesubject <- rbind(testsubject, trainsubject)
mergefeature <- rbind(testfeature, trainfeature)
```

1.  This part assigns column names to the datasets:

``` r
names(mergesubject) <- c("Subject")
names(mergeactivity) <- c("Activity")

features <- read.table("./features.txt", header=FALSE)
names(mergefeature) <- features$V2
```

1.  This part combines all information using cbind:

``` r
datasubset <- cbind(mergesubject, mergeactivity)
alldata <- cbind(mergefeature, datasubset)
```

1.  This part extracts only the measurements on the mean and standard
    deviation for each measurement:

``` r
featuresubset <- features$V2[grep("mean\\(\\)|std\\(\\)", features$V2)]
namesubset <- c(featuresubset, "Subject", "Activity")
finaldata <- subset(alldata,select=namesubset)
```

1.  This part assigns descriptive activity names to the activities in
    the dataset:

``` r
activitylabels <- read.table("./activity_labels.txt", header=FALSE)
finaldata$Activity <- factor(finaldata$Activity, levels=activitylabels$V1, labels=activitylabels$V2)
```

1.  This part appropriately labels the dataset with descriptive variable
    names:

``` r
names(finaldata) <- gsub("^t", "time", names(finaldata))
names(finaldata) <- gsub("^f", "frequency", names(finaldata))
names(finaldata) <- gsub("Acc", "Accelerometer", names(finaldata))
names(finaldata) <- gsub("Gyro", "Gyroscope", names(finaldata))
names(finaldata) <- gsub("Mag", "Magnitude", names(finaldata))
names(finaldata) <- gsub("BodyBody", "Body", names(finaldata))
```

1.  This part creates a second, independent tidy dataset with the
    average of each variable for each activity and each subject:

``` r
library(plyr)
tidydata <- aggregate(. ~Subject + Activity, finaldata, mean)
tidydata <- tidydata[order(tidydata$Subject, tidydata$Activity),]
write.table(tidydata, file="tidydata.txt",row.name=FALSE)
```
