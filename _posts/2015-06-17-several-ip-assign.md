---
layout: post
title: Назначение интерфейсу нескольких IP-адресов при загрузке
modified: 2015-06-17
tags: [network]
---
В RHEL-совместимых дистрибутивах конфигурация сетевых интерфейсов хранится в файлах `/etc/sysconfig/network-scripts/ifcfg-<iface>`. Пример файла `ifcfg-eth0`:
{% highlight console %}
DEVICE="eth0"
IPADDR0="1.2.3.4"
NETMASK0="255.255.255.0"
BROADCAST0="1.2.3.255"
GATEWAY="1.2.3.1"
IPADDR1="5.6.7.8"
NETMASK1="255.255.0.0"
BROADCAST1="5.6.255.255"
ONBOOT="yes"
{% endhighlight %}
При такой конфигурации интерфейс eth0 при загрузке получит два сетевых адреса с разной маской, также в таблицу маршрутизации будет добавлена указанная запись для шлюза по умолчанию.
В Debian и дистрибутивах, основанных на нём, конфигурация хранится в одном файле - `/etc/network/interfaces`. Debian Handbook рекомендует добавлять в этот файл дополнительные секции с параметрами псевдонимов. Пример файла:

{% highlight console %}
auto eth0
iface eth0 inet static
address 11.2.3.4
netmask 255.255.255.0
gateway 11.2.3.1
dns-nameservers 8.8.8.8 8.8.4.4

iface eth0 inet static
address 192.168.1.66
netmask 255.255.255.0

auto lo
iface lo inet loopback
{% endhighlight %}

Наиболее удобный способ предоставляет дистрибутив Gentoo. Примерное содержание файла конфигурации сети `/etc/conf.d/net`:
{% highlight console %}
config_eth0=(
    "1.2.3.4/24"
    "5.6.7.8/16"
)
routes_eth0=( "default via 1.2.3.1" )
{% endhighlight %}