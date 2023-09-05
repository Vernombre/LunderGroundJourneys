# London Underground Journey Analysis

## Summary

This is a project aimed at analysing how total passenger number have changed over the last 13 years on the London Underground and to see to what extent time series analysis can be used to predict future trends.  As TfL themselves note (TfL, 2012) travel demand is a complicated function of many factors, both on the demand side as well the supply side.   
Factors such as growth in population, health of the economy and demographics all play a part in demand for London Underground Journeys over a long period of time.  This demand is also affected by supply, both London Underground in terms of its capacity, but also of alternate transport options.  These can be alternatives provided by TfL such as the London Overground or Elizabeth Line, as well as other modes of transport such as driving and cycling.

An advantage of looking at passenger numbers on the London Underground, as opposed to the rest of the TfL network, is that the network has not largely changed since 2010. Only the openings of a few stations and some line improvements would have had a small affect on capacity throughout that period.
Of course, the largest single factor affecting passenger demand between 2010 and 2023 on the London Underground is the COVID-19 pandemic.  COVID-19 caused a massive fall in demand due to government lockdowns which the London Underground is still recovering from.  Associated factors such as an increase in working from home may also cause a more long-term fall in demand for the London underground.  However, by April of 2023 London Underground journeys were at 90% of pre-pandemic levels (BBC News, 2023)

The aim of this analysis is to see how well machine learning models can be used to analyse this sort of time series data as well as to forecast when passenger numbers on the London underground are likely to reach pre pandemic levels.  Of particular interest will be seeing how well these models deal with the impact of the COVID-19 pandemic and its consequent effects on passenger numbers.

## Project Structure

