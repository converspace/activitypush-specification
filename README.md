# Activity Pingback Specification

A lightweight method for URI addressable resource owners to request and receive unsolicited ([JSON Activity Streams](http://activitystrea.ms/specs/json/1.0/) based)  notifications about remote (off-site) activities on their resources with an explicit goal of being easy to implement.


## Discovery

```
> GET /referenced-resource HTTP/1.1
> Host: source.host


< HTTP/1.1 200 OK
< Activity-Pingback: http://source.host/activity-pingback-receiver
```




## Notification

```
> POST /activity-pingback-receiver HTTP/1.1
> Host: source.host
> Activity-Pingback-HMAC: hmac_signature
> Activity-Pingback-Verify: http://remote.host/activity-pingback-verifier
>
> {JSON Activity Streams Payload}


< HTTP/1.1 202 Accepted
```

The `hmac_signature` of the `JSON Activity Streams Payload` is calculated by `remote.host` using an `algo` of its choice and its `secret`.


## Verification

```
> POST /activity-pingback-verifier HTTP/1.1
> Host: remote.host
> Activity-Pingback-HMAC: hmac_signature
>
> {JSON Activity Streams Payload}


< HTTP/1.1 200 OK
```

`remote.host` compares the received `hmac_signature` against the signature of the received `JSON Activity Streams Payload` calculated using the `algo` and `secret` it uses while sending notifications.


See also
--------
* [Pingback](http://www.hixie.ch/specs/pingback/pingback)
* [Trackback](http://archive.cweiske.de/trackback/trackback-1.2.html)
* [Salmon Protocol](http://salmon-protocol.googlecode.com/svn/trunk/draft-panzer-salmon-00.html)
* [Dialback Authentication](http://tools.ietf.org/html/draft-prodromou-dialback-00)
* [PubSubHubbub](https://code.google.com/p/pubsubhubbub/)
* [Activity Streams](http://activitystrea.ms/)
