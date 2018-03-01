---
layout: post
title:  "GSoC 2017: Work product submission"
date:   2017-08-27 00:00:00 +0300
categories:
---

Hello, folks!

It is time to take stock of what has been done so far with a view
to charting a practical way forward.

# Go back to proposal

First of all, let me remind what I stated in proposal[1] and what I really made.
Actually, I guess it is no secret, that strace has an unique database storage of system
calls for a wide range of architectures, such as microblaze, riscv, avr32, well-known
x86 etc. So **asinfo** (advanced system call information) tool had to operate with this
database and provide any information based on strace database in the most convenient way.
Well, on the whole, I suppose, that I handled it. Sure, I exaggerate a little bit the list
of features in proposal, but I've added more meaningful functionality, that I talk about below.

# Final architecture

After weeks of growing and refining, the **asinfo** tool gets the program model as shown in Figure 1.

![diagram]({{ site.url }}/assets/diagram2.png#center)
<center>Fig. 1. Architecture diagram</center>

# Usability

The usability of program can be examined in some good cases, here we go.

1) **asinfo** provides brief information about architecture
{%highlight bash %}
$ asinfo --set-arch or1k
| N | Architecture name | ABI mode | IMPL syscalls | IPC IMPL | SOCKET IMPL |
| 1 |     openrisc/or1k |    32bit |           277 | external |    external |
{% endhighlight %}

moreover, it is possible to type multiple architectures at a time

{%highlight bash %}
$ asinfo --set-arch or1k,riscv --list-abi
| N | Architecture name | ABI mode | IMPL syscalls | IPC IMPL | SOCKET IMPL |
| 1 |     openrisc/or1k |    32bit |           277 | external |    external |
| 2 |             riscv |    64bit |           276 | external |    external |
| 3 |             riscv |    32bit |           276 | external |    external |
{% endhighlight %}

The strace architecture database is quite large

{%highlight bash %}
$ asinfo --list-arch
|  N |       Architecture name |   ABI mode | IMPL syscalls | IPC IMPL | SOCKET IMPL |
|  1 |           blackfin/bfin |      32bit |           391 | external |    external |
|  2 |                    ia64 |      64bit |           707 | external |    external |
|  3 |                    m68k |      32bit |           378 | internal |     int/ext |
|  4 |                 sparc64 |      64bit |           340 | internal |     int/ext |
|  5 |                 sparc64 |      32bit |           358 | internal |     int/ext |
|  6 |                   sparc |      32bit |           358 | internal |     int/ext |
|  7 |                   metag |      32bit |           280 | external |    external |
|  8 |         mips64/mips64le |        n64 |          1000 |  int/ext |     int/ext |
|  9 |         mips64/mips64le |        n32 |          1004 |  int/ext |     int/ext |
| 10 |         mips64/mips64le |        o32 |          1039 | internal |     int/ext |
| 11 |             mips/mipsle |        o32 |          1039 | internal |     int/ext |
| 12 |                   alpha |      64bit |           433 | external |    external |
| 13 | ppc64/ppc64le/powerpc64 |      64bit |           374 |  int/ext |     int/ext |
| 14 | ppc64/ppc64le/powerpc64 |      32bit |           383 |  int/ext |     int/ext |
| 15 |       ppc/ppcle/powerpc |      32bit |           383 |  int/ext |     int/ext |
| 16 |           aarch64/arm64 |      64bit |           332 | external |    external |
| 17 |           aarch64/arm64 |       eabi |           400 | external |    external |
| 18 |                     arm |       oabi |           400 |  int/ext |     int/ext |
| 19 |                     arm |       eabi |           400 | external |    external |
| 20 |                   avr32 |      32bit |           329 | external |    external |
| 21 |                     arc |      32bit |           281 | external |    external |
| 22 |                   s390x |      64bit |           326 | internal |     int/ext |
| 23 |                    s390 |      32bit |           359 | internal |     int/ext |
| 24 |             parisc/hppa |      32bit |           348 | external |    external |
| 25 |                    sh64 |      64bit |           382 |  int/ext |     int/ext |
| 26 |                      sh |      32bit |           374 | internal |     int/ext |
| 27 |      x86_64/amd64/EM64T |      64bit |           333 | external |    external |
| 28 |      x86_64/amd64/EM64T |        x32 |           369 | external |    external |
| 29 |      x86_64/amd64/EM64T |      32bit |           381 | internal |     int/ext |
| 30 | x86/i386/i486/i586/i686 |      32bit |           381 | internal |     int/ext |
| 31 |            cris/crisv10 |      32bit |           355 | internal |    internal |
| 32 |                 crisv32 |      32bit |           355 | internal |    internal |
| 33 |             tile/tilegx |      64bit |           278 | external |    external |
| 34 |             tile/tilegx |      32bit |           278 | external |    external |
| 35 |                 tilepro |      32bit |           278 | external |    external |
| 36 |              microblaze |      32bit |           395 | external |    external |
| 37 |                   nios2 |      32bit |           277 | external |    external |
| 38 |           openrisc/or1k |      32bit |           277 | external |    external |
| 39 |                  xtensa |      32bit |           325 | external |    external |
| 40 |                   riscv |      64bit |           276 | external |    external |
| 41 |                   riscv |      32bit |           276 | external |    external |
{% endhighlight %}

