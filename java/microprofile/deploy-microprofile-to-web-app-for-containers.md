---
title: Развертывание службы MicroProfile на основе Java в службе Azure "Веб-приложение для контейнеров"
description: Узнайте, как развернуть службу MicroProfile с помощью Docker и службы Azure "Веб-приложение для контейнеров"
services: container-registry;app-service
documentationcenter: java
author: jonathangiles
manager: douge
editor: jonathangiles
ms.assetid: ''
ms.author: jogiles
ms.date: 09/07/2018
ms.devlang: java
ms.service: container-registry;app-service
ms.tgt_pltfrm: multiple
ms.topic: article
ms.workload: web
ms.openlocfilehash: 4ef47693094489baae85bc7622289d9943acb62b
ms.sourcegitcommit: 2efdb9d8a8f8a2c1914bd545a8c22ae6fe0f463b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/15/2019
ms.locfileid: "68284025"
---
# <a name="deploy-a-java-based-microprofile-service-to-azure-web-app-for-containers"></a>Развертывание службы MicroProfile на основе Java в службе Azure "Веб-приложение для контейнеров"

MicroProfile — отличный способ создавать очень компактные приложения Java, которые можно быстро и легко развернуть в различных службах, например в решении Azure [Веб-приложение для контейнеров](https://azure.microsoft.com/services/app-service/containers/). В этом руководстве мы создадим простую микрослужбу на основе MicroProfile, упакуем ее в контейнер Docker, опубликуем в [Реестре контейнеров Azure](https://azure.microsoft.com/services/container-registry/), а затем развернем с помощью службы Azure "Веб-приложение для контейнеров".

> [!NOTE]
>
> Эта процедура подходит для любой реализации MicroProfile.io при условии, что образ контейнера Docker является самовыполняющимся (т. е. включает среду выполнения).

Говоря конкретнее, в этом примере мы используем [Payara Micro](https://www.payara.fish/payara_micro) и [MicroProfile 1.3](https://microprofile.io/) для создания компактного файла Java WAR (5085 байт на компьютере авторов), а затем упакуем его в образ Docker (примерно 174 МБ). Этот образ Docker содержит все компоненты, необходимые для полностью контейнеризированного развертывания нашего веб-приложения.

В большинстве случаев принцип работы Docker позволяет не развертывать полный образ Docker размером 174 МБ при каждом изменении исходного кода приложения, так как Docker будет отправлять только различия (которые значительно меньше). Это значительно повышает скорость и эффективность развертывания новой версии приложения MicroProfile с помощью конвейера CI/CD, так как снижается сложность и ускоряется цикл разработки.

В этом руководстве мы сначала создадим и выполним программный код локально, а затем развернем его как веб-приложение в Azure. В обоих случаях мы будем использовать Docker для упрощения и стандартизации процедуры. Прежде чем начать, мы создадим Реестр контейнеров Azure для хранения контейнеров Docker.

## <a name="creating-an-azure-container-registry"></a>Создание Реестра контейнеров Azure

Для создания Реестра контейнеров Azure мы будем использовать [портал Azure](http://portal.azure.com), но есть и другие способы, например, можно применить Azure CLI. Чтобы создать Реестр контейнеров Azure, выполните следующие действия:

1. Войдите на [портал Azure](http://portal.azure.com) и создайте новый ресурс Реестра контейнеров Azure. Укажите имя реестра (обратите внимание, что оно должно быть задано как свойство `docker.registry` в файле `pom.xml`). При необходимости измените значения по умолчанию и нажмите кнопку "Создать".

1. Как только реестр контейнеров будет создан (это занимает около 30 секунд после нажатия кнопки "Создать"), выберите его и щелкните ссылку "Ключи доступа" в области меню слева. Здесь необходимо включить параметр "администратор", чтобы осуществлять доступ к этому реестру контейнеров с наших компьютеров (для отправки в него контейнеров Docker), а также разрешить доступ из экземпляра службы Azure "Веб-приложение для контейнеров", который мы скоро настроим.

1. В разделе "Ключи доступа" обратите внимание на значения `username` и `password`. Мы скопируем их и вставим в глобальный файл Maven `settings.xml` (дополнительные сведения о параметрах Maven см. на веб-сайтe [Apache Maven Project](https://maven.apache.org/settings.html)). Для справки ниже приведена версия файла `${user.home}/.m2/settings.xml` с маскированием в системе авторов:

    ```xml
    <settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0
                          https://maven.apache.org/xsd/settings-1.0.0.xsd">
        <servers>
          <server>
            <id>jogilescr.azurecr.io</id>
            <username>jogilescr</username>
            <password>ojoirshois.this-isn't-real.hrihslirhlishrglih</password>
          </server>
        </servers>
    </settings>
    ```

После завершения этой процедуры можно перейти к сборке и запуску приложения MicroProfile локально.

## <a name="creating-our-microprofile-application"></a>Создание приложения MicroProfile

Наш пример построен на основе демонстрационного приложения, доступного на сайте GitHub, поэтому мы клонируем это приложение и пошагово проанализируем его код. Чтобы клонировать код на свой компьютер, выполните следующие инструкции:

1. `git clone https://github.com/Azure-Samples/microprofile-docker-helloworld.git`
1. `cd microprofile-docker-helloworld`

В этой папке есть файл `pom.xml`, который используется для указания настроек проекта в формате инструмента сборки Maven. Этот файл можно изменить в соответствии с потребностями. В частности, значения свойств `docker.registry` и `docker.name` нужно изменить на `docker.registry` и `docker.name`, которые были созданы при настройке Реестра контейнеров Azure.

В этой папке также следует обратить внимание на файл Dockerfile, который приведен ниже:

```dockerfile
FROM payara/micro

ARG WAR_FILE
COPY target/${WAR_FILE} $DEPLOY_DIR

EXPOSE 8080
```

С помощью этого файла Dockerfile просто создается новый контейнер Docker на основе контейнера Payara Micro и в новый контейнер копируется файл WAR, созданный в процессе сборки. Кроме того, с помощью файла открывается порт 8080, чтобы мы могли осуществлять доступ к нашей службе после ее запуска в контейнере Docker.

Просматривая папку `src`, мы обнаружим в ней класс `Application`, показанный ниже:

```java
package com.microsoft.azure.samples.microprofile.docker.helloworld;

import javax.ws.rs.ApplicationPath;

@ApplicationPath("/api")
public class Application extends javax.ws.rs.core.Application { }
```

В аннотации `@ApplicationPath("/api")` указана базовая конечная точка этой микрослужбы, т. е. URL-адреса для доступа к каждой конечной точке REST будут иметь префикс `/api`.

Внутри пакета `api` находится класс с именем `API`, код которого приведен ниже:

```java
package com.microsoft.azure.samples.microprofile.docker.helloworld.api;

import javax.enterprise.context.ApplicationScoped;
import javax.ws.rs.GET;
import javax.ws.rs.Path;
import javax.ws.rs.Produces;

import static javax.ws.rs.core.MediaType.TEXT_HTML;

@ApplicationScoped
@Path("/")
public class API {

    @GET
    @Path("/helloworld")
    @Produces(TEXT_HTML)
    public String info() {
        return "Hello, world!";
    }
}
```

За счет использования аннотации `@Path("/helloworld")` путь этой конечной точки, образованный путем добавления префикса `/api`, указанного в классе `Application`, будет равен `/api/helloworld`. При вызове этой конечной точки с помощью запроса HTTP GET приведенный выше метод возвращает содержимое с типом "text/html". В нашем случае это просто жестко закодированная строка "Hello, world!".

Теперь мы описали весь программный код, необходимый для создания микрослужбы с помощью MicroProfile. Далее мы используем Maven, чтобы собрать нашу службу, упаковать ее в контейнер Docker и запустить локально. Это можно сделать, выполнив следующие действия:

1. Выполните команду `mvn clean package` и дождитесь ее успешного завершения.

1. Выполните команду `docker run -it --rm -p 8080:8080 <docker.registry>/<docker.name>:latest`, например `docker run -it --rm -p 8080:8080 jogilescr.azurecr.io/samples/docker-helloworld:latest`, если параметр `docker.registry` равен `jogilescr.azurecr.io` и параметр `docker.name` равен `samples/docker-helloworld`.

1. Попробуйте открыть адреса [http://localhost:8080/microprofile/api/helloworld](http://localhost:8080/microprofile/api/helloworld) и [http://localhost:8080/health](http://localhost:8080/health) в веб-браузере. Если вы видите ожидаемый ответ "Hello, world!" (и сведения о состоянии из конечной точки [/health](http://localhost:8080/health)), значит вы успешно развернули приложение MicroProfile на своем локальном компьютере.

## <a name="pushing-to-the-azure-container-registry"></a>Отправка в Реестр контейнеров Azure

После успешной сборки и запуска приложения MicroProfile на локальном компьютере следующим шагом станет отправка этого контейнера в созданный ранее реестр. Для выполнения задач нашего руководства мы используем Реестр контейнеров Azure, но вы можете использовать любой реестр контейнеров (при условии, что в файле `pom.xml` будет указано соответствующее расположение).

1. Выполните `mvn clean package`, чтобы очистить, скомпилировать и создать локальный образ Docker.
2. Выполните `mvn dockerfile:push`, чтобы отправить образ в Реестр контейнеров Azure.

На этом этапе мы отправили образ контейнера Docker в Реестр контейнеров Azure, но он еще не запущен, потому что образ необходимо развернуть в экземпляре службы Azure "Веб-приложение для контейнеров". Сейчас мы этим займемся.

## <a name="creating-an-azure-web-app-for-containers-instance"></a>Создание экземпляра службы Azure "Веб-приложение для контейнеров"

1. Вернитесь на [портал Azure](http://portal.azure.com) и создайте новый экземпляр службы Azure "Веб-приложение для контейнеров" (эта функция находится в меню под заголовком "Интернет и мобильные устройства"). Несколько советов:

   1. Указанное здесь имя станет общедоступным URL-адресом веб-приложения (хотя позже при необходимости можно добавить личный домен), потому стоит выбрать имя, которое легко запомнить.

   1. В разделе "Настроить контейнер" в качестве источника образа можно указать "Реестр контейнеров Azure", а затем выбрать нужный образ в раскрывающихся списках.

   1. Поле "Загрузочный файл" нужно оставить пустым.

1. После создания экземпляра (это также очень быстрый процесс), щелкните его и выберите пункт меню "Параметры приложения". Здесь нужно добавить новый параметр приложения с именем `WEBSITES_PORT` и значением `8080`. Это указывает Azure, какой порт нужно открыть в контейнере, и этот порт будет привязан к порту 80 извне.

1. Дополнительно можно щелкнуть ссылку "Контейнер Docker" и включить "Непрерывное развертывание", чтобы при каждом обновлении образа в Реестре контейнеров Azure он автоматически обновлялся в экземпляре службы Azure "Веб-приложение для контейнеров".

1. Экземпляры веб-приложений, развернутых в Azure, должны быть доступны по адресам `http://<appname>.azurewebsites.net/microprofile/api/helloworld` и `http://<appname>.azurewebsites.net/health`.

## <a name="summary"></a>Сводка

В этом руководстве мы продемонстрировали пошаговый процесс создания простой микрослужбы MicroProfile, которую мы упаковали в контейнер Docker, запустили локально и опубликовали в Azure. Расширение нашей микрослужбы для добавления более полезных функций выходит за рамки этого руководства, но в Интернете можно найти множество руководств и советов по MicroProfile. Мы рекомендуем читателям посетить веб-сайт [MicroProfile.io](https://microprofile.io/), на котором можно найти дополнительные материалы.
