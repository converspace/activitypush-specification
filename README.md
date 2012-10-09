# Activity Pingback Specification

A lightweight method for URI addressable resource owners to request and receive __unsolicited notifications__ about __remote (off-site) activities__ on their resources with an explicit goal of being easy to implement. Activites are represented using [Activity Streams](http://activitystrea.ms/).


## Step 1: Discover Receiver Endpoint

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




## Step 2: Notify Receiver

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


## Step 3: Discover Actor Endpoint

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

## Step 4: Get Activity from Actor Endpoint & Validate

```
> GET /activity-pingback-endpoint?actor=...&activityid=...&object=... HTTP/1.1
> Host: pingback.sender


< HTTP/1.1 200 OK
< Content-Type: application/json
<
< {JSON Activity Streams}
```

`pingback.receiver` should check that the `object` of the `activity` is valid.


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