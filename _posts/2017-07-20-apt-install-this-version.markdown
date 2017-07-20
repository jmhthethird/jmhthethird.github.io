---
layout: post
title: "Apt: Install this version (pretty please)"
date:   2017-07-20
categories: apt
---
A simple story of package version installation/management:

A PR needed verification and I was on a mission to do it. I needed a place that was "like" production and lo and behold our Disaster Recovery site was just the place.

But what is this? The DR site's Elixir wasn't compiling our app. Why wasn't it compiling our app? I have no idea. I know nothing about Elixir. BUT I do know that things don't generally stop working without good reason. Commence me plumbing through environment variable files for the next 30 minutes playing a rousing game of "one is not like the other".

So low and behold Elixir isn't the right version. Let us fix that.

First off let's determine the version installed.
```
DR-Site:~$ elixir --version

Elixir 1.4.5
```

Ok simple enough. But what version is installed in the other environment?
```
Prod-Site:~$ elixir --version

Elixir 1.3.4
```

AH. OK, so that is a pretty big version mismatch. Let's look at the versions available.
```
Prod-Site:~$ apt-cache policy elixir
elixir:
  Installed: 1.3.4-1
  Candidate: 1.4.5-1
  Version table:
     1.4.5-1 0
        999 http://binaries.erlang-solutions.com/debian/ trusty/contrib amd64 Packages
     1.4.4-1 0
        999 http://binaries.erlang-solutions.com/debian/ trusty/contrib amd64 Packages
     1.4.2-1 0
        999 http://binaries.erlang-solutions.com/debian/ trusty/contrib amd64 Packages
     1.4.1-1 0
        999 http://binaries.erlang-solutions.com/debian/ trusty/contrib amd64 Packages
     1.4.0-1 0
        999 http://binaries.erlang-solutions.com/debian/ trusty/contrib amd64 Packages
 *** 1.3.4-1 0
        999 http://binaries.erlang-solutions.com/debian/ trusty/contrib amd64 Packages
        100 /var/lib/dpkg/status
     1.3.3-1 0
        999 http://binaries.erlang-solutions.com/debian/ trusty/contrib amd64 Packages
     1.3.2-1 0
 .....(many older versions were listed as well)
```

Alright so it looks like our versions aren't matching.
<br>DR-Site version:   1.4.5
<br>Prod-site version: 1.3.4

And furthermore, it looks like 1.4.5 is the latest version of Elixir! Hmmm.

To me, this looks like someone accidentally updated a bunch of packages to the latest without knowing that they were breaking stuff. So let's downgrade that version to what we need!
```
DR-Site:~$ sudo apt-get install elixir=1.3.4-1
Reading package lists... Done
Building dependency tree
Reading state information... Done
The following package was automatically installed and is no longer required:
  libodbc1
Use 'apt-get autoremove' to remove it.
The following packages will be DOWNGRADED:
  elixir
0 upgraded, 0 newly installed, 1 downgraded, 0 to remove and 79 not upgraded.
Need to get 0 B/2,684 kB of archives.
After this operation, 463 kB disk space will be freed.
Do you want to continue? [Y/n] Y
dpkg: warning: downgrading elixir from 1.4.5-1 to 1.3.4-1
(Reading database ... 124582 files and directories currently installed.)
Preparing to unpack .../elixir_1.3.4-1_all.deb ...
Unpacking elixir (1.3.4-1) over (1.4.5-1) ...
Processing triggers for man-db (2.6.7.1-1ubuntu1) ...
Setting up elixir (1.3.4-1) ...
```
<sub>Note: from the above output we are using sudo to run this command. It is required that we have superuser privilege for this action.</sub>

You should always read the full output of what `apt` is doing to your packages. It is explicitly telling us that the package `elixir` is going to be `DOWNGRADED`. In this case that is exactly what we want to do.

And with that we can check that elixir is the right version:
```
DR-Site:~$ elixir --version

Elixir 1.3.4
```

Voila!

~Cheers~
