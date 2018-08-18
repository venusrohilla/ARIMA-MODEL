Loading the `tseries` and `forecast` packages for time series and forecasting tools and `ggplot2` for visualization.
```{r}
getwd()
library("ggplot2", lib.loc="E:/New folder/R-3.4.3/library")
library("tseries", lib.loc="E:/New folder/R-3.4.3/library")
library("forecast", lib.loc="E:/New folder/R-3.4.3/library")
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
```{r}
dtr<-stl(tr,s.window = "periodic")
dtr
```

```{r}
plot(dtr)
```
```{r}
ndiffs(tr)
```
[1] 1
```{r}
nsdiffs(tr)
```
[1] 1
```{r}
ggsubseriesplot(st)+ylab("sales")+ggtitle("seasonal plot:")
```
```{r}
autoplot(tr, series="Data") +
  autolayer(ma(tr, 12), series="12-MA") +
  xlab("Year") + ylab("sales") +
  ggtitle("sales") +
  scale_colour_manual(values=c("Data"="grey","12-MA"="red"),
                      breaks=c("Data","12-MA"))
```
```{r}
boxplot(tr~cycle(tr))
```
```{r}
acf(tr,lag(24))
```
```{r}
y<-diff(tr,lag=12)
plot(y)
```
```{r}
x<-log(tr)
plot(x)
```
```{r}
auto_tract<-auto.arima(log10(tr),stepwise = FALSE,approximation = FALSE)
summary(auto_tract)
```
Series: log10(tr) 
ARIMA(0,1,1)(0,1,1)[12] 

Coefficients:
    ma1     sma1
-0.4047  -0.5529
s.e.   0.0885   0.0734

sigma^2 estimated as 0.0002571:  log likelihood=354.4
AIC=-702.79   AICc=-702.6   BIC=-694.17

Training set error measures:
    ME       RMSE        MAE         MPE      MAPE
Training set 0.0002410698 0.01517695 0.01135312 0.008335713 0.4462212
MASE       ACF1
Training set 0.2158968 0.01062604
```{r}
ggtsdisplay(residuals(auto_tract))
```
```{r}
checkresiduals(auto_tract)
```
```{r}
res<-residuals(auto_tract)
Box.test(res,lag = 24,type = "Ljung")
```
Box-Ljung test

data:  res
X-squared = 26.219, df = 24, p-value = 0.3422
#prediction
```{r}
par(mfrow = c(1,1))
Pred= predict(auto_tract, n.ahead = 36)
Pred
plot(tr,type='l',xlim=c(2004,2018),ylim=c(1,1600),xlab = 'Year',ylab = 'Tractor Sales')
lines(10^(Pred$pred),col='red')
lines(10^(Pred$pred+2*Pred$se),col='blue')
lines(10^(Pred$pred-2*Pred$se),col='orange')
```
