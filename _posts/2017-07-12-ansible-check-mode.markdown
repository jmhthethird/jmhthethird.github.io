---
layout: post
title:  "Ansible check_mode"
date:   2017-07-12
categories: ansible
---
Yesterday I discovered the Ansible flag `check_mode`. Super useful in my case!

I was doing some interfacing with applications in Ansible. Specifically, I was using a shell command to ask an application for its id then storing that in a variable.

That looked something like this:
{% highlight yaml %}
---
- name: "Get app id"
  shell: "bash -lc 'echo $APPID'
  register: app_id
{% endhighlight %}

And this worked great! It took away me having to manually grab an app id from a server. It stores it dynamically so I can reference it later and it removed me having to manually put that app id in my vars for the Ansible run.

The downside was the `--check` mode in Ansible skips shell commands by default as they often change things on the host system.

So long story short you can tell a given task to not honor the `check` mode flag and to run anyway with `check_mode: no`.

That looks like:
{% highlight yaml%}
- name: "Get app id"
  shell: "bash -lc 'echo $APPID'
  check_mode: no
  register: app_id
{% endhighlight %}

And _boom_ it won't be skipped and now it gathers that variable even when it's doing a `check` run.
