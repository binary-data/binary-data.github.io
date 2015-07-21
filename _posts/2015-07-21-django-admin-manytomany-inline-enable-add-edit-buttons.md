---
layout: post
title:  "Django admin: how to enable add/edit buttons for many-to-many model relationships"
date:   2015-07-21 12:14:03
tags: django-1.8 django-admin django-models
---
It was pretty easy to add an inline for many-to-many model relationship Django admin, like it was mentioned in the [Django documentation](https://docs.djangoproject.com/en/dev/ref/contrib/admin/#working-with-many-to-many-models).

{% highlight python %}
from django.contrib import admin

class MembershipInline(admin.TabularInline):
    model = Group.members.through

class PersonAdmin(admin.ModelAdmin):
    inlines = [
        MembershipInline,
    ]

class GroupAdmin(admin.ModelAdmin):
    inlines = [
        MembershipInline,
    ]
    exclude = ('members',)
{% endhighlight %}

Models:

{% highlight python %}
from django.db import models

class Person(models.Model):
    name = models.CharField(max_length=128)

class Group(models.Model):
    name = models.CharField(max_length=128)
    members = models.ManyToManyField(Person, related_name='groups')
{% endhighlight %}

The trick is that line in `admin.py`:

{% highlight python %}
model = Group.members.through
{% endhighlight %}

But what wasn't unclear to me is why there is no add/edit buttons on inline items in the Django admin interface, so I couldn't add or edit any of related model's objects.

My `models.py` (simplified):

{% highlight python %}
class OfferGeo(models.Model):
    price = models.PositiveIntegerField()
    goal = models.PositiveIntegerField()

class Offer(models.Model):
    title = models.CharField(max_length=200)
    description = models.TextField()
    geo = models.ManyToManyField(OfferGeo, related_name='offers')
{% endhighlight %}

My `admin.py` (before fix):

{% highlight python %}
from django.contrib import admin
from dashboard.models import *

class GeoInline(admin.TabularInline):
    model = Offer.geo.through

@admin.register(Offer)
class OfferAdmin(admin.ModelAdmin):
    inlines = (GeoInline,)
    exclude = ('geo',) # Excluding field to hide unnecessary field, as mentioned in the docs
{% endhighlight %}

My mistake was that I didn't register related model `OfferGeo` in `admin.py`. After adding `admin.site.register(OfferGeo)` add/edit buttons appeared.

Final `admin.py` looks like this:

{% highlight python %}
from django.contrib import admin
from dashboard.models import *

admin.site.register(OfferGeo)

class GeoInline(admin.TabularInline):
    model = Offer.geo.through

@admin.register(Offer)
class OfferAdmin(admin.ModelAdmin):
    inlines = (GeoInline,)
    exclude = ('geo',) # Excluding field to hide unnecessary field, as mentioned in the docs
{% endhighlight %}

Hope this helps someone else.