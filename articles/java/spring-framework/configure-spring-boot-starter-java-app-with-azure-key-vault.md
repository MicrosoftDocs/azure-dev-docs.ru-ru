---
title: Учебник по считыванию секрета из Azure Key Vault в приложении Spring Boot
description: Учебник по считыванию секрета из Azure Key Vault в приложении Spring Boot
services: key-vault
documentationcenter: java
ms.date: 08/15/2020
ms.service: key-vault
ms.tgt_pltfrm: multiple
ms.topic: tutorial
ms.workload: identity
ms.custom: devx-track-java
ms.openlocfilehash: d3504137c2c9a8ee2b1af960a17deb902be95d7a
ms.sourcegitcommit: 39f3f69e3be39e30df28421a30747f6711c37a7b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/21/2020
ms.locfileid: "90831990"
---
# <a name="tutorial-reading-a-secret-from-azure-key-vault-in-a-spring-boot-application"></a>Руководство по Считывание секрета из Azure Key Vault в приложении Spring Boot

Приложения Spring Boot хранят всю конфиденциальную информацию, такую как имена пользователей и пароли, во внешнем хранилище.  Это повышает безопасность, удобство поддержки и тестирования.  Хранение секретов за пределами кода предпочтительнее, чем встраивание этой информации в код во время разработки или сборки.

В этом учебнике описывается, как создать приложение Spring Boot, которое считывает значение из Azure Key Vault, и развернуть приложение в Службе приложений Azure и Azure Spring Cloud.

> [!div class="checklist"]
> * Создание Azure Key Vault и сохранение секрета
> * Создание приложения с помощью Spring Initializr
> * Добавление интеграции с Key Vault в приложение
> * Развертывание в службе приложений Azure
> * Повторное развертывание в Службе приложений Azure с управляемыми удостоверениями для ресурсов Azure
> * Развертывание в Azure Spring Cloud

## <a name="prerequisites"></a>Предварительные требования

* Активная подписка Azure.
  * Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/).
