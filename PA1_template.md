# Reproducible Research: Peer Assessment 1


## Loading and preprocessing the data
1. Load the data (i.e. read.csv())
2. Process/transform the data (if necessary) into a format suitable for your analysis

```r
unzip("activity.zip")
a_df <- read.csv(file = "activity.csv", sep = ",", header = TRUE)
a_df_na <- a_df 
a_df <- a_df[!is.na(a_df$steps),]
```


## What is mean total number of steps taken per day?
1. Make a histogram of the total number of steps taken each day.

```r
StepsPerDay <- aggregate(x = a_df$steps, by=list("Date"= a_df$date), sum)
names(StepsPerDay)[names(StepsPerDay)=="x"] <- "Steps"
hist(StepsPerDay$Steps, main = "Histogram of the total number of steps taken each day", xlab = "Steps")
```

![](PA1_template_files/figure-html/unnamed-chunk-2-1.png)<!-- -->
2.Calculate and report the mean and median total number of steps taken per day


```r
mean(StepsPerDay$Steps)
```

```
## [1] 10766.19
```

```r
median(StepsPerDay$Steps)
```

```
## [1] 10765
```

## What is the average daily activity pattern?
1.Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)


```r
#TotalDays <- length(levels(a_df$date))
StepsPerInterval  <- aggregate(x = a_df$steps, by = list("5_Min_Interval" = a_df$interval), mean)
names(StepsPerInterval)[names(StepsPerInterval)=="x"] <- "Steps"
plot(x = StepsPerInterval$`5_Min_Interval`, y = StepsPerInterval$Steps, type = "l", 
     main = "Average number of steps in 5 - minute interval acorss all Days", xlab = "5 Min Interval (24 hours)", ylab = "Steps")
```

![](PA1_template_files/figure-html/unnamed-chunk-4-1.png)<!-- -->

2. Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?

```r
StepsPerInterval[StepsPerInterval$Steps == max(StepsPerInterval$Steps),]$`5_Min_Interval`
```

```
## [1] 835
```
## Imputing missing values
1. Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)

```r
a_df_na$NAs <- ifelse(is.na(a_df_na$steps), TRUE, FALSE)
nrow(a_df_na[a_df_na$NAs == TRUE,])
```

```
## [1] 2304
```
2. Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.
3.Create a new dataset that is equal to the original dataset but with the missing data filled in.

```r
missLogic <- (a_df_na$NAs & which(a_df_na$interval == StepsPerInterval$`5_Min_Interval`))
a_df_na$newSteps <- ifelse((a_df_na$NAs & a_df_na$interval == StepsPerInterval$`5_Min_Interval`), StepsPerInterval$Steps, a_df_na$steps)
```
4.1 Make a histogram of the total number of steps taken each day   

```r
newStepsPerDay <- aggregate(x=a_df_na$newSteps, list("Date" = a_df_na$date), sum)
names(newStepsPerDay)[names(newStepsPerDay) == "x"] <- "steps"
hist(newStepsPerDay$steps, main = "Histogram of total number of steps taken each day w/ Imputed Data", xlab = "Steps")
```

![](PA1_template_files/figure-html/unnamed-chunk-8-1.png)<!-- -->
  
4.2 Calculate and report the mean and median total number of steps taken per day.

```r
mean(newStepsPerDay$steps)
```

```
## [1] 10766.19
```

```r
median(newStepsPerDay$steps)
```

```
## [1] 10766.19
```
4.3 Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?

The difference of imputing minus original data mean: 0   
The difference of imputing minus original data median: -1.1886792

## Are there differences in activity patterns between weekdays and weekends?
5.1 Create a new factor variable in the dataset with two levels -- "weekday" and "weekend" indicating whether a given date is a weekday or weekend day.

```r
week_day <- c("Monday", "Tuesday", "Wednesday", "Thursday", "Friday")
a_df_na$weekday <- weekdays(as.Date(a_df_na$date))
a_df_na$week <- factor(ifelse(weekdays(as.Date(a_df_na$date)) %in% week_day, "weekday", "weekend"))
```
5.2 Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). The plot should look something like the following, which was created using simulated data:


```r
Steps_Interval_Impute <- aggregate( steps~interval+week, a_df_na,mean)
library(ggplot2)
ggplot(Steps_Interval_Impute, aes(x = interval, y = steps)) + geom_line(aes(colour = week)) +
 facet_grid(week~.) + ggtitle("No of average Steps by Weekend and Weekday")
```

![](PA1_template_files/figure-html/unnamed-chunk-11-1.png)<!-- -->
