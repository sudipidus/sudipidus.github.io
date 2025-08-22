---
date: "2018-08-12"
title: Some notes on HTTP
tags: post
tags: [http,web,internet,netcat,networking,engineering]
---


# Playing with HTTP: Practical Notes and Examples

HTTP is the foundation of the web, but it's easy to overlook how it actually works. This post collects some hands-on notes, experiments, and practical tips for understanding HTTP, inspired by the excellent [Udacity client-server communication course](https://classroom.udacity.com/courses/ud897). Whether you're a beginner or just want to refresh your basics, try these out yourself!

## Playing with HTTP: Practical Notes and Examples

These are some hands-on notes and experiments from the excellent [Udacity client-server communication course](https://classroom.udacity.com/courses/ud897). If you want to really understand HTTP, try these out yourself!

### Using netcat to Explore HTTP

You can use `nc` (netcat) to manually send HTTP requests and see the raw response:

```sh
nc google.com 80
GET / HTTP/1.1
Host: google.com
```
Try sending `OPTIONS` or `HEAD` requests to see what methods are supported or to fetch just headers. This is a great way to see how HTTP works under the hood.

**More examples:**
- `nc example.com 80` then type `HEAD / HTTP/1.1` and `Host: example.com`.
- Use `curl -I https://www.example.com/` to see only headers.

### AJAX: XHR and Modern JavaScript

The traditional way to do AJAX was with `XMLHttpRequest` (XHR). Now, we use the `fetch` API and Promises:

```js
fetch('https://api.example.com/data')
    .then(res => res.json())
    .then(data => console.log(data));
```

### HTTP Caching and Validation

Browsers and proxies use headers to cache and validate resources:
- `Last-Modified` and `If-Modified-Since`
- `E-Tag` and `If-None-Match`

**Example:**
```sh
curl -I https://developer.mozilla.org/
```
Look for `Last-Modified` and `E-Tag` in the response.

**Reference:** [MDN HTTP Caching](https://developer.mozilla.org/en-US/docs/Web/HTTP/Caching)

### Common Response Headers

- `Content-Length`: Size of the response body
- `Content-Type`: How the browser should handle the response (HTML, JSON, image, etc.)
- `Last-Modified`, `E-Tag`: For cache validation

### Performance Concepts

- **Time to First Byte (TTFB):** How quickly the first byte arrives from the server
- **Head-of-Line Blocking:** One slow request can block others (solved in HTTP/2)
- **Keep-Alive:** Reuse TCP connections for multiple requests

### Using Proxies for Debugging

Set up a proxy to inspect or modify HTTP traffic:

```sh
export http_proxy=http://localhost:8080/
export https_proxy=$http_proxy
```
Tools: [Burp Suite](https://portswigger.net/burp), [FoxyProxy](https://getfoxyproxy.org/)

### TLS, HTTPS, and Certificates

- **TLS:** Secures HTTP (HTTPS), FTP (FTPS), etc.
- **Certificates:** Server sends a certificate; browser checks it against trusted CAs.
- **Symmetric/Asymmetric Crypto:** Handshake uses asymmetric, then switches to faster symmetric encryption.

Test your browser's HTTPS handling at [badssl.com](https://badssl.com/).

### HTTP/2: Modern Improvements

- **Header Compression:** Reduces bandwidth
- **Multiplexing:** Multiple streams over one connection, no head-of-line blocking
- **TLS by Default:** Security is built-in

**Reference:** [HTTP/2 Explained](https://http2.github.io/)

### JavaScript, CORS, and Security

- **Same Origin Policy:** JS can only access its own origin (scheme, host, port)
- **CORS:** Allows controlled cross-origin requests. Preflight (OPTIONS) checks what is allowed.
- **JSONP:** Old workaround for cross-origin requests

**Example:**
```js
fetch('https://api.otherdomain.com/data', {
    mode: 'cors'
})
```

**Reference:** [MDN CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS)

### CSRF (Cross-Site Request Forgery)

CSRF exploits browser cookies to perform unwanted actions. Mitigations include same-site cookies and CSRF tokens.

**Reference:** [OWASP CSRF](https://owasp.org/www-community/attacks/csrf)

---

**Further Reading & Tools:**
- [MDN HTTP Overview](https://developer.mozilla.org/en-US/docs/Web/HTTP/Overview)
- [HTTPie](https://httpie.io/) — user-friendly HTTP client
- [Postman](https://www.postman.com/) — API testing tool

*Try these tools and examples to deepen your understanding of HTTP!*


