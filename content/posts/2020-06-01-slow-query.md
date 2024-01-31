---
date: "2020-06-01"
layout: post
title: Debugging Slow Queries In Backend
tags: [backend, database, query, optimization]
---

What are the things that should be on your checklist when you are facing a situation where your db queries are taking a lot of time?

- Cross check simple things such as if you would require pagination. If your filters are none (or too broad) even with optimized query plan/indexing query can take some time. 

- Take the slow query, view the execution plan and cross check if you would require indexing, if your query is set to run with the indices (eg: is the query properly making use of functional indices?)

- Check for things such as fragmentation. Is your table highly fragmented? This can happen if you support hard delete (which ideally you shouldn't, instead have a boolean flag like deleted/not sort of thing). 

- Make sure you invest good resource on your db's computational resource. Use SSD along with enough cores and memory. (you don't want to be limited by the number of rotations your HDD can make)

- Review optimizer layer of your database. (Sometimes post clean up activities, it doesn't pick the optimized path by default and you/dba would need to make manual configurational chnages)

- Use APM tools like newrelic to monitor query time, throughput and overall time. (Sometimes these could just be related to abnormal requests)
