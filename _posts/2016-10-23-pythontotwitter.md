---
layout: post
title: Python to Twitter
---
**This post assumes you know how to install Python and submodules for Python.**

I have been working on a script that will allow me to post a link to [Twitter](https://twitter.com/ki6bjv) when I write a blog post here. 

I have used [IFTTT](https://ifttt.com/) for things like this in the past, but I have never been happy with that. Technically IFTTT works but I have had issues, double posts, posting the wrong thing, etc. So I wanted to figure out away to do this myself.

And of course I turned to Python for this. 

I started by trying to find a module that would allow me to easily post to Twitter. I found a module called [Twython](https://github.com/ryanmcgrath/twython), it was fairly easy to set up: Something like this:

{% highlight ruby %}
# Import Twython and TwythonError
from twython import Twython, TwythonError

# Set up your Credententals
APP_KEY = 'app key'
APP_SECRET = 'app secret'
OAUTH_TOKEN = 'oauth token'
OAUTH_TOKEN_SECRET = 'oauth secret token'
# set up your twython connection to twitter
twitter = Twython(APP_KEY, APP_SECRET, OAUTH_TOKEN, OAUTH_TOKEN_SECRET)
#send your status to twitter
twitter.update_status(status = 'Posting to twitter from Python')

{% endhighlight %}<br/>
You can create an 'app' and create the needed credentials by going to [https://apps.twitter.com](https://apps.twitter.com)

Next I wanted to use my [bit.ly](https://bit.ly) account to create a 'short link'(this is  in air quotes because my short link is www2.ki6bjv.com/xxxxxxxx because I haven't figured out a shorter url that I am happy with yet. 😏) 

Awesome thing about Bit.ly is that they have an API, man I love API's. So I didn't need to go find a module for Python I already had one that i used before that is awesome......[Requests](https://github.com/kennethreitz/requests). To implement requests you do something like this:

{% highlight ruby %}
# Import the Requests Module
import requests

# Set first half of API call with API key
apiend    = 'https://api-ssl.bitly.com/v3/shorten?access_token=APITOKENGOESHERE&longUrl='
# Set the link you want to shorten 
longlink  = 'https://google.com'
# Set format you want the API to return data in 
getformat = '&format=txt'
# combine all elements of the API url
url = (apiend + longlink + getformat)
# Run API get 
r = requests.get(url)
#Print out the results of the API call
print r.text

{% endhighlight %}<br/>
Finally I needed to be able to read and parse the RSS feed from the blog so that I could get the latest post so that we can shorten the link and and post it to Twitter. 

I needed another Python module for this and when i wen searching I found [Feedparser](https://github.com/kurtmckee/feedparser). Again the implementation goes like this:

{% highlight ruby %}
# Import Feedparser
import feedparser
# set feed to read
d = feedparser.parse('http://ki6bjv.com/feed.xml')
# set title from the top post in the feed
title =  d['entries'][0]['title']
# set the link from the top post in the feed
link  =  d['entries'][0]['link']
# print out the combine titel and link
print (title + '  ' + link)

{% endhighlight %}<br/>
So when I finally put all this together we get something like this:

{% highlight ruby %}

from twython import Twython, TwythonError
import feedparser
import requests

d = feedparser.parse('http://ki6bjv.com/feed.xml')

title =  d['entries'][0]['title']
link  =  d['entries'][0]['link']


APP_KEY = 'app key'
APP_SECRET = 'app secret'
OAUTH_TOKEN = 'oauth token'
OAUTH_TOKEN_SECRET = 'oauth secret token'


apiend    = 'https://api-ssl.bitly.com/v3/shorten?access_token=APITOKENGOESHERE&longUrl='
longlink  = link
getformat = '&format=txt'
url = (apiend + longlink + getformat)

twitter = Twython(APP_KEY, APP_SECRET, OAUTH_TOKEN, OAUTH_TOKEN_SECRET)

url = (apiend + longlink + getformat)

r = requests.get(url)

twitter.update_status(status = title + ' ' + r.text)
{% endhighlight %}<br/>
Couple of things are missing before I would consider this completely ready for production. For one there is no erroring handling at all. I should also be keeping track of the title of each blog post to make sure I am not spamming twitter with the same blog post over and over again. 

those are going to be my steps.....

***
