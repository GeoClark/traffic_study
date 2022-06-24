# traffic_study

![image](https://user-images.githubusercontent.com/30851535/175295769-474a100b-8e3d-47a8-872d-a1cd5a7056cc.png)

## Introduction

*The following notebook assembles a clean dataset of weather and traffic data from the Eisenhower and Johnson Tunnels on I-70 on the continental divide, West of Denver Colorado.  Seven years of hourly traffic counts for east and westbound traffic were examined.  People who live on the plains in Colorado often head west into the mountains.  The Rocky Mountains are a magnificent beuty and source of inspiration.  The journey to get there, particualrly in heavy traffic, in contrast is very frustrating.  Holiday and weekend traffic can double or triple the amount of time required in a low-traffic conditions.  
Predictions were generated using a combination of  statistical models and machine learning.  ARIMA (autoregression, inversion, moving average), SARIMA (Seasonal ARIMA), and Prophet were trained on 85% of the data and tested on the remainder.  The multiple models were built on the weekly traffic totals and assessed.  The winning model was then applied to the hourly data.*


## Data
Hourly traffic and weather data were assembled, cleaned, and imputed prior to modelling.  Intuitively weather has a significant impact on traffic.  Weather data were included for this reason.  After exploring the data and various modelling techniques the weather data were not used in the models.  Data are highly cyclical with hourly, daily, weekly, and monthly seasonality.  Traffic steadily increases annually until 2020 where an extreme drop in daily counts occurs due to the pandemic.

![image](https://user-images.githubusercontent.com/30851535/175449802-e4abb32d-333b-4805-95d8-7e5c5b371c22.png)
Figure: Yearly data
### Sources

Hourly data, for two directions, were pulled for years 2015-2021 from CDOT (Colorado Department of Transit).  
  Hourly weather data were pulled from the nearest station using the Metestat package.  The weather data from the Meteostat package were only available  through 2020.


### Data Cleaning
The weather and traffic data were mostly complete.  Six years of data with 122,688 rows
The traffic data had limited missingness assocciated with outages that were clustered around certain times.  Outages were typically limited to 1-2 days but may occur in rapid succession.  Weather data had high missingness for values beyond temperature, humidity, and dewpoint.  Temperature and humidity were sufficiently complete to build some simple linear models for imputation.  Missing temperature data was imputed using mean imputation from the same week in other years.  All of 2021 temperature data is absent from the Meteostat datasource.  
The final dataset has no missingess and all columns are in the appropriate format for modelling. 

![image](https://user-images.githubusercontent.com/30851535/175458850-b3dfb880-bca8-47c7-b8a0-a68ddbd2bd42.png)
Figure: Hourly traffic. Missingness.

## Data Exploration


![image](https://user-images.githubusercontent.com/30851535/175449936-e690cb38-178a-441b-aa7e-e6850774283b.png)
Weekly data

![image](https://user-images.githubusercontent.com/30851535/175450083-6839381b-3119-4d8a-9ec6-76299b8f517c.png)
corr plots


## Modelling


## Methods



## Improvements

change weather data source
include weather data in modells
tune model to weight weekends
