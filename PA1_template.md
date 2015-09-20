# Reproducible Research: Peer Assessment 1


## Loading and preprocessing the data
##### 1. Load the data (i.e. read.csv())

```r
if(!file.exists('activity.csv')){
    unzip('activity.zip')
}
activityData <- read.csv('activity.csv')
```
##### 2. Process/transform the data (if necessary) into a format suitable for your analysis

```r
#activityData$interval <- strptime(gsub("([0-9]{1,2})([0-9]{2})", "\\1:\\2", activityData$interval), format='%H:%M')
```


## What is mean total number of steps taken per day?

```r
stepsByDay <- tapply(activityData$steps, activityData$date, sum, na.rm=TRUE)
```
##### 1. Make a histogram of the total number of steps taken each day
![](PA1_template_files/figure-html/unnamed-chunk-4-1.png) 

##### 2. Calculate and report the mean and median total number of steps taken per day

```r
meanSteps <- mean(stepsByDay)
medianSteps <- median(stepsByDay)
```
* Mean: 9354.2295082
* Median:  10395

## What is the average daily activity pattern?

```r
averageStepsInterval <- aggregate(x=list(meanSteps=activityData$steps), by=list(interval=activityData$interval), FUN=mean, na.rm=TRUE)
```
##### 1. Make a time series plot

```r
ggplot(data=averageStepsInterval, aes(x=interval, y=meanSteps)) +
    geom_line() +
    xlab("5-minute intervals") +
    ylab("avg steps") 
```

![](PA1_template_files/figure-html/unnamed-chunk-7-1.png) 

##### 2. Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?

```r
mostSteps <- which.max(averageStepsInterval$meanSteps)
timeMostSteps <-  gsub("([0-9]{1,2})([0-9]{2})", "\\1:\\2", averageStepsInterval[mostSteps,'interval'])
```

* Most Steps at: 8:35


## Imputing missing values
##### 1. Calculate and report the total number of missing values in the dataset 

```r
missingValues <- length(which(is.na(activityData$steps)))
```

* Number of missing values: 2304

##### 2. Devise a strategy for filling in all of the missing values in the dataset.
##### 3. Create a new dataset that is equal to the original dataset but with the missing data filled in.



##### 4. Make a histogram of the total number of steps taken each day 

```r
imputedStepsByDay <- tapply(activityDataImputed$steps, activityDataImputed$date, sum)
qplot(imputedStepsByDay, xlab='Total steps per day (Imputed)', ylab='Frequency using width 1000', binwidth=1000)
```

![](PA1_template_files/figure-html/unnamed-chunk-11-1.png) 

##### ... and Calculate and report the mean and median total number of steps taken per day. 

```r
imputedStepsByDayMean <- mean(imputedStepsByDay)
imputedStepsByDayMedian <- median(imputedStepsByDay)
```
* Mean (Imputed): 1.0766189\times 10^{4}
* Median (Imputed):  1.0766189\times 10^{4}




## Are there differences in activity patterns between weekdays and weekends?
##### 1. Create a new factor variable in the dataset with two levels - "weekday" and "weekend" indicating whether a given date is a weekday or weekend day.


```r
activityDataImputed$dateType <-  ifelse(as.POSIXlt(activityDataImputed$date)$wday %in% c(0,6), 'weekend', 'weekday')
```

##### 2. Make a panel plot containing a time series plot


```r
imputedAveragedActivityData <- aggregate(steps ~ interval + dateType, data=activityDataImputed, mean)
ggplot(imputedAveragedActivityData, aes(interval, steps)) + 
    geom_line() + 
    facet_grid(dateType ~ .) +
    xlab("5-minute interval") + 
    ylab("avg steps")
```

![](PA1_template_files/figure-html/unnamed-chunk-14-1.png) 
