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
![decomposed time series](https://github.com/venusrohilla/Time-Series-and-Forecasting/blob/master/new%20folder/decomposed%20series%20plot.png)
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
![seasonal variation plot](https://github.com/venusrohilla/Time-Series-and-Forecasting/blob/master/new%20folder/seasonal%20plot.png)
```{r}
autoplot(tr, series="Data") +
  autolayer(ma(tr, 12), series="12-MA") +
  xlab("Year") + ylab("sales") +
  ggtitle("sales") +
  scale_colour_manual(values=c("Data"="grey","12-MA"="red"),
                      breaks=c("Data","12-MA"))
```
![trend plot](https://github.com/venusrohilla/Time-Series-and-Forecasting/blob/master/new%20folder/plot%20for%20showing%20trend.png)
```{r}
boxplot(tr~cycle(tr))
```
![Boxplot for detecting outliers and seasonal variations](https://github.com/venusrohilla/Time-Series-and-Forecasting/blob/master/new%20folder/boxplot.png) 
```{r}
acf(tr,lag(24))
```
![checking stationarity](https://github.com/venusrohilla/Time-Series-and-Forecasting/blob/master/new%20folder/acf%204%20check%20stationar.png)
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
