##******************************************************************
### README file to explain the R code implementing the project in analysis.R file
##******************************************************************
## Introduction
### One of the most exciting areas in all of data science right now is wearable computing. Companies like Fitbit, Nike, and Jawbone Up are racing to develop the most advanced algorithms to attract new users. The data linked to from the course website represent data collected from the accelerometers from the Samsung Galaxy S smartphone. 
### A full description is available at the site where the data was obtained:
### http://archive.ics.uci.edu/ml/datasets/Human+Activity+Recognition+Using+Smartphones
### Here are the data for the project:
### https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip

## The R script run_analysis.R that does the following steps is explained below:
### 1.	Merges the training and the test sets to create one data set.
### 2.	Extracts only the measurements on the mean and standard deviation for each measurement.
### 3.	Uses descriptive activity names to name the activities in the data set
### 4.	Appropriately labels the data set with descriptive variable names.
### 5.	From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject.

##*******************************************************************
## the tidy data will be stored in file "tidy_averages.txt" in folder projectfiles
##*******************************************************************

### Explanation of each of the above steps and the R code
 
## step 1
### Merges the training and the test sets to create one data set.

### read training data
x_train <- read.table("projectfiles/train/X_train.txt")

y_train <- read.table("projectfiles/train/y_train.txt")

subject_train <- read.table("projectfiles/train/subject_train.txt")

### read test data
x_test <- read.table("projectfiles/test/X_test.txt")

y_test <- read.table("projectfiles/test/y_test.txt")

subject_test <- read.table("projectfiles/test/subject_test.txt")

### create x_data data set
x_data <- rbind (x_train, x_test)

### create y_data data set
y_data <- rbind (y_train, y_test)

### create the subject_data data set
subject_data <- rbind (subject_train, subject_test)

## step 2
### Extracts only the measurements on the mean and standard deviation for each measurement.

### read the features file
features <- read.table("projectfiles/features.txt")

### keep only the rows with the words 'mean' or 'std' (from 2nd column of features table)
mean_std_in_features <- grep("-(mean|std)\\(\\)", features[, 2])

### project on the second column
x_data <- x_data[, mean_std_in_features]

### put the correct the column names
names(x_data) <- features[mean_std_in_features, 2]

## step 3
### Uses descriptive activity names to name the activities in the data set

### read the activity labels 
activities <- read.table("projectfiles/activity_labels.txt")

### replace numeric values with appropriate activity names
y_data[, 1] <- activities[y_data[, 1], 2]

### replace the column name 'V1' by 'activity'
names(y_data) <- "activity"

## step 4
### Appropriately labels the data set with descriptive variable names.

### replace column name 'V1' by 'subject'
names(subject_data) <- "subject"

### column bind all the data sets in a single data set
all_data <- cbind(x_data, y_data, subject_data)

## step 5
### From the data set in step 4, creates a second, independent tidy data set with
### the average of each variable for each activity and each subject.

### ddply - For each subset of the data frame all_data, apply function colMeans 
### to the 66 columns each corresponding to a column then combine 
### results into the data frame tidy_averages_data.
tidy_averages_data <- ddply(all_data, 
                      .(subject, activity),
                       function(x) colMeans(x[, 1:66]))

### create and write tidy_averages_data to the new file tidy_averages
write.table (tidy_averages_data,
             "projectfiles/tidy_averages.txt",
              row.name=FALSE)
