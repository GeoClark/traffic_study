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



## Missingness

### Traffic Counts
### Weather Data
## Cleaning
### General
### Missingness

## Data Exploration
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


