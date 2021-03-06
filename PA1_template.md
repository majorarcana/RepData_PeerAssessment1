#Reproducible Research: Peer Assessment 1

## Loading and preprocessing the data

* Load the data


```r
activity = read.csv('activity.csv', header = TRUE)
```

## What is mean total number of steps taken per day?

* Calculate the total number of steps taken per day


```r
total <- aggregate(steps~date, data=activity, sum, na.rm = TRUE)
```

* Make a histogram of the total number of steps taken each day


```r
hist(total$steps)
```

![plot of chunk unnamed-chunk-3](figure/unnamed-chunk-3-1.png)

* Calculate and report the mean and median of the total number of steps taken per day


```r
mean(total$steps)
```

```
## [1] 10766.19
```

```r
median(total$steps)
```

```
## [1] 10765
```

The mean total number of steps per day is 1.0766189 &times; 10<sup>4</sup>
The median total number of steps per day is 10765

## What is the average daily activity pattern?

* Make a time series plot (i.e. 𝚝𝚢𝚙𝚎 = "𝚕") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)


```r
stepsInterval <- aggregate(steps~interval, data=activity, mean, na.rm = TRUE)
plot(steps~interval, data = stepsInterval, type = "l")
```

![plot of chunk unnamed-chunk-5](figure/unnamed-chunk-5-1.png)

* Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?


```r
stepsInterval[which.max(stepsInterval$steps),]$interval
```

```
## [1] 835
```

The interval that on average contains the max number of steps is 835

## Imputing missing values

* Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with 𝙽𝙰s)


```r
sum(is.na(activity$steps))
```

```
## [1] 2304
```

The total number of rows with NA values is 2304

* Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.

I have chosen to use the mean for that 5-minute interval


```r
intervalSteps <- function(interval) {
    stepsInterval[stepsInterval$interval == interval,]$steps
}
```


* Create a new dataset that is equal to the original dataset but with the missing data filled in.


```r
activityComplete <- activity
for(i in 1:nrow(activityComplete)) {
    if(is.na(activityComplete[i,]$steps)) {
        activityComplete[i,]$steps <- intervalSteps(activityComplete[i,]$interval)
    }
}
```

* Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. 


```r
total2 <- aggregate(steps~date, data = activityComplete, sum)
hist(total2$steps)
```

![plot of chunk unnamed-chunk-10](figure/unnamed-chunk-10-1.png)

```r
mean(total2$steps)
```

```
## [1] 10766.19
```

```r
median(total2$steps)
```

```
## [1] 10766.19
```

The mean total number of steps per day is 1.0766189 &times; 10<sup>4</sup>
The median total number of steps per day is 1.0766189 &times; 10<sup>4</sup>

* Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?

The mean value is identical to before as I used the mean value for the 5 minute interval. There is a slight difference in the median values. 

## Are there differences in activity patterns between weekdays and weekends?

* Create a new factor variable in the dataset with two levels – “weekday” and “weekend” indicating whether a given date is a weekday or weekend day.


```r
activityComplete$day = ifelse(as.POSIXlt(as.Date(activityComplete$date))$wday%%6==0, "weekend", "weekday")
activityComplete$day = factor(activityComplete$day, levels = c("weekday", "weekend"))
```

* Make a panel plot containing a time series plot (i.e. 𝚝𝚢𝚙𝚎 = "𝚕") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). See the README file in the GitHub repository to see an example of what this plot should look like using simulated data.


```r
stepsInterval2 = aggregate(steps~interval+day, activityComplete, mean)
library(lattice)
xyplot(steps~interval|factor(day), data = stepsInterval2, aspect = 1/2, type = "l")
```

![plot of chunk unnamed-chunk-12](figure/unnamed-chunk-12-1.png)
