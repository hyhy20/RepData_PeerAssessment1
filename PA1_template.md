Summary
-------

This assignment makes use of data from a personal activity monitoring
device. This device collects data at 5 minute intervals through out the
day. The data consists of two months of data from an anonymous
individual collected during the months of October and November, 2012 and
include the number of steps taken in 5 minute intervals each day.

This project inludes several parts:

1.  Code for reading in the dataset and/or processing the data

2.  Histogram of the total number of steps taken each day

3.  Mean and median number of steps taken each day

4.  Time series plot of the average number of steps taken

5.  The 5-minute interval that, on average, contains the maximum number
    of steps

6.  Code to describe and show a strategy for imputing missing data

7.  Histogram of the total number of steps taken each day after missing
    values are imputed

8.  Panel plot comparing the average number of steps taken per 5-minute
    interval across weekdays and weekends

All of the R code needed to reproduce the results (numbers, plots, etc.)
in the report

Code and plot
-------------

### Code for reading in the dataset and/or processing the data

    data <- read.csv("activity.csv")
    data$date <- as.Date(data$date)

### Histogram of the total number of steps taken each day

    a <- aggregate(data$steps, list(Date=data$date), sum)
    names(a) <- c("Date", "Total_Steps")
    hist(a$Total_Steps, breaks=7, xlab="Total steps in one day", 
         main="Histogram of one day's total steps")

![](PA1_template_files/figure-markdown_strict/Hist1-1.png)

### Mean and median number of steps taken each day

    summary(a)

    ##       Date             Total_Steps   
    ##  Min.   :2012-10-01   Min.   :   41  
    ##  1st Qu.:2012-10-16   1st Qu.: 8841  
    ##  Median :2012-10-31   Median :10765  
    ##  Mean   :2012-10-31   Mean   :10766  
    ##  3rd Qu.:2012-11-15   3rd Qu.:13294  
    ##  Max.   :2012-11-30   Max.   :21194  
    ##                       NA's   :8

### Time series plot of the average number of steps taken

    b <- aggregate(data$steps, list(Date=data$date), mean)
    b <- b[complete.cases(b),]
    names(b) <- c("Date", "Avg_Step")
    with(b, plot(Date, Avg_Step, main="Average steps in one day", type="l"))

![](PA1_template_files/figure-markdown_strict/avg_plot-1.png)

### The 5-minute interval that, on average, contains the maximum number of steps

    b[which.max(b$Avg_Step),]

    ##          Date Avg_Step
    ## 54 2012-11-23 73.59028

### Code to describe and show a strategy for imputing missing data

    library(impute)
    c <- as.data.frame(a)
    c$num <- seq(1:61)
    c <- as.data.frame(impute.knn(as.matrix(c[,-1]))$data)
    c <- as.data.frame(cbind(a$Date, c[,-2]))
    names(c) <- c("Date", "Total_Steps")

### Histogram of the total number of steps taken each day after missing values are imputed

    hist(as.numeric(c$Total_Steps), xlab="Total steps in one day", 
         main="Histogram of one day's total steps(After impute)")

![](PA1_template_files/figure-markdown_strict/hist2-1.png)

### Panel plot comparing the average number of steps taken per 5-minute interval across weekdays and weekends

    library(lattice)
    d <- as.data.frame(b)
    d$Date <- as.Date(d$Date)
    d$fac <- "Weekdays"
    d[weekdays(d$Date)=="Saturday"| weekdays(d$Date)
         =="Sunday",]$fac<-"Weekends"
    d$fac <- as.factor(d$fac)
    with(d, xyplot(Avg_Step~Date|fac, layout=c(2,1), type="l"))

![](PA1_template_files/figure-markdown_strict/plot2-1.png)
