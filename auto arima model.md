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
1. Trend:
A trend exists when there is a long-term increase or decrease in the data. It does not have to be linear. Sometimes we will refer to a trend as “changing direction”, when it might go from an increasing trend to a decreasing trend.

2. Seasonal:
A seasonal pattern occurs when a time series is affected by seasonal factors such as the time of the year or the day of the week. Seasonality is always of a fixed and known frequency. 

3. Cyclic:
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

![image](https://github.com/venusrohilla/Time-Series-and-Forecasting/blob/master/new%20folder/stationarity%201.PNG)
![image](https://github.com/venusrohilla/Time-Series-and-Forecasting/blob/master/new%20folder/stationarity%202.PNG)
![image](https://github.com/venusrohilla/Time-Series-and-Forecasting/blob/master/new%20folder/stationarity%203.PNG)

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
![image](https://github.com/venusrohilla/Time-Series-and-Forecasting/blob/master/new%20folder/kpss%20test.PNG)

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
###### These functions suggest we should do both a seasonal difference and a first difference.
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
By first differencing the series we get we have got constant mean.
![time series with constant mean](https://github.com/venusrohilla/Time-Series-and-Forecasting/blob/master/new%20folder/removing%20trend.png)
```{r}
x<-log(tr)
plot(x)
```
THe log transformation stabilizes the variance of the time series.
![time series with constant variance](https://github.com/venusrohilla/Time-Series-and-Forecasting/blob/master/new%20folder/constant%20variance.png)

# Modelling Procedure
Since we have made our series stationary (independent of time) we can now build an appropriate model on it which can be used for predicting future tractor sales.
`ARIMA` models are the two most widely used approaches to time series forecasting, and provide complementary approaches to the problem.
ARIMA models aim to describe the autocorrelations in the data.
Before working with ARIMA model first we need to understand what `AR`,`I`and `MA` mean .ARIMA is an acronym for AutoRegressive Integrated Moving Average (in this context, “integration” is the reverse of differencing).
###### AR :
We forecast the variable of interest using a linear combination of predictors. In an autoregression model, we forecast the variable of interest using a linear combination of past values of the variable. The term autoregression indicates that it is a regression of the variable against itself.Thus, an autoregressive model of order p can be written as:
                     `y(t)=ϕ(1)y(t-1)+ϕ(2)y(t-2)...+ϕ(p)y(t-p)+ε(t)`
Where ε(t) is the erroe term .We refer to this as an AR(p) model, an autoregressive model of order p.
###### MA :
Rather than using past values of the forecast variable in a regression, a moving average model uses past forecast errors in a regression-like model.
                     `y(t)= ε(t)+θ(1)ε(t-1)+θ(2)ε(t-2)+...+θ(q)ε(t-q)`
Where ε(t) is white noise.We refer to this as an MA(q) model, a moving average model of order q.Notice that each value of y(t) can be thought of as a weighted moving average of the past few forecast errors. 


###### ARIMA 
If we combine differencing with autoregression and a moving average model, we obtain a non-seasonal ARIMA model. ARIMA is an acronym for AutoRegressive Integrated Moving Average (in this context, “integration” is the reverse of differencing). The full model can be written as:
                   `y′(t)=ϕ(1)y′(t-1)+..+ϕ(p)y′(t−p)+θ(1)ε(t−1)+...+θ(q)ε(t−q)+ε(t)`
where y′(t) is the differenced series (it may have been differenced more than once). The “predictors” on the right hand side include both lagged values of y(t) and lagged errors. We call this an ARIMA(p,d,q) model, where p=order of the autoregressive part,q=order of the moving average part and d= degree of the first differencing involved.
The same stationarity and invertibility conditions that are used for autoregressive and moving average models also apply to an ARIMA model.

When fitting an ARIMA model to a set of (non-seasonal) time series data, the following procedure provides a useful general approach.

* Plot the data and identify any unusual observations.
* If necessary, transform the data (using a Box-Cox transformation) to stabilise the variance.
* If the data are non-stationary, take first differences of the data until the data are stationary.
* Examine the ACF/PACF: Is an ARIMA( p,d,0) or ARIMA( 0,d,q) model appropriate?
* Try your chosen model(s), and use the AICc to search for a better model.
* Check the residuals from your chosen model by plotting the ACF of the residuals, and doing a portmanteau test of the residuals. If they do not look like white noise, try a modified model.
* Once the residuals look like white noise, calculate forecasts.

The auto.arima() function in R uses a variation of the Hyndman-Khandakar algorithm (Hyndman & Khandakar, 2008), which combines unit root tests, minimisation of the AICc and MLE to obtain an ARIMA model. The arguments to auto.arima() provide for many variations on the algorithm. 

![arimaflowimage](https://github.com/venusrohilla/Time-Series-and-Forecasting/blob/master/new%20folder/arimaflowchart.png)
```{r}
auto_tract<-auto.arima(log10(tr),stepwise = FALSE,approximation = FALSE)
summary(auto_tract)
```
![summaryauto arima](https://github.com/venusrohilla/Time-Series-and-Forecasting/blob/master/new%20folder/auto%20arima%20summary.PNG)
#### Observations:
The best fit model is selected based on Akaike Information Criterion (AIC) , and Bayesian Information Criterion (BIC) values. The idea is to choose a model with minimum AIC and BIC values.
As expected, our model has I (or integrated) component equal to 1. This represents differencing of order 1.
There is additional differencing of lag 12 in the above best fit model. Moreover, the best fit model has MA value of order 1. Also, there is seasonal MA with lag 12 of order 1.
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
#### RESIDUALS
Residuals are useful in checking whether a model has adequately captured the information in the data. A good forecasting method will yield residuals with the following properties:

* The residuals are uncorrelated. If there are correlations between residuals, then there is information left in the residuals which should be used in computing forecasts.
* The residuals have zero mean. If the residuals have a mean other than zero, then the forecasts are biased.
Any forecasting method that does not satisfy these properties can be improved.From the above plots we can see that residuals follow a normal distribution that means the mean of the residuals is close to zero and there is no significant correlation in the residuals serie.The time plot of the residuals shows that the variation of the residuals stays much the same across the historical data. 
# Predicting Tractor sales.
## Now based on the above built model we would predict sales for tractor for the next 3 years.
```{r}
par(mfrow = c(1,1))
Pred= predict(auto_tract, n.ahead = 36)
Pred
plot(tr,type='l',xlim=c(2004,2018),ylim=c(1,1600),xlab = 'Year',ylab = 'Tractor Sales')
lines(10^(Pred$pred),col='red')
lines(10^(Pred$pred+2*Pred$se),col='blue')
lines(10^(Pred$pred-2*Pred$se),col='blue')
```
![predicted sales](https://github.com/venusrohilla/Time-Series-and-Forecasting/blob/master/new%20folder/predicted%20graph.png)

The above is the output with forecasted values of tractor sales in red. Also, the range of expected error (i.e. 2 times standard deviation) is displayed with blue lines on either side of predicted red line.

Assumptions while forecasting: Forecasts for a long period of 3 years is an ambitious task. The major assumption here is that the underlining patterns in the time series will continue to stay the same as predicted in the model. A short-term forecasting model, say a couple of business quarters or a year, is usually a good idea to forecast with reasonable accuracy. A long-term model like the one above needs to evaluated on a regular interval of time (say 6 months). The idea is to incorporate the new information available with the passage of time in the model.





