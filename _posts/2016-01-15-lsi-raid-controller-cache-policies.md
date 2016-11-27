---
layout: post
title: Политики кэширования в RAID-контроллерах Avago (LSI)
modified: 2016-01-15
tags: [storage]
---
Разъяснение настроек кэширования данных в контроллерах LSI (оригинал по [ссылке](http://lists.us.dell.com/pipermail/linux-poweredge/2006-May/025738.html)):

When dealing with LSI controllers there are actually 3 caches to deal with. From the OMSA documentation (I have deleted all references to non-LSI ptions)

#### Read Policy:

The read policies indicate whether or not the controller should read sequential sectors of the logical drive when seeking data.

* **Read-Ahead**. When using read-ahead policy, the controller reads sequential sectors of the logical drive when seeking data. Read-ahead policy may improve system performance if the data is actually written to sequential sectors of the logical drive.
* **No-Read-Ahead**. Selecting no-read-ahead policy indicates that the controller should not use read-ahead policy.
* **Adaptive Read-Ahead**. When using adaptive read-ahead policy, the controller initiates read-ahead only if the two most recent read requests ccessed sequential sectors of the disk. If subsequent read requests access random sectors of the disk, the controller reverts to no-read-ahead olicy. The controller continues to evaluate whether read requests are accessing sequential sectors of the disk, and can initiate read-ahead if ecessary.


#### Write Policy:

The write policies specify whether the controller sends a write-request completion signal as soon as the data is in the cache or after it has been ritten to disk.

* **Write-Back**. When using write-back caching, the controller sends a write-request completion signal as soon as the data is in the controller ache but has not yet been written to disk. Write-back caching may provide improved performance since subsequent read requests can more quickly retrieve data from the controller cache than they could from the disk. Write-back caching also entails a data security risk, however, since a system failure could prevent the data from being written to disk even though the controller has sent a write-request completion signal. In this case, data may be lost. Other applications may also experience problems when taking actions that assume the data is available on the disk.
* **Write-Through**. When using write-through caching, the controller sends a write-request completion signal only after the data is written to he disk. Write-through caching provides better data security than write-back caching, since the system assumes the data is available only after it as been safely written to the disk.


#### Cache Policy:

The Direct I/O and Cache I/O cache policies apply to reads on a specific virtual disk. These settings do not affect the read-ahead policy. The cache policies are as follows:

* **Cache I/O**. Specifies that all reads are buffered in cache memory.
* **Direct I/O**. Specifies that reads are not buffered in cache memory. When using direct I/O, data is transferred to the controller cache and he host system simultaneously during a read request. If a subsequent read request requires data from the same data block, it can be read directly from the controller cache. The direct I/O setting does not override the cache policy settings. Direct I/O is also the default setting.
