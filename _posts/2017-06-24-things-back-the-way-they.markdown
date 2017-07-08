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

![diagram]({{ site.url }}/assets/diagram.png#center)

As for architectures, the **asinfo** has *get-arch*, *list-arch*, *set-arch %name_of_arch* options,
<strong>arch_dispatcher</strong> is purposed to process these options.
As a return value, subroutine provides array of arch_descriptor's:

{%highlight c %}
struct arch_descriptor {
	enum arch_types arch_num;
	const char *arch_name;
	int max_scn;
	struct_sysent *list_of_syscall;
};
{% endhighlight %}

Concerning managing with system calls, the **asinfo** has *get-num*, *get-nargs*, *get-proto*,
*get-kernel*, *list-syscall* options.
Based on request, the <strong>syscall_dispatcher</strong> will return the corresponding structures, for example, typing:

{%highlight bash %}
$ asinfo get-nargs write
{% endhighlight %}

<strong>syscall_dispatcher</strong> will return the following structure:

{%highlight c %}
struct sc_nargs {
	struct sc_base_info sys_bi;
	int nargs;
};
{% endhighlight %}

where <strong>struct sc_base_info</strong> is:

{%highlight c %}
struct sc_base_info {
	int sys_number;
	char *sys_name;
	int sys_flags;
};
{% endhighlight %}

After that, the result will be printed:

{%highlight bash %}
$ asinfo get-nargs write
3
{% endhighlight %}

Now, to consider the <strong>filter_dispatcher</strong>, let's enter the next one command:

{%highlight bash %}
$ asinfo list-syscall set-filter signal
{% endhighlight %}

Firstly, <strong>arch_dispatcher</strong> will return the current architecture, after that
<strong>syscall_dispatcher</strong> will provide the list of system calls (<strong>struct sc_base_info</strong>)
for this architecture, finally <strong>filter_dispatcher</strong> working with syscallent's
database will drop all system calls, which are not signal-related.

The last one detail, how to convert set-arch option to request for arch_dispatcher?
For this purpose, there is special function <strong>form_req</strong>:

{%highlight c %}
static unsigned ATTRIBUTE_NOINLINE
form_req(int argc, char *argv[], char *args[])
{% endhighlight %}

Oh, State Examination is acting up... That brings me to the end of my post. Next time, I
am about to describe the push-back interface with list of arch_descriptor and something more.
And yes, the previous post was an example of list-arch command.

Watch for updates!
