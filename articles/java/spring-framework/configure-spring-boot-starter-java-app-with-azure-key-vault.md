---
title: Как использовать начальное приложение Spring Boot Starter с Azure Key Vault
description: Сведения о настройке приложения Spring Boot Initializer с помощью начального приложения Azure Key Vault.
services: key-vault
documentationcenter: java
ms.date: 10/29/2019
ms.service: key-vault
ms.tgt_pltfrm: multiple
ms.topic: article
ms.workload: identity
ms.openlocfilehash: 2df574104376ec1900c7dc5cbd4f0a49ef1f4732
ms.sourcegitcommit: e6cdb0ce11a8272195a0072c7c91cc9b7e89b0b6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82138740"
---
# <a name="how-to-use-the-spring-boot-starter-for-azure-key-vault"></a>Как использовать начальное приложение Spring Boot Starter с Azure Key Vault

В этой статье описано, как создать с помощью **[Spring Initializr]** начальное приложение Spring Boot для Azure Key Vault для получения строки подключения, которая хранится в виде секрета в хранилище ключей.

## <a name="prerequisites"></a>Предварительные требования

Чтобы выполнить действия, описанные в этой статье, необходимо следующее:

* Подписка Azure. Если у вас ее еще нет, вы можете активировать [Преимущества для подписчиков MSDN] или зарегистрироваться для получения [бесплатной учетной записи Azure].
* Поддерживаемая версия Java Development Kit (JDK). Дополнительные сведения о версиях JDK, доступных для разработки в Azure, см. в статье <https://aka.ms/azure-jdks>.
* [Apache Maven](http://maven.apache.org/) версии 3.0 или более поздней.

## <a name="create-an-app-using-spring-initializr"></a>Создание приложения с помощью Spring Initializr

Следующая процедура создает приложение с помощью Spring Initializr.

1. Перейдите по адресу <https://start.spring.io/>.

1. Выберите в соответствующих полях **Maven Project** (Проект Maven) и **Java**.  

1. Заполните поля **Group** (Группа) и **Artifact** (Артефакт) для приложения.

1. В разделе **Dependencies** (Зависимости) введите **Azure Key Vault**.

1. Прокрутите до нижней части страницы и щелкните **Generate** (Создать).

   ![Создание проекта Spring Boot][secrets-01]

1. При появлении запроса скачайте проект на локальный компьютер.

## <a name="sign-into-azure"></a>Вход в Azure

Следующая процедура выполняет проверку подлинности пользователя в Azure CLI.

1. Откройте командную строку.

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

Следующая процедура создает и инициализирует хранилище ключей.

1. Создайте группу ресурсов Azure, которые будут использоваться для хранилища ключей, например:

   ```azurecli
   az group create --name vged-rg2 --location westus
   ```

   Где:

   | Параметр | Описание |
   |---|---|
   | `name` | Указывает уникальное имя для группы ресурсов. |
   | `location` | Указывает [регион Azure](https://azure.microsoft.com/regions/) для размещения группы ресурсов. |

   В Azure CLI отобразятся результаты созданной группы ресурсов, например:  

   ```json
   {
     "id": "/subscriptions/ssssssss-ssss-ssss-ssss-ssssssssssss/resourceGroups/vged-rg2",
     "location": "westus",
     "managedBy": null,
     "name": "vged-rg2",
     "properties": {
       "provisioningState": "Succeeded"
     },
     "tags": null
   }
   ```

2. Создайте хранилище ключей в группе ресурсов, например:

   ```azurecli
   az keyvault create --resource-group vged-rg2 \
       --name vgedkeyvault \
       --enabled-for-deployment true \
       --enabled-for-disk-encryption true \
       --enabled-for-template-deployment true \
       --sku standard --query properties.vaultUri \
       --location westus
   ```

   Где:

   | Параметр | Описание |
   |---|---|
   | `name` | Указывает уникальное имя для хранилища ключей. |
   | `enabled-for-deployment` | Указывает [вариант развертывания хранилища ключей](/cli/azure/keyvault). |
   | `enabled-for-disk-encryption` | Указывает [вариант шифрования хранилища ключей](/cli/azure/keyvault). |
   | `enabled-for-template-deployment` | Указывает [вариант шифрования хранилища ключей](/cli/azure/keyvault). |
   | `sku` | Указывает [вариант номера SKU хранилища ключей](/cli/azure/keyvault). |
   | `query` | Указывает значение, извлекаемое из ответа — URI хранилища ключей, необходимый для работы с этим руководством. |
   | `location` | Указывает [регион Azure](https://azure.microsoft.com/regions/) для размещения группы ресурсов. |

   Azure CLI отобразит этот URI для хранилища ключей для дальнейшего использования, например:  

   ```output
   "https://vgedkeyvault.vault.azure.net"
   ```

3. Сохраните секрет в новом хранилище ключей, например:

   ```azurecli
   az keyvault secret set --vault-name "vgedkeyvault" \
       --name "connectionString" \
       --value "jdbc:sqlserver://SERVER.database.windows.net:1433;database=DATABASE;"
   ```

   Где:

   | Параметр | Описание |
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
     "id": "https://vgedkeyvault.vault.azure.net/secrets/connectionString/123456789abcdef123456789abcdef",
     "kid": null,
     "managed": null,
     "tags": {
       "file-encoding": "utf-8"
     },
     "value": "jdbc:sqlserver://.database.windows.net:1433;database=DATABASE;"
   }
   ```

## <a name="configure-and-compile-your-app"></a>Настройка и компиляция приложения

Используйте следующую процедуру для настройки и компиляции приложения.

1. Распакуйте архив с файлами проекта Spring Boot, которые вы скачали в каталог.

2. В папке *src/main/resources* проекта откройте файл *application.properties* в текстовом редакторе.

3. Добавьте значения для хранилища ключей, используя ранее выполненные действия, например:

   ```yaml
   azure.keyvault.uri=https://vgedkeyvault.vault.azure.net/
   azure.keyvault.enabled=true
   ```

   Где:

   |          Параметр          |                                 Описание                                 |
   |-----------------------------|-----------------------------------------------------------------------------|
   |    `azure.keyvault.uri`     |           Указывает URI, полученный при создании хранилища ключей.           |
    
    
4. Перейдите к файлу с основным кодом проекта, например: */src/main/java/com/vged/secrets*.

5. Откройте в текстовом редакторе основной Java-файл приложения, например *SecretsApplication.java*, и добавьте следующие строки в файл.

   ```java
   package com.vged.secrets;

   import org.springframework.boot.SpringApplication;
   import org.springframework.boot.autoconfigure.SpringBootApplication;
   import org.springframework.beans.factory.annotation.Value;
   import org.springframework.boot.CommandLineRunner;
   import org.springframework.web.bind.annotation.GetMapping;
   import org.springframework.web.bind.annotation.RestController;
   
   @SpringBootApplication
   @RestController
   public class SecretsApplication implements CommandLineRunner {

      @Value("${connectionString}")
      private String connectionString;

      public static void main(String[] args) {
         SpringApplication.run(SecretsApplication.class, args);
      }
   
      @GetMapping("get")
      public String get() {
         return connectionString;
      }
   
      public void run(String... varl) throws Exception {
         System.out.println(String.format("\nConnection String stored in Azure Key Vault:\n%s\n",connectionString));
      }
   }
   ```
   Этот пример кода позволяет получить строку подключения из хранилища ключей и отобразить ее по URL-адресу `https://{your-appservice-name}.azurewebsites.net/get`.

6. Сохраните и закройте файл Java.

7. Отключите тестирование и выполните сборку JAR-файла с помощью Maven.
    
   ```bash
   mvn clean package -Dmaven.test.skip=true
   ```

## <a name="configure-maven-plugin-for-azure-app-service"></a>Настройка подключаемого модуля Maven для Службы приложений Azure

В этом разделе объясняется, как настроить проект Spring Boot, чтобы обеспечить развертывание приложения в Службе приложений Azure.

1.  Перейдите к разделу [Настройка подключаемого модуля Maven для Службы приложений Azure].
    
    В нем объясняется, как создать экземпляр Службы приложений Azure. Если вы хотите развернуть приложение в существующем экземпляре, можно повторно настроить развертывание с помощью команды `mvn azure-webapp:config` и выбрать часть приложения для настройки.
    
    ```bash
    [INFO] Scanning for projects...                                                     
    [INFO]                                                                              
    [INFO] ----------------------< com.wingtiptoys:secrets >-----------------------     
    [INFO] Building secrets 0.0.1-SNAPSHOT                                              
    [INFO] --------------------------------[ jar ]---------------------------------     
    [INFO]                                                                              
    [INFO] --- azure-webapp-maven-plugin:1.9.0:config (default-cli) @ secrets ---       
    Please choose which part to config                                                  
    1. Application                                                                      
    2. Runtime                                                                          
    3. DeploymentSlot                                                                   
    Enter index to use: 1                                                              
    Define value for appName(Default: ********):                                      
    Define value for resourceGroup(Default: ********):                                 
    Define value for region(Default: ********):                                           
    Define value for pricingTier(Default: P1v2):                                        
    1. b1                                                                               
    2. b2                                                                               
    3. b3                                                                               
    4. d1                                                                               
    5. f1                                                                               
    6. p1v2 [*]                                                                         
    7. p2v2                                                                             
    8. p3v2                                                                             
    9. s1                                                                               
    10. s2                                                                              
    11. s3                                                                              
    Enter index to use:                                                                 
    Please confirm webapp properties                                                                                                          
    ```
    
    Кроме того, вы можете напрямую изменить раздел `<configuration>` для `<azure-webapp-maven-plugin>` в файле *pom.xml*. Измените значения `<resourceGroup>`, `<appName>` и `<region>` для вашего экземпляра Службы приложений.

2. Назначьте удостоверение Службе приложений и запишите значение `principalId`, которое потребуется на следующем шаге.

   ```bash
   az webapp identity assign --name your-appservice-name \
      --resource-group vged-rg2
   ```
   
3. Предоставьте MSI разрешение.

   ```bash
   az keyvault set-policy --name vgedkeyvault \
       --object-id your-managed-identity-objectId \
       --secret-permissions get list
   ```

## <a name="deploy-the-app-to-azure-and-run-app-service"></a>Развертывание приложения в Azure и запуск Службы приложений

Теперь все готово для развертывания веб-приложения в Azure. Для этого выполните следующие действия.

1. Перестройте JAR-файл с помощью Maven, если вы внесли изменения в файл *pom.xml*.

   ```bash
   mvn clean package
   ```
   
2. Разверните приложение в Azure с помощью Maven.

   ```bash
   mvn azure-webapp:deploy
   ```
   
3. Перезапустите Службу приложений.

4. Проверьте URL-адрес `https://{your-appservice-name}.azurewebsites.net/get` в браузере, чтобы получить значение `connectionString`.
   

## <a name="summary"></a>Сводка

В этом руководстве вы создали веб-приложение Java с помощью **[Spring Initializr]** , создали хранилище Azure Key Vault для хранения конфиденциальной информации, а затем настроили созданное приложение для получения сведений из хранилища ключей.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о Spring и Azure см. в центре документации об использовании Spring в Azure.

> [!div class="nextstepaction"]
> [Spring в Azure](/azure/developer/java/spring-framework)

### <a name="additional-resources"></a>Дополнительные ресурсы

См. дополнительные сведения об использовании Azure Key Vault:

* [Документация по Key Vault].

* [Приступая к работе с хранилищем ключей Azure]

Дополнительные сведения об использовании приложений Spring Boot в Azure см. в следующих статьях:

* [Развертывание приложения Spring Boot Application в службе приложений Azure](deploy-spring-boot-java-app-from-container-registry-using-maven-plugin.md)

* [Запуск приложения Spring Boot в кластере Kubernetes в Службе контейнеров Azure](deploy-spring-boot-java-app-on-kubernetes.md)

Дополнительные сведения об использовании Java в Azure см. в статьях [Azure для разработчиков Java] и [Working with Azure DevOps and Java] (Работа с Azure DevOps и Java).

Дополнительные сведения об использовании управляемых удостоверений для Службы приложений см. в [Использование управляемых удостоверений для Службы приложений].

<!-- URL List -->

[Документация по Key Vault]: /azure/key-vault/
[Приступая к работе с хранилищем ключей Azure]: /azure/key-vault/key-vault-get-started
[Azure для разработчиков Java]: /azure/developer/java/
[бесплатной учетной записи Azure]: https://azure.microsoft.com/pricing/free-trial/
[Working with Azure DevOps and Java]: /azure/devops/ (Работа с Azure DevOps и Java)
[Преимущества для подписчиков MSDN]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Initializr]: https://start.spring.io/
[Spring Framework]: https://spring.io/
[Использование управляемых удостоверений для Службы приложений]: /azure/app-service/overview-managed-identity
[Настройка подключаемого модуля Maven для Службы приложений Azure]: /azure/developer/java/spring-framework/deploy-spring-boot-java-app-with-maven-plugin#configure-maven-plugin-for-azure-app-service

<!-- IMG List -->

[secrets-01]: media/configure-spring-boot-starter-java-app-with-azure-key-vault/secrets-01.png
[secrets-02]: media/configure-spring-boot-starter-java-app-with-azure-key-vault/secrets-02.png
[secrets-03]: media/configure-spring-boot-starter-java-app-with-azure-key-vault/secrets-03.png

[build-application-01]: media/configure-spring-boot-starter-java-app-with-azure-key-vault/build-application-01.png
[build-application-02]: media/configure-spring-boot-starter-java-app-with-azure-key-vault/build-application-02.png
