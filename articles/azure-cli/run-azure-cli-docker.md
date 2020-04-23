---
title: Запуск Azure CLI в контейнере Docker
description: Как запустить контейнер Docker, в котором размещен Azure CLI
author: dbradish-microsoft
ms.author: dbradish
manager: barbkess
ms.date: 01/29/2018
ms.topic: conceptual
ms.service: azure-cli
ms.devlang: azurecli
ms.openlocfilehash: 96a2bcf311cb504b08d44da3ea6033dbad9034b8
ms.sourcegitcommit: 36e02e96b955ed0531f98b9c0f623f4acb508661
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "82031296"
---
# <a name="run-azure-cli-in-a-docker-container"></a>Запуск Azure CLI в контейнере Docker

Docker можно использовать для запуска изолированного контейнера Linux с предварительно установленным Azure CLI. Docker позволяет быстро начать работу, используя изолированную среду для запуска CLI. Этот образ также можно использовать в качестве основы для ваших собственных развертываний.

## <a name="run-in-a-docker-container"></a>Запуск в контейнере Docker

> [!NOTE]
> Интерфейс командной строки Azure перенесен в [Реестр контейнеров](https://azure.microsoft.com/services/container-registry). Существующие теги в Docker Hub по-прежнему поддерживаются, но новые выпуски будут доступны только в виде mcr.microsoft.com/azure-cli.

Установите CLI с помощью команды `docker run`.

   ```bash
   docker run -it mcr.microsoft.com/azure-cli
   ```

> [!NOTE]
> Если вы хотите извлечь ключи SSH из пользовательской среды, можно использовать `-v ${HOME}/.ssh:/root/.ssh`, чтобы подключить ключи SSH в этой среде.
>
> ```bash
> docker run -it -v ${HOME}/.ssh:/root/.ssh mcr.microsoft.com/azure-cli
> ```

CLI устанавливается в образ как команда `az` в папку `/usr/local/bin`. Чтобы войти, выполните команду [az login](/cli/azure/reference-index#az-login).

[!INCLUDE [interactive-login](includes/interactive-login.md)]

Дополнительные сведения о различных методах проверки подлинности см. в статье [Вход с помощью Azure CLI](authenticate-azure-cli.md).

## <a name="update-docker-image"></a>Обновление образа Docker

Для обновления с помощью Docker требуется извлечь новый образ и повторно создать существующие контейнеры. По этой причине не следует использовать контейнер, в котором размещается CLI, в качестве хранилища данных.

Обновите локальный образ с помощью команды `docker pull`.

```bash
docker pull mcr.microsoft.com/azure-cli
```

## <a name="uninstall-docker-image"></a>Удаление образа Docker

[!INCLUDE [uninstall-boilerplate.md](includes/uninstall-boilerplate.md)]

После остановки любого контейнера под управлением образа CLI удалите образ.

```bash
docker rmi mcr.microsoft.com/azure-cli
```

## <a name="next-steps"></a>Next Steps

Теперь вы можете пользоваться Azure CLI. Просмотрите общие сведения о его возможностях и список распространенных команд.

> [!div class="nextstepaction"]
> [Начало работы с Azure CLI](get-started-with-azure-cli.md)
