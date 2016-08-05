# Getting and Cleaning Data Programming Assignment

## This document is to explain how the script works.

### Load all of the functions needed to do this task
library(data.table)
library(dplyr)

### Read and merge all of the Readings data files (STEP 1)
data<-rbind(fread("train/X_train.txt"),fread("test/X_test.txt"))

### Load the column names file for the data set
columns<-fread("features.txt")
names(columns)<- c("col", "name")

### Find all of the columns that deal with the measurements on the mean and 
### standard deviation for each measurement
isMeanStd <- grepl("mean|std", columns$name)

### Get their column numbers
columnsToExtract<-columns[isMeanStd]$col

### Select out all of the relevant columns from the megrged data (STEP 2)
data<-select(data,columnsToExtract)

### Read and merge all of the activity data files and merge that into the 
### dataset
all_ac<-rbind(fread("train/y_train.txt"),fread("test/y_test.txt"))
names(all_ac)<-"ac_id"
data<-cbind(all_ac,data)

### Get descriptive activity names 
acLabels<-fread("activity_labels.txt")
names(acLabels)<-c("ac_id","Activity")

### Use that to name the activities in the data set and drop the id (STEP 3)
data<-full_join(acLabels, data, by="ac_id") %>% select(Activity:V552)

### Appropriately label the data set with descriptive variable names (STEP 4)
names(data)<-c("Activity",sub("\\(\\)","",columns[isMeanStd]$name))

### Read and merge the subject data files
subjects <- rbind(fread("train/subject_train.txt"),fread("test/subject_test.txt"))
names(subjects)<-"Subject"
data<-cbind(subjects, data)

### Now keep Subject and Activity and stack the remaining colmns into name:value 
### pairs under Feature
stack <- data.table(melt(data, c("Subject","Activity"), variable.name="Feature"))

############################
### The function 'tidyup' reads the "Feature" column for searching for the 
### regular expression and returns a logical vector corresponding to the rows 
### where those values were found 
###
### The function takes one argument: 
### what ----- string regular expression 
###
### Returns a logical vector 
###
############################
tidyUp <- function(what){
  c(grepl(what,stack$Feature))
}

### Create a column indicating whether the corresponding CalcType is a mean or 
### standard deviation
stack$CalcType[tidyUp("mean")] <- "Mean"
stack$CalcType[tidyUp("std")] <- "STD"

### Create a column indicating whether the corresponding Domain is time or
### frequency
stack$Domain[tidyUp("^t")] <- "Time"
stack$Domain[tidyUp("^f")] <- "Freq"

### Create a column indicating whether the corresponding Accelaration Type
### is body or gravity
stack$AccelType[tidyUp("BodyAcc")] <- "Body"
stack$AccelType[tidyUp("GravityAcc")] <- "Gravity" 

### Create a column indicating whether the value came from which device
### Accelerometer or Gyroscope
stack$Device[tidyUp("Acc")] <-  "Accelerometer"
stack$Device[tidyUp("Gyro")] <- "Gyroscope"

### Create a column indicating whether the value is Magnitude 
stack$Magnitude<-tidyUp("Mag")

### Create a column indicating whether the value is a Jerk 
stack$Jerk<-tidyUp("Jerk")

### Create a column indicating whether the value is one of the 3-axial angular
### readings
stack$Axis[tidyUp("-X")] <-  "X"
stack$Axis[tidyUp("-Y")] <-  "Y"
stack$Axis[tidyUp("-Z")] <-  "Z"

### Create a tidy data set with the average of each variable for each activity and each subject (STEP 5)
stack <- stack %>% 
select(Subject, Activity, CalcType, Domain, AccelType, Device, Magnitude, Jerk, Axis, value) %>% 
group_by(Subject,Activity, CalcType, Domain, AccelType, Device, Magnitude, Jerk, Axis) %>% 
summarise(mean(value))

### clean up the names
names(stack) <- c("Subject","Activity","CalcType","Domain","AccelType","Device","Magnitude","Jerk","Axis","Value")

### Save to the tidy data into the working directory
write.csv(stack, file="HumanActivityRecognitionMeanData.csv")

