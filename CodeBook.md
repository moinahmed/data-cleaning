# Code Book

Assuming the dataset is already download and extracted in working directory with name "UCI HAR Dataset"


Include packages
library(data.table)


Read test & train subjects
subjects_test <- fread(input = 'UCI HAR Dataset/test/subject_test.txt', sep = ' ')
subjects_train <- fread(input = 'UCI HAR Dataset/train/subject_train.txt', sep = ' ')

Read test & train features 
features_train <- fread(input = 'UCI HAR Dataset/train/X_train.txt', sep = ' ')
features_test <- fread(input = 'UCI HAR Dataset/test/X_test.txt', sep = ' ')

Read test & train class labels 
labels_test <- fread(input = 'UCI HAR Dataset/test/y_test.txt', sep = ' ')
labels_train <- fread(input = 'UCI HAR Dataset/train/y_train.txt', sep = ' ')



### 1 - Merges the training and the test sets to create one data set.

Combine test & train data rows into one
subjects <- rbind(subjects_train, subjects_test)
features <- rbind(features_train, features_test)
labels <- rbind(labels_train, labels_test)

Combine features, subjects, and labels columns into one data set
data <- cbind(features, subjects, labels)

Read the features names
feature_names <- fread(input = 'UCI HAR Dataset/features.txt', sep = ' ')

Assign column names in feature_names
colnames(feature_names) <- c('id', 'name')

Assign column names to data set
colnames(data) <- c(feature_names$name, 'subject', 'label')

===================================


### 2 - Extracts only the measurements on the mean and standard deviation for each measurement.

Get features names; exclude subject and label
measurements_names <- head(colnames(data), n = -2)

Get the map of mean and STD measurements
meanNstdMeasurements_map <- grep('mean|std', measurements_names, ignore.case = TRUE)

Extract mean and STD measurements
meanNstdMeasurements <- data[, c(meanNstdMeasurements_map), with = FALSE]

Assign subject and label to extracted measurements
data <- cbind(meanNstdMeasurements, subjects, labels)

Assign column names to subject and activity
colnames(data)[87:88] <- c('subject', 'label')

===================================


### 3 - Uses descriptive activity names to name the activities in the data set

Read activity labels
activities <- fread(input = 'UCI HAR Dataset/activity_labels.txt', sep = ' ')

Assign names to activities columns
colnames(activities) <- c('label', 'activity')

Assign names to activities columns
colnames(labels) <- c('label')

Merge activity & labels into activity_labels (over label column)
activity_labels <- merge(x = labels, y = activities, by = 'label', all = TRUE)

Column bind to data; remove redundant label columns
data <- cbind(data, activity_labels$activity)

Give new column a descriptive name
colnames(data)[89] <- 'activity'

===================================


### 4 - Appropriately labels the data set with descriptive variable names.

names <- colnames(data)

Replace prefix t with Time
names <- gsub('^t', 'Time', names)

Replace tBody with TimeBody
names <- gsub("tBody", "TimeBody", names)

Replace prefix f with Frequency
names <- gsub("^f", "Frequency", names)

Replace Acc/Gyro/Mag abbreviations with full word
names <- gsub("Acc", "Accelerometer", names)
names <- gsub("Gyro", "Gyroscope", names)
names <- gsub("Mag", "Magnitude", names)

Change first letter to Caps
names <- gsub("angle", "Angle", names)
names <- gsub("gravity", "Gravity", names)

colnames(data) <- names

===================================


### 5 - From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject.

Aggregate by subject & activity
tidyset <- aggregate(. ~subject + activity, data = data, FUN = mean)

Write in file as instructed with row.names = FALSE
write.table(x = tidyset, file = 'tidyset.txt', row.names = FALSE)

===================================
-end-