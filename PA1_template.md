---
title: "Reproducible Research: Peer Assessment 1"
author: mlovejoy
output: PA1_template.md
  html_document: PA1_template.html
    keep_md: true
---


## Loading and preprocessing the data

```r
    install.packages("knitr")
```

```
## Error in contrib.url(repos, "source"): trying to use CRAN without setting a mirror
```

```r
    library(knitr)
```
### 1. Load the data

```r
    activity <- read.csv("activity.csv", header=TRUE)
```
### 2. Convert the date field into the proper format

```r
    activity$date <- as.Date(activity$date)
```
## What is mean total number of steps taken per day?

### Exclude NAs from the data for this question

```r
   activityrm <- activity[complete.cases(activity), ]
```
### 1. Steps per day table

```r
steps_perday <- tapply(activityrm$steps, activityrm$date, sum)
```
### 2. Histogram of steps per day

```r
    png("figure/HistogramStepsPerDay.png", width = 480, height = 480)
    
    hist(steps_perday, 10, main = "Histogram of Total Steps per Day", xlab = "Number of Steps")
    
    dev.off()
```

```
## quartz_off_screen 
##                 2
```
![plot of chunk histogram of total steps per day](figure/HistogramStepsPerDay.png)

### 3. Mean and median for total number of steps per day

```r
mean_perday <- mean(steps_perday)
mean_perday
```

```
## [1] 10766.19
```

```r
median_perday <- median(steps_perday)
median_perday
```

```
## [1] 10765
```
## What is the average daily activity pattern?

### Average steps per day by time interval table

```r
    daily_activity <- tapply(activityrm$steps, activityrm$interval, mean)
```
### 1. Plot the average number of steps for each interval

```r
    png("figure/PlotAvgDailyActivity.png", width = 480, height = 480)
    
    plot(y = daily_activity, x = names(daily_activity), type = "l", xlab = "5-Minute Interval", ylab = "Average Number of Steps", main = "Average Daily Activity Pattern")
    
    dev.off()
```

```
## quartz_off_screen 
##                 2
```
![plot of chunk average daily activity pattern](figure/PlotAvgDailyActivity.png)

### 2. Determine the 5-minute interval with most steps

```r
    daily_max <- daily_activity[which.max(daily_activity)]
    daily_max
```

```
##      835 
## 206.1698
```
## Imputing missing values

### 1. Calculate number of NAs in the data

```r
    sum(is.na(activity))
```

```
## [1] 2304
```
### 2. To handle the 2,304 records that contain NAs, I will substitute the NAs with the mean value for the 5-minute interval

### 3. Create new data set with missing values imputed

```r
activityImputed <- activity
activity_nas <- is.na(activityImputed$steps)
mean_interval <- tapply(activityImputed$steps, activityImputed$interval, mean, na.rm=TRUE, simplify=TRUE)
activityImputed$steps[activity_nas] <- mean_interval[as.character(activityImputed$interval[activity_nas])]
```

### 4. Histogram of total steps per day and mean/median

```r
steps_perdayImputed <- tapply(activityImputed$steps, activityImputed$date, sum)
    
mean_perdayImputed <- mean(steps_perdayImputed)
mean_perdayImputed
```

```
## [1] 10766.19
```

```r
median_perdayImputed <- median(steps_perdayImputed)
median_perdayImputed
```

```
## [1] 10766.19
```


```r
png("figure/HistogramImputedStepsPerDay.png", width = 480, height = 480)

hist(steps_perdayImputed, 10, main = "Histogram of Total Steps per Day (With Imputed Values)", xlab = "Number of Steps")

dev.off()
```

```
## quartz_off_screen 
##                 2
```
![plot of chunk histogram of imputed steps per day](figure/HistogramImputedStepsPerDay.png)

#### Note that mean steps per day did not change, while median steps per day changed from 10,765 to 10,766.19, a difference of 1.19 steps

## Are there differences in activity patterns between weekdays and weekends?

### 1. Create a new factor variable for weekend/weekday

```r
    activityImputed$weekday <- ifelse(weekdays(activityImputed$date) %in% c('Saturday', 'Sunday'), 'weekend', 'weekday')
```
### 2. Weekend vs. weekday plots

#### Subset for weekends and weekdays

```r
    activityImputedWE <- subset(activityImputed, weekday == "weekend")
    activityImputedWD <- subset(activityImputed, weekday == "weekday")
```
#### Calculate average number of steps each day by interval for weekends and weekdays

```r
    daily_activityImputedWE <- tapply(activityImputedWE$steps, activityImputedWE$interval, mean)
    daily_activityImputedWD <- tapply(activityImputedWD$steps, activityImputedWD$interval, mean)
```
#### Plots for weekend and weekday activity

```r
    png("figure/Weekend-WeekdayImputedAvgDailyActivity.png", width = 480, height = 480)
    
    par(mfrow=c(2, 1), mai = c(.5, .5, .5, .5))
    
    plot(y = daily_activityImputedWE, x = names(daily_activityImputedWE), type = "l", xlab = NA, ylab = "Average Number of Steps", main = "Average Daily Activity Pattern - Weekends")

    plot(y = daily_activityImputedWD, x = names(daily_activityImputedWD), type = "l", xlab = "5-Minute Interval", ylab = "Average Number of Steps", main = "Average Daily Activity Pattern - Weekdays")

    dev.off()
```

```
## quartz_off_screen 
##                 2
```
![plot of chunk weekend-weekday imputed daily activity](figure/Weekend-WeekdayImputedAvgDailyActivity.png)
