---
title: "Reproducible Research: Peer Assessment 1"
output: html_document
---

```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = TRUE)
```

This is an R Markdown document for Coursera Reproducible Research course's assignment.

Libraries used in this assignment are as follows:


```{r}
library(knitr)
library(ggplot2)
library(readr)
```
Reading data set from a CSV file.

```{r results='hold'}
activity <- read_csv("activity.csv")
```



### What is mean total number of steps taken per day?
Histogram of the total number of steps taken each day
```{r}
totalStepPerDay <- aggregate(activity$steps, by=list(activity$date), sum)
colnames(totalStepPerDay) <- c("date", "step")
hist(totalStepPerDay$step, xlab = "Total Step", main = "Total Step Per Each Day")
```
Saving the image
```{r}
dev.print(png, "plot1.png", width = 1000, height = 1000)
```
Calculating mean and median
```{r}
summary(totalStepPerDay)
```



### What is the average daily activity pattern?
```{r}
data2 <- aggregate(activity$steps ~ activity$interval, activity, mean)
colnames(data2) <- c("interval", "step")
plot(data2$interval, data2$step, type = "l", xlab = "Interval", ylab = "Steps", main = "Average Daily Activity Pattern")
data2[which.max(data2$step), ]
```
Save the image
```{r}
dev.print(png, "plot2.png", width = 1000, height = 1000)
```



### Imputing missing values
Total number of missing values
```{r}
sum(is.na(activity))
```
I prefered to fill missing values with the average of all days' step
Get average remove NA first
```{r}
noNA <- na.omit(totalStepPerDay)
```
Get average 
```{r}
avg <- floor(mean(noNA$step))
```
New data set
```{r}
activity2 <- activity
```
Fill NA with average
```{r}
activity2$steps[is.na(activity2$steps)] <- avg
```
Histogram
```{r}
totalStepPerDay2 <- aggregate(activity2$steps, by=list(activity2$date), sum)
colnames(totalStepPerDay2) <- c("date", "step")
hist(totalStepPerDay2$step, xlab = "Total Step", main = "Total Step Per Each Day")
```
Save image
```{r}
dev.print(png, "plot3.png", width = 1000, height = 1000)
```
Mean 
```{r}
meean <- mean(totalStepPerDay2$step)
```
Median
```{r}
meedian <- median(totalStepPerDay2$step)
```
Mean changed a lot but median did changed slightly



### Are there differences in activity patterns between weekdays and weekends?
```{r}
activity3 <- activity
```
I use local date-time so my days are written in Turkish.

`Cumartesi - Saturday`

`Pazar - Sunday`
```{r}
activity3["days"] <- weekdays(as.Date(activity3$date))
activity3$days[activity3$days == "Cumartesi"] <- "weekend"
activity3$days[activity3$days == "Pazar"] <- "weekend"
activity3$days[activity3$days != "weekend"] <- "weekday"
totalStepPerDay3 <- aggregate(steps ~ interval + days, activity3, mean)
```
Plotting
```{r}
g <- ggplot(totalStepPerDay3, aes(x = interval, y = steps))
g + geom_bar(stat="identity") + facet_grid(~days) + labs(x = "Interval", y = "Steps", title = "Activity Patterns (Weekdays vs Weekend)")
```
Save image
```{r}
dev.print(png, "plot4.png", width = 1000, height = 1000)
```
