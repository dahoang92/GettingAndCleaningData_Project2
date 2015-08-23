Loading Library dplyr
```{r}
library("dplyr")
```

Read the features and activity_labels data into the data frame
```{r}
features <- read.table("features.txt")
activity_labels <- read.table("activity_labels.txt")
```

Read the test data and change the variables's names using the feature data frame
```{r}
X_test <- read.table("test/X_test.txt")
colnames(X_test) <- features$V2
y_test <- read.table("test/y_test.txt")
colnames(y_test) <- c("activity")
subject_test <- read.table("test/subject_test.txt")
colnames(subject_test) <- c("subject")
```

Read the train data and change the variables's names using the feature data frame
```{r}
X_train <- read.table("train/X_train.txt")
colnames(X_train) <- features$V2
y_train <- read.table("train/y_train.txt")
colnames(y_train) <- c("activity")
subject_train <- read.table("train/subject_train.txt")
colnames(subject_train) <- c("subject")
```

Combine the test and the train data into one data frame
( using cbind to combind by column and rbind to combind by row)
```{r}
Test <- cbind(subject = subject_test$subject,activity = y_test$activity, X_test)
Train <- cbind(subject = subject_train$subject,activity = y_train$activity, X_train)
Data_total <- rbind(Test, Train)library("dplyr")
```

Replace activity number with its label by merging the data with activity_labels, changing activity
column to the corresponding string and then delete the V2 variable
```{r}
Data_merge <- merge(Data_total, activity_labels, by.x= "activity", by.y= "V1")
Data_merge$activity <- Data_merge$V2
Data_merge$V2 <- NULL
```

Select the variable with information about the mean and std and not the "MeanFreq" 
and then merge them back to Data
```{r}
Data <- Data_merge[,grep("mean()|std()",colnames(Data_merge))]
Data <- Data[,-grep("meanFreq()",colnames(Data))]
Data <- cbind(Data_merge[,1:2], Data)
```

Create data with average of each variable for each activity and each subject
```{r}
Data_final <- Data %>%
  group_by(subject, activity) %>%
  summarise_each(funs(mean))
```

Write the data into the text file
```{r}
write.table(Data_final, file="data_final.txt", row.names = FALSE)
```