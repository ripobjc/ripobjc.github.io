---
layout: post
title:  "Disable Xcode's 'Block implicitly retains self' warning ⚠️"
lang: "en"
tags: objective-c clang xcode
---

GCD block is not popular but useful.
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

This disable the warning.
The 'self' will be retained anyway, at your own risk.