* [Установите Azure CLI версии 2.0.67 или более поздней](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest) и расширение Azure Spring Cloud с помощью команды `az extension add --name spring-cloud`.
* Поддерживаемая версия Java Development Kit (JDK). Дополнительные сведения о версиях JDK, доступных для разработки в Azure, см. в статье <https://aka.ms/azure-jdks>.
* [Apache Maven](http://maven.apache.org/) версии 3.0 или более поздней.
* Команда `curl`.  В большинстве операционных систем из семейства UNIX эта команда уже предварительно установлена.  Клиенты для конкретных ОС можно скачать на [официальном веб-сайте curl](https://curl.haxx.se/).
* Команда `jq`. В большинстве операционных систем из семейства UNIX эта команда уже предварительно установлена.  Клиенты для конкретных ОС можно скачать на [официальном веб-сайте jq](https://stedolan.github.io/jq/).

## <a name="create-a-new-azure-key-vault"></a>Создание хранилища Azure Key Vault

В следующих разделах показано, как войти в Azure и создать хранилище Azure Key Vault.

### <a name="sign-into-azure-and-set-your-subscription"></a>Вход в Azure и настройка подписки

Прежде всего выполните следующие действия для аутентификации с помощью Azure CLI.

1. Если потребуется, выйдите из системы и удалите некоторые файлы аутентификации, чтобы удалить устаревшие учетные данные:

   ```azurecli
   az logout
   rm ~/.azure/accessTokens.json
   rm ~/.azure/azureProfile.json
   ```

1. Войдите в учетную запись Azure с помощью интерфейса командной строки Azure.

   ```azurecli
   az login
   ```

   Для завершения процесса входа следуйте инструкциям.

1. Отобразите список подписок:

   ```azurecli
   az account list
   ```

   Azure возвратит список подписок. Скопируйте `id` для той подписки, которую вы намерены использовать, например:

   ```json
   [
     {
       "cloudName": "AzureCloud",
       "id": "ssssssss-ssss-ssss-ssss-ssssssssssss",
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

1. Укажите GUID подписки, которую вы собираетесь использовать в Azure, например:

   ```azurecli
   az account set -s ssssssss-ssss-ssss-ssss-ssssssssssss
   ```

### <a name="create-a-service-principal-for-use-in-by-your-app"></a>Создание субъекта-службы для использования в приложении

*Субъекты-службы* в Azure AD используются для доступа к ресурсам Azure в подписке. Субъект-службу можно считать удостоверением пользователя для службы.  "Служба" здесь обозначает любое приложение, службу или платформу (в том числе созданный в этом учебнике пример приложения), которым требуется доступ к ресурсам Azure. Субъекту-службе можно присвоить права доступа, ограниченные конкретными ресурсами. После этого вам останется лишь настроить применение этого субъекта-службы для доступа к ресурсам из приложения или службы.

Создайте субъект-службу с помощью предложенной команды.

```azurecli
az ad sp create-for-rbac --name contososp
```

Значение `name` должно быть уникальным в пределах вашей подписки.  Сохраните значения, возвращаемые командой, для дальнейшего использования в этом учебнике.  Результат в формате JSON будет выглядеть примерно следующим образом.

```json
{
  "appId": "8r7o486s-o5q9-450s-8457-pr26p86n0497",
  "displayName": "ejbcontososp",
  "name": "http://ejbcontososp",
  "password": "4bt.lCKJKlbYLn_3XF~wWtUwyHU0jKggu2",
  "tenant": "72s988os-86s1-41ns-91no-2d7cd011db47"
}
```

### <a name="create-the-key-vault-instance"></a>Создание экземпляра Key Vault

Следующая процедура создает и инициализирует Key Vault.

1. Определите, в каком регионе Azure будут храниться ваши ресурсы.
   1. Вы можете просмотреть [список регионов и расположений](https://azure.microsoft.com/regions/).
   1. Чтобы найти правильное значение `Name` для выбранного региона, можно использовать команду `az account list-locations`.

      ```azurecli
      az account list-locations -o table
      ```

      В этом учебнике используется значение `eastus`.
1. Создайте группу ресурсов для хранения Key Vault и приложения Службы приложений.  Это значение должно быть уникальным в пределах подписки Azure.  В этом учебнике используется значение `contosorg`.

   ```azurecli
   az group create --name contosorg --location eastus
   ```

1. Создайте Key Vault в группе ресурсов.

   ```azurecli
   az keyvault create \
       --resource-group contosorg \
       --name contosokv \
       --enabled-for-deployment true \
       --enabled-for-disk-encryption true \
       --enabled-for-template-deployment true \
       --location eastus
       --query properties.vaultUri \
       --sku standard 
   ```

    > [!NOTE]
    > Значение `--name` должно быть уникальным в пределах подписки Azure.

   В этой таблице описаны представленные выше параметры.

   | Параметр | Описание |
   |---|---|
   | `enabled-for-deployment` | Указывает [вариант развертывания Key Vault](/cli/azure/keyvault). |
   | `enabled-for-disk-encryption` | Указывает [вариант шифрования Key Vault](/cli/azure/keyvault). |
   | `enabled-for-template-deployment` | Указывает [вариант шифрования Key Vault](/cli/azure/keyvault). |
   | `location` | Указывает [регион Azure](https://azure.microsoft.com/regions/) для размещения группы ресурсов. |
   | `name` | Указывает уникальное имя для Key Vault. |
   | `query` | Получите URI для Key Vault из ответа.  Этот URI вам потребуется для работы с этим учебником. |
   | `sku` | Указывает [вариант ценовой категории для Key Vault](/cli/azure/keyvault). |

   Azure CLI отобразит этот URI для Key Vault, который вы примените позднее, например:

   ```output
   "https://contosokv.vault.azure.net/"
   ```

1. Настройте Key Vault, чтобы разрешить операции `get` и `list` из этого управляемого удостоверения.  Значением `object-id` является `appId` из приведенной выше команды `az ad sp create-for-rbac`.

   ```azurecli
   az keyvault set-policy --name contosokv --spn http://ejbcontososp --secret-permissions get list
   ```

   В качестве выходных данных будет получен объект JSON, заполненный сведениями о Key Vault.  Он будет включать запись `type` со значением `Microsoft.KeyVault/vaults`.

   В этой таблице описаны представленные выше свойства.

   | Параметр | Описание |
   |---|---|
   | name | Имя Key Vault. |
   | spn | Значение `name` из выходных данных представленной выше команды `az ad sp create-for-rbac`. |
   | secret-permissions | Список разрешенных операций из именованного субъекта. |

    > [!NOTE]
    > Хотя в соответствии с принципом минимальных разрешений рекомендуется предоставлять минимально возможный набор разрешений для ресурса, архитектура интеграции Key Vault требует по меньшей мере наличия разрешений `get` и `list`.

1. Сохраните секрет в новом Key Vault.  Типичный вариант использования — сохранить строку подключения JDBC.  Пример:

   ```azurecli
   az keyvault secret set --name "connectionString" \
       --vault-name "contosokv" \
       --value "jdbc:sqlserver://SERVER.database.windows.net:1433;database=DATABASE;"
   ```

   В этой таблице описаны представленные выше параметры.

   | Параметр | Описание |
   |---|---|
   | `name` | Указывает имя секрета. |
   | `value` | Указывает значение секрета. |
   | `vault-name` | Указывает имя Key Vault, упомянутое выше. |

   В Azure CLI отобразятся результаты создания секрета, например:

   ```json
   {
     "attributes": {
       "created": "2020-08-24T21:48:09+00:00",
       "enabled": true,
       "expires": null,
       "notBefore": null,
       "recoveryLevel": "Purgeable",
       "updated": "2020-08-24T21:48:09+00:00"
     },
     "contentType": null,
     "id": "https://contosokv.vault.azure.net/secrets/connectionString/123456789abcdef123456789abcdef",
     "kid": null,
     "managed": null,
     "tags": {
       "file-encoding": "utf-8"
     },
     "value": "jdbc:sqlserver://.database.windows.net:1433;database=DATABASE;"
   }
   ```

Итак, вы создали хранилище Key Vault и сохранили в нем секрет, а в следующем разделе мы покажем, как создать приложение с помощью Spring Initializr.

## <a name="create-the-app-with-spring-initializr"></a>Создание приложения с помощью Spring Initializr

В этом разделе описано, как с помощью Spring Initializr и `RestController` создать приложение Spring Boot и запустить его в локальной среде.

1. Перейдите по адресу <https://start.spring.io/>.
1. Выберите параметры, как показано на рисунке ниже.
   1. **Project (Проект)** : `Maven Project`
   1. **Language (Язык)** : `Java`
   1. **Spring Boot**: `2.3.3`
   1. **Group (Группа)** : `com.contoso` (Здесь можно указать любое допустимое имя пакета Java.)
   1. **Artifact (Артефакт)** : *keyvault* (Здесь можно указать любое допустимое имя класса Java.)
   1. **Packaging (Формат пакета)** : `Jar`
   1. **Java**. `11` (Можно выбрать 8, но этот учебник проверялся с версией 11.)
1. Щелкните **Add Dependencies...** (Добавить зависимости).
1. В этом текстовом поле введите `Spring Web` и нажмите клавиши CTRL+ВВОД.
1. В этом текстовом поле введите `Azure Key Vault` и нажмите клавишу ВВОД.  Теперь экран будет выглядеть следующим образом.
   :::image type="content" source="media/configure-spring-boot-starter-java-app-with-azure-key-vault/spring-initializr-choices.png" alt-text="Экран Spring Initializr, где выбраны правильные варианты.":::
1. Внизу страницы щелкните **Generate** (Создать).
1. При появлении запроса скачайте проект на локальный компьютер.  Предварительно мы укажем каталог *keyvault* в корневом каталоге текущего пользователя.  С представленными выше значениями будет создан файл *keyvault.zip* в этом каталоге.

Выполните эти шаги, чтобы изучить приложение и запустить его в локальной среде.

1. Распакуйте архив *keyvault.zip*.  Макет этого файла будут выглядеть следующим образом.  В этом учебнике мы не используем папку *test* и ее содержимое.

   ```bash
   ├── HELP.md
   ├── mvnw
   ├── mvnw.cmd
   ├── pom.xml
   └── src
       ├── main
       │   ├── java
       │   │   └── com
       │   │       └── contoso
       │   │           └── keyvault
       │   │               └── KeyvaultApplication.java
       │   └── resources
       │       ├── application.properties
       │       ├── static
       │       └── templates
   ```

1. Откройте файл *KeyvaultApplication.java* в текстовом редакторе.  Измените этот файл так, чтобы он выглядел следующим образом.

   * Этот класс дополнен меткой `@RestController`.  `@RestController` сообщает Spring Boot, что класс умеет отвечать на HTTP-запросы RESTful.
   * В этом классе есть метод с меткой `@GetMapping(get)`.  `@GetMapping` сообщает Spring Boot, что HTTP-запросы этому методу нужно отправлять по пути `/get`, что позволит возвращать клиенту HTTP ответы от этого метода.
   * В этом классе есть частная переменная экземпляра `connectionString`.  Значение этой переменной экземпляра возвращается из метода `get()`.

   ```java
   import org.springframework.boot.SpringApplication;
   import org.springframework.boot.autoconfigure.SpringBootApplication;
   import org.springframework.web.bind.annotation.GetMapping;
   import org.springframework.web.bind.annotation.RestController;

   @SpringBootApplication
   @RestController
   public class KeyvaultApplication {

      public static void main(String[] args) {
        SpringApplication.run(KeyvaultApplication.class, args);
      }

     @GetMapping("get")
     public String get() {
       return connectionString;
     }

     private String connectionString = "defaultValue\n";  

     public void run(String... varl) throws Exception {
       System.out.println(String.format("\nConnection String stored in Azure Key Vault:\n%s\n",connectionString));
     }  

   }
   ```

1. В каталоге *keyvault* верхнего уровня, где расположен файл *pom.xml*, введите команду `mvn spring-boot:run`.  
1. Сообщение **Completed initialization** (Инициализация завершена) в выходных данных этой команды означает, что сервер готов к работе.  В другом окне оболочки введите эту команду.

   ```bash
   $ curl http://localhost:8080/get
   ```

   В выходных данных вы увидите `defaultValue`.

1. Завершите процесс, который был запущен из `mvn spring-boot:run`.  Для этого можно нажать клавиши Ctrl+C или использовать команду `jps`, чтобы получить идентификатор процесса `Launcher` и завершить его.

В следующем разделе описано, как добавить интеграцию с Key Vault в локально выполняемое приложение.

## <a name="add-key-vault-integration-to-the-app"></a>Добавление интеграции с Key Vault в приложение

Следующие шаги демонстрируют необходимые изменения, которые нужно внести в приложение Spring Boot `KeyvaultApplication`.

Так же, как Key Vault позволяет хранить секреты отдельно от кода приложения, конфигурация Spring позволяет хранить конфигурацию отдельно от кода.  Простейший пример конфигурации Spring представлен в файле *application.properties*.  В проекте Maven этот файл расположен по адресу *src/main/resources/application.properties*.  Spring Initializr для удобства размещает в этом расположении файл нулевой длины.

Выполните следующие шаги для добавления конфигурации в этот файл.

1. Откройте файл *src/main/resources/application.properties* в текстовом редакторе и разместите в нем следующее содержимое, указав значения для реальной подписки Azure.

   ```txt
   azure.keyvault.client-id=685on005-ns8q-4o04-8s16-n7os38o2ro5n
   azure.keyvault.client-key=4bt.lCKJKlbYLn_3XF~wWtUwyHU0jKggu2
   azure.keyvault.enabled=true
   azure.keyvault.tenant-id=72s988os-86s1-41ns-91no-2q7pq011qo47
   azure.keyvault.uri=https://contosokv.vault.azure.net/
   ```

   В этой таблице описаны представленные выше свойства.

   | Параметр | Описание |
   |---|---|
   | azure.keyvault.client-id | Значение `appId` из результата JSON, полученного от `az ad sp create-for-rbac`.|
   | azure.keyvault.client-key | Значение `password` из результата JSON, полученного от `az ad sp create-for-rbac`.|
   | azure.keyvault.enabled | Эта конфигурация будет полезной, когда на этапе разработки нужно задать `enabled` или `disabled`.  Дополнительные сведения о конфигурации Spring см. в [документации по Spring](https://docs.spring.io/spring-boot/docs/2.2.2.RELEASE/reference/htmlsingle/#boot-features-external-config).
   | azure.keyvault.tenant-id | Значение `tenant` из результата JSON, полученного от `az ad sp create-for-rbac`.|
   | azure.keyvault.uri | Значение из выходных данных приведенной выше команды `az keyvault create`. |

   Полный список доступных свойств включен в [справку по свойствам](https://aka.ms/azure-spring-boot-starter-keyvault-secrets).

1. Сохраните файл и закройте его.

Внесите небольшое изменение в файл *KeyvaultApplication.java* (или файл с тем именем класса, который вы используете).

1. Откройте *src/main/java/com/contoso/keyvault/KeyvaultApplication.java* в текстовом редакторе.
1. Добавьте эту инструкцию import.

   ```java
   import org.springframework.beans.factory.annotation.Value;
   ```

1. Добавьте метку к переменной экземпляра `connectionString`.

   ```java
   @Value("${connectionString}")
   private String connectionString;  
   ```

   Интеграция с Key Vault предоставляет метку Spring `PropertySource`, заполненную значениями для Key Vault.  Некоторые подробности реализации доступны в [справочной документации](https://aka.ms/azure-spring-boot-starter-keyvault-secrets).

1. В каталоге *keyvault* верхнего уровня, где расположен файл *pom.xml*, введите команду `mvn clean package spring-boot:run`.  
1. Сообщение **initialization completed** (Инициализация завершена) в выходных данных этой команды означает, что сервер готов к работе.  В другом окне оболочки введите эту команду.

   ```bash
   $ curl http://localhost:8080/get
   ```

   В окне выходных данных отобразится `jdbc:sqlserver://SERVER.database.windows.net:1433;database=DATABASE` вместо `defaultValue`.

1. Завершите процесс, который был запущен из `mvn spring-boot:run`.  Для этого можно нажать клавиши Ctrl+C или использовать команду `jps`, чтобы получить идентификатор процесса `Launcher` и завершить его.


В следующем разделе показано, как развернуть это приложение в Службе приложений Azure.

## <a name="deploy-to-azure-app-service"></a>Развертывание в службе приложений Azure

Выполните шаги в этом разделе, чтобы развернуть `KeyvaultApplication` в Службе приложений Azure.

### <a name="use-the-azure-maven-web-app-plugin-to-deploy-the-application-to-azure-app-service"></a>Развертывание приложения в Службе приложений Azure с помощью подключаемого модуля веб-приложения Azure Maven

Выполните эти шаги, чтобы подготовить POM к развертыванию `KeyvaultApplication` в Службе приложений Azure.

1. В каталоге *keyvault* верхнего уровня откройте файл *pom.xml*.
1. В разделе `<build><plugins>` добавьте `azure-webapp-maven-plugin`, включив следующий код XML.

   ```xml
    <plugin>
     <groupId>com.microsoft.azure</groupId>
     <artifactId>azure-webapp-maven-plugin</artifactId>
     <version>1.9.1</version>
    </plugin>
   ```

   > [!NOTE]
   > Не обращайте внимание на форматирование.  В ходе этого процесса `azure-webapp-maven-plugin` изменит все форматирование POM.

1. Сохраните и закройте файл *pom.xml*.
1. В командной строке вызовите целевой объект `config` из только что добавленного подключаемого модуля.  Подключаемый модуль maven предложит вам несколько вопросов и на основе полученных ответов внесет некоторые изменения в файл *pom.xml*.  Дальше нужно еще немного подправить этот POM.

   ```bash
   mvn azure-webapp:config
   ```

1. В качестве ОС должна быть выбрана `linux`.
1. Для `javaVersion` нужно выбрать ту же версию Java, которую вы указали в Spring Initializr.  Выше мы выбрали `11`, так что здесь выберите 11.
1. Для остальных вопросов сохраните значения по умолчанию.
1. На запрос подтверждения введите Y, чтобы продолжить процесс, или N, чтобы повторно пройти те же вопросы.  Когда подключаемый модуль завершит работу, можно перейти к редактированию POM.

Выполните следующие шаги, чтобы внести необходимые изменения в POM.

1. В каталоге *keyvault* верхнего уровня откройте файл *pom.xml*.
1. Найдите запись `azure-webapp-maven-plugin` в разделе <plugins>.
1. Измените значения `<resourceGroup>`, `<appName>` и `<region>`.  
   1. Для `<resourceGroup>` укажите то же значение, которое вы выбрали при создании Key Vault.
   1. Выберите допустимое значение `<appName>`, которое будет уникальным в пределах всей подписки.
   1. Для `<region>` укажите то же значение, которое вы выбрали при создании Key Vault.
1. Добавьте элемент `<appSettings>`, который сообщит серверу, что нужно ожидать передачи данных по порту TCP 80.
1. Готовая версия `<plugin>` для `azure-webapp-maven-plugin` приведена ниже.  Значения, которые вам нужно изменить, помечены `*`.

   ```xml
   <plugins> 
     <plugin> 
       <groupId>org.springframework.boot</groupId>  
       <artifactId>spring-boot-maven-plugin</artifactId> 
     </plugin>  
     <plugin> 
       <groupId>com.microsoft.azure</groupId>  
       <artifactId>azure-webapp-maven-plugin</artifactId>  
       <version>1.9.1</version>  
       <configuration>
         <schemaVersion>V2</schemaVersion>
         *<resourceGroup>contosorg</resourceGroup>
         *<appName>contosokeyvault</appName>
         <pricingTier>P1v2</pricingTier>
         *<region>eastus</region>
         <runtime>
           <os>linux</os>
           <javaVersion>java11</javaVersion>
           <webContainer>java11</webContainer>
         </runtime>
         *<!-- Begin of App Settings  -->
         *<appSettings>
         *  <property>
         *    <name>JAVA_OPTS</name>
         *    <value>-Dserver.port=80</value>
         *  </property>
         *</appSettings>
         *<!-- End of App Settings  -->          
         <deployment>
           <resources>
             <resource>
               <directory>${project.basedir}/target</directory>
               <includes>
                 <include>*.jar</include>
               </includes>
             </resource>
           </resources>
         </deployment>
       </configuration>
     </plugin> 
   </plugins>
   ```

1. Сохраните и закройте POM.
1. Разверните приложение в Службе приложений Azure.

   ```bash
   mvn -DskipTests clean package azure-webapp:deploy
   ```

1. Выполнение этой команды может занять несколько минут в зависимости от многих факторов, на которые вы не можете повлиять.  Если вы видите в выходных данных что-то похожее на представленное здесь, значит приложение успешно развернуто.

   ```bash
   [INFO] Deploying the zip package contosokeyvault-22b7c1a3-b41b-4082-a9f0-9339723fa36a11893059035499017844.zip...
   [INFO] Successfully deployed the artifact to https://contosokeyvault.azurewebsites.net
   [INFO] ------------------------------------------------------------------------
   [INFO] BUILD SUCCESS
   [INFO] ------------------------------------------------------------------------
   [INFO] Total time:  01:45 min
   [INFO] Finished at: 2020-08-16T22:47:48-04:00
   [INFO] ------------------------------------------------------------------------
   ```

1. Подождите примерно три-пять минут, пока выполняется развертывание.  После этого вы сможете обратиться к нему с помощью команды `curl`, пример которой приводится выше, но теперь укажите в ней имя узла из выходных данных `BUILD SUCCESS`.  Например, такие выходные данные команды `curl` обозначают успешное выполнение.

   ```bash
   curl https://contosokeyvault.azurewebsites.net/get
   jdbc:sqlserver://SERVER.database.windows.net:1433;database=DATABASE;
   ```

Итак, вы развернули приложение в Службе приложений Azure.

## <a name="redeploy-to-azure-app-service-and-use-managed-identities-for-azure-resources"></a>Повторное развертывание в Службе приложений Azure с использованием управляемых удостоверений для ресурсов Azure

В этом разделе описано, как связать удостоверения с ресурсами Azure для приложения.  Это нужно для того, чтобы Azure смогла применить правила безопасности и отслеживать доступ.  Оплата только тех ресурсов, которые вы реально используете, является одним из основополагающих принципов облачных вычислений.  Высокая детализация отслеживания ресурсов становится возможной только в том случае, если каждый ресурс связан с удостоверением.  Служба приложений Azure и Azure Key Vault — эти лишь два примера из множества служб Azure, которые используют управляемые удостоверения для ресурсов Azure.  Подробнее об этой важной технологии см. в статье [Что такое управляемые удостоверения для ресурсов Azure?](/azure/active-directory/managed-identities-azure-resources/overview)

> [!NOTE]
> Управляемые удостоверения для ресурсов Azure — это новое название службы "Управляемое удостоверение службы" (MSI).

Выполните эти шаги, чтобы создать управляемое удостоверение для приложения Службы приложений Azure и разрешить этому удостоверению доступ к Key Vault.

1. Создание управляемого удостоверения для приложения Службы приложений.  Замените значения этих параметров значениями элементов `<resourceGroup>` и `<appName>` из вашего POM.

   ```azurecli
   az webapp identity assign --resource-group contosorg --name contosokeyvault
   ```

   Выход будет иметь примерно следующий вид:  Запишите значение `principalId`, оно вам потребуется на следующем шаге.

   ```json
   {
     "principalId": "2r7s6r00-92o9-4sq7-n10r-popq294ssr8s",
     "tenantId": "72s988os-86s1-41ns-91no-2q7pq011qo47",
     "type": "SystemAssigned",
     "userAssignedIdentities": null
   }
   ```

1. Измените файл *application.properties* таким образом, чтобы он включал имя управляемого удостоверения для ресурсов Azure, созданных на предыдущем шаге.

   1. Удалите `azure.keyvault.client-key`.
   1. Обновите `azure.keyvault.client-id` таким образом, чтобы он включал значение `principalId` из предыдущего шага.  Теперь готовый файл будет выглядеть примерно так.

   ```bash
   azure.keyvault.client-id=56rqs994-0o66-43o3-9roo-8e3534d0cb23
   azure.keyvault.enabled=true
   azure.keyvault.tenant-id=72s988os-86s1-41ns-91ab-2q7pq011qo47
   azure.keyvault.uri=https://contosokv.vault.azure.net/    
   ```

1. Настройте Key Vault, чтобы разрешить операции `get` и `list` из этого управляемого удостоверения.  Параметр `object-id` содержит значение `principalId` из полученных ранее выходных данных.

   ```azurecli
   az keyvault set-policy --name contosokv \
     --object-id 2r7s6r00-92o9-4sq7-n10r-popq294ssr8s --secret-permissions get list
   ```

   В качестве выходных данных будет получен объект JSON, заполненный сведениями о Key Vault.  Он будет включать запись `type` со значением `Microsoft.KeyVault/vaults`.

   В этой таблице описаны представленные выше свойства.

   | Параметр | Описание |
   |---|---|
   | name | Имя Key Vault. | 
   | object-id | Значение `principalId`, полученное из предыдущей команды. |
   | secret-permissions | Список разрешенных операций из именованного субъекта. |

1. Упакуйте приложение и повторно разверните его.

   ```bash
   mvn -DskipTests clean package azure-webapp:deploy
   ```

1. Подождите еще несколько минут, чтобы гарантировать успешное завершение развертывания.  После этого вы сможете обратиться к нему с помощью команды `curl`, пример которой приводится выше, но теперь укажите в ней имя узла из выходных данных `BUILD SUCCESS`.  Например, такие выходные данные команды `curl` обозначают успешное выполнение.

   ```bash
   curl https://contosokeyvault.azurewebsites.net/get
   jdbc:sqlserver://SERVER.database.windows.net:1433;database=DATABASE;
   ```

Вместо того, чтобы возвращать `defaultValue`, проверяется и возвращается значение `connectionString` из Key Vault.  В следующем разделе мы развернем это же приложение в Azure Spring Cloud.

## <a name="deploy-to-azure-spring-cloud"></a>Развертывание в Azure Spring Cloud

Azure Spring Cloud представляет собой полностью управляемую платформу для развертывания и выполнения приложений Spring Boot в Azure.  Обзор Azure Spring Cloud см. в статье [Что представляет собой Azure Spring Cloud?](/azure/spring-cloud/spring-cloud-overview).

В этом разделе мы применим приложение Spring Boot и хранилище Key Vault, созданные ранее, к новому экземпляру Azure Spring Cloud.

Следующие шаги демонстрируют, как создать ресурс Azure Spring Cloud и развернуть в нем приложение.  Обязательно установите расширение Azure CLI для Azure Spring Cloud, как указано в разделе [Предварительные требования](#prerequisites).

1. Выберите имя для экземпляра службы.  Чтобы использовать Azure Spring Cloud в подписке Azure, вам нужно создать ресурс Azure с типом Azure Spring Cloud.  Как и все остальные ресурсы Azure, экземпляр службы должен размещаться в группе ресурсов.  Выберите для этого ту же группу ресурсов, которую вы создали для хранения экземпляра службы.  Создайте экземпляр службы с помощью этой команды. 

   ```bash
   az spring-cloud create --resource-group "contosorg" --name "contososvc" 
   ```

   Выполнение этой команды занимает несколько минут.

1. Создайте приложение Spring Cloud в службе.

   ```bash
   az spring-cloud app create --resource-group "contosorg" --name "contosoascsapp" --assign-identity --is-public true
     --runtime-version Java_11 --service "contososvc"
   ```

   В этой таблице описаны представленные выше параметры.

   | Параметр | Описание |
   |---|---|
   | assign-identity | Запускает в службе создание удостоверения для управляемых удостоверений для ресурсов Azure. |
   | is-public | Назначает службе общедоступное доменное имя DNS. |
   | name | Имя приложения. |
   | resource-group | Имя группы ресурсов, в которой вы создали существующий экземпляр службы. |
   | runtime-version | Версия среды выполнения Java.  **Это значение должно совпадать с тем, которое вы ранее выбрали в Spring Initializr.** |
   | служба | Имя существующей службы. |

   Чтобы понять различия между *службой* и *приложением*, изучите статью [Общие сведения о приложении и развертывании в Azure Spring Cloud](/azure/spring-cloud/spring-cloud-concept-understand-app-and-deployment).

1. Получите управляемое удостоверение для ресурса Azure.  Примените его для того, чтобы настроить в существующем хранилище Key Vault доступ из вашего приложения.

   ```bash
   SERVICE_IDENTITY=$(az spring-cloud app show --resource-group "contosorg" --name "contosoascsapp" --service "contososvc" | jq -r '.identity.principalId')
   az keyvault set-policy --name "contosokv" --object-id <the value of the environment variable SERVICE_IDENTITY> --secret-permissions set get list
   ```

1. Поскольку существующее приложение Spring Boot уже имеет файл *application.properties* со всей необходимой конфигурацией, мы можем сразу развернуть приложение в Spring Cloud, используя следующую команду.  Выполните эту команду в том каталоге, где хранится POM.

   ```bash
   az spring-cloud app deploy --resource-group "contosorg" --name "contosoascsapp" --jar-path target/keyvault-0.0.1-SNAPSHOT.jar \
     --service "contososvc"
   ```

   Эта команда создает *развертывание* в приложении в службе.  Дополнительные сведения о таких концепциях, как экземпляры службы, приложения и развертывания, вы найдете в статье [Общие сведения о приложении и развертывании в Azure Spring Cloud](/azure/spring-cloud/spring-cloud-concept-understand-app-and-deployment).

   Если развертывание будет неудачным, настройте ведение журнала для устранения неполадок, как описано [здесь](https://aka.ms/azure-spring-cloud-configure-logs).  Скорее всего, полезная информация из этих журналов поможет вам диагностировать и устранить проблему.

1. Когда приложение будет успешно развернуто, с помощью `curl` проверьте работу интеграции с Key Vault.  Поскольку вы указали `--is-public`, для службы по умолчанию используется URL-адрес `https://<service name>-<app name>.azuremicroservices.io/`.  Заменив нужные значения и добавив значение метки `@GetMapping`, мы получим следующую команду `curl`.

   ```bash
   curl https://contososvc-contosoascsapp.azuremicroservices.io/get
   ```

   В выходных данных вы увидите `jdbc:sqlserver://SERVER.database.windows.net:1433;database=DATABASE`.

## <a name="summary"></a>Итоги

Вы создали веб-приложение Java с помощью **Spring Initializr**.  Вы создали хранилище Azure Key Vault для хранения конфиденциальной информации, а затем настроили в приложении получение информации из этого хранилища.  Проверив приложение в локальной среде, вы развернули его в Службе приложений Azure и в Azure Spring Cloud.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о Spring и Azure см. в центре документации об использовании Spring в Azure.

> [!div class="nextstepaction"]
> [Настройка Application Insights в инициализаторе Spring Boot](configure-spring-boot-java-applicationinsights.md)