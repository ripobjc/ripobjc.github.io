---
layout: post
title:  "Xcode の Block implicitly retains self 警告を無効にする方法 ⚠️"
lang: "ja"
tags: objective-c clang xcode
---

いまさら GCD ブロック、便利だよね．あまり人気なさそうだけど…

ただこういうのは

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


> Block implicitly retains 'self'; explicitly mention 'self' to indicate this is intended behavior

と怒られる．

`self->_some_ivar` と書いたり、 `self` の weak リファレンスを作ってキャプチャーするのが正しい．ググるとそればっか出てくる．

使い捨てのコードにいちいちそんな事してられないので、以下は正しくない便利な方法．

{% comment %}
```
{% endcomment %}
{% highlight objc %}

#pragma clang diagnostic push
#pragma clang diagnostic ignored "-Wimplicit-retain-self"

/* コード */

#pragma clang diagnostic pop

{% endhighlight %}
{% comment %}
```
{% endcomment %}

self は retain されたままなになるので自己責任で．