2) Well, as Linux kernel allows userspace to work in a different mode or launch
some non-native (in sense of bitness) binaries, **asinfo** provides information
about most commonly used application binary interfaces (ABIs), you can request
list of ABIs or set it manually

{%highlight bash %}
$ asinfo --set-arch arm7 --list-abi
| N | Architecture name | ABI mode | IMPL syscalls | IPC IMPL | SOCKET IMPL |
| 1 |               arm |     oabi |           400 |  int/ext |     int/ext |
| 2 |               arm |     eabi |           400 | external |    external |
$ asinfo --set-arch arm7 --set-abi oabi
| N | Architecture name | ABI mode | IMPL syscalls | IPC IMPL | SOCKET IMPL |
| 1 |               arm |     oabi |           400 |  int/ext |     int/ext |
$ asinfo --set-arch arm7 --set-abi oabi2
asinfo: architecture 'arm' does not have ABI mode 'oabi2'
{% endhighlight %}

Some architectures, like mips64, contains at least 3 ABIs, so while working with
multiple architectures, there is no need to enumerate all ABIs, just use 'all'

{%highlight bash %}
$ asinfo --set-arch arm,mips64,amd64 --set-abi oabi,all,x32
| N |  Architecture name | ABI mode | IMPL syscalls | IPC IMPL | SOCKET IMPL |
| 1 |                arm |     oabi |           400 |  int/ext |     int/ext |
| 2 |    mips64/mips64le |      n64 |          1000 |  int/ext |     int/ext |
| 3 |    mips64/mips64le |      n32 |          1004 |  int/ext |     int/ext |
| 4 |    mips64/mips64le |      o32 |          1039 | internal |     int/ext |
| 5 | x86_64/amd64/EM64T |      x32 |           369 | external |    external |
{% endhighlight %}

3) Perhaps, the bare essential part of **asinfo** tool is system call part.
In the basic case, you can get strict mapping from system call name to number
and reverse

{%highlight bash %}
$ asinfo --get-sname 1
|   |              | x86_64 |
| N | Syscall name |  64bit |
| 1 |        write |      1 |
$ asinfo --get-snum write
|   |      | x86_64 |
| N | Snum |  64bit |
| 1 |    1 |  write |
{% endhighlight %}

or find occurence of input system call name

{%highlight bash %}
$ asinfo --get-snum /write
|   |      |            x86_64 |
| N | Snum |             64bit |
| 1 |    1 |             write |
| 2 |   18 |          pwrite64 |
| 3 |   20 |            writev |
| 4 |  296 |           pwritev |
| 5 |  311 | process_vm_writev |
| 6 |  328 |          pwritev2 |
{% endhighlight %}

It is worth mentioning, that usabilty (system call part) of other related tools
such as [2] thus ends, but **asinfo** in addition suggests filtering with extended
regular expression

