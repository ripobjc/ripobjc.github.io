---
layout: post
title:  "Disable Xcode's 'Block implicitly retains self' warning ⚠️"
lang: "en"
tags: objective-c clang xcode
---

 I find GCD blocks useful.

However, this kind of code

{% comment %}
```
{% endcomment %}
{% highlight objc %}

    auto some_func = ^() {
        NSLog(@"%@", _some_ivar);
    }

{% endhighlight %}
{% comment %}
```
{% endcomment %}

will generate a warning as below.

> Block implicitly retains 'self'; explicitly mention 'self' to indicate this is intended behavior

You should write `self->_some_ivar` or caputer a weak reference of `self`. This is what the internet teaches you.

It's too lengthly for one-off prototyping codes. Here's a less formal but useful workaround.

{% comment %}
```
{% endcomment %}
{% highlight objc %}

#pragma clang diagnostic push
#pragma clang diagnostic ignored "-Wimplicit-retain-self"

/* your code */

#pragma clang diagnostic pop

{% endhighlight %}
{% comment %}
```
{% endcomment %}

Warnings will disappear but the `self` stays retained. Not for the production codes.
