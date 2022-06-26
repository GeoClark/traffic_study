# Final Report
# Traffic Forecasting of the Eisenhower tunnel
 *By Kit Clark*

A short form documentation is included in the project file as a README, the following document privides more technical details

# Introduction
The Eisenhower Traffic study provides a coprehensive analysis of a auto-correlated time-series dataset.  Seven years (2015-2021) of hourly traffic data were combined with hourly weather data and analyzed.
The study represents a  full analysis workflow from data-scraping to modelling for time-series data from the Eisenhower and Johnson Tunnels on I-70 on the continental divide, West of Denver Colorado. People who live on the plains in Colorado often head west into the mountains. The Rocky Mountains are a magnificent beauty and source of inspiration. The journey to get there, particualrly in heavy traffic, in contrast is very frustrating. Holiday and weekend traffic can double or triple the amount of time required in a low-traffic conditions.  The authors were interested in understanding the trends in the data and exploring the drivers of heavy traffic.  Predictions were generated using a combination of statistical models and machine learning. ARIMA (autoregression, inversion, moving average), SARIMA (Seasonal ARIMA), and Prophet were trained on 85% of the data and tested on the remainder. The ARIMA, SARIMA, auto-ARIMA, auto-SARIMA models performed poorly, despite lots of hyperparmater tuning.  The prophet model had the best performance.  Although additional wok is required to understand the relationship between weather conditions and traffic counts the currrent model provides accurate predictions that approximate the  daily, hourly, weekly, monthly, and annual sasonality in the data.

# Data
We will be working with hourly count data collected by CDOT (Colorado Dept of Transport) at numerous stations across Colorado. We will be examining the station at the Eisehower tunnel. Data are stored with one column per hour and one row per day for each month.  Hourly traffic and weather data were assembled, cleaned, and imputed prior to modelling. Intuitively weather has a significant impact on traffic. Weather data were included for this reason. After exploring the data and various modelling techniques the weather data were not used in the models. Data are highly cyclical with hourly, daily, weekly, and monthly seasonality.  The final cleaned dataset consists of 122,687 rows and 17 columns, traffic and time 8 time categories (i.e  counts, holidays and weekends) and 9 weather columns.  Missingness was low in the critical traffic data with more widespread missingness in the weather data (Figure 1).

![image](https://user-images.githubusercontent.com/30851535/175829940-bdd7403b-0977-4932-889a-5d6812b2214b.png)
*Figure 1:Data heading for the final combined dataset of weather and traffic data *

## Sources
Hourly data, for two directions (Primary/West, Secondary/East), were pulled for years 2015-2021 from CDOT (Colorado Department of Transit).  Traffic data are available for all stations in Colorado but the scope of this study was limited to traffic counts at the Eisenhower and Johnson tunnels along I-70.
The Eisenhower Tunnel is at 11,158' ft elevation and 26 ft (~8 m) annual snowfall. Recent snowfall has the potential to adjust the behavior of drivers; drivers may wait for roads to be cleared or may take the opportunity to ski fresh snow.
  Hourly weather data were pulled from the nearest station using the Meteostat package.  The nearest weather station was identified using the lat/long coordinates of the tunnel.  Experimentation with the number of weather stations did reduce missingness but included stations from either side of the continental divide.  Each side of the divide is a seperate basin with semi-decoupled patterns of precipitation. The decision was made to include weather data from the Slate/Creek/Copper Mountain station alone. The weather data from the Meteostat package for all sations was  only available  through 2020.  Weather data in 2021 was approximated from the mean value in previous years for a given week.  
  The raw data were in good shape.  The primary obstacle was accounting for missing time rows and imputing missing values for the principal columns of concern.

## Cleaning

### General

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
The strongest correlations with hourly vehicle counts and time (day of week and hour of day).  Dew point is weakly correlated with precipitation levels as expected.  The team examined days with high precipitation to determine any potential effects on traffic counts.  The impacts appear minimal and potentially counterintuitive.  
 Correlations between the features are relatively weak (*Figure 6*).

![image](https://user-images.githubusercontent.com/30851535/175450083-6839381b-3119-4d8a-9ec6-76299b8f517c.png)
*Figure 6: Correlation plots across the features of interest.* 
Data are highly cyclical and follow a seasonal pattern.  There are clear patterns between westbound (away from Denver) and eastbound (towards Denver).  Westbound traffic peaks earlier than eastbound traffic and is highest on Fridays (Figure 2).  

![image](https://user-images.githubusercontent.com/30851535/175527334-4cf1008a-ba3b-44ff-872a-53257abef8ff.png)
*Figure 7: Heat map of hourly traffic for each day of the weak. One plot for each traffic direction.  Traffic peaks between 9-4:00.*

### Data Trends
### Correlations
### Autocorrelation

# Modelling
## Weekly Totals

### ARIMA
### SARIMA
### Prophet

## Hourly Model
### Prophet

# Final thoughts

 ## Findings
 ## Improvements


