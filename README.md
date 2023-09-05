# London Underground Journey Analysis

London Underground Journey Analysis
Summary
This is a project aimed at analysing how total passenger number have changed over the last 13 years on the London Underground and to see to what extent time series analysis can be used to predict future trends.  As TfL themselves note (TfL, 2012) travel demand is a complicated function of many factors, both on the demand side as well the supply side.   
Factors such as growth in population, health of the economy and demographics all play a part in demand for London Underground Journeys over a long period of time.  This demand is also affected by supply, both London Underground in terms of its capacity, but also of alternate transport options.  These can be alternatives provided by TfL such as the London Overground or Elizabeth Line, as well as other modes of transport such as driving and cycling.

An advantage of looking at passenger numbers on the London Underground, as opposed to the rest of the TfL network, is that the network has not largely changed since 2010. Only the openings of a few stations and some line improvements would have had a small affect on capacity throughout that period.
Of course, the largest single factor affecting passenger demand between 2010 and 2023 on the London Underground is the COVID-19 pandemic.  COVID-19 caused a massive fall in demand due to government lockdowns which the London Underground is still recovering from.  Associated factors such as an increase in working from home may also cause a more long-term fall in demand for the London underground.  However, by April of 2023 London Underground journeys were at 90% of pre-pandemic levels (BBC News, 2023)

The aim of this analysis is to see how well machine learning models can be used to analyse this sort of time series data as well as to forecast when passenger numbers on the London underground are likely to reach pre pandemic levels.  Of particular interest will be seeing how well these models deal with the impact of the COVID-19 pandemic and its consequent effects on passenger numbers.

Data

ETL

The data used for this analysis was TfL’s Journey Type data from 2010 up to June 2023.  This contains data for journeys by all modes of transport offered by TfL up to, and including, their cable car.  The data is largely based on entry and exit data from stations, so must be estimated at least to an extent on some routes, though Wi-Fi tracking would have made this more accurate from 2019 onwards (TfL, 2023).

![image](https://github.com/Vernombre/LunderGroundJourneys/assets/111520178/b9e8c738-0246-4284-bb2d-389834d1ab33)



