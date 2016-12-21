---
layout: post
title: Сравнение IP-адресов в MySQL
date: 2015-09-07
tags: [mysql]
---

В MySQL есть две интересные функции: `INET_ATON()` и `INET_NTOA()`.

Они позволяют переводить IP-адреса в десятичный вид и обратно. Это очень удобно, если нужно выбрать из таблицы IP, принадлежащий определённой подсети.
Например:

```sql
SELECT
    ip AS stor_backup_ip
FROM
    `billing`.`servers_backup_interfaces`
WHERE
    (INET_ATON(ip) & (-1<<11)) = INET_NTOA("172.16.16.0")
AND
    name = "storage75";
```
