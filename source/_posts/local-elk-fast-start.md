---
layout: post
title: Запуск ELK stack локально для быстрого анализа лог-файлов
date: 2015-07-14
tags: [logs, elk, apache]
---
Столкнулся с необходимостью проанализировать логи Apache хостингового сервера. Период - около недели. Логов много - почти 2 миллиона строк.

Самое удобное решение - развернуть на локальной машине ELK stack (Elasticsearch, Logstash, Kibana) и запихнуть туда логи. Делается это очень быстро.

1) Скачиваем ELK с [elastic.co](http://elastic.co) (версия Elasticsearch должна быть та, которая совпадает с Logstash. В моём случае это 1.5.2 - последнняя версия Logstash)

2)  Пишем конфиг для Logstash. Вот мой, для логов Apache:

```
input {
    file {
        path => [ "/home/gena/Desktop/galileo.log" ]
        sincedb_path => "/dev/null"
        start_position => "beginning"
    }
}
filter {
     grok {
         match => { "message" => "apache_access: %{IPORHOST:http_host} %{DATA:remote_addr} .* \[%{HTTPDATE:time_local}\] \"(?<request_method>[A-Z]+) %{DATA:request} HTTP\/[01]\.[0-9]\" (?<status>\d\d\d) (?<body_bytes_sent>.*) %{QS:http_referer} %{QS:http_user_agent}" }
     }
     date {
         match => [ "time_local", "dd/MMM/YYYY:HH:mm:ss Z" ]
     }
}
output {
    elasticsearch {
    }
}
```

Следует обратить внимание на опции `sincedb_path` и `start_position`. Если их не указать, можно впасть в долгие попытки понять, почему же не работает ввод из файла (если, к примеру, были ошибки в конфигурации и пришлось запускать Logstash несколько раз).

3) Запускаем Elastisearch и Logstash:

```
$ logstash-1.5.2/bin/logstash -f apache.conf
$ elasticsearch-1.5.2/bin/elasticsearch
```

4) Логи уже потекли в Elasticsearch. Теперь надо создать индекс `.kibana` для служебных нужд Kibana и включить для него Dynamic Mapping.
```
$ curl -XPUT localhost:9200/.kibana -d '{ "index.mapper.dynamic": true }'
```

5) Запускаем Kibana

```
$ kibana-4.1.1-linux-x64/bin/kibana
```

6) Открываем [http://localhost:5601](http://localhost:5601) (Kibana) и настраиваем шаблон для отображения индексов.

7) ...

8) PROFIT!!!

Теперь можно работать с логами по-человечески.
