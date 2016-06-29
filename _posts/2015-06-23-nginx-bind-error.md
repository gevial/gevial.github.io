---
layout: post
title: nginx не запускается, безуспешно пытается bind 0.0.0.0:80
modified: 2015-06-17
tags: [nginx, troubleshooting]
---
При запуске nginx столкнулся с ошибкой:

{% highlight console %}
* Checking nginx' configuration ...
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful                               [ ok ]
 * Starting nginx ...
nginx: [emerg] bind() to 0.0.0.0:80 failed (98: Address already in use)
{% endhighlight %}

При этом `listen 80;` в конфигах не указывается. А забиндить `0.0.0.0:80` nginx не смог, так как на 80 порту на `localhost` слушает Apache.
Оказалось, что в одном из конфигов в `/etc/nginx/ssl-enabled`, которые подключаются директивой `include`, не указана директива `listen` вовсе. И `nginx` брал для неё значение по умолчанию, то есть `0.0.0.0:80`