{%highlight bash %}
$ asinfo --set-arch mips64 --set-abi o32 --get-snum "/^(.*_)?statv?fs"
|   |      |       mips64 |
| N | Snum |          o32 |
| 1 |   35 |  svr4_statfs |
| 2 |  103 | svr4_statvfs |
| 3 | 1035 |  sysv_statfs |
| 4 | 1174 | sysv_statvfs |
| 5 | 2160 | bsd43_statfs |
| 6 | 3035 | posix_statfs |
| 7 | 4099 |       statfs |
| 8 | 4255 |     statfs64 |
{% endhighlight %}

And the class filtering, where, for instance, user can request tool to show all
signal related system calls

{%highlight bash %}
$ asinfo --set-arch mips64 --set-abi o32 --get-sname signal
|    |                   | mips64 |
|  N |      Syscall name |    o32 |
|  1 |              kill |   4037 |
|  2 |             pause |   4029 |
|  3 |      rt_sigaction |   4194 |
|  4 |     rt_sigpending |   4196 |
|  5 |    rt_sigprocmask |   4195 |
|  6 |   rt_sigqueueinfo |   4198 |
|  7 |      rt_sigreturn |   4193 |
|  8 |     rt_sigsuspend |   4199 |
|  9 |   rt_sigtimedwait |   4197 |
| 10 | rt_tgsigqueueinfo |   4332 |
| 11 |          sgetmask |   4068 |
| 12 |         sigaction |   4067 |
| 13 |       sigaltstack |   4206 |
| 14 |            signal |   4048 |
| 15 |          signalfd |   4317 |
| 16 |         signalfd4 |   4324 |
| 17 |        sigpending |   4073 |
| 18 |       sigprocmask |   4126 |
| 19 |         sigreturn |   4119 |
| 20 |        sigsuspend |   4072 |
| 21 |          ssetmask |   4069 |
| 22 |            tgkill |   4266 |
| 23 |             tkill |   4236 |
{% endhighlight %}

The main advantage of tool is it can work in multiarch mode with the
opportunity to show discrepancies in system call characteristics

{%highlight bash %}
$ asinfo --set-arch amd64 --list-abi --get-sname signal
|    |                   | amd64 | amd64 | amd64 |
|  N |      Syscall name | 64bit |   x32 | 32bit |
|  1 |              kill |    62 |    62 |    37 |
|  2 |             pause |    34 |    34 |    29 |
|  3 |      rt_sigaction |    13 |   512 |   174 |
|  4 |     rt_sigpending |   127 |   522 |   176 |
|  5 |    rt_sigprocmask |    14 |    14 |   175 |
|  6 |   rt_sigqueueinfo |   129 |   524 |   178 |
|  7 |      rt_sigreturn |    15 |   513 |   173 |
|  8 |     rt_sigsuspend |   130 |   130 |   179 |
|  9 |   rt_sigtimedwait |   128 |   523 |   177 |
| 10 | rt_tgsigqueueinfo |   297 |   536 |   335 |
| 11 |          sgetmask |     - |     - |    68 |
| 12 |         sigaction |     - |     - |    67 |
| 13 |       sigaltstack |   131 |   525 |   186 |
| 14 |            signal |     - |     - |    48 |
| 15 |          signalfd |   282 |   282 |   321 |
| 16 |         signalfd4 |   289 |   289 |   327 |
| 17 |        sigpending |     - |     - |    73 |
| 18 |       sigprocmask |     - |     - |   126 |
| 19 |         sigreturn |     - |     - |   119 |
| 20 |        sigsuspend |     - |     - |    72 |
| 21 |          ssetmask |     - |     - |    69 |
| 22 |            tgkill |   234 |   234 |   270 |
| 23 |             tkill |   200 |   200 |   238 |
$ asinfo --set-arch amd64 --list-abi --get-snum process
|    |      |             amd64 |             amd64 |             amd64 |
|  N | Snum |             64bit |               x32 |             32bit |
|  1 |    1 |                 - |                 - |              exit |
|  2 |    2 |                 - |                 - |              fork |
|  3 |    7 |                 - |                 - |           waitpid |
|  4 |   11 |                 - |                 - |            execve |
|  5 |   56 |             clone |             clone |                 - |
|  6 |   57 |              fork |              fork |                 - |
|  7 |   58 |             vfork |             vfork |                 - |
|  8 |   59 |            execve |                 - |                 - |
|  9 |   60 |              exit |              exit |                 - |
| 10 |   61 |             wait4 |             wait4 |                 - |
| 11 |  114 |                 - |                 - |             wait4 |
| 12 |  120 |                 - |                 - |             clone |
| 13 |  158 |        arch_prctl |        arch_prctl |                 - |
| 14 |  190 |                 - |                 - |             vfork |
| 15 |  231 |        exit_group |        exit_group |                 - |
| 16 |  247 |            waitid |                 - |                 - |
| 17 |  252 |                 - |                 - |        exit_group |
| 18 |  272 |           unshare |           unshare |                 - |
| 19 |  284 |                 - |                 - |            waitid |
| 20 |  297 | rt_tgsigqueueinfo |                 - |                 - |
| 21 |  310 |                 - |                 - |           unshare |
| 22 |  322 |          execveat |                 - |                 - |
| 23 |  335 |                 - |                 - | rt_tgsigqueueinfo |
| 24 |  358 |                 - |                 - |          execveat |
| 25 |  384 |                 - |                 - |        arch_prctl |
| 26 |  520 |                 - |            execve |                 - |
| 27 |  529 |                 - |            waitid |                 - |
| 28 |  536 |                 - | rt_tgsigqueueinfo |                 - |
| 29 |  545 |                 - |          execveat |                 - |
{% endhighlight %}
Besides that, you can switch second system call characteristic to number of
arguments

