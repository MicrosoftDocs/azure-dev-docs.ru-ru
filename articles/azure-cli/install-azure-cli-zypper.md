---
title: Установка Azure CLI в Linux с помощью zypper
description: Как установить Azure CLI с помощью zypper
author: dbradish-microsoft
ms.author: dbradish
manager: barbkess
ms.date: 09/09/2018
ms.topic: conceptual
ms.service: azure-cli
ms.devlang: azurecli
ms.openlocfilehash: d07fe2e807bd6e1fac6d0e9f883bcc8092be46bb
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/05/2020
ms.locfileid: "82031006"
---
# <a name="install-azure-cli-with-zypper"></a>Установка Azure CLI с помощью zypper

Для дистрибутивов Linux, использующих `zypper`, например openSUSE или SLES, доступен пакет Azure CLI. Этот пакет протестирован с openSUSE Leap 15.1 и SLES 15.

[!INCLUDE [current-version](includes/current-version.md)]

[!INCLUDE [rpm-warning](includes/rpm-warning.md)]

## <a name="install"></a>Установка

1. Установите `curl`:

   ```bash
   sudo zypper install -y curl
   ```

2. Импортируйте ключ репозитория Майкрософт:

   ```bash
   sudo rpm --import https://packages.microsoft.com/keys/microsoft.asc
   ```

3. Создайте сведения о локальном репозитории `azure-cli`:

   ```bash
   sudo zypper addrepo --name 'Azure CLI' --check https://packages.microsoft.com/yumrepos/azure-cli azure-cli
   ```

4. Обновите индекс пакета `zypper` и выполните установку:

   ```bash
   sudo zypper install --from azure-cli azure-cli
   ```
   Используйте вход 2 чтобы продолжить установку, игнорируя некоторые зависимости.

Запустите Azure CLI с помощью команды `az`. Чтобы войти, используйте команду [az login](/cli/azure/reference-index#az-login).

[!INCLUDE [interactive-login](includes/interactive-login.md)]

Дополнительные сведения о различных методах проверки подлинности см. в статье [Вход с помощью Azure CLI](authenticate-azure-cli.md).

## <a name="troubleshooting"></a>Устранение неполадок

Ниже описаны некоторые распространенные проблемы при установке с помощью `zypper`. Если у вас возникла проблема, не описанная здесь, [сообщите об этом на сайте GitHub](https://github.com/Azure/azure-cli/issues).

### <a name="install-on-sles-12-or-other-systems-without-python-36"></a>Установка в SLES 12 или других системах без Python 3.6

В SLES 12 пакет python3 по умолчанию теперь имеет версию 3.4 и не поддерживается в Azure CLI. Сначала можно создать более позднюю версию python3 из источника. Затем можно скачать пакет Azure CLI и установить его без зависимости.
```bash
$ sudo zypper install -y gcc gcc-c++ make ncurses patch wget tar zlib-devel zlib openssl-devel
# Download Python source code
$ PYTHON_VERSION="3.6.9"
$ PYTHON_SRC_DIR=$(mktemp -d)
$ wget -qO- https://www.python.org/ftp/python/$PYTHON_VERSION/Python-$PYTHON_VERSION.tgz | tar -xz -C "$PYTHON_SRC_DIR"
# Build Python
$ $PYTHON_SRC_DIR/*/configure
$ make
$ sudo make install
#Download azure-cli package 
$ AZ_VERSION=$(zypper --no-refresh info azure-cli |grep Version | awk -F': ' '{print $2}' | awk '{$1=$1;print}')
$ wget https://packages.microsoft.com/yumrepos/azure-cli/azure-cli-$AZ_VERSION.x86_64.rpm
#Install without dependency
$ sudo rpm -ivh --nodeps azure-cli-$AZ_VERSION.x86_64.rpm
```

### <a name="proxy-blocks-connection"></a>Прокси-сервер блокирует подключения

[!INCLUDE[configure-proxy](includes/configure-proxy.md)]

Вы также можете явным образом настроить `zypper` (через `yast2`), чтобы использовать этот прокси-сервер все время. Чтобы сделать это, выполните команду `yast2 proxy` как суперпользователь и заполните форму. Если в системе установлен диспетчер окон, можно также использовать панель `Network Services > Proxy` в `YaST Control Center`.

Дополнительные сведения о расширенной конфигурации см. в [документации по конфигурации прокси-сервера OpenSUSE](https://www.suse.com/documentation/slms1/book_slms/data/sec_wy_config_updates_proxy.html).

Чтобы вы могли получить ключ подписывания (Майкрософт) и получить пакет из нашего репозитория, ваш прокси-сервер должен разрешать HTTPS-подключения по следующим адресам:

* `https://packages.microsoft.com`
* `https://download.opensuse.org`

[!INCLUDE[troubleshoot-wsl.md](includes/troubleshoot-wsl.md)]

## <a name="update"></a>Update

Можно обновить пакет, воспользовавшись командой `zypper update`.

```bash
sudo zypper refresh
sudo zypper update azure-cli
```

## <a name="uninstall"></a>Удаление

[!INCLUDE [uninstall-boilerplate.md](includes/uninstall-boilerplate.md)]

1. Удалите пакет из системы.

    ```bash
    sudo zypper remove -y azure-cli
    ```

2. Если вы не планируете переустанавливать CLI, удалите сведения о репозитории.

   ```bash
   sudo zypper removerepo azure-cli
   ```

3. Если вы не используете другие пакеты Майкрософт, удалите ключ подписывания (Майкрософт).

   ```bash
   MSFT_KEY=`rpm -qa gpg-pubkey /* --qf "%{version}-%{release} %{summary}\n" | grep Microsoft | awk '{print $1}'`
   sudo rpm -e --allmatches gpg-pubkey-$MSFT_KEY
   ```

## <a name="next-steps"></a>Next Steps

Теперь вы можете пользоваться Azure CLI. Просмотрите общие сведения о его возможностях и список распространенных команд.

> [!div class="nextstepaction"]
> [Начало работы с Azure CLI](get-started-with-azure-cli.md)
