# Tutorial Twitter Web Crawler with Python3 
## Introduction 
This python3 programming exercise is about creating a Web Crawler for Twitter with Python 3. The Crawler shall search through the twitter posts of a user in an automated way and read all HTML-tags until our desired tag is found.


### Learn how to ...
* Make a web request to a web site
* Read the HTML DOM-Tree with Beautiful Soup
* Automatically downloading all pictures of the twitter user
* create a CSV file
* create a JSON file and read a JSON file

### Goal
* Task 1: pull all tweets from the twitter user `SATW_ch`
* Task 2a: create a CSV with all the tweets
* Task 2b: create a JSON file with all the tweets
* Task 2c: read the JSON file and search for specific keywords
* Task 3: download all images that have been tweeted by this user


## Preparation
### Step 1
Please run the following commands (e.g. Hacking-Lab LiveCD) and set up your python3 environment.

```
mkdir -p /opt/git
cd /opt/git
git clone https://github.com/ibuetler/p3s-twitter.git
cd /opt/git/p3s-twitter
pipenv --python 3 sync
pipenv --python 3 shell
```

## Pull all tweets from the twitter user `SATW_ch`

In this Task, we want to receive all tweets a specific user has ever made and want to store them in a CSV file. The Profile of SATW_ch will be used for this task.

### Task 1: Receive all Tweets

To get all the tweets, some theory is necessary, which will be explained in the next steps.

#### Theory about Requests

First of all, we need to make a web request to the Twitter user's profile and save an instance of it. In Python 3 we can use the Request library to solve this. The request object has a function 'get' which takes an URL as a parameter and makes a request to the given URL. This simple code snippet illustrates this:

```Python
import requests
url = 'https://twitter.com/SATW_ch'
response = requests.get(url)
```

#### Theory about Beautiful Soup

Beautiful Soup is a Python library for getting data out of HTML, XML, and other markup languages. It is possible to pull particular content from a webpage, remove the HTML markup, and save the information. For our Task, this means we need to find the particular HTML tag which stores the information for each tweet.

Firstly, from our instance of the request we want to receive the HTML structure. This can be achieved like this:

```python
from bs4 import BeautifulSoup

html = BeautifulSoup(response.text, 'html.parser')
```

The whole HTML structure of our previous request is now saved in the HTML variable.

The next step is to find the HTML tag that stores the information for the tweets. It is possible to view the HTML structure of the websites in the browser developer tools and go through the whole site until the desired Tag is found. You can try this on your own if you want, otherwise below is the tag provided and how to extract it.

Open the Google Chrome browser and go to https://twitter.com/SATW_ch. Navigate to View -> Developer -> Developer -> Developer Tools. You should now see the following:
 
 ![screenshot](/media/challenge/png/Screenshot_developer_tools.png)
 
As an alternative, you could also print the HTML variable from the above snippet to the console. But this will be very unstructured and hard to read.
 
 Now try to find the corresponding tag that contains all tweets as a small hint all tweets are kept in a list.
 
-----------
Solution:


In Twitter, all tweets are contained in a div-element that has the class "stream-container". In this container, the tweets are embedded as a list. Every tweet is a List-element (li HTML-Tag) with the the following data attribute "data-item-typ = tweet".

This Code illustrates how to receive all tweets with beautiful soup:

```python
tweets = html.find_all("li", {"data-item-type": "tweet"})
```
------------

Now we want to receive the actual content of the tweet. Every tweet is embedded in a p-Element which posses the following 4 Classes: TweetTextSize TweetTextSize--normal js-tweet-text tweet-text. Since the tweets variable contains a list of each tweet, we need to iterate over it to get the p element of each list entry. This illustrated by this snippet:


```python

tweets = html.find_all("li", {"data-item-type": "tweet"})
  for tweet in tweets:
        tweet_text_box = tweet.find("p", {"class": "TweetTextSize TweetTextSize--normal js-tweet-text tweet-text"})
        tweet_text = tweet_text_box.text
        tweet_id = tweet['data-item-id']
```

The find_all Method is provided by beautiful soup. The method goes through the whole provided DOM-structure and searches for the tag given in the parameter. Find_all is used when the tag is not unique and can have multiple occurrences. Therefore, a list is returned. On the other hand, find is used when the tag is unique and only one can occur. 


Additionally, not only the text should be extracted, but also the date of the tweet and all its Hashtags. For a better usage later for our CSV file and JSON file a dictionary should be used which has the ID of the tweet as the key and a string as the value. The string is composed of date, tweet content, hashtags. Here an example of how the String should look like:

