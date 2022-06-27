# Final Report
# Traffic Forecasting of the Eisenhower tunnel
 *By Kit Clark*

A short form documentation is included in the project file as a README, the following document privides more technical details

# Introduction
The Eisenhower Traffic study provides a comprehensive analysis of a auto-correlated time-series dataset. Seven years (2015-2021) of hourly traffic data were combined with hourly weather data and analyzed. The study represents a full analysis workflow from data-scraping to modelling for time-series data from the Eisenhower and Johnson Tunnels on I-70 on the continental divide, West of Denver Colorado. People who live on the plains in Colorado often head west into the mountains. The Rocky Mountains are a magnificent beauty and source of inspiration. The journey to get there, particularly in heavy traffic, in contrast is very frustrating. Holiday and weekend traffic can double or triple the amount of time required in a low-traffic conditions. The authors were interested in understanding the trends in the data and exploring the drivers of heavy traffic. Predictions were generated using a combination of statistical models and machine learning. ARIMA (autoregression, inversion, moving average), SARIMA (Seasonal ARIMA), and Prophet were trained on 85% of the data and tested on the remainder. The ARIMA, SARIMA, auto-ARIMA, auto-SARIMA models performed poorly, despite lots of hyperparameter tuning. The prophet model had the best performance. Although additional wok is required to understand the relationship between weather conditions and traffic counts the current model provides accurate predictions that approximate the daily, hourly, weekly, monthly, and annual seasonality in the data.

# Data
We will be working with hourly count data collected by CDOT (Colorado Dept of Transport) at numerous stations across Colorado. We will be examining the station at the Eisenhower tunnel. Data are stored with one column per hour and one row per day for each month.  Hourly traffic and weather data were assembled, cleaned, and imputed prior to modelling. Intuitively weather has a significant impact on traffic. Weather data were included for this reason. After exploring the data and various modelling techniques the weather data were not used in the models. Data are highly cyclical with hourly, daily, weekly, and monthly seasonality.  The final cleaned dataset consists of 122,687 rows and 17 columns, traffic and 8 time categories (i.e.  counts, holidays and weekends) and 9 weather columns.  Missingness was low in the critical traffic data with more widespread missingness in the weather data (Table 1).

