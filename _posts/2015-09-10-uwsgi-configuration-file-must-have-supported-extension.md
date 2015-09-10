---
layout: post
title:  "uWSGI configuration file must have supported extension"
date:   2015-09-10 16:59:00
comments: true
tags: uwsgi
---
I faced a problem when setting up uWSGI server. I wanted to run it in Emperor mode. I repeated steps from [official docs](http://uwsgi-docs.readthedocs.org/en/latest/Systemd.html), but vassal processes wasn't able to start. It seemed that uWSGI Emperor didn't see vassal config files.

Spend a whole bunch of hours, digging into that problem. Then I realized my vassal config haven't had any extension. I added `.ini` extension to config file and it worked. After restarting main uWSGI proccess, vassal proccess started immidiately.

Although `ini` files is default choice, uWSGI also [supports](http://uwsgi-docs.readthedocs.org/en/latest/Configuration.html) `json`, `xml` and `yaml` formats.

The point of this story is pretty simple: RTFM.
