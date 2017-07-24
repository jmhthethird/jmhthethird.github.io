---
layout: post
title: "I don't know very much bash but what I do know gets me in trouble"
date:   2017-07-21
categories: bash
---
Most of my day to day work is done in version control and with automation tools like [Ansible](https://www.ansible.com/).

But that doesn't cover all the little things that come up. So here are a few of my favorite commands and an example of how it bit me in the ass.



```
for i in `openstack ip floating list | grep 169 | awk '{print $2}'`; do openstack ip floating delete $i; done
```
So here's a bash command that uses the [Openstack](https://www.openstack.org/) cli to get a list of all vms. It greps the IPs of those vms and returns just the vms with a floating ip attached. Per our config all floating ips start with 169. The assumption here being 169 is unique to just the first octet......which it wasn't.

So _poof_ there went an extra 2 vms that I had to rebuild. Just because of lazy grepping. :(


<br>
```
grep -rl 'qa99' ./ | xargs sed -i 's/qa99/qa36/g'
```
Another lazy grep. This outputs the current directories files if it has the phrase qa99 in it. It then feeds that to sed and does a find/replace in the file for every occurrance and replaces it with qa36.

Basically a quick and dirty find/replace for every file in this directory.

What I didn't think about was that we have one master file with all the qa environments listed....in that same directory. So after I ran this it looked like I completely removed qa99 from our system. One small heart attack and a quick look at the files I changed and we were back to normal. Still....lazy grepping is bad grepping.


<br>
```
git commit --amend --author="Jack Hefner <definitely@not.my.real.email.com>"
```
Ok so full disclosure here. I actually went into OSX and made auto-correct turn the phrase, `god fucking dammit` into the above line. Because I pull down repos and forget to set my author info wayyyyy too often.

How did something this benign bite me? I didn't notice what directory I was in when I ran this. It was definitely not my own working directory on a shared server.....and the next day I, all of a sudden, was credited with like 30 commits that were not mine. So I got to go back and correct all those afterwards.

Fun times.

<br>
Ok last one:
```
find . -maxdepth 1 -mindepth 1 -type d -exec sh -c '(echo {} && cd {} && git status && echo)' \;
```
Ok truth be told this one hasn't bitten me yet. It is just really nice and useful. It will look 1 dir deep and run `git status` followed by an empty echo statement that basically just adds a return line to make the format more readable.

This has helped me on numerous occassions to make sure everything in a working dir is on the same branch. I often sub out the `git status` command for all kinds of things. Anything you can think of that you'd want to run in all directories 1 level deep from  your current location.

~GLHF~
