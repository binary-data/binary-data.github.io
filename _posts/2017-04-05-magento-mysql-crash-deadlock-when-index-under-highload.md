---
layout: post
title:  "Magento slow or crashes MySQL under high load: possible reason"
date:   2017-04-05 18:50:00 +0400
comments: true
tags: magento mysql 
---
Sometimes your Magento 1 website starts slow down or even crashing for no reason if load becomes higher than average.

Once we had exactly this situation with our Magento installation. When load became higher, website started to slow down periodically, or even crash. MySQL was crashing. We also had MySQL deadlocks as a bonus.
  
We started to debug it. First, we had a script running by cron. Crash usually happened after this script starts. Script was using MySQL and was making select queries to many Magento tables.

At first we thought that this script was responsible for crashes. We even optimized its queries and made it run about ten times faster. But all in vain, MySQL was still crashing time to time.

We started to check all queries that were running near crash time. And then we saw strange queries:


{% highlight sql %}
ALTER TABLE `cataloginventory_stock_status` DISABLE KEYS;
{% endhighlight %}

and

{% highlight sql %}
ALTER TABLE `cataloginventory_stock_status` ENABLE KEYS;
{% endhighlight %}

So we started checking where it came from. And then things became more clear.

In Magento, product stock status is updated every every time someone makes an order. And this causing reindex of a stock statuses. And reindex in turn causes disabling indexes on tables to make index process faster. It happens in abstract indexer parent class `Mage_Index_Model_Indexer_Abstract::disableKeys` and `Mage_Index_Model_Indexer_Abstract::enableKeys`.

{% highlight ruby %}
    /**
     * Disable resource table keys
     *
     * @return Mage_Index_Model_Indexer_Abstract
     */
    public function disableKeys()
    {
        if (empty($this->_resourceName)) {
            return $this;
        }

        $resourceModel = $this->getResource();
        if ($resourceModel instanceof Mage_Index_Model_Resource_Abstract) {
            $resourceModel->useDisableKeys(true);
            $resourceModel->disableTableKeys();
        }

        return $this;
    }

    /**
     * Enable resource table keys
     *
     * @return Mage_Index_Model_Indexer_Abstract
     */
    public function enableKeys()
    {
        if (empty($this->_resourceName)) {
            return $this;
        }

        $resourceModel = $this->getResource();
        if ($resourceModel instanceof Mage_Index_Model_Resource_Abstract) {
            $resourceModel->useDisableKeys(true);
            $resourceModel->enableTableKeys();
        }

        return $this;
    }
{% endhighlight %}

But! MySQL's `DISABLE KEYS` and `ENABLE KEYS` [works only for MyISAM tables](https://dev.mysql.com/doc/refman/5.7/en/alter-table.html#alter-table-index). `cataloginventory_stock_status` table is not one of them, it has "InnoDB" engine, like most tables in Magento. So those queries has no positive effect. Maybe they are just oblivious since time when tables were or were planned to be MyISAM.

We checked that those queries were reason of crashing by running them when our heavy cron script just started:

{% highlight sql %}
ALTER TABLE `cataloginventory_stock_status` DISABLE KEYS;ALTER TABLE `cataloginventory_stock_status` ENABLE KEYS;
{% endhighlight %}

After running it MySQL crashed instantly.

So we commented out those queries and after that MySQL stopped crashing. 
 
Since I am not an expert in MySQL internals, I still do not know what exactly caused this MySQL behavior. And why it happened only when load was above our usual load. Any explanations appreciated.

Magento version we were using was 1.9.1.0, but latest 1.9 version has the same code at the moment of writing.