Activity Pingback Specification
===============================

A lightweight method for URI addressable resource owners to request and receive unsolicited notifications about remote (off-site) activities on their resources with an explicit goal of being easy to implement.

Discovery
---------

```
Request:
GET /referenced-resource HTTP/1.1
Host: source.example

Response:
HTTP/1.1 200 OK
X-ActivityPingback-URI: http://source.example/activity-pingback-receiver
```
Optionally a the AcitivityPingback-URI could also be provided as a link element that satisfies the following regex:
`<link rel="activitypingback-uri" href="([^"]+)" ?/?>`

Activity Pingback Notification
------------------------------

```
Request:
POST /activity-pingback-receiver
Host: source.example
X-ActivityPingback-Token: 'q1w2e3r4t5y6'
X-ActivityPingback-VerficationURI: http://remote.example/activity-pingback-verifier

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