- 📁 [Data](https://github.com/Vernombre/LunderGroundJourneys/tree/main/Data): This folder contains the raw data files downloaded from TfL.
- 📁 [London_Underground_Journey_Analysis](https://github.com/Vernombre/LunderGroundJourneys/blob/main/London_Underground_Journeys_Analysis.ipynb): The Colab workbook in which the data was transformed and analysed.

## Data

### ETL

The data used for this analysis was TfL’s Journey Type data from 2010 up to June 2023.  This contains data for journeys by all modes of transport offered by TfL up to, and including, their cable car.  The data is largely based on entry and exit data from stations, so must be estimated at least to an extent on some routes, though Wi-Fi tracking would have made this more accurate from 2019 onwards (TfL, 2023).

![image](https://github.com/Vernombre/LunderGroundJourneys/assets/111520178/b9e8c738-0246-4284-bb2d-389834d1ab33)

_Table 1. TfL’s Journey Type dataset_

Each year in the data is divided into 13 periods, mostly of 28 days, but this can vary a bit on the first and last periods of the financial year.  This is not ideal for the type of models that will be applied to this dataset. Ideally a consistent frequency of observations is needed, or at least for the data to be formatted in a more commonly used way.

Initially, the Days in period variable was used to transform Undergrounds journeys (m) into Passengers Per Day (PPD) for each item in the dataset.  This data was then resampled (Hallouard, 2020) to provide two new datasets.  Firstly, the data was downsampled to provide daily breakdown of the data, with linear interpolation used to fill in the missing values.  This daily data can then be upsampled into monthly values comprised of the arithmetic mean of the daily values.

![image](https://github.com/Vernombre/LunderGroundJourneys/assets/111520178/6d13d540-6f06-4f21-b9f1-7999aa4a9b0d)

_Figure 1. Process used to transform the dataset._

The monthly dataset will be used for analysis though there are still a few issues with this data.  Months also have an inconsistent number of days similar to the original dataset which can lead to issues such as uneven amounts of weekends in each period, slightly skewing passenger numbers.

### EDA

Average daily passengers per month can be plotted over the whole time period to give an overview of the data:

![image](https://github.com/Vernombre/LunderGroundJourneys/assets/111520178/fc5a26e3-d53e-45a2-8ad1-df4687726cef)

_Figure 2. Chart showing daily passenger numbers on the London Underground over time._

Key characteristics can already be seen in the time series data:

•	Seasonality: especially shown by a fall in passenger numbers every Christmas.  Similar, but smaller, seasonal drops in passenger numbers likely align with school holidays.

•	Trends: a gradual upward trend leading up to COVID-19, then a more extreme upwards trend afterwards as passenger numbers recover.

•	Outliers: there are the obvious outliers from the COVID-19 pandemic, but also in 2015 where there are higher numbers in March and lower in September compared to other years.

•	Stationarity: the time series is not currently stationary as shows obvious trends and seasonality as well as inconstant variance and autocorrelation.

### Stationarity

The data appears to be non-stationary, and this can be confirmed with an Augmented Dickey-Fuller test:

![image](https://github.com/Vernombre/LunderGroundJourneys/assets/111520178/43501df4-cde4-464c-86f5-0aaf64fa6380)

_Table 2. ADF test results on original dataset._

The ADF tests the null hypothesis that the time series has a unit root, meaning that it is nonstationary.  From the results above we can see that the critical values at all significance levels are less than the ADF test statistics, as well as the p-value being greater than 0.05.  The null hypothesis cannot be rejected, and the time series is indeed non-stationary.

To make the time series stationary the data can differenced:

![image](https://github.com/Vernombre/LunderGroundJourneys/assets/111520178/ba6b8047-cd96-403e-a03d-91ac0bbb1ae1)

_Figure 3. Chart showing the differenced time series._

The data now appears much more stationary, so the ADF test can be applied again, as well as to 2nd order differencing just to be sure:

![image](https://github.com/Vernombre/LunderGroundJourneys/assets/111520178/ff2ecd21-d25b-4dbb-97b1-d59241f10bd0)

_Table 3.  ADF results for 1st and 2nd order differenced time series data._

The p-value is now below the significance level of 5%, but for the 1st order differenced data the 1% significance critical value is still less than the ADF test statistic so the null hypothesis cannot be rejected.  The 2nd order difference data has critical values greater than the ADF test statistic at all significance levels so can be said to be stationary.

### STL decomposition

Other characteristics seen in the series can by quantified by using Seasonal-Trend decomposition using LOESS (STL) on the differenced data:

![image](https://github.com/Vernombre/LunderGroundJourneys/assets/111520178/95b8ac4c-25ed-4eac-b91a-04f5787ff449)

_Figure 4. STL analysis of the 2nd order differenced data._

The trend seen in the original time series has largely disappeared since the series is stationary now, with only fluctuations caused by the residuals around COVID-19 as well as the 2015/2016 period.  The 12-month seasonality is also retained.

### ACF and PACF

The correlations between the individual values in the time series can be analysed by applying an Autocorrelation Function (ACF) and partial Autocorrelation Function (PACF):

![image](https://github.com/Vernombre/LunderGroundJourneys/assets/111520178/83abecda-83ef-4305-83ec-71808b6d5f7e)
![image](https://github.com/Vernombre/LunderGroundJourneys/assets/111520178/0b270cb7-cb67-49dd-ab83-ef413eaf7014)

_Figure 5. ACF and PACF of the 2nd order differenced data._

The ACF shows significant spikes at lag 1,6,11 and 12 showing a strong correlation with the seasonality that has already been seen.  The PACF shows more of a gradual geometric decline suggesting there is a moving average term in the data, with some effect from residuals.

## Analysis

### ARIMA

The AutoRegressive Integrated Moving Average (ARIMA) statistical model will be applied to dataset first, though this is not necessarily ideal since the model does not account well for the seasonality in the data.  ARIMA uses three parameters to model time series data:

•	P (Auto Regression): the number of autoregressive terms to be used.  Due to the four significant lag values in the PACF this will be set to 4.

•	D (Integration): the order to which the data needs to be differenced to become stationary.  The data was differenced twice to become stationary so this will be set to 2.

•	Q (Moving Average): The number of lagged forecast errors.  Due to the potential moving average term in the PACF as well as forecast error in the ACF are caused by seasonality, this will be set to 0.

![image](https://github.com/Vernombre/LunderGroundJourneys/assets/111520178/4b61ac18-5890-41b6-b51e-0912683cde79)

_Figure 6. Charts showing the ARIMA (4,2,0) model fitted to the dataset and the forecast for future dates._

Whilst the model fits the data fairly well the limitations can be seen in the forecast, with no account for seasonality as well as an indefinite linear trend.

### SARIMAX

The SARIMAX model is a variation of ARIMA that includes seasonality.  As we have a strong yearly seasonality this can be incorporated by SARIMAX:

![image](https://github.com/Vernombre/LunderGroundJourneys/assets/111520178/84d01d86-d437-4c36-b990-4193e55e019b)

_Figure 7. Charts showing the SARIMAX (4,2,0) (1,1,1,12) model fitted to the dataset and the forecast for future dates._

Seasonality is now included in the forecast though there is still a linear upwards trend due to using the same parameters as the ARIMA model and using placeholder values for the seasonal parameters used by the SARIMAX model.

### Auto ARIMA

To find more optimal parameters to model the data Auto ARIMA can be used.  Auto Arima can perform a stepwise (or parallel) iterative process to find the optimal parameters for an ARIMA model, aimed at reducing the prediction error by minimising the information criterion.  

![image](https://github.com/Vernombre/LunderGroundJourneys/assets/111520178/7c492b30-4cab-40da-8f72-b72203cf7d9e)

_Figure 7. Charts showing the Auto ARIMA (2,1,0) (3,1,0,12) model fitted to the dataset and the forecast for future dates._

The Auto ARIMA process’s parameters returned a model that fit the data better than the models where the parameters were inferred manually.  This can be quantified by looking at some of the metrics the process is aimed at minimising, in this case the log of the likelihood and the Akaike Information Criterion (AIC).  It is worth noting that Auto Arima looks considers more metrics than just these, as is shown by the fact the process did not choose the model with the lowest AIC or log likelihood as the best fit to the data:

![image](https://github.com/Vernombre/LunderGroundJourneys/assets/111520178/d44a64b1-9d65-4282-9cb6-c6ef8af5fbdf)

_Figure 8. Best models evaluated by Auto ARIMA process._

![image](https://github.com/Vernombre/LunderGroundJourneys/assets/111520178/9d7844ef-35fb-4d81-bbb1-3f5e00d945c9)

_Table 4.  Log Likelihood and AIC for each ARIMA model._

### Prophet

Prophet is a tool that should make time series forecasting simpler without the need for parameter tuning.  However, when applied to our dataset it clearly struggles:

![image](https://github.com/Vernombre/LunderGroundJourneys/assets/111520178/cbc04273-2980-45dc-adee-966fc36b7092)

Prophet’s struggles with data affected by COVID-19 are well documented (Sarem, 2022) though there are ways to mitigate this (Prophet, 2023).

## Conclusions

The last time a record was set for passenger numbers on the London Underground was Christmas 2015 (TfL, 2015).  By fitting the best model from above it would seem this record may not be broken until Christmas of 2026:

![image](https://github.com/Vernombre/LunderGroundJourneys/assets/111520178/f8549cb9-78df-487c-b05e-6912724134f1)

_Figure 10. Chart showing when the Auto ARIMA forecast will reach previous max number of passengers per day._

Is this likely to be accurate?  Maybe…but probably not.  The record was last broken in 2015 not 2019, it may be the case that this record is never broken again.  For a system as complicated as London’s transport network, of which the underground is just one part, analysis of solely headline figures such as passenger numbers is likely to be lacking.  Models that incorporate covariates (Sivek, 2020) from socioeconomic factors through to the weather, are likely to be more effective.