{%highlight bash %}
$ asinfo --set-arch amd64 --list-abi --get-sname process --nargs
|    |                   | amd64 | amd64 | amd64 |
|  N |      Syscall name | 64bit |   x32 | 32bit |
|  1 |        arch_prctl |     2 |     2 |     2 |
|  2 |             clone |     5 |     5 |     5 |
|  3 |            execve |     3 |     3 |     3 |
|  4 |          execveat |     5 |     5 |     5 |
|  5 |              exit |     1 |     1 |     1 |
|  6 |        exit_group |     1 |     1 |     1 |
|  7 |              fork |     0 |     0 |     0 |
|  8 | rt_tgsigqueueinfo |     4 |     4 |     4 |
|  9 |           unshare |     1 |     1 |     1 |
| 10 |             vfork |     0 |     0 |     0 |
| 11 |             wait4 |     4 |     4 |     4 |
| 12 |            waitid |     5 |     5 |     5 |
| 13 |           waitpid |     - |     - |     3 |
{% endhighlight %}

4) Also, the single arch mode allows program to take a guess about the current
architecture and ABI, if they are not specified. The following example with
x86_64 architecture and 64bit userspace environment

{%highlight bash %}
$ asinfo --get-sname /write
|   |                   | x86_64 |
| N |      Syscall name |  64bit |
| 1 | process_vm_writev |    311 |
| 2 |          pwrite64 |     18 |
| 3 |           pwritev |    296 |
| 4 |          pwritev2 |    328 |
| 5 |             write |      1 |
| 6 |            writev |     20 |
{% endhighlight %}

5) To get rid of built-in output formating and to use in combination with other tools,
use raw printing

{%highlight bash %}
$ asinfo --set-arch arm64,amd64 --list-abi --get-sname /write --raw
1;pciconfig_write;-;273;-;-;-;
2;process_vm_writev;271;377;311;540;348;
3;pwrite64;68;181;18;18;181;
4;pwritev;70;362;296;535;334;
5;pwritev2;287;393;328;547;379;
6;write;64;4;1;1;4;
7;writev;66;146;20;516;146;
$ asinfo --get-sname /write --raw | awk -F ";" '{print $2,$3}' | column -t
process_vm_writev  311
pwrite64           18
pwritev            296
pwritev2           328
write              1
writev             20
{% endhighlight %}

# Current state

For now I've completed all preparations, which are necessary to
share source code  between strace and asinfo binaries
(**Merged** [3]):<br>

* `e398011d Move SUPPORTED_PERSONALITIES to a separate file`<br>
* `b1fab45b Move string_to_uint* functions to a separate file`<br>
* `3d565ef2 Move sysent shorthand notations to separate files`<br>
* `b4f16886 Move err/mem subroutines to separate files`<br>

Speaking about main work, I've implemented asinfo tool and pushed
to forked github repository [4]:<br>

