---
layout: post
title: Geo Routing (Craigslist Example)
tags: [programming,dns,georouting,system-design,internet,engineering]
date:  "2024-03-14"
---

### Geo-Routing (Case Study on Craigslist)
When you open craigslist.org it does a couple of redirection before finally landing on the subdomain closest to your city. Having separate sub-domain and listings for those particular cities is partly how it seems to handle the distribution of the content. (making it more scalable)

Give it a try:
[https://craigslist.org](https://craigslist.org)

Here is the journey of the request:

```bash
curl -I craigslist.org
HTTP/1.1 301 Found
Location: https://www.craigslist.org/
```

- redirected to https://www.craigslist.org/ (very typical of how we set up domains)


```bash
curl -I https://www.craigslist.org
HTTP/1.1 302 Found
Location: https://geo.craigslist.org/
Set-Cookie: cl_b=4|bdaad4bbb0e25be0dce55966b78f0d284bb32f62|1710414765fUrHI;path=/;domain=.craigslist.org;secure;expires=Fri, 01-Jan-2038 00:00:00 GMT
Strict-Transport-Security: max-age=63072000
```

- Note that, earlier 301 redirection was permanent. This geo redirection is temporary (302)

```bash
curl -I https://geo.craigslist.org/
HTTP/1.1 302 Found
Set-Cookie: cl_b=4|79f26cb381207ed66b52ce99325fde4410b540ae|17104147910rTD0;path=/;domain=.craigslist.org;secure;expires=Fri, 01-Jan-2038 00:00:00 GMT
Content-Length: 0
Content-Security-Policy: base-uri 'self' https://hcaptcha.com https://*.hcaptcha.com; child-src https://*.craigslist.org; connect-src https://*.craigslist.org https://hcaptcha.com https://*.hcaptcha.com; font-src data:; form-action https://*.craigslist.org; frame-ancestors 'self'; frame-src https://*.craigslist.org https://duckduckgo.com/search.html; media-src data:; object-src 'none'; script-src 'unsafe-inline' 'unsafe-eval' https://*.craigslist.org https://hcaptcha.com https://*.hcaptcha.com; style-src 'unsafe-inline' https://*.craigslist.org https://hcaptcha.com https://*.hcaptcha.com
Date: Thu, 14 Mar 2024 11:13:11 GMT
Last-Modified: Thu, 14 Mar 2024 11:13:11 GMT
Location: https://lucknow.craigslist.org
Vary: Accept-Encoding
X-Frame-Options: SAMEORIGIN
Strict-Transport-Security: max-age=63072000
```

- this redirection has finally led me to https://lucknow.craigslist.org (probably that's the closest one geographically)

```bash
curl -I https://lucknow.craigslist.org
HTTP/1.1 200 OK
Content-Type: text/html; charset=UTF-8
Date: Thu, 14 Mar 2024 11:14:02 GMT
Expires: Thu, 14 Mar 2024 12:14:02 GMT
```

- Finally, I am served with the HTML

(When you use the browser all of these redirections happen behind the scenes and the browser handles them transparently. Actually, for a split second you can see the redirection happening at the address bar.)


Finally confirming the IP addresses for these domains:

```bash
test dig +short craigslist.org
208.82.237.129
➜  test dig +short geo.craigslist.org
www.g.craigslist.org.
208.82.238.129
➜  test dig +short lucknow.craigslist.org
cities.g.craigslist.org.
208.82.238.130
```