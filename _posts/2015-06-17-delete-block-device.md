---
layout: post
title: Удаление дисков из системы в Linux
modified: 2015-06-17
tags: [hardware]
---
Перед физическим извлечение дисков в Linux следует отключить их от SATA-контроллера:

{% highlight console %}
echo 1 > /sys/block/sdc/device/delete
{% endhighlight %}