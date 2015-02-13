# Reproducible Research: Peer Assessment 1


## Loading and preprocessing the data
Show any code that is needed to

1. Load the data (i.e. read.csv())

```r
setwd("e:/rdata/repdata_peerassessment1")
#unzip("activity.zip")
data<-read.csv("activity.csv")
```

2. Process/transform the data (if necessary) into a format suitable for your analysis




## What is mean total number of steps taken per day?

For this part of the assignment, you can ignore the missing values in the dataset.

1. Make a histogram of the total number of steps taken each day

```r
stepbyday_sum<-tapply(data$steps,data$date,sum)
hist(stepbyday_sum, xlab="total steps by day",ylab="frequency (Days)",main="number of daily steps", breaks=53)
```

![](PA1_template_files/figure-html/unnamed-chunk-3-1.png) 

2. Calculate and report the mean and median total number of steps taken per day

```r
mean(stepbyday_sum,na.rm=TRUE)
```

```
## [1] 10766.19
```

```r
median(stepbyday_sum,na.rm=TRUE)
```

```
## [1] 10765
```


## What is the average daily activity pattern?
1. Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)

```r
stepbyinterval_mean<-tapply(data$steps,data$interval,mean,na.rm=TRUE)
plot(stepbyinterval_mean,xlab="total steps by interval",ylab="average number of steps",main="daily activity pattern",type="l")
```

![](PA1_template_files/figure-html/unnamed-chunk-5-1.png) 

2. Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?

```r
data$interval[max(stepbyinterval_mean)]
```

```
## [1] 1705
```


## Imputing missing values
Note that there are a number of days/intervals where there are missing values (coded as NA). The presence of missing days may introduce bias into some calculations or summaries of the data.

1. Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)

```r
sum(is.na(data$steps))
```

```
## [1] 2304
```

2. Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.
strategy:fill the missing values with the mean for interval.


```r
data$newsteps<-data$steps
for (i in 1:288){
    if(is.na(data[i,1])){
        data[i,4]<-as.data.frame(stepbyinterval_mean)[((i-1)%%288+1),1]
    }
}
```

3. Create a new dataset that is equal to the original dataset but with the missing data filled in.

```r
newdata<-data.frame(data[,4],data[,2],data[,3])
names(newdata)<-c("steps","date","interval")
```

4. Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?

```r
newstepbyday_sum<-tapply(newdata$steps,newdata$date,sum)
hist(newstepbyday_sum, xlab="total steps by day",ylab="frequency (Days)",main="number of daily steps(new)", breaks=61)
```

![](PA1_template_files/figure-html/unnamed-chunk-10-1.png) 

```r
mean(newstepbyday_sum,na.rm=TRUE)
```

```
## [1] 10766.19
```

```r
median(newstepbyday_sum,na.rm=TRUE)
```

```
## [1] 10765.59
```
These values are differ from the estimates from the first part of the assignment.

After imputing missing data, some new data were added.(i.e. 2012-10-01)


## Are there differences in activity patterns between weekdays and weekends?
For this part the weekdays() function may be of some help here. Use the dataset with the filled-in missing values for this part.

1. Create a new factor variable in the dataset with two levels -- "weekday" and "weekend" indicating whether a given date is a weekday or weekend day.

```r
newdata$wd<-weekdays(as.Date(newdata[,2]))
newdata$wd[newdata$wd=="星期六"|newdata$wd=="星期日"]<-"weekend"
newdata$wd[newdata$wd %in% c("星期一","星期二","星期三","星期四","星期五")]<-"weekday"
```

2. Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). The plot should look something like the following, which was created using simulated data:

```r
stepsbyinterval_weekday_mean<-tapply(subset(newdata,wd=="weekday")$steps,subset(newdata,wd=="weekday")$interval,mean,na.rm=TRUE)
stepsbyinterval_weekend_mean<-tapply(subset(newdata,wd=="weekend")$steps,subset(newdata,wd=="weekend")$interval,mean,na.rm=TRUE)
par(mfrow = c(2, 1))
plot(stepsbyinterval_weekday_mean, type="l", xlab="5 minute interval", ylab="Average number of steps", main="weekdays")
plot(stepsbyinterval_weekend_mean, type="l", xlab="5 minute interval", ylab="Average number of steps", main="weekends")
```

![](PA1_template_files/figure-html/unnamed-chunk-12-1.png) 
