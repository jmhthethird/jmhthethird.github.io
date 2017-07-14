---
layout: post
title:  "Bash getopts"
date:   2017-07-14
categories: bash
---
If you want to pass parameters to a CLI tool getopts seems to be a great way to go.

To get a CLI that interfaces like:
`$>script.sh -u jflex -h localhost -v`

Do something like:
{% highlight bash %}
while getops "u:h:v" OPT; do
  case "${OPT}" in
    u)
      do_something()
      ;;
    h)
      do_something_else()
      ;;
    v)
      make_things_verbose()
      ;;
  esac
done
{% endhighlight %}

From a coworker, "ain't called bash for no reason. if you don't want to bash your head against your desk then you ain't doing it right!"

~GLHF