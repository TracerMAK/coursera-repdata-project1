
## Loading and preprocessing the data

```r
activity <- read.csv("activity.csv")
activity = transform(activity, date=as.Date(date))
```

## Total number of steps taken per day

```r
dailySteps <- aggregate(steps ~ date, data=activity, FUN=sum)
hist(x=dailySteps$steps, breaks=20, main="Daily Steps", xlab="Steps", col="lightblue")
```

![plot of chunk dailysteps](figure/dailysteps-1.png)

## What is the mean and median total number of steps taken per day?

```r
mean(dailySteps$steps)
```

```
## [1] 10766.19
```

```r
median(dailySteps$steps)
```

```
## [1] 10765
```

## What is the average daily activity pattern?

```r
intervals <- aggregate(steps ~ interval, data=activity, FUN=mean)
maxInterval <- intervals$interval[intervals$steps == max(intervals$steps)]
plot(steps ~ interval, data=intervals, type="l")
text(1800, 200, paste("Maximum step interval = ", maxInterval), col="blue", font=2)
```

![plot of chunk avgactivity](figure/avgactivity-1.png)

## Imputing missing values

### Number of rows with missing values

```r
sum(is.na(activity))
```

```
## [1] 2304
```
### Replace missing step values with the rounded mean of the individual intervals 

```r
means <- aggregate(activity$steps, by=list(activity$interval), mean, na.rm=TRUE)
mean_map <- round(means[,2])
names(mean_map) <- means[,1]

activity_updated = activity
missing <- which(is.na(activity_updated))
activity_updated[missing,1] = mean_map[as.character(activity_updated[missing,3])]
sum(is.na(activity_updated))
```

```
## [1] 0
```

### Updated Total number of steps taken per day

```r
dailySteps <- aggregate(steps ~ date, data=activity_updated, FUN=sum)
hist(x=dailySteps$steps, breaks=20, main="Daily Steps Updated", xlab="Steps", col="lightblue")
```

![plot of chunk dailystepsupdated](figure/dailystepsupdated-1.png)

Replacing the missing values with the inverval averages only increased the
frequency of one histogram bar. This adds little insight into the data analysis.

### What is the updated mean and median total number of steps taken per day?

```r
mean(dailySteps$steps)
```

```
## [1] 10765.64
```

```r
median(dailySteps$steps)
```

```
## [1] 10762
```
Since the missing values were across the entirety of only 8 days, imputed values
had no noticeable effect on the mean or median.

## Are there differences in activity patterns between weekdays and weekends?

```r
activity_updated$daytype = factor(
        ifelse(weekdays(activity_updated$date) %in% c("Saturday", "Sunday"),
                "weekend", "weekday"))
table(activity_updated$daytype)
```

```
## 
## weekday weekend 
##   12960    4608
```
