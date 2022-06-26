# Final Report
# Traffic Forecasting of the Eisenhower tunnel
 *By Kit Clark*

A short form documentation is included in the project file as a README, the following document privides more technical details

# Introduction
The Eisenhower Traffic study provides a coprehensive analysis of a auto-correlated time-series dataset.  Seven years (2015-2021) of hourly traffic data were combined with hourly weather data and analyzed.
The study represents a  full analysis workflow from data-scraping to modelling for time-series data from the Eisenhower and Johnson Tunnels on I-70 on the continental divide, West of Denver Colorado. People who live on the plains in Colorado often head west into the mountains. The Rocky Mountains are a magnificent beauty and source of inspiration. The journey to get there, particualrly in heavy traffic, in contrast is very frustrating. Holiday and weekend traffic can double or triple the amount of time required in a low-traffic conditions.  The authors were interested in understanding the trends in the data and exploring the drivers of heavy traffic.  Predictions were generated using a combination of statistical models and machine learning. ARIMA (autoregression, inversion, moving average), SARIMA (Seasonal ARIMA), and Prophet were trained on 85% of the data and tested on the remainder. The ARIMA, SARIMA, auto-ARIMA, auto-SARIMA models performed poorly, despite lots of hyperparmater tuning.  The prophet model had the best performance.  Although additional wok is required to understand the relationship between weather conditions and traffic counts the currrent model provides accurate predictions that approximate the  daily, hourly, weekly, monthly, and annual sasonality in the data.

# Data
We will be working with hourly count data collected by CDOT (Colorado Dept of Transport) at numerous stations across Colorado. We will be examining the station at the Eisehower tunnel. Data are stored with one column per hour and one row per day for each month.  Hourly traffic and weather data were assembled, cleaned, and imputed prior to modelling. Intuitively weather has a significant impact on traffic. Weather data were included for this reason. After exploring the data and various modelling techniques the weather data were not used in the models. Data are highly cyclical with hourly, daily, weekly, and monthly seasonality.  The final cleaned dataset consists of 122,687 rows and 17 columns, traffic and time 8 time categories (i.e  counts, holidays and weekends) and 9 weather columns.  Missingness was low in the critical traffic data with more widespread missingness in the weather data (Table 1).

