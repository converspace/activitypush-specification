# Activity Pingback Specification

A lightweight method for URI addressable resource owners to request and receive unsolicited ([JSON Activity Streams](http://activitystrea.ms/specs/json/1.0/) based)  notifications about remote (off-site) activities on their resources with an explicit goal of being easy to implement.


## Step 1: Discover

```
> GET /referenced-resource HTTP/1.1
> Host: notification.receiver


< HTTP/1.1 200 OK
< Link: <http://notification.receiver/activity-pingback-endpoint>; rel="http://activitypingback.org/"
```




## Step 2: Notify

```
> POST /activity-pingback-endpoint HTTP/1.1
> Host: notification.receiver
> Activity-Pingback: from="http://notification.sender/activity-pingback-endpoint",
>                    timestamp="1336363200",
>                    nonce="dj83hs9s",
>                    payload_hash='1f3870be274f6c49b3e31a0c6728957f',
>                    request_hmac="bhCQXTVyfj5cmA9uKkPFx1zeOXM="
>
> {JSON Activity Streams Payload}


< HTTP/1.1 202 Accepted
```
`payload_hash` is the MD5 hash of the `JSON Activity Streams Payload`. `request_hmac` is the [HMAC](http://en.wikipedia.org/wiki/HMAC) over (`http://notification.receiver/activity-pingback-endpoint` + `timestamp` + `nonce` + `payload_hash`) using an `algo` of its choice and its `secret` as key.


## Step 3: Verify

```
> POST /activity-pingback-endpoint HTTP/1.1
> Host: notification.sender
> Content-Type: application/x-www-url-form-encoded
>
> to=http://notification.receiver/activity-pingback-endpoint&\
> timestamp=1336363200&\
> nonce=dj83hs9s&\
> payload_hash=1f3870be274f6c49b3e31a0c6728957f&\
> request_hmac=bhCQXTVyfj5cmA9uKkPFx1zeOXM=



< HTTP/1.1 200 OK
```
*Note: the "\" character is used here to indicate line wrapping in the request content and is not part of the content itself.*

`notification.sender` compares the received `request_hmac` against the one calculated over the received (`to` + `timestamp` + `nonce` + `payload_hash`) using the `algo` and `secret` it uses while sending notifications.


## Security and Spam


### Questions receivers should be able to reliably answer:

1. Did __you__ (the sender mentioned in the request) send __this activity__  to __me__?
 * The `verification` step should answer the __you__ part. Calculating the `request_hmac` over the `payload_hash` should answer the __this activity__ part. Calculating the `request_hmac` over `to` should answer the __me__ part.
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
 * Calculating the `request_hmac` over a `timestamp` and `nonce` should prevent this.

## TODO

* Consider using [Dialback Authentication](http://tools.ietf.org/html/draft-prodromou-dialback-00) if [evanp/dialback#2](https://github.com/evanp/dialback/issues/2) and [evanp/dialback#7](https://github.com/evanp/dialback/issues/7) are fixed.
* Currently the notification process is asynchronous. Consider making it synchronous?


## See also

* [Pingback](http://www.hixie.ch/specs/pingback/pingback)
* [Trackback](http://archive.cweiske.de/trackback/trackback-1.2.html)
* [RESTful](http://bblfish.net/tmp/2011/05/09/) [Pings](http://www.w3.org/community/rww/wiki/Pingback)
* [Salmon Protocol](http://salmon-protocol.googlecode.com/svn/trunk/draft-panzer-salmon-00.html)
* [Dialback Authentication](http://tools.ietf.org/html/draft-prodromou-dialback-00)
* [PubSubHubbub](https://code.google.com/p/pubsubhubbub/)
* [Activity Streams](http://activitystrea.ms/)

## Contribute
* Feel free to [file an issue](https://github.com/converspace/activity-pingback/issues) if you have feedback/questions/suggestions.