---
title: "Локфайлы в Python"
date: 2016-12-11 15:07:33
tags: programming, python, ipc
---
Появилась необходимость исключить параллельный запуск скрипта на Python. Вспомнил про пакет `lockfile`, однако выяснилось, что он находится в статусе deprecated.

На странице проекта есть рекомендация использовать пакет [fasteners](https://pypi.python.org/pypi/fasteners). Он более сложный, направлен на синхронизацию между потоками или процессами. Однократный запуск с его помощью реализовать легко. При запуск пытаемся лочить файл. Не удалось - выходим.

Пример:
```python
lock = fasteners.process_lock.InterProcessLock(LOCKFILE)
if not lock.acquire(blocking=False):
    hist_logger.error(
        'Unable to acquire lock on {}. Maybe another instance is running.'
        .format(LOCKFILE)
    )
    sys.exit(os.EX_TEMPFAIL)
```
