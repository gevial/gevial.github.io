---
title: 'Граф зависимостей ресурсов в Puppet'
date: 2017-03-17 12:09:29
tags: [puppet, infrastructure, troubleshooting]
---
При настройке нового хоста наткнулся на циклическую зависимость в каталоге.
```
root@influxdb1.example.com:~# puppet agent --test
Info: Retrieving pluginfacts
Info: Retrieving plugin
Device "eth1" does not exist.
Could not retrieve fact='tw_mysql_basedir', resolution='<anonymous>': No such file or directory - /var/lib/mysql
Info: Caching catalog for influxdb1.example.com
Info: Applying configuration version '79f72b0'
Error: Failed to apply catalog: Found 1 dependency cycle:
(Anchor[apt::source::acmecorp-local] => Apt::Source[acmecorp-local] => Class[Acme_apt_sourcelist] => Stage[first] => Stage[second] => Class[Acme_common_deb] => Exec[default-kernel-delete] => Class[Acme_common_deb] => Stage[second] => Stage[main] => Class[Influxdb::Repo::Apt] => Apt::Source[repos.influxdata.com] => File[repos.influxdata.com.list] => Exec[apt_update] => Class[Apt::Update] => Stage[first])
Try the '--graph' option and opening the resulting '.dot' file in OmniGraffle or GraphViz
root@influxdb1.example.com:~#
```
Агент заботливо подсказал, как проще разобраться с проблемой. Достаточно дать агенту опцию `--graph`, и он сгенерирует dot-файл с графом зависимостей проблемных ресурсов.
```
root@influxdb1.example.com:~# puppet agent --test --graph
Info: Retrieving pluginfacts
Info: Retrieving plugin
Device "eth1" does not exist.
Could not retrieve fact='tw_mysql_basedir', resolution='<anonymous>': No such file or directory - /var/lib/mysql
Info: Caching catalog for influxdb1.example.com
Info: Applying configuration version '79f72b0'
Error: Failed to apply catalog: Found 1 dependency cycle:
(Anchor[apt::source::acmecorp-local] => Apt::Source[acmecorp-local] => Class[Acme_apt_sourcelist] => Stage[first] => Stage[second] => Class[Acme_common_deb] => Exec[default-kernel-delete] => Class[Acme_common_deb] => Stage[second] => Stage[main] => Class[Influxdb::Repo::Apt] => Apt::Source[repos.influxdata.com] => File[repos.influxdata.com.list] => Exec[apt_update] => Class[Apt::Update] => Stage[first])
Cycle graph written to /var/lib/puppet/state/graphs/cycles.dot.
root@influxdb1.example.com:~#
```
Этот файл можно забрать на свою тачку и превратить в, например, PNG следующей командой:
```
dot -Tpng -o cycle.png -Lg Desktop/cycles.dot
```
Команда `dot` - часть пакета `graphviz`. Если вы работаете на Маке, то вместо этого можно воспользоваться [OmniGraffle](https://www.omnigroup.com/omnigraffle/).
