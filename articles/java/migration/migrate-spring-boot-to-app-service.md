---
title: Перенос приложений Spring Boot в Службу приложений Azure
description: Узнайте, что следует учитывать при переносе существующего приложения Spring Boot в Службу приложений Azure.
author: yevster
ms.author: yebronsh
ms.topic: conceptual
ms.date: 01/22/2019
ms.openlocfilehash: 46fa281fdbaf53e35a73701fa2c17ae2ed3e2d90
ms.sourcegitcommit: 81577378a4c570ced1e9c6765f4a9eee8453c889
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/08/2020
ms.locfileid: "84507644"
---
# <a name="migrate-spring-boot-applications-to-azure-app-service"></a>Перенос приложений Spring Boot в Службу приложений Azure

Узнайте, что следует учитывать при переносе существующего приложения Spring Boot в Службу приложений Azure.

## <a name="pre-migration"></a>Подготовка к миграции

Чтобы обеспечить успешную миграцию, перед ее началом выполните шаги оценки и инвентаризации, описанные в следующих разделах.

Если вы не можете выполнить какие-либо требования для подготовки к миграции, ознакомьтесь со следующими дополнительными руководствами по переносу:

* Перенос приложений на базе исполняемых JAR-файлов в контейнеры в Службе Azure Kubernetes (руководство ожидается)
* Перенос приложений на базе исполняемых JAR-файлов в Виртуальные машины Azure (руководство ожидается)

### <a name="switch-to-a-supported-platform"></a>Переход на поддерживаемую платформу

В Службе приложений доступны несколько версий Java SE. Чтобы обеспечить совместимость, перенесите приложение в одну из поддерживаемых версий текущей среды, прежде чем продолжать. Обязательно полностью протестируйте готовую конфигурацию. Используйте в этих тестах последний стабильный выпуск дистрибутива Linux.

[!INCLUDE [note-obtain-your-current-java-version-app-service](includes/note-obtain-your-current-java-version-app-service.md)]

### <a name="inventory-external-resources"></a>Проверка внешних ресурсов

Определите внешние ресурсы, в том числе источники данных, брокеры сообщений JMS и URL-адреса других служб. В приложениях Spring Boot файл с конфигурацией этих ресурсов обычно размещается в каталоге *src/main/directory* и называется *application.properties* или *application.yml*. Кроме того, проверьте наличие параметров конфигурации в переменных среды в рабочем развертывании.

[!INCLUDE [inventory-databases-spring-boot](includes/inventory-databases-spring-boot.md)]

[!INCLUDE [identify-jms-brokers-in-spring](includes/identify-jms-brokers-in-spring.md)]

Когда вы найдете один или несколько используемых брокеров, получите их параметры. В приложениях Spring Boot они обычно размещаются в файле *application.properties* или *application.yml* в каталоге приложения.

[!INCLUDE [jms-broker-settings-examples-in-spring](includes/jms-broker-settings-examples-in-spring.md)]

[!INCLUDE [identify-external-caches-azure-spring-cloud](includes/identify-external-caches-azure-spring-cloud.md)]

[!INCLUDE [inventory-identity-providers-spring-boot](includes/inventory-identity-providers-spring-boot.md)]

#### <a name="all-other-external-resources"></a>Другие связанные внешние ресурсы

Нет смысла описывать все возможные внешние зависимости, перечисленные в этом руководстве. Ваша команда должна убедиться в том, что после миграции в Службу приложений будут все внешние зависимости приложения будут доступными.

### <a name="inventory-secrets"></a>Проверка секретов

#### <a name="passwords-and-secure-strings"></a>Пароли и защищенные строки

Проверьте наличие секретных строк и паролей во всех свойствах и файлах конфигурации, а также всех переменных среды в рабочих развертываниях. В приложении Spring Boot такие строки обычно содержатся в файлах *application.properties* или *application.yml*.

[!INCLUDE [inventory-certificates-h4](includes/inventory-certificates-h4.md)]

[!INCLUDE [determine-whether-and-how-the-file-system-is-used](includes/determine-whether-and-how-the-file-system-is-used.md)]

### <a name="special-cases"></a>Особые случаи

В некоторых рабочих сценариях может потребоваться внести дополнительные изменения. Также могут применяться дополнительные ограничения. Хотя такие сценарии могут использоваться нечасто, важно убедиться, что приложение в них не задействовано или что они правильно реализуются.

