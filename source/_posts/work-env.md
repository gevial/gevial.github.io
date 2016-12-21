---
title: Настройка рабочего окружения
date: 2016-07-01 12:00:00
tags: [environment]
---
Вот как я настраиваю рабочее окружение.

## Клавиша "#"
Мне не нужен символ номера (shift+3 на русской раскладке). Но я часто пользуюсь символом #. Это нумерованный список в Redmine плюс ссылки на задачи начинаются также с #. В Ubuntu 16.04 исправить недоразумение и сделать так, чтобы в русской раскладке также печатался символ #, можно, отредактировав файл `/usr/share/X11/xkb/symbols/ru`. Достаточно закомментировать строку 13 (это секция, где переопределяются значения сочетаний shift+цифра). Вот эту строку, если быть точным:

```
// Keyboard layouts for Russia.
// AEN <aen@logic.ru>
// 2001/12/23 by Leon Kanter <leon@blackcatlinux.com>
// 2005/12/09 Valery Inozemtsev <shrek@altlinux.ru>

// Windows layout
default  partial alphanumeric_keys
xkb_symbols "winkeys" {

    include "ru(common)"
    name[Group1]= "Russian";

//    key <AE03> { [           3,  numerosign  ] };
    key <AE04> { [           4,   semicolon  ] };
    key <AE05> { [           5,     percent  ] };
```

## Редактор
Лучший редактор для меня - Atom. Список модулей, которые я использую:

* autocomplete-python
* build
* busy
* file-icons
* git-plus
* hyperclick _(В сочетании с autocomplete-python даёт возможность Ctrl-кликнуть на символ в коде, чтобы перейти к его определению)_
* language-puppet
* linter
* linter-flake8
* linter-puppet-lint
* linter-rubocop
* minimap

## Build
Удобно писать код в Atom и тестировать его запуском на удалённом сервере через SSH. Для этого я написал build-сценарий для соответствующего модуля:

```
{
  "name": "Runscript",
  "cmd": "echo 'Choose target'",
  "sh": true,
  "targets": {
    "host_accessed_with_fabric": { "cmd": "fab -f /home/gena/rexec.py -H srvname runscript:\"{FILE_ACTIVE}\"" },
    "host_accessed_with_plain_ssh": { "cmd": "scp {FILE_ACTIVE} srvname:~/atom-build.script; ssh srvname ~g.aleksandrov/atom-build.script; ssh srvname rm ~g.aleksandrov/atom-build.script" },
    "local_python": { "cmd": "python {FILE_ACTIVE}" }
  }
}
```

## .ssh/config
Мы используем SSH-шлюз, доступ на сервера осуществляется через него. Для тестирования скриптов это не очень удобно, иногда нужен прямой доступ. Его можно получить, настроив SSH-клиент, вот конфиг:

```
Host gateway
    Hostname gateway.example.com
    User g.aleksandrov
    IdentityFile ~/.ssh/g.aleksandrov-tw_key

Host directaccess
    Hostname directaccess.example.com
    User root
    IdentityFile ~/.ssh/g.aleksandrov-tw_key

Host gitlab.example.com
    User git
    IdentityFile ~/.ssh/g.aleksandrov-tw_key

Host github.com
    User git
    IdentityFile ~/Dropbox/keys/github_main_key

Host !gateway* !directaccess* !gitlab.example.com !github.com !*.* *
    User root
    ProxyCommand ssh gateway -W %h:22
    IdentityFile ~/.ssh/id_rsa.admins
    StrictHostKeyChecking no
```
При таком конфиге доступ на любой сервер будет осуществляться через gateway. За исключением, кхм, исключений.

## Fabric
Для выполнения команд/скриптов на удалённых серверах я написал fabfile. Вот его шаблон:

```python
#!/usr/bin/python

from fabric.api import run, task, env, put, settings, hosts
from fabric.state import output


env.use_ssh_config = True
env.colorize_errors = True
env.warn_only = True
env.skip_bad_hosts = True
output['running'] = False


def get_srv_list(**kwargs):
    where = ''
    for arg in kwargs:
        if isinstance(kwargs[arg], int) or isinstance(kwargs[arg], float):
            where += '{} = {} AND '.format(arg, kwargs[arg])
        elif isinstance(kwargs[arg], str):
            where += '{} = "{}" AND '.format(arg, kwargs[arg])
    where = where[:-5]
    if not where:
        raise(ValueError('Query params not set!'))
    # srv_list = DBClient.select_rows(where)
    return srv_list


env.roledefs = {
    role1: ['srv1', 'srv2', 'srv3'],
}


@task
def runcmd(cmd):
    run(cmd)


@task
def runscript(script_path):
    with settings(warn_only=False):
        res = put(script_path, remote_path='/root')
    for script in res:
        run('chmod u+x {}'.format(script))
        run(script)
        run('rm {}'.format(script))


@task
def putfile(file_path, remote_path):
    put(file_path, remote_path=remote_path)


@task
@hosts('localhost')
def srvlist():
    output['status'] = False
    for role in env.roles:
        for srv in env.roledefs[role]:
            print(srv)
```
Пользуюсь им при помощи алиаса командной строки. Добавил в `~/.bashrc`:

```
alias rex='fab -f /home/g.aleksandrov/rexec.py'
```

## Mission Control в Unity
В macOS часто пользуюсь Mission Control ради просмотра всех открытых окон. Такую штуку можно сделать и в Unity.
Для вывода всех окон по наведению курсора в угол экрана в `ccsm` надо выставить настройку "Scaling" > "initiate_all_edge" (как-то так).
