---
layout: post
title: Объяснение работы range access method и ICP в MySQL
link: http://jorgenloland.blogspot.ru/2011/08/mysql-range-access-method-explained.html
modified: 2016-01-12
tags: [DB, performance]
---
В статье объясняется, почему при использовании композитного индекса вот такой запрос будет использовать весь индекс:
{% highlight sql %}
SELECT * FROM orders WHERE customer_id = 2 AND value > 1000;
{% endhighlight %}
а такой:
{% highlight sql %}
SELECT * FROM orders WHERE customer_id < 2 AND value = 1000;
{% endhighlight %}
лишь первую его часть.

В MySQL 5.6 реализован [Index Condition Pushdown](http://jorgenloland.blogspot.co.uk/2012/03/index-condition-pushdown-to-rescue.html), который помогает в ряде случаев обойти описанное ограничение.
