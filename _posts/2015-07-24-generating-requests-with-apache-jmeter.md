---
layout: post
title:  "Generating requests with Apache JMeter"
date:   2015-07-24 16:06:03
comments: true
tags: jmeter
---
# The problem

> **UPD 31 July 2015: For Django and python I now prefer to use [requests](http://docs.python-requests.org/en/latest/) library and Django management commands. With management commands it is easier to collect data required for generating requests. But I will leave the post here because it can be helpful for someone.**

I develop a web application. One of it's modules collects data from user request (user agent, device etc.) and shows visit statistics. While developing statistic module I needed to generate sample request data.

My requirements was:

 - ability to generate requests (of course)
 - ability to use external data sources (links from my application)
 - easy to run on my local machine
 - easy to configure

> TL;DR: I ended up with [Apache JMeter](http://jmeter.apache.org/). Although purpose of this tool is load testing, it helped me reach my goal quickly.

# The solution
Start with [downloading](http://jmeter.apache.org/download_jmeter.cgi) and [running](http://jmeter.apache.org/usermanual/get-started.html) JMeter.

First of all, we need to set up users. Add a [Thread Group](http://jmeter.apache.org/usermanual/component_reference.html#Thread_Group) from "Threads (Users)" section:

<a href="#" data-featherlight="/images/2015-07/threadgroup.jpg">![Thread Group](/images/2015-07/threadgroup.jpg)</a>

Thread group options depends on how many requests you want to generate. Then add [HTTP Request](http://jmeter.apache.org/usermanual/component_reference.html#HTTP_Request) from "Samplers":

<a href="#" data-featherlight="/images/2015-07/httprequest.jpg">![Thread Group](/images/2015-07/httprequest.jpg)</a>

There we are setting up our webservers host (`localhost` in my case) and path. See a `${link}` parameter in Path? I will explain it below.

Then we add a [HTTP Header Manager](http://jmeter.apache.org/usermanual/component_reference.html#HTTP_Header_Manager) from "Config Element":

<a href="#" data-featherlight="/images/2015-07/httpheader.jpg">![Thread Group](/images/2015-07/httpheader.jpg)</a>

Here you can add necessary headers. There is another variable `${userAgent}`.

Now we need to populate our variables. JMeter has a tool for that called [CSV Data Set Config](http://jmeter.apache.org/usermanual/component_reference.html#CSV_Data_Set_Config). It takes data from CSV file.

<a href="#" data-featherlight="/images/2015-07/csvlink.jpg">![Thread Group](/images/2015-07/csvlink.jpg)</a>

Here we define a filename, variable name that we used before and csv options, such as encoding and delimiter.

> Generate CSV file with my links was pretty easy with simple Django management command (because I use Django)

I also needed to use different browser User Agents, so I downloaded CSV list of them [here](http://sql.sh/2290-liste-user-agents) and created another CSV Data Set Config:

<a  href="#" data-featherlight="/images/2015-07/csvua.jpg">![Thread Group](/images/2015-07/csvua.jpg)</a>

Now our variables in HTTP Request and HTTP Header Manager will be populated with values from CSV file. I also set first row in CSV files to match variable names, but don't know if this even needed.

That's all! Now we can run our test case.

But one more thing: if you want to track requests you need to add listener from "Listener" group (I picked up "View Results in Table"):

<a  href="#" data-featherlight="/images/2015-07/listener.jpg">![Thread Group](/images/2015-07/listener.jpg)</a>

# Alternatives

JMeter is not the only one. I tried several things:

### [Tsung](http://tsung.erlang-projects.org/)
This one have a simple XML config, which I could populate with external data. But Tsung requires running it on a remote host, and this adds a lot of pain. But maybe I just didn't go deeper through docs.

### [Gatling.io](http://gatling.io/)
Gatling has a nice documentation, but it's config is written in it's own DSL. Remember, one of my requirements was "easy to configure", and I didn't want to spend my day learning yet another language (too lazy, eh). Also, quickstart section of docs told me that I need to manually collect an initial test case. It is a great feature for testing sites, but not for my goal.

### [ab (Apache Bench)](http://httpd.apache.org/docs/2.2/programs/ab.html)
Didn't find out how to configure it :(

Thanks for reading! Hope it helps.
