---
layout: post
title:  "Things back the way they"
date:   2017-06-24 00:00:00 +0300
categories: gsoc2017
---
Good evening, readers. Last time, so much has happened. Let me tell this in order.

Firstly, perhaps, I have passed in mathematical physics at 8 grade (by the way, we
have a 10-score grade system, for instance, A is 10, 9, 8; B is 7, 6, 5; C – 4, 3).
I am really excited about it.

Now, let me introduce a little bit my project to make clear the previous post.
As I was saying before, I am working on advanced syscall information tool. What is that?
Actually, the name speaks for itself. Literally, this tool is purposed to work with
stored database about syscalls. Where am I going to get this database? It has already existed.

The strace contains special files, syscallent.h’s, for different architectures and stores
basic info about system calls, for instance (strace/linux/x86_64/syscallent.h:1):

{%highlight c %}
[ 0] = { 3, TD, SEN(read), "read" },
{% endhighlight %}

where, <br />
**3** - number of arguments; <br />
**TD** - flags for system calls to be grouped (here - file descriptor-related system call); <br />
**SEN(read)** - converts read to SEN_read(int) and sys_read(ponter to function); <br />
**"read"** - just the system call name.

Now, everything which is necessary to implement initial functionality - just use these files.
Therefore, the <strong>asinfo</strong> tool has the following architecture:
![diagram]({{ site.url }}/assets/diagram.png)
