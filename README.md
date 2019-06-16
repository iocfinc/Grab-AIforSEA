# Grab-AIforSEA 🚍 🚘 🚖🚲 🛵

This is my entry to Grab's AI for S.E.A. Challenge. For my assignment, I chose to do the Traffic Management problem. The objective of the problem is to help improve the traffic congestion for Southeast Asia through the Grab's booking demand.

---

<h2>Table of Contents</h2>
<h5>

[Problem Statement](##-problem-statement)<br>
[Approach](##-approach)<br>
[Assumptions](##-assumptions)<br>
[General Instructions](##-general-instructions)<br>
[Results](##-results)<br>
[Spatial Analysis](##-spatial-analysis)<br>

- [High Demand Areas](###-high-demand-areas)<br>
- [Unserved Areas](###-unserved-areas)<br>

[Temporal Analysis](##-temporal-analysis)<br>

- [Encoded Data](###-encoded-data)
- [Seasonal Decomposition](###-seasonal-decomposition)<br>

[Spatiotemporal](##-spatiotemporal)<br>
[Suggestions and Findings](##-possible-suggestions-to-traffic-management)<br>
[Improvements](##-improvements)<br>
[References](##-references)<br>

</h5>

---

Summary:

> Grab has [challenged](https://www.aiforsea.com/) participants to come up with solutions on how AI and their data can be used to solve problems in SEA. For my challenge I chose to take on [Traffic Mangement](https://www.aiforsea.com/traffic-management). There is a portion on [exploring the data](##-spatial-analysis) provided by Grab to understand demands and traffic patterns in the city. Using an [forecasting model](##-approach) I was able to come up with predictions on the demand on the area at a given time. Together with the insights I got from data exploration, our forecast could become part of [actionable plans](##-possible-suggestions-to-traffic-management) which we can explore. The possibilities for [improvement](##-improvements) on the model and the data is also provided.

**TODO**

- [ ] 🎯 Fill up the Evaluation Notebook for Instructions on how to use the data.
- [ ] 🎯 Check the plot of seasonal decomposition and decompose it into individual components to be added to the EDA.
- [ ] 🎯 Add Additional features on the Regressor. Rolling window mean, std, difference.

## Problem Statement

Economies in Southeast Asia are turning to AI to solve traffic congestion, which hinders mobility and economic growth. The first step in the push towards alleviating traffic congestion is to understand travel demand and travel patterns within the city.

> Can we accurately forecast travel demand based on historical Grab bookings to predict areas and times with high travel demand?

## Approach

For this problem I went with an Auto-encoder - LSTM - SARIMA - Random Forest Ensemble for the forecasting. The Autoencoder is to allow me to frame this problem as a univariate-multistep forecasting problem. This allowed me to use LSTM and SARIMA as my time-series forecasting models. The LSTM is a static model which was trained under a supervised learning approach. The SARIMA acts as a dynamic model where there is no prior training but with grid-search utilized to come up with a good hyperparameter setting. The LSTM + SARIMA forecasts were then combined together via Random Forest Regression. The idea behind it was to minimize the deviation of the forecasts especially when its farther from the current time. The output of the Random Forest model would then be converted back to the individual date/geohash6 element to allow RMSE calculation and evaluation as part of the project evaluation.

I also added a small data exploration and analysis portion to check if there are insights that can be gained from the current data. I was able to get trends and seasonalities information for the provided data which could be quite useful in providing a solution to the problem in the challenge which is traffic management. There is also a provided heatmap to visualize the demand on a spatiotemporal level.

## Assumptions

There were some assumptions made prior to creating the model. First was that I am assuming that the data between day 61 and the time t is going to be provided. The models _can_ walk towards the current time t from day 61 but without the true values, the model would understandably get further from the actual values. Another assumption I made is that even if the data between day 61 and time t is not provided, the last 14-day equivalent data would be provided. This would make the model a one-shot model instead of a walk-forward model, where the evaluator will have to position the model to time t and ask it to forecast times (t+1) ... (t+5) as indicated on the problem.

## General Instructions

The EvaluationNotebook is provided for the evaluation of the final model. The EvaluationNotebook.ipynb has been created to allow the evaluator to get the prediction results given the training and/or holdoff data. Instructions on how to load the models and get predictions would be provided in the notebook. Additional notebooks were also provided to show the solutions and thought process on how the models were created. The Forecasting-Notes.ipynb is the _workbook_ where majority of the model training and exploration were done. It shows the results of the training and testing of the models that were chosen for the final model. The EDA-NOTES.ipynb is also provided for review to provide the data exploration I did for the project. While the model challenge is about a prediction model, I think the insights we get from the training data alone is enough for us to start talking about possible ways where in we can answer the problem on Traffic Congestion.

## Results



## Spatial Analysis 🗺

<p align='center'><img src="https://github.com/iocfinc/Grab-AIforSEA/blob/master/images/AIFORSEA-HEATMAP-HIGHDEMAND.png" alt="Major Areas - Bloom Plot"></p>

The original dataset one feature that is Spatial, it is the geohash6 encoded tag. There was already a hint provided in the description on how we can make sense of the tag. Resolving the geohash6 locations to individual latitude and longitude combination were done via dictionary lookup to speed up the process. The lookup table was done by making use of the python-geohash library and all the unique geohash tags were resolved to their individual latitude and longitude components. In terms of spatial features this is the only one I made. After Lat and Long were added I was able to make use of hexbins to serve as my heatmap. In terms of studying the demand and its relationship with the 2D space, we see several notable insights about **where** our demands come from. The locations that we get resolves to a spot somewhere in the middle of the sea so we cannot overlay it to a specific location. What we can do is derive some hypothesis on how our services are needed in the area. Throughout this exploration I made several hypothesis which may or may not be useful in finding a solution to the original statement of the problem. Here are some insights that we could use based on the data we have.

### High Demand Areas

<p align='center'><img src="https://github.com/iocfinc/Grab-AIforSEA/blob/master/images/TO_EDIT_HTMAP.png" alt="Major Areas Plot"></p>

We have two major locations with a high concentration of demands. These two areas seem to correspond to a major district where demand would be high like city centers or CBDs **Red Circles in the map below**. I am thinking this is a highly built-up area where there is a high concentration of people confined in a small space. These area tends to exhibit high demand in a small geographic area (around 8 hexbins). In terms of problem of traffic, having a high demand on a small space would likely contribute to congestion since we are going to send cars to a small geographic location to meet demands. That would probably have detrimental effects on traffic management in the long run.

Aside from the two major areas, we see two areas that also had high demand but more spread out through a wider geographic area **Yellow circles**. These areas continually show demand, not as high as the two major areas but due to the consistency of the spread of demand we can say that it is still a high demand area. In the context of the earlier areas of high concentration as CBDs where there are high-rises and major businesses and places of interests, these two areas tend to be more spread out so I am thinking something of a suburban setting where the development is more horizontal instead of vertical. So think of houses instead of condos for these areas. The demand on these areas are relatively high but due to it being spread around a larger area it should fare better in terms of congestion.

### Unserved Areas

<p align='center'><img src="https://github.com/iocfinc/Grab-AIforSEA/blob/master/images/AIFORSEA-HEATMAP-UNDERSERVED.png" alt="Major Areas Plot"></p>

I have created a snapshot of a heatmap with locations without demand registered colored blue (which makes sense considering this area is in the middle of the ocean, it really is AI for sea😏 jk). In any case, there are certain areas where there is continually no demand. From this, we can get a "lay of the land" even without the use of a reference/overlay. In a way, it does make sense that there are areas without demand or service. Areas where it would be physically impossible to have a demand, for example, the middle of the sea or inside a big park, or possibly there is a river in our map (which would explain the split of concentration of areas of demand). No city is exactly square, or rectangular for this matter, so it should be fine to have a few gaps. Without an actual reference/overlay we can only assume what is causing the absence of demand. But in terms of planning trips and resource positioning, knowing areas where there is no demand is useful to avoid unnecessarily sending resources to known areas of no demand. Directing our drivers towards areas where our services are needed should also be an important insight for us. I do not think Grab Drivers are paid when they are moving around without a passenger or a booking (I might be wrong about it though). Knowing the areas of low demand is also important for us so that our driver partners can also make a living.

## Temporal Analysis 📅

We can now take a look at the temporal components of our data. The original dataset provided had two features that relate to time. One was the `Day` feature which is simply a day indicator for the individual rows. The other one is the `timestamp` which is a string which indicates the time the data was polled. What we are after here is the structure of the demand with respect to time. We can know which hours our demand peaks, when it would drop, which day of the week would have high demand, how does our trend look. All these items could be retrieved through the temporal analysis of data. Below are some of the plots and findings I was able to get from studying the data.

### Encoded Data

<p align='center'><img src="https://github.com/iocfinc/Grab-AIforSEA/blob/master/images/AIFORSEA-ENCODED_DEMAND_PLOT.png" alt="AIFORSEA-ENCODED_DEMAND_PLOT"></p>

The plot above is the encoded data obtained from using the encoder portion of the autoenoder. It looks like a fairly good representation of combined data from the individual geohash6 location. The only issue I had with this current data is that its values are quite large (max value ~7+). It does hurt the LSTM and SARIMAX model scores but other autoencoder structures I have tried have either an inverted plot or a much larger range so I am for the moment stuck with this autoencoder. What I do like about the autoencoder is that it allows me to frame the problem of multivariate (multiple geohash6 codes) into a simpler univariate problem. It also has an added benefit of being a fairly reasonable representation of the actual demand of the combined geohash6 locations since its a higher level representation (it is assumed that all the relevant features have been encapsulated inside the single encoded value). To start with the temporal analysis of the dataset we go with a seasonal decomposition graph.

### Seasonal Decomposition

<p align='center'><img src="https://github.com/iocfinc/Grab-AIforSEA/blob/master/images/AIFORSEA-seaonality_decompose-96.png" alt="AIFORSEA-seaonality_decompose-96 Plot"></p>

In terms of the time component, I did some seasonal decomposition of the encoded data. It is one added benefit of converting the demand data into an encoded high-level representation. This way, I was, for the most part, able to analyze a representation of the demand we have in a single value. Having this information allows us to see and get an idea of how our demand is with respect to time. We can see the demand in terms of daily, weekly, hourly and even monthly timeframe.

First of is the trend and seasonalities as well as the residuals plot. The graph above shows our data on a "daily" frequency (freq=96 = 24 x 4). I think it is daily if I understood the frequency setting correctly 😂 The **observed** data shows the daily values of the demand. It is basically the combination of the three lower plots. The observed values are the sum of the components on that specific time. The components of a specific observation in time is simply the base represented by the trend, the seasonality which represents the hours in this case and the residuals which represents the 15-minute buckets for this example. All the values of the three, trend + seasonality + residual, **AT A GIVEN SPECIFIC TIME** when added would correspond to the observed value for that time.

🛰 If your familiar with AM in communications I think that applies to this data. If your an Electronics engineer you might notice that it looks like an AM waveform.

The **trend** graph is going to be the weekly timeframe component. We can see based on the trend of how the demand flows through the week. We see the demand's crest and through at a fairly regular interval. We see from the trend which days have high _base_ demand. I think it is fair to assume that the low points of the trend are weekends where generally demand would be lower. Prior to the days of low demand are days with increasing demand, if the low points are the weekends then we can say that demand picks up on Mondays and grows until it reaches Friday where base demand is consistetly highest throughout the dataframe. We can also see dips in some trend days where the pattern is broken. This might correspond to holidays where the effects are on a large scale that it reflects in the trend plot. Although we can only assume without the reference data of where day 1 is, seeing these things in the data is fun enough.

The **seasonal** data is the representation of the hourly data. It is highly regular to the point that it is almost sinusoidal in nature. We can see the demand flow per hour of every day. Obviously, demand will never be consistent (it would be nice for Grab if it was) and if we dig a little deeper with the seasonal graph we can see that the demand tends to be low in the late afternoon towards the evening. The demand will then start to pick up and grow during the late evenings and finally crest during the morning until around lunchtime. From lunchtime (noon) towards the afternoon, the demand returns to a downtrend. There is not much left to interpret in the plot of the seasonal component since it is highly uniform. We do have to note that this uniformity allows us to better plans. Since we already know the times at which the demand rises and falls, we can adjust our plans accordingly. Maybe in the context of reducing traffic congestion we limit the total number of cars on the road especially when demmands are down. This way our drivers could also get some rests or eat dinner at home with their family,

The last component of the decomposition would be the **residuals**. The _noise_ of the model if there ever was one. These are the movements possibly of the minutes throughout the day. We can see some sort of structure on the residuals. What's important to point out is that the residuals can be investigated further to get an idea of which _peaks_ can be explained. For example, the high spike on March 18, 2019, can be explained by missing timestamps on the dataset. The missing timestamps happened to coincide during the peak hours of demand (noon) so the missing data, which would be interpolated or filled with zero, was considered a sudden drop (spike). From peak demand, a sudden data point loss is understandably going to spike the residuals. Being able to check these times on the data can allow us to make new features for future implementation.

<p align='center'><img src="https://github.com/iocfinc/Grab-AIforSEA/blob/master/images/AIFORSEA-seaonality_decompose-672.png" alt="AIFORSEA-seaonality_decompose-672 Plot"></p>

If we want to consider a higher time frame then we can simply change the frequency set in the seasonal decomposition so that we can change the resulting timeframe. In the plot above its the seasonal decomposition of our encoded data. The observed would be in terms of weekly data (672 points or 4 x 24 x 7). It becomes more long tern with the trend being one step higher so it is showing the monthly, the seasonality one step lower showing the daily (the observed in the earlier plot) and the residuals showing 2 steps lower which means its on the hourly.

<p align='center'><img src="https://github.com/iocfinc/Grab-AIforSEA/blob/master/images/AIFORSEA-PROPHET-SeasonalityPlot.png" alt="AIFORSEA-PROPHET-SeasonalityPlot"></p>

FB Prophet's seasonality plot was also retrieved and is shown above. Although I did not pursue using FB Prophet as a model to ensemble, I was able to explore it and get some sample predictions on it as well as the plot above. It just confirms what we can observe in the seasonal decomposition plots we had earlier. This plot was taken when I tried to forecast the demand of a single geoencoded location. It had a training set of all the data before day 45 (04-15-2019 00:00:00) and it is predicting all the data beyond that point so until day 61. We see that there is positively sloped trend so we expect the data to continually grow. We see the structure of the weekly seasonality where the demand starts to dip at one point before picking up again. Notable here is the fact that the dip happens on Wednesday. I am not sure if there is data to support that, my earlier hypothesis was the dips were happening on the weekends. Both my hypothesis and Prophet's layout of the actual day of week where the dip happens cannot be confirmed since we are using an arbitrary date as a stand-in. The important thing to consider here would be the structure of the seasonalities and trend. Finally, we have the daily timeframe which shows the hourly structure of demand. We confirm that there is decrease in demand around late afternoon and in the evening. As we go later into the night hours our demand starts to pick up again and will contiously grow until we reach the peak which is around 10:00 AM to Noon. For the hourly structure of data we can be confident that this structure holds. We do not have to hypothesize the actual time since it has been provided directly in the dataset without any obfuscation. Again, the important thing to consider here is the structure of the demand on the hourly level. Later on in the animated heatmap we can somehow confirm that the demand does follow the hourly structure of dipping on the late afternoons and evenings and peaking around the morning until noon.

## Spatiotemporal

Now we combine both the **when** and the **where** data. In **Temporal** we get an idea of how demand moves with time. With **Spatial** we see how demand is distributed in space. Moving further, with the help of heatmaps and animation, we can get an idea of how demand is distributed in time and in space.

<p align='center'>
<img src="https://github.com/iocfinc/Grab-AIforSEA/blob/master/images/HEATMAP-DAY7-10_giphy.gif" alt="Heatmap day 7-10 gif">

<b><a href='https://github.com/iocfinc/Grab-AIforSEA/blob/master/images/' alt='HEATMAP ANIMATION VIDEOS'></a></b>
<br>

</p>

Making these animated heatmaps should reinforce what we have been noticing in the spatial and temporal. For example, we will notice that the heatmap _blooms_ during times where we have high demand indicated in the observed data. Translating this into the heatmap allows us to see where those areas that had high demand contributions are located. We can get an idea of which areas are active at a given time. We see possible areas of interest that would show high demand relative to the areas surrounding it.

One example insight we can get is in checking which area/s would have to be served during late hours. We have stated in the temporal analysis section that our demand tends to die down during the late afternoons and start to pick up late in the evening. Knowing where these areas of demand are after a period of low demand will allow us to focus our drivers towards that area on a timely manner, this way they don't stay too long in the area so they reduce their contribution to congestion. Another insight we can also get is where are the active areas when its early morning. We know that our demand will steadily grow from late in the night (11PM+) towards the next day. One possible explanation for this would be the absence of other forms of transport. When we know which areas are active during the _off hours_ we can advise drivers to go towards known areas of demand so that they get rides faster.

I mentioned that there are areas that are out of phase in terms of the demand of the region as a whole. One example would be this location. It tends to have demand late in the nights and early morning but less during lunch time. It is out of sync from the majority of the locations. I'm guessing this is a major location, possibly an airport based on the times of its demand and its approximate distance from the city center where high demand is concentrated.

Having a spatiotemporal representation allows us to have a model of how our demand behaves at different areas at specific times. Know this allows us to adjust our plans accordingly and have initiatives that could help in decongesting the "city".

**Does this help solve the Problem Statement?**

How does this help in the context of traffic management? Knowing these cycles would be useful in planning our resources, again. Knowing the day of the week that has considerably less demand allows us to schedule how much cars we send out on the roads. We can schedule the rotation of our driver partners so they can spend more time with their family during off-days. Knowing which hour of the day has more demand and knowing when the demand starts to peak and a when it starts to wane allows us to better inform our driver on when to stay out on the road and when they could opt to rest or have breaks. Knowing when the fairly regular things happen would be a big help in planning the scheduling of our drivers. By knowing when it is likely for demand to rise we would be able to time our resources to meet the increase which would help improve our service to customers so that they will not have to wait long to get a booking. Knowing when the demand would wane allows us to reduce the number of cars we put on the street by coordinating with our drivers on when they would be better off staying put and resting. For example in the afternoon or evening, by knowing that demand will be low beforehand we can coordinate with our drivers so that they would not be loitering on the roads which should help in reducing the number of cars on the road and in effect help in the traffic congestion which we are trying to solve.

The one thing we must take care of when dealing with is the residuals data. If you are familiar with the "coconut" reference then these are our coconuts. Yes, it is helpful to study these residuals to see if we can correlate spikes to certain information, for example, a spike which was caused by a transport strike. Or a sudden surge due to an event. We can use the residuals as a reference so that we can set up other sources of information. For example, if we see a sudden surge and notice that there was a trending item on social media about a concert then we can in the future check for these sorts of anomalies so they can at least be flagged and we can adapt. The coconuts are coconuts for a reason. They are rare events which have a considerable impact on our demand. Although I don't think modeling residuals would be worth it, they are really noisy, using them in the context of early warning can be useful. With the case of missing data, it is not related to traffic, but we can possibly create a monitoring system for our infrastructure as well. It may not be traffic related but it will certainly help when we know if there is a failure in our internal infrastructure which could lead to downtimes.


## Possible Suggestions to Traffic Management

Based on what we have explored so far I came up with suggestions on how we can address the traffic congestion problem.

<h2> Incentivise Pooling 🚙✅</h2>

Identifying these areas of high demand and their relative concentration/density is useful in planning our services and approximating the traffic situation. For example, it is plausible to imagine that the areas with higher demand density would be more prone to traffic congestion. We are physically limited to how much cars we can put on an area especially well built up ones so it would be useful to make certain policies on how to serve these high-density areas so that we are not adding to the traffic congestion without affecting the quality of service we provide to our users. One possible product we can push towards areas with higher density would be pooling services which we could incentivice in someway so that our users will more likely choose it that having their own personal ride. Doing so would be one way we can lessen the number of cars headed towards these areas.

<h2>Synergize with public transport 🚍 🚇 🚀</h2>

We could possibly take a look at having better partnerships with public transport and integrating our services with one another. One scenario I had in mind was instead of having our drivers drive towards the areas of major demand we could partner with public transport to bring our clients closer to our drivers. Maybe from the city centers we can have designated pickup points towards the outlying districts. The user will still book with us and have a car ready to serve them on the designated pickups but they will be using public transport to get to those designated sites. It might be a long shot but if we can coordinate with other service providers like buses and trains then I think its a possibility. This would mean that we are not required to add to the volume of cars in high demand areas which could help ease the traffic congestion. I never thought Grab's mission was to replace public transport, I think it serves more to complement it. With this initiative we can help other services as well while at the same time doing our part in lessening our impact to traffic all while maintaining the quality of service our users expect.

<h2>Staging Areas :parking:</h2>

Knowing where our demand is and when it would likely increase allows us to look at the possibility of staging our resources. Instead of our drivers loitering around waiting for a booking, it would be possible for us to direct them to a staging area. Grab could partner with parking garages, gasoline stations, neighborhood associations to have designated staging areas on their vicinity. In the staging area, our drivers can rest up and wait for a booking. From the analysis of the spatiotemporal data, we can locate suitable locations where our drivers could stay in anticipation of an increased demand. We can pool our resources to staging areas so that they are off the roads so they minimize their effect to congestion.

## Improvements

There are a lot of features that could be added to the ones provided. Some were not implemented due to the obfuscation of the details on the dataset (location and dates). For example, landmarks overlayed to the spatial map would be useful in understanding the nature of the demand in certain areas. The use of holidays as a regressor could also be added after the actual dates have been provided. The timestamp and date values on the dataset were not provided so I did not think it was wise to add special holidays as a regressor since we cannot localize the data and the timings.

On the prediction model itself, I think a lot of improvements can be made. The use of a CNN to get the effect of neighboring geohashes to the demand could be explored. Longer lookback window can also be considered for future implementation. The current lookback window for the models was set to 25 periods (~6.25 hours). The challenge can accept up to 14-days worth of data (1344 points). Relative to the maximum allowable I am only using 1.86%, it might be worth looking at higher windows to see if any improvement can be extracted. The autoencoder can also use some work. The autoencoder implemented on this project is simply relative to what could be possible for the context. A 2D CNN autoencoder that will look at the spatial data (long and lat) arranged demand could be explored. The autoencoder results could also be rescaled to help the downstream models in achieving a better result. We can take a look at adding other models like varying lookback periods and varying hyperparameter setups prior to ensembling to see if that achieves a better score. There are a lot of possible avenues to explore on this model but for this challenge, I'm quite happy with the MVP I had created.

## References

[Hyperparameter Tuning the Random Forest in Python](https://towardsdatascience.com/hyperparameter-tuning-the-random-forest-in-python-using-scikit-learn-28d2aa77dd74)

[Save and Load Machine Learning Models in Python with scikit-learn](https://machinelearningmastery.com/save-load-machine-learning-models-python-scikit-learn/)

[A study on Regression applied to the Ames dataset](https://www.kaggle.com/juliencs/a-study-on-regression-applied-to-the-ames-dataset?scriptVersionId=379783)