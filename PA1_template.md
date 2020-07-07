`{r setup, include=FALSE} knitr::opts_chunk$set(echo = TRUE)`

R Markdown Assignment
---------------------

    data <- read.csv("activity.csv")  

3a.What is mean total number of steps taken per day?
----------------------------------------------------

    steps_by_day <- aggregate(steps ~ date, data, sum)
    hist(steps_by_day$steps, main = paste("Total Steps Each Day"), col="red",xlab="Number of Steps")

    rmean <- mean(steps_by_day$steps)
    rmean

    rmedian <- median(steps_by_day$steps)
    rmedian

The mean is 10766.19 and the median is 10765

\#\#3b.What is the average daily activity pattern? Calculate average
steps for each interval for all days Plot the Average Number Steps per
Day by Interval Find interval with most average steps

    steps_by_interval <- aggregate(steps ~ interval, data, mean)
    plot(steps_by_interval$interval,steps_by_interval$steps, type="l", xlab="Interval", ylab="Number of Steps",main="Average Number of Steps per Day by Interval")

    max_interval <- steps_by_interval[which.max(steps_by_interval$steps),1]
    max_interval

The interval with most steps is 835

\#\#3c.Imputing missing values

Calculate and report the total number of missing values in the dataset
(i.e. the total number of rows with NAs) Using Mean for the day compute
missing values Create a new dataset that is equal to the original
dataset but with the missing data filled in. Make a histogram of the
total number of steps taken each day and Calculate and report the mean
and median total number of steps taken per day.

\#1.Calculate and report the total number of missing values in the
dataset

    NATotal <- sum(!complete.cases(data))
    NATotal

Total Number of Missing values are 2304

\#2.Using Mean for the day compute missing values

    StepsAverage <- aggregate(steps ~ interval, data = data, FUN = mean)
    fillNA <- numeric()
    for (i in 1:nrow(data)) {
        obs <- data[i, ]
        if (is.na(obs$steps)) {
            steps <- subset(StepsAverage, interval == obs$interval)$steps
        } else {
            steps <- obs$steps
        }
        fillNA <- c(fillNA, steps)
    }

\#3. Create a new dataset including the imputed missing values

    new_activity <- data
    new_activity$steps <- fillNA

\#4. Make a histogram of the total number of steps taken each day and
Calculate and report the mean and median total number of steps taken per
day.

    StepsTotalUnion <- aggregate(steps ~ date, data = new_activity, sum, na.rm = TRUE)
    hist(StepsTotalUnion$steps, main = paste("Total Steps Each Day"), col="blue", xlab="Number of Steps")
    #Create Histogram to show difference. 
    hist(steps_by_day$steps, main = paste("Total Steps Each Day"), col="green", xlab="Number of Steps", add=T)
    legend("topright", c("Imputed", "Non-imputed"), col=c("blue", "green"), lwd=10)

\#Calculate Mean & Median

    rmeantotal <- mean(StepsTotalUnion$steps)
    paste("Mean=",rmeantotal)
    rmediantotal <- median(StepsTotalUnion$steps)
    paste("Median=",rmediantotal)

Do these values differ from the estimates from the first part of the
assignment?

    rmediandiff <- rmediantotal - rmedian
    paste("Mediandiff=",rmediandiff)
    rmeandiff <- rmeantotal - rmean
    paste("Meandiff=",rmeandiff)

\#\#3d.Are there differences in activity patterns between weekdays and
weekends? Created a plot to compare and contrast number of steps between
the week and weekend. There is a higher peak earlier on weekdays, and
more overall activity on weekends.

    weekdays <- c("Monday", "Tuesday", "Wednesday", "Thursday", 
                  "Friday")
    new_activity$dow = as.factor(ifelse(is.element(weekdays(as.Date(new_activity$date)),weekdays), "Weekday", "Weekend"))
    StepsTotalUnion <- aggregate(steps ~ interval + dow, new_activity, mean)
    library(lattice)
    xyplot(StepsTotalUnion$steps ~ StepsTotalUnion$interval|StepsTotalUnion$dow, main="Average Steps per Day by Interval",xlab="Interval", ylab="Steps",layout=c(1,2), type="l")
