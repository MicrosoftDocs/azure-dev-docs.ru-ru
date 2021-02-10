---
title: Добавление пользовательского доменного имени в веб-приложение
description: Добавьте пользовательское доменное имя в веб-приложение Azure с помощью Azure CLI.
ms.topic: how-to
ms.date: 01/29/2021
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: 977376fda6e7c93390c45196ae5baae751f5ad64
ms.sourcegitcommit: 3f8aa923e4626b31cc533584fe3b66940d384351
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/01/2021
ms.locfileid: "99230191"
---
# <a name="configuring-a-custom-domain-name"></a>Настройка пользовательского доменного имени

Добавьте пользовательское доменное имя в веб-приложение Azure с помощью Azure CLI. 

Служба приложений имеет удобное DNS-имя, которое отлично подходит для тестирования, в виде `YOUR-RESOURCE-NAME.azurewebsites.net`. В определенный момент может потребоваться добавить пользовательское доменное имя в веб-приложение. 

## <a name="purchase-a-domain-name-and-configure-dns-record"></a>Приобретение доменного имени и настройка записи DNS

1. Приобретите доменное имя у регистратора доменных имен. 
1. Для вашей записи DNS добавьте запись `A`, которая указывает на внешний IP-адрес веб-приложения (который фактически является подсистемой балансировки нагрузки). Используйте процедуру, описанную в следующем разделе, чтобы получить внешний IP-адрес.

    Кроме добавления записи `A`, добавьте запись `TXT` к своему домену, которая указывает на домен `*.azurewebsites.net`, используемый вами до сих пор. Сочетание записей `A` и `TXT` позволяет Azure убедиться в том, что вы являетесь владельцем домена.

## <a name="get-web-app-external-ip"></a>Получение внешнего IP-адреса веб-приложения

Чтобы получить этот IP-адрес, выполните следующую команду:

```azurecli
az webapp config hostname get-external-ip --name
```

<a name="register-a-domain-name-with-your-azure-app"></a>

## <a name="configure-web-app-domain-name"></a>Настройка доменного имени веб-приложения 

Создав эти записи и распространив изменения DNS, зарегистрируйте пользовательский домен в Azure, чтобы входящий трафик обрабатывался правильно.

Используйте команду [az webapp config hostname add](/cli/azure/webapp/config/hostname):

```azurecli
az webapp config hostname add \
    --hostname YOUR-DOMAIN-NAME
    --webapp-name YOUR-WEBAPP-NAME
    --resource-group YOUR-RESOURCE-GROUP-NAME
```

> [!NOTE]
> Команда не будет работать, пока не будут распространены изменения в DNS.

Откройте браузер и перейдите к пользовательскому домену, чтобы увидеть, что он разрешается в развернутое приложение в Azure.

## <a name="next-steps"></a>Следующие шаги

* [Создание ресурса реестра контейнеров](create-container-registry-resource.md)