#### <a name="determine-whether-application-relies-on-scheduled-jobs"></a>Определение того, использует ли приложение запланированные задания

Запланированные задания, такие как задачи планировщика Quartz или задания Cron, нельзя использовать со Службой приложений. Служба приложений не будет препятствовать развертыванию приложения, содержащего запланированные внутренние задачи. Но если приложение масштабируется, одно запланированное задание может выполняться несколько раз в течение указанного периода. Это может привести к нежелательным последствиям.

Проверьте все запланированные задания, связанные с приложением (выполняющиеся и не выполняющиеся).

#### <a name="determine-whether-your-application-contains-os-specific-code"></a>Определение того, содержит ли приложение код, зависящий от ОС

[!INCLUDE [determine-whether-your-application-contains-os-specific-code](includes/determine-whether-your-application-contains-os-specific-code-no-title.md)]

#### <a name="identify-all-outside-processesdaemons-running-on-the-production-servers"></a>Определение всех внешних процессов и управляющих программ, запущенных на рабочих серверах

Все процессы (например, управляющие программы мониторинга), выполняющиеся за пределами сервера приложений, нужно перенести в другое расположение или завершить.

#### <a name="identify-handling-of-non-http-requests-or-multiple-ports"></a>Определение обработки запросов, отличающихся от запросов HTTP или выполняющихся по нескольким портам

Служба приложений позволяет использовать только одну конечную точку HTTP и только один порт. Если приложение прослушивает несколько портов или принимает запросы, отличающиеся от запросов HTTP, не используйте Службу приложений Azure.

## <a name="migration"></a>Миграция

### <a name="parameterize-the-configuration"></a>Параметризация конфигурации

