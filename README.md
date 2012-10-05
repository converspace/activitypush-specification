# Activity Pingback Specification

A lightweight method for URI addressable resource owners to request and receive unsolicited ([JSON Activity Streams](http://activitystrea.ms/specs/json/1.0/) based)  notifications about remote (off-site) activities on their resources with an explicit goal of being easy to implement.


## Step 1: Discover

```
> GET /referenced-resource HTTP/1.1
> Host: pingback.receiver


< HTTP/1.1 200 OK
< Link: <http://pingback.receiver/activity-pingback-endpoint>; rel="http://activitypingback.org/"
```




## Step 2: Notify

```
> POST /activity-pingback-endpoint HTTP/1.1
> Host: pingback.receiver
> Content-Type: application/x-www-url-form-encoded
> Activity-Pingback: from="http://pingback.sender/activity-pingback-endpoint",
>
> source=http://pingback.sender/activity/42&\
> target=http://pingback.receiver/referenced-resource


< HTTP/1.1 202 Accepted
```

_Note: the "\" character is used here to indicate line wrapping in the request content and is not part of the content itself._




## Step 3: Verify & retrieve Activity

```
> GET /activity/42 HTTP/1.1
> Host: pingback.sender


< HTTP/1.1 200 OK
< Content-Type: application/json
<
< {JSON Activity Streams}
```

`pingback.receiver` needs to figure out the `activity` based on the `content-type` (if it's a HTML page look for Activity Streams microformat, if it's a JSON Activity Stream just read it).


## See also

* [Pingback](http://www.hixie.ch/specs/pingback/pingback)
* [Trackback](http://archive.cweiske.de/trackback/trackback-1.2.html)
* [RESTful Pingback](http://www.w3.org/wiki/Pingback)
* [Semantic Pingback](http://aksw.org/projects/semanticpingback)
* [Salmon Protocol](http://salmon-protocol.googlecode.com/svn/trunk/draft-panzer-salmon-00.html)
* [Dialback Authentication](http://tools.ietf.org/html/draft-prodromou-dialback-00)
* [PubSubHubbub](https://code.google.com/p/pubsubhubbub/)
* [Activity Streams](http://activitystrea.ms/)
* [Watch, trust, friend](http://markpasc.typepad.com/blog/2011/03/watch-trust-friend.html)
* [Private webhooks, hold the crypto?](http://markpasc.typepad.com/blog/2011/04/private-webhooks-hold-the-crypto.html)

## Let's collaborate
Feel free to [file an issue](https://github.com/converspace/activity-pingback/issues) if you have feedback/questions/suggestions.