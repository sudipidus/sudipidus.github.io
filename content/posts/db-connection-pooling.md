---
layout: post
title: Database Connection Pooling
tags: [engineering,programming,database,backend]
---


## DB Connections are expensive

Normally following things happen when your backend service talks to a database:

 - service initiates a connection to DB using the database driver
 - db does the authentication and establishes a network session (tcp) if succeeded
 - program/service performs some db operation (CRUD) which follows authorization (if user/role is allowed to perform the requested operation)

 Now if the service decides to close the connection, it has to do these all over again which is quite an expensive process. It's a network session which holds upto 10 MB of memory (depending on DB)


 Enter connection pooling, where we maintain a set of connections and re-use them bypassing the expensive operation of opening and closing again. 


 Connection pooling can be done at the application side (various frameworks and db drivers), at the DB server itself or somewhere in between (Eg a pgBouncer daemon for postgres)


 ## Parameters

 - How many connections to have?
 	- how many idle connections to have? (database also can have its own restriction)
 	- how many open connections to have at a time?
- How long to keep a connection in the pool if it's idle?
	- database also has its own restriction (i.e it can decide to terminate the connection if it is idle for long)


## Some points

- It's crucial to ensure that connections are properly released back to the pool once you're done using it. Many databases allow one operation to be performed at a time per connection. If an operation returns some data from the database it's not available for others until the client finishes consuming the result set. This is a very common pitfall where connections leak. Depending on framework/db-drivers there is a way to 'close' the result set so that the connection is returned back to the pool.



