### EC601_Project2

# Mood Trend

By using Google NLP and Twitter API, I made an app called "Mood Trend" that could analyze an Twitter user's sentiment trend over a period. The source code is in the file analyzer.py, also displayed below:

``` python
#!/usr/bin/env python
# encoding: utf-8

from google.cloud import language_v1
import requests
from requests import session
import tweepy # https://github.com/tweepy/tweepy
import json
#Twitter API credentials
consumer_key = "8KMps10UjAdfCZoLzUMOjJtaX"
consumer_secret = "wstgm1dNVCgnPWKiBJ1Z7EPnIjQGzrYgLwvUwvC6N2tQ7z1zFv"
bearer_token = "AAAAAAAAAAAAAAAAAAAAAL5iUAEAAAAAmo6FYRjqdKlI3cNziIm%2BHUQB9Xs%3DS31pj0mxARMTOk2g9dvQ1yP9wknvY4FPBPUlE00smJcncw4dPR"

class Twitter_feed:

    def __init__(self,comsumer_Key, consumer_Secret):
        self.consumer_key = comsumer_Key
        self.consumer_secret = consumer_Secret
        self.bearer_token = bearer_token
        self.avg_sentiment = []

    def sentiment_analysis(self):
        auth = tweepy.AppAuthHandler(consumer_key, consumer_secret)
        try:
            api = tweepy.API(auth)               
            for tweet in tweepy.Cursor(api.search_tweets, q='tweepy').items(10):
                text = tweet.text
                document = language_v1.Document(content=text, type_=language_v1.Document.Type.PLAIN_TEXT)
                sentiment = client.analyze_sentiment(request={'document': document}).document_sentiment
                print("Text: {}".format(text))
                print("Sentiment: {:.1f}, {:.1f}\n-------------------------".format(sentiment.score, sentiment.magnitude))
                self.avg_sentiment.append(round(sentiment.score,3))

        except tweepy.TweepError:
            print('Error! Failed to get request token.')

if __name__ == '__main__':
    client = language_v1.LanguageServiceClient()
    twitter_acount = Twitter_feed(consumer_key, consumer_secret)
    twitter_acount.sentiment_analysis()
    print(f"{len(twitter_acount.avg_sentiment)} tweets are analyzed.")
    print("The user's sentiment score trend is: ",twitter_acount.avg_sentiment)
    
```
Sample output:

10 tweets are analyzed.
The user's sentiment score trend is:  0.2 0.2 0.2 0.3 0.2 0.2 0.0 0.1 0.2 0.2

