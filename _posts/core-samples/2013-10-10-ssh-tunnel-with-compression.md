---
layout: post
category : lessons
tags : [ssh, tips]
---
{% include JB/setup %}


##SSH Connecting with compression

A SSH tunnel does not only have the advantage that nobody can listen in on your transfers, 
but also another neat feature of SSH.... compression. 
To add compression to your connection just add a -C to the command which you use to connect with.

	ssh -C user@server.com