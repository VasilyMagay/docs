---
title: Управление резервными копиями (снапшотами) Elasticsearch
description: 'Сервис Elasticsearch позволяет использовать механизм снапшотов Elasticsearch для управления резервными копиями данных. Для работы со снапшотами используется публичный API Elasticsearch, а для их хранения — бакет в Object Storage.'
keywords:
  - резервные копии Elasticsearch
  - снапшоты Elasticsearch
  - Elasticsearch
---

# Управление резервными копиями

Сервис {{ mes-name }} позволяет использовать механизм [снапшотов](https://www.elastic.co/guide/en/elasticsearch/reference/current/snapshot-restore.html) {{ ES }} для управления резервными копиями данных. Для работы со снапшотами используется [публичный API {{ ES }}](https://www.elastic.co/guide/en/elasticsearch/reference/current/snapshot-restore-apis.html), а для их хранения — бакет в {{ objstorage-name }}.


## Получить список резервных копий {#list-backups}

1. Найдите в списке репозиториев {{ ES }} тот, который содержит в себе резервные копии в виде снапшотов:

    ```http
    GET https://admin:<пароль>@<FQDN или IP-адрес хоста>:9200/_snapshot/_all
    ```

    Если нужного репозитория нет в списке — [подключите его](./s3-access.md).

1. Получите список снапшотов в репозитории:

    ```http
    GET https://admin:<пароль>@<FQDN или IP-адрес хоста>:9200/_snapshot/<репозиторий>/_all
    ```

    Каждой резервной копии соответствует один снапшот.


## Создать резервную копию {#create-backup}

1. Найдите в списке репозиториев {{ ES }} тот, в котором нужно создать резервную копию в виде снапшота:

    ```http
    GET https://admin:<пароль>@<FQDN или IP-адрес хоста>:9200/_snapshot/_all
    ```

    Если нужного репозитория нет в списке — [подключите его](./s3-access.md).

1. [Создайте снапшот](https://www.elastic.co/guide/en/elasticsearch/reference/current/create-snapshot-api.html) нужных данных или целого кластера в выбранном репозитории:

    ```http
    PUT https://admin:<пароль>@<FQDN или адрес хоста>:9200/_snapshot/<репозиторий>/<снапшот>
    ```

## Восстановить кластер из резервной копии {#restore}

{% note warning %}

При восстановлении из снапшотов действуют следующие ограничения:

* Версия {{ ES }} в кластере должна совпадать с версией {{ ES }}, в которой был сделан снапшот, или быть новее.
* Для восстановления индексов необходимо, чтобы мажорная версия {{ ES }} в кластере была не больше чем на единицу старше мажорной версии {{ ES }}, в которой сделан снапшот: индексы, созданные в версии 5.0, нельзя восстановить в версии 7.0.

{% endnote %}

1. [Создайте новый кластер {{ ES }}](./cluster-create.md) в нужной конфигурации, но не наполняйте его данными.

    При создании кластера выберите:

    * Количество и класс хостов, размер и тип хранилища исходя из размера снапшота и требований к быстродействию. При необходимости [повысьте класс хостов](./cluster-update.md#change-resource-preset) или [увеличьте размер хранилища кластера](./cluster-update.md#change-disk-size).

    * Версию {{ ES }}, в которой был создан снапшот, или более новую.

1. Закройте открытые индексы с помощью [{{ES}} API](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-close.html):

    ```http
    POST: https://admin:<пароль>@<FQDN или адрес хоста>:9200/<индекс>/_close
    ```

    Для восстановления всего кластера закройте все открытые индексы. Для восстановления отдельных индексов закройте только их.

1. [Получите список резервных копий](#list-backups) и найдите нужный снапшот.
1. [Запустите операцию восстановления](https://www.elastic.co/guide/en/elasticsearch/reference/current/restore-snapshot-api.html) из нужного снапшота всего кластера или отдельных индексов и потоков данных.

Подробнее о восстановлении из снапшотов см. в [документации {{ ES }}](https://www.elastic.co/guide/en/elasticsearch/reference/current/snapshots-restore-snapshot.html).
