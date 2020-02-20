# Project 5 : Client Project

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

In the event of a blackout, residential areas often experience prolonged power outages.  Throughout the past, utility companies mapped power outages based on live feeds and data from major energy entities in order to pick up power outages in an area to investigate.  In order to help utility companies identify neighborhoods of prolonged power outages, we wanted to come up with a decision tree model that can accurately determine whether a live social media (twitter) data can be used to obtain information regarding an ongoing power outage based on historical data.  


### Executive Summary

In order for any analysis, we first needed to determine what data we will be using to determine the *cosine similarity* of the average vector of words in a tweet against pro-outage versus anti-outage word lists.  Although we had originally intended on covering all major power outage incidents in the US, we observed that our historical data from eia.gov only goes down to a county level by state on a granular scale in recording major outages.  As a result, we wanted to focus our *sentiment analysis* on a county level, so we picked *Kings County, Washington* as a focal point and used twitterscraper to scrape all tweets of users during the period of an actual power outage in the area up to 1 day after the event had ended.  We then decided we will be using a decision tree model using sentiment analysis score to determine if we can classify whether a specific tweet is indeed related to a power outage during a blackout period or not.  

#### TwitterScraper

**One of the issues with using a twitterscraper instead of a twitterapi was that we are not able to obtain geolocation of tweets which brings up limitations on accurately determining locations of live tweets.**

We narrowed our search query to scan for all tweets within *x* distance in all counties in the state of Washington within a 1 day range of different power outage restoration date & time period.  During our query, we specifically excluded results that are associated with "Washington DC", "D.C", "District of Columbia" to remove any unrelated query results that may affect our train data. 

#### Word2Vec

Word2vec returns the cosine similarity of words based on *data* so that we can feed the results on a decision tree model to provide us an insight.  We were able to successfully create and segregate words based on their correlation to another relating to a power outage.  We then classified the words as **here**if they are pro-outage or **here** if they are anti-outage based on the cosine similarity.  

#### Decision Tree Model

#### Geopandas Visualizations


### Conclusion


### Recommendation

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

Sources : https://www.eia.gov/electricity/monthly/epm_table_grapher.php?t=epmt_b_1




