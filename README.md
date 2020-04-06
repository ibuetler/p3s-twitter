 

# Tutorial Twitter Web Crawler with Python3 

  

## Introduction 

This challenge is about creating a Web Crawler for Twitter with Python 3. You will get the name of the Twitter Profil we will use. The Name is located in the Profile.txt File.


### Learn how to ...

* Make Request to a web site

* Read the HTML DOM-Tree with Beautiful Soup

* Download Pictures in a automated way


## Preperation

### Step1

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

### Step1 Receive all Tweets

To get all the tweets, first some theory is necessary, which will be explained in the next steps.

#### Theory about Requests

First of all, we need to make a web request to the Twitter user's profile and save an instance of it. In Python 3 we can use the Request library to solve this. This simple Code snippet illustrates this:
```Python

url = 'https://twitter.com/SATW_ch'
response = requests.get(url)

```

#### Theory about Beautiful Soup

Beautiful Soup is a Python library for getting data out of HTML, XML, and other markup languages. It is posible to pull particular content from a webpage, remove the HTML markup, and save the information. For our Task, this means we need to find the particular html tag which stores the information for each tweet.