```
Date: 03:10 - 16. Apr. 2020,Tweet-text: .@Leopoldina hat Eine dritte Ad-hoc-Stellungnahme zur COVID19-Pandemie veröffentlicht. Das Papier behandelt behandelt die psychologischen, sozialen, rechtlichen, pädagogischen und wirtschaftlichen Aspekte,Tags: #COVID19 #Pandemie
```
The string "Date:" should be added before the date. After the date a comma and then before the Tweet content the string Tweet-text: closed with a comma. Before the hashtags the string "Tags:" is to be added.

**Extracting the date**

The date of each tweet can be found in a link tag (a). The tags have the following classes: "tweet-timestamp js-permalink js-nav js-tooltip". The data can then be read using the title attribute. The following code shows this:
 
```python
tweets = html.find_all('li', {'data-item-type': 'tweet'})
date_box = tweet.find('a', {'class': 'tweet-timestamp js-permalink js-nav js-tooltip'})
date = date_box['title']
```
**Extracting the tags of the tweet**

The hashtags of a tweet are again stored in a link tag. Each tag has the following classes: 'twitter-hashtag pretty-link js-nav'. Please note that a tweet can have several tags. So you must not use the find method of beautiful soup, instead the find_all method must be used. 

This is illustrated here:

```python
tweets = html.find_all('li', {'data-item-type': 'tweet'})
hash_tags = tweet.find_all('a', {'class':'twitter-hashtag pretty-link js-nav'})
```
The content of the tag is then contained in a 'b' tag. This content should be added to a list in form of a string with a Hash Tag character added. For this we have to iterate over the found hash tags as follows:

```python
 hash_tags = tweet.find_all('a', {'class':'twitter-hashtag pretty-link js-nav'})
 tag_list = []
 for tag in hash_tags:
   tag_text = tag.find('b')
   tag_string = "#"+tag_text.text
   tag_list.append(tag_string)
```

**Task::** With this knowledge, write a function named get_this_page_tweets(HTML), this function takes a Beautiful Soup HTML instance and returns a dictionary with the tweet ID as key and the value is a string composed of date, content, tags of the tweet.

If you want some additional help, below is skeleton which you can use:


Skeleton:

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

The list of tweets is located in a div-container with the class "stream-container" and has an attribute "data-min-position". This attribute always holds de ID of the last Tweet of the current page. In our case with only one request, it will hold the ID of the 20th tweet. 

It is now possible to use this ID for manipulating our request. In a Twitter URL, there is a parameter called "max_position". This parameter will define from which ID the next 20 tweets should be loaded.

This snippet shows how the ID of the last tweet can be received and how this can be used to load the next 20 with the URL:

```python
    next_tweets = html.find("div", {"class": "stream-container"})["data-min-position"]
    next_url = "https://twitter.com/i/profiles/show/" + 'SATW_ch' + \
                "/timeline/tweets?include_available_features=1&" \
                "include_entities=1&max_position=" + next_tweets + "&reset_error_state=false"
```


**Task:** Now write a function with the name get_all_tweets(HTML) This function takes an HTML instance of Beautiful Soup created. Call the function from the previous step to get the first 20 tweets. Then find the ID of the last tweet and adjust the URL to this ID. Now iterate until all tweets are loaded.

If you want some additional help, below is a skeleton of the get_all_tweets function.


Skeleton:

```python

def get_all_tweets(html):
    tweets_dict = get_this_page_tweets(html)
    next_tweets = html.find("div", {"class": "stream-container"})["data-min-position"]
    
  #iterate over the URL until no more tweets are found
  
  return tweets_dict
```



### Task 2a: Create CSV File

Next, we want to write our Dictionary into a CSV File. A CVS file can be created with the "with" Keyword. This Example illustrates this:

```python
import csv

 with open('tweets.csv', 'w') as csv_file:
        writer = csv.writer(csv_file)

```
The "with" keywords creates the tweets.csv File in write mode if it does not exist. After a Writer Instance is created it provides member functions to manipulate the file.

The writer Instance provides a helper function which is called "writerow([Lits])". This Function will write the provided List in the parameter as a delimited string into the CSV File. It also takes as in our case a dictionary as a parameter and will directly write from the dictionary in a CSV format.


**Task** Create a function "create_CSV_file(dict)" which takes a dictionary as a parameter and converts it into a CSV File. The dictionary passed is the format which the above functions created. For a better Layout replace in the dictionary for every value it contains the '\n' character by a whitespace. Then Loop over the dictionary and create a CVS file with the Format: Tweet-ID, Tweet

