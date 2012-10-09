# Activity Pingback Specification

A lightweight method for URI addressable resources to be __automatically notified__ about __remote (off-site) [activites](http://activitystrea.ms/)__ on them.

A sample flow with a like activity might go like this:

1. Alice's website aggregates posts from eveyone she follows (via [PubSubHubbub](https://code.google.com/p/pubsubhubbub/)).
2. Alice visits her website, notices a post by Bob (that he made on his website) and _likes_ it (on her website).
3. Alice's website notifies Bob's website about the (_like_) activity (using Activity Pingback).
4. Bob's website shows the activity on Bob's activity stream and with his original post.
5. Other users following Bob's website are notified about the activity (via [PubSubHubbub](https://code.google.com/p/pubsubhubbub/)).

## Discover Receiver Endpoint

```
> GET /object-of-the-activity HTTP/1.1
> Host: pingback.receiver


< HTTP/1.1 200 OK
< Link: <http://pingback.receiver/activity-pingback-endpoint>; rel="http://activitypingback.org/"
< ...
<
< <html>
< ...
< <link href="http://pingback.receiver/activity-pingback-endpoint" rel="http://activitypingback.org/" />
< ...
```




## Notify Receiver

```
> POST /activity-pingback-endpoint HTTP/1.1
> Host: pingback.receiver
> Content-Type: application/x-www-url-form-encoded
>
> actor=http://pingback.sender/actor/2&\
> activityid=http://pingback.sender/activity/42&\
> object=http://pingback.receiver/object-of-the-activity


< HTTP/1.1 202 Accepted
```

_Note: the "\" character is used here to indicate line wrapping in the request content and is not part of the content itself._

`pingback.receiver` should check that the `object` of the `activity` belongs to it.


## Discover Actor Endpoint

```
> GET /actor/2 HTTP/1.1
> Host: pingback.sender


< HTTP/1.1 200 OK
< Link: <http://pingback.sender/activity-pingback-endpoint>; rel="http://activitypingback.org/"
< ...
<
< <html>
< ...
< <link href="http://pingback.sender/activity-pingback-endpoint" rel="http://activitypingback.org/" />
< ...
```

## Get Activity from Actor Endpoint & Validate

```
> GET /activity-pingback-endpoint?actor=...&activityid=...&object=... HTTP/1.1
> Host: pingback.sender


< HTTP/1.1 200 OK
< Content-Type: application/stream+json
<
< {JSON Activity Streams}
```

`pingback.receiver` should check that the `actor` and the `object` of the `activity` are the same as the one it received in the notification.


## See also

* [Pingback](http://www.hixie.ch/specs/pingback/pingback)
* [Trackback](http://archive.cweiske.de/trackback/trackback-1.2.html)
* [RESTful Pingback](http://www.w3.org/wiki/Pingback)
* [Semantic Pingback](http://aksw.org/projects/semanticpingback)
* [Salmon Protocol](http://salmon-protocol.googlecode.com/svn/trunk/draft-panzer-salmon-00.html)
* [Dialback Authentication](http://tools.ietf.org/html/draft-prodromou-dialback-00)
* [PubSubHubbub](https://code.google.com/p/pubsubhubbub/)
* [Watch, trust, friend](http://markpasc.typepad.com/blog/2011/03/watch-trust-friend.html)
* [Private webhooks, hold the crypto?](http://markpasc.typepad.com/blog/2011/04/private-webhooks-hold-the-crypto.html)

## Let's collaborate
Feel free to [file an issue](https://github.com/converspace/activity-pingback/issues) if you have feedback/questions/suggestions.