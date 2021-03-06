# Reproducible Research: Peer Assessment 1
## Analyzing Data from a Personal Activity Monitoring Device 
&nbsp;&nbsp;
&nbsp;&nbsp;
&nbsp;&nbsp;
&nbsp;&nbsp;
&nbsp;&nbsp;

## Introduction
Personal activity monitoring devices such as Fitbit, Nike Fuelband, and Jawbone Up are becoming very popular for people who would like to monitor their activities to improve their health, to find patterns in their behavior, etc. The data from these devices are often difficult to obtain and there exists the need to improve upon the processing and analysis of these types of data.  
&nbsp;&nbsp;
&nbsp;&nbsp;

## The Data
The original data set was downloaded from Coursera's Reproducible Research class web site on February 03, 2015 and is contained in the activity.zip file which can be found in this repository. Measurements were taken from a personal activity device worn by an anonymous individual whose activities were recorded for two months from October to November of 2012. The downloaded data set conists of the number of steps taken by the individual in each five-minute interval for each day during that two-month period.  
&nbsp;&nbsp;
&nbsp;&nbsp;  
The variables in this data set are:
- **steps**:&nbsp;&nbsp;&nbsp;&nbsp;the number of steps taken in each 5-minute interval (missing values are recorded as 'NA')
- **date**:&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;the date on which the measurement was taken (YYYY-MM-DD)
- **interval**:&nbsp;the identifier for the 5-minute interval.  
These are discrete and not continuous identifiers. The first 5-minute interval of each day has an identifier of 0. The interval from 55 minutes to 60 minutes has an identifier of 55 and is followed by the identifier 100 which identifies the interval from 1:00 am to 1:05 am. This pattern continues for the range of identifiers. 155 (1:55 am) is followed by 200 (2:00 am) and so on.
&nbsp;&nbsp;
&nbsp;&nbsp;

## Study Design
Can we identify any discernable patterns of a person's physical activity based on the number of daily steps that they take over a period of two months?
In order to address this question, analysis has been conducted to answer the following:
- What is the mean total number of steps taken per day?  
This question is addressed by taking the mean number of steps per day calculated both with the missing (NA) data removed and with the missing data imputed with values from the means of the respective intervals across the days. The data is presented in two separate histograms to determine the impact of imputing missing data on the estimates of the total daily number of steps.
- What is the average daily activity pattern?  
This question is addressed by taking the measurements from each interval averaged across all of the days. The data is presented in a time-series plot.
- Are there differences in activity patterns between weekdays and weekends?  
This question is addressed using the imputed data set and taking the measurements from each interval averaged across all of the weekdays and the measurements from each interval averaged across all of the weekend days. The data is presented in two separate time-series plots.
&nbsp;&nbsp;
&nbsp;&nbsp;

## The Analysis
The code for the analysis was tested on Mac OS X 10.10.2, R 3.1.2 with dplyr 0.4.1 and ggplot2 1.0.0  
The analysis is broken down into four major sections to address the questions put forth in the Study Design. Each section consists of code and the output from that code as either charts or values such as a mean. Each section closes with a discussion of the output. 


#### Load necessary packages and create base data frame

```r
library(dplyr)
library(ggplot2)
# create data frame from original data
activity_df <- read.csv('activity.csv')
```

### Section 1: Total Steps Taken per Day
#### Create new data frame which collapses base data frame to total number of steps taken in each day

```r
# create total-steps-per-day data frame.
tspd_df <- summarize(group_by(activity_df[!is.na(activity_df$steps),], date), totalsteps = sum(steps))
```

#### Plot histogram of the total number of steps taken per day

```r
# plot histogram of number of days vs total steps per day.
# scaled to match equivalent histogram of data with imputed values so that the two histograms are more easily compared.
hist1 <- ggplot(tspd_df, aes(x = totalsteps / 1000))
hist1 <- hist1 + geom_histogram(color = 'black', fill = 'steelblue', binwidth = 0.25) +
    coord_cartesian(xlim = c(0, 22), ylim = c(0, 11)) +
    scale_x_continuous(breaks = seq(0, 22, by = 1)) +
    labs(title = 'Histogram (binwidth = 250 steps)\nNumber of Days with Total Steps per Day (NA Values Removed)') +
    labs(x = 'Total Steps x 1000', y = 'Number of Days') +
    theme(plot.title = element_text(hjust = 0.5, size = 25, colour = 'black', face = 'bold')) +
    theme(axis.title.x = element_text(size = 20, colour = 'black', face = 'bold')) +
    theme(axis.title.y = element_text(size = 20, colour = 'black', face = 'bold')) +
    theme(axis.text = element_text(size = 20, colour = "black", face = 'bold'))
hist1
```

![plot of chunk Histogram1](figure/Histogram1-1.png) 

#### Calculate and report the mean and median of the total number of steps taken per day

```r
mean_tspd <- round(mean(tspd_df$totalsteps))
median_tspd <- round(median(tspd_df$totalsteps))
```
The mean and median of the total steps per day are 1.0766 &times; 10<sup>4</sup> and 1.0765 &times; 10<sup>4</sup>, respectively.  