Here is an example of an resulting Entry:

1250728160070709248,",Date: 03:10 - 16. Apr. 2020,Tweet-text: .@Leopoldina hat eine dritte Ad-hoc-Stellungnahme zur #COVID19-Pandemie veröffentlicht. Das Papier behandelt behandelt die psychologischen, sozialen, rechtlichen, pädagogischen und wirtschaftlichen Aspekte der #Pandemie: https://www.leopoldina.org/publikationen/detailansicht/publication/coronavirus-pandemie-die-krise-nachhaltig-ueberwinden-13-april-2020/ …pic.twitter.com/AESvsHtHof,Tags: #COVID19 #Pandemie "


This 2 Documentation might be helpful:
String Functions: https://docs.python.org/2.5/lib/string-methods.html
CSV Reader/Writer: https://docs.python.org/3/library/csv.html

## Task 2b create a JSON file

In this task, we want to display our tweets not only as CSV format but also as JSON format. JSON offers the advantage that it can be easily queried to get specific information. We will do this in the next task.

Python provides a library called json to read and write JSON files. A dictionary can be easily converted to JSON format. You create a dictionary that contains a list of JSON objects. Here is an example how to create a JSON object for the tweets in our case:

```python
 json_dict = {}
 json_dict['tweets'] = []
 json_dict['tweets'].append({
            'id': 'example',
            'date' : 'example' ,
            'tweet-text' : 'example',
            'tags' : 'example'
        })
```

This dictionary can then be easily converted to a JSON file using the JSON library. The file can be created again with the "with" keyword and inside the json.dump function is called. This function takes a dictionary and an output file as parameters. This dictionary is then written to the file in JSON format. Here is an illustration:

```python
  with open('tweets.json', 'w') as output:
      json.dump(json_dict, output)
```

**Task** Write a function with the name create_Json_file, which takes a dictionary as a parameter. This dictionary is the one created by the previous get_all_tweets method. Then iterate over this dictionary and create the JSON file.

## Task 2c: Read the JSON file and extract information

In this task, we want to write a method that takes a string as parameter and searches our JSON file for it. To be more precise, hashtags are passed in the form of a string and our method should then retrieve all JSON objects (tweets) that contain this hashtag.

To read a JSON file, the "with" keyword can be used in combination with the JSON.load function from the JSON library. This function returns a dictionary with the same structure as the one we created in the previous task.  

```python
    with open ('tweets.json', 'r') as input:
        data = json.load(input)
```

**Task** Now write a function named find_tweets_by_tag which takes a string as parameter and returns a list with all tweets containing this string as a hashtag.

## Task 3: Download Pictures

In this next step, the goal is to download all images that the user has ever posted. The account used for this task is again SATW_ch.

### Theory downloading Pictures

To download images, the library wget can be used. Using this library is very easy. After importing the library, the wget object offers a "download" function. This function takes 2 parameters: the first is the URL of the image and the second is the path where the image should be saved and under which name.

Illustrated by this snippet:

```python
import wget

wget.download(https://example.com/picture.jpg, 'pictures/pictureExample.jpg')

```
###  Finding URL of the pictures

To find the URL of the images, we have to load the tweets again, as in the previous task. In each tweet, if there is an image, there is a "div" tag with the following classes: "AdaptiveMedia-photoContainer js-adaptive-photo". This div tag has an attribute "data-image-url", which contains the link from the image.

The following Code shows how to receive the link with beautiful soup:

```python
 pic_div = tweet.find("div", {"class": "AdaptiveMedia-photoContainer js-adaptive-photo"})
        if pic_div is not None:
            url = pic_div['data-image-url']

```

**Task** Now write two functions as in the previous task. One function with the name and parameter "get_images_of_page(html)". This function returns a list with the URL's of the images from the current page. The other function is called "get_all_images(html)", which iterates through all tweets and always calls the function "get_images_of_page". (As a reminder a URL request only loads 20 tweets at a time). The function returns a list with all URL's for the images. Then write a third function named "download_images(picture_list)". This function receives the list and downloads all images. Create a new folder with the name "pictures". The images should have a continuous name, the first image has the name "picture1.jpg", the second one "picture2.jpg" and so on.

**Hint:** In Python 3 two lists can be merged with the += operator. Example: 
```python
list1 += list2 #list 2 will be merged in list1
```

