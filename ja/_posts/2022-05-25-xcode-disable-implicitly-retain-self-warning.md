---
layout: post
title:  "Xcode の Block implicitly retains self 警告を無効にする ⚠️"
lang: "ja"
tags: objective-c clang xcode
---

GCD ブロック、便利だよね．不人気だけど．
ただこういうの

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


```Block implicitly retains 'self'; explicitly mention 'self' to indicate this is intended behavior```

と怒られる．

{% comment %}
```
{% endcomment %}
{% highlight objc %}

#pragma clang diagnostic push
#pragma clang diagnostic ignored "-Wimplicit-retain-self"

〜〜コード〜〜

#pragma clang diagnostic pop

{% endhighlight %}
{% comment %}
```
{% endcomment %}

以上の様に挟み込めば出なくなる．
self は retain されてしまうので自己責任で．