---
title: Настройка стека nginx – uwsgi – web2py в CentOS
tags: [nginx, python, uwsgi]
---
Как сообщает Википедия, WSGI – это простой и универсальный низкоуровневый интерфейс между веб-сервером и веб-приложением/фреймворком, написанном на Python’е.

В WSGI существуют понятия “server”, или “gateway”, и “application”, или “framework”. Наиболее распространённым middleware для реализации обмена через WSGI является uWSGI. Для веб-сервера uWSGI выступает в роли “application”, для приложения – в роли “server”.
nginx имеет нативную поддержку uwsgi, что не может не радовать.

В качестве “приложения” в моём случае будет выступать фреймворк web2py – очень удобная штука, позволяет быстро разрабатывать веб-приложения. Имеет встроенный Twitter bootstrap и DAL (Database Abstraction Layer).

Настройка nginx заключается в создании нового блока `server{}`, пример конфигурации для web2py:

```
server {
    listen          80;
    server_name     web2py.example.com;

    location ~* /(\w+)/static/ {
        root /var/www/web2py/applications/;
    }

    location / {
        uwsgi_pass      127.0.0.1:9001;
        include         uwsgi_params;
        uwsgi_param     UWSGI_SCHEME $scheme;
    }
}
```

Фактически, достаточно указать директиве `uwsgi_pass` сокет демона uWSGI (TCP/IP или Unix domain) и подключить директивы из файла `uwsgi_params`, входящего в стандартную поставку nginx.

Настройка uWSGI заключается в настройке родительского процесса, называемого Emperor, и одного или нескольких “вассалов” – пулов обработчиков. Надо лишь не забыть помимо самого uwsgi установить к нему плагин python:

```
yum install uwsgi-plugin-python
```

Дело в том, что uwsgi, как я уже говорил – middleware, бэкендом может служить не только Python, но и PHP, Perl, Ruby, Lua, Go и пр. Вообще uwsgi богат возможностями – поддерживает различные модели управления обработчиками (prefork, threaded, asynchronous/evented, green thread/coroutine), кластеризацию, мониторинг и т.д.

Вот пример конфигурационного файла “императора” и пула для web2py.

```ini
[uwsgi]
uid = uwsgi
gid = uwsgi
pidfile = /run/uwsgi/uwsgi.pid
emperor = /etc/uwsgi.d
stats = /run/uwsgi/stats.sock
emperor-tyrant = true
cap = setgid,setuid
plugins = python
```

```xml
<uwsgi>
    <uid>nginx</uid>
    <gid>nginx</gid>
    <plugin>python</plugin>
    <socket>127.0.0.1:9001</socket>
    <pythonpath>/var/www/web2py/</pythonpath>
    <module>wsgihandler</module>
    <processes>3</processes>
    <harakiri>60</harakiri>
    <reload-mercy>8</reload-mercy>
    <cpu-affinity>1</cpu-affinity>
    <max-requests>2000</max-requests>
    <limit-as>512</limit-as>
    <reload-on-as>256</reload-on-as>
    <reload-on-rss>192</reload-on-rss>
    <no-orphans/>
</uwsgi>
```

Теперь о подводных камнях.

* Владельцем файла настроек пула `/etc/uwsgi.d/web2py.xml` должен быть пользователь, указанный в теге , иначе вы увидите ошибку “invalid permissions”.
* Процессы uwsgi не завершаются по SIGTERM. Они лишь перечитывают конфиг. Завершаются же они по SIGQUIT. Конфигурационная опция `–die-on-term` инвертирует интерпретацию данных сигналов.
* Для работы фреймворка web2py требуется сделать символическую ссылку из `/var/www/web2py/handlers/wsgihandler.py` в корень фреймворка.
* Доступ к административной части web2py обязательно должен осуществляться по HTTPS.
* Хэш пароля администратора должен храниться в файле `parameters_<port>.py` в корне фреймворка, где – тот порт, через который осуществляется доступ к веб-приложению. Т.е. если uwsgi запущен на localhost:9001, но вы ставите его за nginx и подключаетесь по HTTPS, то файл должен называться `parameters_443.py`. В любом случае, корректное имя файла можно узнать из трассировки системных вызовов обработчика uwsgi.

Ссылки:
[http://uwsgi-docs.readthedocs.org http://www.web2py.com](http://uwsgi-docs.readthedocs.org http://www.web2py.com)
