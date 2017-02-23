---
layout: post
title:  "How to find node name of a class for config.xml in Magento (easy way)"
date:   2017-02-23 17:50:00 +0400
comments: true
tags: magento
---
Let's assume we want to rewrite some core model (or resource model, or block, or helper). In order to do that we need to make changes in `config.xml` file of our module. In our case this is `Mage_Catalog_Model_Resource_Product_Flat_Indexer`:

```xml
<models>
    <catalog_resource>
        <rewrite>
            <product_flat_indexer>YourCompany_ExtensionName_Model_Resource_Product_Flat_Indexer</product_flat_indexer>
        </rewrite>
    </catalog_resource>
</models>
```

But it could be frustrating to figure out `product_flat_indexer` node name.
 
So we'll use [PHPStorm](https://www.jetbrains.com/phpstorm/) and [Magicento](http://magicento.com/) plugin for it (plugin is paid, but it worth it to install if you work with Magento). With both of that installed we'll just type `Mage::getResourceModel('');` and use autocomplete feature: 

<a href="#" data-featherlight="/images/2017-02/autocomplete.png">![Autocomplete](/images/2017-02/autocomplete.png)</a>

As you can see, the part after `catalog/` is what we are looking for.