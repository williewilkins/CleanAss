CODEBOOK

Output is tidydata.txt dataset


What is done:

You should create one R script called run_analysis.R that does the following. 
1.Merges the training and the test sets to create one data set.
2.Extracts only the measurements on the mean and standard deviation for each measurement. 
3.Uses descriptive activity names to name the activities in the data set
4.Appropriately labels the data set with descriptive variable names. 
5.From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject.


Steps by Step

Donwload file

if(!file.exists(“./data”)){dir.create(“./data”)} fileUrl <- “https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip” download.file(fileUrl,destfile=“./data/Dataset.zip”)

Unzip

unzip(zipfile=“./Dataset.zip”,exdir=“./data”)

Path of the Unzip files

path_rf <- file.path(“./data” , “UCI HAR Dataset”) files<-list.files(path_rf, recursive=TRUE)

Reading data

dataActivityTest <- read.table(file.path(path_rf, “test” , “Y_test.txt” ),header = FALSE) dataActivityTrain <- read.table(file.path(path_rf, “train”, “Y_train.txt”),header = FALSE) dataSubjectTrain <- read.table(file.path(path_rf, “train”, “subject_train.txt”),header = FALSE) dataSubjectTest <- read.table(file.path(path_rf, “test” , “subject_test.txt”),header = FALSE) dataFeaturesTest <- read.table(file.path(path_rf, “test” , “X_test.txt” ),header = FALSE) dataFeaturesTrain <- read.table(file.path(path_rf, “train”, “X_train.txt”),header = FALSE)

Accessing data

str(dataActivityTest) str(dataActivityTrain) str(dataSubjectTrain) str(dataSubjectTest) str(dataFeaturesTest) str(dataFeaturesTrain)

Combine Data

dataSubject <- rbind(dataSubjectTrain, dataSubjectTest) dataActivity<- rbind(dataActivityTrain, dataActivityTest) dataFeatures<- rbind(dataFeaturesTrain, dataFeaturesTest)

Reading feature data

dataFeaturesNames <- read.table(file.path(path_rf, “features.txt”),head=FALSE)

Name Varialbes

names(dataSubject)<-c(“subject”) names(dataActivity)<- c(“activity”) names(dataFeatures)<- dataFeaturesNames$V2

Merging data

dataCombine <- cbind(dataSubject, dataActivity) Data <- cbind(dataFeatures, dataCombine)

Extract Information

subdataFeaturesNames<-dataFeaturesNames$V2[grep(“mean\(\)|std\(\)”, dataFeaturesNames$V2)]

#Subset selectedNames<-c(as.character(subdataFeaturesNames), “subject”, “activity” ) Data<-subset(Data,select=selectedNames)

#Reading descriptive name data activityLabels <- read.table(file.path(path_rf, “activity_labels.txt”),header = FALSE) names(Data)<-gsub(“t”, “time”, names(Data)) names(Data)<-gsub(“f”, “frequency”, names(Data)) names(Data)<-gsub(“Acc”, “Accelerometer”, names(Data)) names(Data)<-gsub(“Gyro”, “Gyroscope”, names(Data)) names(Data)<-gsub(“Mag”, “Magnitude”, names(Data)) names(Data)<-gsub(“BodyBody”, “Body”, names(Data))

#Install packages install.packages(“plyr”) library(plyr)

Write data

Data2<-aggregate(. ~subject + activity, Data, mean) Data2<-Data2[order(Data2$subject,Data2$activity),] write.table(Data2, file = “tidydata.txt”,row.name=FALSE)







