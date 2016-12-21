---
title: 'Из книги "Web Operations" - Data Assets'
date: 2016-10-28 12:00:00
tags: [book]
---
Продолжаю постить выдержки из книги "Web Operations: Keeping the Data on Time".

На этот раз речь пойдёт об одной полезной практике. Она заключается в составлении таблицы, каждая строка в которой - какой-то ресурс данных вашей компании, будь то база данных, пользовательские данные, репозитории с кодом. Штука в том, что в эту таблицы обязательно должны быть записаны и бэкапы. Лучше всего проиллюстрировать эту технику готовой таблицей из книги.

Таблица 14-5 из книги.
RTO - recovery-time objective, RPO - recovery-point objective.

> RTOs define the maximum length of time it should take to recover from any sort of disruption of service. Let’s say your expensive NAS appliance goes down, and part of your site depends on it. You’ll need to know how long the system can reasonably be unavailable before it has a serious negative impact on your product or users. It’s important that you talk with your product people to figure out how long the system can be down (and data inaccessible) before the business side of things starts to unravel. This is something you’ll want to know and agree upon before something bad happens.

> RPOs describe the maximum amount of data loss that is acceptable after a disruption of service. The amount of data loss is usually measured in time. Let’s say the crops database I described earlier is backed up nightly at 10:00. If the storage system melts down and loses all its data at 9:00 p.m., the only backup you have is from 10:00 the night before, and you will have lost about a day’s worth of data if you restore from that backup. This means the maximum RPO for this system is about 24 hours, the time elapsed between nightly backups.

|Location|System|System Type|Data type|RTO|RPO|Business impact|Data protection|
|San Diego|database1.sd|Storage system|Database|10 minutes|15 minutes|Users can’t harvest crops or plant new crops|Nightly backups; local replication;  application-consistent snapshots every hour; continuously back up transaction logs from master to VTL|
|San Diego|database2.sd|Storage system|Database|4 hours|2 hours|No local replica for primary crops database|Remote replica|
|New York|database3.ny|Storage system|Database|4 hours|2 hours|No remote replica for primary crops database|None|
|San Diego|vtl1.sd|VTL|Database backups and transaction logs|12 hours|24 hours|Can't back up or recover the crops database or transaction logs|None|
