---
layout: post
title:  "Django migration error: No installed app with label 'myapp'"
date:   2015-09-15 14:25:00
comments: true
tags: django migrations
---
## How to get rid of this error
This usually happens when you use models from one app in another, as ForeignKey or ManyToMany etc. Django cannot add dependencies in migrations to other apps automatically, so you should manually adjust your migration file and add a dependency to other app's migration, as described in [Django docs](https://docs.djangoproject.com/en/dev/topics/migrations/#accessing-models-from-other-apps):

```python
class Migration(migrations.Migration):

    dependencies = [
        ('app1', '0001_initial'),
        # added dependency to enable using models from app2 in move_m1
        ('app2', '0004_foobar'),
    ]

    operations = [
        migrations.RunPython(move_m1),
    ]
```
