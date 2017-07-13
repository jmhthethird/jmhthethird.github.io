---
layout: post
title:  "Glances is pretty dope"
date:   2017-07-13
categories: unix
---
My system administration chops are still kinda light.

I feel like I'm still googling way too often and have to rut around a bit too much on a given system to find out whats causing the "issue" im chasing at that particular time. So while the really amazing folks I work with have great chops with Linux/Unix etc. I still find myself discovering cool stuff like glances!

I've found that [top](https://linux.die.net/man/1/top) is pretty great. So is [iftop](http://www.ex-parrot.com/pdw/iftop/), [iotop](http://guichaz.free.fr/iotop/), [htop](https://github.com/hishamhm/htop) and [nethogs](https://github.com/raboof/nethogs). All very cool.

One tool I recently started loving was [glances](https://nicolargo.github.io/glances/). It makes it so I can see all of the constraints that usually plague a server all in one place. Which for me is a huge deal in getting to the bottom of something as quickly as possible. I wish I lived in a land where we centrally log all this stuff and can monitor/watch it with super pretty dashboards but unfortunately there is only so much time in the day and if we are all lucky meetings only take up half of that.

Do yourself a favor and check them out:
[https://nicolargo.github.io/glances/](https://nicolargo.github.io/glances/)

![](https://raw.githubusercontent.com/nicolargo/glances/develop/docs/_static/glances-summary.png)
<sub>Image Credit: https://github.com/nicolargo/glances</sub>

