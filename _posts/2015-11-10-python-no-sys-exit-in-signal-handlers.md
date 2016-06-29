---
layout: post
title: Настройка логирование в Python
modified: 2015-12-19
tags: [python]
---
Архитектура логирования в Python описана в [PEP 282](https://www.python.org/dev/peps/pep-0282/). В состав стандартной библиотеки входит модуль `logging`.
Пример настройки логирования для скрипта. Вывод сообщений уровня DEBUG и выше идёт в файл, вывод сообщений уровня INFO и выше - на консоль.

```python
import logging

logger = logging.getLogger(__name__)
logger.setLevel(logging.DEBUG)

console = logging.StreamHandler()
console.setLevel(logging.INFO)
console.setFormatter(logging.Formatter('[%(levelname)s] %(message)s'))
logger.addHandler(console)

logfile = logging.FileHandler(LOGFILE)
logfile.setLevel(logging.DEBUG)
logfile.setFormatter(logging.Formatter('%(asctime)s [%(levelname)s] %(message)s',
                                       datefmt='[%d.%m.%Y - %H:%M:%S]'))
logger.addHandler(logfile)
```

Начиная с Python 2.7 настройки `logging` можно описать в словаре. Это намного более удобный способ, хоть и менее гибкий:

```python
import logging
from logging.config import dictConfig

logging_config = dict(
    version=1,
    formatters={
        'console': {
            'format': '[%(levelname)s] %(message)s'
        },
        'file': {
            'format': '%(asctime)s [%(levelname)s] %(message)s',
            'datefmt': '[%d.%m.%Y - %H:%M:%S]'
        }
    },
    handlers={
        'console': {
            'class': 'logging.StreamHandler',
            'formatter': 'console',
            'level': logging.INFO
        },
        'file': {
            'class': 'logging.FileHandler',
            'formatter': 'file',
            'filename': logfile,
            'level': logging.DEBUG
        }
    },
    loggers={
        'root': {'handlers': ['console', 'file'], 'level': logging.DEBUG}
    }
)

dictConfig(logging_config)
logger = logging.getLogger('root')
```

Более подробно о логировании в Python написано тут: [Hitchhiker's Guide to Python](http://docs.python-guide.org/en/latest/writing/logging/)
