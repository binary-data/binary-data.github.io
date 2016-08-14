---
layout: post
title:  "Overriding base magento layout with local.xml file"
date:   2016-08-14 11:50:00 +0400
comments: true
tags: magento magento-layout
---
Sometimes you need to redefine Magento's base layout entries. But since you should never edit Magento core files, there must be other way to do this.

Solution is simple: you need to create `local.xml` file in your theme's `layout` folder (e.g. `app/design/frontend/yourpackage/yourtheme/layout/local.xml`) and override required entries there.

Real example: we want to override template that is defined in base layout in `app/design/frontend/base/default/layout/persistent.xml`:

```xml
<?xml version="1.0"?>
<layout version="0.1.0">

<!-- ... skip ... -->

    <checkout_onepage_index>
        
        <!-- ... skip ... -->
        
        <reference name="checkout.onepage.billing">
            <action method="setTemplate"><template>persistent/checkout/onepage/billing.phtml</template></action>
            <block type="persistent/form_remember" name="persistent.remember.me" template="persistent/remember_me.phtml" />
            <block type="core/template" name="persistent.remember.me.tooltip" template="persistent/remember_me_tooltip.phtml" />
        </reference>
    </checkout_onepage_index>

<!-- ... skip ... -->    
    
</layout>
```

So we create `local.xml` file in our theme's layout folder if it is not already there and put our override in it:

```xml
<?xml version="1.0"?>
<layout version="0.1.0">

    <checkout_onepage_index>
        <reference name="checkout.onepage.billing">
            <action method="setTemplate"><template>checkout/onepage/billing.phtml</template></action>
        </reference>
    </checkout_onepage_index>

</layout>
```

And voila: we have just replaced `persistent/checkout/onepage/billing.phtml` with our `checkout/onepage/billing.phtml`