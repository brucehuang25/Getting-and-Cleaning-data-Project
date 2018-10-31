# Getting-and-Cleaning-data-Project

Considering we have in local our test and train dataset
The first thing is to store those tables into variables

### Loading activity datasets
``` R
testActivity  <- read.table("UCI HAR Dataset/test/Y_test.txt" , header = FALSE)
trainActivity <- read.table("UCI HAR Dataset/train/Y_train.txt", header = FALSE)
```

### Loading subject datasets 
``` R
testSubject  <- read.table("UCI HAR Dataset/test/subject_test.txt", header = FALSE)
trainSubject <- read.table("UCI HAR Dataset/train/subject_train.txt", header = FALSE)
```

### Loading features datasets 
``` R
testFeatures  <- read.table("UCI HAR Dataset/test/X_test.txt", header = FALSE)
trainFeatures <- read.table("UCI HAR Dataset/train/X_train.txt", header = FALSE)
```

### Loading labels
``` R
labels <- read.table("UCI HAR Dataset/activity_labels.txt", header = FALSE)
```

### Loading features names
``` R
featuresNames <- read.table("UCI HAR Dataset/features.txt", head=FALSE)
```

### Combining the testing and training datasets
``` R
activity <- rbind(trainActivity, testActivity)
subject <- rbind(trainSubject, testSubject)
features <- rbind(trainFeatures, testFeatures)
```

### Aliasing activity to assing the label a human readable category
``` R
activity$V1 <- factor(activity$V1, levels = as.integer(labels$V1), labels = labels$V2)
``` 

### Renaming columns for activity and subject
``` R
names(activity)<- c("activity")
names(subject)<-c("subject")
```
### Renaming features
``` R
names(features)<- featuresNames$V2
``` 


### Selecting just those columns with mean and standard deviation information
``` R
meanstdev<-c(as.character(featuresNames$V2[grep("mean\\(\\)|std\\(\\)", featuresNames$V2)]))
``` 

### Subseting the dataset with the mean and std columns
``` R
featuresSubset<-subset(features,select=meanstdev)
```

### Combining data sets: subject, activity and features
``` R
subjectactivity <- cbind(subject, activity)
finaldataset <- cbind(featuresSubset, subjectactivity)
```

### Renaming time and frequency
``` R
names(finaldataset)<-gsub("^t", "time", names(finaldataset))
names(finaldataset)<-gsub("^f", "frequency", names(finaldataset))
```

### Creating a new data set with subject and activity means
``` R
suppressWarnings(cleandata <- aggregate(finaldataset, by = list(finaldataset$subject, finaldataset$activity), FUN = mean))
colnames(cleandata)[1] <- "Subject"
names(cleandata)[2] <- "Activity"
```

### Removing avg and stdev for non-aggregated sub and act columns
``` R
cleandata <- cleandata[1:68]
```
### Writing our tidy data set into a text file
``` R
write.table(cleandata, file = "cleandata.txt", row.name = FALSE)
```
