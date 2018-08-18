# Time Series and Forecatsing .
 In my research to learn about time series analysis and forecasting, I came across five sites that helped me to understand time series modeling, as well as how to create a model.
 Statistical forecasting: notes on regression and time series analysis: This site provides a deep dive into time series analysis, explaining every aspect in detail. It really helped to me understand what I was doing:
 * [Notes on Time series and Forecasting](http://people.duke.edu/~rnau/411home.htm)
 * [Forecasting : Principles and Techniques](https://otexts.org/fpp2/)
 * [Time Series ANalysis using R](https://a-little-book-of-r-for-time-series.readthedocs.io/en/latest/)
 * [STAT 510 -An online course on Applied Time Series Analysis by PennState Eberly College Science](https://onlinecourses.science.psu.edu/stat510/)
 * [A Complete Tutorial on Time Series Modeling in R](https://www.analyticsvidhya.com/blog/2015/12/complete-tutorial-time-series-modeling/)

## INTRODUCTION
The most important factor for success in any field is justifiable usage of `TIME`. It’s difficult to keep up with the pace of time.  But, technology has developed some powerful methods using which we can make meaningful insights by studying the things that are directly or indirectly influenced by time. Time Series and Forecasting involves working on time (years, days, hours, minutes) based data, to derive hidden insights to make informed decision making.	

A time series is a sequence of measurements of the same variable collected over time.  Most often, the measurements are made at regular time intervals.

One defining characteristic of time series is that this is a list of observations where the ordering matters.  Ordering is very important because there is dependency and changing the order could change the meaning of the data.
The basic objective usually is to determine a model that describes the pattern of the time series.  Uses for such a model are:
* To describe the important features of the time series pattern.
* To explain how the past affects the future or how two time series can “interact”.
* To forecast future values of the series.

## Important Characteristics to Consider First
Some important questions to first consider when first looking at a time series are:
* Is there a trend, meaning that, on average, the measurements tend to increase (or decrease) over time?
* Is there seasonality, meaning that there is a regularly repeating pattern of highs and lows related to calendar time such as seasons, quarters, months, days of the week, and so on?
* Are their outliers? In regression, outliers are far away from your line. With time series data, your outliers are far away from your other data.
* Is there a long-run cycle or period unrelated to seasonality factors?
* Is there constant varianceover time, or is the variance non-constant?
* Are there any abrupt changes to either the level of the series or the variance?

#### EXAMPLE
For studying Time series and Forecasting I have used Tractor-Sales dataset.
The dataset consists of sales records of tractors from January 2003 to December 2014.
