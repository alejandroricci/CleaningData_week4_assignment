Config
------

Load the necesary packages and set the paths to the files that I’m going
to use.

    library(data.table)

    ## Warning: package 'data.table' was built under R version 3.6.1

    ################-
    #### CONFIG ####
    ################-
    data_folder <- "D:/Workspace/Doctorado/Coursera 2020/03_Getting And Cleaning Data/week_4/project/getdata_projectfiles_UCI HAR Dataset/UCI HAR Dataset"

    activity_names_file <- sprintf("%s/activity_labels.txt", data_folder)
    features_names_file <- sprintf("%s/features.txt", data_folder)

    train_features_file <- sprintf("%s/train/X_train.txt", data_folder)
    train_labels_file <- sprintf("%s/train/y_train.txt", data_folder)
    train_subjects_file <- sprintf("%s/train/subject_train.txt", data_folder)

    test_features_file <- sprintf("%s/test/X_test.txt", data_folder)
    test_labels_file <- sprintf("%s/test/y_test.txt", data_folder)
    test_subjects_file <- sprintf("%s/test/subject_test.txt", data_folder)

    output_file <- sprintf("%s/activity_tidy_data.txt", data_folder)
    output_per_activity_and_subject_file <- sprintf("%s/activity_tidy_data_per_activity_and_subject.txt", data_folder)

Read data
---------

Here I read the files and replace the column names, sometimes for names
of my own and sometimes for the features names themselves. I also add
the activity ids and subject ids to their corresponding measurements.

    ###################-
    #### READ DATA ####
    ###################-
    #Read the activity labels
    activity_names <- fread(activity_names_file, header = F, sep = " ")
    colnames(activity_names) <- c("activity_id", "activity_name")

    #Read the features
    feature_names <- fread(features_names_file, header = F, sep = " ")
    colnames(feature_names) <- c("feature_id", "feature_name")

    #Read the training data
    train_features <- fread(train_features_file, header = F, sep = " ")
    colnames(train_features) <- feature_names$feature_name

    train_labels <- fread(train_labels_file, header = F, sep = " ")
    train_features$activity_id <- train_labels$V1

    train_subjects <- fread(train_subjects_file, header = F, sep = " ")
    train_features$subject_id <- train_subjects$V1

    #Read the testing data
    test_features <- fread(test_features_file, header = F, sep = " ")
    colnames(test_features) <- feature_names$feature_name

    test_labels <- fread(test_labels_file, header = F, sep = " ")
    test_features$activity_id <- test_labels$V1

    test_subjects <- fread(test_subjects_file, header = F, sep = " ")
    test_features$subject_id <- test_subjects$V1

Process data
------------

Here I combine both datas into one while creating a new variable to
remember where they came from (train or test). Then I filter the columns
to keep only those that were a mean() or a std(). Finally, I add the
actual activity names to the data based on the activity ids.

As a clarification, I wasn’t sure if the assignment wanted me to keep
only the actual measured columns (tBodyAcc, tGravityAcc & tBodyGyro) or
all the measurements from the data. I decided to keep all, but if
necesary you could filter only those columns similarly to how I filtered
the mean() and std() using regex.

    ######################-
    #### PROCESS DATA ####
    ######################-
    #Combine both datas (without losing from where each datapoint came
    train_features$group <- "train"
    test_features$group <- "test"

    merged_data <- rbindlist(list(train_features, test_features))

    #Here I wasn't sure if this assignment wanted me to keep only the tBodyAcc- and tGravityAcc- columns
    #If that was the case then I should do something using cols_names[grepl("^tBodyAcc-|^tGravityAcc-", cols_names)]
    #to filter the data and keep just those columns

    #Keep only the mean() and std() columns, along the ones I added
    cols_names <- colnames(merged_data)
    cols_to_keep <- c("group", "subject_id", "activity_id")
    cols_to_keep <- c(cols_to_keep, cols_names[grepl("mean\\(\\)|std\\(\\)", cols_names)])

    merged_data <- merged_data[, cols_to_keep, with = F]

    #Add the activities names
    merged_data <- merge(merged_data,
                         activity_names,
                         by = "activity_id")

    setcolorder(merged_data, c("group", "subject_id", "activity_id", "activity_name"))

Fix col names
-------------

Now I have my data as I wanted it, but the column names are still a bit
confusing so the old columns were renamed to be easier to understand. To
achieve this, I replaced the *t* or *f* suffix for one of *measure*,
*derived*, *euclidean* or *fourier* depending on how that variable was
calculated. I also removed the parentheses inside the col names. For
example, the old column *tBodyAcc-mean()-X* is now
*measure\_BodyAcc\_mean\_X*.

    #################################-
    #### FIX A BIT THE COL NAMES ####
    #################################-
    #Remove the parenthesis from the colnames and change the - to _ to match my names
    cols_names <- colnames(merged_data)
    cols_names <- gsub("\\(\\)", "", cols_names)
    cols_names <- gsub("-", "_", cols_names)

    #Replace the prefixes used for something a bit more obvious
    cols_names <- gsub("^t(BodyAcc|GravityAcc|BodyGyro)_", "measure_\\1_", cols_names)
    cols_names <- gsub("^t(BodyAccJerk|BodyGyroJerk)_", "derived_\\1_", cols_names)
    cols_names <- gsub("^t(BodyAccMag|GravityAccMag|BodyAccJerkMag|BodyGyroMag|BodyGyroJerkMag)_",
                       "euclidean_\\1_", cols_names)
    cols_names <- gsub("^f(.+)_", "fourier_\\1_", cols_names)

    #Replace the actual col names
    colnames(merged_data) <- cols_names

Create the second data set
--------------------------

Now that both the data and the columns are fixed, I’m going to calculate
the mean for all the measurement columns for all posible combinations of
subject and activity.

    ####################################-
    #### CREATE THE SECOND DATA SET ####
    ####################################-
    #Create another data with the means of each variable per subject / activity
    merged_data_by_subject_and_activity <- merged_data[, lapply(.SD, mean), by = .(group, subject_id, activity_id, activity_name)]

    #Add the mean_of prefix to the columns so they represent what they are
    cols_names <- colnames(merged_data_by_subject_and_activity)
    mean_cols_names <- cols_names[!(cols_names %in% c("group", "subject_id", "activity_id", "activity_name"))]
    mean_cols_names <- sprintf("mean_of_%s", mean_cols_names)
    cols_names <- c("group", "subject_id", "activity_id", "activity_name", mean_cols_names)
    colnames(merged_data_by_subject_and_activity) <- cols_names

Save the data
-------------

The last step is to save the now tidy data.

    ####################-
    #### WRITE DATA ####
    ####################-
    write.table(merged_data, file = output_file, col.names = T, row.names = F, sep = ",", quote = T)
    write.table(merged_data_by_subject_and_activity, file = output_per_activity_and_subject_file, col.names = T, row.names = F, sep = ",", quote = T)
