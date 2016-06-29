---
layout: post
title: Неприятная особенность модуля Puppet concat
modified: 2015-07-17
tags: [puppet, troubleshooting]
---
Я потратил два часа в поисках причины неправильного порядка фрагментов в результирующем файле.

В документации к популярному и поддерживаемому Puppet Labs [модулю Puppet concat](https://forge.puppetlabs.com/puppetlabs/concat) говорится, что значение параметра `order` для дефайна `concat::fragment` может быть

> a string (recommended) or an integer

По факту (по крайней мере в Puppet 3.7) integer не сработает. Нужно указывать string. Ниже пример.

Плохо:

```puppet
concat::fragment { 'opening':
  target  => $::dummy_module::conf::conf_path,
  order   => 1,
  content => "databases = {\n",
}
```

Хорошо:

```puppet
concat::fragment { 'opening':
  target  => $::dummy_module::conf::conf_path,
  order   => '01',
  content => "databases = {\n",
}
```
