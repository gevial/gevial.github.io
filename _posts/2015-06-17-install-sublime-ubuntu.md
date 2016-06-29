---
layout: post
title: Установка Sublime Text в Ubuntu
description: "Установка Sublime и плагинов к нему."
modified: 2015-06-17
tags: [sublime, ubuntu]
---
Sublime Text – очень удобный кроссплатформенный редактор кода.

На конец 2013 год стабильной является версия 2, однако третья версия также доступна для использования. В Ubuntu, как и в любом дистрибутиве Linux, наиболее удобным способом установки является установка из репозитория. Ребята из команды WebUpd8 регулярно делают сборки Sublime Text и выкладывают у себя в PPA.
Итак, для установки Sublime Text в Ubuntu нужно выполнить следующее:

{% highlight console %}
sudo add-apt-repository ppa:webupd8team/sublime-text-3
sudo apt-get update
sudo apt-get install sublime-text-installer
{% endhighlight %}

После этого в Unity появится значок редактора.