# Activity Pingback Specification

A lightweight method for URI addressable resource owners to request and receive unsolicited notifications about remote (off-site) activities on their resources with an explicit goal of being easy to implement.

It aims to be the natural successor to [Pingback](http://www.hixie.ch/specs/pingback/pingback)/[Trackback](http://archive.cweiske.de/trackback/trackback-1.2.html) and uses [Activity Streams](http://activitystrea.ms/).




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
> {JSON Activity Stream Payload}


< HTTP/1.1 202 Accepted
```

The `hmac_signature` of the `JSON Activity Stream Payload` is calculated using remote.host's private `secret`.


## Verification

```
> POST /activity-pingback-verifier HTTP/1.1
> Host: remote.host
> Activity-Pingback-HMAC: hmac_signature
>
> {JSON Activity Stream Payload}


< HTTP/1.1 200 OK
```


See also
--------
* [Salmon Protocol](http://salmon-protocol.googlecode.com/svn/trunk/draft-panzer-salmon-00.html)
* [Dialback Authentication](http://tools.ietf.org/html/draft-prodromou-dialback-00)
