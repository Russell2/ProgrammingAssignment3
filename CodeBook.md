# Getting and Cleaning Data Programming Assignment

This document is the description of the variables, the data, and any transformations or work that I performed to clean up the data. The data was aquired originally on 07-31-2016 @ 11:48:54 PDT from:    
    
https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip  
    
I extracted the files into a folder named "UCI HAR Dataset" and set my working directory to that directory. Then I ran the script run_analysis.R see README.md for information on that.  
  
The purpose of this project was to demonstrate my ability to collect, work with, and clean a data set. The goal was to prepare tidy data that could be used for later analysis.  
  
I was required to submit:  
1.  a tidy data set as described below.  
2.  a link to a Github repository my script for performing the analysis.
3.  a code book called CodeBook.md and README.md in the repo with my script.
  
I created one R script called run_analysis.R that does the following:  
  
1.  Merge the training and the test sets to create one data set.
2.  Extracts only the measurements on the mean and standard deviation for each measurement.
3.  Add descriptive activity names to name the activities in the data set
4.  Appropriately label the data set with descriptive variable names.
5.  From the data set in step 4, it creates a second, independent tidy data set with the average of each variable for each activity and each subject and stores that dataset as a csv file.
  
To explain my actions we need to start with the abstract and data information drawn from the website where the data was originally derived.  

Abstract: Human Activity Recognition database built from the recordings of 30 subjects performing activities of daily living (ADL) while carrying a waist-mounted smartphone with embedded inertial sensors.  

Data Set Information:  

The experiments have been carried out with a group of 30 volunteers within an age bracket of 19-48 years. Each person performed six activities (WALKING, WALKING_UPSTAIRS, WALKING_DOWNSTAIRS, SITTING, STANDING, LAYING) wearing a smartphone (Samsung Galaxy S II) on the waist. Using its embedded accelerometer and gyroscope, we captured 3-axial linear acceleration and 3-axial angular velocity at a constant rate of 50Hz.  

The sensor signals (accelerometer and gyroscope) were pre-processed by applying noise filters and then sampled in fixed-width sliding windows of 2.56 sec and 50% overlap (128 readings/window). The sensor acceleration signal, which has gravitational and body motion components, was separated using a Butterworth low-pass filter into body acceleration and gravity. The gravitational force is assumed to have only low frequency components, therefore a filter with 0.3 Hz cutoff frequency was used. From each window, a vector of features was obtained by calculating variables from the time and frequency domain.  

In addition to that reading the text files accompanying the data sets we see the body linear acceleration and angular velocity were derived in time to obtain Jerk signals and Magnitude was calculated using the Euclidean norm.  

Now if we examine the names for the columns in "features_info.txt" and break them apart we see they are values not variable names for our tidy data set.  

### From all this we see our data set has all of these variables:
Variable  | Value
--------- |:-----
Subject   | Subject id number 1-30
CalcType  | Value derived Mean or Standard Deviation (STD)
Activity  | The activity subject is engaged in WALKING, WALKING_UPSTAIRS, WALKING_DOWNSTAIRS, SITTING, STANDING, or LAYING
Domain    | Whether the variance value is derived within the Time or Frequency domain
Device    | Where was the information coming from the Accelerometer or Gyroscope 
AccelType | Whether the acceleration is Gravitational or Body motion
Axis      | X,Y, or Z which of the 3-axial is the information for 
Jerk      | TRUE or FALSE
Magnitude | TRUE or FALSE
Value     | The average value for this observation


