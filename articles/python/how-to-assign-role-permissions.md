---
title: Назначение разрешений роли удостоверению приложения или субъекту-службе
description: Предоставление разрешений субъекту-службе или удостоверению приложения с помощью Azure CLI
ms.date: 05/12/2020
ms.topic: conceptual
ms.custom: devx-track-python, devx-track-azurecli
ms.openlocfilehash: 3eb81eac5ee9a7f2f85e50494efa2e04bbcbe439
ms.sourcegitcommit: 980efe813d1f86e7e00929a0a3e1de83514ad7eb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/07/2020
ms.locfileid: "87983136"
---
# <a name="how-to-assign-role-permissions-to-an-app-identity-or-service-principal"></a>Как назначить разрешения роли удостоверению приложения или субъекту-службе

Система управления доступом на основе ролей (RBAC) Azure управляет конкретными разрешениями широкого спектра ресурсов. *Роль* — это, по сути, коллекция связанных разрешений, которые обычно используются совместно. Чтобы включить разрешения, назначьте роль *субъекту безопасности* (пользователю, группе, субъекту-службе или удостоверению приложения) в определенной *области*, к которой применяется эта роль.

На практике всегда назначайте только те роли, которые субъекту безопасности действительно требуются в определенной области. Старайтесь не назначать более широкие роли в более широких областях, даже если изначально это кажется более удобным. Ограничивая роли и области, вы ограничиваете ресурсы, подверженные риску в случае компрометации субъекта безопасности (т. е. если происходит утечка учетных данных субъекта в результате нарушения безопасности данных или других инцидентов безопасности).

