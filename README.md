# Grab-AIforSEA

This is my entry to Grab's AI for S.E.A. Challenge. For my assignment, I chose to do the Traffic Management problem. The objective of the problem is to help improve the traffic congestion for Southeast Asia through the Grab's booking demand.

## Problem Statement

Economies in Southeast Asia are turning to AI to solve traffic congestion, which hinders mobility and economic growth. The first step in the push towards alleviating traffic congestion is to understand travel demand and travel patterns within the city.

> Can we accurately forecast travel demand based on historical Grab bookings to predict areas and times with high travel demand?

## Approach

For this problem I went with an Auto-encoder - LSTM - SARIMA - Random Forest Ensemble for the forecasting. The Autoencoder is to allow me to frame this problem as a univariate-multistep forecasting problem. This allowed me to use LSTM and SARIMA as my time-series forecasting models. The LSTM is a static model which was trained under a supervised learning approach. The SARIMA acts as a dynamic model where there is no prior training but with grid-search utilized to come up with a good hyperparameter setting. The LSTM + SARIMA forecasts were then combined together via Random Forest Regression. The idea behind it was to minimize the deviation of the forecasts especially when its farther from the current time. The output of the Random Forest model would then be converted back to the individual date/geohash6 element to allow RMSE calculation and evaluation as part of the project evaluation.

I also added a small data exploration and analysis portion to check if there are insights that can be gained from the current data. I was able to get trends and seasonalities information for the provided data which could be quite useful in providing a solution to the problem in the challenge which is traffic management. There is also a provided heatmap to visualize the demand on a spatiotemporal level.

## Assumptions

There were some assumptions made prior to creating the model. First was that I am assuming that the data between day 61 and the time $t$ is going to be provided. The models *can* walk towards the current time $t$ from day 61 but without the true values, the model would understandably get further from the actual values. Another assumption I made is that even if the data between day 61 and time $t$ is not provided, the last 14-day equivalent data would be provided. This would make the model a one-shot model instead of a walk-forward model, where the evaluator will have to position the model to time $t$ and ask it to forecast times $(t+1) ... (t+5)$ as indicated on the problem.

## General Instructions

The _______ Notebook is provided for the evaluation of the final model. Additional notebooks were also provided to show the solutions and thought process on how the models were created. You can take a look at the <FORECASTING EXPLORATION notebook> for the forecasting model workbook. The <EDA notebook> is also provided to get an idea of the findings on the project.

## Results

## Findings

## Spatial

In terms of studying the demand and its relationship with the 2D space, we see several notable insights.

### High Demand Areas

<p align='center'><img src="https://github.com/iocfinc/Grab-AIforSEA/blob/master/images/AIFORSEA-HEATMAP-HIGHDEMAND.png" alt="Major Areas - Bloom Plot"></p>

We have two major locations with a high concentration of demands. These two areas seem to correspond to a major district where demand would be high like city centers or CBDs **Red Circles in the map below**. I am thinking this is a highly built-up area where there is a high concentration of people. This area tends to exhibit high demand in a small geographic area (around 8 hexbins).

<p align='center'><img src="https://github.com/iocfinc/Grab-AIforSEA/blob/master/images/TO_EDIT_HTMAP.png" alt="Major Areas Plot"></p>

Aside from the two major areas, we see two areas that also had high demand but more spread out through a wider geographic area **Yellow circles**. These areas continually show demand, not as high as the two major areas but due to the consistency of the spread of demand we can say that it is still a high demand area. In the context of the earlier areas of high concentration as CBDs where there are high-rises and major businesses and places of interests, these two areas tend to be more spread out so I am thinking something of a suburban setting where the development is more horizontal instead of vertical. So think of houses instead of condos for these areas.

Identifying these areas of high demand and their relative concentration/density is useful in planning our services and approximating the traffic situation. For example, it is plausible to imagine that the areas with higher demand density would be more prone to traffic congestion. We are physically limited to how much cars we can put on an area especially well built up ones so it would be useful to make certain policies on how to serve these high-density areas so that we are not adding to the traffic congestion without affecting the quality of service we provide to our users.

### Unserved Areas

