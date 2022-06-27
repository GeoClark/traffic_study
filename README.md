# traffic_study

![image](https://user-images.githubusercontent.com/30851535/175295769-474a100b-8e3d-47a8-872d-a1cd5a7056cc.png)

## Introduction

*The following notebook assembles a clean dataset of weather and traffic data from the Eisenhower and Johnson Tunnels on I-70 on the continental divide, West of Denver Colorado.  Seven years of hourly traffic counts for east and westbound traffic were examined.  People who live on the plains in Colorado often head west into the mountains.  The Rocky Mountains are a magnificent beuty and source of inspiration.  The journey to get there, particualrly in heavy traffic, in contrast is very frustrating.  Holiday and weekend traffic can double or triple the amount of time required in a low-traffic conditions.  
Predictions were generated using a combination of  statistical models and machine learning.  ARIMA (autoregression, inversion, moving average), SARIMA (Seasonal ARIMA), and Prophet were trained on 85% of the data and tested on the remainder.  The multiple models were built on the weekly traffic totals and assessed.  The winning model was then applied to the hourly data.*


## Data
Hourly traffic and weather data were assembled, cleaned, and imputed prior to modelling.  Intuitively weather has a significant impact on traffic.  Weather data were included for this reason.  After exploring the data and various modelling techniques the weather data were not used in the models.  Data are highly cyclical with hourly, daily, weekly, and monthly seasonality.  Traffic steadily increases annually until 2020 where an extreme drop in daily counts occurs due to the pandemic.  Traffic is highest in Summer and winter with lower counts in the Fall and Spring

![image](https://user-images.githubusercontent.com/30851535/175449802-e4abb32d-333b-4805-95d8-7e5c5b371c22.png)
Figure 1: Daily traffic data plotted by time with one line for each year.
### Sources

Hourly data, for two directions, were pulled for years 2015-2021 from CDOT (Colorado Department of Transit).  
  Hourly weather data were pulled from the nearest station using the Meteostat package.  The weather data from the Meteostat package were only available  through 2020.


### Data Cleaning
The weather and traffic data were mostly complete.  Six years of data with 122,688 rows
The traffic data had limited missingness assocciated with outages that were clustered around certain times.  Outages were typically limited to 1-2 days but may occur in rapid succession.  Weather data had high missingness for values beyond temperature, humidity, and dewpoint.  Temperature and humidity were sufficiently complete to build some simple linear models for imputation.  Missing temperature data was imputed using mean imputation from the same week in other years.  All of 2021 temperature data is absent from the Meteostat datasource.  
The final dataset has no missingess and all columns are in the appropriate format for modelling. 

## Data Exploration
The strongest correlations with hourly vehicle counts and time (day of week and hour of day).  Dew point is weakly correlated with precipitation levels as expected.  The team examined days with high precipitation to determine any potential effects on traffic counts.  The impacts appear minimal and potentially counterintuitive.  
 Correlations between the features are relatively weak (Table 1)

![image](https://user-images.githubusercontent.com/30851535/175450083-6839381b-3119-4d8a-9ec6-76299b8f517c.png)
Table 1: Correlation plots across the features of interest.  
Data are highly cyclical and follow a seasonal pattern.  There are clear patterns between westbound (away from Denver) and eastbound (towards Denver).  Westbound traffic peaks earlier than eastbound traffic and is highest on Fridays (Figure 2).  

![image](https://user-images.githubusercontent.com/30851535/175527334-4cf1008a-ba3b-44ff-872a-53257abef8ff.png)
Figure 2: Heat map of hourly traffic for each day of the weak. One plot for each traffic direction.  Traffic peaks between 9-4:00.


![image](https://user-images.githubusercontent.com/30851535/175449936-e690cb38-178a-441b-aa7e-e6850774283b.png)
Figure 3: Hourly data for a random week. Westbound traffic (P) and eastbound traffic (S) are posted. 

## Modelling Methods
The team explored both conventional-statistical and machine learning modelling approaches to time series forecasting.  Data was summed up to weekly totals to simplify the model training workflow.  The prefeered method was then applied to the hourly data.
ARIMA, auto-regressive inversion moving-average, is a classic model for time series forecasting. The model has several hyperparamters that define the lags that are used to foreward predict. We need to specify p,d, and q in that order as arguments for the model. p is the lag of the autoreregression, d is the degree of differencing, and q is the lag for the moving average. 
SARIMA is an ARIMA model with a seasonal component and has an additional 4 hyperparameters that define the seasonality to be used for predictions.  Both of these models were specified manually; auto-ARIMA and auto-Sarima were also utilized.  All four of these models performed  poorly on the test set (Figure 4).

![image](https://user-images.githubusercontent.com/30851535/175533388-6ef621b8-442a-4e98-bf2f-5e8992b2223f.png)
Figure 4: Auto-ARIMA forecasts vs test set.  The predictions generalize very poorly.

Facebook prophet, a popular time series forecaster, was used to determine if other methods could outperform the ARIMA and SARIMA models.
The model performed better on the weekly predictions right out of the box(Figure 5).  The predictions are low relative to actuals but much better than the SARIMA and ARIMA.  Prophet tends to underpredict peak traffic, a phenomenon more evident in the hourly model (Figure 6).

![image](https://user-images.githubusercontent.com/30851535/175535920-0a538398-c401-4872-b803-9c03893435d2.png)
Figure 5: Prophet weekly traffic predictions.  Actuals fall within the 95% confidence interval but are typically high of predictions.


![image](https://user-images.githubusercontent.com/30851535/175536123-f22c60eb-f214-4764-973f-39487d18301a.png)
Figure 6: Prophet hourly traffic model test set performance. 

The model is doing a good job of approximating the cyclity of the data but it is not capturing the the daily or holiday peaks.  Prophet has hyperparemeters to better account for this and should be considered.

Error metrics:
RMSE = 322

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


