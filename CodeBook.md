# Code Book

> Assuming the dataset is already download and extracted in working directory with name "UCI HAR Dataset"

run_analysis.R file contains in this repository does all the cleaning and transofrmation work. To do the analysis you need to open the run_analysis.R into RStudio and run line by line.

Include packages:
```
library(data.table)
```
> **Note:** data.table package to perform all the loadings and processing as it is recommended to use data.table for fast reading and transofrmation of larget data sets into R.


Read test & train subjects
```
subjects_test <- fread(input = 'UCI HAR Dataset/test/subject_test.txt', sep = ' ')
subjects_train <- fread(input = 'UCI HAR Dataset/train/subject_train.txt', sep = ' ')
```

Read test & train features 
```
features_train <- fread(input = 'UCI HAR Dataset/train/X_train.txt', sep = ' ')
features_test <- fread(input = 'UCI HAR Dataset/test/X_test.txt', sep = ' ')
```

Read test & train class labels 
```
labels_test <- fread(input = 'UCI HAR Dataset/test/y_test.txt', sep = ' ')
labels_train <- fread(input = 'UCI HAR Dataset/train/y_train.txt', sep = ' ')
```

Through dim(<datatable>) you can get the dimensions of each of the above data table. You can run dim over above data tables and make sure the data load is successful.


#### 1 - Merges the training and the test sets to create one data set.

Combine test & train data rows into one
```
subjects <- rbind(subjects_train, subjects_test)
features <- rbind(features_train, features_test)
labels <- rbind(labels_train, labels_test)
```
Here I have combined all the paired up test & train data tables into one file through rbind command. rbind provides you the way to make single data table or frame of two or more data sets. The only criteria required is number of columns must be same in each data table.

Combine features, subjects, and labels columns into one data set
```
data <- cbind(features, subjects, labels)
```
Similarly the features, subjects, and labels are combined into one data table through cbind. cbind requires all the combined tables must have same number of rows and combine them into one data table.

Read the features names and assign to each column:
```
# read the feature_names
feature_names <- fread(input = 'UCI HAR Dataset/features.txt', sep = ' ')

#assign column names in feature_names
colnames(feature_names) <- c('id', 'name')

# assign column names to data set
colnames(data) <- c(feature_names$name, 'subject', 'label')
```
Next thing I have performed is reading features names from features.txt and then assigned the column names to each column. Prior to that I have assigned column names to each column.

===================================


#### 2 - Extracts only the measurements on the mean and standard deviation for each measurement.

```
# get features names; exclude subject and label
measurements_names <- head(colnames(data), n = -2)

# get the map of mean and STD measurements
meanNstdMeasurements_map <- grep('mean|std', measurements_names, ignore.case = TRUE)

# extract mean and STD measurements
meanNstdMeasurements <- data[, c(meanNstdMeasurements_map), with = FALSE]

# assign subject and label to extracted measurements
data <- cbind(meanNstdMeasurements, subjects, labels)

# assign column names to subject and activity
colnames(data)[87:88] <- c('subject', 'label')
```
In this step I have extracted all the features/measurements that have mean and std into their names and pulled out all the respective columns by their indices from 'data' table. The resultant dataset now have only 88 column including the subject and activity.
Here is the glimpse of selected column names:

> [1] "tBodyAcc-mean()-X"                    "tBodyAcc-mean()-Y"                   
 [3] "tBodyAcc-mean()-Z"                    "tBodyAcc-std()-X"                    
 [5] "tBodyAcc-std()-Y"                     "tBodyAcc-std()-Z"                    
 [7] "tGravityAcc-mean()-X"                 "tGravityAcc-mean()-Y"                
 [9] "tGravityAcc-mean()-Z"                 "tGravityAcc-std()-X"                 
