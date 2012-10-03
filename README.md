# Activity Pingback Specification

A lightweight method for URI addressable resource owners to request and receive unsolicited ([JSON Activity Streams](http://activitystrea.ms/specs/json/1.0/) based)  notifications about remote (off-site) activities on their resources with an explicit goal of being easy to implement.


## Discovery

```
> GET /referenced-resource HTTP/1.1
> Host: notification.receiver


< HTTP/1.1 200 OK
< Link: <http://notification.receiver/activity-pingback-endpoint>; rel="http://activitypingback.org/"
```




## Notification

```
> POST /activity-pingback-receiver HTTP/1.1
> Host: notification.receiver
> Activity-Pingback: uri="http://notification.sender/activity-pingback-endpoint",
>                    timestamp="1336363200",
>                    nonce="dj83hs9s",
>                    hmac_sig="bhCQXTVyfj5cmA9uKkPFx1zeOXM="
>
> {JSON Activity Streams Payload}


< HTTP/1.1 202 Accepted
```

The `hmac_sig` is calculated by `notification.sender` over (`http://notification.receiver/activity-pingback-endpoint` + `timestamp` + `nounce` + `JSON Activity Streams Payload`) using an `algo` of its choice and its `secret` as key.


## Verification

```
> POST /activity-pingback-endpoint HTTP/1.1
> Host: notification.sender
> Activity-Pingback-Verify: uri="http://notification.receiver/activity-pingback-endpoint",
>                           timestamp="1336363200",
>                           nonce="dj83hs9s",
>                           hmac_sig="bhCQXTVyfj5cmA9uKkPFx1zeOXM="
>
> {JSON Activity Streams Payload}


< HTTP/1.1 200 OK
```

`notification.sender` compares the received `hmac_signature` against the one calculated over the received (`uri` + `timestamp` + `nouce` + `JSON Activity Streams Payload`) using the `algo` and `secret` it uses while sending notifications.


## Security and Spam


### Questions receivers should be able to reliably answer:

* Did __you__ (the sender mentioned in the request) send __this activity__  to __me__?
* Is __this activity__ about __my resource__?
  * Check the _object_ in the activity?
* How do I know if I'm actually taking with the intended sender?
  * HTTPS?
* Is this activity relevant to the conversation or just spam?
  * Recommend moderation of first time senders and then maually whitelist/backlist them? Given the use case the frequency of this might be similar to accepting friend requests.

### Questions senders should be able to reliably answer:
* How do I know my notification is actually reaching the intended receiver?
  * HTTPS?
* Can someone else send this message on my behalf without me knowing about it?


## TODO

* Consider using [Dialback Authentication](http://tools.ietf.org/html/draft-prodromou-dialback-00) if [e14n/pump.io#144](https://github.com/e14n/pump.io/issues/144) is fixed.


## See also

* [Pingback](http://www.hixie.ch/specs/pingback/pingback)
* [Trackback](http://archive.cweiske.de/trackback/trackback-1.2.html)
* [RESTful](http://bblfish.net/tmp/2011/05/09/) [Pings](http://www.w3.org/community/rww/wiki/Pingback)
* [Salmon Protocol](http://salmon-protocol.googlecode.com/svn/trunk/draft-panzer-salmon-00.html)
* [Dialback Authentication](http://tools.ietf.org/html/draft-prodromou-dialback-00)
* [PubSubHubbub](https://code.google.com/p/pubsubhubbub/)
* [Activity Streams](http://activitystrea.ms/)
