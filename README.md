 

# Tutorial Twitter Web Crawler with Python3 

  

## Introduction 

This challenge is about creating a Web Crawler for Twitter with Python 3. You will get the name of the Twitter Profil we will use. The Name is located in the Profile.txt File.


### Learn how to ...

* Make Request to a web site

* Read the HTML DOM-Tree with Beautiful Soup

* Download Pictures in a automated way


## Preperation

### Step 1

We have a pipenv python3 skeleton for you. please run the following commands (e.g. Hacking-Lab LiveCD) and setup your python3 environment.

```
mkdir -p /opt/git
cd /opt/git
git clone https://github.com/ibuetler/p3s-twitter.git
cd /opt/git/twitter
pipenv --python 3 sync
pipenv --python 3 shell
```
you should now have your python3 environment ready for this exercise.

## Receive all Tweets from a user

In this Task we want to receive all tweets a specific user has ever made and want to store them in a csv file. The Profile of SATW_ch will be used for this task.

### Step 1: Receive all Tweets

To get all the tweets, first some theory is necessary, which will be explained in the next steps.

#### Theory about Requests

First of all, we need to make a web request to the Twitter user's profile and save an instance of it. In Python 3 we can use the Request library to solve this. This simple Code snippet illustrates this:
```Python

url = 'https://twitter.com/SATW_ch'
response = requests.get(url)

```

#### Theory about Beautiful Soup

Beautiful Soup is a Python library for getting data out of HTML, XML, and other markup languages. It is posible to pull particular content from a webpage, remove the HTML markup, and save the information. For our Task, this means we need to find the particular html tag which stores the information for each tweet.

Firstly, from our instance of the request we want to receive the html structure. This can be achieved like this:

```python
from bs4 import BeautifulSoup

html = BeautifulSoup(response.text, 'html.parser')
```
The whole html structure of our previous request is now saved in the html variable.

The next step is to find the HTML tag that stores the information for the tweets. It is possible to view the HTML structure of the websites in the browser developer tools and go through the whole site until the desired Tag is found. To save this time, we will provde the tag. In Twitter all tweets are contained in a div-elment that has the class "stream-container". In this container the tweets are embedded as a list. Every tweet is List-element (li HTML-Tag) with the the following data attribute "data-item-typ = tweet".

This Code illustrates how to receive all tweets with beautiful soup:

```python

tweets = html.find_all("li", {"data-item-type": "tweet"})

```

Now we want to receive the actual content of the tweet. Every tweet is embedded in a p-Element which posses the following 4 Classes: TweetTextSize TweetTextSize--normal js-tweet-text tweet-text. Since the tweets variable contains a list of each tweet, we need to iterate over it to get the p element of each list entry. This illustrated by this snippet:


```python

 tweets = html.find_all("li", {"data-item-type": "tweet"})
    for tweet in tweets:
           tweet_text_box = tweet.find("p", {"class": "TweetTextSize TweetTextSize--normal js-tweet-text tweet-text"})
           tweet_text = tweet_text_box.text
           tweet_id = tweet['data-item-id']
```

**Task::** With this knowledge, write a function named get_this_page_tweets(html), this function takes a Beautiful Soup HTML instance and returns a dictionary with the tweet ID as key and the tweet content as value.

**Skeleton:** you can use this Code Skeleton for your solution

```python
from bs4 import BeautifulSoup
import requests

url = 'https://twitter.com/SATW_ch'
response = requests.get(url)

html = BeautifulSoup(response.text, 'html.parser')

def get_this_page_tweets(html):
   tweets_dict = {}
   
   # your code
   
   
   return tweets_dict

```

As you will notice your solution will only contain the recent 20 tweets. This is because the tweets are in a scrollbar and only loaded when you scroll down. The request does not do this for us and therefore only contains the first 20 tweets.

The list of tweets is located in a div-container with the class "stream-container" and has an attribute "data-min-position". This attribute always holds de ID of the last Tweet of the current page. In our case with only one request it will hold the ID of the 20th tweet. It is also possible to specify in the URL from which tweet the history should be displayed.

This snippet shows how the ID of the last tweet can be received and how this can be used to load the next 20 with the URL:

```python
    next_tweets = html.find("div", {"class": "stream-container"})["data-min-position"]
    next_url = "https://twitter.com/i/profiles/show/" + 'SATW_ch' + \
                "/timeline/tweets?include_available_features=1&" \
                "include_entities=1&max_position=" + next_tweets + "&reset_error_state=false"
```


**Task:** Now write a function with the name get_all_tweets(html) This function takes an html instance of Beautiful Soup created. Call the function from the previous step to get the first 20 tweets. Then find the ID of the last tweet and adjust the URL to this ID. Now iterate until all tweets are loaded.

***Skeleton*** for this Function:

```python

def get_all_tweets(html):
    tweets_dict = get_this_page_tweets(html)
    next_tweets = html.find("div", {"class": "stream-container"})["data-min-position"]
    
  #iterate over the URL until no more tweets are found
  
  return tweets_dict
```

### Step 2: Create CSV File

Next, we want to write our Dictionary into a CSV File. A CVS file can be created with the "with" Keyword. This Example illustrates this:

```python
import csv

 with open('tweets.csv', 'w') as csv_file:
        writer = csv.writer(csv_file)

```
The "with" keywords creates the tweets.csv File in write mode if it does not exist. After a Writer Instance is created which provides member functions to manipulate the file.

The writer Instance provides a helpful function which is called "writerow([Lits])". This Function will write the provided List in the parameter as a delimited string into the CSV File


**Task** Create a Funtcion create_CSV_file(dict) which takes a dictionary as a parameter and converts it into a CSV File. For a better Layout replace in the dictionary for every value it contains the '\n' character by a whitespace. Then Loop over the dictionary and create a CVS file with the Format: Tweet-ID, Tweet

Here is an example of an resulting Entry:

1247139537580687360,"Ein kleiner Ausblick auf den nächsten #TechnologyOutlook im 2021: Was bleibt, was ändert sich, was wird besser: http://ow.ly/Znkt50z6gGN  #Innovationpic.twitter.com/tGFCCM0NeH"

This 2 Documentation might be helpful:
String Functions: https://docs.python.org/2.5/lib/string-methods.html
CSV Reader/Writer: https://docs.python.org/3/library/csv.html

## Download Pictures
