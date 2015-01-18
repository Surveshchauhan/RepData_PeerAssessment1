# Reproducible Research: Peer Assessment 1


## Loading and preprocessing the data

```r
library(lattice)
mydata <- read.csv('activity.csv',sep = ",",header = TRUE, na.strings ="NA",colClasses = c('numeric','Date','numeric'))

mydatanew<-na.omit(mydata)
```


## What is mean total number of steps taken per day?
### 1. Make a histogram of the total number of steps taken each day

```r
tsteps <- aggregate(steps ~ date, mydatanew, sum)
hist(tsteps$steps , main = "Total number of steps for day", xlab = "sum of steps", col = "green")
```

![](./PA1_template_files/figure-html/unnamed-chunk-2-1.png) 

### 2. Calculate and report the mean and median total number of steps taken per day

```r
tstepsDayMean <- mean(tsteps$steps)
print(sprintf("Mean of steps per day: %f ", tstepsDayMean))
```

```
## [1] "Mean of steps per day: 10766.188679 "
```

```r
tstepsDayMedian <- median(tsteps$steps)
print(sprintf("Median of steps per day: %f ", tstepsDayMedian))
```

```
## [1] "Median of steps per day: 10765.000000 "
```

## What is the average daily activity pattern?
### 1. Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)

```r
timeseriesplot <- tapply(mydatanew$steps, mydatanew$interval, mean)

plot(row.names(timeseriesplot), timeseriesplot, type = "l", xlab = "5-min interval", 
     ylab = "Average (all Days)", main = "Avg number of steps taken", 
     col = "blue")
```

![](./PA1_template_files/figure-html/unnamed-chunk-4-1.png) 

### 2. Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?

```r
max<- which.max(timeseriesplot)
names(max)
```

```
## [1] "835"
```

## Imputing missing values
### 1. Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)

```r
NosofRowswithNA <- sum(is.na(mydata))
NosofRowswithNA
```

```
## [1] 2304
```
### 2. Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.

```r
mydataimputed<-mydata
tstepsDayMean <- aggregate(steps ~ interval, data = mydata, FUN = mean)
## will use the mean values to fill in the NA values in the mydata 
```
### 3.Create a new dataset that is equal to the original dataset but with the missing data filled in.

```r
for (i in 1:nrow(mydata)) {
  row <- mydata[i, ]
  if (is.na(row$steps)) {
    mydataimputed[i,1] <- subset(tstepsDayMean,interval==row$interval)$steps
  } 
}
## check total rows has that has NA values
sum(is.na(mydataimputed))
```

```
## [1] 0
```
### 4. Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?

```r
tstepsnew <- aggregate(steps ~ date, mydataimputed, sum,na.rm=TRUE)
hist(tstepsnew$steps , main = "Total steps by day", xlab = "steps", col = "green")
```

![](./PA1_template_files/figure-html/unnamed-chunk-9-1.png) 

```r
tstepsDayMeannew <- mean(tstepsnew$steps)
print(sprintf("Mean of steps per day: %f ", tstepsDayMeannew))
```

```
## [1] "Mean of steps per day: 10766.188679 "
```

```r
tstepsDayMediannew <- median(tstepsnew$steps)
print(sprintf("Median of steps per day: %f ", tstepsDayMediannew))
```

```
## [1] "Median of steps per day: 10766.188679 "
```


## Are there differences in activity patterns between weekdays and weekends?
### 1. Create a new factor variable in the dataset with two levels - "weekday" and "weekend" indicating whether a given date is a weekday or weekend day.

```r
day<-weekdays(mydata$date)
level<-vector()
for (i in 1:nrow(mydata)) {
    
  if(day[i]=="Sunday")
    {
      level[i]<-"weekend"
    }
  else if(day[i]=="Saturday")
    {
      level[i]<-"weekend"
    }
  else 
  {
    level[i]<-"weekday"
  }  
}
mydata$level<-level
mydata$level<-factor(mydata$level)
```
### 2. Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). See the README file in the GitHub repository to see an example of what this plot should look like using simulated data

```r
tsteps <- aggregate(steps ~ interval + level, mydata, mean)

xyplot(steps ~ interval | level, tsteps, type = "l", layout = c(1, 2), 
       xlab = "Interval", ylab = "Number of steps")
```

![](./PA1_template_files/figure-html/unnamed-chunk-11-1.png) 
