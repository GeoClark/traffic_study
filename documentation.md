# Final Report
# Traffic Forecasting of the Eisenhower tunnel
 *By Kit Clark*

A short form documentation is included in the project file as a README, the following document privides more technical details

# Introduction
The Eisenhower Traffic study provides a coprehensive analysis of a auto-correlated time-series dataset.  Seven years (2015-2021) of hourly traffic data were combined with hourly weather data and analyzed.
The study represents a  full analysis workflow from data-scraping to modelling for time-series data from the Eisenhower and Johnson Tunnels on I-70 on the continental divide, West of Denver Colorado. People who live on the plains in Colorado often head west into the mountains. The Rocky Mountains are a magnificent beauty and source of inspiration. The journey to get there, particualrly in heavy traffic, in contrast is very frustrating. Holiday and weekend traffic can double or triple the amount of time required in a low-traffic conditions.  The authors were interested in understanding the trends in the data and exploring the drivers of heavy traffic.  Predictions were generated using a combination of statistical models and machine learning. ARIMA (autoregression, inversion, moving average), SARIMA (Seasonal ARIMA), and Prophet were trained on 85% of the data and tested on the remainder. The ARIMA, SARIMA, auto-ARIMA, auto-SARIMA models performed poorly, despite lots of hyperparmater tuning.  The prophet model had the best performance.  Although additional wok is required to understand the relationship between weather conditions and traffic counts the currrent model provides accurate predictions that approximate the  daily, hourly, weekly, monthly, and annual sasonality in the data.

# Data
We will be working with hourly count data collected by CDOT (Colorado Dept of Transport) at numerous stations across Colorado. We will be examining the station at the Eisehower tunnel. Data are stored with one column per hour and one row per day for each month.  Hourly traffic and weather data were assembled, cleaned, and imputed prior to modelling. Intuitively weather has a significant impact on traffic. Weather data were included for this reason. After exploring the data and various modelling techniques the weather data were not used in the models. Data are highly cyclical with hourly, daily, weekly, and monthly seasonality.  The final cleaned dataset consists of 122,687 rows and 17 columns, traffic and time 8 time categories (i.e  counts, holidays and weekends) and 9 weather columns.  Missingness was low in the critical traffic data with more widespread missingness in the weather data.

## Sources

### Traffic Data

### Weather Data

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


