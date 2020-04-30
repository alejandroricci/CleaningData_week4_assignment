---
title: "Code Book"
output: html_document
---

## Tidy data

Like the assignment asked, I modified the columns so one dataset had information obtained from many files, resulting in a file named **activity_tidy_data.csv** that contained the mean and std of each measurement for the train and test groups. The meanings of each of the new columns are:

- **group**: if that measurement belongs to the *train* or *test* group
- **subject_id**: the id of the subject corresponding to that measurement
- **activity_id**: the id of the activity corresponding to that measurement
- **activity_name**: the name of the activity corresponding to that measurement, can be *WALKING*, *WALKING_UPSTAIRS*, *WALKING_DOWNSTAIRS*, *SITTING*, *STANDING* or *LAYING*

The old columns were renamed to be easier to understand and only those with the mean and the std were kept. To achieve this, I replaced the *t* or *f* suffix for one of *measure*, *derived*, *euclidean* or *fourier* depending on how that variable was calculated. I also removed the parentheses inside the col names. For example, the old column *tBodyAcc-mean()-X* is now *measure_BodyAcc_mean_X*. The original meaning of each column can be found in the **features_info.txt** file in the original data. The full list of new names for the old variables is:

- **measure_BodyAcc_mean_X**
- **measure_BodyAcc_mean_Y**
- **measure_BodyAcc_mean_Z**
- **measure_BodyAcc_std_X**
- **measure_BodyAcc_std_Y**
- **measure_BodyAcc_std_Z**
- **measure_GravityAcc_mean_X**
- **measure_GravityAcc_mean_Y**
- **measure_GravityAcc_mean_Z**
- **measure_GravityAcc_std_X**
- **measure_GravityAcc_std_Y**
- **measure_GravityAcc_std_Z**
- **derived_BodyAccJerk_mean_X**
- **derived_BodyAccJerk_mean_Y**
- **derived_BodyAccJerk_mean_Z**
- **derived_BodyAccJerk_std_X**
- **derived_BodyAccJerk_std_Y**
- **derived_BodyAccJerk_std_Z**
- **measure_BodyGyro_mean_X**
- **measure_BodyGyro_mean_Y**
- **measure_BodyGyro_mean_Z**
- **measure_BodyGyro_std_X**
- **measure_BodyGyro_std_Y**
- **measure_BodyGyro_std_Z**
- **derived_BodyGyroJerk_mean_X**
- **derived_BodyGyroJerk_mean_Y**
- **derived_BodyGyroJerk_mean_Z**
- **derived_BodyGyroJerk_std_X**
- **derived_BodyGyroJerk_std_Y**
- **derived_BodyGyroJerk_std_Z**
- **euclidean_BodyAccMag_mean**
- **euclidean_BodyAccMag_std**
- **euclidean_GravityAccMag_mean**
- **euclidean_GravityAccMag_std**
- **euclidean_BodyAccJerkMag_mean**
- **euclidean_BodyAccJerkMag_std**
- **euclidean_BodyGyroMag_mean**
- **euclidean_BodyGyroMag_std**
- **euclidean_BodyGyroJerkMag_mean**
- **euclidean_BodyGyroJerkMag_std**
- **fourier_BodyAcc_mean_X**
- **fourier_BodyAcc_mean_Y**
- **fourier_BodyAcc_mean_Z**
- **fourier_BodyAcc_std_X**
- **fourier_BodyAcc_std_Y**
- **fourier_BodyAcc_std_Z**
- **fourier_BodyAccJerk_mean_X**
- **fourier_BodyAccJerk_mean_Y**
- **fourier_BodyAccJerk_mean_Z**
- **fourier_BodyAccJerk_std_X**
- **fourier_BodyAccJerk_std_Y**
- **fourier_BodyAccJerk_std_Z**
- **fourier_BodyGyro_mean_X**
- **fourier_BodyGyro_mean_Y**
- **fourier_BodyGyro_mean_Z**
- **fourier_BodyGyro_std_X**
- **fourier_BodyGyro_std_Y**
- **fourier_BodyGyro_std_Z**
- **fourier_BodyAccMag_mean**
- **fourier_BodyAccMag_std**
- **fourier_BodyBodyAccJerkMag_mean**
- **fourier_BodyBodyAccJerkMag_std**
- **fourier_BodyBodyGyroMag_mean**
- **fourier_BodyBodyGyroMag_std**
- **fourier_BodyBodyGyroJerkMag_mean**
- **fourier_BodyBodyGyroJerkMag_std**

## Tidy data per subject and activity

This data has the same columns as before, only that I added a **mean_of_** prefix to the measurement data columns so they represent what they are.