---
title: Установка Azure CLI для Linux вручную
description: Как установить Azure CLI в Linux вручную
author: dbradish-microsoft
ms.author: dbradish
manager: barbkess
ms.date: 09/09/2018
ms.topic: conceptual
ms.service: azure-cli
ms.devlang: azurecli
ms.openlocfilehash: 9a98da54f397c1fd03a7cc6b581a769afe84ef88
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/05/2020
ms.locfileid: "82031076"
---
# <a name="install-azure-cli-on-linux-manually"></a>Установка Azure CLI в Linux вручную

Если для вашего дистрибутива отсутствует пакет Azure CLI, установите CLI вручную, выполнив скрипт.

[!INCLUDE [current-version](includes/current-version.md)]

> [!NOTE]
> Настоятельно рекомендуется устанавливать CLI с помощью диспетчера пакетов. Диспетчер пакетов гарантирует, что вы получите последние обновления, и обеспечит стабильность компонентов CLI. Перед установкой вручную проверьте, есть ли пакет для вашего дистрибутива.

## <a name="prerequisites"></a>предварительные требования

Для использования CLI требуется следующее ПО:

* [Python 3.6.x, 3.7.x или 3.8.x](https://www.python.org/downloads/). 
* [libffi](https://sourceware.org/libffi/)
* [OpenSSL 1.0.2](https://www.openssl.org/source/)

> [!IMPORTANT]
>
> Начиная с версии `2.1.0`, в CLI прекращена поддержка Python 2.7. Новые версии больше не будут гарантированно работать при использовании Python 2.7.

## <a name="install-or-update"></a>Установка или обновление

Для установки и обновления CLI требуется повторный запуск установочного скрипта. Установите CLI, выполнив `curl`.

```bash
curl -L https://aka.ms/InstallAzureCli | bash
```

Скрипт также можно скачать и выполнить на локальном компьютере. Чтобы изменения вступили в силу, может потребоваться перезапустить оболочку.

Запустите Azure CLI с помощью команды `az`. Чтобы войти, используйте команду [az login](/cli/azure/reference-index#az-login).

[!INCLUDE [interactive-login](includes/interactive-login.md)]

Дополнительные сведения о различных методах проверки подлинности см. в статье [Вход с помощью Azure CLI](authenticate-azure-cli.md).

## <a name="troubleshooting"></a>Устранение неполадок

Ниже указаны некоторые распространенные проблемы, возникающие при установке вручную. Если у вас возникла проблема, не описанная здесь, [сообщите об этом на сайте GitHub](https://github.com/Azure/azure-cli/issues).

### <a name="curl-object-moved-error"></a>Ошибка "Объект перемещен" при выполнении команды curl

Если при выполнении команды `curl` появляется сообщение об ошибке относительно параметра `-L` или сообщение об ошибке "Объект перемещен", попробуйте использовать полный URL-адрес вместо URL-адреса перенаправления `aka.ms`:

```bash
curl https://azurecliprod.blob.core.windows.net/install | bash
```

### <a name="az-command-not-found"></a>Команда `az` не найдена

Если не удается выполнить эту команду после установки и используется `bash` или `zsh`, очистите кэш хэша команд в оболочке. Run (Запустить)

```bash
hash -r
```

и проверьте, будет ли устранена проблема.

Кроме того, проблема может возникнуть, если вы не перезапустили оболочку после установки. Убедитесь, что команда `az` добавлена в переменную `$PATH`. Расположение команды `az`:

```bash
<install path>/bin
```

### <a name="proxy-blocks-connection"></a>Прокси-сервер блокирует подключения

[!INCLUDE[configure-proxy](includes/configure-proxy.md)]

Чтобы вы могли получить скрипты установки, ваш прокси-сервер должен разрешать HTTPS-подключения по следующим адресам:

* `https://aka.ms/`
* `https://azurecliprod.blob.core.windows.net/`
* `https://pypi.python.org`
* Конечные точки, используемые диспетчером пакетов дистрибутива (при его наличии) для основных пакетов

[!INCLUDE[troubleshoot-wsl.md](includes/troubleshoot-wsl.md)]

## <a name="uninstall"></a>Удаление

[!INCLUDE [uninstall-boilerplate.md](includes/uninstall-boilerplate.md)]

Удалите CLI, удалив файлы непосредственно из расположения, выбранного при установке. Расположение установки по умолчанию — `$HOME`.

1. Удалите установленные файлы CLI.

   ```bash
   rm -r <install location>/lib/azure-cli
   rm <install location>/bin/az
   ```

2. Измените файл `$HOME/.bash_profile`, чтобы удалить следующую строку:

   ```text
   <install location>/lib/azure-cli/az.completion
   ```

3. При использовании `bash` или `zsh` перезагрузите кэш команд в оболочке.

   ```bash
   hash -r
   ```

## <a name="next-steps"></a>Next Steps

Теперь вы можете пользоваться Azure CLI. Просмотрите общие сведения о его возможностях и список распространенных команд.

> [!div class="nextstepaction"]
> [Начало работы с Azure CLI](get-started-with-azure-cli.md)
