---
layout: post
title: On Domain And DNS
tags: [domain, dns, web, www, internet]
---



**This github page**

This blog is a github page based on jekyll now and the URL as per github convention is ```sudipbhandari126.github.io``` (It's username.github.io) unless you set up a custom domain.

As of Jan 2020, I have a custom domain setup for this at ```www.sudipbhandari.wtf```. I had some insights into internet domains, dns and domain resolution while doing this activity.

***How are domains organized?***

All the internet domains are organized in a hierarchical fashion. In my case the descending order of hierarchy would be:

```
. : root hierarchy (every domain is suffixed with dot)

wtf : top level 

www.sudipbhandari : second level
```

***What happens when you browse?***

When you hit a domain in browser it first hits the configured dns server (usually google's dns server at 8.8.8.8) Practically there is a lot of caching involved in local computer as well as dns server. This query is generally a UDP query (Query Response protocol) and not TCP. After a series of queries (queries traverse down the hierarchy: root->top-level->second-level....) you finally get A record (IP) [which could be preceeded by Cname (Alias for your domain)] and then the browser makes the request to the particular IP.


**Testing My DNS Resolution**

I used ```dig``` which is a DNS look-up utility to, quite literally, dig up my DNS information. I took google's DNS server for this purpose which is at 8.8.8.8.

```bash
dig @8.8.8.8 +trace www.sudipbhandari.wtf
#+trace show the steps in each process of hierarchical querying
```

Each of the following steps is hierarchical. Step 1 gives authoratative server to which subsequent step has to be queried against until we have the IP address.

- Step 1: It queries the root domain server

```
; <<>> DiG 9.11.3-1ubuntu1.11-Ubuntu <<>> @8.8.8.8 +trace www.sudipbhandari.wtf
; (1 server found)
;; global options: +cmd
.			298	IN	NS	m.root-servers.net.
.			298	IN	NS	b.root-servers.net.
.			298	IN	NS	c.root-servers.net.
.			298	IN	NS	d.root-servers.net.
.			298	IN	NS	e.root-servers.net.
.			298	IN	NS	f.root-servers.net.
.			298	IN	NS	g.root-servers.net.
.			298	IN	NS	h.root-servers.net.
.			298	IN	NS	a.root-servers.net.
.			298	IN	NS	i.root-servers.net.
.			298	IN	NS	j.root-servers.net.
.			298	IN	NS	k.root-servers.net.
.			298	IN	NS	l.root-servers.net.
```

As mentioned earlier, we can see that the root domain is dot(.)

- Step 2: It queries for top domain (wtf in my case)

```
Received 525 bytes from 8.8.8.8#53(8.8.8.8) in 163 ms

wtf.			172800	IN	NS	demand.beta.aridns.net.au.
wtf.			172800	IN	NS	demand.alpha.aridns.net.au.
wtf.			172800	IN	NS	demand.delta.aridns.net.au.
wtf.			172800	IN	NS	demand.gamma.aridns.net.au.
```

- Step 3: It queries for second level domain (sudipbhandari.wtf)

```
;; Received 717 bytes from 192.58.128.30#53(j.root-servers.net) in 181 ms

sudipbhandari.wtf.	86400	IN	NS	ns3cqz.name.com.
sudipbhandari.wtf.	86400	IN	NS	ns1jsv.name.com.
sudipbhandari.wtf.	86400	IN	NS	ns2fln.name.com.
```

- Step 3: Now it queries for FQDN (Fully qualified domain name)

```
www.sudipbhandari.wtf.	300	IN	CNAME	sudipbhandari126.github.io.
;; Received 90 bytes from 162.88.60.47#53(ns2fln.name.com) in 214 ms
```

At this step we get a C-Name record. This is because I have set up an alias for sudipbhandari126.github.io at my domain provider (name.com as of Jan 2020). (Setting alias like requires changes on both domain provider side to add entry as well as your site (where you identify yourself as such)). (Otherwise you can imagine I could set up a domain www.evil-site.com and redirect it to www.google.com)

- Step 4: We still don't have IP. So the finally query gets the IP (A record)

```
www.sudipbhandari.wtf.	299	IN	CNAME	sudipbhandari126.github.io.
sudipbhandari126.github.io. 2531 IN	A	185.199.110.153
sudipbhandari126.github.io. 2531 IN	A	185.199.111.153
sudipbhandari126.github.io. 2531 IN	A	185.199.109.153
sudipbhandari126.github.io. 2531 IN	A	185.199.108.153
```

(4 Different IPs at github for this github page to load balance the traffic, make fault tolerant)



We can check the domain details of any domain (which is the registrar, when does it expire, when was it last updated, etc) by querying whois directory service. Against my domain I get the following output:

```
 whois sudipbhandari.wtf                               sudipbhandari@sudipbhandari-Latitude-5480
Domain Name: sudipbhandari.wtf
Registry Domain ID: ae07b5b17a0e481fb88fbeec97475175-DONUTS
Registrar WHOIS Server: whois.name.com
Registrar URL: http://www.name.com
Updated Date: 2020-01-21T18:29:01Z
Creation Date: 2020-01-16T18:28:48Z
Registry Expiry Date: 2021-01-16T18:28:48Z
Registrar: Name.com, Inc.
Registrar IANA ID: 625
Registrar Abuse Contact Email: abuse@name.com
Registrar Abuse Contact Phone: +7.202492374
Domain Status: clientTransferProhibited https://icann.org/epp#clientTransferProhibited
Registry Registrant ID: REDACTED FOR PRIVACY
Registrant Name: REDACTED FOR PRIVACY
......
```

Google:

```
whois google.com                                      sudipbhandari@sudipbhandari-Latitude-5480
   Domain Name: GOOGLE.COM
   Registry Domain ID: 2138514_DOMAIN_COM-VRSN
   Registrar WHOIS Server: whois.markmonitor.com
   Registrar URL: http://www.markmonitor.com
   Updated Date: 2019-09-09T15:39:04Z
   Creation Date: 1997-09-15T04:00:00Z
   Registry Expiry Date: 2028-09-14T04:00:00Z
   Registrar: MarkMonitor Inc.
```



