---
date: "2021-09-04"
layout: post
title: Websockets, Dealing with realtime updates for async API
tags: [engineering,websocket,networking,async,API,backend]
---


## Problem 
Recently I came across a scenario at work where I had to deal with real time updates of payment status in mobile which involved an asynchronous chain of events in the backend. Mobile would call the backend to initiate a payment request, backend would do basic validation - initialize the payment and return a token to the UI. Actual payment involved multiple API calls to some of the internal systems as well as third party systems. Especially the ones involving third party API could have response time upto a minute in worst cases. Now for the user who is eagerly waiting to see what happens with the payment it's obviously better to deliver that news (good or bad) as soon as possible.

### Existing Solution
- Polling
    + Mobile would call an endpoint in the backend to check the latest payment status (with the token obtained during the payment). This would happen on users intervention (via pull refresh on UI elements and via a way to refresh/check-status). Obviously this was not real time and it was used only as an adon to the flow
    + Existing approach at real time update made use of push notifications. App would listen for the push notification (which contained the payment details) and would display the status/info accordingly. Although we were aware of some of the shortcomings this approach had it started getting worse and harder to ignore as we started scaling further. Some common problems were:
        * Push Notifications generally have a low delivery rate. There are no strong SLAs around it
        * Users can choose to opt out of PN
        * For above 2 reasons, PNs are generally used for marketing campaigns in the industry (which didn't clearly fit for our real time requirement - criticality)
        * Delayed PNs would often create confusions too. (Eg: user would get sms/email with payment confirmation and the UI would still not update because PN hasn't been delivered yet)


### Alternatives Considered
- Polling/Long-Polling
    + Discarded as this approaches has heavy penalty on compute resources. Also questions like what duration should be considered for polling? would have to be tuned/experimented
- HTML5 SSE (Server Side Events)
    + This seemed like a viable approach. But this seemed more appropriate where you have a series of events that a server would like to send to client in the same context which was not the case with us. We just wanted to send a one time update stating whether it's a success or a failure. Further we wanted a generic solution that would work on mobile and web both.
- Realtime Firebase DB
    + We also considered updating the status in realtime firebase DB. But this again meant an introduction of third party dependency and we were already aware of frequent cases where operations involving firebase would fail. (eg: retrieving a token)


## Websockets to the rescue
Finally we chose to perform this using websockets.

[Websocket](https://datatracker.ietf.org/doc/html/rfc6455) is an entirely different protocol from HTTP. Generally an HTTP request involves client connecting to server, a client - server tcp connection establishment, server writing back to the client and the connection gets closed. At protocol level all the HTTP requests are stateless and are added with cookies and other headers to establish a context at the server level. If client needs another information it sends another request to the server. Websocket, on the contrary, works in a full duplex mode. 

- Client initiates a normal http request (It internally involves using an 'Upgrade header')
- Server validates the request and attempts to 'upgrade' it to a websocket connection (It responds with `101 Switching Protocol` response if succeeded and a normal `200 OK`(without protocol upgrade if failed) [more on protocol upgrade mechanism](https://developer.mozilla.org/en-US/docs/Web/HTTP/Protocol_upgrade_mechanism)
- Once the http connection is upgraded we have a full duplex tcp connection between client and server (server sends an ack to the client)
- Once the connection is upgraded the older http connection can no longer be used (it's 'hijacked' into tcp - a common pitfall while working with websockets, in our case some of the middlewares for logging were failing due to this which we rectified later.)


### Gluing asynchronous API response with the websocket
Application architecture involved an API container to which mobile/UI talked. API container would schedule/queue the payment job which would be picked up by the worker container. Since mobile would only talk with the API container we needed to communicate payment update from worker to API as soon as it happened. Any queueing/event-messaging system would do the job but since our architecture already made use of redis for scheduling we chose to use [redis pub sub](https://redis.io/topics/pubsub) for this communication.


#### Final Architecture

![websocket](https://sudipidus.github.io/resources/billbox-websocket.jpeg "websocket")

Technology wise since our app was written in golang we chose this library, [gorilla websocket](https://github.com/gorilla/websocket). Socket connections were authenticated by app token. We ran redis listener in go routines and on each redis message we picked corresponding socket connection (those are tagged with payment token) and closed it.

## Some Considerations

- AWS ALB Support
    + Since we hosted our apps on AWS we needed to confirm if AWS ALB had a native support for websocket. Turns out [ALB natively supports websocket upgrade.](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/load-balancer-listeners.html)
    
>
    "Application Load Balancers provide native support for WebSockets. You can upgrade an existing HTTP/1.1 connection into a WebSocket (ws or wss) connection by using an HTTP connection upgrade. When you upgrade, the TCP connection used for requests (to the load balancer as well as to the target) becomes a persistent WebSocket connection between the client and the target through the load balancer. You can use WebSockets with both HTTP and HTTPS listeners. The options that you choose for your listener apply to WebSocket connections as well as to HTTP traffic. For more information, see How the WebSocket Protocol Works in the Amazon CloudFront Developer Guide."
    

- Security
    + We used wss (websocket over HTTPS). HTTPS upgraded to WSS (encryption continues with the same certificate) (A quick peek at ALB access logs verified this fact)

- ALB allows a single connection to be in idle state only for a certain amount of time (default at 300 sec). We had to either PING to keep the connection alive or reinitiate the connection from mobile/UI.

- Since socket connections could be in open state for a long time and app containers could potentially restart,redeploy during that time we also did graceful shutdown where just before the container shutdown we closed all the socket connections gracefully. On this mobile/UI would attempt to establish a connection again after few seconds. (API have multiple instances behind a LB)
    
## Measuring Improvement
>If you can't measure it, you can't improve it.
- Peter Drucker

We log events for all the key actions that happen within the system. 2 main events which helped us to track the progress were:
 - payment event 
 - websocket status update event

By looking at the time lapse we were able to figure out how timely were the users getting payment update. On average it happened on tens of seconds, worst case being upto a minute. (limiting factor here was the response time by the third party APIs). We built aws cloudwatch metrics on top of it which would page us incase payment updates were happening after a big lag. Additionally we also started logging these events in Clevertap. 