![image](https://user-images.githubusercontent.com/30851535/175829940-bdd7403b-0977-4932-889a-5d6812b2214b.png)
*Table 1:Data heading for the final combined dataset of weather and traffic data *


## Sources
Hourly data, for two directions (Primary/West, Secondary/East), were pulled for years 2015-2021 from CDOT (Colorado Department of Transit).  Traffic data are available for all stations in Colorado but the scope of this study was limited to traffic counts at the Eisenhower and Johnson tunnels along I-70.
The Eisenhower Tunnel is at 11,158' ft elevation and 26 ft (~8 m) annual snowfall. Recent snowfall has the potential to adjust the behavior of drivers; drivers may wait for roads to be cleared or may take the opportunity to ski fresh snow.
  Hourly weather data were pulled from the nearest station using the Meteostat package.  The nearest weather station was identified using the lat/long coordinates of the tunnel.  Experimentation with the number of weather stations did reduce missingness but included stations from either side of the continental divide.  Each side of the divide is a seperate basin with semi-decoupled patterns of precipitation. The decision was made to include weather data from the Slate/Creek/Copper Mountain station alone. The weather data from the Meteostat package for all sations was  only available  through 2020.  Weather data in 2021 was approximated from the mean value in previous years for a given week.  
  The raw data were in good shape(*Figure 1*).
![image](https://user-images.githubusercontent.com/30851535/175449802-e4abb32d-333b-4805-95d8-7e5c5b371c22.png)
*Figure 1: Annual patterns in hourly traffic data summed to the day.  The Summer and Winter have the maximum traffic, the Spring and Fall have lower traffic counts.*  
  The primary obstacle was accounting for missing time rows and imputing missing values for the principal columns of concern.

## Cleaning

### General
The weather and traffic data were mostly complete.  Six years of data with 122,688 rows for two directions of traffic 
The traffic data had limited missingness assocciated with outages that were clustered around certain times.  Outages were typically limited to 1-2 days but may occur in rapid succession.  Weather data had high missingness for values beyond temperature, humidity, and dewpoint.  Temperature and humidity were sufficiently complete to build some simple linear models for imputation.  Missing temperature data was imputed using mean imputation from the same week in other years.  All of 2021 temperature data is absent from the Meteostat datasource.  
The final dataset has no missingess and all columns are in the appropriate format for modelling. 

### Missingness
Of the 122,687 rows, ~6.7% (2,241), were missing from the traffic counts and  ~21% (12,882) were missing from the weather data.  The weather data has higher missingess than the traffic data, especially in for fields aside from temeprature, dewpoint, and relative humidity.
The missing values in the traffic data are missing at semi-random.  The "chunks" of missing values occur at random but once there's a data outage, the duration of the outage is non-uniform. *Figure 2*, sorted by measurement time, shows the patterns of missingness in vehicle counts are infrequent and don't occur at a repeating cadence.

![image](https://user-images.githubusercontent.com/30851535/175830596-e394e0d4-4483-4239-bc52-ee3cca0ec071.png)
*Figure 2: Missingness patterns in the hourly traffic counts from CDOT, 2016-2021.* 

The weather data has much more missingness than the traffic data (*Figure 3*).  Temperature (temp), relative humidity (rhum), and dewpoint (dwpt) are the most complete (79% populated).  

![image](https://user-images.githubusercontent.com/30851535/175830873-d87e2f39-a6c6-4d39-8429-9d0b7f5df81d.png)
*Figure 3: Table of the percent of missingness for hourly weather data from 2016-2021.*

Precipitation (prcp) is ~8% complete but non-measurements are recorded as NULL. Weather data is unavailable from the meteostat package for 2021.  Most of the missing rows of temperature, dew point, and humidity are due to the absecence of 2021 data.  otherwise these three fields appear to be missing at random (*Figure 4*).  
![image](https://user-images.githubusercontent.com/30851535/175831091-05f81074-eb7f-467b-81a8-70158d259cc3.png)
*Figure 4: Figure showing the patterns of  missingness for hourly weather data from 2016-2021.*
Some of the missing weather data were partially imputed as part of the data import from the Meteostat package.  The package interpolates between successive complete rows to fill in missing rows.  The interpolation is apparent in crossplots of the weather data (Data exploration section).

### imputation
Imputation was conducted using mean imputation or linear regression.  Missing temperature and traffic counts were populated using mean imputation grouped by the month, day, hour, and direction (traffic only).  The missing values for relative humidity and dewpoint were populated using a model that predicted each feature as a function of temperature.  Both the mean imputation and linear regression provide a satisfactory approximation for the missing values (*Figure 5*).

![image](https://user-images.githubusercontent.com/30851535/175831790-e1e8edec-919a-4807-9eb4-79d2cc2178bd.png)
*Figure 5: Predicted value (x-axis) vs actual (y-axis) for relative humidity and dewpoint.  Models predict Y as a function of temperature. Models provide directional guidance for expected values of missing data.*

## Data Exploration

The combined traffic and weather data were analyzed for cyclicity and correlations between features.  The traffic data are highly cyclical with clear daily and seasonal cyclicity.

### Data Trends
  Data are highly cyclical and follow a seasonal pattern.  The strongest patterns for traffic counts are 1) the day of the week, and 2) the time of day (*Figure 6*).
  
![image](https://user-images.githubusercontent.com/30851535/175834244-5ac83bca-3b10-460a-9a2b-f32757a8333e.png) 
*Figure 6: Hourly traffic counts for one, seven-day period.  The patterns observed in this random week from Monday to Sunday are typical. Traffic directions, indicated as "primary" and "secondary" refer to east and westbound traffic respectively. Resident on the front Range drive west (secondary, "s") to access the mountains. Each day westward traffic spike around 8 AM and eastbound traffic peaks in the PM.*
  
 ![image](https://user-images.githubusercontent.com/30851535/175295769-474a100b-8e3d-47a8-872d-a1cd5a7056cc.png)
 *Figure 7: heatmap of hourly traffic counts by day of the week.* 
 daily and hourly patterns for both traffic directions are evident in *Figure 7*. Friday, Saturday, and Sunday have the highest vehicle counts throughout the week and the busiest times of day are from 9:00 AM to ~6:00 PM.  The trends are different for east and westbound traffic.  Although the weekend is still the busiest time of the week, Fridays have higher westbound traffic (into the mountains) and Sundays have higher eastbound traffic (returning to Denver) (*Figure 8*).
 
 ![image](https://user-images.githubusercontent.com/30851535/175833058-f5addee0-5864-4ef5-bb0a-31e33df07d19.png)
 *Figure 8: Average hourly traffic data divided by westbound and eastbound.*

 Despite stringly weekly cycles, annual seasonality is also apparent in the data.  The Winter and Summer are the peak travel months with less traffic in the Spring and Fall (*Figure 1* & *Figure 9*).
 ![image](https://user-images.githubusercontent.com/30851535/175833341-33e523fa-bb5d-401d-a393-033d8df4919e.png)
*Figure 9: Daily traffic counts for Monday to Sunday for each month of the year.*

 *Figure 9* shows the average hourly traffic counts by month for each day of the week. The averages are much lower than those above because the daily values include all of the low traffic hours in the early morning. Friday through Sunday are high traffic days in nearly every month. Traffic is elevated during the ski season, January-March, and during the Summer. Interestingly the week of fourth of July sees elevated traffic on weekdays. This is likely due to when the July 4th holiday falls in a given year. Overall Winter and Summer have the most traffic with reduced traffic in the Fall and Spring. Traffic is higher in the fall than in the spring. On average the best day and month to travel is a Tuesday in April or May. One would expect very high traffic on a Weekend afternoon in July.
  

### Correlations
Correlations between vehicle counts and weather data are weak(*Figure 10*).  The strongest correlations with hourly vehicle counts and time.  The day of the week and hour have a  correlation coefficients of.18 and .29 respectively with vehicles per hour.  Dew point is weakly correlated  with precipitation (r=.2)  as expected.  The team examined days with high precipitation to determine any potential effects on traffic counts.  The impacts appear minimal and potentially counterintuitive.  In fact there is a very slightly negative correlation with precipitation and vehicles per hour suggesting a surprising effect.

![image](https://user-images.githubusercontent.com/30851535/175450083-6839381b-3119-4d8a-9ec6-76299b8f517c.png)
*Figure 10: Correlation plots across the features of interest.* 

Following exploration of the data it was clear that the best predictor of vehicle counts would be time and recent counts (lag).  The weather data was put aside for modelling and instead effort was directed at using time-based models such as ARIMA and SARIMA.

### Autocorrelation
Autocorrelation in time series describe how well correlated a previous value is with a given row.  A dataset with high autocorrelation indicates that the past values are very helpful in predicting future values.
Data were filtered to westbound traffic only (primary). The autocorrelation of the last 100 hours of data were assessed for the traffic data (*Figure 11*). 
![image](https://user-images.githubusercontent.com/30851535/175834730-843f19ac-eb88-499c-ab15-cab62d6baaa4.png)
*Figure 11: Autocorrelation plot of the last 100 hours.*
The autocorrelation plot or correlogram summarizes the strength of the relationship from -1 to 1 between a value and a lag of k. The 95% confidence interval is shown in the blue cone. The detrended data shows strong daily and weekly cyclicity. The high pearson coefficients indicate that past values are strongly correlated with future values.
The Autocorrelation plot shows troughs and peaks on 24 hour cycles.  The high correlation coefficients (>.5) suggest that the previous 1-4 days could help predict the subsequent values.  
The partial autocorrelation plot incates only the last few hours are helpful in predicting future values (*Figure 12*). 

![image](https://user-images.githubusercontent.com/30851535/175834889-838a64d8-1e09-49a7-9ded-89a59754275c.png)
*Figure 12:  Partial autocorrelation of hourly traffic data with lags up to 50 hours.*
The partial autocorrelation shows only the correlation that isn't accounted for in earlier lags.  *Figure 12* indicates that the previous 2 hours are the most useful in predicting a subsequent value.  The hourly traffic data exbitit cyclicity at the monthly, weekly, and hourly level. The lagged values are highly correlated with a given row and stay high. Tools such as ARIMA, SARIMA, and Prophet were tasked to develop a time-series model that would perofmr well against a "test" set.

Data was summed up to weekly totals to simplify the model training workflow. The winning methodology was then applied to the complete hourly dataset.
The autocorrelations and partial correlation plots of the weekly data show similar to what were observed in the hourly data (*Figure 13*).
![image](https://user-images.githubusercontent.com/30851535/175835487-05bb9114-2ea4-44e0-bfe0-b2db48be21d0.png)
*Figure 13: Partial and autocorrelation plots for weekly traffic data.*

*Figure 13* indicates that the last 4 weeks of traffic data are predictive of a given value. The Partial autocorrelation indicates the unique information in each lag. In this context only the last two weekly values are predictive of a future row.

The data, weekly and hourly, were assessef for trends using the AD Fuller test.  in both cases, p vlues were far below .05 indicating no trends.   

# Modelling
The team explored both conventional-statistical and machine learning modelling approaches to time series forecasting. The data was divided into a training and test dataset using a 85/15% split. The split wasn't sampled randomly but just selected the last 15% of all rows.


 


## ARIMA
ARIMA, auto-regressive inversion moving-average, is a classic model for time series forecasting. The model has several hyperparamters that define the lags that are used to foreward predict. We need to specify p,d, and q in that order as arguments for the model. p is the lag of the autoreregression, d is the degree of differencing, and q is the lag for the moving average. 
The manually-specified ARIMA was (2,0,2). 
*Figure 14* is shows the eprformance of the ARIMA model vs the test set. 

![image](https://user-images.githubusercontent.com/30851535/175835749-bf1b3f66-349f-4d0d-b101-90c94b785f11.png)
*Figure 14: forecasted values compared to test set for the manual ARIMA.*
The manual ARIMA was specified using a pdq of 2,0,2 respectively. The first and last vlaue are using the previous 2 rows to predict a given row. the d=0, indicates that there's no trend in our data and that we don't need to difference the data. 
The predictions in the training set fit the data very well, the predictions to the test set leave much to be desired. The model does predict relatively higher values and lower values that correspond with highs and lows in the test set but the magnitude and fidelity of the predictions are way off.

A Python package designed to solve for the ideal ARIMA hyperpareters was also used.  The auto-ARIMA selected a model with a lower AIC then the manual-ARIMA.  The auto-ARIMA selected the ideal pd,q as (1,0,1) respectively. The forecast performed slightly better than the manual-ARIMA but the fit left a lot to be desired.  the plot is included in the notebook but excluded from this documentation. 


## SARIMA
SARIMA is an ARIMA model with a seasonal component and has an additional 4 hyperparameters that define the seasonality to be used for predictions.
## Prophet

## Hourly Model
### Prophet

# Final thoughts

 ## Findings
 ## Improvements


