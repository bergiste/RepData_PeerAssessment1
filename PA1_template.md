# Reproducible Research: Peer Assessment 1


The following document represents an example of reproducible research. We will analyze a data set about user steps activity from a smart phone.

## Loading and preprocessing the data
We will load the data from the data source (a URL from the web), unzip it, save it locally, and read it into R for further analysis. This portion of code is also cached for efficiency.

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
summarizedData <- summarise(group_by(data, date),  
    total = sum(steps, na.rm = T)
)

hist(summarizedData$total, main = "Total Steps Per Day", xlab = "Number of Steps")
```

![](./PA1_template_files/figure-html/unnamed-chunk-2-1.png) 

```r
avgStepsDay <- ceiling(mean(summarizedData$total, na.rm= T))
medianStepsDay <- median(summarizedData$total, na.rm= T)
paste("Average number of steps", avgStepsDay)
```

```
## [1] "Average number of steps 9355"
```

```r
paste("Median number of steps", medianStepsDay)
```

```
## [1] "Median number of steps 10395"
```

## What is the average daily activity pattern?

```r
activityPattern <- summarise(group_by(data,interval), avgSteps = mean(steps, na.rm = T))
with(activityPattern, { plot(interval, avgSteps, type = 'l') })
```

![](./PA1_template_files/figure-html/unnamed-chunk-3-1.png) 

```r
activityPattern[activityPattern$avgSteps == max(activityPattern$avgSteps),1]
```

```
## Source: local data frame [1 x 1]
## 
##   interval
## 1      835
```

## Imputing missing values
See the total number of missing values. To fill in the missing values, we will calculate the average steps for each interval across the entire data set and use that average for any missing value with the corresponding interval.

```r
paste("Total missing values:", sum(is.na(data$steps) == TRUE))
```

```
## [1] "Total missing values: 2304"
```

```r
missingVals <- data[is.na(data$steps),c(2:3)]
hasVals <- data[is.na(data$steps) == FALSE,]
avgInterval <- summarise(group_by(hasVals, interval), steps = ceiling(mean(steps)))

filledData <- inner_join(missingVals, avgInterval, by = 'interval')

newData <- rbind(hasVals, filledData)

# Report on data set with filled in values
summarizedData <- summarise(group_by(newData, date),  
    total = sum(steps, na.rm = T)
)

hist(summarizedData$total, main = "Total Steps Per Day", xlab = "Number of Steps")
```

![](./PA1_template_files/figure-html/unnamed-chunk-4-1.png) 

```r
newAvgStepsDay <- ceiling(mean(summarizedData$total, na.rm= T))
newMedianStepsDay <- median(summarizedData$total, na.rm= T)
paste("Average number of steps", newAvgStepsDay, 
      "compared to", avgStepsDay, 
      "without imputing missing values. This is a difference of", newAvgStepsDay - avgStepsDay, "steps")
```

```
## [1] "Average number of steps 10785 compared to 9355 without imputing missing values. This is a difference of 1430 steps"
```

```r
paste("Median number of steps", newMedianStepsDay,
      "compared to", medianStepsDay, 
      "without imputing missing values. This is a difference of", newMedianStepsDay - medianStepsDay, "steps")
```

```
## [1] "Median number of steps 10909 compared to 10395 without imputing missing values. This is a difference of 514 steps"
```

## Are there differences in activity patterns between weekdays and weekends?

```r
library(lattice)
categorizedData <- mutate(newData, day = weekdays(as.Date(date)))
categorizedData <- mutate(categorizedData, dayCat = ifelse(day %in% c("Saturday", "Sunday"), "Weekend", "Weekday" ))

finalData <- summarise(group_by(categorizedData,interval, dayCat), avgSteps = mean(steps))

xyplot(avgSteps ~ interval | dayCat, data = finalData,layout=c(1,2),type="l", ylab = "Number of steps")
```

![](./PA1_template_files/figure-html/unnamed-chunk-5-1.png) 
