---
layout: post
title:  "Subscribing on events in Magento custom models"
date:   2016-07-14 17:00:00 +0400
comments: true
tags: magento magento-models
---
To subscribe on events only for your custom models in Magento you need to redefine `$_eventPrefix` property of your model like this:

```php
<?php

class Vendor_Module_Model_Something extends Mage_Core_Model_Abstract
{
    protected $_eventPrefix = 'custom_event_prefix';
    
    ...
}
```

Then you can subscribe on your events in `etc/config.xml` file of your module as usual:

```xml
<?xml version="1.0"?>
<config>
    
    ...

    <global>

    ...

        <events>
            <custom_event_prefix_save_after>
                <observers>
                    <your_identifier>
                        <class>Vendor_Module_Model_Observer</class>
                        <method>yourMethodName</method>
                    </your_identifier>
                </observers>
            </custom_event_prefix_save_after>
        </events>
        
    ...

    </global>

...

</config>
```

### What model events are available?

Here is a list of available events for a model in Magento 1.9 (assuming `PREFIX` will be replaced by your prefix):

```
PREFIX_load_before
PREFIX_load_after
PREFIX_save_commit_after
PREFIX_save_before
PREFIX_save_after
PREFIX_delete_before
PREFIX_delete_after
PREFIX_delete_commit_after
PREFIX_clear
```

Explanation of some:

* **PREFIX_save_commit_after** this event happens after save transaction commit in resource model
* **PREFIX_delete_commit_after** happens after delete transaction commit in resource model
* **PREFIX_clear** happens before clearing data in model's `clearInstance()` method. Rarely used

Other events are pretty obvious.

### What about standard models?

Magento standard models already have their own event prefixes. If you need them, just do a search for `$_eventPrefix = ` in `app/code/core/Mage` folder.
