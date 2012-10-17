# ActivityPush Specification

A _lightweight_ method for __URI addressable resources__ to be __automatically notified__ about __remote (off-site) [activites](http://activitystrea.ms/)__ on them.

A crypto-free alternative to the [Salmon Protocol](http://www.salmon-protocol.org/) for public activites.

## Imagine

1. Alice's website aggregates posts from eveyone she follows (via [PubSubHubbub](https://code.google.com/p/pubsubhubbub/)).
2. Alice visits her website, notices a post by Bob (that he made on his website) and _likes_ it (on her website).
3. Alice's website notifies Bob's website about the (_like_) activity (using ActivityPush).
4. Bob's website shows the activity on Bob's activity stream and with his original post.
5. Other users following Bob's website are notified about the activity (via [PubSubHubbub](https://code.google.com/p/pubsubhubbub/)).

## Protocol Flow

### Sender discovers Receiver Endpoint

```
> GET /bob/post/2 HTTP/1.1
> Host: bobs.host


< HTTP/1.1 200 OK
< Link: <http://bobs.host/activitypush-endpoint>; rel="http://activitypush.org/"
< ...
<
< <html>
< ...
< <link href="http://bobs.host/activitypush-endpoint" rel="http://activitypush.org/" />
< ...
```




### Sender Notifies Receiver

```
> POST /activitypush-endpoint HTTP/1.1
> Host: bobs.host
> Content-Type: application/x-www-url-form-encoded
>
> actor=http://alices.host/alice&\
> activityid=http://alices.host/alice/activity/42&\
> object=http://bobs.host/bob/post/2


< HTTP/1.1 202 Accepted
```

_Note: the "\" character is used here to indicate line wrapping in the request content and is not part of the content itself._

`bobs.host` should check that the `object` of the `activity` belongs to it.


### Receiver discovers Actor Endpoint

```
> GET /alice HTTP/1.1
> Host: alices.host


< HTTP/1.1 200 OK
< Link: <http://alices.host/activitypush-endpoint>; rel="http://activitypush.org/"
< ...
<
< <html>
< ...
< <link href="http://alices.host/activitypush-endpoint" rel="http://activitypush.org/" />
< ...
```

### Receiver gets Activity from Actor Endpoint & Validates it

```
> GET /activitypush-endpoint?actor=...&activityid=...&object=... HTTP/1.1
> Host: alices.host


< HTTP/1.1 200 OK
< Content-Type: application/stream+json
<
< {JSON Activity Streams}
```

`bobs.host` should check that the `actor` and the `object` of the `activity` are the same as the one it received in the notification.

ActivityPush is agnostic to how Activities are represented as long as it has an `actor` URL, a `verb` and an `object` URL. For example, instead of the `application/stream+json`, 'alices.host` could have returned an HTML representation containing activity [microformats](http://microformats.org/wiki/activity-streams) or RDFa.


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
Feel free to [file an issue](https://github.com/converspace/activitypush/issues) if you have feedback/questions/suggestions.