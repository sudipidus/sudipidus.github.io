---
date: "2020-03-01"
layout: post
title: ENTRYPOINT Vs CMD in Docker
tags: [docker, container, linux, devops]
---

CMD Vs Entrypoint is a bit confusing at the beginning. When should you use CMD and when should you use ENTRYPOINT while building your docker images?

***CMD***

This is the command that runs when you start a container.

Eg:

cmd-docker

```
FROM ubuntu
CMD [ "echo" , "this is running from CMD which runs when the container starts" ]
```

Building an image:

```docker build -t cmd-docker -f cmdvsentrypoint.yaml .```

Default docker run:

```
docker run cmd-docker
this is running from CMD which runs when the container starts
```

Docker run with external command as input

```
docker run cmd-docker echo this is passed as startup argument
this is passed as startup argument
```

***ENTRYPOINT***

This is just the starting program without the options/arguments.

Sample:

```
FROM ubuntu
ENTRYPOINT [ "echo" ]
```

docker build:  ```docker build -t entrypoint -f entrypoint.yaml .```

default run: 

```
docker run entrypoint

```
(there is no output since we just have echo and didn't pass an argument)

docker run with argument:

```
docker run entrypoint hello
hello
```

```
docker run entrypoint echo hello
echo hello
```

(This is equivalent to ```echo echo hello world```)
