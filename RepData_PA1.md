# Reproducible Research: Peer Assessment 1
**Yongdan Tang**


*Global Option is set as echo = TRUE so all codes should be visible. Results are shown in markup brackets so they can be differentiated as well.* 


```r
require(knitr)
```

```
## Loading required package: knitr
```

```r
opts_chunk$set(echo = TRUE, results = "markup")
```


## Loading and preprocessing the data
1. set working directory properly
2. download the file from the coursera source url *the url need to be http instead of https*
3. unzip the file into the same directory and read it into R as a temp data.frame
4. format this temp data.frame as tbl_df using dplyr package for following use
5. this dataset has  17,568 observations and 3 variables


```r
wd_url <- "C:/Users/Yong/RepData_Assessment1/RepData_PeerAssessment1"
setwd(wd_url)

download.file("http://d396qusza40orc.cloudfront.net/repdata%2Fdata%2Factivity.zip", "activity.zip")
unzip("activity.zip")
temp <- read.csv(file = "activity.csv", na.strings = "NA")

library(dplyr)
```

```
## 
## Attaching package: 'dplyr'
## 
## The following object is masked from 'package:stats':
## 
##     filter
## 
## The following objects are masked from 'package:base':
## 
##     intersect, setdiff, setequal, union
```

```r
temp <- tbl_df(temp)
temp$date <- as.Date(temp$date)
print(temp)
```

```
## Source: local data frame [17,568 x 3]
## 
##    steps       date interval
## 1     NA 2012-10-01        0
## 2     NA 2012-10-01        5
## 3     NA 2012-10-01       10
## 4     NA 2012-10-01       15
## 5     NA 2012-10-01       20
## 6     NA 2012-10-01       25
## 7     NA 2012-10-01       30
## 8     NA 2012-10-01       35
## 9     NA 2012-10-01       40
## 10    NA 2012-10-01       45
## ..   ...        ...      ...
```


## What is mean total number of steps taken per day?
For this part of the assignment, ignore the missing values in the dataset.

1. Calculate the total number of steps taken per day. Group the dataset by date, then use summarise() function to calculate the total steps per day. 
2. Plot the histogram of total steps per day using hist() funciton
3. Calculate and report the mean and median of the total number of steps taken per day and report them afterwards



```r
per_day_steps <- summarise(group_by(temp, date), 
                           steps = sum(steps, na.rm = TRUE))

with(per_day_steps, hist(steps,
                         main = "Histogram of total number of steps taken per day", 
                         xlab = "steps"
                         ))
```

![](RepData_PA1_files/figure-html/calculate_steps_taken_per_day-1.png) 

```r
mean_steps <- mean(per_day_steps$steps, na.rm = TRUE)

median_steps <- median(per_day_steps$steps, na.rm = TRUE)

print(mean_steps)
```

```
## [1] 9354.23
```

```r
print(median_steps)
```

```
## [1] 10395
```

**The mean total steps taken per day is 9354.2295082.**
**The median total steps taken per day is 10395.**

## What is the average daily activity pattern?

1. Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis).
- make another factor variable called "time" as we know the 5-minute interval represents certain time in a day
- Calculate the averaged numbers of steps taken averaged across all days for the 5-minute interval using summarise() function group the steps by time
- Plot time-series steps using base plotting system. *since time is a factor variable, plot() function will call plot.factor() function which results in a scatter plot. calling lines() function again to connect the dots.*

2. Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?
- use the filter() function to extract the row which contains the maximum steps
- print the max result


```r
temp$time <- as.factor(temp$interval)
per_interval_avg_steps <- summarise(group_by(temp, time),
                                    steps = mean(steps, na.rm = TRUE))


with(per_interval_avg_steps, plot(time, 
                                  steps, 
                                  type = "n",
                                  main = "Averaged steps taken (acrossed all days) per 5 minute interval",
                                  xlab = "time",
                                  ylab = "steps"))
with(per_interval_avg_steps, lines(time, steps))
```

![](RepData_PA1_files/figure-html/average_daily_pattern-1.png) 

```r
max <- filter(per_interval_avg_steps, steps == max(steps))
print(max)
```

```
## Source: local data frame [1 x 2]
## 
##   time    steps
## 1  835 206.1698
```
**On average across all the days in the dataset, the 5-minute interval that contains the maximum number of steps is 835.**
**The 5-minute interval 835 contains 206.1698113 of steps.**


## Imputing missing values


## Are there differences in activity patterns between weekdays and weekends?
