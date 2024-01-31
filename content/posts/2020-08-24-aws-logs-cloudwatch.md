---
date: "2020-08-24"
layout: post
title: Accessing Cloudwatch logs via AWS CLI
tags: [aws, cloudwatch, logging, monitoring, devops, engineering]
---


## AWS Cloudwatch

[cloudwatch](https://aws.amazon.com/cloudwatch/) allows to take a peek into your aws applications, their logs and other metrices. Apart from the centralized logging (kibana,loggly) where we limit retention duration (cost factor), cloudwatch provides a way to do pay-per-volume query operations.


## AWS CLI

- [installation on mac](https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2-mac.html#cliv2-mac-install-gui)

- configuration `aws configure`
[Setting up AWS keys](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_access-keys.html)



Although cloudwatch comes with a nice GUI, we can leverage CLI to automate and ease up some tasks. Here is a use case for filtering logs and downloading them for further analysis.

- command being used `aws logs start-query` (For help: `aws logs start-query help`)

- this triggers the search on cloudwatch and returns a query id which can be used to download the content.

Eg:

```sh

aws logs start-query \
 --log-group-name production-logs \
 --start-time `date -j -f "%Y-%m-%d %H:%M:%S" "2020-08-20 00:00:00" "+%s"` \
 --end-time `date -j -f "%Y-%m-%d %H:%M:%S" "2020-08-21 23:59:59" "+%s"` \
 --query-string 'fields @timestamp, @message | filter @logStream like /$MyApp/ | filter @message like /$MyErrorMessage/'
``` 

- start-time and end-time are provided as unix epoch in seconds. You can further check your os' date program to format. (above is for standard bsd version)


Above command returns a response with just query_id:

```
{
    "queryId": "98bf5ee1-ff5e-4f1b-bbae-2b26e4e26e45"
}
```

This is an asynchronous process and to actually know when it finishes you can check the history in cloudwatch GUI.

![cloudwatch](https://sudipbhandari126.github.io/resources/cloudwatch.png "cloudwatch")

- Fetching of the data using queryId

```
aws logs get-query-results --query-id $queryId > $file.json
```

For a completed query, `status:complete` will be there in file or else `status:running`


