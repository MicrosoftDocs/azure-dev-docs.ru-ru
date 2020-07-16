---
title: Развертывание приложения MicroProfile в облаке с помощью Docker и Azure
description: Узнайте, как развернуть приложение MicroProfile в облаке с помощью Docker и службы "Экземпляры контейнеров Azure".
services: container-instances;container-retistry
documentationcenter: java
author: brunoborges
ms.author: brborges
ms.date: 11/21/2018
ms.service: container-instances
ms.tgt_pltfrm: multiple
ms.topic: article
ms.workload: web
ms.custom: devx-track-java
ms.openlocfilehash: 5823e2ee6a6467f0eb57b7e57108a191ff225896
ms.sourcegitcommit: 44016b81a15b1625c464e6a7b2bfb55938df20b6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/14/2020
ms.locfileid: "86379888"
---
# <a name="deploy-a-microprofile-application-to-the-cloud-with-docker-and-azure"></a>Развертывание приложения MicroProfile в облаке с помощью Docker и Azure

В этой статье показано, как упаковать приложение [MicroProfile.io] в контейнер Docker и запустить его в службе "Экземпляры контейнеров Azure".

> [!NOTE]
>
> Эта процедура подходит для любой реализации MicroProfile.io при условии, что образ контейнера Docker является самовыполняющимся (т. е. включает среду выполнения).

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим руководством требуется следующее:

* Подписка Azure. Если у вас ее еще нет, создайте [бесплатной учетной записи Azure].
* [Интерфейс командной строки Azure (CLI)].
* Поддерживаемая версия Java Development Kit (JDK). Дополнительные сведения о версиях JDK, доступных для разработки в Azure, см. в статье <https://aka.ms/azure-jdks>.
* Средство сборки [Maven] от Apache (версии 3 или более поздней версии).
* Клиент [Git].

## <a name="microprofile-hello-azure-sample"></a>Пример приложения MicroProfile Hello Azure

