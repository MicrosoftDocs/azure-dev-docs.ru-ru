---
author: mriem
ms.author: manriem
ms.date: 2/28/2020
ms.openlocfilehash: 8af791a3b85f5e64865cee965a37797327db0b0d
ms.sourcegitcommit: 56e5f51daf6f671f7b6e84d4c6512473b35d31d2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/07/2020
ms.locfileid: "78897612"
---
### <a name="build-and-push-the-docker-image-to-azure-container-registry"></a>Сборка и передача образа Docker в Реестр контейнеров Azure

После создания Dockerfile необходимо создать образ Docker и опубликовать его в Реестре контейнеров Azure.

Если вы использовали наш [репозиторий GitHub для быстрого начала работы с контейнерами WildFly](https://github.com/Azure/wildfly-container-quickstart), процесс создания образа и его передачи в реестр контейнеров Azure будет аналогичен вызову следующих трех команд.

В этих примерах переменная среды `MY_ACR` содержит имя Реестра контейнеров Azure, а переменная `MY_APP_NAME` содержит имя веб-приложения, используемого в Реестре контейнеров Azure.

Создание WAR-файла:

```shell
mvn package
```

Войдите в Реестр контейнеров Azure:

```shell
az acr login -n ${MY_ACR}
```

Сборка и передача образа:

```shell
az acr build -t ${MY_ACR}.azurecr.io/${MY_APP_NAME} -f src/main/docker/Dockerfile .
```

Кроме того, с помощью Docker CLI можно сначала создать и протестировать образ локально, как показано в следующих командах. Такой подход упрощает тестирование и дополнительную настройку образа перед первым развертыванием в ACR. Однако для этого необходимо установить Docker CLI и убедиться, что управляющая программа Docker запущена.

Сборка образа:

```shell
docker build -t ${MY_ACR}.azurecr.io/${MY_APP_NAME}
```

Локальный запуск образа:

```shell
docker run -it -p 8080:8080 ${MY_ACR}.azurecr.io/${MY_APP_NAME}
```

Теперь вы можете получить доступ к приложению по адресу [http://localhost:8080](http://localhost:8080).

Войдите в Реестр контейнеров Azure:

```shell
az acr login -n ${MY_ACR}
```

Передача образа в Реестр контейнеров Azure:

```shell
docker push ${MY_ACR}.azurecr.io/${MY_APP_NAME}
```

Более подробные сведения о создании и хранении образов контейнеров в Azure см. в модуле [Создание и хранение образов контейнеров с помощью службы "Реестр контейнеров Azure"](https://docs.microsoft.com/learn/modules/build-and-store-container-images/).