[11] "tGravityAcc-std()-Y"                  "tGravityAcc-std()-Z"                 
[13] "tBodyAccJerk-mean()-X"                "tBodyAccJerk-mean()-Y"               
[15] "tBodyAccJerk-mean()-Z"                "tBodyAccJerk-std()-X"                
[17] "tBodyAccJerk-std()-Y"                 "tBodyAccJerk-std()-Z"                
[19] "tBodyGyro-mean()-X"                   "tBodyGyro-mean()-Y"                  
[21] "tBodyGyro-mean()-Z"                   "tBodyGyro-std()-X"                   
[23] "tBodyGyro-std()-Y"                    "tBodyGyro-std()-Z"                   
[25] "tBodyGyroJerk-mean()-X"               "tBodyGyroJerk-mean()-Y"              
[27] "tBodyGyroJerk-mean()-Z"               "tBodyGyroJerk-std()-X"               
[29] "tBodyGyroJerk-std()-Y"                "tBodyGyroJerk-std()-Z"               
[31] "tBodyAccMag-mean()"                   "tBodyAccMag-std()"                   
[33] "tGravityAccMag-mean()"                "tGravityAccMag-std()"                
[35] "tBodyAccJerkMag-mean()"               "tBodyAccJerkMag-std()"               
[37] "tBodyGyroMag-mean()"                  "tBodyGyroMag-std()"                  
[39] "tBodyGyroJerkMag-mean()"              "tBodyGyroJerkMag-std()"              
[41] "fBodyAcc-mean()-X"                    "fBodyAcc-mean()-Y"                   
[43] "fBodyAcc-mean()-Z"                    "fBodyAcc-std()-X"                    
[45] "fBodyAcc-std()-Y"                     "fBodyAcc-std()-Z"                    
[47] "fBodyAcc-meanFreq()-X"                "fBodyAcc-meanFreq()-Y"               
[49] "fBodyAcc-meanFreq()-Z"                "fBodyAccJerk-mean()-X"               
[51] "fBodyAccJerk-mean()-Y"                "fBodyAccJerk-mean()-Z"               
[53] "fBodyAccJerk-std()-X"                 "fBodyAccJerk-std()-Y"                
[55] "fBodyAccJerk-std()-Z"                 "fBodyAccJerk-meanFreq()-X"           
[57] "fBodyAccJerk-meanFreq()-Y"            "fBodyAccJerk-meanFreq()-Z"           
[59] "fBodyGyro-mean()-X"                   "fBodyGyro-mean()-Y"                  
[61] "fBodyGyro-mean()-Z"                   "fBodyGyro-std()-X"                   
[63] "fBodyGyro-std()-Y"                    "fBodyGyro-std()-Z"                   
[65] "fBodyGyro-meanFreq()-X"               "fBodyGyro-meanFreq()-Y"              
[67] "fBodyGyro-meanFreq()-Z"               "fBodyAccMag-mean()"                  
[69] "fBodyAccMag-std()"                    "fBodyAccMag-meanFreq()"              
[71] "fBodyBodyAccJerkMag-mean()"           "fBodyBodyAccJerkMag-std()"           
[73] "fBodyBodyAccJerkMag-meanFreq()"       "fBodyBodyGyroMag-mean()"             
[75] "fBodyBodyGyroMag-std()"               "fBodyBodyGyroMag-meanFreq()"         
[77] "fBodyBodyGyroJerkMag-mean()"          "fBodyBodyGyroJerkMag-std()"          
[79] "fBodyBodyGyroJerkMag-meanFreq()"      "angle(tBodyAccMean,gravity)"         
[81] "angle(tBodyAccJerkMean),gravityMean)" "angle(tBodyGyroMean,gravityMean)"    
[83] "angle(tBodyGyroJerkMean,gravityMean)" "angle(X,gravityMean)"                
[85] "angle(Y,gravityMean)"                 "angle(Z,gravityMean)"                
[87] "subject"                              "label"        

===================================


#### 3 - Uses descriptive activity names to name the activities in the data set

```
# read activity labels
activities <- fread(input = 'UCI HAR Dataset/activity_labels.txt', sep = ' ')

# assign names to activities columns
colnames(activities) <- c('label', 'activity')

# assign names to activities columns
colnames(labels) <- c('label')

# merge activity & labels into activity_labels (over label column)
activity_labels <- merge(x = labels, y = activities, by = 'label', all = TRUE)

# column bind to data; remove redundant label columns
data <- cbind(data, activity_labels$activity)

# give new column a descriptive name
colnames(data)[89] <- 'activity'
```
This part of assignment pulls activity labels from the file and associate them using class label id to the actual row. There are 6 activity labels are defined in file as follows:
> [1] "WALKING"            "WALKING_UPSTAIRS"   "WALKING_DOWNSTAIRS" "SITTING"            "STANDING"          
[6] "LAYING" 

They have been assigned to the filtered data set in the last step.

===================================


#### 4 - Appropriately labels the data set with descriptive variable names.

Pull the names to be processed
```
names <- colnames(data)
```

Replace prefix t with Time
```
names <- gsub('^t', 'Time', names)
```

Replace tBody with TimeBody
```
names <- gsub("tBody", "TimeBody", names)
```

Replace prefix f with Frequency
```
names <- gsub("^f", "Frequency", names)
```

Replace Acc/Gyro/Mag abbreviations with full word
```
names <- gsub("Acc", "Accelerometer", names)
names <- gsub("Gyro", "Gyroscope", names)
names <- gsub("Mag", "Magnitude", names)
```

Change first letter to Caps
```
names <- gsub("angle", "Angle", names)
names <- gsub("gravity", "Gravity", names)
```

Assign processed names back to colnames(data)
```
colnames(data) <- names
```

===================================


#### 5 - From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject.

Aggregate by subject & activity
```
tidyset <- aggregate(. ~subject + activity, data = data, FUN = mean)
```

Write in file as instructed with row.names = FALSE
```
write.table(x = tidyset, file = 'tidyset.txt', row.names = FALSE)
```

===================================
