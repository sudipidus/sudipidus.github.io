---
title: A tiny Springboot app
layout: post
tags: [springboot,java,groovy,spring,web-framework]
---

*This miniblog is adpated from [this linkedin course](https://www.linkedin.com/learning/spring-boot-essential-training/the-tweet-that-started-it-all)*

## The tweet that started it all


<blockquote class="twitter-tweet" data-lang="en"><p lang="en" dir="ltr"><a href="https://twitter.com/Controller?ref_src=twsrc%5Etfw">@Controller</a><br>class ThisWillActuallyRun {<br>    <a href="https://twitter.com/RequestMapping?ref_src=twsrc%5Etfw">@RequestMapping</a>(&quot;/&quot;)<br>    <a href="https://twitter.com/responsebody?ref_src=twsrc%5Etfw">@ResponseBody</a><br>    String home() {<br>        &quot;Hello World!&quot;<br>    }<br>}</p>&mdash; Rob Winch (@rob_winch) <a href="https://twitter.com/rob_winch/status/364871658483351552?ref_src=twsrc%5Etfw">August 6, 2013</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

## Install springboot cli
Easiest way is via sdkman.

```bash
sdk install springboot
```

Groovy classFile:

ThisClassWillActuallyRun.groovy
```groovy
@Controller
class ThisWillActuallyRun {
    @RequestMapping("/")
    @ResponseBody
    String home() {
        "Hello World!"
    }
}
```

Running the app:

```bash
spring ThisWillActuallyRun
```

Output:

```bash
  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::        (v2.1.3.RELEASE)

2019-03-15 00:26:53.437  INFO 1091 --- [       runner-0] o.s.boot.SpringApplication               : Starting application on sysadm-Latitude-5480 with PID 1091 (started by sudipbhandari in /home/sudipbhandari)
2019-03-15 00:26:53.452  INFO 1091 --- [       runner-0] o.s.boot.SpringApplication               : No active profile set, falling back to default profiles: default
```

Verifying Output:

```bash
curl localhost:8080
Hello World!%
```




