---
title: "test0"
author: "SHL"
date: "2/17/2021"
output: 
 html_document: 
   keep_md: true
---



## R Markdown

This is an R Markdown document. Markdown is a simple formatting syntax for authoring HTML, PDF, and MS Word documents. For more details on using R Markdown see <http://rmarkdown.rstudio.com>.

When you click the **Knit** button a document will be generated that includes both content as well as the output of any embedded R code chunks within the document. You can embed an R code chunk like this:


```r
library(dplyr)
```

```
## Warning: package 'dplyr' was built under R version 4.0.3
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
# Read in data; Eliminate NAs;  Sum by date ; Draw histogram
data<-read.csv("C:/Users/Samford/tmpdir/eda/activity.csv")
data_no_na<-na.omit(data) 
sum<-data_no_na %>% group_by(date) %>% summarize(esum=sum(steps),n=n())
```

```
## `summarise()` ungrouping output (override with `.groups` argument)
```

```r
hist(sum$esum,col="red",xlab="steps per day",ylab="Freq Count",main="Count of Steps per day")
```

![](PA1_template_files/figure-html/chart1-1.png)<!-- -->

```r
summarize(sum,mean=mean(esum),median=median(esum))
```

```
## # A tibble: 1 x 2
##     mean median
##    <dbl>  <int>
## 1 10766.  10765
```


```r
sum_intvl<-data_no_na %>% group_by(interval) %>% summarize(mean=mean(steps),n=n())
```

```
## `summarise()` ungrouping output (override with `.groups` argument)
```

```r
with(sum_intvl,plot(interval,mean,type="l",main="Average Steps in Each Interval",
                    xlab="Interval",ylab="Steps"))
```

![](PA1_template_files/figure-html/chart2-1.png)<!-- -->

```r
sum_intvl[which.max(sum_intvl$mean),]
```

```
## # A tibble: 1 x 3
##   interval  mean     n
##      <int> <dbl> <int>
## 1      835  206.    53
```


```r
sum(is.na(data$steps))
```

```
## [1] 2304
```

```r
data2<-merge(data,sum_intvl,by="interval")
data2$steps1<-ifelse(is.na(data2$steps),data2$mean,data2$steps)
sum2<-data2%>% group_by(date) %>% summarize(esum=sum(steps1),n=n())
```

```
## `summarise()` ungrouping output (override with `.groups` argument)
```

```r
hist(sum2$esum,col="red",xlab="Steps",
  main="Steps per Day With Imputed Values For NAs") 
```

![](PA1_template_files/figure-html/chart3-1.png)<!-- -->

```r
mean(data2$mean)
```

```
## [1] 37.3826
```

```r
median(data2$mean)
```

```
## [1] 34.11321
```

```r
data2$date_d<-as.Date(data2$date)
data2$wkday<-weekdays(data2$date_d)
data2$wk_flag<-ifelse(substr(data2$wkday,1,1)=="S",0,1)
# sum_wk<-data2 %>% group_by(date,wk_flag) %>% summarize(mean=mean(steps1),n=n())
# sum_wk<-data2 %>% group_by(interval) %>% summarize(mean=mean(steps),n=n())
sum_wkday<-filter(data2,wk_flag==1)
sum_wkend<-filter(data2,wk_flag==0)
sum_wkday$date1<-as.Date(sum_wkday$date)
sum_wkend$date1<-as.Date(sum_wkend$date)
sum_wkday_g<-sum_wkday %>% group_by(interval) %>% summarize(mean=mean(steps1),n=n())
```

```
## `summarise()` ungrouping output (override with `.groups` argument)
```

```r
sum_wkend_g<-sum_wkend %>% group_by(interval) %>% summarize(mean=mean(steps1),n=n())
```

```
## `summarise()` ungrouping output (override with `.groups` argument)
```

```r
par(mfrow=c(2,1),mar=c(4,4,2,1))
with(sum_wkday_g,plot(interval,mean, ylab="Steps",xlab=" ",type="l",main="Average Steps per Day Week Day"))
with(sum_wkend_g,plot(interval,mean, ylab="Steps",xlab=" ",type="l",
                    main="Average Steps per Day Weekend"))
```

![](PA1_template_files/figure-html/chart4-1.png)<!-- -->



## Including Plots

You can also embed plots, for example:



Note that the `echo = FALSE` parameter was added to the code chunk to prevent printing of the R code that generated the plot.