<p align='center'><img src="https://github.com/iocfinc/Grab-AIforSEA/blob/master/images/TO_EDIT_HTMAP.png" alt="Major Areas Plot"></p>
![Underserved Areas Plot](https://github.com/iocfinc/Grab-AIforSEA/blob/master/images/AIFORSEA-HEATMAP-UNDERSERVED.png)

I have created a snapshot of a heatmap with locations without demand registered colored blue (which makes sense considering this area is in the middle of the ocean :lol: jk). In any case, there are certain areas where there is continually no demand. From this, we can get a "lay of the land" even without the use of a reference/overlay. In a way, it does make sense that there are areas without demand or service. Areas where it would be physically impossible to have a demand, for example, the middle of the sea or inside a big park, or possibly there is a river in our map (which would explain the split of concentration of areas of demand). No city is exactly square, or rectangular for this matter, so it should be fine to have a few gaps. Without an actual reference/overlay we can only assume what is causing the absence of demand. But in terms of planning trips and resource positioning, knowing areas where there is no demand is useful to avoid unnecessarily sending resources to known areas of no demand.


## Temporal


### Seasonal Decomposition

<p align='center'><img src="https://github.com/iocfinc/Grab-AIforSEA/blob/master/images/AIFORSEA-seaonality_decompose-96.png" alt="AIFORSEA-seaonality_decompose-96 Plot"></p>

In terms of the time component, I did some seasonal decomposition of the encoded data. It is one added benefit of converting the demand data into an encoded high-level representation. This way, I was, for the most part, able to analyze a representation of the demand we have in a single value. First of is the trend and seasonalities as well as the residuals plot.

Having this information allows us to see and get an idea of how our demand is with respect to time. We can see the demand in terms of daily, weekly, hourly and even monthly timeframe. The graph above shows our data on a "daily" frequency. I think it is daily if I understood the frequency setting correctly :lol: The **observed** data shows the daily values of the demand. It is basically the combination of the three lower plots. The observed values are the sum of the components on that specific time. The **trend** graph is going to be the weekly timeframe component. We can see based on the trend of how the demand flows through the week. We see the demand's crest and through at a fairly regular interval. We see from the trend which days have high *base* demand. I think it is fair to assume that the low points of the trend are weekends where generally demand would be lower. The **seasonal** data is the representation of the hourly data. It is highly regular to the point that it is almost sinusoidal in nature. We can see the demand flow per hour of every day. Obviously, demand will never be consistent (it would be nice for Grab if it was) and if we dig a little deeper with the seasonal graph we can see that the demand tends to be low in the late afternoon towards the evening. The demand will then start to pick up and grow during the late evenings and finally crest during the morning until around lunchtime. From lunchtime (noon) towards the afternoon, the demand returns to a downtrend. The last component of the decomposition would be the **residuals**. The *noise* of the model if there ever was one. These are the movements possibly of the minutes throughout the day.  We can see some sort of structure on the residuals. What's important to point out is that the residuals can be investigated further to get an idea of which *peaks* can be explained. For example, the high spike on March 18, 2019, can be explained by missing timestamps on the dataset. The missing timestamps happened to coincide during the peak hours of demand (noon) so the missing data, which would be interpolated or filled with zero, was considered a sudden drop (spike). From peak demand, a sudden data point loss is understandably going to spike the residuals. Being able to check these times on the data can allow us to make new features for future implementation.

<p align='center'><img src="https://github.com/iocfinc/Grab-AIforSEA/blob/master/images/AIFORSEA-seaonality_decompose-672.png" alt="AIFORSEA-seaonality_decompose-672 Plot"></p>

How does this help in the context of traffic management? Knowing these cycles would be useful in planning our resources, again. Knowing the day of the week that has considerably less demand allows us to schedule how much cars we send out on the roads. We can schedule the rotation of our driver partners so they can spend more time with their family during off-days. Knowing which hour of the day has more demand and knowing when the demand starts to peak and a when it starts to wane allows us to better inform our driver on when to stay out on the road and when they could opt to rest or have breaks. Knowing when the fairly regular things happen would be a big help in planning the scheduling of our drivers. By knowing when it is likely for demand to rise we would be able to time our resources to meet the increase which would help improve our service to customers so that they will not have to wait long to get a booking. Knowing when the demand would wane allows us to reduce the number of cars we put on the street by coordinating with our drivers on when they would be better off staying put and resting. For example in the afternoon or evening, by knowing that demand will be low beforehand we can coordinate with our drivers so that they would not be loitering on the roads which should help in reducing the number of cars on the road and in effect help in the traffic congestion which we are trying to solve.

The one thing we must take care of when dealing with is the residuals data. If you are familiar with the "coconut" reference then these are our coconuts. Yes, it is helpful to study these residuals to see if we can correlate spikes to certain information, for example, a spike which was caused by a transport strike. Or a sudden surge due to an event. We can use the residuals as a reference so that we can set up other sources of information. For example, if we see a sudden surge and notice that there was a trending item on social media about a concert then we can in the future check for these sorts of anomalies so they can at least be flagged and we can adapt. The coconuts are coconuts for a reason. They are rare events which have a considerable impact on our demand. Although I don't think modeling residuals would be worth it, they are really noisy, using them in the context of early warning can be useful. With the case of missing data, it is not related to traffic, but we can possibly create a monitoring system for our infrastructure as well. It may not be traffic related but it will certainly help when we know if there is a failure in our internal infrastructure which could lead to downtimes.

> You may have noticed that all the findings tend to point towards planning and resource allocation. This is because its the only thing we can do with these insights. We can always model data but that is always going to be subject to 

### Spatiotemporal

Now we combine both the **when** and the **where** data. In **Temporal** we get an idea of how demand moves with time. With **Spatial** we see how demand is distributed in space. Moving further, with the help of heatmaps and animation, we can get an idea of how demand is distributed in time and in space.

<p align='center'>
<img src="https://github.com/iocfinc/Grab-AIforSEA/blob/master/images/HEATMAP-DAY7-10_giphy.gif" alt="Heatmap day 7-10 gif">

<b><a href='https://github.com/iocfinc/Grab-AIforSEA/blob/master/images/' alt='HEATMAP ANIMATION VIDEOS'></a></b>
<br>
</p>

Making these animated heatmaps should reinforce what we have been noticing in the spatial and temporal. For example, we will notice that the heatmap *blooms* during times where we have high demand indicated in the observed data. Translating this into the heatmap allows us to see where those areas that had high demand contributions are located. We can get an idea of which areas are active at a given time. We see possible areas of interest that would show high demand relative to the areas surrounding it.

One example insight we can get is in checking which area/s would have to be served during late hours. We have stated in the temporal analysis section that our demand tends to die down during the late afternoons and start to pick up late in the evening. Knowing where these areas of demand are after a period of low demand will allow us to focus our drivers towards that area on a timely manner, this way they don't stay too long in the area so they reduce their contribution to congestion. Another insight we can also get is where are the active areas when its early morning. We know that our demand will steadily grow from late in the night (11PM+) towards the next day. One possible explanation for this would be the absence of other forms of transport. When we know which areas are active during the *off hours* we can advise drivers to go towards known areas of demand so that they get rides faster.

I mentioned that there are areas that are out of phase in terms of the demand of the region as a whole. One example would be this location. It tends to have demand late in the nights and early morning but less during lunch time. It is out of sync from the majority of the locations. I'm guessing this is a major location, possibly an airport based on the times of its demand and its approximate distance from the city center where high demand is concentrated.

Having a spatiotemporal representation allows us to have a model of how our demand behaves at different areas at specific times. Know this allows us to adjust our plans accordingly and have initiatives that could help in decongesting the "city".

## Improvements

There are a lot of features that could be added to the ones provided. Some were not implemented due to the obfuscation of the details on the dataset (location and dates). For example, landmarks overlayed to the spatial map would be useful in understanding the nature of the demand in certain areas. The use of holidays as a regressor could also be added after the actual dates have been provided. The timestamp and date values on the dataset were not provided so I did not think it was wise to add special holidays as a regressor since we cannot localize the data and the timings.

On the prediction model itself, I think a lot of improvements can be made. The use of a CNN to get the effect of neighboring geohashes to the demand could be explored. Longer lookback window can also be considered for future implementation. The current lookback window for the models was set to 25 periods (~6.25 hours). The challenge can accept up to 14-days worth of data (1344 points). Relative to the maximum allowable I am only using 1.86%, it might be worth looking at higher windows to see if any improvement can be extracted. The autoencoder can also use some work. The autoencoder implemented on this project is simply relative to what could be possible for the context. A 2D CNN autoencoder that will look at the spatial data (long and lat) arranged demand could be explored. The autoencoder results could also be rescaled to help the downstream models in achieving a better result. We can take a look at adding other models like varying lookback periods and varying hyperparameter setups prior to ensembling to see if that achieves a better score. There are a lot of possible avenues to explore on this model but for this challenge, I'm quite happy with the MVP I had created.

## References:

[Hyperparameter Tuning the Random Forest in Python](https://towardsdatascience.com/hyperparameter-tuning-the-random-forest-in-python-using-scikit-learn-28d2aa77dd74)

[Save and Load Machine Learning Models in Python with scikit-learn](https://machinelearningmastery.com/save-load-machine-learning-models-python-scikit-learn/)