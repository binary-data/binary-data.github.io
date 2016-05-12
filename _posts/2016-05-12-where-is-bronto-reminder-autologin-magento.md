---
layout: post
title:  "Where is bronto reminder auto login feature located"
date:   2016-05-12 11:58:00 +0400
comments: true
tags: magento bronto
---
There is an service called [Bronto](http://bronto.com/), a marketing platform for e-commerce websites. It has it's own extension for Magento. This extension has an "auto login" or "silent login" feature: when user clicks on link in abandoned cart email, he gets redirected to his cart and logs in automatically.

You may wonder, where is exactly "auto login" happens. For Magento 1.9 with Bronto 2.4 it is located in `Bronto_Reminder_LoadController::indexAction`. Actual url where Bronto redirects looks like this: `http://site.com/reminder/load/index/message_id/0baa06eb00000000000000000000001b5fa3/id/eDRedi5YZGVbUEk9/?{some_more_params}`

It wasn't obvious for me at first so I decided to share this knowledge here. Hope it helps.

More on reminder emails in [bronto documentation](http://dev.bronto.com/prebuilt-integrations/magento/2-4/configuration/reminder-emails/)
