---
layout: post
title: Segfault в MySQL при старте демона
modified: 2015-06-17
tags: [MySQL, troubleshooting]
---
Перезагрузка сервера посредством нажатия кнопки “Reset” чревата последствиями.

В частности, файловые системы или отдельные файлы могут остаться в неконсистентном состоянии, так как ядро или RAID-контроллер не имеют возможности слить буферы записи или writeback-кэш.

Однажды после жёсткой перезагрузки сервера демон MySQL перестал запускаться – при старте в нём возникала ошибка сегментации.
Первый шаг любой диагностики – просмотр логов демона.

{% highlight console %}
140101  2:04:00 [Note] Plugin 'FEDERATED' is disabled.
InnoDB: The InnoDB memory heap is disabled
InnoDB: Mutexes and rw_locks use GCC atomic builtins
InnoDB: Compressed tables use zlib 1.2.3
140101  2:04:00  InnoDB: Initializing buffer pool, size = 512.0M
140101  2:04:00  InnoDB: Completed initialization of buffer pool
140101  2:04:00  InnoDB: highest supported file format is Barracuda.
InnoDB: Log scan progressed past the checkpoint lsn 9612902337
140101  2:04:00  InnoDB: Database was not shut down normally!
InnoDB: Starting crash recovery.
InnoDB: Reading tablespace information from the .ibd files...
InnoDB: Restoring possible half-written data pages from the doublewrite
InnoDB: buffer...
InnoDB: Doing recovery: scanned up to log sequence number 9612905565
131231  5:33:44  InnoDB: Starting an apply batch of log records to the database...
InnoDB: Progress in percents: 59 60 61 62 63 64 65 66 67 68 69 70 71 72 73 74 75 76 77 78 79 80 81 82 83 84 85 86 87 88 89 90 91 92 93 94 95 96 97 98 99 131231  5:33:44 -
mysqld got signal 11 ;
This could be because you hit a bug. It is also possible that this binary or one of the libraries it was linked against is corrupt, improperly built, or misconfigured. This error can also be caused by malfunctioning hardware.
We will try our best to scrape up some info that will hopefully help diagnose the problem, but since we have already crashed, something is definitely wrong and this may fail.

key_buffer_size=16777216
read_buffer_size=16777216
max_used_connections=0
max_threads=500
threads_connected=0
It is possible that mysqld could use up to
key_buffer_size + (read_buffer_size + sort_buffer_size)*max_threads = 12309856 K bytes of memory
Hope that's ok; if not, decrease some variables in the equation.

Thread pointer: 0x0
Attempting backtrace. You can use the following information to find out where mysqld died. If you see no messages after this, something went terribly wrong...
stack_bottom = (nil) thread_stack 0x40000
/usr/sbin/mysqld(my_print_stacktrace+0x24) [0x862214]
/usr/sbin/mysqld(handle_segfault+0x34d) [0x5a391d]
/lib/libpthread.so.0(+0xf010) [0x7fcc80a87010]
/usr/sbin/mysqld() [0x74f1d0]
/usr/sbin/mysqld() [0x7508ea]
/usr/sbin/mysqld() [0x73ed6f]
/usr/sbin/mysqld() [0x7405ab]
/usr/sbin/mysqld() [0x7c848b]
/usr/sbin/mysqld() [0x7f337a]
/usr/sbin/mysqld() [0x789121]
/lib/libpthread.so.0(+0x68c4) [0x7fcc80a7e8c4]
/lib/libc.so.6(clone+0x6d) [0x7fcc7fc27fdd]
The manual page at http://dev.mysql.com/doc/mysql/en/crashing.html contains information that should help you find out what is causing the crash.
{% endhighlight %}

Видим, что ошибка сегментации возникает при воспроизведении redo-логов. В таком случае надо запустить демон с `innodb_force_recovery = 6` (включается SRV_FORCE_NO_LOG_REDO). После этого выполнить `SET GLOBAL innodb_fast_shutdown = 0` и остановить MySQL штатными средствами. Затем отключить `innodb_force_recovery`. После этого демон запустится.

И, конечно, никогда не перезагружайте сервера при помощи “Reset” ;)

Ссылки:
[http://www.mysqlperformanceblog.com/2006/08/04/innodb-double-write/](http://www.mysqlperformanceblog.com/2006/08/04/innodb-double-write/)
[http://www.mysqlperformanceblog.com/2011/02/03/how-innodb-handles-redo-logging/](http://www.mysqlperformanceblog.com/2006/08/04/innodb-double-write/)
[http://dev.mysql.com/doc/refman/5.5/en/forcing-innodb-recovery.html](http://www.mysqlperformanceblog.com/2006/08/04/innodb-double-write/)