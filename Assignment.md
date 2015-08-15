# Reproducible Research: Peer Assessment 1

The following document represents an example of reproducible research. We will analyze a data set about user steps activity from a smart phone.

## Loading and preprocessing the data

```r
url <- "http://d396qusza40orc.cloudfront.net/repdata%2Fdata%2Factivity.zip"
fileName <- "data/activity.zip"
download.file(url, destfile = fileName, method = "curl")

unzip(fileName, exdir = "data")

data <- read.csv("data/activity.csv")
```
## What is mean total number of steps taken per day?

```r
library(dplyr)
library(ggplot2)
summarizedData <- summarise(group_by(data, date),  
    total = sum(steps, na.rm = T)
)

hist(summarizedData$total, main = "Total Steps Per Day", xlab = "Number of Steps")
```

![](./Assignment_files/figure-html/unnamed-chunk-2-1.png) 

```r
mean(summarizedData$total, na.rm= T)
```

```
## [1] 9354.23
```

```r
median(summarizedData$total, na.rm= T)
```

```
## [1] 10395
```

## What is the average daily activity pattern?

```r
activityPattern <- summarise(group_by(data,interval), avgSteps = mean(steps, na.rm = T))
with(activityPattern, { plot(interval, avgSteps, type = 'l') })
```

![](./Assignment_files/figure-html/unnamed-chunk-3-1.png) 

```r
activityPattern[activityPattern$avgSteps == max(activityPattern$avgSteps),]
```

```
## Source: local data frame [1 x 2]
## 
##   interval avgSteps
## 1      835 206.1698
```

## Imputing missing values


## Are there differences in activity patterns between weekdays and weekends?

