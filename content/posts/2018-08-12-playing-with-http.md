---
title: Some notes on HTTP
tags: post
tags: [http,web,internet,netcat,networking,engineering]
---

Learning from the udacity course on client server communication

course link: https://classroom.udacity.com/courses/ud897

Using netcat:

    nc google.com 80
    <head/get/post/put>
    headers:
        OPTIONS: get a list of methods supported by the URL end point
        HEAD: just the header and not the body.. validate if the incoming data will fit the memory, cache validation

xml-http-request: xhr, traditional way of doing ajax, new way is javascript promises which are chainable callbacks and are asynchronous

How head is used for cache checking.. check for incoming size and the size of the data chunk stored in cache.. but with increasing assets in a website it becomes in efficient (each round trip costs network)


Common response headers:
    content-length: 
        content-type: browser uses appropriate parsing enginer (render image, play audio/video)
        Last-modified: not very good to detect modification.. (dev can change one file but upload entire project to server again)
        E-tag: entity tag, based on file content, most servers use hashing (sort of like version control of the resources)
        If-Modified-Since: based on last Last-modified
        If-None-Match: based on e tag (this takes precedence over If-Modified-Since)

tcp/ip joke, udp joke

time to first byte (ttfb)
head of line blocking

http connection keep alive: use same channel (each http connection requires a tcp handshake underneath) (js+css bundled together in a bundle.js)



using proxy:
     export http_proxy=http://localhost:8080/
$ export https_proxy=$http_proxy (in linux)
there are several tools like burp suite, foxy proxy for firefox (route browser traffic through a proxy)


tls: transfer layer security (can be combined with http->https, ftp->ftps)


asymmetric cryptography: sign documents using public key, but documents can be fairly huge, so just sign the hash of it


server sends a certificate, browsers store (CA collection) public key and certificates (key, domain, authority signature) 
browser generates a random key (meant for symmetric encryption) encrypts is using public key and sends it to the server.. now client server can have symmetric encryption connection (faster, scales more, efficient), also validates server's identity because if it didn't have private key it would not be able to communicate with the client


https://badssl.com/ website to find out what sort of things are valid, trustable or not


http 2:
    http 1 header cannot be compressed, 2's header can be (saving header data can be significant improvement where requests are more. )

    header compression, avoid head of line blocking, security via TLS are the main points

    no head of line blocking: single connection, multiple streams, multiplexing: header compression.. streams can share the header too


    what should be different with http2: (find the screenshot)




javascript and CORS:  (same origin policy)
    by default it's only allowed to interact with it's own origin
    origin:: DAta scheme, hostname, port 
             http:         something.com    90

             change either one of these and the origin is different

             can't get/fetch,

Cross origin resources sharing

jsonp json with padding

why it requires pre-flight: pre-flight is an OPTIONS request to check what operations and what origins are supported (just not to lose the operations)
normal GET are not preflighted (if it's blocked it won't give any resources)

csrf:
    

