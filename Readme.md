#libraries required
library(dplyr)
library(tidyr)


# read features.txt into a dataset
features <- read.table("features.txt")

# convert the second column into a vector containing the column names 
 
 colnames <- make.names(features$V2)
# read the activity labels into the activity master dataframe
 
 activity_master <-  
 
 read.table("activity_labels.txt",col.name = c("activity_code","activity"))
  
#now read test data into activities_test, subject_test, and test_partial dataframes 
    
   activities_test <- read.table("test/y_test.txt",col.name = "activity_code")
  
 
subject_test <- read.table("test/subject_test.txt",col.name = "subject_code")

 test_partial <-   read.table("test/X_test.txt",col.names=colnames)

#merge all of the above test data to create a complete test datafram

 test_all <- cbind(subject_test,activities_test,test_partial)


# remove all the fields that dont have mean or std in them 
test_clean =      select(test_all,subject_code, activity_code, matches('\\.mean\\.'), matches('\\.std'))


# now for train data 

  activities_train <- read.table("train/y_train.txt",col.name = "activity_code")
  
 
subject_train <- read.table("train/subject_train.txt",col.name = "subject_code")

 train_partial <-   read.table("train/X_train.txt",col.names=colnames)


# read all of the training data into a complete train dataframe 
 train_all <- cbind(subject_train,activities_train,train_partial)
 
 # remove all of the columns that do not have mean or std in them 
 
 train_clean =      select(train_all,subject_code, activity_code, matches('\\.mean\\.'), matches('\\.std'))
 
# merge the test and train data. they only have the columns that have mean or std in the name 

data_clean <- rbind(test_clean,train_clean)


# Replace all the activities with words instead of just numbers  #remove the activity code column 

data_descriptive <- left_join(data_clean,activity_master,by ='activity_code')

#remove the activity code column 
data_descriptive <- select(data_descriptive,-activity_code)

# reordering of the columns

data_descriptive <- data_descriptive[c(1,68,2:67)]

#do the summary and store in tidy data

#convert the feature columns into single column using gather we retain
# subject code and activity. the feature columns becomes the key and readings become the value

data_long <- gather(data_descriptive,feature,reading,-c(activity,subject_code))

# now we need to group this data as asked in the problem and call it tidy data 

# group by subject, activity, feature 

data_long <- group_by(data_long,subject_code, activity, feature )
# we take the mean of the readings as asked in the problem 

tidy_data <- summarize(data_long,mean = mean(reading))





