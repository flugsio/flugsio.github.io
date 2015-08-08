---
layout: post
title:  "A wild bug appears!"
categories: code
---

I still don't know why I write about these things,
they seem trivial and unimportant.
But I need to fill the blog with something i guess.

I'm using archlinux,
mostly to get fresh software and for a somewhat minimal environment,
but I'm rather bad at it.
After an embarrasing long uptime without updating (33 days!),
I finally cleaned away all my open applications,
updated everything and restarted the system.

Context
-------

I received some messages with links in
[telegram](http://desktop.telegram.org)
that didn't seem to work.
Closer inspection revealed that
the character & was being prefixed with \\\\
when clicking the link.
I was just about to make an issue on github,
but first checked with the friend
if it actually worked on windows
to help with the triage.
I was intrigued to find out that it did,
because it was the same codebase that was used on both platforms.
I was
[one](http://i.imgur.com/J7zqedF.png) mouse click away
from sending the bug-report
and thus missing out on all the fun.

The fun stuff
-------------

My friend was laughing at me for using linux,
but I was exited;
this could not have been broken for long,
a fresh bug to find!
I though about what was different between windows and linux related to opening links,
and concluded that it would probably use xdg-open on linux.
I tested it directly instead of trying to dig through the telegram code.

`xdg-open 'https://www.google.se/?a=b&c=d'` indeed, the & was being prefixed with \\\\ in multiple browsers.
I skimmed through the recent commits in the
[project](http://cgit.freedesktop.org/xdg/xdg-utils/),
but didn't find anything obvious.
I noticed that the latest commit
was the same that my xdg-open was compiled from.
if the bug was in this project,
then it would not been fixed yet.

Let the hunt begin
------------------

I cloned the project and tried to reproduce the issue.
Spent some time trying compile it without success,
the main Makefile hanged on some html generation.
The Makefile in the scripts directory failed because
I didn't have xmlto installed so I installed that
but it still failed on some documentation errors.

I decided to take a peak at the readme and found this:

{% highlight text %}
Building is not required, all tools are located in the
scripts/ subdirectory and are ready to be used.
{% endhighlight %}

xdg-open.in was just a sh (bash) file,
I ran the example
`./scrits/xdg-open.in 'https://www.google.se/?a=b&c=d'`
and it seemed to be working.
Weirdly I could not reproduce the issue,
it worked fine apart from alot of messages about command not found;
mostly missing browsers.
but how‽

I double checked that the
[PKGBUILD](https://projects.archlinux.org/svntogit/packages.git/tree/trunk/PKGBUILD?h=packages/xdg-utils)
didn't do anything weird.
Then I compared the repository script with /usr/bin/xdg-open
and found that the only difference was alot of code after this line
#@xdg-utils-common@

I used a command to include that file
to be able to redo it with git bisect.
`sed -i '/#@xdg-utils-common@/r scripts/xdg-utils-common.in' scripts/xdg-open.in`

Weirdly, by inserting this code the but appeard.
It sent me a bit offtrack, thinking the bug might've been in that file.
I didn't use that much time trying to figure out the code,
I started a git bisect from a previous commit where it worked,
