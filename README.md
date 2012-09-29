Activity Pingback Specification
===============================

A lightweight method for URI addressable resource owners to request and receive unsolicited notifications about remote (off-site) activities on their resources with an explicit goal of being easy to implement.

It aims to be the natural successor to [Pingback](http://www.hixie.ch/specs/pingback/pingback)/[Trackback](http://archive.cweiske.de/trackback/trackback-1.2.html) and uses [Activity Streams](http://activitystrea.ms/).

Discovery
---------

```
Request:
GET /referenced-resource HTTP/1.1
Host: source.example

Response:
HTTP/1.1 200 OK
ActivityPingback-URI: http://source.example/activity-pingback-receiver
```
Optionally the AcitivityPingback-URI could also be provided as a link element that satisfies the following regex:
`<link rel="activitypingback-uri" href="([^"]+)" ?/?>`

Activity Pingback Notification
------------------------------

```
Request:
POST /activity-pingback-receiver
Host: source.example
ActivityPingback-Token: 'q1w2e3r4t5y6'
ActivityPingback-VerficationURI: http://remote.example/activity-pingback-verifier

<JSON Activity Stream Payload>


Response:
HTTP/1.1 202 Accepted
```

Activity Pingback Verification
------------------------------
```
Request:
POST /activity-pingback-verifier HTTP/1.1
Host: remote.example 
Content-Type: application/x-www-form-urlencoded; charset=utf-8

token=q1w2e3r4t5y6&URI=http://source.example/referenced-resource


Response:
HTTP/1.1 200 OK
```


See also
--------
* [Salmon Protocol](http://salmon-protocol.googlecode.com/svn/trunk/draft-panzer-salmon-00.html)
* [Dialback Authentication](http://tools.ietf.org/html/draft-prodromou-dialback-00)
