---
layout: post
title: Объяснение работы range access method и ICP в MySQL
link: http://jorgenloland.blogspot.ru/2011/08/mysql-range-access-method-explained.html
date: 2016-01-12 12:00:00
tags: [mysql, performance]
---
В статье объясняется, почему при использовании композитного индекса вот такой запрос будет использовать весь индекс:

```sql
SELECT * FROM orders WHERE customer_id = 2 AND value > 1000;
```
а такой:

```sql
SELECT * FROM orders WHERE customer_id < 2 AND value = 1000;
```
лишь первую его часть.

В MySQL 5.6 реализован [Index Condition Pushdown](http://jorgenloland.blogspot.co.uk/2012/03/index-condition-pushdown-to-rescue.html), который помогает в ряде случаев обойти описанное ограничение.
