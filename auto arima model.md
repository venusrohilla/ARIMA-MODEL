Loading the `tseries` and `forecast` packages for time series and forecasting tools and `ggplot2` for visualization.
```{r}
getwd()
library("ggplot2", lib.loc="E:/New folder/R-3.4.3/library")
library("tseries", lib.loc="E:/New folder/R-3.4.3/library")
library("forecast", lib.loc="E:/New folder/R-3.4.3/library")
library("urca", lib.loc="E:/New folder/R-3.4.3/library")
```
Now reading the Sales dataset in our working directory.
```{r}
tract<-read.csv("Tractor-Sales (1).csv")
View(tract)
```
As the second column of our dataset contains the sales record .We convert those records into time series using the `ts` time series function.
```{r}
tr<-ts(tract[,2],start =2003,frequency = 12)
plot(tr)
```
![time series plot](https://github.com/venusrohilla/Time-Series-and-Forecasting/blob/master/new%20folder/time%20series%20plot.png)
## TIME SERIES PATTERNS
`Trend`
A trend exists when there is a long-term increase or decrease in the data. It does not have to be linear. Sometimes we will refer to a trend as “changing direction”, when it might go from an increasing trend to a decreasing trend.
`Seasonal`
A seasonal pattern occurs when a time series is affected by seasonal factors such as the time of the year or the day of the week. Seasonality is always of a fixed and known frequency. 
`Cyclic`
A cycle occurs when the data exhibit rises and falls that are not of a fixed frequency. These fluctuations are usually due to economic conditions, and are often related to the “business cycle”.
In the above plot we see a clear visual of an increasing trend and seasonal effects however there is no cyclic component present.
> Decomposing the time series help us in deep analysis of trend ,seasonality and cyclic component.
```{r}
dtr<-stl(tr,s.window = "periodic")
dtr
```
The remainder present in time series is the `unexplained` or the `error` term .
If we assume an additive decomposition, then we can write:
`y(t)= s(t)+T(t)+r(t)`  where y(t) is the data,  s(t) is the seasonal component,  T(t)  is the trend-cycle component, and  
r(t) is the remainder component, all at period t.
Alternatively, a multiplicative decomposition would be written as:
`y(t)=s(t).T(t).r(t)`.
* The additive decomposition is the most appropriate if the magnitude of the seasonal fluctuations, or the variation around the trend-cycle, does not vary with the level of the time series. When the variation in the seasonal pattern, or the variation around the trend-cycle, appears to be proportional to the level of the time series, then a multiplicative decomposition is more appropriate. Multiplicative decompositions are common with economic time series.

* An alternative to using a multiplicative decomposition is to first transform the data until the variation in the series appears to be stable over time, then use an additive decomposition. When a log transformation has been used, this is equivalent to using a multiplicative decomposition because: `y(t)=s(t).T(t).r(t) is equivalent to log(y(t))=log(s(t))+log(T(t))+log(r(t))`
```{r}
plot(dtr)
```
![decomposed time series](https://github.com/venusrohilla/Time-Series-and-Forecasting/blob/master/new%20folder/decomposed%20series%20plot.png)
## unless our time series is stationary, we cannot build a time series model. In cases where the stationary criterion are violated, the first requisite becomes to stationarize the time series and then try stochastic models to predict this time series. There are multiple ways of bringing this stationarity. 
Transformations such as logarithms can help to stabilise the variance of a time series. Differencing can help stabilise the mean of a time series by removing changes in the level of a time series, and therefore eliminating (or reducing) trend and seasonality.
As well as looking at the time plot of the data, the ACF plot is also useful for identifying non-stationary time series. For a stationary time series, the ACF will drop to zero relatively quickly, while the ACF of non-stationary data decreases slowly. Also, for non-stationary data, the value of r(1) is often large and positive.
```{r}
acf(tr,lag(24))
```
![checking stationarity](https://github.com/venusrohilla/Time-Series-and-Forecasting/blob/master/new%20folder/acf%204%20check%20stationar.png)
From the above plot we see that acf slowly drops to zero.

One way to determine more objectively whether differencing is required is to use a unit root test. These are statistical hypothesis tests of stationarity that are designed for determining whether differencing is required,we use the Kwiatkowski-Phillips-Schmidt-Shin (KPSS) test.
##### In this test, the null hypothesis is that the data are stationary, and we look for evidence that the null hypothesis is false. 
```{r}
ur.kpss(tr)
```
![image](kpss test)
The test statistic is much bigger than the 1% critical value, indicating that the null hypothesis is rejected. That is, the data are not stationary. We can difference the data, and apply the test again.
This process of using a sequence of KPSS tests to determine the appropriate number of first differences is carried out by the function `ndiffs()`.A similar function for determining whether seasonal differencing is required is `nsdiffs()`.
```{r}
ndiffs(tr)
```
[1] 1
```{r}
nsdiffs(tr)
```
[1] 1

A ggsubseries plot emphasises the seasonal patterns the data for each season are collected together in separate mini time plots.This is helpful in getting a more clear picture of the seasonality in the time series data.
```{r}
ggsubseriesplot(st)+ylab("sales")+ggtitle("seasonal plot:")
```
![seasonal variation plot](https://github.com/venusrohilla/Time-Series-and-Forecasting/blob/master/new%20folder/seasonal%20plot.png)
The horizontal lines indicate the means for each month. This form of plot enables the underlying seasonal pattern to be seen clearly, and also shows the changes in seasonality over time. It is especially useful in identifying changes within particular season
 As we saw our time series has many minor fluctuations ,so in order to get a clear visual of the time series we apply `moving average smoothing` 
```{r}
autoplot(tr, series="Data") +
  autolayer(ma(tr, 12), series="12-MA") +
  xlab("Year") + ylab("sales") +
  ggtitle("sales") +
  scale_colour_manual(values=c("Data"="grey","12-MA"="red"),
                      breaks=c("Data","12-MA"))
```
![trend plot](https://github.com/venusrohilla/Time-Series-and-Forecasting/blob/master/new%20folder/plot%20for%20showing%20trend.png)

Notice that the trend-cycle (in red) is smoother than the original data and captures the main movement of the time series without all of the minor fluctuations. The order of the moving average determines the smoothness of the trend-cycle estimate. In general, a larger order means a smoother curve. if the seasonal period is even and of order  m, we use a 2 × m-MA to estimate the trend-cycle. If the seasonal period is odd and of order m, we use a m-MA to estimate the trend-cycle.where m =2k+1. That is, the estimate of the trend-cycle at time t is obtained by averaging values of the time series within k periods of t.
```{r}
boxplot(tr~cycle(tr))
```
![Boxplot for detecting outliers and seasonal variations](https://github.com/venusrohilla/Time-Series-and-Forecasting/blob/master/new%20folder/boxplot.png) 

From the boxplot of the seasonal variations in time series we are able to analyse that there are no outliers present in the seasonal data and the mean values of sales of tractors is more in 7th and 8th month.

```{r}
y<-diff(tr,lag=12)
plot(y)
```
![time series with constant mean](https://github.com/venusrohilla/Time-Series-and-Forecasting/blob/master/new%20folder/removing%20trend.png)
```{r}
x<-log(tr)
plot(x)
```
![time series with constant variance](https://github.com/venusrohilla/Time-Series-and-Forecasting/blob/master/new%20folder/constant%20variance.png)
```{r}
auto_tract<-auto.arima(log10(tr),stepwise = FALSE,approximation = FALSE)
summary(auto_tract)
```
![summary of auto arima]
```{r}
ggtsdisplay(residuals(auto_tract))
```
![image](https://github.com/venusrohilla/Time-Series-and-Forecasting/blob/master/new%20folder/auto%20arima%20residual%20plot1.PNG)
![image](https://github.com/venusrohilla/Time-Series-and-Forecasting/blob/master/new%20folder/acf%20lag%20plot2.PNG)
![image](https://github.com/venusrohilla/Time-Series-and-Forecasting/blob/master/new%20folder/Capturauto%20arima%20pacf%20plot3.PNG)
```{r}
checkresiduals(auto_tract)
```
![image](https://github.com/venusrohilla/Time-Series-and-Forecasting/blob/master/new%20folder/normal%20plot.PNG)
```{r}
res<-residuals(auto_tract)
Box.test(res,lag = 24,type = "Ljung")
```
![image](https://github.com/venusrohilla/Time-Series-and-Forecasting/blob/master/new%20folder/box-ljung%20test.PNG)
# Predicting Tractor sales.
## Now based on the above built model we would predict sales for tractor for the next 3 years.
```{r}
par(mfrow = c(1,1))
Pred= predict(auto_tract, n.ahead = 36)
Pred
plot(tr,type='l',xlim=c(2004,2018),ylim=c(1,1600),xlab = 'Year',ylab = 'Tractor Sales')
lines(10^(Pred$pred),col='red')
lines(10^(Pred$pred+2*Pred$se),col='blue')
lines(10^(Pred$pred-2*Pred$se),col='orange')
```
![predicted sales](https://github.com/venusrohilla/Time-Series-and-Forecasting/blob/master/new%20folder/predicted%20graph.png)