В этой статье мы используем пример приложения [MicroProfile Hello Azure](https://github.com/azure-samples/microprofile-hello-azure):

### <a name="clone-build-and-run-locally"></a>Клонирование, сборка и локальный запуск

```bash
$ git clone https://github.com/Azure-Samples/microprofile-hello-azure.git
$ mvn clean package
...
[INFO] BUILD SUCCESS
...
$ mvn payara-micro:start
...
[2018-07-30T13:34:51.553-0700] [] [INFO] [] [PayaraMicro] [tid: _ThreadID=1 _ThreadName=main] [timeMillis: 1532982891553] [levelValue: 800] Payara Micro  5.182 #badassmicrofish (build 303) ready in 10,304 (ms)
...
```

Приложение можно протестировать, используя команду `curl` или открыв в веб-браузере страницу `http://localhost:8080/api/hello`:

```bash
$ curl http://localhost:8080/api/hello
Hello, Azure!
```

## <a name="deploy-to-azure"></a>Развертывание в Azure

Теперь развернем это приложение в облаке с помощью служб [Экземпляры контейнеров Azure] и [Реестр контейнеров Azure].

### <a name="build-a-docker-image"></a>Создание образа Docker

Пример проекта уже содержит файл Dockerfile, который можно использовать. Вам не нужно устанавливать Docker, так как мы воспользуемся службой "Сборка Реестра контейнеров Azure" для сборки образа в облаке.

Для сборки образа и его подготовки к запуску в Azure выполните следующие действия:

1. Установите Azure CLI и выполните вход.
1. Создание группы ресурсов Azure
1. Создайте Реестр контейнеров Azure (ACR).
1. Создание образа Docker
1. Опубликуйте образ Docker в созданном ранее реестре ACR.
1. Дополнительно: выполните сборку образа и опубликуйте его в ACR с помощью одной команды.


#### <a name="set-up-azure-cli"></a>Настройка Azure CLI

Убедитесь, что у вас есть подписка на Azure, установлен [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) и вы выполнили проверку подлинности в своей учетной записи.

```bash
az login
```

#### <a name="create-a-resource-group"></a>Создание группы ресурсов

```bash
export ARG=microprofileRG
export ADCL=eastus
az group create --name $ARG --location $ADCL
```

#### <a name="create-an-azure-container-registry-instance"></a>создадите экземпляр реестра контейнеров Azure;

С помощью этой команды должен быть создан глобально уникальный реестр контейнеров с использованием базового имени и случайного числа.

```bash
export RANDINT=`date +"%m%d%y$RANDOM"`
export ACR=mydockerrepo$RANDINT
az acr create --name $ACR -g $ARG --sku Basic --admin-enabled
```

#### <a name="build-the-docker-image"></a>Создание образа Docker

Хотя можно легко выполнить сборку образа Docker локально с помощью самого средства Docker, рекомендуем выполнить ее в облаке по ряду причин:

1. Нет необходимости устанавливать Docker локально.
1. Скорость значительно увеличивается, потому что сборка выполняется в другом расположении (это не влияет на время, требующееся для отправки контекста).
1. Для процесса в облаке используется более скоростной интернет-канал, и загрузка происходит быстрее.
1. Образ попадает непосредственно в Реестр контейнеров.

Поэтому мы выполним сборку образа с помощью службы [Служба "Сборка Реестра контейнеров Azure"]:

```bash
export IMG_NAME="mympapp:latest"
az acr build -r $ACR -t $IMG_NAME -g $ARG .
...
Build complete
Build ID: aa1 was successful after 1m2.674577892s
```

#### <a name="deploy-docker-image-from-azure-container-registry-acr-into-container-instances-aci"></a>Развертывание образа Docker из Реестра контейнеров Azure (ACR) в службе "Экземпляры контейнеров Azure (ACI)"

Теперь, когда образ доступен в ACR, отправим его в службу ACI и создадим там экземпляр контейнера. Но сначала нужно убедиться, что мы можем выполнить проверку подлинности в ACR:

```bash
export ACR_REPO=`az acr show --name $ACR -g $ARG --query loginServer -o tsv`
export ACR_PASS=`az acr credential show --name $ACR -g $ARG --query "passwords[0].value" -o tsv`
export ACI_INSTANCE=myapp`date +"%m%d%y$RANDOM"`

az container create --resource-group $ARG --name $ACR --image $ACR_REPO/$IMG_NAME --cpu 1 --memory 1 --registry-login-server $ACR_REPO --registry-username $ACR --registry-password $ACR_PASS --dns-name-label $ACI_INSTANCE --ports 8080
```

#### <a name="test-your-deployed-microprofile-application"></a>Тестирование развернутого приложения MicroProfile

На этом этапе ваше приложение должно быть запущено. Чтобы протестировать его, попробуйте выполнить следующую команду:

```bash
curl http://$ACI_INSTANCE.$ADCL.azurecontainer.io:8080/api/hello
````

Поздравляем! Вы успешно собрали и развернули приложение MicroProfile в виде контейнера Docker в Microsoft Azure.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о различных технологиях, рассматриваемых в данной статье, см. в следующих статьях.

* [Вход в Azure из интерфейса командной строки Azure](/azure/xplat-cli-connect)

<!-- URL List -->

[Служба "Сборка Реестра контейнеров Azure"]: /azure/container-registry/container-registry-build-overview
[MicroProfile.io]: https://microprofile.io
[Azure Command Line Interface (CLI)]: /cli/azure/overview
[Azure for Java Developers]: /azure/developer/java/
[Azure portal]: https://portal.azure.com/
[бесплатной учетной записи Azure]: https://azure.microsoft.com/pricing/free-trial/
[Git]: https://github.com/;
[Maven]: http://maven.apache.org/
[Java Development Kit (JDK)]: https://aka.ms/azure-jdks
<!-- http://www.oracle.com/technetwork/java/javase/downloads/ -->
[Экземпляры контейнеров Azure]: /azure/container-instances/;
[Реестр контейнеров Azure]:  /azure/container-registry