![image](https://user-images.githubusercontent.com/30851535/175829940-bdd7403b-0977-4932-889a-5d6812b2214b.png)
*Table 1:Data heading for the final combined dataset of weather and traffic data *


## Sources
Hourly data, for two directions (Primary/West, Secondary/East), were pulled for years 2015-2021 from CDOT (Colorado Department of Transit).  Traffic data are available for all stations in Colorado, but the scope of this study was limited to traffic counts at the Eisenhower and Johnson tunnels along I-70.
The Eisenhower Tunnel is at 11,158' ft elevation and 26 ft (~8 m) annual snowfall. Recent snowfall has the potential to adjust the behavior of drivers; drivers may wait for roads to be cleared or may take the opportunity to ski fresh snow.
  Hourly weather data were pulled from the nearest station using the Meteostat package.  The nearest weather station was identified using the lat/long coordinates of the tunnel.  Experimentation with the number of weather stations did reduce missingness but included stations from either side of the continental divide.  Each side of the divide is a separate basin with semi-decoupled patterns of precipitation. The decision was made to include weather data from the Slate/Creek/Copper Mountain station alone. The weather data from the Meteostat package for all stations was only available through 2020.  Weather data in 2021 was approximated from the mean value in previous years for a given week.  
  
  The raw data were in good shape(*Figure 1*).
![image](https://user-images.githubusercontent.com/30851535/175449802-e4abb32d-333b-4805-95d8-7e5c5b371c22.png)
*Figure 1: Annual patterns in hourly traffic data summed to the day.  The Summer and Winter have the maximum traffic, the Spring and Fall have lower traffic counts.*  
  The primary obstacle was accounting for missing time rows and imputing missing values for the principal columns of concern.

## Cleaning

### General
The weather and traffic data were mostly complete.  Six years of data with 122,688 rows for two directions of traffic 
The traffic data had limited missingness associated with outages that were clustered around certain times.  Outages were typically limited to 1-2 days but may occur in rapid succession.  Weather data had high missingness for values beyond temperature, humidity, and dewpoint.  Temperature and humidity were sufficiently complete to build some simple linear models for imputation.  Missing temperature data was imputed using mean imputation from the same week in other years.  All of 2021 temperature data is absent from the Meteostat data source.  
The final dataset has no missingness and all columns are in the appropriate format for modelling. 

### Missingness
Of the 122,687 rows, ~6.7% (2,241), were missing from the traffic counts and ~21% (12,882) were missing from the weather data.  The weather data has higher missingness than the traffic data, especially in for fields aside from temperature, dewpoint, and relative humidity.
The missing values in the traffic data are missing at semi-random.  The "chunks" of missing values occur at random but once there's a data outage, the duration of the outage is non-uniform. *Figure 2*, sorted by measurement time, shows the patterns of missingness in vehicle counts are infrequent and don't occur at a repeating cadence.

![image](https://user-images.githubusercontent.com/30851535/175830596-e394e0d4-4483-4239-bc52-ee3cca0ec071.png)
*Figure 2: Missingness patterns in the hourly traffic counts from CDOT, 2016-2021.* 

The weather data has much more missingness than the traffic data (*Figure 3*).  Temperature (temp), relative humidity (rhum), and dewpoint (dwpt) are the most complete (79% populated).  

![image](https://user-images.githubusercontent.com/30851535/175830873-d87e2f39-a6c6-4d39-8429-9d0b7f5df81d.png)
*Figure 3: Table of the percent of missingness for hourly weather data from 2016-2021.*

Precipitation (prcp) is ~8% complete but non-measurements are recorded as NULL. Weather data is unavailable from the Meteostat package for 2021.  Most of the missing rows of temperature, dew point, and humidity are due to the absence of 2021 data.  otherwise, these three fields appear to be missing at random (*Figure 4*).  
![image](https://user-images.githubusercontent.com/30851535/175831091-05f81074-eb7f-467b-81a8-70158d259cc3.png)
*Figure 4: Figure showing the patterns of missingness for hourly weather data from 2016-2021.*
Some of the missing weather data were partially imputed as part of the data import from the Meteostat package.  The package interpolates between successive complete rows to fill in missing rows.  The interpolation is apparent in cross plots of the weather data (Data exploration section).

### imputation
Imputation was conducted using mean imputation or linear regression.  Missing temperature and traffic counts were populated using mean imputation grouped by the month, day, hour, and direction (traffic only).  The missing values for relative humidity and dewpoint were populated using a model that predicted each feature as a function of temperature.  Both the mean imputation and linear regression provide a satisfactory approximation for the missing values (*Figure 5*).

![image](https://user-images.githubusercontent.com/30851535/175831790-e1e8edec-919a-4807-9eb4-79d2cc2178bd.png)
*Figure 5: Predicted value (x-axis) vs actual (y-axis) for relative humidity and dewpoint.  Models predict Y as a function of temperature. Models provide directional guidance for expected values of missing data.*

## Data Exploration

The combined traffic and weather data were analyzed for cyclicity and correlations between features.  The traffic data are highly cyclical with clear daily and seasonal cyclicity.

### Data Trends
  Data are highly cyclical and follow a seasonal pattern.  The strongest patterns for traffic counts are 1) the day of the week, and 2) the time of day (*Figure 6*).
  
![image](https://user-images.githubusercontent.com/30851535/175834244-5ac83bca-3b10-460a-9a2b-f32757a8333e.png) 
*Figure 6: Hourly traffic counts for one, seven-day period.  The patterns observed in this random week from Monday to Sunday are typical. Traffic directions indicated as "primary" and "secondary" refer to east and westbound traffic respectively. Resident on the front Range drive west (secondary, "s") to access the mountains. Each day westward traffic spike around 8 AM and eastbound traffic peaks in the PM.*
  
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
Correlations between vehicle counts and weather data are weak (*Figure 10*).  The strongest correlation with hourly vehicle counts and time.  The day of the week and hour have a correlation coefficient of.18 and .29 respectively with vehicles per hour.  Dew point is weakly correlated with precipitation (r=.2) as expected.  The team examined days with high precipitation to determine any potential effects on traffic counts.  The impacts appear minimal and potentially counterintuitive.  In fact, there is a very slightly negative correlation with precipitation and vehicles per hour suggesting a surprising effect.

![image](https://user-images.githubusercontent.com/30851535/175450083-6839381b-3119-4d8a-9ec6-76299b8f517c.png)
*Figure 10: Correlation plots across the features of interest.* 

Following exploration of the data it was clear that the best predictor of vehicle counts would be time and recent counts (lag).  The weather data was put aside for modelling and instead effort was directed at using time-based models such as ARIMA and SARIMA.

### Autocorrelation
Autocorrelation in time series describe how well correlated a previous value is with a given row.  A dataset with high autocorrelation indicates that the past values are very helpful in predicting future values.
Data were filtered to westbound traffic only (primary). The autocorrelation of the last 100 hours of data were assessed for the traffic data (*Figure 11*). 
![image](https://user-images.githubusercontent.com/30851535/175834730-843f19ac-eb88-499c-ab15-cab62d6baaa4.png)
*Figure 11: Autocorrelation plot of the last 100 hours.*
The autocorrelation plot or correlogram summarizes the strength of the relationship from -1 to 1 between a value and a lag of k. The 95% confidence interval is shown in the blue cone. The detrended data shows strong daily and weekly cyclicity. The high Pearson coefficients indicate that past values are strongly correlated with future values.
The Autocorrelation plot shows troughs and peaks on 24 hour cycles.  The high correlation coefficients (>.5) suggest that the previous 1-4 days could help predict the subsequent values.  
The partial autocorrelation plot suggests only the last few hours are helpful in predicting future values (*Figure 12*). 

![image](https://user-images.githubusercontent.com/30851535/175834889-838a64d8-1e09-49a7-9ded-89a59754275c.png)
*Figure 12:  Partial autocorrelation of hourly traffic data with lags up to 50 hours.*
The partial autocorrelation shows only the correlation that isn't accounted for in earlier lags.  *Figure 12* indicates that the previous 2 hours are the most useful in predicting a subsequent value.  The hourly traffic data exbibit cyclicity at the monthly, weekly, and hourly level. The lagged values are highly correlated with a given row and stay high. Tools such as ARIMA, SARIMA, and Prophet were tasked to develop a time-series model that would perform well against a "test" set.

Data was summed up to weekly totals to simplify the model training workflow. The winning methodology was then applied to the complete hourly dataset.
The autocorrelations and partial correlation plots of the weekly data show similar to what were observed in the hourly data (*Figure 13*).
![image](https://user-images.githubusercontent.com/30851535/175835487-05bb9114-2ea4-44e0-bfe0-b2db48be21d0.png)
*Figure 13: Partial and autocorrelation plots for weekly traffic data.*

*Figure 13* indicates that the last 4 weeks of traffic data are predictive of a given value. The Partial autocorrelation indicates the unique information in each lag. In this context only the last two weekly values are predictive of a future row.

The data, weekly and hourly, were assessed for trends using the AD Fuller test.  in both cases, p values were far below .05 indicating no trends.   
  

# Modelling
The team explored both conventional-statistical and machine learning modelling approaches to time series forecasting. The data was divided into a training and test dataset using a 85/15% split. The split wasn't sampled randomly but just selected the last 15% of all rows.

## ARIMA
ARIMA, auto-regressive inversion moving-average, is a classic model for time series forecasting. The model has several hyperparameters that define the lags that are used to forward predict. We need to specify p,d, and q in that order as arguments for the model. p is the lag of the auto-regression, d is the degree of differencing, and q is the lag for the moving average. 
The manually-specified ARIMA was (2,0,2). 
*Figure 14* is shows the performance of the ARIMA model vs the test set. 

![image](https://user-images.githubusercontent.com/30851535/175835749-bf1b3f66-349f-4d0d-b101-90c94b785f11.png)
*Figure 14: forecasted values compared to test set for the manual ARIMA.*
The manual ARIMA was specified using a pdq of 2,0,2 respectively. The first and last value are using the previous 2 rows to predict a given row. the d=0, indicates that there's no trend in our data and that we don't need to difference the data. 
The predictions in the training set fit the data very well, the predictions to the test set leave much to be desired. The model does predict relatively higher values and lower values that correspond with highs and lows in the test set but the magnitude and fidelity of the predictions are way off.

A Python package designed to solve for the ideal ARIMA hyperparameters was also used.  The auto-ARIMA selected a model with a lower AIC then the manual-ARIMA.  The auto-ARIMA selected the ideal pd,q as (2,0,0) respectively. The forecast performed slightly better than the manual-ARIMA but the fit left a lot to be desired.  the plot is included in the notebook but excluded from this documentation. 


## SARIMA
SARIMA is an ARIMA model with a seasonal component and has an additional 3 hyperparameters that define the seasonality to be used for predictions.
The auto-SARIMA package was used to solve for the ideal tuning parameters.  A wide range of lags was used to ensure the model was considering all relevant solutions. Similarly, to the auto-ARIMA the model selected low hyperparameters values.  The ideal parameters were (1,0,1)(0,0,1)(12).  The "12" indicates the seasonality is 12 months.  The Auto-SARIMA improved the AIC very slightly from 6792 to 6787 (essentially identical).
The forecasted values using the SARIMA do not compare favorably with the test data (*Figure 15*).
![image](https://user-images.githubusercontent.com/30851535/175836795-0feb60f7-e929-4db8-888f-d293bb60c191.png)
*Figure 15: Auto-Sarima forecasts vs the test set.  the model performs very poorly.*

Despite autotuning the auto-ARIMA and auto-SARIMA never approximated the trends in the test set.  The Prophet package developed by Facebook was implemented next.

## Prophet
Despite a rigorous attempt to fit the ARIMA and SARIMA models both manually and automatically the "best" models were relatively poor.  Next, we will experiment using the Prophet time series forecasting package developed by Facebook.

Prophet has several advantages over the conventional statistical methodologies.  prophet is an additive regression model with a piecewise linear growth trend, an annual seasonal model, and a weekly seasonal model.
1) Prophet accounts for the impacts of holidays, 2) Prophet can automatically detect changepoints in time-series trends, and 3) automatically characterize weekly and annual seasonality.  The Prophet package excels with making forecasts on data with like cyclicity to what we observe in the traffic data.

### Weekly Prophet Model

The prophet model was first fitted on the weekly traffic data.  The fits were way better than the ARIMA and SARIMA methods (*Figure 16*).

![image](https://user-images.githubusercontent.com/30851535/175836781-3d48ebf2-206f-47a3-a3f8-1c90aca45a3a.png)
*Figure 16:Weekly Forecasted traffic counts vs weekly actual traffic counts using Prophet*
The weekly Prophet model is doing a good job of following the trends.  Interestingly there appears to be a trend with annual traffic increasing through 2019 and declining after 2020.  We had concluded there was no such trend when we applied the AD Fuller test on the hourly and weekly data.  Although the actual traffic counts fall within the 95% confidence interval of the predictions the predictions are typically low of actuals.  The residuals are greatest in the lows for the Spring and Fall.  2021 isn't an ideal year to use as a test set as there was a mudslide in the Summer of 2021 that forced a road closure and several months of construction delays that certainly impacted drivers' route choice.  Regardless the model outperformed the other methods.  
Prophet allows the user to examine the components of the model.  The components are shown in *Figure 17*. 

![image](https://user-images.githubusercontent.com/30851535/175837748-49adee37-497d-4e3b-8e87-ea46bd50f37b.png)
*Figure 17: Model components.  Prophet shows the impacts of an annual trend as well as any monthly seasonality.*

*Figure 17* indicates an upward annual trend in traffic data with a reduction since 2019.  Also, the model indicates high seasonality associated with the months of the year.  this matched what we see in the actual data.  The Prophet package is doing a good job of approximating weekly data. Prophet was then deployed to predict hourly traffic counts.

### Hourly Prophet Model

The hourly prophet model performs semi-satisfactorily "out of the box" (*Figure 18*). 
![image](https://user-images.githubusercontent.com/30851535/175837986-4194e056-d10c-4567-a9b6-77a2433f1266.png)
*Figure 18: A plot of forecasts from the hourly prophet model (red) vs actuals with the 95% prediction interval.*  

The hourly prophet model is failing to capture the peak traffic patterns observed on Weekends. The weekend troughs are also predicted much lower than expected. in most cases the predictions fall within the 95% confidence interval with the exception of weekends and holidays. We won't spend more time here, but additional tuning would likely help the model. We could also explore "chaining" two models where we use the westbound traffic to predict eastbound traffic. Overall, the model is responding to the fluctuations in the data, but improvements are likely possible and necessary. Holidays need to be added separately to Prophet but that would likely greatly enhance the performance of the model. The model is occasionally predicting negative values which is impossible in this use case. We could try a log transform or scaling to minimize the number of negative values.
The model components of the model resonate with the trends observed in the data (*Figure 19*).

![image](https://user-images.githubusercontent.com/30851535/175838087-fc9ea5e1-3084-4e13-8bfe-b74f7fc18d1a.png)
*Figure 19: Hourly Model components.  Prophet shows the impacts of an annual trend as well as any monthly seasonality.*
*Figure 19* puts everything together. The top plot indicates that the hourly data have no underlying trends.  this matches the findings of the ARIMA and SARIMA but contradicts the weekly Prophet model.  Evidently there's an underlying trend in the weekly traffic values but not in the hourly values.  The last three subplots of *Figure 19* indicate strong relationships between model the Prophet predictions and the day of the week, month of the year, and time of day.  These plots match very well with our observations in the data (heatmaps of *Figures 8-10*) 
The hourly Prophet model is doing a good job of approximating the cyclicity of the data but it is not capturing the weekend or holiday peaks. Prophet has hyperparameters to better account for this and tweaks should be considered.  regardless it is clear that Prophet has advantages over the other 4 models we considered.

Error metrics: RMSE = 322 


# Final thoughts

 ## Improvements

There's additional work to be done in tuning of the Prophet model to better predict peak vehicle counts on holidays and weekends.
Here's some of the future work to be considered:

1) update weather data source to include 2021
2) Incorporate weather data into the traffic predictions
3) Tune Prophet hyperparameters to better fit the training data
4) Add a feature or break-point for 2020 to mark the pandemic as non-representative.
5) Experiment with chaining east and west bound traffic models
6) Experimment with other modeling algorithms including LSTM or Random Forest
7) Negative predictions are nonsensible. Consider transforming data to a square rt or standardization.
