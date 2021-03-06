---
title: "Reproducible Research: Peer Assessment 1"
output: 
  html_document:
    keep_md: true
---


```r
#install.packages("mice")
library(dplyr)
```

```
## 
## Attaching package: 'dplyr'
```

```
## The following objects are masked from 'package:stats':
## 
##     filter, lag
```

```
## The following objects are masked from 'package:base':
## 
##     intersect, setdiff, setequal, union
```

```r
library(ggplot2)
require(plyr)
```

```
## Loading required package: plyr
```

```
## ------------------------------------------------------------------------------
```

```
## You have loaded plyr after dplyr - this is likely to cause problems.
## If you need functions from both plyr and dplyr, please load plyr first, then dplyr:
## library(plyr); library(dplyr)
```

```
## ------------------------------------------------------------------------------
```

```
## 
## Attaching package: 'plyr'
```

```
## The following objects are masked from 'package:dplyr':
## 
##     arrange, count, desc, failwith, id, mutate, rename, summarise,
##     summarize
```

```r
require(Hmisc)
```

```
## Loading required package: Hmisc
```

```
## Loading required package: lattice
```

```
## Loading required package: survival
```

```
## Loading required package: Formula
```

```
## 
## Attaching package: 'Hmisc'
```

```
## The following objects are masked from 'package:plyr':
## 
##     is.discrete, summarize
```

```
## The following objects are masked from 'package:dplyr':
## 
##     src, summarize
```

```
## The following objects are masked from 'package:base':
## 
##     format.pval, units
```


```r
dat <- read.table("activity.csv",header=T,sep=",")

head(dat)
```

```
##   steps       date interval
## 1    NA 2012-10-01        0
## 2    NA 2012-10-01        5
## 3    NA 2012-10-01       10
## 4    NA 2012-10-01       15
## 5    NA 2012-10-01       20
## 6    NA 2012-10-01       25
```



```r
typeof(dat)
```

```
## [1] "list"
```
### create a new variable - dataFrame

```r
dt <- data.frame(dat)
head(dt)
```

```
##   steps       date interval
## 1    NA 2012-10-01        0
## 2    NA 2012-10-01        5
## 3    NA 2012-10-01       10
## 4    NA 2012-10-01       15
## 5    NA 2012-10-01       20
## 6    NA 2012-10-01       25
```



## What is mean total number of steps taken per day?
### 1. Calculate the total number of steps taken per day

```r
steps_per_day <- aggregate(steps ~ date, data=dt, FUN=sum)
```



```r
head(steps_per_day)
```

```
##         date steps
## 1 2012-10-02   126
## 2 2012-10-03 11352
## 3 2012-10-04 12116
## 4 2012-10-05 13294
## 5 2012-10-06 15420
## 6 2012-10-07 11015
```


## Make a histogram of the total number of steps taken each day

```r
hist(steps_per_day$steps, col="blue",xlab = "Total steps daily", 
        main = "Number of steps per day")
```

![](Rep_Research_files/figure-html/unnamed-chunk-7-1.png)<!-- -->

### Calculate the mean and median of the total number of steps taken per day

```r
median(steps_per_day$steps,na.rm = TRUE)
```

```
## [1] 10765
```

```r
mean(steps_per_day$steps,na.rm = TRUE)
```

```
## [1] 10766.19
```

## What is the average daily activity pattern?
### 1. Make a time series plot (i.e. type = “l”) of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)

```r
steps_i <- aggregate(steps ~ interval, data = dt, FUN = mean, na.rm = TRUE)
str(steps_i)
```

```
## 'data.frame':	288 obs. of  2 variables:
##  $ interval: int  0 5 10 15 20 25 30 35 40 45 ...
##  $ steps   : num  1.717 0.3396 0.1321 0.1509 0.0755 ...
```

## timeseries graph

```r
plot(steps_i$interval,steps_i$steps,type="l",xlab="time = 5 min",
        ylab="Average number of steps",main="Average daily activity pattern")
```

![](Rep_Research_files/figure-html/unnamed-chunk-10-1.png)<!-- -->

## 2. Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?

```r
max_i <- steps_i[which.max(steps_i$steps),]
max_i
```

```
##     interval    steps
## 104      835 206.1698
```


## Imputing missing values
### 1. Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)

```r
cout_missing_values <- sum(is.na(dt$steps))
cout_missing_values
```

```
## [1] 2304
```



### 2. Fill in all of the missing values in the dataset


### 3. Create a new dataset that is equal to the original dataset but with the missing data filled in.



```r
dt_Imputed <- ddply(dt, "interval", mutate, imputed.value = impute(steps, mean))
#dt_Imputed <- ddply(dt$steps, mean)
```



```r
median(dt_Imputed$steps,na.rm = TRUE)
```

```
## [1] 0
```

```r
mean(dt_Imputed$steps,na.rm = TRUE)
```

```
## [1] 37.3826
```


## alternative way


```r
fillITin <-  function(x){

x$steps[is.na(x$steps)] <- ave(x$steps,  
                                     FUN = function(z) 
                                    mean(z, na.rm = TRUE))[c(which(is.na(x$steps)))]
return(x)
}


dt_Inputed_2 <- fillITin(dt)
```



```r
median(dt_Inputed_2$steps,na.rm = TRUE)
```

```
## [1] 0
```

```r
mean(dt_Inputed_2$steps,na.rm = TRUE)
```

```
## [1] 37.3826
```



### 3. Histogram of the total number of steps taken each day

```r
hist(dt_Inputed_2$steps, col="light blue",xlab = "Total steps daily", 
         main = "Number of steps per day with imputed values")
```

![](Rep_Research_files/figure-html/unnamed-chunk-17-1.png)<!-- -->


### 4. Calculate and report the mean total number of steps taken per day

```r
mean(dt_Inputed_2$steps,na.rm = TRUE)
```

```
## [1] 37.3826
```


### 5. Calculate and report the median total number of steps taken per day

```r
median(dt_Inputed_2$steps,na.rm = TRUE)
```

```
## [1] 0
```

## Are there differences in activity patterns between weekdays and weekends?
### 1. Create a new factor variable in the dataset with two levels - “weekday” and “weekend” indicating whether a given date is a weekday or weekend day.

```r
dt_Inputed_2$dateType <-  ifelse(as.POSIXlt(dt_Inputed_2$date)$wday %in% c(0,6), 'weekend', 'weekday')
```


### 2. Make a panel plot containing a time series plot (i.e. type = “l”) of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis)

```r
av_dt_Inputed_2 <- aggregate(steps ~ interval + dateType, data=dt_Inputed_2, mean)
ggplot(av_dt_Inputed_2, aes(interval, steps)) + 
    geom_line() + 
    facet_grid(dateType ~ .) +
    xlab("5-minute interval") + 
    ylab("avarage number of steps")
```

![](Rep_Research_files/figure-html/unnamed-chunk-21-1.png)<!-- -->
