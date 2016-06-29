---
layout: post
title: Хранимые процедуры и кодировка
modified: 2015-09-24
tags: [sql, mysql, performance]
---

Столкнулись с серьёзной деградацией производительности хранимых функций MySQL на реплике.

Выяснилось, что запросы к хранимой функции выполняются слишком долго. Анализ PROCESSLIST'а выявил, что на мастере и на реплике запросы, выполняющиеся внутри функции, выглядят по-рзному.

Мастер:

{% highlight console %}
mysql> SELECT SUM(cost) FROM ... LEFT JOIN ... WHERE customer_id = 'johndoe' AND end_date < NOW() AND NOT g.name <=> "certificate";
+-----------+
| SUM(cost) |
+-----------+
|        50 |
+-----------+
1 row in set (0.00 sec)

{% endhighlight %}

Реплика:

{% highlight console %}
mysql> SELECT SUM(cost) FROM ... LEFT JOIN ... WHERE customer_id = NAME_CONST('customer',_utf8'johndoe' COLLATE 'utf8_general_ci') AND end_date < NOW() AND NOT g.name <=> "certificate";
+-----------+
| SUM(cost) |
+-----------+
|        50 |
+-----------+
1 row in set (1.64 sec)

mysql>
{% endhighlight %}

На реплике происходит перевод аргумента функции в другую кодировку. Из-за того, что в запросе поле _customer_id_ сравнивается с результатом встроенной функции, а не с константой, не используется индекс. Из-за этого и происходит деградация производительности.

Причина оказалась в том, что база данных на реплике создана с другими CHARACTER SET и COLLATION, нежели чем на мастере (utf8 против koi8r).

Как временное решение пришлось изменить код функции на реплике, явно указав аргументу `customer` кодировку koi8r. Чтобы предотвратить появление таких ситуаций в будущем, надо лишь выполнить `ALTER SCHEMA billing CHARACTER SET = "koi8r" COLLATE = "koi8r_general_ci"`.

Ссылки по теме:

1. [ http://www.justincarmony.com/blog/2011/02/02/mysql-stored-procedure-name_const-and-character-sets/](http://www.justincarmony.com/blog/2011/02/02/mysql-stored-procedure-name_const-and-character-sets/)
