---
title: Setting up local postfix to send mails via Google's SMTP
layout: post
tags: [mailx,postfix,smtp,linux-hacks]
---

## Postfix

[Postfix](https://en.wikipedia.org/wiki/Postfix_(software)) is a free and open-source mail transfer agent (MTA) that routes and delivers electronic mail. This is required in order to be able to send mails from the local machine (without using any vendor's client)

## Installation
```bash
sudo apt-get install postfix
```

Postfix has be configured. The suggested way to do is via: ```sudo dpkg-reconfigure postfix```. You can also directly edit ```/etc/postfix/main.cf``` file and make changes to the configuration. For most of the purposes ```dpkg-reconfigure``` should suffice.

Now that we have MTA ready, we need a mail user agent program in order to actually send and recieve those. In Unix the most common tool is [mailx](https://en.wikipedia.org/wiki/Mailx)

## Installation of mailx
```bash
sudo apt-get install bsd-mailx 
```

Now given that postfix is correctly configured we can start experimenting with mailx. I learned quite a few things doing this and thus I want to enumerate all of my attempts.

```bash
mailx -s "test subject" sudip.post@gmail.com
<body>
CtrlD
```

Command executed succesfully. However, I didn't get the message delivered.

## Postfix logs
Postfix mail logs are generally stored under /var/log/mail.log

```bash
 postfix/smtp[21002]: 0CCFD2083E1: to=<sudip.post@gmail.com>, relay=gmail-smtp-in.l.google.com[172.217.194.26]:25, delay=3.4, delays=0.15/0.11/2.3/0.82, dsn=5.7.1, status=bounced (host gmail-smtp-in.l.google.com[172.217.194.26] said: 550-5.7.1 [122.167.64.74] The IP you\'re using to send mail is not authorized to 550-5.7.1 send email directly to our servers. Please use the SMTP relay at your 550-5.7.1 service provider instead. Learn more at 550 5.7.1  https://support.google.com/mail/?p=NotAuthorizedError s8si140686pgp.140 - gsmtp (in reply to end of DATA command))
```

I figured out that google's smtp server is not allowing connection via port 25. So, I reconfigured smtp port for postfix to be 587. I also added config related to google smtp authorization.

## Postfix config changes ([StackExchange Source](https://superuser.com/a/280205/542628))

```bash
# This tells Postfix to hand off all messages to Gmail, and never do direct delivery.
relayhost = [smtp.gmail.com]:587

# This enables TLS (SMTPS) certificate verification, because Gmail has a valid one.
smtp_tls_security_level = verify
smtp_tls_CAfile = /etc/ssl/certs/ca-certificates.crt
smtp_tls_session_cache_database = btree:/var/run/smtp_tls_session_cache

# This tells Postfix to provide the username/password when Gmail asks for one.
smtp_sasl_auth_enable = yes
smtp_sasl_password_maps = hash:/etc/postfix/sasl_passwd
smtp_sasl_security_options = noanonymous
```

## store password inside /etc/postfix/sasl_passwd

```bash
[smtp.gmail.com]:587    user@gmail.com:mypassword # inside /etc/postfix/sasl_passwd
postmap /etc/postfix/sasl_passwd #compile password to db (we can safely remove previous cleartext file now)
sudo service postfix restart #restarting as it's run as a service
```


I tried executing ```mailx``` again and still didn't see that the mails were getting delivered. However I could see that the mail queue was holding it back. I could see the following error in mail.log

```bash
1917F2083CF: to=<sudip.post@gmail.com>, relay=smtp.gmail.com[74.125.24.109]:587, delay=322, delays=318/0.17/3.3/0, dsn=4.7.9, status=deferred (SASL authentication failed; server smtp.gmail.com[74.125.24.109] said: 534-5.7.9 Application-specific password required. Learn more at?534 5.7.9  https://support.google.com/mail/?p=InvalidSecondFactor o5sm22737898pgc.16 - gsmtp)
```

That's when I realized that the password I supplied earlier had to be 'app password' and not the actual gmail password. A quick google search got me landed to this [official support page](https://support.google.com/accounts/answer/185833). After I generated app password I changed the password in ```/etc/postfix/sasl_passwd```, postmapping (to database) and restarting the postfix MTA I executed mailx and voila, I was able to send message from linux terminal without using any non open source client or tool :bowtie:

## Sample postfix log for succesfull deliveries:

```bash
postfix/smtp[22127]: 6EC6720842A: to=<sudip.post@gmail.com>, relay=smtp.gmail.com[74.125.24.109]:587, delay=5, delays=0.13/0/3.4/1.4, dsn=2.0.0, status=sent (250 2.0.0 OK  1552745970 p20sm10104430pfi.45 - gsmtp)
```


## Usage for sys admins
You can trigger mails based on certain criterias. When memory usage goes high, or some certain conditions are met. Here's one such example I took from [tecmint:](https://www.tecmint.com/shell-script-to-send-email-alert-when-memory-low/)

```bash
#!/bin/bash 
#######################################################################################
#Script Name    :alertmemory.sh
#Description    :send alert mail when server memory is running low
#Args           :       
#Author         :Aaron Kili Kisinga
#Email          :aaronkilik@gmail.com
#License       : GNU GPL-3  
#######################################################################################
## declare mail variables
##email subject 
subject="Server Memory Status Alert"
##sending mail as
from="server.monitor@example.com"
## sending mail to
to="admin1@example.com"
## send carbon copy to
also_to="admin2@example.com"

## get total free memory size in megabytes(MB) 
free=$(free -mt | grep Total | awk '{print $4}')

## check if free memory is less or equals to  100MB
if [[ "$free" -le 100  ]]; then
        ## get top processes consuming system memory and save to temporary file 
        ps -eo pid,ppid,cmd,%mem,%cpu --sort=-%mem | head >/tmp/top_proccesses_consuming_memory.txt

        file=/tmp/top_proccesses_consuming_memory.txt
        ## send email if system memory is running low
        echo -e "Warning, server memory is running low!\n\nFree memory: $free MB" | mailx -a "$file" -s "$subject" -r "$from" -c "$to" "$also_to"
fi

exit 0
```

We can have this script run as a cron job, say, every 5 minutes or so and get the job done.

Having a local config and set up of postfix, mailx and other unix utilities definitely come handy in other such scenarios too.




