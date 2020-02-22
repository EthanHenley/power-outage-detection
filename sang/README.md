# Project 5 : Using Twitter to Identify Counties with Power Outages
by Sang Cheon, Ethan Henley, Jason Morman
prepared for New Light Technologies

### Table of Contents:
- [Problem Statement](#Problem-Statement)
- [Executive Summary](#Executive-Summary)
    [TwitterScraper](#TwitterScraper)
    [Word2vec](#Word2vec)
    [Decision Tree Model](#Decision-Tree-Model)
    [Geopandas Visualizations](#Geopandas-Visualizations)
- [Conclusion](#Conclusion)
- [Recommendation](#Recommendation)
- [Data Dictionary](#Data-Dictionary)
- [Datasets](#Datasets)
---
### Problem Statement

An emergency response organization or public utility company will, from time to time, have to deal with power outages on varying scales, and may want to directly scan Twitter to see if people in a certain locality are tweeting about outages that need to be resolved. We have developed a proof of concept for this task, focused on the 39 counties of the state of Washington: a two-part model that, given a tweet, can with ~89% accuracy predict if the tweet represents a legitimate power outage. A user could run this model on a live feed of tweets to determine which represent real power outages, then go attend to those outages.


### Executive Summary

In order for any analysis, we first needed to determine what data we will be using to determine the *cosine similarity* of the average vector of words in a tweet against pro-outage versus anti-outage word lists.  Although we had originally intended on covering all major power outage incidents in the US, we observed that our historical data from eia.gov only goes down to a county level by state on a granular scale in recording major outages.  As a result, we wanted to focus our *sentiment analysis* on a county level, so we picked *Kings County, Washington* as a focal point and used twitterscraper to scrape all tweets of users during the period of an actual power outage in the area up to 1 day after the event had ended.  We then decided we will be using a decision tree model using sentiment analysis score to determine if we can classify whether a specific tweet is indeed related to a power outage during a blackout period or not.  

#### TwitterScraper

**One of the issues with using a twitterscraper instead of a twitterapi was that we are not able to obtain geolocation of tweets which brings up limitations on accurately determining locations of live tweets.**

**Twitter Api only goes back 7 days in history and cuts tweets off at 140 characters (current maximum (2020) : 280 char)**

We narrowed our search query to scan for all tweets within *x* distance in all counties in the state of Washington within a 1 day range of different power outage restoration date & time period using key words "blackout OR power OR outage" and blank query.  During our query, we specifically excluded results that are associated with "Washington DC", "D.C", "District of Columbia" to remove any unrelated query results that may affect our train data.  As a result, we were able to collect 230 tweets that fit the keywords criteria and "2000" tweets for the blank query criteria (tweets not believed to be outage-related).


#### Word2Vec

After the tweets corpus was formed, we were able to input our result tweet corpus into word2vec.  Word2vec returns the cosine similarity of words based on the *wikipedia? corpus?*.  Similar documents that are far apart due to document size can still be aligned closely together so that we can feed the results on a decision tree model to provide us an insight.  

#### Decision Tree Model

#### Geopandas Visualizations


### Conclusion

Our results from the decision tree stump indicated that our model's cosine similarity cutoff for our data was about 0.277.  With this cutoff, we are able to predict whether a tweet is outage related with 89% accuracy.  However, due to the sheer number of tweets that are queried that fits our model, we are able to rationalize that people don't particularly tweet much often about power outages.  

### Recommendation

With proper funding and resources, this model can be better utilized by any agency looking to determine if live-read tweets were related to power outages.  This could potentially improve the model's performance not only particularly to the state of Washington but to all other states or internationally as long as the region is an active tweet user-base region.

"weak learner" do we add boosting to improve it?

### Data Dictionary

|Feature|Type|Dataset|Description|
|---|---|---|---|
|Area Affected|object|washington_outages|The name of the county in WA with a power-outage | 
|index|int64|washington_outages|A number to reference a specific outage occurence (identical index number for same outage even if occurred simultaneously in different counties)| 
|Year|int64|washington_outages|The year of the power outage occurrence| 
|Month|int64|washington_outages|The month of the power outage occurrence| 
|Event Date and Time|object|washington_outages|The date and time of the power outage occurence (yyyy-mm-dd)| 
|Restoration Date and Time|object|washington_outages|The date and time of when the power is restored (yyyy-mm-dd)| 
|Duration|object|washington_outages|The duration of the power outage occurence | 
|Utility/Power Pool|object|washington_outages|The name of the utility provider for the service area of power outage| 
|NERC Region|object|washington_outages|The region name of the service area of the power outage specified by The North American Electric Reliability Corporation|
|Type of Disturbances|object|washington_outages|An explanation of what is causing the power outage| 
|Loss (megawatts)|object|washington_outages|The amount of electric generating capacity loss during the power outage| 
|Number of Customers Affected|object|washington_outages|The estimated number of customers affected by the power outage| 
|event_row|int64|washington_outage_tweets|**filtered value**| 
|timestamp|object|washington_outage_tweets|The date and time of when the tweet text went live| 
|text|object|washington_outage_tweets|The content of the tweet | 
|outage|int64|washington_outage_tweets|**filtered value**| 

### Datasets

- [Washington Outages](./dataset/washington_outages.csv)
- [Washington Outage Tweets](./dataset/washington_outage_tweets.csv)
- [Combined EIA Data](./dataset/combined_data.csv)

Sources : 

https://www.eia.gov/electricity/monthly/epm_table_grapher.php?t=epmt_b_1
https://github.com/taspinar/twitterscraper        
https://github.com/topojson/topojson
https://github.com/alexandres/lexvec
https://twitter.com/