* `416e1bb5 asinfo: Introduce static query tool asinfo`<br>
* `60deb084 Generate arch_includes.h and arch_personalities.h`<br>
* `a25b0d09 asinfo: add man page`<br>
* `bb30d149 Add asinfo to build strace deb/rpm packages`<br>
* `f3e95004 Update NEWS`<br>

# TO-DO

* Complete work on tests for **asinfo**
* Merge **asinfo** to main strace repository

# Changelog

All weekly reports with progress are available in strace mailing list [5].

v10
* Add asinfo binary to deb/rpm build.
* Add '\-\-raw' parameter to print out without formating.
* Update MAN page and usage.
* Fix issues and typos.

v9
* Add MAN page.
* Add script to generate source code describing architecture.
* Use new number_set API.
* Fix issues and typos.
* Fix broken make dist with regard to asinfo.

v8
* Add multiarch mode for syscall_dispatcher, which allows to show
descrepancy in syscall numbers/name/arguments (number).
* Add syscall filtering(as in strace).
* Fix issues and typos.
* Refine push back interface for syscall_service.
* Refine push back interface for arch_service.

v7
* Introduce multiarch mode for arch_dispatcher and abi_dispatcher.
* Introduce new arch_description storage (arch_definitions.h).
* Fix issues and typos.

v6
* Adapt syscall dispatcher to the new program architecture.
* Implement usage.
* Refine command_dispatcher.
* Fix issues and typos.

v5,v4,v3
* Add command_dispatcher
* Introduce syscall_dispatcher(raw version).
* Introduce push back interface for syscall_service.
* Fix issues and typos.

v2
* Refine arch_dispatcher.
* Introduce abi_dispatcher.
* Add all architectures/ABIs supported by strace.
* Introduce push back interface for arch_service.
* Fix issues and typos.

v1 and older minor implementation:
* Introduce pipeline architecture.
* Implement the first version of arch_dispatcher.
* Introduce define-based interface to interact with array of arch_description.

# Acknowledgments

Apart from anything else, I would say, that participation in Google Summer of
Code program provides a valuable experience in open-source.
And I would like to thank my mentors for reviewing patches and I look forward
to continued cooperation to merge **asinfo** into main strace git repo.

# **Links:**

[1] [GSoC 2017 strace proposal][GSoC Proposal 2017]{:target="_blank"}<br>
[2] [ausyscall(8) - Linux man page][ausyscall man]{:target="_blank"}<br>
[3] [strace main repository][strace repo]{:target="_blank"}<br>
[4] [strace-fork with asinfo][asinfo tool]{:target="_blank"}<br>
[5] [Edgar Kaziakhmedov's GSoC status reports][status reports]{:target="_blank"}<br>

[status reports]: https://sourceforge.net/p/strace/mailman/search/?q=Edgar+Kaziakhmedov%27s+GSoC+status+report
[strace repo]: https://github.com/strace/strace/commits/master?author=edosedgar
[asinfo tool]: https://github.com/edosedgar/strace-fork/commits/master?author=edosedgar
[ausyscall man]: https://linux.die.net/man/8/ausyscall
[GSoC Proposal 2017]: https://storage.googleapis.com/summerofcode-prod.appspot.com/gsoc/core_project/doc/5155023655796736_1491232330_Proposal.pdf?Expires=1504007739&GoogleAccessId=summerofcode-prod%40appspot.gserviceaccount.com&Signature=V2Tu8vfOk%2FWudYqawuB2MEEPI14u6qotSOgpfQys4kpKYE2Ej9oWU4qQe14tjML8rJsKJWSReXAukDWnHcYDI0i4s46yy9ozWhvAwq3reL1q%2FOAkULgZwcvLYkxdDiKQJYx75q%2BLMeuWuU%2F%2BBwTXaNeoh4CPC4EIF1DpgvgssKQIq7nfa%2FzIgHP%2FiLoD5%2F%2BZ2Sww8sdaYVfczR%2Bf1smKy0CZOu4s4DZ41j4iC5QK3133UbHjVTxf%2FauFcreQ4mTYPlXdlcEUX62ETStAwE1ksUK%2BiMMS2xZoJLdKrZa177lR8fBCBIbh%2Ba368VbWfPBheuzx487czbb0WrrSbraAgQ%3D%3D





