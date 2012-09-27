Activity Pingback
=================

A lightweight method for URI addressable resource owners to request and receive unsolicited notifications about remote/distributed activities on their resources.


* This notification will be kinda like a Pingback post request to the resources ActivityURI (specified via a HTTP header or HTML Link element) with an ActivityStream Payload and an auth token + TokenVerifcationURI in the header.
* The Pingback receiver (ActivityURI) will make a POST request to the TokenVerifcationURI along with the token to confirm that the pingback originated from the said TokenVerifcationURI.
* This Pingback like notification system could also be used for private/group messaging in the future. 