# Client Project: Using Twitter to Identify Counties with Power Outages
by Sang Cheon, Ethan Henley, and Jason Morman\
prepared for New Light Technologies in cooperation with General Assembly

---

### Project Notebooks
- [1 Introduction and Data Collection](./code/01_introduction_collection.ipynb)
- [2 EDA](./code/02_eda.ipynb)
- [3 Modeling and Conclusion](./code/03_model_conclusion.ipynb)
- [A1 Mapping](./code/a01_mapping.ipynb)

### Table of Contents:
- [Problem Statement](#Problem-Statement)
- [Executive Summary](#Executive-Summary)
    - [EIA-Data](#EIA-Data)
    - [TwitterScraper](#TwitterScraper)
    - [Preprocessing and Word2Vec](#Preprocessing-and-Word2Vec)
    - [Decision Tree Model](#Decision-Tree-Model)
- [Recommendation and Conclusion](#Recommendation-and-Conclusion)
- [Data Dictionary](#Data-Dictionary)
- [Libraries Used](#Libraries-Used)
- [Sources](#Sources)

---

## Problem Statement

An emergency response organization or public utility company will, from time to time, have to deal with power outages on varying scales, and at those times may want to directly scan social media such as Twitter to see if people in a certain locality are posting about outages that need to be resolved. We have developed a proof of concept for this task, focused on the county level for the state of Washington: a model that can classify a tweet as outage-relevant or -irrelevant. A user could run this model on a live feed of tweets to determine which represent real power outages, then attend to those outages.

---

## Executive Summary

Twitter is a constant source of new information, but it can be difficult for a human to discern meaning from a live feed of tweets, and the challenge is even greater for a machine. We built a two-part model that can predict with about 89% accuracy the relevance to power outages of a tweet. Wanting to choose a manageable but large-enough locale, we focused specifically on power outages and tweets from the state of Washington.

### EIA Data

We first gathered information about major power outages from the US Energy Information Administration's Electric Power Monthly reports from the Februaries of 2013-2019, as each February's issue contains information for the whole of the prior year. Specifically, we used Table B.1 Major Disturbances and Unusual Occurrences. 

We collated these, cut the dataset down to only outages affecting the state of Washington, and then further restricted to outages for which county-specific information existed. We selected Washington because it was the state for which the most county-level information was available in the dataset. We refer to the events described in this limited table as county-level outages or major outages for the state of Washington.

### Scraping Twitter

We next used Ahmet Taspinar's twitterscraper library to gather tweets. We selected twitterscraper over the Twitter API because the free version of the API only goes back 7 days and cuts tweets off after 140 characters, though the premium Twitter API is likely the tool of choice for implementing this fully for live data.

We ran three sets of queries through twitterscraper, which is built to interface with Twitter's advanced search funciton. Our first query searched for tweets with the words power, outage, or blackout during outages listed in our reduced EIA dataset and in the counties affected by those outages, generating our positive class of outage-related tweets. Our second query searched the same set of counties affected by any major outages, but across the whole timespan of 2012-2019 and with no text specifications, generating our negative class of tweets we believed were virtually all unrelated to power outages. 

Our final query searched for tweets with the words power, outage, or blackout, but during the full set of counties for the 2012-2019 timespan, generating what we called our unknown class—tweets not meant to be used in training or testing a model, but more analogous to the tool's potential use case of predicting outage-relatedness of tweets for which there is no known class. For each tweet we saved its text along with some information about the query and its class.

### Preprocessing and Word2Vec

Building off of others' similar past projects, we processed tweets for modeling with RegEx and other tools. We interpreted hashtags as single words, removed Twitter handles and most URLs, removed non-alphabetical characters as well as stopwords from the nltk English stopwords corpus (barring some that we deemed potentially relevant to power outages), and tokenized tweets to reduce them to lists of potentially relevant words. 

The first phase of our modeling used the unsupervised Word2Vec model, a method of interpreting words in some multidimensional vector space representing their relationships with neighboring words and each other. We decided to use a pretrained model developed by Alexandre Salle off of a scrape of Wikipedia, as our attempts to develop models based on tweets led to results that did not hold clear meaning for human language. 

We vectorized each tweet and then determined the vector's cosine similarity to a standard vector of words related to power outages (but deliberately excluding words that were included in our search query itself). These cosine similarities were a simple quantification of the outage-relatedness of a tweet, but because the process was unsupervised, there was no way of determining what cosine similarities actually represented power outages.

### Decision Tree Model

To create a cutoff point in cosine similarity to the power outage vector, we used a supervised model and a deliberately weak learner: a one layer decision tree classifier, a stump. In essence, we only leveraged the power of the information gain algorithm to find an optimal cutoff point in the already established group of cosine similarities—this was not like typical predictive use of decision trees. 

We trained and tested the decision tree stump on our combined positive and negative classes, bootstrapping the positive class to account for the greater quantity of tweets in the negative class. This model, in conjunction with the previous model, was about 89% accurate in predicting outage-relatedness for both the (bootstrapped) train and test data.

As proof of concept for our use case, we ran the model on our unknown class and treated the positive predictions as legitimate, outage-related tweets. We mapped these tweets, along with our confirmed positive class, in an appendix to the project.

---

## Recommendation and Conclusion

Governments, emergency response agencies, and utilities already have tools at their disposal for recognizing and reacting to power outages, but they are not publically available and cannot always respond immediately or account for individual reporting. Twitter is not a convenient source for most data, but with tweets one can detect far more power outages than are, say, reported by the EIA. Our tool, though it is only a limited proof of concept, is clear evidence that even lightweight interpretation of social media posts can generate meaningful conclusions about power outages, as it is successful at determining the outage-relatedness of tweets from the state of Washington with an accuracy of 89%. Were one to integrate it with the Twitter premium API, the resultant tool would provide more useful and specific power outage location data than some preexisting tools and datasets can. We hope that our model, or its structure and principles, can be furthered to better support the organizations that respond to power outages, and we thank the reader for considering our work.

---

## Data Dictionary

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
|event_row_or_county|object|wa_tweets|An integer corresponding to a specific event for the positive class of tweets, the county name for other tweets.| 
|timestamp|datetime|wa_tweets|Tweet's timestamp.| 
|text|string|wa_tweets|The content of the tweet.| 
|outage|int64|wa_tweets|1 for tweets about known major county-level outages, 0 for tweets not believed outage-related, -1 for unknown.| 

---

## Sources
- [EIA](https://www.eia.gov/electricity/monthly/) February Reports Table B1, 2012-2019
- [Eldersveld](https://github.com/deldersveld/topojson)'s Map of USA by County
- [Twitterscraper](https://github.com/taspinar/twitterscraper) library by Ahmet Taspinar
- [English Wikipedia 2015 Pre-trained LexVec Word Vectors](https://github.com/alexandres/lexvec#pre-trained-vectors) by Alexandre Salle
- Previous Cohorts' Power Outage Identification Projects
	- [Devahastin Na Ayudhya et al](https://github.com/boom-deva/FEMA-Power-Outage-Hotspot-Detection)
	- [Christiansen et al](http://github.com/jenrhill/Power_Outage_Identification/)