Так как вы используете разные субъекты безопасности в разработке и рабочей среде, вы повторно назначаете роли в каждой среде. Это значит, что во время разработки вы обычно назначаете роли локальному субъекту-службе, созданному на рабочей станции (см. раздел [Настройка проверки подлинности](configure-local-development-environment.md#configure-authentication)). В рабочей среде вы назначаете роли удостоверению приложения или субъекту-службе перед развертыванием, чтобы обеспечить доступ к приложению при запуске.

Дополнительные сведения о RBAC см. в статье [Общие сведения об управлении доступом на основе ролей (RBAC) для ресурсов Azure](/azure/role-based-access-control/overview).

## <a name="role-assignment-process"></a>Процесс назначения роли

Назначение роли выполняется в три этапа:

1. [Найдите соответствующую роль](#find-the-roles-you-need) для типа вовлеченных ресурсов и операций, которые требуется авторизовать.

1. Определите необходимую область для рассматриваемой роли, которая описывает экстент ресурсов, для которых разрешены операции.

1. Назначьте роль субъекту безопасности.

Этап 1 является одинаковым как для портала Azure, так и для Azure CLI. Этапы 2 и 3 для портала и Azure CLI отличаются и поэтому объединены в следующих разделах: [Идентификация области и назначение роли на портале Azure](#azure-portal) и [Идентификация области и назначение роли с помощью Azure CLI](#azure-cli).

> [!NOTE]
> Если ваша учетная запись пользователя не позволяет назначать роли в вашей подписке, вы увидите сообщение об ошибке о том, что ваша учетная запись не авторизована для выполнения действия Microsoft.Authorization/roleAssignments/write. В этом случае обратитесь к администраторам подписки, так как они могут назначать разрешения от вашего имени.

## <a name="find-the-roles-you-need"></a>Поиск нужных ролей

1. Для начала ознакомьтесь со статьей [Встроенные роли Azure](/azure/role-based-access-control/built-in-roles). Таблица в верхней части статьи является индексом для сведений, приведенных далее в этой статье.

1. В этой статье перейдите к категории службы (вычисление, хранение, базы данных и т. д.) для ресурса, которому необходимо предоставить разрешения. Самый простой способ найти искомый объект — это, как правило, искать на странице релевантное ключевое слово, такое как "большой двоичный объект", "виртуальная машина" и т. д.

1. Ознакомьтесь с перечисленными ролями для категории службы и идентифицируйте конкретные необходимые операции. Опять же, всегда начинайте с наиболее ограниченных ролей.

    Например, если субъекту безопасности необходимо считывать большие двоичные объекты в учетной записи службы хранилища Azure, но доступ на запись не требуется, выберите "Модуль чтения данных BLOB-объекта хранилища", а не "Участник для данных BLOB-объектов хранилища" (и, безусловно, не роль "Владелец данных BLOB-объектов хранилища" на уровне администратора). При необходимости назначения ролей всегда можно обновлять позднее.

    Если субъекту безопасности также требуется доступ к хранилищу очередей, можно использовать такие роли, как "Модуль чтения данных очереди хранилища" и "Участник данных очереди хранилища". Опять же, будьте максимально конкретны и не назначайте широкую роль, такую как "Модуль чтения и доступ к данным", которая предоставляет доступ к ключам учетной записи, через которые субъект может получить доступ к данным во всей учетной записи хранения.

1. Если подходящая роль не найдена, можно создать [пользовательские роли](/azure/role-based-access-control/custom-roles).

## <a name="identify-scope-and-assign-a-role-on-the-azure-portal"></a><a name="azure-portal"></a>Идентификация области и назначение роли на портале Azure

1. На [портале Azure](https://portal.azure.com) перейдите к ресурсу, которому нужно назначить роль. Область ресурса определяет область назначения.

    Например, при переходе к учетной записи хранения любое назначение роли применяется ко всей учетной записи хранения. При переходе к определенному контейнеру больших двоичных объектов в этой учетной записи хранения назначение роли применяется только к этому контейнеру.

1. Выберите колонку **Управление доступом (IAM)** (IAM означает "управление идентификацией и доступом").

1. В этой колонке находится раздел **Назначение ролей**, в котором можно добавлять и удалять роли, назначенные любому субъекту безопасности.

Полные сведения и пошаговое руководство по пользовательскому интерфейсу см. в статье [Добавление и удаление назначений ролей Azure с помощью портала Azure](/azure/role-based-access-control/role-assignments-portal) в документации по RBAC Azure.

## <a name="identify-scope-and-assign-a-role-through-the-azure-cli"></a><a name="azure-cli"></a>Идентификация области и назначение ролей с помощью Azure CLI

Для назначения ролей с помощью Azure CLI используется команда [`az role assignment`](/cli/azure/role/assignment?view=azure-cli-latest). Команда `az role assignment create` используется для добавления назначения, а команда `az role assignment delete` — для удаления назначения. 

Хотя полный процесс описан в статье [Добавление и удаление назначений ролей Azure с помощью портала Azure](/azure/role-based-access-control/role-assignments-cli), в следующей сводке приведены конкретные примеры, относящиеся к другим статьям в этом центре разработчиков.

Команда `az role assignment create` имеет следующий синтаксис:

```azurecli
az role assignment create --assignee <assignee> --role <role> --scope <scope>
```

- Команда `<assignee>` определяет субъект безопасности; для субъектов-служб, например тех, которые используются во время локальной разработки, субъектом назначения является идентификатор клиента этого субъекта. Для приложений, развернутых в облаке, субъект назначения является именем приложения.
- `<role>` — имя назначаемой роли, например "Участник для данных BLOB-объектов хранилища", или GUID, например ba92f5b4-2d11-453d-a403-e96b0029c9fe.
- `<scope>` — потенциально длинная строка, которая определяет точную область назначения.

Область состоит из последовательности идентификаторов, разделенных символом "/". Эту строку можно представить как выражение следующей иерархии, где текстом без заполнителей (`<>`) являются фиксированные идентификаторы:

<pre>
/subscriptions
  /&lt;subscription_id&gt;
    /resourcegroups
      /&lt;resource_group_name&gt;
        /providers
          /&lt;provider_name&gt;
            /&lt;resource_type&gt;
              /&lt;resource_sub_type_1&gt;
                /&lt;resource_sub_type_2&gt;
                  /&lt;resource_name&gt;
</pre>

- `<subscription_id>` — идентификатор используемой подписки (GUID).
- `<resources_group_name>` — имя содержащейся группы ресурсов.
- `<provider_name>` — имя службы, которая обрабатывает ресурс. `<resource_type>` и `<resource_sub_type_*>` идентифицируют дополнительные уровни в этой службе.
  
    Сведения об этих именах и типах можно найти в статье [Встроенные роли Azure](/azure/role-based-access-control/built-in-roles). Найдите и выберите роль в верхней таблице, чтобы перейти к конкретному описанию роли. Там находятся строки, содержащие имя поставщика, тип ресурса и подтипы ресурсов. Например, для роли "Участник для данных BLOB-объектов хранилища" отображается Microsoft.Storage/storageAccounts/blobServices/containers/. Для роли читателя учетных записей Cosmos DB отображается Microsoft.DocumentDB/databaseAccounts/readonlykeys, где есть только один подтип.

- `<resource_name>` — последняя часть строки, идентифицирующая конкретный ресурс.

Самая широкая (наиболее общая) область — `/subscriptions/<subscription_id>`. Она применяет назначения во всей подписке. Каждый дополнительный уровень иерархии делает область более конкретной.

### <a name="examples"></a>Примеры

В следующих примерах соблюдаются указанные ниже условия (см. статью [Пример Подготовка к работе и использование службы хранилища Azure](azure-sdk-example-storage.md)):

- Идентификатор подписки Azure содержится в переменной среды `AZURE_SUBSCRIPTION_ID`.
- Идентификатор клиента субъекта-службы, которой необходимо назначить роль, содержится в переменной среды `AZURE_CLIENT_ID`.
- В подписке есть группа ресурсов PythonAzureExample-Storage-rg.
- Группа ресурсов содержит учетную запись хранения Azure pythonazurestorage-12345.
- У вас есть контейнер больших двоичных объектов в этой учетной записи хранения с именем blob-container-01.
- Необходимо назначить субъекту-службе роль "Участник для данных BLOB-объектов хранилища".

> [!TIP]
> Для распространения изменений в назначениях ролей в Azure может потребоваться несколько минут. В результате вы можете обнаружить, что код по-прежнему работает в течение короткого времени после удаления разрешения. Если вы наблюдаете необычное поведение, подождите пару минут и повторите попытку.

#### <a name="grant-permissions-for-the-specific-container-only"></a>Предоставление разрешений только для конкретного контейнера

# <a name="cmd"></a>[cmd](#tab/cmd)

```azurecli
az role assignment create --assignee %AZURE_CLIENT_ID% ^
    --role "Storage Blob Data Contributor" ^
    --scope "/subscriptions/%AZURE_SUBSCRIPTION_ID%/resourceGroups/PythonAzureExample-Storage-rg/providers/Microsoft.Storage/storageAccounts/pythonazurestorage12345/blobServices/default/containers/blob-container-01"
```

# <a name="bash"></a>[bash](#tab/bash)

```azurecli
az role assignment create --assignee $AZURE_CLIENT_ID \
    --role "Storage Blob Data Contributor" \
    --scope "/subscriptions/$AZURE_SUBSCRIPTION_ID/resourceGroups/PythonAzureExample-Storage-rg/providers/Microsoft.Storage/storageAccounts/pythonazurestorage12345/blobServices/default/containers/blob-container-01"
```

---

#### <a name="grant-permissions-for-all-blob-containers-in-the-storage-account"></a>Предоставление разрешений для всех контейнеров больших двоичных объектов в учетной записи хранения

# <a name="cmd"></a>[cmd](#tab/cmd)

```azurecli
az role assignment create --assignee %AZURE_CLIENT_ID% ^
    --role "Storage Blob Data Contributor" ^
    --scope "/subscriptions/%AZURE_SUBSCRIPTION_ID%/resourceGroups/PythonAzureExample-Storage-rg/providers/Microsoft.Storage/storageAccounts/pythonazurestorage12345"
```

# <a name="bash"></a>[bash](#tab/bash)

```azurecli
az role assignment create --assignee $AZURE_CLIENT_ID \
    --role "Storage Blob Data Contributor" \
    --scope "/subscriptions/$AZURE_SUBSCRIPTION_ID/resourceGroups/PythonAzureExample-Storage-rg/providers/Microsoft.Storage/storageAccounts/pythonazurestorage12345"
```

---

#### <a name="grant-permissions-for-all-blob-containers-in-the-resource-group"></a>Предоставление разрешений для всех контейнеров больших двоичных объектов в группе ресурсов

# <a name="cmd"></a>[cmd](#tab/cmd)

```azurecli
az role assignment create --assignee %AZURE_CLIENT_ID% ^
    --role "Storage Blob Data Contributor" ^
    --scope "/subscriptions/%AZURE_SUBSCRIPTION_ID%/resourceGroups/PythonAzureExample-Storage-rg"
```

Кроме того, можно указать группу ресурсов с помощью параметра `--resource-group`:

```azurecli
az role assignment create --assignee %AZURE_CLIENT_ID% ^
    --role "Storage Blob Data Contributor" ^
    --resource-group "PythonAzureExample-Storage-rg"
```

# <a name="bash"></a>[bash](#tab/bash)

```azurecli
az role assignment create --assignee $AZURE_CLIENT_ID \
    --role "Storage Blob Data Contributor" \
    --scope "/subscriptions/$AZURE_SUBSCRIPTION_ID/resourceGroups/PythonAzureExample-Storage-rg"
```

Кроме того, можно указать группу ресурсов с помощью параметра `--resource-group`:

```azurecli
az role assignment create --assignee $AZURE_CLIENT_ID \
    --role "Storage Blob Data Contributor" \
    --resource-group "PythonAzureExample-Storage-rg"
```

---

#### <a name="grant-permissions-to-all-blob-containers-in-the-subscription"></a>Предоставление разрешений для всех контейнеров больших двоичных объектов в подписке

# <a name="cmd"></a>[cmd](#tab/cmd)

```azurecli
az role assignment create --assignee %AZURE_CLIENT_ID% ^
    --role "Storage Blob Data Contributor" ^
    --scope "/subscriptions/%AZURE_SUBSCRIPTION_ID%"
```

# <a name="bash"></a>[bash](#tab/bash)

```azurecli
az role assignment create --assignee $AZURE_CLIENT_ID \
    --role "Storage Blob Data Contributor" \
    --scope "/subscriptions/$AZURE_SUBSCRIPTION_ID"
```

---
