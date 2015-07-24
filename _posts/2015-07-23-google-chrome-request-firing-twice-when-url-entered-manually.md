---
layout: post
title:  "Attention: Google Chrome request firing twice when url entered manually"
date:   2015-07-23 12:14:03
comments: true
tags: google-chrome browsers
---
I was writing a simple Django view. That view collected data from user request. During the testing I found a problem: data was collecting twice.

I tested my view by copy-pasting url to address bar of Google Chrome. And before I hit "Enter" request apeared in server log.

I have also tested in Firefox and Opera - extra requests did not appear.

> The best solution is to avoid changing application state in GET requests. More info at [StackExchange](http://programmers.stackexchange.com/questions/188860/why-shouldnt-a-get-request-change-data-on-the-server) 