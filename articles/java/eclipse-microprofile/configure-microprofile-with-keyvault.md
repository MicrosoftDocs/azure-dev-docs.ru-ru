---
title: Настройка MicroProfile для использования с Azure Key Vault
description: Узнайте, как добавлять секреты в веб-службу MicroProfile с помощью Azure Key Vault
services: key-vault
documentationcenter: java
author: jonathangiles
ms.author: jogiles
ms.date: 09/07/2018
ms.service: key-vault
ms.tgt_pltfrm: multiple
ms.topic: article
ms.workload: web
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: a2f39a13012a2ec1422ddf1f620ddd3e8f6e482d
ms.sourcegitcommit: 0eb25e1fdafcd64118843748dc061f60e7e48332
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/21/2021
ms.locfileid: "98625945"
---
# <a name="configure-microprofile-with-azure-key-vault"></a>Настройка MicroProfile для использования с Azure Key Vault

В этом руководстве мы продемонстрируем, как настроить приложение [MicroProfile](http://microprofile.io), чтобы получать секреты из [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) с использованием программных интерфейсов [API MicroProfile Config](https://microprofile.io/project/eclipse/microprofile-config) для создания прямого соединения с Azure Key Vault. Программные интерфейсы API MicroProfile Config предоставляют разработчикам стандартные средства для добавления данных конфигурации в микрослужбы и получения этих данных.

Прежде чем начать, рассмотрим вкратце те возможности, которые предоставляет сочетание Azure Key Vault и API MicroProfile Config при написании кода. Ниже приведен фрагмент кода, содержащего поле класса с аннотациями `@Inject` и `@ConfigProperty`. Параметр `name`, указанный в аннотации, — это имя свойства, которое нужно найти в Azure Key Vault, а `defaultValue` — значение по умолчанию, которое будет использовано, если ключ не найден. Теперь значение, хранящееся в Azure Key Vault, или значение по умолчанию будет автоматически добавлено в поле во время выполнения. Благодаря этому разработчикам больше не нужно передавать значения в конструкторах или методах задания, так как всю работу берет на себя MicroProfile.

```java
@Inject
@ConfigProperty(name = "key-name", defaultValue = "Unknown")
String keyValue;
```

Также можно осуществлять доступ непосредственно к конфигурации MicroProfile, чтобы получать секреты по мере необходимости, например:

```java
public class DemoClass {
    @Inject
    Config config;

    public void method() {
        System.out.println("Hello: " + config.getValue("key-name", String.class));
    }
}
```

В этом примере используется [Payara Micro](https://www.payara.fish/payara_micro) и [MicroProfile](https://microprofile.io/) для создания компактного файла Java WAR, который можно запускать локально на вашем компьютере. Упаковка кода в контейнер и его отправка в Azure выходит за рамки нашего примера. Но в конце этого руководства приведены ссылки на другие полезные руководства, в которых вы найдете нужную информацию.

В этом примере используется бесплатная библиотека с открытым кодом, которая создает источник конфигурации (при помощи API MicroProfile Config) для Azure Key Vault. Подробные сведения об этой библиотеке и ее код можно найти на [странице проекта на сайте GitHub](https://github.com/Azure/azure-microprofile/tree/master/microprofile-config-keyvault). Благодаря использованию этой библиотеки в этом руководстве нам не нужно писать специальный код для Azure. Мы можем просто настроить библиотеку и добавить ключи в свой код.

Ниже описаны действия, необходимые для запуска этого кода на вашем локальном компьютере, начиная с создания ресурса Azure Key Vault.

## <a name="creating-an-azure-key-vault-resource"></a>Создание ресурса Azure Key Vault

При помощи Azure CLI мы создадим ресурс Azure Key Vault и добавим в него один секрет.

1. Сначала создадим субъект-службу Azure. Это позволит нам получить идентификатор клиента и ключ, необходимые для доступа к Azure Key Vault:

```azurecli
az login
az account set --subscription <subscription_id>

az ad sp create-for-rbac --name <service_principal_name>
```

Предположим, что на предыдущем этапе ми использовали в качестве имени субъекта-службы значение `microprofile-keyvault-service-principal`. Тогда ответ от Azure будет иметь следующий вид (значения немного отредактированы):

```json
{
  "appId": "5292398e-XXXX-40ce-XXXX-d49fXXXX9e79",
  "displayName": "microprofile-keyvault-service-principal",
  "name": "http://microprofile-keyvault-service-principal",
  "password": "9b217777-XXXX-4954-XXXX-deafXXXX790a",
  "tenant": "72f988bf-XXXX-41af-XXXX-2d7cd011db47"
}
```

Обратите внимание на значения `appID` и `password`. Они будут использованы позднее для параметров `client ID` и `key` соответственно.

После создания субъекта-службы мы дополнительно можем создать группу ресурсов (можно пропустить этот шаг, если у вас уже есть группа ресурсов, которую вы планируете использовать). Чтобы получить список расположений групп ресурсов, можно выполнить команду `az account list-locations`. Затем вы можете использовать значение `name` из этого списка, чтобы указать, где нужно создать группу ресурсов.

```azurecli
# For this tutorial, the author chose to use `westus`
# and `jg-test` for the resource group name.
az group create -l <resource_group_location> -n <resource_group_name>
```

Теперь мы создадим ресурс Azure Key Vault. Обратите внимание, что имя хранилища ключей Azure Key Vault будет позднее использоваться для доступа к этому хранилищу. Поэтому выберите имя, которое легко запомнить.

```azurecli
az keyvault create --name <your_keyvault_name>            \
                   --resource-group <your_resource_group> \
                   --location <location>                  \
                   --enabled-for-deployment true          \
                   --enabled-for-disk-encryption true     \
                   --enabled-for-template-deployment true \
                   --sku standard
```

Нам также нужно предоставить созданному ранее субъекту-службе соответствующие разрешения, чтобы он мог осуществлять доступ к секретам Azure Key Vault. Обратите внимание, что значение "appID" здесь — это значение `appId`, полученное при создании субъекта-службы ранее (т. е. `5292398e-XXXX-40ce-XXXX-d49fXXXX9e79`, но вам нужно использовать свое значение, полученное при выполнении соответствующей команды).

```azurecli
az keyvault set-policy --name <your_keyvault_name>   \
                       --secret-permission get list  \
                       --spn <your_sp_appId_created_in_step1>
```

На этом этапе мы можем отправить секрет в Azure Key Vault. Используем имя ключа `demo-key` и его значение `demo-value`:

```azurecli
az keyvault secret set --name demo-key      \
                       --value demo-value   \
                       --vault-name <your_keyvault_name>  
```

Вот и все! Теперь у нас есть хранилище ключей Azure Key Vault с одним секретом. Далее мы можем клонировать нужный репозиторий и настроить его для использования этого ресурса в нашем приложении.

## <a name="getting-up-and-running-locally"></a>Настройка и запуск на локальном компьютере

Наш пример построен на основе демонстрационного приложения, доступного на сайте GitHub, поэтому мы клонируем это приложение и пошагово выполним нужный код. Чтобы клонировать код на свой компьютер, выполните следующие шаги:

1. `git clone https://github.com/Azure-Samples/microprofile-configsource-keyvault.git`

1. `cd microprofile-configsource-keyvault`

1. Перейдите к расположению `src/main/resources/META-INF/microprofile-config.properties` и измените файл microprofile-config.properties, указав сведения, полученные ранее.

1. Попробуйте запустить сервер при помощи команды `mvn clean package payara-micro:start`.

1. Попытайтесь открыть страницу `http://localhost:8080/keyvault-configsource/api/config` в своем веб-браузере. Должен отобразиться простой ответ, содержащий значения, полученные из Azure Key Vault.

## <a name="summary"></a>Сводка

В этом примере приложения используется сочетание API MicroProfile Config, Azure Key Vault и бесплатной библиотеки [microprofile-config-keyvault](https://github.com/Azure/azure-microprofile/tree/master/microprofile-config-keyvault) с открытым кодом для простого добавления данных конфигурации и секретов в веб-службы MicroProfile.
