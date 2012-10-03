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

1. Did __you__ (the sender mentioned in the request) send __this activity__  to __me__?
 * The `verification` step should answer the __you__ part. Calculating the `hmac_sig` over the `payload` should answer the __this activity__ part. Calculating the `hmac_sig` over the `receivers activity pingback endpoint` should answer the __me__ part.
2. Is __this activity__ about __my resource__?
  * Checking the _object_ in the activity should answer this.
3. How do I know if I'm actually talking with the intended sender?
  * Given the _unsolicited nature of the notification_, an `HTTPS` activity pingback endpoint by the __sender__ might be the only answer.
4. Is this activity relevant to the conversation or just spam?
  * __Moderation of first time senders__ and then _whitelisting/backlisting_ them should help. Given the use case, the frequency of this might be similar to accepting friend requests.

### Questions senders should be able to reliably answer:
* How do I know my notification is actually reaching the intended receiver?
 * Given the _unsolicited nature of the notification_, an `HTTPS` activity pingback endpoint by the __receiver__ might be the only answer.
* Can someone else send this message on my behalf without me knowing about it?
 * Calculating the `hmac_sig` over a `timestamp` and `nounce` should prevent this.

## TODO

* Consider using [Dialback Authentication](http://tools.ietf.org/html/draft-prodromou-dialback-00) if [evanp/dialback#2](https://github.com/evanp/dialback/issues/2) is fixed.
* Currently the notification process is asynchronous. Consider making it synchronous?


## See also

* [Pingback](http://www.hixie.ch/specs/pingback/pingback)
* [Trackback](http://archive.cweiske.de/trackback/trackback-1.2.html)
* [RESTful](http://bblfish.net/tmp/2011/05/09/) [Pings](http://www.w3.org/community/rww/wiki/Pingback)
* [Salmon Protocol](http://salmon-protocol.googlecode.com/svn/trunk/draft-panzer-salmon-00.html)
* [Dialback Authentication](http://tools.ietf.org/html/draft-prodromou-dialback-00)
* [PubSubHubbub](https://code.google.com/p/pubsubhubbub/)
* [Activity Streams](http://activitystrea.ms/)
