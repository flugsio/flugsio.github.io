---
layout: post
title:  "Recover hidden disk space usage on android"
date:   2015-08-13 01:27
categories: life
---

Recently I have had alot of issues
when installing new apps on my andorid device,
it would display errors about being out of disk space.

Even when there was 2-300MB free,
nothing seemed to help.
I had removed most of all the useless apps,
but the space was eaten right away.
When I couldn't update the important apps
I decided it was enough.

I finally found out
what was eating all of the disk space.
Apparently it was log files,
I used the
[DiskUsage](https://github.com/ivanvolosyuk/diskusage)
[application](https://play.google.com/store/apps/details?id=com.google.android.diskusage)
and saw that "System data" took over 1GB.
This seemed somewhat resonable,
but then I discovered that it actually grew every day.

![DiskUsage graph]({{ site.url }}/images/20150813_005629.jpg)

I crawled the web looking for answers,
and found a hidden tool in the dialer app.
You might not know what it looks like,
it usually comes preinstalled and
has a keypad like a calculator
but can't actually do math.

If you type these characters you will get a menu: `*#9900#`

![SysDump options]({{ site.url }}/images/20150813_005424.jpg)

I pressed "Delete dumpstate/logcat" and freed around 1GB of space,
awesome.
That dialer app is actually useful for something,
I guess I'll keep it installed for now.
