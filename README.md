# snscrape
snscrape is a scraper for social networking services (SNS). It scrapes things like user profiles, hashtags, or searches and returns the discovered items, e.g. the relevant posts.

This is a clone of https://github.com/JustAnotherArchivist/snscrape but it is designed to work for Python under 3.8 and fixed some errors. It's under development, use with your responsibilities. If you have Python >= 3.8 you'd better use the main project created by JustAnotherArchivist.

The following services are currently supported:

* Facebook: user profiles, groups, and communities (aka visitor posts)
* Instagram: user profiles, hashtags, and locations
* Mastodon: user profiles and toots (single or thread)
* Reddit: users, subreddits, and searches (via Pushshift)
* Telegram: channels
* Twitter: users, user profiles, hashtags, searches, tweets (single or surrounding thread), list posts, and trends
* VKontakte: user profiles
* Weibo (Sina Weibo): user profiles

## Requirements
snscrape requires Python 3.6 or higher.

Note that one of the dependencies, lxml, also requires libxml2 and libxslt to be installed.

If you want to use the development version:

    pip3 install git+https://github.com/tobe93gf/snscrape.git
    
or

    pip install git+https://github.com/tobe93gf/snscrape.git

## Usage
### CLI
The generic syntax of snscrape's CLI is:

    snscrape [GLOBAL-OPTIONS] SCRAPER-NAME [SCRAPER-OPTIONS] [SCRAPER-ARGUMENTS...]

`snscrape --help` and `snscrape SCRAPER-NAME --help` provide details on the options and arguments. `snscrape --help` also lists all available scrapers.

The default output of the CLI is the URL of each result.

Some noteworthy global options are:

* `--jsonl` to get output as JSONL. This includes all information extracted by snscrape (e.g. message content, datetime, images; details vary by scraper).
* `--max-results NUMBER` to only return the first `NUMBER` results.
* `--with-entity` to get an item on the entity being scraped, e.g. the user or channel. This is not supported on all scrapers. (You can use this together with `--max-results 0` to only fetch the entity info.)

### Twitter
Attributes Available Through snscrape Tweet Object: Attribute description left blank if purpose is unknown
* url: Permalink pointing to tweet location 
* date: Date tweet was created 
* content: Text content of tweet 
* renderedContent: Appears to also be text content of tweet 
* id: ld of tweet 
* user: User object containing the following data: username, displayname, id, description, descriptionUrls, verified, created, followersCount, friendsCount,
statusesCount, favouritesCount, listed Count, mediaCount, location, protected, linkuri, profileImageUrl, profileBannerUrl • outlinks • tcooutlinks · replyCount: Count of replies · retweetCount: Count of retweets 
* likeCount: Count of likes 
* quoteCount: Count of users that quoted the tweet and replied 
* conversationld: Appears to be the same as tweetid 
* lang: Machine generated, assumed language of tweet 
* source: Where tweet was posted from, ex: IPhone, Android, etc. 
* media: Media object, containing previewUrl, fullUrl, and type · retweeted Tweet: If is a retweet, id of original tweet 
* quoted Tweet: If is a quoted tweet, id of original tweet • mentionedUsers: User objects of any mentioned user in tweet


#### Examples
Collect all tweets by Jason Scott (@textfiles):

    snscrape twitter-user textfiles

It's usually useful to redirect the output to a file for further processing, e.g. in bash using the filename `twitter-@textfiles`:

```bash
snscrape twitter-user textfiles >twitter-@textfiles
```

To get the latest 100 tweets with the hashtag #archiveteam:

    snscrape --max-results 100 twitter-hashtag archiveteam

#### Python Examples
Collect all tweets by jack (@jack):

    # importing libraries and packages
    import snscrape.modules.twitter as sntwitter
    import pandas

    # Creating list to append tweet data 
    tweets_list = []

    # Using TwitterSearchScraper to scrape data and append tweets to list
    for i,tweet in enumerate(sntwitter.TwitterSearchScraper('from:jack').get_items()): #declare a username 
        tweets_list.append([tweet.date, tweet.id, tweet.content, tweet.user.username]) #declare the attributes to be returned
        if i>1000: #number of tweets you want to scrape
            break
    
    # Creating a dataframe from the tweets list above 
    tweets_df1 = pd.DataFrame(tweets_list, columns=['Datetime', 'Tweet Id', 'Text', 'Username'])



Using the code below, we are scraping 100 tweets between January 1, 2021, and May 31, 2021, with the keywords ‘COVID’, then pull attributes DateTime, tweet id, text, and username from the tweet object.

    import snscrape.modules.twitter as sntwitter
    import pandas

    # Creating list to append tweet data to
    tweets_list = []

    # Using TwitterSearchScraper to scrape data and append tweets to list
    for i,tweet in enumerate(sntwitter.TwitterSearchScraper('COVID since:2021-01-01 until:2021-05-31').get_items()):
        if i>5000:
            break
        tweets_list.append([tweet.date, tweet.id, tweet.content, tweet.user.username])
    
    # Creating a dataframe from the tweets list above
    tweets_list = pd.DataFrame(tweets_list, columns=['Datetime', 'Tweet Id', 'Text', 'Username'])


## Issue reporting
If you discover an issue with snscrape, please report it at <https://github.com/tobe93gf/snscrape/issues>. If possible please run snscrape with `-vv` and `--dump-locals` and include the log output as well as the dump files referenced in the log in the issue. Note that the files may contain sensitive information in some cases and could potentially be used to identify you (e.g. if the service includes your IP address in its response). If you prefer to arrange a file transfer privately, just mention that in the issue.

## License
This program is free software: you can redistribute it and/or modify it under the terms of the GNU General Public License as published by the Free Software Foundation, either version 3 of the License, or (at your option) any later version.

This program is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the GNU General Public License for more details.

You should have received a copy of the GNU General Public License along with this program.  If not, see <https://www.gnu.org/licenses/>.