### Discussion of Histogram above and the Mean and Median Total Steps per Day
The mean and median are almost identical which is not surprising as the histogram shows a fairly even distribution. The histogram has been scaled to match that of the histogram created with the imputed data set so as to make a visual comparison easier.  
&nbsp;&nbsp;
&nbsp;&nbsp;

### Section 2: Average Daily Activity Pattern
#### Create new data frame which collapses base data frame to mean number of steps taken per interval across days.

```r
mspi_df <- summarize(group_by(activity_df, interval), 'meansteps' = as.integer(mean(steps, na.rm = TRUE)))
```

#### Time-series plot of the number of steps taken per interval averaged across all days

```r
time_labels = c('00:00', '01:00', '02:00', '03:00', '04:00', '05:00', '06:00', '07:00', '08:00', '09:00', '10:00', '11:00', '12:00', '13:00', '14:00', '15:00', '16:00', '17:00', '18:00', '19:00', '20:00', '21:00', '22:00', '23:00', '00:00')
tsp1 <- ggplot(mspi_df, aes(interval, meansteps))
tsp1 <- tsp1 + geom_line(color = 'steelblue') + scale_x_discrete(labels = time_labels, breaks = seq(0, 2400, by = 100)) +
    labs(title = 'Number of Steps per 5-minute Interval Averaged Across Days') +
    labs(x = 'Intervals', y = 'Number of Steps (Mean)') +
    theme(plot.title = element_text(hjust = 0.5, size = 25, colour = 'black', face = 'bold')) +
    theme(axis.title.x = element_text(size = 20, colour = 'black', face = 'bold')) +
    theme(axis.text.x = element_text(angle = 45)) +
    theme(axis.title.y = element_text(size = 20, colour = 'black', face = 'bold')) +
    theme(axis.text = element_text(size = 15, colour = "black", face = 'bold'))
tsp1
```

![plot of chunk TimeSeriesPlot1](figure/TimeSeriesPlot1-1.png) 

#### The interval which contains the most amount of steps on average

```r
ims <- mspi_df[mspi_df$meansteps == max(mspi_df$meansteps), 1]
```
The interval with maximum number of steps averaged across days is 835.  

### Discussion of Daily Activity Pattern
On average it appears that the subject is not very active from 21:00 to 05:00 the next day. The data implies that the subject gets to sleep anywhere between 21:00 and 24:00 and wakes shortly after 05:00 on average. The subject's physical activity appears to drop between the hours 09:00-16:00, indicative of someone who may hold a sedentary desk job. There are four major peaks worth noting... 08:00-09:00, 11:00-12:00, 15:00-16:00, and 18:00-19:00. We can speculate on what this pattern means. The first peak may represent a morning commute to a job. The second peak may signify the time at which the subject normally takes a lunch break. The third peak may represent some activity during an afternoon break. The fourth peak may signify an afternoon commute back to home. It's interesting to note the difference between the first peak and the fourth peak, the first peak involving more steps and containing the average maximum number of steps which occurs in the 5-minute interval from 08:35-08:40 as shown in the calculation above. This may represent a morning exercise regimen or possibly the subject walks to work in the morning and then takes the metro home.  
&nbsp;&nbsp;
&nbsp;&nbsp;

### Section 3: Affect of Imputing Missing Values
#### The number of missing values in the data set

```r
mvalues <- sum(is.na(activity_df$steps))
```
The number of missing values in the base data set is 2304  

#### How are missing values distributed?

```r
summarize(group_by(activity_df[is.na(activity_df$steps),], date), count = n())
```

```
## Source: local data frame [8 x 2]
## 
##         date count
## 1 2012-10-01   288
## 2 2012-10-08   288
## 3 2012-11-01   288
## 4 2012-11-04   288
## 5 2012-11-09   288
## 6 2012-11-10   288
## 7 2012-11-14   288
## 8 2012-11-30   288
```

#### Create new data set from base data set with missing values imputed from the means of the respective intervals across all days. Create new data frame which collapses the imputed data frame to total number of steps taken per day

```r
iactivity_df = activity_df %>% group_by(interval) %>% mutate(steps = ifelse(is.na(steps), as.integer(mean(steps, na.rm = TRUE)), steps))
itspd_df <- summarize(group_by(iactivity_df, date), totalsteps = sum(steps))
```

#### Plot histogram with the imputed data of the total number of steps taken each day

```r
hist2 <- ggplot(itspd_df, aes(x = totalsteps / 1000))
hist2 <- hist2 + geom_histogram(color = 'black', fill = 'steelblue', binwidth = 0.25) + 
    coord_cartesian(xlim = c(0, 22), ylim = c(0, 11)) +
    scale_x_continuous(breaks = seq(0, 22, by = 1)) +
    labs(title = 'Histogram (binwidth = 250 steps)\nNumber of Days with Total Steps per Day\n(NA Values Imputed with Mean from Respective Interval)') +
    labs(x = 'Total Steps x 1000', y = 'Number of Days') +
    theme(plot.title = element_text(hjust = 0.5, size = 25, colour = 'black', face = 'bold')) +
    theme(axis.title.x = element_text(size = 20, colour = 'black', face = 'bold')) +
    theme(axis.title.y = element_text(size = 20, colour = 'black', face = 'bold')) +
    theme(axis.text = element_text(size = 20, colour = "black", face = 'bold'))
hist2
```

