---
title: Как использовать начальное приложение Spring Boot Starter с Azure Key Vault
description: Сведения о настройке приложения Spring Boot Initializer с помощью начального приложения Azure Key Vault.
services: key-vault
documentationcenter: java
author: bmitchell287
manager: douge
editor: ''
ms.assetid: ''
ms.author: brendm
ms.date: 12/19/2018
ms.devlang: java
ms.service: key-vault
ms.tgt_pltfrm: multiple
ms.topic: article
ms.workload: identity
ms.openlocfilehash: 1c04bab67c7fc6a409893416d27de7ed18018cd9
ms.sourcegitcommit: 2efdb9d8a8f8a2c1914bd545a8c22ae6fe0f463b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/15/2019
ms.locfileid: "68283225"
---
# <a name="how-to-use-the-spring-boot-starter-for-azure-key-vault"></a>Как использовать начальное приложение Spring Boot Starter с Azure Key Vault

## <a name="overview"></a>Обзор

В этой статье описано, как создать с помощью **[Spring Initializr]** начальное приложение Spring Boot для Azure Key Vault для получения строки подключения, которая хранится в виде секрета в хранилище ключей.

## <a name="prerequisites"></a>Предварительные требования

Чтобы выполнить действия, описанные в этой статье, необходимо следующее:

