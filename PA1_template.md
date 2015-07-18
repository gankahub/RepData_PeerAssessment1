# Reproducible Research: Peer Assessment 1


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
  Import <- read.csv(file='./Data/activity.csv')
```

## What is mean total number of steps taken per day?



## What is the average daily activity pattern?



## Imputing missing values



## Are there differences in activity patterns between weekdays and weekends?