![plot of chunk Histogram2](figure/Histogram2-1.png) 

#### From the data set with the imputed values, calculate and report the mean and median of the total number of steps taken per day

```r
mean_itspd <- round(mean(itspd_df$totalsteps))
median_itspd <- round(median(itspd_df$totalsteps))
```
The mean and median of the total steps per day in the imputed data set are 1.075 &times; 10<sup>4</sup> and 1.0641 &times; 10<sup>4</sup>, respectively.  

### Discussion of the Impact of Imputing Missing Values
In the data set there are 2,304 intervals with missing values for number of steps. There are 288 5-minute intervals in a 24-hour period. 2,304 represents 8 whole days of missing data. Inspection of the data reveals that there are exactly 8 different days where data was not recorded. The missing values do not represent days with sporadic interval-data collection.  The missing values have been imputed with data from the means of the respective intervals. Considering the distribution of the data, imputing with the mean or median from the respective intervals or with the mean or median of the total number of steps per day should not be much different. Imputing missing values in this way should not change the data by much as we are simply adding values to the bin with the mean number of steps per day. All other histogram bins should stay the same. Comparing the histograms shows this to be the case. The mean and median of the total number of steps drop only slightly with only a slight skew towards days with lower number of steps as signified by a slightly larger mean than median of the imputed data.  
&nbsp;&nbsp;
&nbsp;&nbsp;

### Section 4: Comparing Weekday Activity Patterns to Weekend day Activity Patterns
#### Add factor data to the imputed data frame which categorizes a day as either a weekday or a weekend day. From this data frame create another which contains the means of steps taken for each interval averaged across either the weekdays or the weekend days.

```r
iactivity_df <- ungroup(iactivity_df)
iactivity_df <- mutate(iactivity_df, dow = ifelse(weekdays(as.Date(iactivity_df$date)) %in% c('Saturday', 'Sunday'), 'WEEKEND', 'WEEKDAY'))
iactivity_df$dow <- as.factor(iactivity_df$dow)
iactivity_df <- summarize(group_by(iactivity_df, dow, interval), 'meansteps' = mean(steps))
```

#### Two time-series plots of the number of steps taken per interval averaged across either all weekdays or all weekend days.

```r
tsp2 <- ggplot(iactivity_df, aes(interval, meansteps))
tsp2 <- tsp2 + geom_line(color = 'steelblue') + facet_wrap(~dow, ncol = 1) +
    scale_x_discrete(labels = time_labels, breaks = seq(0, 2400, by = 100)) +
    labs(title = 'Number of Steps per 5-minute Interval Averaged Across Days\nAverage Weekday Compared to Average Weekend day') +
    labs(x = 'Intervals', y = 'Number of Steps (Mean)') +
    theme(plot.title = element_text(hjust = 0.5, size = 25, colour = 'black', face = 'bold')) +
    theme(axis.title.x = element_text(size = 20, colour = 'black', face = 'bold')) +
    theme(axis.title.y = element_text(size = 20, colour = 'black', face = 'bold')) +
    theme(axis.text = element_text(size = 15, colour = "black", face = 'bold') ) +
    theme(axis.text.x = element_text(angle = 45)) +
    theme(strip.text.x = element_text(size = 20, color = 'black', face = 'bold'))
tsp2
```

![plot of chunk TimeSeriesPlot2](figure/TimeSeriesPlot2-1.png) 

### Discussion of the Differences in Activity Patterns Between Weekdays and Weekend Days
Considering the fact that there are more weekdays than weekend days, it is likely that averaging the number of steps in an interval across all days will mask weekend activity patterns; therefore, a separate analysis of activity was conducted for the weekdays and the weekend days. It is not surprising that the activity pattern shown for the weekdays is not much different than the activity pattern for the conglomerate of all days. Comparing the time series plot of the weekdays to the weekend days implies that the subject seems to wake a bit later on weekend days, that the subject conducts more physical activity thoughout an average weekend day than they do for an average weekday, and that the subject is slightly more active in the period from 22:00 to 24:00 on weekends... a weekday/weekend pattern of someone who may hold a sedantary desk job during the week and is fairly physically active in their free time.  
&nbsp;&nbsp;
&nbsp;&nbsp;

### Conclusion
The aim of this study was to determine, analyzing data from a personal activity monitoring device, whether we can identify any discernable patterns of a person's daily physical activity based on the number of steps in five minute increments that they take over a period of two months. It is clear from the time-series plots that identifying discernable patterns is possible, that several daily physical-activity habits can be inferred from the data, and that weekday patterns are different than weekend patterns for this particular subject.
