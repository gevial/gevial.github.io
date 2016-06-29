---
layout: post
title: Отключение Zend Memory Manager
modified: 2015-06-17
tags: [PHP, Apache]
---
Интерпретатор PHP Zend Engine включает в себя такой компонент как Zend Memory Manager.
Таким образом, PHP сам управляет памятью, позволяя отслеживать выделения памяти для предотвращения утечек и ограничивать потребление памяти для каждого отдельно взятого сценария.
Иногда, например, при возникновении в сценарии ошибки сегментации, бывает полезным отключить Zend Memory Manager (в таком случае интерпретатор начинает использовать стандартные средства ОС для работы с памятью). Делается это установкой переменной окружения USE_ZEND_ALLOC в 0.

Если вы используете Apache, наиболее удобные способы установить данную переменную – добавить в init-скрипт веб-сервера строку

{% highlight console %}
export USE_ZEND_ALLOC=0
{% endhighlight %}

или указать в конфигурационном файле Apache директиву

{% highlight console %}
SetEnv USE_ZEND_ALLOC 0
{% endhighlight %}

Ссылки:
[http://www.php.net/manual/en/internals2.memory.php](http://www.php.net/manual/en/internals2.memory.php)