# Управление доступом к DataLens


Доступ к сервису {{ datalens-full-name }} регулируется через консоль {{ yandex-cloud }}.
Чтобы предоставить доступ, назначьте пользователю одну из ролей {{ datalens-short-name }}.


Разграничение прав доступа в сервисе реализовано на уровне объектов и папок.
На каждый объект и папку можно назначать пользователю права доступа, которые определяют допустимые операции. Если вы создали или скопировали папку или объект, то у них будут те же права, что и у родительской папки, в которой они будут размещены.

Вы можете предоставить пользователю доступ к папке или к любому объекту сервиса:
- Подключение
- Датасет
- Чарт
- Дашборд


Также пользователь может запросить права доступа самостоятельно через форму запроса. Подробнее в разделе [{#T}](../operations/permission/request.md).

## Пользовательские роли {#users-roles}

Позволяют определить права пользователя в экземпляре {{ datalens-short-name }}:

- `{{ roles-datalens-instances-user }}` — пользователь {{ datalens-short-name }} с правами на создание, чтение и изменение объектов согласно [правам доступа на объекты](#permissions).
- `{{ roles-datalens-instances-admin }}` — администратор экземпляра {{ datalens-short-name }}. Роль автоматически присваивается создателю экземпляра. Администратор обладает правами `{{ roles-datalens-instances-user }}`, а также может изменять тарифный план и оплачивать платный контент в {{ marketplace-name }}.

Пользовательские роли назначаются в консоли {{ yandex-cloud }}.

## Добавление пользователя {#add-new-user}

Добавлять можно [пользователей с аккаунтом на Яндексе](#passport-user) и [федеративных пользователей](#federated-user).

### Добавить пользователя с аккаунтом на Яндексе {#passport-user}

Чтобы добавить пользователя и предоставить ему доступ к сервису {{ datalens-short-name }}:

1. {% include [grant-role-console-first-steps](../../_includes/iam/grant-role-console-first-steps.md) %}
1. На странице **Пользователи и роли** в правом верхнем углу нажмите **Добавить пользователя**.
1. Введите электронную почту пользователя в Яндексе.
1. Нажмите кнопку **Добавить**. При добавлении нового пользователя в облако ему автоматически назначается роль участника облака — [`resource-manager.clouds.member`](../../iam/concepts/access-control/roles.md#member).

    {% note info %}
    
    Время до появления логина добавленного пользователя в форме выдачи прав доступа может достигать нескольких часов.
    
    {% endnote %}

1. {% include [configure-roles-console](../../_includes/iam/configure-roles-console.md) %}
1. Для добавления роли на облако нажмите значок ![image](../../_assets/plus-sign.svg) в блоке **Роли на облако <имя облака>**.

    Для добавления роли на каталог, выберите каталог и нажмите **Назначить роль** в блоке **Роли в каталогах**.
1. Выберите `{{ roles-datalens-instances-user }}`  или  `{{ roles-datalens-instances-admin }}` из списка.


### Добавить федеративных пользователей {#federated-user}

{% include [include](../../_includes/iam/add-federated-users-before-begin.md) %}

1. Добавьте федеративных пользователей:

   {% include [include](../../_includes/iam/add-federated-users-instruction.md) %}

1. {% include [configure-roles-console](../../_includes/iam/configure-roles-console.md) %}
1. Для добавления роли на облако нажмите значок ![image](../../_assets/plus-sign.svg) в блоке **Роли на облако <имя облака>**.

    Для добавления роли на каталог, выберите каталог и нажмите **Назначить роль** в блоке **Роли в каталогах**.
1. Выберите `{{ roles-datalens-instances-user }}` из списка. 

Подробнее о назначении ролей в {{ yandex-cloud }} в разделе [Роли](../../iam/concepts/access-control/roles.md).

## Права доступа на объекты {#permissions}

Вы можете назначить следующие права доступа на объекты и папки в сервисе {{ datalens-short-name }}:

- [{{ permission-execute }}](#permission-execute)
- [{{ permission-read }}](#permission-read)
- [{{ permission-write }}](#permission-write)
- [{{ permission-admin }}](#permission-admin)

### {{ permission-execute }} {#permission-execute}

Пользователь с правом доступа `{{ permission-execute }}` на подключение может выполнять к нему запросы, но не может создавать датасеты. Независимо от прав на датасет, пользователю не доступен список таблиц в датасете и просмотр SQL-подзапроса, на котором основан датасет.

Пользователь с правом доступа `{{ permission-execute }}` на датасет может выполнять к нему запросы и создавать чарты, но не может просматривать датасет.  

{% note warning %}

Вы можете назначить право доступа `{{ permission-execute }}` только на подключения и датасеты.

{% endnote %}

Назначение пользователям права доступа `{{ permission-execute }}` дает возможность:

- Уменьшить число запросов в источник, тем самым уменьшить нагрузку на источник подключения.

- Лучше контролировать данные, которые разрешено показывать из датасета. Можно скрыть часть полей из источника, чтобы пользователь не мог посмотреть весь перечень полей.

- Ограничить создание подзапросов к исходной БД. Пользователь с правом `{{ permission-execute }}` не может писать подзапросы.


### {{ permission-read }} {#permission-read}

Пользователь с правом доступа `{{ permission-read }}` может просматривать дашборды, виджеты, датасеты и папки.

{% note warning %}

Право доступа `{{ permission-read }}` не позволяет копировать датасеты, потому что они содержат настройки [RLS](row-level-security.md). Копирование датасетов доступно при наличии права `{{ permission-write }}` или `{{ permission-admin }}`.

{% endnote %}

### {{ permission-write }} {#permission-write}

Пользователь с правом доступа `{{ permission-write }}` может изменять дашборды, виджеты, подключения, датасеты и папки.

Право доступа `{{ permission-write }}` включает в себя все разрешения права доступа `{{ permission-read }}`.

### {{ permission-admin }} {#permission-admin}

Пользователь с правом доступа `{{ permission-admin }}` может изменять доступные объекты и папки, изменять права доступа.

Право доступа `{{ permission-admin }}` включает в себя все разрешения права доступа `{{ permission-write }}`.


## Таблица прав доступа {#permission-table}

Объект доступа<br/>Действие | {{ permission-execute }} | {{ permission-read }} | {{ permission-write }} | {{ permission-admin }}
----|----|----|----|----
**Папка** |
Просмотр папки | N/A | ✔ | ✔ | ✔
Редактирование папки | N/A | - | ✔ | ✔
Удаление папки | N/A | - | - | ✔
Изменение прав доступа | N/A | - | - | ✔
**Подключение** |
Выполнение запросов<br/>к подключению | ✔ | ✔ | ✔ | ✔
Создание датасета<br/>над подключением | - | ✔ | ✔ | ✔
Просмотр параметров<br/>подключения | - | ✔ | ✔ | ✔
Редактирование подключения | - | - | ✔ | ✔
Удаление подключения | - | - | - | ✔
Изменение прав доступа | - | - | - | ✔
**Датасет** |
Выполнение запросов<br/>к датасету | ✔ | ✔ | ✔ | ✔
Создание чарта<br/>над датасетом | ✔ | ✔ | ✔ | ✔
Просмотр датасета | - | ✔ | ✔ | ✔
Редактирование датасета | - | - | ✔ | ✔
Удаление датасета | - | - | - | ✔
Изменение прав доступа | - | - | - | ✔
**Чарт** |
Просмотр чарта | N/A | ✔ | ✔ | ✔
Редактирование чарта | N/A | - | ✔ | ✔
Удаление чарта | N/A | - | - | ✔
Изменение прав доступа | N/A | - | - | ✔
**Дашборд** |
Просмотр дашборда | N/A | ✔ | ✔ | ✔
Редактирование дашборда | N/A | - | ✔ | ✔
Удаление дашборда | N/A | - | - | ✔
Изменение прав доступа | N/A | - | - | ✔

## Аудит доступа к объектам 

Пользователь {{ datalens-short-name }} может получить логи доступа к объектам {{ datalens-short-name }} (просмотр, редактирование, удаление).
Чтобы получить логи, [обратитесь в службу технической поддержки]({{ link-console-support }}).


#### Что дальше {#what-is-next}

- [{#T}](../operations/permission/grant.md)
- [{#T}](../operations/permission/revoke.md)
- [{#T}](../operations/permission/request.md)
- [{#T}](../operations/dataset/manage-row-level-security.md)