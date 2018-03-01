---
layout: post
title:  "Thesis work is coming soon: EPT"
date:   2018-03-01 00:00:00 +0300
categories:
---
Howdy, droogies!

A lot of things happened since the last post in my blog, and one of the
most important, I have done with Google Summer of Code and my
strace patches are still being merged. Hopefully, this month, I will manage
to rebase them finally and send out to the mailing list.

Actually, I don't want to post size of the elephant stories about my life,
because a reader might find it boring and, frankly speaking, it is besides the
point. It will be better to talk about thesis work and its current state.
The first topic I have chosen is EPT A/D (Access/Dirty) bits passthrough to
host machine in KVM. When someone wants to get into some topic in open-source
project, he faces a lot of problems, where the most irritating is a lack of
documentation. In the 99 out of 100 cases, it means that retrieving information
in the open-source project turns to continuous grep'ing git logs and reading
sources. Having spent a week for reading <u>kernel.org</u>, I came to the
conclusion that it is worth to write a small article about Intel technology
called **Extended Page Table**, or simply EPT.

