---
layout: post
title: Автосоздание маршрутов при подключении к VPN на macOS
date: 2015-06-17
tags: [vpn, environment]
---
Не хочется весь трафик пускать через VPN.

Чтобы при подключении автоматически создавались только нужные маршруты до рабочих ресурсов, нужно указать их в файле с именем `/etc/ppp/ip-up`. Примерное содержимое файла:

```bash
#!/bin/sh
/sbin/route add confluence.example.com -interface $1
/sbin/route add monitor.example.com -interface $1
/sbin/route add staff.example.com -interface $1
```
