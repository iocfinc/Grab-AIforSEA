# Grab-AIforSEA 🚍 🚘 🚖🚲 🛵

This is my entry to Grab's AI for S.E.A. Challenge. For my assignment, I chose to do the Traffic Management problem. The objective of the problem is to help improve the traffic congestion for Southeast Asia through the Grab's booking demand.

Have some issues or suggestions on the project?
Feel free to [contact me](https://iocfinc.github.io/)

---

## Table of Contents

* [Summary](#-summary)<br>
* [Problem Statement](#-problem-statement)<br>
* [Evaluation Guide](#-evaluation-guide)<br>
* [General Instructions](#-general-instructions)<br>
* [Approach](#-approach)<br>
* [Assumptions](#-assumptions)<br>
* [Results](#-results)<br>
    * [LSTM Model](#-lstm-model)<br>
    * [SARIMA Model](#-sarima-model)<br>
    * [Ensemble Model](#-ensemble-random-forest-regression)
* [Spatial Analysis](#-spatial-analysis)<br>
    * [High Demand Areas](#-high-demand-areas)<br>
    * [Unserved Areas](#-unserved-areas)<br>
* [Temporal Analysis](#-temporal-analysis)<br>
    * [Encoded Data](#-encoded-data)
    * [Seasonal Decomposition](#-seasonal-decomposition)<br>
* [Spatiotemporal](#-spatiotemporal)<br>
    * [Animated Heatmap](#-animated-heatmap)
* [Suggestions and Findings](#-possible-suggestions-to-traffic-management)<br>
* [Improvements](#-improvements)<br>
* [References](#-references)<br>
* [Personal Notes](#-update)<br>

---

## Summary:

> Grab has [challenged](https://www.aiforsea.com/) participants to come up with solutions on how AI and their data can be used to solve problems in SEA. For my challenge, I chose to take on [Traffic Mangement](https://www.aiforsea.com/traffic-management). There is a portion on [exploring the data](##-spatial-analysis) provided by Grab to understand demands and traffic patterns in the city. Using an [forecasting model](##-approach) I was able to come up with predictions on the demand on the area at a given time. Together with the insights, I got from data exploration, our forecast could become part of [actionable plans](##-possible-suggestions-to-traffic-management) which we can explore. The possibilities for [improvement](##-improvements) on the model and the data is also provided.
<!-- 
**TODO**

- [x] 🎯 Fill up the Evaluation Notebook for Instructions on how to use the data.
- [ ] 🎯 Check the plot of seasonal decomposition and decompose it into individual components to be added to the EDA.
- [ ] 🎯 Add Additional features on the Regressor. Rolling window means, std, difference. -->

# 🏆 Congratulations to everyone!!! 🏆

<p align='center'><img src="https://github.com/iocfinc/Grab-AIforSEA/blob/master/images/GRAB-TOP50,jpg" alt="AIFORSEA-TOP50-Candidates"><br>Top 50 candidates together with judges and Grab's co-founder</p>

## Problem Statement

Economies in Southeast Asia are turning to AI to solve traffic congestion, which hinders mobility and economic growth. The first step in the push towards alleviating traffic congestion is to understand travel demand and travel patterns within the city.

> Can we accurately forecast travel demand based on historical Grab bookings to predict areas and times with high travel demand?

## Evaluation Guide

The [EvaluationNotebook.ipynb](https://github.com/iocfinc/Grab-AIforSEA/blob/master/EvaluationNotebook.ipynb) has been provided to centralize the evaluation process. The notebook contains the preprocessing functions, the model load and other helper functions that will be neede to process the data and prepare the model prior to predictions. The sections that are required were marked with **(RUN)** to make it easier to track. A [`requirements.txt`](https://github.com/iocfinc/Grab-AIforSEA/blob/master/requirements.txt) has also been provided to get a list of libraries that were installed during the testing and development of the project. It was run in Google Colab but you should be able to set it up with the requirements.txt file. Also, some of the assumptions and pointers on how to go over the notebook has been provided on the notebook itself. If you need to reach out to me for any issues or questions kindly reach out to me, my contact details can be found [here](https://iocfinc.github.io/).

## General Instructions

The EvaluationNotebook is provided for the evaluation of the final model. The EvaluationNotebook.ipynb has been created to allow the evaluator to get the prediction results given the training and/or holdoff data. Instructions on how to load the models and get predictions would be provided in the notebook. Additional notebooks were also provided to show the solutions and thought process on how the models were created. The Forecasting-Notes.ipynb is the _workbook_ where the majority of the model training and exploration were done. It shows the results of the training and testing of the models that were chosen for the final model. The EDA-NOTES.ipynb is also provided for review to provide the data exploration I did for the project.

**Update: Pitch deck is also uploaded for anyone who might want to check it.<br>
Feel free to contact/connect with me: https://www.linkedin.com/in/iraoliverfernandoph/. Would love to hear your inputs and learn from you as well.**

## Approach

For this problem I went with an Auto-encoder - LSTM - SARIMA - Random Forest Ensemble for the forecasting. The Autoencoder is to allow me to frame this problem as a univariate-multistep forecasting problem. This allowed me to use LSTM and SARIMA as my time-series forecasting models. The LSTM is a static model which was trained under a supervised learning approach. The SARIMA acts as a dynamic model where there is no prior training but with grid-search utilized to come up with a good hyperparameter setting. The LSTM + SARIMA forecasts were then combined together via Random Forest Regression. The idea behind it was to minimize the deviation of the forecasts especially when its farther from the current time. The output of the Random Forest model would then be converted back to the individual date/geohash6 element to allow RMSE calculation and evaluation as part of the project evaluation.

I also added a small data exploration and analysis portion to check if there are insights that can be gained from the current data. I was able to get trends and seasonalities information for the provided data which could be quite useful in providing a solution to the problem in the challenge which is traffic management. There is also a provided heatmap to visualize the demand on a spatiotemporal level.

## Assumptions

There were some assumptions made prior to creating the model. First was that I am assuming that the data between day 61 and the time t is going to be provided. The models _can_ walk towards the current time t from day 61 but without the true values, the model would understandably get further from the actual values. Another assumption I made is that even if the data between day 61 and time t is not provided, the last 14-day equivalent data would be provided. This would make the model a one-shot model instead of a walk-forward model, where the evaluator will have to position the model to time t and ask it to forecast times (t+1) ... (t+5) as indicated on the problem.

## Results

Below are some of the results I got from the model training. The individual model predictions are shown below. Do note that these plots looks fairly close to the actual and the reason for that is because I am updating at every time step. The results would be somewhat less fit to the encoded demand if it was updated every 5 steps. The walk forward was also done with constant updates to the true value. Using the predictions of the model to predict the next sequences would act as a negative feedback cycle. The errors in the forecast of the models simply get magnified the further we go with the observations.

## LSTM Model

<p align='center'><img src="https://github.com/iocfinc/Grab-AIforSEA/blob/master/images/AIFORSEA-LSTM_WITH_TRAIN_PLOT.png" alt="AIFORSEA-LSTM_WITH_TRAIN_PLOT"></p>

For the LSTM Model, the idea that I have a lookback window of 25 sequences and a forecast window of 5. The results on the encoded model were quite good. In terms of "fit" it seems to look a lot like the actual values. As expected, really high spikes tend to not get represented well. Also, the 25 sequence length could be restrictive. More exploration can be done on this for future improvements.

## SARIMA Model

<p align='center'><img src="https://github.com/iocfinc/Grab-AIforSEA/blob/master/images/SARIMA-MODEL-5800 Predictions.png" alt="SARIMA-MODEL-5800 Predictions Plot"></p>

The output of the SARIMA model is seen above. Again, it does seem to model the encoded data well. The final paramters used for the SARIMA was a PDQ(0,0,0) and a Seasonal pdq(1,0,0) with the trend set to constant. These parameters were arrived at with the help of the Autocorr plot below, the ACF and PACF and a grid search of possible combinations of PDQ, pdq and trend which was already covered in [Machine Learning Mastery](https://machinelearningmastery.com/how-to-grid-search-sarima-model-hyperparameters-for-time-series-forecasting-in-python/). Using the likely values we can see in the plots below we get a SARIMAX [(0, 0, 0), (1, 0, 0, 0), 'c'] with an RMSE of 0.004634905424127102. Not really bad for a simple SARIMAX model. Also, you would notice on the EvaluationNotebook that there is no loaded model for SARIMAX. SARIMAX continously updates its fit per iteration. You can think of it as a dynamic model where the updates happen at the time of the prediction instead of being trained beforehand.

<p align='center'><img src="https://github.com/iocfinc/Grab-AIforSEA/blob/master/images/AIFORSEA-AUTOCORR_PLOT.png" alt="AIFORSEA-AUTOCORR_PLOT"></p>

<p align='center'><img src="https://github.com/iocfinc/Grab-AIforSEA/blob/master/images/AIFORSEA-PACF_PLOT.png" alt="AIFORSEA-PACF_PLOT"></p>

## Ensemble Random Forest Regression

<p align='center'><img src="https://github.com/iocfinc/Grab-AIforSEA/blob/master/images/AIFORSEA-DECODED_SCORE_PLOT_LONG.png" alt="AIFORSEA-DECODED_SCORE_PLOT_LONG"></p>

For the ensemble model predictions, I do not have the plot of the fit of the actual and predicted values. Instead, I have a plot of the decoded RMS values every prediction step of the ensemble model. It shows that there is still room for improvement for the model. We notice that the errors tend to be magnified more during times where there is activity and die down when there is low demand overall. I am thinking this could be thought of as the effects of residuals. There is still room for improvement for future implementations.

Below are the importance of the individual features as inputs to the Random Forest Regressor. LSTM provided majority of the weights towards the final predictions. We see that we can still improve the Features provided to the Regressor. Maybe we take a look at other possible sources of features. These regressors are simply derived from the LSTM and SARIMA model. It is noticable how small SARIMA contributes to the final predictions and how little weight is given to the POS feature.

<p align='center'>

| Feature | Weight | Description |
| --- | --- | --- |
| SARIMA | 0.05922411386898257 | The SARIMA predictions for the time step |
| LSTM | 0.29922722858556994 | The LSTM predictions for the time step |
| SARIMA_UP | 0.05565608540644605 | SARIMA * 1.05 |
| SARIMA_LOW | 0.0723716966207925 | SARIMA * 0.95 |
| LSTM_UP | 0.2773486113129659 | LSTM * 1.05 |
| LSTM_LOW | 0.23378996199788474 | LSTM * 0.95 |
| POS | 0.0023823022073583993 | Location of the forecast (1 - 5). Scaled to fit between (0,1) |

</p>

## Spatial Analysis 🗺

<p align='center'><img src="https://github.com/iocfinc/Grab-AIforSEA/blob/master/images/AIFORSEA-HEATMAP-HIGHDEMAND.png" alt="Major Areas - Bloom Plot"></p>

The original dataset has one feature that is Spatial in nature, it is the geohash6 encoded tag. There was already a hint provided in the description of how we can make sense of the tag. Resolving the geohash6 locations to individual latitude and longitude combination was done via dictionary lookup to speed up the process. The lookup table was done by making use of the python-geohash library and all the unique geohash tags were resolved to their individual latitude and longitude components. In terms of spatial features, this is the only one I made. It did note on the EDA notebook of a possible improvement of this where in we get the spatial relationships of locations close together and how that affects their respective demands. After Lat and Long were added I was able to make use of hexbins to serve as my heatmap. In terms of studying the demand and its relationship with the 2D space, we see several notable insights about **where** our demands come from. The locations that we get resolves to an area somewhere in the middle of the sea so we cannot overlay it to a specific location. What we can do is derive some hypothesis on how our services are needed in the area. Throughout this exploration, I made several hypotheses which may or may not be useful in finding a solution to the original statement of the problem. Here are some insights that we could use based on the data we have.

### High Demand Areas

<p align='center'><img src="https://github.com/iocfinc/Grab-AIforSEA/blob/master/images/TO_EDIT_HTMAP.png" alt="Major Areas Plot"></p>

We have two major locations with a high concentration of demands. These two areas seem to correspond to a major district where demand would be high like city centers or CBDs **Red Circles in the map below**. I am thinking this is a highly built-up area where there is a high concentration of people confined in a small space. This area tends to exhibit high demand in a small geographic area (around 8 hexbins). In terms of the problem of traffic, having a high demand in a small space would likely contribute to congestion since we are going to send cars to a small geographic location to meet demands. That would probably have detrimental effects on traffic management in the long run.

Aside from the two major areas, we see two areas that also had high demand but more spread out through a wider geographic area **Yellow circles**. These areas continually show demand, not as high as the two major areas but due to the consistency of the spread of demand we can say that it is still a high demand area. In the context of the earlier areas of high concentration as CBDs where there are high-rises and major businesses and places of interests, these two areas tend to be more spread out so I am thinking something of a suburban setting where the development is more horizontal instead of vertical. So think of houses instead of condos for these areas. The demand in these areas is relatively high but due to it being spread around a larger area it should fare better in terms of congestion.

### Unserved Areas

<p align='center'><img src="https://github.com/iocfinc/Grab-AIforSEA/blob/master/images/AIFORSEA-HEATMAP-UNDERSERVED.png" alt="Major Areas Plot"></p>

I have created a snapshot of a heatmap with locations without demand registered colored blue (which makes sense considering this area is in the middle of the ocean, it really is AI for sea😏 jk). In any case, there are certain areas where there is continually no demand. From this, we can get a "lay of the land" even without the use of a reference/overlay. In a way, it does make sense that there are areas without demand or service. Areas where it would be physically impossible to have a demand, for example, the middle of the sea or inside a big park, or possibly there is a river in our map (which would explain the split of concentration of areas of demand). No city is exactly square, or rectangular for this matter, so it should be fine to have a few gaps. Without an actual reference/overlay we can only assume what is causing the absence of demand. But in terms of planning trips and resource positioning, knowing areas where there is no demand is useful to avoid unnecessarily sending resources to known areas of no demand. Directing our drivers towards areas where our services are needed should also be an important insight for us. I do not think Grab Drivers are paid when they are moving around without a passenger or a booking (I might be wrong about it though). Knowing the areas of low demand is also important for us so that our driver partners can also make a living.

## Temporal Analysis 📅

We can now take a look at the temporal components of our data. The original dataset provided had two features that relate to time. One was the `Day` feature which is simply a day indicator for the individual rows. The other one is the `timestamp` which is a string which indicates the time the data was polled. What we are after here is the structure of the demand with respect to time. We can know which hours our demand peaks and when it would drop, which day of the week would have high demand, how does our trend look. All these items could be retrieved through the temporal analysis of data. Below are some of the plots and findings I was able to get from studying the data.

### Encoded Data

<p align='center'><img src="https://github.com/iocfinc/Grab-AIforSEA/blob/master/images/AIFORSEA-ENCODED_DEMAND_PLOT.png" alt="AIFORSEA-ENCODED_DEMAND_PLOT"></p>

The plot above is the encoded data obtained from using the encoder portion of the autoencoder. It looks like a fairly good representation of combined data from the individual geohash6 location. The only issue I had with this current data is that its values are quite large (max value ~7+). It does hurt the LSTM and SARIMAX model scores but other autoencoder structures I have tried have either an inverted plot or a much larger range so I am for the moment stuck with this autoencoder. What I do like about the autoencoder is that it allows me to frame the problem of multivariate (multiple geohash6 codes) into a simpler univariate problem. It also has an added benefit of being a fairly reasonable representation of the actual demand of the combined geohash6 locations since its a higher level representation (it is assumed that all the relevant features have been encapsulated inside the single encoded value). To start with the temporal analysis of the dataset we go with a seasonal decomposition graph.

### Seasonal Decomposition

<p align='center'><img src="https://github.com/iocfinc/Grab-AIforSEA/blob/master/images/AIFORSEA-seaonality_decompose-96.png" alt="AIFORSEA-seaonality_decompose-96 Plot"></p>

In terms of the time component, I did some seasonal decomposition of the encoded data. It is one added benefit of converting the demand data into an encoded high-level representation. This way, I was, for the most part, able to analyze a representation of the demand we have in a single value. Having this information allows us to see and get an idea of how our demand is with respect to time. We can see the demand in terms of daily, weekly, hourly and even monthly timeframe.

First of is the trend and seasonalities as well as the residuals plot. The graph above shows our data on a "daily" frequency (freq=96 = 24 x 4). I think it is daily if I understood the frequency setting correctly 😂. The **observed** data shows the daily values of the demand. It is basically the combination of the three lower plots. The observed values are the sum of the components on that specific time. The components of a specific observation in time is simply the base represented by the trend, the seasonality which represents the hours in this case and the residuals which represent the 15-minute buckets for this example. All the values of the three, trend + seasonality + residual, **AT A GIVEN SPECIFIC TIME** when added would correspond to the observed value for that time.

<p align='center'><img src="https://github.com/iocfinc/Grab-AIforSEA/blob/master/images/AIFORSEA-TREND-96.png" alt="AIFORSEA-TREND-96"></p>

The **trend** graph is going to be the weekly timeframe component. We can see based on the trend of how the demand flows through the week. We see the demand's crest and trough at a fairly regular interval. We see from the trend which days have high _base_ demand. I think it is fair to assume that the low points of the trend are weekends where generally demand would be lower. Prior to the days of low demand are days with increasing demand, if the low points are the weekends then we can say that demand picks up on Mondays and grows until it reaches Friday where base demand is consistently highest throughout the data frame. We can also see dips in some trend days where the pattern is broken. This might correspond to holidays where the effects are on a large scale that it reflects in the trend plot. Although we can only assume without the reference data of where day 1 is, seeing these things in the data is fun enough.

<p align='center'><img src="https://github.com/iocfinc/Grab-AIforSEA/blob/master/images/AIFORSEA-SEASONAL-96.png" alt="AIFORSEA-SEASONAL-96"></p>

The **seasonal** data is the representation of the hourly data. It is highly regular to the point that it is almost sinusoidal in nature. We can see the demand flow per hour of every day. Obviously, demand will never be consistent (it would be nice for Grab if it was) and if we dig a little deeper with the seasonal graph we can see that the demand tends to be low in the late afternoon towards the evening. The demand will then start to pick up and grow during the late evenings and finally crest during the morning until around lunchtime. From lunchtime (noon) towards the afternoon, the demand returns to a downtrend. There is not much left to interpret in the plot of the seasonal component since it is highly uniform. We do have to note that this uniformity allows us to better plans. Since we already know the times at which the demand rises and falls, we can adjust our plans accordingly. Maybe in the context of reducing traffic congestion, we limit the total number of cars on the road especially when demands are down. This way our drivers could also get some rests or eat dinner at home with their family,

<p align='center'><img src="https://github.com/iocfinc/Grab-AIforSEA/blob/master/images/AIFORSEA-RESIDUAL-96.png" alt="AIFORSEA-RESIDUAL-96"></p>

The last component of the decomposition would be the **residuals**. The _noise_ of the model if there ever was one. These are the movements possibly of the minutes throughout the day. We can see some sort of structure on the residuals. What's important to point out is that the residuals can be investigated further to get an idea of which _peaks_ can be explained. For example, the high spike on March 18, 2019, can be explained by missing timestamps on the dataset. The missing timestamps happened to coincide during the peak hours of demand (noon) so the missing data, which would be interpolated or filled with zero, was considered a sudden drop (spike). From peak demand, a sudden data point loss is understandably going to spike the residuals. Being able to check these times on the data can allow us to make new features for future implementation.

<p align='center'><img src="https://github.com/iocfinc/Grab-AIforSEA/blob/master/images/AIFORSEA-seaonality_decompose-672.png" alt="AIFORSEA-seaonality_decompose-672 Plot"></p>

If we want to consider a higher time frame then we can simply change the frequency set in the seasonal decomposition so that we can change the resulting timeframe. In the plot above its the seasonal decomposition of our encoded data. The observed data would be in terms of weekly data (672 points or 4 x 24 x 7). It becomes more long term with the trend being one step higher so it is showing the monthly, the seasonality one step lower showing the daily (the observed in the earlier plot) and the residuals showing 2 steps lower which means it is on the hourly.

<p align='center'><img src="https://github.com/iocfinc/Grab-AIforSEA/blob/master/images/AIFORSEA-PROPHET-SeasonalityPlot.png" alt="AIFORSEA-PROPHET-SeasonalityPlot"></p>

FB Prophet's seasonality plot was also retrieved and is shown above. Although I did not pursue using FB Prophet as a model to the ensemble, I was able to explore it and get some sample predictions on it as well as the plot above. It just confirms what we can observe in the seasonal decomposition plots we had earlier. This plot was taken when I tried to forecast the demand for a single geoencoded location. It had a training set of all the data before day 45 (04-15-2019 00:00:00) and it is predicting all the data beyond that point so until day 61. We see that there is a positively sloped trend so we expect the data to continually grow. We see the structure of the weekly seasonality where the demand starts to dip at one point before picking up again. Notable here is the fact that the dip happens on Wednesday. I am not sure if there is data to support that, my earlier hypothesis was the dips were happening on the weekends. Both my hypothesis and Prophet's layout of the actual day of the week where the dip happens cannot be confirmed since we are using an arbitrary date as a stand-in. The important thing to consider here would be the structure of the seasonalities and trend. Finally, we have the daily timeframe which shows the hourly structure of demand. We confirm that there is a decrease in demand around late afternoon and in the evening. As we go later into the night hours our demand starts to pick up again and will continuously grow until we reach the peak which is around 10:00 AM to Noon. For the hourly structure of data, we can be confident that this structure holds. We do not have to hypothesize the actual time since it has been provided directly in the dataset without any obfuscation. Again, the important thing to consider here is the structure of the demand on the hourly level. Later on, in the animated heatmap, we can somehow confirm that the demand does follow the hourly structure of dipping on the late afternoons and evenings and peaking around the morning until noon.

## Spatiotemporal


<p align='center'>
<img src="https://github.com/iocfinc/Grab-AIforSEA/blob/master/images/heatmap-animation_fixed.gif" alt="heatmap-animation_fixed">

Now we combine both the **when** and the **where** data. In **Temporal** we get an idea of how demand moves with time. With **Spatial** we see how demand is distributed in space. Moving further, with the help of heatmaps and animation, we can get an idea of how demand is distributed in time and in space.

## Animated Heatmap

<p align='center'>
<img src="https://github.com/iocfinc/Grab-AIforSEA/blob/master/images/HEATMAP-DAY7-10_giphy.gif" alt="Heatmap day 7-10 gif">

<b><a href='https://github.com/iocfinc/Grab-AIforSEA/blob/master/images/' alt='HEATMAP ANIMATION VIDEOS'></a></b>
<br>

</p>

Making these animated heatmaps should reinforce what we have been noticing in the spatial and temporal. For example, we will notice that the heatmap _blooms_ during times where we have high demand indicated in the observed data. Translating this into the heatmap allows us to see where those areas that had high demand contributions are located. We can get an idea of which areas are active at a given time. We see possible areas of interest that would show high demand relative to the areas surrounding it.

One example insight we can get is in checking which area/s would have to be served during late hours. We have stated in the temporal analysis section that our demand tends to die down during the late afternoons and start to pick up late in the evening. Knowing where these areas of demand are after a period of low demand will allow us to focus our drivers towards that area on a timely manner, this way they don't stay too long in the area so they reduce their contribution to congestion. Another insight we can also get is where are the active areas when its early morning. We know that our demand will steadily grow from late in the night (11PM+) towards the next day. One possible explanation for this would be the absence of other forms of transport. When we know which areas are active during the _off hours_ we can advise drivers to go towards known areas of demand so that they get rides faster.

<p align='center'><img src="https://github.com/iocfinc/Grab-AIforSEA/blob/master/images/AIFORSEA-HEATMAP-HIGHINTEREST.png" alt="AIFORSEA-HEATMAP-HIGHINTEREST"></p>

I mentioned that there are areas that are out of phase in terms of the demand of the region as a whole. One example would be the location indicated above. It tends to have demand late in the nights and early morning but less during lunch time. It is out of sync from the majority of the locations. I'm guessing this is a major location, possibly an airport based on the times of its demand and its approximate distance from the city center where high demand is concentrated.

Having a spatiotemporal representation allows us to have a model of how our demand behaves at different areas at specific times. Know this allows us to adjust our plans accordingly and have initiatives that could help in decongesting the "city".

**Does this help solve the Problem Statement?**

How does this help in the context of traffic management? Knowing these cycles would be useful in planning our resources, again. Knowing the day of the week that has considerably less demand allows us to schedule how much cars we send out on the roads. We can schedule the rotation of our driver partners so they can spend more time with their family during off-days. Knowing which hour of the day has more demand and knowing when the demand starts to peak and a when it starts to wane allows us to better inform our driver on when to stay out on the road and when they could opt to rest or have breaks. Knowing when the fairly regular things happen would be a big help in planning the scheduling of our drivers. By knowing when it is likely for demand to rise we would be able to time our resources to meet the increase which would help improve our service to customers so that they will not have to wait long to get a booking. Knowing when the demand would wane allows us to reduce the number of cars we put on the street by coordinating with our drivers on when they would be better off staying put and resting. For example in the afternoon or evening, by knowing that demand will be low beforehand we can coordinate with our drivers so that they would not be loitering on the roads which should help in reducing the number of cars on the road and in effect help in the traffic congestion which we are trying to solve.

The one thing we must take care of when dealing with is the residuals data. If you are familiar with the "coconut" reference then these are our coconuts. Yes, it is helpful to study these residuals to see if we can correlate spikes to certain information, for example, a spike which was caused by a transport strike. Or a sudden surge due to an event. We can use the residuals as a reference so that we can set up other sources of information. For example, if we see a sudden surge and notice that there was a trending item on social media about a concert then we can in the future check for these sorts of anomalies so they can at least be flagged and we can adapt. The coconuts are coconuts for a reason. They are rare events which have a considerable impact on our demand. Although I don't think modeling residuals would be worth it, they are really noisy, using them in the context of early warning can be useful. With the case of missing data, it is not related to traffic, but we can possibly create a monitoring system for our infrastructure as well. It may not be traffic related but it will certainly help when we know if there is a failure in our internal infrastructure which could lead to downtimes.

## Possible Suggestions to Traffic Management

Based on what we have explored so far I came up with suggestions on how we can address the traffic congestion problem.

<h2> Incentivise Pooling 🚙✅</h2>

Identifying these areas of high demand and their relative concentration/density is useful in planning our services and approximating the traffic situation. For example, it is plausible to imagine that the areas with higher demand density would be more prone to traffic congestion. We are physically limited to how much cars we can put on an area especially well built up ones so it would be useful to make certain policies on how to serve these high-density areas so that we are not adding to the traffic congestion without affecting the quality of service we provide to our users. One possible product we can push towards areas with higher density would be pooling services which we could incentivize in someway so that our users will more likely choose it that having their own personal ride. Doing so would be one way we can lessen the number of cars headed towards these areas.

<h2>Synergize with public transport 🚍 🚇 🚀</h2>

We could possibly take a look at having better partnerships with public transport and integrating our services with one another. One scenario I had in mind was instead of having our drivers drive towards the areas of major demand we could partner with public transport to bring our clients closer to our drivers. Maybe from the city centers, we can have designated pickup points towards the outlying districts. The user will still book with us and have a car ready to serve them on the designated pickups but they will be using public transport to get to those designated sites. It might be a long shot but if we can coordinate with other service providers like buses and trains then I think its a possibility. This would mean that we are not required to add to the volume of cars in high demand areas which could help ease the traffic congestion. I never thought Grab's mission was to replace public transport, I think it serves more to complement it. With this initiative, we can help other services as well while at the same time doing our part in lessening our impact to traffic all while maintaining the quality of service our users expect.

<h2>Staging Areas :parking:</h2>

Knowing where our demand is and when it would likely increase allows us to look at the possibility of staging our resources. Instead of our drivers loitering around waiting for a booking, it would be possible for us to direct them to a staging area. Grab could partner with parking garages, gasoline stations, neighborhood associations to have designated staging areas on their vicinity. In the staging area, our drivers can rest up and wait for a booking. From the analysis of the spatiotemporal data, we can locate suitable locations where our drivers could stay in anticipation of increased demand. We can pool our resources to staging areas so that they are off the roads so they minimize their effect to congestion.

## Improvements

There are a lot of features that could be added to the ones provided. Some were not implemented due to the obfuscation of the details on the dataset (location and dates). For example, landmarks overlayed to the spatial map would be useful in understanding the nature of the demand in certain areas. The use of holidays as a regressor could also be added after the actual dates have been provided. The timestamp and date values on the dataset were not provided so I did not think it was wise to add special holidays as a regressor since we cannot localize the data and the timings.

On the prediction model itself, I think a lot of improvements can be made. The use of a CNN to get the effect of neighboring geohashes to the demand could be explored. Longer lookback window can also be considered for future implementation. The current lookback window for the models was set to 25 periods (~6.25 hours). The challenge can accept up to 14-days worth of data (1344 points). Relative to the maximum allowable I am only using 1.86%, it might be worth looking at higher windows to see if any improvement can be extracted. The autoencoder can also use some work. The autoencoder implemented on this project is simply relative to what could be possible for the context. A 2D CNN autoencoder that will look at the spatial data (long and lat) arranged demand could be explored. The autoencoder results could also be rescaled to help the downstream models in achieving a better result. We can take a look at adding other models like varying lookback periods and varying hyperparameter setups prior to ensembling to see if that achieves a better score. There are a lot of possible avenues to explore on this model but for this challenge, I'm quite happy with the MVP I had created.

If you have any other suggestions or recommendations feel free to [message me](https://iocfinc.github.io/). In the meantime, this version is submitted to GRAB for evaluation.

### POST-PITCH Improvements:

As mentioned, the model has not considered the spatial relationship (e.g. adjacency) between locations. It would *(at least from what I understand)* make sense to ensure that the adjacency between locations is represented as well during the encoding. There might be rare cases where a location is out of phase with the surrounding areas, see <a href='https://github.com/iocfinc/Grab-AIforSEA/blob/master/images/AIFORSEA-HEATMAP-HIGHINTEREST.png' alt='High Interest image'></a>. In general, though, it might make sense to assume that demands in certain locations are closely related and will not suddenly drop so it makes sense to allow the model to give focus to the areas of demand. A CNN might do the trick here, the psuedocode is already in one of the notebooks if I remember correctly. I have not found the time to implement the forecasting that way due to time constraints.

>>> **NOTE:**
The reasoning behind why I used autoencoders in the first place was due to the problem I was having with resources during computation and the lack of data on some locations. My thinking was that it could be possible to do individualized model per location but I was having problems figuring out how that would scale. To give you an idea, this entire project was trained using Google Colab which is quite limited in terms of compute cores 😅 (it is free though so no complaints on my end). I did not think that it would be a good idea to train individualized models per location because if I think about it I do not see it scaling well. Another thing that was a bit problematic when doing individualized models per location was that there are locations in the data where there was only one demand recorded. You might think that it could be dropped to reduce the number of models to train. The counter-point to that was that what if it was a new house on the edge of town and during the time the data was captured there was only one recorded booking from that location. It would not make sense to drop these locations. It would also be hard to extrapolate the entire 5856 points from a single value. Using an autoencoder would solve these problems for me. For one, the encoded data would be smaller than running the entire set while still retaining some resemblance of the original data. It also *learns* how to deal with the points in the map where there are missing data.
 <br><br>Admittedly, the use of an ANN for a *heatmap* is not really that great to begin with🤦🏼‍♂️. I had to resort to a *mask* to ensure that the locations with final predictions are matching the locations where the demand forecast is needed. That is also one downside of not using individualized models, there are some predictions on locations that should not have forecasts to begin with. A quick look at the predictions also showed that some locations had predictions that were way off although using MSE does show that for the most part the errors were small. Still, it could use refinement.<br><br>
 *These are based on how I understand the situation so there might be a better way to do it so feel free to email me and help me improve (🧙‍♂️ please mentor me I am still a 🥔 !!!)*.

Another possible improvement would be to use a generative model to train the model better. In this project, there were only 5856 data points. This is a *relatively* small size considering that we are using LSTM/Deep learning. To counter this, I am thinking that it might make sense to use a generative model to have more data and improve the general model.

The ensembling model also needs work. In contradiction to what the M3 competition results have proven, the LSTM module in this project had more weight to it in the ensembling than the SARIMA module. This has not yet been exhaustively investigated during the challenge as to why there was a contradiction with the weights. From M4 results, we know that ensembling methods work well although I have not read up on any mention of ML approaches performing better than statistical approaches. My idea is to create individualized ensembling models per time step. Instead of one single model for any time step we create one unique model for (t+1,..., t+5) for sanity checking.

## References

[Hyperparameter Tuning the Random Forest in Python](https://towardsdatascience.com/hyperparameter-tuning-the-random-forest-in-python-using-scikit-learn-28d2aa77dd74)

[Save and Load Machine Learning Models in Python with scikit-learn](https://machinelearningmastery.com/save-load-machine-learning-models-python-scikit-learn/)

[A study on Regression applied to the Ames dataset](https://www.kaggle.com/juliencs/a-study-on-regression-applied-to-the-ames-dataset?scriptVersionId=379783)

[Why Forecasts Fail. What to Do Instead](https://sloanreview.mit.edu/article/why-forecasts-fail-what-to-do-instead/)

[SARIMAX on mean visits](https://www.kaggle.com/aless80/sarimax-on-mean-visits)

[How to Grid Search SARIMA Hyperparameters for Time Series Forecasting](https://machinelearningmastery.com/how-to-grid-search-sarima-model-hyperparameters-for-time-series-forecasting-in-python/)

## Personal Notes on the competition:

The first iteration of the challenge has been completed. A quick rundown of the stats for the challenge showed that there were 25,000 individuals invited to join, 1,200 were able to submit solutions and eventually the top candidates were selected. The Pitch Day was held at Grab's Singapore Headquarters and all the top 50 candidates were flown in. I was fortunate enough to have this submission get selected for the top 10 entries. Personally, it was a great achievement for me considering that it was my first public competition.

During the pitch day, I was able to chat with a few of the participants and the backgrounds were diverse. We had someone working already as a data scientist, some data analysts, I was able to chat with someone who was already doing his doctorate degree, there was a VP for Data, there were some graduating students as well. It is not just the experiences and backgrounds that were diverse, The was a lot of representation from the different nationalities as well, mostly in SEA region. Some were based in Singapore, I had a chat with someone from Thailand, another one from Indonesia, I was seated next to someone from Malaysia, there was another Filipino candidate as well. What I think was great during that day was the amount of talent that they were able to find from the different countries in SEA and worldwide. The commonality is that we are all interested in using AI/ML to help solve some of the pressing problems in SEA. It is always great to get together with others on the same goal, it is like being in the Slack channel in Udacity only in real life. Together with the Top 50 were some of Grab's data scientist, some managers, engineers. We were able to talk to the employees of Grab as well. We talked about their work and what it was like working for Grab and how they ended up working for Grab. The representatives from the sponsors were also there and even the co-founder of Grab was present.

The top 10 participants were given the chance to pitch our submissions and provide the insights and solutions we had come up with for our problem statements. This was personally one of the hardest parts of the challenge. There were some interesting work presented, some were cutting edge actually. We had to distill the work we have done for one month into a four-minute presentation. Creating a coherent pitch from the framing of the problem up until the solutions and eventually the findings and implementations and gutting it out to fit 4 minutes was more challenging than the actual challenge itself. Pitching/public speaking and introvert are two words that rarely mix well 😂.

After the pitch was some presentation from the sponsors. Their presentation was centered on how AI solutions that we were making were creating an impact on society. They also highlighted how they are working together with the industry to enable the integration and adoption of the solutions. The co-founder of Grab, Hooi Ling Tan, was also present and gave a small talk on Grab and its initiative and how the competition was in line with what Grab is trying to achieve for the SEA region. We even had the chance for a quick meet-and-greet/Roundtable with Ms. Tan after the ceremony on some items we had for Grab.

On a personal note, this was a great experience for me. I decided to pivot towards Data Science/AI role in June 2018 as an objective for the year during my birthday. I started doing online courses in Udacity focused on Deep Learning in July 2018. I had the chance to take part in a scholarship grant by Facebook for a nanodegree on Deep Learning using PyTorch. For almost a full year I was learning in my spare time. The original goal was actually to join Kaggle challenges to test out my learnings and get an idea on which parts I have to improve on, sort of a validation set for my journey. Hopefully, I get to work with Grab and have some positive impact towards SEA. 

A huge thank you to Grab for the opportunity. A big thank you to [Mr. Andrey Lukyanenko](https://www.linkedin.com/in/andlukyane/) who was always answering my questions about public competitions and answering all the questions I had during the challenge. To [Padang & Co.](http://padang.co/) (Sir Diva) for running the challenge and the help during the pitch clinic and a huge congratulations to everyone.