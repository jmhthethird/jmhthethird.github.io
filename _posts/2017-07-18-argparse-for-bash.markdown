---
layout: post
title: "Argparse for Bash"
date:   2017-07-18
categories: argparse bash
---
Still struggling a bit with bash scripting. I've been trying to build upon a previous colleagues `getops`, `case` and other bash-isms in their script to put in a few wanted features by the devs of my team.

Boy, do I miss python! I mean I've been thinking about rewriting this all in Python since the first time I looked at it. I haven't yet for a couple, hopefully good, reasons.

1. This is a good chance to learn some bash. Something I've been neglecting ever since I picked up Perl about 8 years ago.
2. It adds more dependencies to the system the script runs on. Currently, its just plain bash that executes fast and works on all our systems.
3. It is basically giving up and I'm very stubborn.

So with that in mind, I introduce to you my compromise:
[https://github.com/nhoffman/argparse-bash](https://github.com/nhoffman/argparse-bash)

Argparse for bash! Looks like someone was wonderful enough to take the arparse Python library and make a bash equivalent. I'm going to take a peek today and see if this is as plug-and-play as the Python version. If so this would be a great intermediary. It'll make managing the dependencies between different flags within the script much easier and auto-build the help text whenever changes are made.

-Cheers-