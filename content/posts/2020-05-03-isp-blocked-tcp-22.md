---
layout: post
title: Why Some ISPs block TCP 22? 
tags: [tcp, ssh, networking, isp, internet]
---

**Why Some ISPs block TCP 22?**

Recently I got to know that some ISP indeed block any outbound traffic for port 22 (tcp) which is a standard port for ssh connections. 

(I came to know about this as I was being unable to log into one of the compute instances from my terminal via ``gcloud`` in gcp. I prefer terminal logins instead of browser as it's simply 100 times easier to work with. Although compute instance allowed ssh connection from any (*/*) it was timing out for me. On doing a portscan I found that 999 ports were blocked in my network while 998 ports were blocked in others. (I asked my friends from various geographical locations to do so)



**PortScan Result**
```
nmap -Pn *.*.*.* 
Starting Nmap 7.80 ( https://nmap.org ) at 2020-05-03 22:36 IST
Nmap scan report for *.*.*.*.bc.googleusercontent.com (*.*.*.*)
Host is up (0.043s latency).
Not shown: 999 filtered ports
PORT     STATE  SERVICE
3389/tcp closed ms-wbt-server
```
(filtered=> ISP inspects such traffic and drops them)
)

Why so?
Mainly to prevent 'ssh remote port forwarding'.

When you remote port forward you are essentially redirecting traffic from your remote machine to your local machine. (Exposing this to the network) This can be thought of as a security threat as it's essentially allowing a backdoor to your internal network. (To me this looks more like security  by obscurity) as things like this are very easy to circumvent.

***When is ssh port forwarding actually userful?***

- ssh local port forwarding: this is how 'jump servers' are set up in IT companies. You don't want to give access to each system/account. So a central system's access is given to all which internally forwards to traffic to the destination.

- ssh remote port forwarding: this is used to expose your local system to the internet. May be you are creating a nice web page and want to show it your friend (you haven't hosted). You can simply forward traffic from some remote system that you both know to your local. (This is how [ngrok](http://www.sudipbhandari.wtf/ngrok/) works.)


***Solution***

Use a VPN that properly encrypts the traffic so that the ISP doesn't have a clue. (not all VPNs do encryption of same degree)









