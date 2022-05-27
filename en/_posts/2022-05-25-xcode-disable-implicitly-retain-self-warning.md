---
layout: post
title:  "Disable Xcode's 'Block implicitly retains self' warning ⚠️"
lang: "en"
tags: objective-c clang xcode
---

GCD block seams not so popular but I find it useful.

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

will generate the following warning.

```Block implicitly retains 'self'; explicitly mention 'self' to indicate this is intended behavior```

The proper way to solve this is to make a weak reference of 'self' as Google search would say.

I don't ***** for my one-off prototyping code. This not a solution but it does the job.

{% comment %}
```
{% endcomment %}
{% highlight objc %}

#pragma clang diagnostic push
#pragma clang diagnostic ignored "-Wimplicit-retain-self"

*** Your code ***

#pragma clang diagnostic pop

{% endhighlight %}
{% comment %}
```
{% endcomment %}

Warnings disappears but the 'self' will stay retained. At your own risk.
