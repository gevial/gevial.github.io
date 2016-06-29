---
layout: post
title: Ошибка puppet cert - header too long
modified: 2015-10-12
tags: [puppet, troubleshooting]
---

Вызов `puppet cert list` завершается с неочевидной ошибкой:

```
root@puppet.example.com:~# puppet cert list
Error: header too long
root@puppet.example.com:~#
```

Наиболее частая причина - пустой запрос на подпись сертификата. В таком случае нам поможет пара команд:

```
root@puppet.example.com:~# cd /var/lib/puppet/ssl/ca/requests
root@puppet.example.com:/var/lib/puppet/ssl/ca/requests# find . -type f -empty
./bogushost.example.com.pem
root@puppet.example.com:/var/lib/puppet/ssl/ca/requests# rm ./bogushost.example.com.pem
```

Можно, конечно, запустить `find` с ключом `-delete`, но тогда вы не узнаете, какой хост отправил некорректный запрос.
