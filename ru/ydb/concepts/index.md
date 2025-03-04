# Обзор


*Yandex Database* ({{ ydb-short-name }}) — это горизонтально масштабируемая распределённая отказоустойчивая СУБД. {{ ydb-short-name }} спроектирована с учетом требований высокой производительности — например, обычный сервер может обрабатывать десятки тысяч запросов в секунду. В дизайн системы заложена работа с объемами данных в сотни петабайт.

{{ ydb-short-name }} является региональной базой данных и располагается в трёх  [зонах доступности](../../overview/concepts/geo-scope.md).

{{ ydb-short-name }} обеспечивает:

* [строгую консистентность](https://en.wikipedia.org/wiki/Consistency_model#Strict_Consistency) с возможностью ослабления для увеличения производительности;
* подержку запросов [YQL](../yql/reference/overview.md) (диалект SQL для работы с большими данными);
* автоматическую репликацию данных;
* высокую доступность с автоматической обработкой отказов вычислительных узлов или зон доступности;
* автоматическое партицирование данных при увеличении их объема или увеличении нагрузки.

{{ ydb-short-name }} может работать в двух режимах:

* С выделенными инстансами (dedicated) — режим, при котором для базы выделяются отдельные виртуальные хосты с задаными ресурсами. Вы самостоятельно задаете число хостов, их [вычислительные ресурсы](databases.md#compute-units) задаете параметры хранилища и настраиваете облачные сети.

  Если вычислительных ресурсов базы данных станет недостаточно для обслуживания подаваемой нагрузки, скорость обработки запросов будет уменьшаться вплоть до отказа в обслуживании. Чтобы избежать этого, своевременно [добавляйте вычислительные ресурсы](../operations/create_manage_database.md#change-db-params) в базу данных.
   
   Такой режим удобно использовать, когда вы хорошо представляете, какой объем ресурсов потребуется вашей базе для работы и можете спрогнозировать пиковые нагрузки. При этом оплата происходит за выделенные вычислительные ресурсы, объем хранилища и резервных копий и не зависит от числа операций с базой. Подробнее об оплате читайте в [правилах тарификации](../pricing/dedicated.md).

* Бессерверный (serverless) режим — режим, в котором вы можете развернуть базу данных в обслуживаемой среде без создания отдельных хостов. Вам не нужно контролировать вычислительные ресурсы, сети и другие параметры. Все ресурсы, которые необходимы для работы базы, автоматически предоставляются сервисом и масштабируются в зависимости от нагрузки.
   
   Бессерверный режим подходит для небольших проектов, когда отдельный сервер не нужен, или для задач с неравномерной и плохо прогнозируемой нагрузкой. В таком режиме вы платите за фактические операции с базой и за хранение данных и резервных копий. Подробнее об оплате читайте в [правилах тарификации](../pricing/serverless.md).

   Бессерверный режим {{ ydb-full-name }} поддерживает работу с данными не только с помощью YDB API, но и с помощью [Document API](../docapi/api-ref/index.md) — HTTP API, совместимого с Amazon DynamoDB. С помощью этого API можно выполнять операции над документными таблицами.

{{ ydb-short-name }} предоставляет YDB API и его реализации в виде [YDB CLI](../quickstart/yql-api/ydb-cli.md) и [YDB SDK](../sdk/index.md) для [Java](https://github.com/yandex-cloud/ydb-java-sdk), [Python](https://github.com/yandex-cloud/ydb-python-sdk), [Node.js](https://github.com/yandex-cloud/ydb-nodejs-sdk), [PHP](https://github.com/yandex-cloud/ydb-php-sdk) и [Go](https://github.com/yandex-cloud/ydb-go-sdk)

{{ ydb-short-name }} поддерживает реляционную [модель данных](datamodel.md) и оперирует таблицами с предопределённой схемой. Для удобства организации таблиц поддерживается создание директорий по аналогии с файловой системой.

В {{ ydb-short-name }} поддерживаются высокопроизводительные распределенные [ACID](https://en.wikipedia.org/wiki/ACID_(computer_science))-транзакции, которые могут затрагивать несколько записей из разных таблиц. Обеспечивается самый строгий уровень изоляции транзакций — serializable. Также имеется возможность ослабления уровня изоляции для увеличения производительности.

В дизайн {{ ydb-short-name }} заложена поддержка разных сценариев нагрузки, таких как [OLTP](https://en.wikipedia.org/wiki/Online_transaction_processing) и [OLAP](https://en.wikipedia.org/wiki/Online_analytical_processing). В текущей реализации поддержка аналитических запросов ограничена. Поэтому можно говорить, что в данный момент YDB — это OLTP-база данных.
