---
layout: post
title:  "Xcode の Block implicitly retains self 警告を無効にする ⚠️"
lang: "ja"
tags: objective-c clang xcode
---

GCD ブロック、便利だよね．いまいち人気なさそうだけど…

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

といちいち怒られる．

正しい解決方法は self の weak リファレンスを作って、それをキャプチャーする．ググると出てくる．

使い捨てのコードにそんな事やってられないので

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

以上の様に挟み込めば黙っってくれる．

self は retain されたままなので自己責任で．