* Подписка Azure. Если у вас ее еще нет, вы можете активировать [Преимущества для подписчиков MSDN] или зарегистрироваться для получения [бесплатной учетной записи Azure].
* Поддерживаемая версия Java Development Kit (JDK). Дополнительные сведения о версиях JDK, доступных для разработки в Azure, см. в статье <https://aka.ms/azure-jdks>.
* [Apache Maven](http://maven.apache.org/) версии 3.0 или более поздней.

## <a name="create-an-app-using-spring-initializr"></a>Создание приложения с помощью Spring Initializr

1. Перейдите по адресу <https://start.spring.io/>.

1. Укажите, что необходимо создать проект **Maven** с помощью **Java**, введите имя **группы** и **артефакта** вашего приложения, а затем щелкните ссылку, чтобы **перейти к полной версии** Spring Initializr.

   ![Указание имен группы и артефакта][secrets-01]

1. Прокрутите вниз до раздела **Azure** статьи и установите флажок рядом с пунктом **Azure Key Vault**.

   ![Выбор начального приложения Azure Key Vault][secrets-02]

1. Прокрутите страницу вниз и нажмите кнопку, чтобы **создать проект**.

   ![Создание проекта Spring Boot][secrets-03]

1. При появлении запроса скачайте проект на локальный компьютер.

## <a name="sign-into-azure"></a>Вход в Azure

1. Откройте окно командной строки.

1. Войдите в учетную запись Azure с помощью интерфейса командной строки Azure.

   ```azurecli
   az login
   ```
   Для завершения процесса входа следуйте инструкциям.

1. Отобразите список подписок:

   ```azurecli
   az account list
   ```
   Azure отобразит список подписок, и вам нужно будет скопировать идентификатор GUID для подписки, которая будет использоваться, например:

   ```json
   [
     {
       "cloudName": "AzureCloud",
       "id": "ssssssss-ssss-ssss-ssss-ssssssssssss",
       "isDefault": true,
       "name": "Converted Windows Azure MSDN - Visual Studio Ultimate",
       "state": "Enabled",
       "tenantId": "tttttttt-tttt-tttt-tttt-tttttttttttt",
       "user": {
         "name": "contoso@microsoft.com",
         "type": "user"
       }
     }
   ]
   ```

1. Укажите GUID учетной записи, которую вы собираетесь использовать в Azure, например:

   ```azurecli
   az account set -s ssssssss-ssss-ssss-ssss-ssssssssssss
   ```

## <a name="create-a-new-azure-key-vault"></a>Создание нового хранилища Azure Key Vault

1. Создайте группу ресурсов Azure, которые будут использоваться для хранилища ключей, например:
   ```azurecli
   az group create --name wingtiptoysresources --location westus
   ```
   Описание

   | Параметр | ОПИСАНИЕ |
   |---|---|
   | `name` | Указывает уникальное имя для группы ресурсов. |
   | `location` | Указывает [регион Azure](https://azure.microsoft.com/regions/) для размещения группы ресурсов. |

   В Azure CLI отобразятся результаты созданной группы ресурсов, например:  

   ```json
   {
     "id": "/subscriptions/ssssssss-ssss-ssss-ssss-ssssssssssss/resourceGroups/wingtiptoysresources",
     "location": "westus",
     "managedBy": null,
     "name": "wingtiptoysresources",
     "properties": {
       "provisioningState": "Succeeded"
     },
     "tags": null
   }
   ```

2. Создайте субъект-службу Azure, связанный с зарегистрированным приложением, например:
   ```shell
   az ad sp create-for-rbac --name "wingtiptoysuser"
   ```
   Описание

   | Параметр | ОПИСАНИЕ |
   |---|---|
   | `name` | Определяет имя пользователя для субъекта-службы Azure. |

   Azure CLI отобразит сообщение о состоянии JSON, которое содержит значения *appId* и *password* (вы будете использовать эти значения в качестве идентификатора клиента и пароля клиента позже), например:

   ```json
   {
     "appId": "iiiiiiii-iiii-iiii-iiii-iiiiiiiiiiii",
     "displayName": "wingtiptoysuser",
     "name": "http://wingtiptoysuser",
     "password": "pppppppp-pppp-pppp-pppp-pppppppppppp",
     "tenant": "tttttttt-tttt-tttt-tttt-tttttttttttt"
   }
   ```

3. Создайте хранилище ключей в группе ресурсов, например:
   ```azurecli
   az keyvault create --name wingtiptoyskeyvault --resource-group wingtiptoysresources --location westus --enabled-for-deployment true --enabled-for-disk-encryption true --enabled-for-template-deployment true --sku standard --query properties.vaultUri
   ```
   Описание

   | Параметр | ОПИСАНИЕ |
   |---|---|
   | `name` | Указывает уникальное имя для хранилища ключей. |
   | `location` | Указывает [регион Azure](https://azure.microsoft.com/regions/) для размещения группы ресурсов. |
   | `enabled-for-deployment` | Указывает [вариант развертывания хранилища ключей](/cli/azure/keyvault). |
   | `enabled-for-disk-encryption` | Указывает [вариант шифрования хранилища ключей](/cli/azure/keyvault). |
   | `enabled-for-template-deployment` | Указывает [вариант шифрования хранилища ключей](/cli/azure/keyvault). |
   | `sku` | Указывает [вариант номера SKU хранилища ключей](/cli/azure/keyvault). |
   | `query` | Указывает значение, извлекаемое из ответа — URI хранилища ключей, необходимый для работы с этим руководством. |

   Azure CLI отобразит этот URI для хранилища ключей для дальнейшего использования, например:  

   ```
   "https://wingtiptoyskeyvault.vault.azure.net"
   ```

4. Настройте политику доступа для ранее созданного субъекта-службы Azure, например:
   ```azurecli
   az keyvault set-policy --name wingtiptoyskeyvault --secret-permission set get list delete --spn "iiiiiiii-iiii-iiii-iiii-iiiiiiiiiiii"
   ```
   Описание

   | Параметр | ОПИСАНИЕ |
   |---|---|
   | `name` | Указывает имя используемого хранилища ключей (см. выше). |
   | `secret-permission` | Указывает [политики безопасности](/cli/azure/keyvault) для хранилища ключей. |
   | `spn` | Указывает идентификатор GUID для зарегистрированного приложения (см. выше). |

   В Azure CLI отобразятся результаты создания политики безопасности, например:  

   ```json
   {
     "id": "/subscriptions/ssssssss-ssss-ssss-ssss-ssssssssssss/...",
     "location": "westus",
     "name": "wingtiptoyskeyvault",
     "properties": {
       ...
       ... (A long list of values will be displayed here.)
       ...
     },
     "resourceGroup": "wingtiptoysresources",
     "tags": {},
     "type": "Microsoft.KeyVault/vaults"
   }
   ```

5. Сохраните секрет в новом хранилище ключей, например:
   ```azurecli
   az keyvault secret set --vault-name "wingtiptoyskeyvault" --name "connectionString" --value "jdbc:sqlserver://SERVER.database.windows.net:1433;database=DATABASE;"
   ```
   Описание

   | Параметр | ОПИСАНИЕ |
   |---|---|
   | `vault-name` | Указывает имя используемого хранилища ключей (см. выше). |
   | `name` | Указывает имя секрета. |
   | `value` | Указывает значение секрета. |

   В Azure CLI отобразятся результаты создания секрета, например:  

   ```json
   {
     "attributes": {
       "created": "2017-12-01T09:00:16+00:00",
       "enabled": true,
       "expires": null,
       "notBefore": null,
       "recoveryLevel": "Purgeable",
       "updated": "2017-12-01T09:00:16+00:00"
     },
     "contentType": null,
     "id": "https://wingtiptoyskeyvault.vault.azure.net/secrets/connectionString/123456789abcdef123456789abcdef",
     "kid": null,
     "managed": null,
     "tags": {
       "file-encoding": "utf-8"
     },
     "value": "jdbc:sqlserver://wingtiptoys.database.windows.net:1433;database=DATABASE;"
   }
   ```

## <a name="configure-and-compile-your-app"></a>Настройка и компиляция приложения

1. Распакуйте архив с файлами проекта Spring Boot, которые вы скачали в каталог.

2. В папке *main/src/ресурсы* проекта откройте файл *application.properties* в текстовом редакторе.

3. Добавьте значения для хранилища ключей, используя ранее выполненные действия, например:
   ```yaml
   azure.keyvault.uri=https://wingtiptoyskeyvault.vault.azure.net/
   azure.keyvault.client-id=iiiiiiii-iiii-iiii-iiii-iiiiiiiiiiii
   azure.keyvault.client-key=pppppppp-pppp-pppp-pppp-pppppppppppp
   ```
   Описание

   |          Параметр          |                                 ОПИСАНИЕ                                 |
   |-----------------------------|-----------------------------------------------------------------------------|
   |    `azure.keyvault.uri`     |           Указывает URI, полученный при создании хранилища ключей.           |
   | `azure.keyvault.client-id`  |  Указывает GUID *appId*, полученный при создании субъекта-службы.   |
   | `azure.keyvault.client-key` | Указывает GUID *password*, полученный при создании субъекта-службы. |


4. Перейдите к файлу с основным кодом проекта, например: */src/main/java/com/wingtiptoys/secrets*.

5. Откройте в текстовом редакторе основной Java-файл приложения, например *SecretsApplication.java*, и добавьте следующие строки в файл.

   ```java
   package com.wingtiptoys.secrets;

   import org.springframework.boot.SpringApplication;
   import org.springframework.boot.autoconfigure.SpringBootApplication;
   import org.springframework.beans.factory.annotation.Value;
   import org.springframework.boot.CommandLineRunner;

   @SpringBootApplication
   public class SecretsApplication implements CommandLineRunner {

      @Value("${connectionString}")
      private String connectionString;

      public static void main(String[] args) {
         SpringApplication.run(SecretsApplication.class, args);
      }

      public void run(String... varl) throws Exception {
         System.out.println(String.format("\nConnection String stored in Azure Key Vault:\n%s\n",connectionString));
      }
   }
   ```
   Этот пример кода извлекает строку подключения из хранилища ключей и отображает ее в командной строке.

6. Сохраните и закройте файл Java.

## <a name="build-and-test-your-app"></a>Создание и тестирование приложения

1. Перейдите в каталог с файлом *pom.xml* приложения Spring Boot:

1. Создайте приложение Spring Boot с помощью Maven, например:

   ```bash
   mvn clean package
   ```

   В Maven отобразятся результаты создания.

   ![Состояние сборки приложения Spring Boot][build-application-01]

1. Запустите приложение Spring Boot с помощью Maven. В приложении отобразится строка подключения, полученная из хранилища ключей. Например:

   ```bash
   mvn spring-boot:run
   ```

   ![Сообщение о времени выполнения Spring Boot][build-application-02]

## <a name="summary"></a>Сводка

В этом руководстве вы создали веб-приложение Java с помощью **[Spring Initializr]** , создали хранилище Azure Key Vault для хранения конфиденциальной информации, а затем настроили созданное приложение для получения сведений из хранилища ключей.

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о Spring и Azure см. в центре документации об использовании Spring в Azure.

> [!div class="nextstepaction"]
> [Spring в Azure](/azure/java/spring-framework)

### <a name="additional-resources"></a>Дополнительные ресурсы

См. дополнительные сведения об использовании Azure Key Vault:

* [Документация по Key Vault].

* [Приступая к работе с хранилищем ключей Azure]

Дополнительные сведения об использовании приложений Spring Boot в Azure см. в следующих статьях:

* [Развертывание приложения Spring Boot Application в службе приложений Azure](deploy-spring-boot-java-app-from-container-registry-using-maven-plugin.md)

* [Запуск приложения Spring Boot в кластере Kubernetes в Службе контейнеров Azure](deploy-spring-boot-java-app-on-kubernetes.md)

Дополнительные сведения об использовании Java в Azure см. в статьях [Azure для разработчиков Java] и [Working with Azure DevOps and Java] (Работа с Azure DevOps и Java).

<!-- URL List -->

[Документация по Key Vault]: /azure/key-vault/
[Приступая к работе с хранилищем ключей Azure]: /azure/key-vault/key-vault-get-started
[Azure для разработчиков Java]: /azure/java/
[бесплатной учетной записи Azure]: https://azure.microsoft.com/pricing/free-trial/
[Working with Azure DevOps and Java]: /azure/devops/ (Работа с Azure DevOps и Java)
[Преимущества для подписчиков MSDN]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Initializr]: https://start.spring.io/
[Spring Framework]: https://spring.io/

<!-- IMG List -->

[secrets-01]: media/configure-spring-boot-starter-java-app-with-azure-key-vault/secrets-01.png
[secrets-02]: media/configure-spring-boot-starter-java-app-with-azure-key-vault/secrets-02.png
[secrets-03]: media/configure-spring-boot-starter-java-app-with-azure-key-vault/secrets-03.png

[build-application-01]: media/configure-spring-boot-starter-java-app-with-azure-key-vault/build-application-01.png
[build-application-02]: media/configure-spring-boot-starter-java-app-with-azure-key-vault/build-application-02.png
