twitter4s
=========

[![Build Status](https://travis-ci.org/DanielaSfregola/twitter4s.svg?branch=master)](https://travis-ci.org/DanielaSfregola/twitter4s) [![Coverage Status](https://img.shields.io/coveralls/DanielaSfregola/twitter4s.svg)](https://coveralls.io/r/DanielaSfregola/twitter4s?branch=master) [![License](http://img.shields.io/:license-Apache%202-red.svg)](http://www.apache.org/licenses/LICENSE-2.0.txt)

An asynchronous non-blocking Scala Twitter Client, implemented using spray and json4s.

Prerequisites
-------------
Scala 2.11.+ is supported.

- Go to http://apps.twitter.com/, login with your twitter account and register your application to get a consumer key and a consumer secret.
- Once the app has been created, generate a access key and access secret with the desired permission level.

Rate Limits
-----------
Be aware that the Twitter REST Api has rate limits specific to each endpoint. For more information, please have a look at the Twitter developers website [here](https://dev.twitter.com/rest/public/rate-limits).

Setup
-----
If you don't have it already, make sure you add the Maven Central as resolver in your SBT settings:

```scala
resolver += Resolver.sonatypeRepo("releases")
```

Also, you need to include the library as your dependency:
```scala
libraryDependencies ++= Seq(
  "com.danielasfregola" %% "twitter4s" % "1.1"
)
```

Usage
-----

Add your consumer and access token as either environment variables or as part of your configuration.
Twitter4s will look for the following environment variables:
```bash
export TWITTER_CONSUMER_TOKEN_KEY='my-consumer-key'
export TWITTER_CONSUMER_TOKEN_SECRET='my-consumer-secret'
export TWITTER_ACCESS_TOKEN_KEY='my-access-key'
export TWITTER_ACCESS_TOKEN_SECRET='my-access-secret'
```
You can also add them to your configuration file, usually called `application.conf`:
```scala
twitter {
  consumer {
    key = "my-consumer-key"
    secret = "my-consumer-secret"
  }
  access {
    key = "my-access-key"
    secret = "my-access-secret"
  }
}
```
These configurations will be automatically loaded when creating a twitter client, so all you have to do is to initialize your clients as following:
```scala
import com.danielasfregola.twitter4s.TwitterRestClient
import com.danielasfregola.twitter4s.TwitterStreamingClient

val restClient = TwitterRestClient()
val streamingClient = TwitterStreamingClient()
```

Alternatively, you can also specify your tokens directly when creating the client:
```scala
import com.danielasfregola.twitter4s.TwitterRestClient
import com.danielasfregola.twitter4s.TwitterStreamingClient
import com.danielasfregola.twitter4s.entities.{AccessToken, ConsumerToken}

val consumerToken = ConsumerToken(key = "my-consumer-key", secret = "my-consumer-secret")
val accessToken = AccessToken(key = "my-access-key", secret = "my-access-secret")  

val restClient = new TwitterRestClient(consumerToken, accessToken)
val streamingClient = new TwitterStreamingClient(consumerToken, accessToken)
```

Once you have instantiated your client you are ready to use it! :smile:

Twitter Streaming Client
-----------------------
[TwitterStreamingClient](http://danielasfregola.github.io/twitter4s/1.1/api/index.html#com.danielasfregola.twitter4s.TwitterStreamingClient) is the client to support stream connections offered by the Twitter Streaming Api.

You can initialize the client as follows:
```scala
import com.danielasfregola.twitter4s.TwitterStreamingClient

val client = TwitterStreamingClient()
```

There are three types of streams, each with different streaming message types: [Public Stream](https://github.com/DanielaSfregola/twitter4s#public-stream), [User Stream](https://github.com/DanielaSfregola/twitter4s#user-stream), [Site Stream](https://github.com/DanielaSfregola/twitter4s#site-stream).

Each stream requires a partial function that indicates how to process messages. If a message type is not specified, it is ignored. See the section of each stream for more information.

For example, you can create the following function to print the text of a tweet:
```scala
import com.danielasfregola.twitter4s.entities.Tweet
import com.danielasfregola.twitter4s.entities.streaming.StreamingMessage

def printTweetText: PartialFunction[StreamingMessage, Unit] = {
    case tweet: Tweet => println(tweet.text)
  }
```
All  you need to do is attach your processing function to the stream:
```scala
client.getStatusesSample(stall_warnings = true)(printTweetText)
```
...and you are done, happy days! :dancers:

Have a look at [TwitterProcessor](https://github.com/DanielaSfregola/twitter4s/blob/master/src/main/scala/com/danielasfregola/twitter4s/processors/TwitterProcessor.scala) for some predefined processing functions.

### Public Stream
Have a look at the complete scaladoc for the [Public Stream Client](http://danielasfregola.github.io/twitter4s/1.1/api/index.html#com.danielasfregola.twitter4s.http.clients.streaming.statuses.TwitterStatusClient).

#### Available streams
- getStatusesFilter
- getStatusesSample
- getStatusesFirehose

#### CommonStreamingMessage types:
- [Tweet](http://danielasfregola.github.io/twitter4s/1.1/api/index.html#com.danielasfregola.twitter4s.entities.Tweet)
- [DisconnectMessage](http://danielasfregola.github.io/twitter4s/1.1/api/index.html#com.danielasfregola.twitter4s.entities.streaming.common.DisconnectMessage)
- [LimitNotice](http://danielasfregola.github.io/twitter4s/1.1/api/index.html#com.danielasfregola.twitter4s.entities.streaming.common.LimitNotice)
- [LocationDeletionNotice](http://danielasfregola.github.io/twitter4s/1.1/api/index.html#com.danielasfregola.twitter4s.entities.streaming.common.LocationDeletionNotice)
- [StatusDeletionNotice](http://danielasfregola.github.io/twitter4s/1.1/api/index.html#com.danielasfregola.twitter4s.entities.streaming.common.StatusDeletionNotice)
- [StatusWithheldNotice](http://danielasfregola.github.io/twitter4s/1.1/api/index.html#com.danielasfregola.twitter4s.entities.streaming.common.StatusWithheldNotice)
- [UserWithheldNotice](http://danielasfregola.github.io/twitter4s/1.1/api/index.html#com.danielasfregola.twitter4s.entities.streaming.common.UserWithheldNotice)
- [WarningMessage](http://danielasfregola.github.io/twitter4s/1.1/api/index.html#com.danielasfregola.twitter4s.entities.streaming.common.WarningMessage)

### User Stream
Have a look at the complete scaladoc for the [User Stream Client](http://danielasfregola.github.io/twitter4s/1.1/api/index.html#com.danielasfregola.twitter4s.http.clients.streaming.users.TwitterUserClient).

#### Available streams
- getUserEvents

#### UserStreamingMessage types:
- All the `CommonStreamingMessage`s -- see the [Public Stream Section](https://github.com/DanielaSfregola/twitter4s#public-stream)
- [FriendsLists and FriendsListsStringified](http://danielasfregola.github.io/twitter4s/1.1/api/index.html#com.danielasfregola.twitter4s.entities.streaming.user.FriendsLists)
- [SimpleEvent](http://danielasfregola.github.io/twitter4s/1.1/api/index.html#com.danielasfregola.twitter4s.entities.streaming.user.SimpleEvent)
- [TweetEvent](http://danielasfregola.github.io/twitter4s/1.1/api/index.html#com.danielasfregola.twitter4s.entities.streaming.user.TweetEvent)
- [TwitterListEvent](http://danielasfregola.github.io/twitter4s/1.1/api/index.html#com.danielasfregola.twitter4s.entities.streaming.user.TwitterListEvent)

### Site Stream
Have a look at the complete scaladoc for the [Site Stream Client](http://danielasfregola.github.io/twitter4s/1.1/api/index.html#com.danielasfregola.twitter4s.http.clients.streaming.sites.TwitterSiteClient).

#### Available streams
- getSiteEvents

#### SiteStreamingMessage types:
- All the `CommonStreamingMessage`s -- see the [Public Stream Section](https://github.com/DanielaSfregola/twitter4s#public-stream)
- [ControlMessage](http://danielasfregola.github.io/twitter4s/1.1/api/index.html#com.danielasfregola.twitter4s.entities.streaming.site.ControlMessage)
- [UserEnvelopTweet and UserEnvelopTweetStringified](http://danielasfregola.github.io/twitter4s/1.1/api/index.html#com.danielasfregola.twitter4s.entities.streaming.site.UserEnvelopTweet)
- [UserEnvelopDirectMessage and UserEnvelopDirectMessageStringified](http://danielasfregola.github.io/twitter4s/1.1/api/index.html#com.danielasfregola.twitter4s.entities.streaming.site.UserEnvelopDirectMessage)
- [UserEnvelopSimpleEvent and UserEnvelopSimpleEventStringified](http://danielasfregola.github.io/twitter4s/1.1/api/index.html#com.danielasfregola.twitter4s.entities.streaming.site.UserEnvelopSimpleEvent)
- [UserEnvelopTweetEvent and UserEnvelopTweetEventStringified](http://danielasfregola.github.io/twitter4s/1.1/api/index.html#com.danielasfregola.twitter4s.entities.streaming.site.UserEnvelopTweetEvent)
- [UserEnvelopTwitterListEvent and UserEnvelopTwitterListEventStringified](http://danielasfregola.github.io/twitter4s/1.1/api/index.html#com.danielasfregola.twitter4s.entities.streaming.site.UserEnvelopTwitterListEvent)
- [UserEnvelopFriendsLists and UserEnvelopFriendsListsEventStringified](http://danielasfregola.github.io/twitter4s/1.1/api/index.html#com.danielasfregola.twitter4s.entities.streaming.site.UserEnvelopFriendsLists)
- [UserEnvelopWarningMessage and UserEnvelopWarningMessageStringified](http://danielasfregola.github.io/twitter4s/1.1/api/index.html#com.danielasfregola.twitter4s.entities.streaming.site.UserEnvelopWarningMessage)

### Documentation
The complete scaladoc with all the available streams for the `TwitterStreamingClient` can be found [here](http://danielasfregola.github.io/twitter4s/1.1/api/index.html#com.danielasfregola.twitter4s.TwitterStreamingClient).


Twitter REST Client
-------------------

[TwitterRestClient](http://danielasfregola.github.io/twitter4s/1.1/api/index.html#com.danielasfregola.twitter4s.TwitterRestClient) is the client for the REST endpoints offered by the Twitter REST Api.

Once you have configured your consumer and access token, you can initialize an instance of `TwitterRestClient` as follows:
```
import com.danielasfregola.twitter4s.TwitterRestClient

val client = TwitterRestClient()
```

For example, you can get the home timeline of the authenticated user:
```scala
client.getHomeTimeline()
```

or you can get the timeline of a specific user:
```scala
client.getUserTimelineForUser("DanielaSfregola")
```

You can also update your tweet status:
```scala
client.tweet(status = "Test")
```
Asynchronous upload of images or short videos is also supported:
```scala
for {
  upload <- client.uploadMediaFromPath("/path/to/file.png")
  tweet <- client.tweet(status = "Test with media", media_ids = Seq(upload.media_id))
} yield tweet
```

### Documentation
The complete scaladoc with all the available functionalities for the `TwitterRestClient` can be found [here](http://danielasfregola.github.io/twitter4s/1.1/api/index.html#com.danielasfregola.twitter4s.TwitterRestClient).

[TwitterRestClient](http://danielasfregola.github.io/twitter4s/1.1/api/index.html#com.danielasfregola.twitter4s.TwitterRestClient) is composed by several traits. A list of the supported resources is following:
- [account](http://danielasfregola.github.io/twitter4s/1.1/api/index.html#com.danielasfregola.twitter4s.http.clients.rest.account.TwitterAccountClient)
- [application](http://danielasfregola.github.io/twitter4s/1.1/api/index.html#com.danielasfregola.twitter4s.http.clients.rest.application.TwitterApplicationClient)
- [blocks](http://danielasfregola.github.io/twitter4s/1.1/api/index.html#com.danielasfregola.twitter4s.http.clients.rest.blocks.TwitterBlockClient)
- [direct_messages](http://danielasfregola.github.io/twitter4s/1.1/api/index.html#com.danielasfregola.twitter4s.http.clients.rest.directmessages.TwitterDirectMessageClient)
- [favorites](http://danielasfregola.github.io/twitter4s/1.1/api/index.html#com.danielasfregola.twitter4s.http.clients.rest.favorites.TwitterFavoriteClient)
- [followers](http://danielasfregola.github.io/twitter4s/1.1/api/index.html#com.danielasfregola.twitter4s.http.clients.rest.followers.TwitterFollowerClient)
- [friends](http://danielasfregola.github.io/twitter4s/1.1/api/index.html#com.danielasfregola.twitter4s.http.clients.rest.friends.TwitterFriendClient)
- [friendships](http://danielasfregola.github.io/twitter4s/1.1/api/index.html#com.danielasfregola.twitter4s.http.clients.rest.friendships.TwitterFriendshipClient)
- [geo](http://danielasfregola.github.io/twitter4s/1.1/api/index.html#com.danielasfregola.twitter4s.http.clients.rest.geo.TwitterGeoClient)
- [help](http://danielasfregola.github.io/twitter4s/1.1/api/index.html#com.danielasfregola.twitter4s.http.clients.rest.help.TwitterHelpClient)
- [lists](http://danielasfregola.github.io/twitter4s/1.1/api/index.html#com.danielasfregola.twitter4s.http.clients.rest.lists.TwitterListClient)
- [mutes](http://danielasfregola.github.io/twitter4s/1.1/api/index.html#com.danielasfregola.twitter4s.http.clients.rest.mutes.TwitterMuteClient)
- [saved_searches](http://danielasfregola.github.io/twitter4s/1.1/api/index.html#com.danielasfregola.twitter4s.http.clients.rest.savedsearches.TwitterSavedSearchClient)
- [searches](http://danielasfregola.github.io/twitter4s/1.1/api/index.html#com.danielasfregola.twitter4s.http.clients.rest.search.TwitterSearchClient)
- [statuses](http://danielasfregola.github.io/twitter4s/1.1/api/index.html#com.danielasfregola.twitter4s.http.clients.rest.statuses.TwitterStatusClient)
- [suggestions](http://danielasfregola.github.io/twitter4s/1.1/api/index.html#com.danielasfregola.twitter4s.http.clients.rest.suggestions.TwitterSuggestionClient)
- [users](http://danielasfregola.github.io/twitter4s/1.1/api/index.html#com.danielasfregola.twitter4s.http.clients.rest.users.TwitterUserClient)
- [trends](http://danielasfregola.github.io/twitter4s/1.1/api/index.html#com.danielasfregola.twitter4s.http.clients.rest.trends.TwitterTrendClient)
- [media](http://danielasfregola.github.io/twitter4s/1.1/api/index.html#com.danielasfregola.twitter4s.http.clients.rest.media.TwitterMediaClient)

Proxy Support
-------------
If needed, you can redefine the domain used for each of the twitter api by overriding the following settings in your configuration file:
```scala
twitter {

  rest {
    api = "https://api.twitter.com"
    media = "https://upload.twitter.com"
  }

  streaming {
    public = "https://stream.twitter.com"
    user = "https://userstream.twitter.com"
    site = "https://sitestream.twitter.com"
  }
}
```

Examples
--------
Have a look at the repository [twitter4s-demo](https://github.com/DanielaSfregola/twitter4s-demo) for more examples on how to use `twitter4s`.

Coming up Features
---------------
- OAuth1 support
- Query support
- Site streaming extended support
- Support for dump to file
- Upgrade to Akka Http
- ...

Contributions and feature requests are always welcome!

Snapshot Versions
-----------------
To use a snapshot version of this library, make sure you have the resolver for maven central (snapshot repositories) in your SBT settings:
```scala
resolver += Resolver.sonatypeRepo("snapshots")
```

Then, add the library as your dependency:
```scala
libraryDependencies ++= Seq(
  "com.danielasfregola" %% "twitter4s" % "1.2-SNAPSHOT"
)
```