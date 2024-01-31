---
layout: post
title: Monoliths Vs Microservices
tags: [monolith, microservice, architecture, software]
---




### Microservice is not the silver bullet

Software Industry is heavily trend-driven. People love going behind the trendy hypes

- microservices are cool
- containers are cool
- noSQL is cool
- this new language/framework is cool


#### what makes a good use case for microservice?

- functionality has very clear demarcation/boundary (the most important.. don't try to force split an already domain complex module)
- functionality needs independent scaling
- functionality needs lot of re-use (many components need it)
- functionality demands language agnosticity (rapid iterations, deployment)


#### what should you ensure?

- you have proper ci/cd set up for continuous build,test, deployment and release
- you have proper monitoring and visibility (centralized logging (eg ELK), application performance monitoring (eg Newrelic))
- you have proper deployment strategy (failover, rolling upgrade, provision to switch to previous version)
- you have proper communication between services (no cascading failures)