Убедитесь, что координаты всех внешних ресурсов (например, строк подключения к базам данных) и другие настраиваемые параметры считываются из переменных среды. В приложениях Spring Boot все параметры конфигурации должны поддерживать вынос во внешний код. Дополнительные сведения см. в разделе о [вынесении конфигурации во внешних код](https://docs.spring.io/spring-boot/docs/current/reference/html/spring-boot-features.html#boot-features-external-config) в документации по Spring Boot.

Вот пример со ссылкой на переменную среды `SERVICEBUS_CONNECTION_STRING` из файла *application.properties*:

```properties
spring.jms.servicebus.connection-string=${SERVICEBUS_CONNECTION_STRING}
spring.jms.servicebus.topic-client-id=contoso1
spring.jms.servicebus.idle-timeout=10000
```

### <a name="provision-an-app-service-plan"></a>Подготовка плана службы приложений

В [списке доступных планов обслуживания](https://azure.microsoft.com/pricing/details/app-service/linux/) выберите план, характеристики которого соответствуют требованиям к оборудованию для рабочей среды или превосходят их.

> [!NOTE]
> Если вы намерены использовать промежуточное или раннее развертывание либо [слоты развертывания](/azure/app-service/deploy-staging-slots), план службы приложений должен учитывать эту дополнительную емкость. Для приложений Java рекомендуется использовать план уровня "Премиум" или выше.

[Создайте план службы приложений](/azure/app-service/app-service-plan-manage#create-an-app-service-plan).

### <a name="create-and-deploy-web-apps"></a>Создание и развертывание веб-приложений

Вам нужно создать в плане службы приложений новое веб-приложение (со стеком времени выполнения Java SE) для каждого исполняемого JAR-файла, который вы хотите выполнить.

#### <a name="maven-applications"></a>Приложения Maven

Если приложение создано на основе POM-файла Maven, [используйте подключаемый модуль веб-приложения для Maven](/azure/developer/java/spring-framework/deploy-spring-boot-java-app-with-maven-plugin#configure-maven-plugin-for-azure-app-service), чтобы создать и развернуть веб-приложение.

#### <a name="non-maven-applications"></a>Приложения, отличающиеся от Maven

Если вы не можете использовать подключаемый модуль Maven, вам нужно будет подготовить веб-приложение с помощью других механизмов, например:

* [Портал Azure](https://portal.azure.com/#create/Microsoft.WebSite)
* [Azure CLI](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create)
* [Azure PowerShell](/powershell/module/az.websites/new-azwebapp)

Создав веб-приложение, разверните его, используя один из [доступных механизмов развертывания](/azure/app-service/deploy-ftp). Если это возможно, приложение нужно передать в файл */home/site/wwwroot/app.jar*. Если вы не хотите переименовывать JAR-файл в *app.jar*, отправьте скрипт оболочки с командой для выполнения JAR-файла. Затем вставьте полный путь к этому скрипту в текстовое поле [Файл запуска](/azure/app-service/containers/app-service-linux-faq#built-in-images) в разделе конфигурации на портале. Скрипт запуска выполняется не в том каталоге, в который он помещен. Поэтому всегда используйте абсолютные пути для создания ссылок на файлы в скрипте запуска (например, `java -jar /home/myapp/myapp.jar`).

### <a name="migrate-jvm-runtime-options"></a>Перенос параметров среды выполнения JVM

Если для работы приложения требуются определенные параметры среды выполнения, [используйте наиболее подходящий механизм, чтобы указать их](/azure/app-service/containers/configure-language-java#set-java-runtime-options).

[!INCLUDE [configure-custom-domain-and-ssl](includes/configure-custom-domain-and-ssl.md)]

[!INCLUDE [import-backend-certificates](includes/import-backend-certificates.md)]

### <a name="migrate-external-resource-coordinates-and-other-settings"></a>Миграция координат внешних ресурсов и других параметров

Выполните [эти действия для переноса строк подключения и других параметров](/azure/app-service/containers/configure-language-java#spring-boot-1).

> [!NOTE]
> Для всех параметров приложения Spring Boot, параметризованных с использованием переменных среды в разделе [параметризации конфигурации](#parameterize-the-configuration), эти переменные среды нужно определить в конфигурации приложения. Даже если параметры приложения Spring Boot явно не параметризованы, как описано выше, их можно соответствующим образом переопределить в конфигурации приложения. Пример:

  ```properties
  spring.jms.servicebus.connection-string=${CUSTOMCONNSTR_SERVICE_BUS}
  spring.jms.servicebus.topic-client-id=contoso1
  spring.jms.servicebus.idle-timeout=1800000
  ```

![Конфигурация приложений Службы приложений](media/migrate-spring-boot-to-app-service/app-service-parameterized-spring-boot-app-settings.png)

[!INCLUDE [migrate-scheduled-jobs](includes/migrate-scheduled-jobs.md)]

[!INCLUDE [migrate-identity-provider-app-service.md](includes/migrate-identity-provider-app-service.md)]

### <a name="restart-and-smoke-test"></a>Перезапуск и тест состояния

Наконец, перезапустите веб-приложение, чтобы применить все изменения конфигурации. После завершения перезагрузки убедитесь, что приложение работает правильно.

## <a name="post-migration"></a>Действия после миграции

После переноса приложения в Службу приложений Azure нужно убедиться, что оно работает правильно. Затем вы можете применить некоторые рекомендации, которые помогут сделать ваше приложение более удобным для использования в облаке.

### <a name="recommendations"></a>Рекомендации

* Если для хранения файлов вы решили использовать каталог */home*, попробуйте [заменить его](/azure/app-service/containers/how-to-serve-content-from-azure-storage) службой хранилища Azure.

* При наличии в каталоге */home* конфигурации со строками подключения, ключами SSL и другими секретными сведениями, попробуйте использовать [Azure Key Vault](/azure/app-service/app-service-key-vault-references) в сочетании с [внедрением параметров с помощью параметров приложения](/azure/app-service/configure-common#configure-app-settings) или без него.

* Для предоставления надежных развертываний с нулевым временем простоя вы можете [использовать слоты развертывания](/azure/app-service/deploy-staging-slots).

* Разработайте и реализуйте стратегию DevOps. Чтобы обеспечить надежность и ускорить разработку, вы можете [автоматизировать развертывание и тестирование с помощью Azure Pipelines](/azure/devops/pipelines/ecosystems/java-webapp). Если используются слоты развертывания, можно [автоматизировать развертывание в слоте](/azure/devops/pipelines/targets/webapp?view=azure-devops&tabs=yaml#deploy-to-a-slot) с последующим переключением слотов.

* Разработайте и реализуйте стратегии обеспечения непрерывности бизнес-процессов и аварийного восстановления. Для критически важных приложений вы можете использовать [архитектуру развертывания с несколькими регионами](/azure/architecture/reference-architectures/app-service-web-app/multi-region).
