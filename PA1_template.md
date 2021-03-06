# Reproducible Research: Peer Assessment 1
##Loading libraries required for analysis

```r
library(dplyr)  #for easy data manipulation
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
library(lattice) # for easy graphing
```

## Loading and preprocessing the data
Creating a data directory if it does not exist

```r
  if(!file.exists("Data")){
    dir.create("Data")
  }
```

Unzipping source file

```r
  unzip('activity.zip',exdir='./Data/')
```

Importing source file

```r
  Import <- read.csv(file='./Data/activity.csv',colClasses = c("numeric","Date","numeric") )
```

## What is mean total number of steps taken per day?


```r
Steps_Total <- Import %>%
                filter(steps != 'NA') %>%
                group_by(date) %>%
                summarise(Steps_Total = sum(steps))

Steps_Mean <- Steps_Total %>%
                summarise(
                          Steps_Mean = mean(Steps_Total)
                          ,Steps_Median = median(Steps_Total)
                          )

Step_Mean <- round(as.numeric(Steps_Mean[1,"Steps_Mean"]),1)
Step_Median <- round(as.numeric(Steps_Mean[1,"Steps_Median"]),1)
```
Creating a historgram of Total step by day

```r
hist(Steps_Total$Steps,xlab= "Total Steps by Day",main = "Histogram of Total Steps by Day")
```

![](PA1_template_files/figure-html/unnamed-chunk-6-1.png) 
  
The following figures are calculated from the total number of steps per day  
* mean: 1.07662\times 10^{4}  
* median: 1.0765\times 10^{4}  
  
## What is the average daily activity pattern?

```r
Steps_Time <- Import %>%
                filter(steps != "NA") %>%
                group_by(interval) %>%
                summarise(Steps_Mean = mean(steps))

plot(Steps_Time$Steps_Mean~Steps_Time$interval,type="l",xlab="interval",ylab="Mean Steps")
```

![](PA1_template_files/figure-html/unnamed-chunk-7-1.png) 

```r
Steps_Time_Max <- Steps_Time[Steps_Time$Steps_Mean == max(Steps_Time[,2]),]
```
  
The maximum number of steps, averaged across all days, is taken during 835 interval.  
  
## Imputing missing values


```r
#calculating the number of missing rows
Missing_Steps <- nrow(Import[complete.cases(Import) != TRUE,])

#calculating average steps per interval
Steps_Interval <- Import %>%
                          filter(steps != "NA") %>%
                          group_by(interval) %>%
                          summarise(Interval_Mean = mean(steps))

#merging average steps per interval onto intial import
Missing_Prep <- merge(Import,Steps_Interval,by.x="interval",by.y="interval",all.x=TRUE)

#filling in missing steps with interval
Missing_Fixed <- mutate(Missing_Prep,steps = ifelse(is.na(Missing_Prep$steps),Missing_Prep$Interval_Mean,Missing_Prep$steps))

#calculating total steps per day in fixed dataframe
Missing_Fixed_DateTotal <- Missing_Fixed %>%
                            group_by(date) %>%
                            summarise(Date_Total = sum(steps)
                                      )

#caculating mean and median from fixed dataset
Missing_Fixed_Steps_Mean <- Missing_Fixed_DateTotal %>%
                                summarise(
                                          Steps_Mean = mean(Date_Total)
                                          ,Steps_Median = median(Date_Total)
                                          )

#generating histogram of total steps by day (fixed dataframe)
hist(Missing_Fixed_DateTotal$Date_Total,xlab="Total Steps per Day",main="Histogram Total Steps per Day (with NA's imputed)")
```

![](PA1_template_files/figure-html/unnamed-chunk-8-1.png) 
  
There are 2304 number of rows with missing values.  
The following figures were calculated with missing values imputed:  
* mean: 1.0766189\times 10^{4} (originally: 1.07662\times 10^{4})  
* median: 1.0766189\times 10^{4} (originally: 1.0765\times 10^{4})
  
## Are there differences in activity patterns between weekdays and weekends?

```r
#adding day of the week to dataframe
Missing_Fixed$Day <- weekdays(Missing_Fixed$date)

#adding whether it is a weekday or weekend
Missing_Fixed$Weekday <- ifelse(Missing_Fixed$Day == "Saturday" | Missing_Fixed$Day == "Saturday","weekend","weekday")

#grouping weekend and weekday steps
Weekday_Steps <- Missing_Fixed %>%
                  group_by(Weekday,interval) %>%
                  summarise(Steps_Interval = sum(steps))

#plotting weekend and weekday steps
xyplot(Steps_Interval ~ interval |Weekday,data=Weekday_Steps,type="l",xlab="Interval",ylab="Number of steps",layout=c(1,2))
```

![](PA1_template_files/figure-html/unnamed-chunk-9-1.png) 
