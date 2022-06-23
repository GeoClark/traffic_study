# traffic_study

![image](https://user-images.githubusercontent.com/30851535/175295769-474a100b-8e3d-47a8-872d-a1cd5a7056cc.png)

## Introduction

*The following notebook assembles a clean dataset of weather and traffic data from the Eisenhower and Johnson Tunnels on I-70 on the continental divide, West of Denver Colorado.  Four years of fourly traffic counts for east and westbound traffic were examined.  People who live on the plains in Colorado often head west into the mountains.  The Rocky Mountains are a magnificent beuty and source of inspiration.  The journey to get there, particualrly in heavy traffic, in contrast is very frustrating.  Holiday and weekend traffic can double or triple the amount of time required in a low-traffic conditions.  
Predictions were generated using a combination of  statistical models and machine learning.  ARIMA (autoregression, inversion, moving average), SARIMA (Seasonal ARIMA), and Prophet were trained on 85% of the data and tested on the remainder.  The multiple models were built on the weekly traffic totals and assessed.  The winning model was then applied to the hourly data.*


## Data
Hourly traffic and weather data were assembled, cleaned, and imputed prior to modelling.  Intuitively weather has a significant impact on traffic.  Weather data were included for this reason.  After exploring the data and various modelling techniques the weather data were not used in the models.
### Sources

Hourly data, for two directions, were pulled for years 2016-2021 from CDOT (Colorado Department of Transit).  
  Hourly weather data were pulled from the nearest station using the Metestat package.  The weather data from the Meteostat package were only available  through 2020.


### Data Cleaning
The weather and traffic data were largely complete
The traffic data had limited missingness assocciated with outages that were clustered around certain times.  Outages were typically limited to 1-2 days but may occur in rapid succession.  Weather data had high missingness for values beyond temperature, humidity, and dewpoint.  Temperature and humidity were sufficiently complete to build some simple linear models for imputation.  Missing temperature data was imputed using mean imputation from the same week in other years.  All of 2021 temperature data is absent from the Meteostat datasource.  
The final dataset has no missingess and all columns are in the appropriate format for modelling. 


## Data Exploration



## Modelling


## Methods



## Improvements

change weather data source
include weather data in modells
tune model to weight weekends
