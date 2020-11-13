**DATASET**

The dataset used for this project is the **Human Activity Recognition
Using Smartphones Dataset** by Jorge L. Reyes-Ortiz, Davide Anguita,
Alessandro Ghio, and Luca Oneto of Smartlab-Non Linear Complex Systems
Laboratory. It was downloaded from:

<a href="https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip" class="uri">https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip</a>

The dataset describes the results of the experiments carried out with a
group of 30 volunteers within an age bracket of 19-48 years. Each person
performed six activities (WALKING, WALKING\_UPSTAIRS,
WALKING\_DOWNSTAIRS, SITTING, STANDING, LAYING) wearing a smartphone
(Samsung Galaxy S II) on the waist.

It provides the following information: 1. Triaxial acceleration from the
accelerometer (total acceleration) and the estimated body acceleration.
2. Triaxial Angular velocity from the gyroscope. 3. A 561-feature vector
with time and frequency domain variables. 4. Its activity label. 5. An
identifier of the subject who carried out the experiment.

**VARIABLES**

``` r
testactivity <- read.table("./Y_test.txt", header=FALSE)
trainactivity <- read.table("./Y_train.txt", header=FALSE)

testsubject <- read.table("./subject_test.txt", header=FALSE)
trainsubject <- read.table("./subject_train.txt", header=FALSE)

testfeature <- read.table("./X_test.txt", header=FALSE)
trainfeature <- read.table("./X_train.txt", header=FALSE)
```

1.  **testactivity** is used to store the test labels from the
    *Y\_test.txt* file.
2.  **trainactivity** is used to store the training labels from the
    *Y\_train.txt* file.
3.  **testsubject** and **trainsubject** are used to store the
    information from the *subject\_test.txt* and *subject\_train.txt*
    files respectively, which identify the subject who performed the
    activity for each window sample.
4.  **testfeature** is used to store the test set from the *X\_test.txt*
    file.
5.  **trainfeature** is used to store the training set from the
    *X\_train.txt* file.

``` r
mergeactivity <- rbind(testactivity, trainactivity)
mergesubject <- rbind(testsubject, trainsubject)
mergefeature <- rbind(testfeature, trainfeature)
```

1.  **mergeactivity** is used to combine the *test activity* and
    *trainactivity* stored data using *rbind*.
2.  **mergesubject** is used to combine the *testsubject* and
    *trainsubject* stored data using *rbind*.
3.  **mergefeature** is used to combine the *testfeature* and
    *trainfeature* stored data using *rbind*.

``` r
datasubset <- cbind(mergesubject, mergeactivity)
alldata <- cbind(mergefeature, datasubset)
```

1.  **datasubject** is used to merge the Subject and Activity columns
    from *mergesubject* and *mergeactivity*.
2.  **alldata** is used to merge the *datasubject* containing the
    Subject and Activity with the columns from *mergefeature* data.

``` r
featuresubset <- features$V2[grep("mean\\(\\)|std\\(\\)", features$V2)]
namesubset <- c(featuresubset, "Subject", "Activity")
finaldata <- subset(alldata,select=namesubset)
```

1.  **featuresubset** is used to extract only the measurements on the
    mean and standard deviation for each measurement.
2.  **namesubset** stores the names of the columns of *featuresubset*.
3.  **finaldata** is used to subset *alldata* using the columns
    identified in *namesubset*.

``` r
activitylabels <- read.table("./activity_labels.txt", header=FALSE)
finaldata$Activity <- factor(finaldata$Activity, levels=activitylabels$V1, labels=activitylabels$V2)
```

1.  **activitylabels** is used to store the table from
    *activity\_labels.txt* file, which links the class labels with their
    activity name.

``` r
library(plyr)
tidydata <- aggregate(. ~Subject + Activity, finaldata, mean)
tidydata <- tidydata[order(tidydata$Subject, tidydata$Activity),]
write.table(tidydata, file="tidydata.txt",row.name=FALSE)
```

1.  **tidydata** is used to store the independent tidy dataset by taking
    the average each variable for each activity and each subject.