![output](https://github.com/chenchuw/EC601_Project2/blob/main/output.png?raw=true)

# User Story

Story #1

- I haven't seen my brother for a long time, I would like to see how is he doing by using the social media analyzer to anlayze his tweets over a long period and see is he happy or not.


# MVP

- A module that can analysis a given context and return a sentiment score
 
- A module that can retrieve text from social media posts

- A module that can plot the sentiment score over the number of tweets

# Modular Design

![modular_design](https://github.com/chenchuw/EC601_Project2/blob/main/Screen%20Shot%202021-10-24%20at%2020.18.42.png?raw=true)

The following sections explain the details of how my app works.

# Cloud Natural Language API
First I used Google's Cloud Natural Language API. It is Google's proven pre-trained model for general content classification; sentiment analysis; entity recognition, etc. After getting access to the client library, I connected to the Cloud and tested sentiments for several sentences using my python script. The script is called language.py in this repository. 

*Code example*
```python

# Imports the Google Cloud client library
from google.cloud import language_v1


# Instantiates a client
client = language_v1.LanguageServiceClient()

# The text to analyze
text = input("Give me a sentence:\n")
document = language_v1.Document(content=text, type_=language_v1.Document.Type.PLAIN_TEXT)

# Detects the sentiment of the text
sentiment = client.analyze_sentiment(request={'document': document}).document_sentiment

print("Text: {}".format(text))
print("Sentiment: {:.1f}, {:.1f}".format(sentiment.score, sentiment.magnitude))
```

A snippet of the output is attached below:

(base) francischen@Ccws-Macbook-Pro EC601 % python language.py\
Give me a sentence:\
Professor Osama is the best!\
Text: Professor Osama is the best!\
Sentiment: 0.9, 0.9\
(base) francischen@Ccws-Macbook-Pro EC601 % python language.py\
Give me a sentence:\
Today's weather sucks\
Text: Today's weather sucks\
Sentiment: -0.7, 0.7\
(base) francischen@Ccws-Macbook-Pro EC601 % python language.py\
Give me a sentence:\
I hate this world.\
Text: I hate this world.\
Sentiment: -0.9, 0.9\
(base) francischen@Ccws-Macbook-Pro EC601 % python language.py\
Give me a sentence:\
Could you pass me the pen?\
Text: Could you pass me the pen?\
Sentiment: -0.3, 0.3

As we observed here, from a scale of -0.9 to 0.9, the NLP returns the sentiment score of the input sentences, following that more positive sentence get a higher score.

# Twitter API (Retrieving Tweets)

A program to retrive tweets. Python script is called Tweeter_Retrive.py in this repository. 

*Code example*
```python
#!/usr/bin/env python
# encoding: utf-8

import requests
from requests import session
import tweepy # https://github.com/tweepy/tweepy
import json
#Twitter API credentials
consumer_key = "8KMps10UjAdfCZoLzUMOjJtaX"
consumer_secret = "wstgm1dNVCgnPWKiBJ1Z7EPnIjQGzrYgLwvUwvC6N2tQ7z1zFv"
bearer_token = "AAAAAAAAAAAAAAAAAAAAAL5iUAEAAAAAmo6FYRjqdKlI3cNziIm%2BHUQB9Xs%3DS31pj0mxARMTOk2g9dvQ1yP9wknvY4FPBPUlE00smJcncw4dPR"

class twitter_access:

    def __init__(self,comsumer_Key, consumer_Secret):
        self.consumer_key = comsumer_Key
        self.consumer_secret = consumer_Secret
        self.bearer_token = bearer_token


    def build_connections(self):
        auth = tweepy.AppAuthHandler(consumer_key, consumer_secret)
        try:
            api = tweepy.API(auth)
            for tweet in tweepy.Cursor(api.search_tweets, q='tweepy').items(10):
                print(tweet.text)

        except tweepy.TweepError:
            print('Error! Failed to get request token.')

if __name__ == '__main__':

    web_app = twitter_access(consumer_key, consumer_secret)
    web_app.build_connections()
 ```
The output is below:

(base) francischen@Ccws-Macbook-Pro EC601 % python Twitter_Retreive.py\
tweepy + oauth!\
@CoderDrax  Latest Covid Updates:-\
                   Total Cases : 233,569,801 \
Active Cases :18,399,778    Closed… https://t.co/26sbqm3ZJd\
RT @chinryu: 日本のツイートトレンドTOP10は['ゴルゴ13', '岸田さん', '決選投票', 'さいとう・たかをさん死去', '高市さん', 'すい臓がんのため死去', '本人の遺志', 'オリコン', 'いとう先生',\ '#自民党総裁選']です #Pytho…\
日本のツイートトレンドTOP10は['ゴルゴ13', '岸田さん', '決選投票', 'さいとう・たかをさん死去', '高市さん', 'すい臓がんのため死去', '本人の遺志', 'オリコン', 'いとう先生', '#自民党総\裁選']です #Python #Tweepy\
hello tweepy ですよん\
@CoderDrax  Latest Covid Updates:-\
                   Total Cases : 233,569,801 \
Active Cases :18,399,778    Closed… https://t.co/1btK1p5Len\
hello tweepy tweet\
hello tweepy tweet https://t.co/9RsHR0XAIU\
日本のツイートトレンドTOP10は['ゴルゴ13', 'さいとう・たかをさん死去', 'オリコン', '本人の遺志', 'すい臓がんのため死去', 'いとう先生', '#出産直後の感想教えて', '#招き猫の日', '… \https://t.co/Tfa7oqPGQA\
hello tweepy\
