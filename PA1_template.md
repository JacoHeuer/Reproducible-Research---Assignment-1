# Loading and preprocessing the data:

Download and load data:

library(ggplot2)
library(scales)
library(Hmisc)
if(!file.exists("repdata-data-activity.zip")) { temp <- tempfile() download.file("http://d396qusza40orc.cloudfront.net/repdata%2Fdata%2Factivity.zip",temp) unzip(temp) unlink(temp) } activityData <- read.csv("activity.csv")

# What is mean total number of steps taken per day?

stepsByDay <- tapply(activityData$steps, activityData$date, sum, na.rm=TRUE)

1.Histogram of total number of steps taken

qplot(stepsByDay, xlab='Total steps per day', ylab='Frequency using binwith 500', binwidth=500)

Reproducible-Research---Assignment-1/frequency using binwith 500.png

2.Calculate mean and median total number of steps taken

stepsByDayMean <- mean(stepsByDay)

stepsByDayMedian <- median(stepsByDay)

# What is the average daily activity pattern?

average daily activity pattern

averageStepsPerTimeBlock <- aggregate(x=list(meanSteps=activityData$steps), by=list(interval=activityData$interval), FUN=mean, na.rm=TRUE)

1.Time series plot

ggplot(data=averageStepsPerTimeBlock, aes(x=interval, y=meanSteps)) +

geom_line() +

xlab("5-minute interval") +

ylab("average number of steps taken") 

Reproducible-Research---Assignment-1/number of step 5-minute intrval.png

2.Contains the maximum number of steps

mostSteps <- which.max(averageStepsPerTimeBlock$meanSteps)

timeMostSteps <- gsub("([0-9]{1,2})([0-9]{2})", "\1:\2", averageStepsPerTimeBlock[mostSteps,'interval'])

# Imputing missing values:

1.Total number of missing values in data set

numMissingValues <- length(which(is.na(activityData$steps)))

2.Missing data filled in new data set

activityDataImputed <- activityData

activityDataImputed$steps <- impute(activityData$steps, fun=mean)

3.Histogram of total number of steps taken each day

stepsByDayImputed <- tapply(activityDataImputed$steps, activityDataImputed$date, sum)

qplot(stepsByDayImputed, xlab='Total steps per day (Imputed)', ylab='Frequency using binwith 500', binwidth=500)

Reproducible-Research---Assignment-1/steps taken each day.png

4.Mean and mediun of total number of steps taken per day

stepsByDayMeanImputed <- mean(stepsByDayImputed)

stepsByDayMedianImputed <- median(stepsByDayImputed)

# Are there differences in activity patterns between weekdays and weekends?

1.Weekday or weekend

activityDataImputed$dateType <- ifelse(as.POSIXlt(activityDataImputed$date)$wday %in% c(0,6), 'weekend', 'weekday')

2.Panel plot

averagedActivityDataImputed <- aggregate(steps ~ interval + dateType, data=activityDataImputed, mean)

ggplot(averagedActivityDataImputed, aes(interval, steps)) +

geom_line() + 

facet_grid(dateType ~ .) +

xlab("5-minute interval") + 

ylab("avarage number of steps")

Reproducible-Research---Assignment-1/avarage number of steps.png
