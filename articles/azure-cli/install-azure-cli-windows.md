---
title: Установка Azure CLI для Windows
description: Как установить Azure CLI в Windows
author: dbradish-microsoft
ms.author: dbradish
manager: barbkess
ms.date: 05/01/2019
ms.topic: conceptual
ms.service: azure-cli
ms.devlang: azurecli
ms.openlocfilehash: c5e9118a04b0dc608309093866307fdc7083f591
ms.sourcegitcommit: 36e02e96b955ed0531f98b9c0f623f4acb508661
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "82030756"
---
# <a name="install-azure-cli-on-windows"></a>Установка Azure CLI в Windows

Azure CLI для Windows устанавливается с помощью MSI. Это предоставляет доступ к CLI из командной строки Windows (CMD) или PowerShell.
При установке подсистемы Windows для Linux (WSL) пакеты доступны для дистрибутива Linux. Список поддерживаемых диспетчеров пакетов и сведения об установке в WSL вручную см. на [главной странице установки](install-azure-cli.md).

[!INCLUDE [current-version](includes/current-version.md)]

## <a name="install-or-update"></a>Установка или обновление

Распространяемый MSI-файл используется для установки или обновления Azure CLI в Windows. Удалять текущие версии перед использованием установщика MSI не нужно.

> [!div class="nextstepaction"]
> [Скачать установщик MSI](https://aka.ms/installazurecliwindows)

Если программа установки запрашивает разрешения на внесение изменений на вашем компьютере, установите флажок "Да".

Azure CLI также можно установить с помощью PowerShell. Запустите PowerShell с правами администратора и выполните следующую команду:

   ```PowerShell
   Invoke-WebRequest -Uri https://aka.ms/installazurecliwindows -OutFile .\AzureCLI.msi; Start-Process msiexec.exe -Wait -ArgumentList '/I AzureCLI.msi /quiet'; rm .\AzureCLI.msi
   ```
Будет скачана и установлена последняя версия Azure CLI для Windows. Если у вас уже установлена определенная версия, она будет обновлена до последней. По завершении установки необходимо снова открыть PowerShell, чтобы использовать Azure CLI.

Теперь можно запустить Azure CLI с помощью команды `az` из командной строки Windows или PowerShell. В PowerShell реализовано несколько функций заполнения нажатием клавиши TAB, которые недоступны в командной строке Windows. Чтобы войти, выполните команду [az login](/cli/azure/reference-index#az-login).

[!INCLUDE [interactive-login](includes/interactive-login.md)]

Дополнительные сведения о различных методах проверки подлинности см. в статье [Вход с помощью Azure CLI](authenticate-azure-cli.md).

## <a name="troubleshooting"></a>Устранение неполадок

Ниже описаны некоторые распространенные проблемы с установкой в Windows. Если у вас возникла проблема, не описанная здесь, [сообщите об этом на сайте GitHub](https://github.com/Azure/azure-cli/issues).

### <a name="proxy-blocks-connection"></a>Прокси-сервер блокирует подключения

Если вы не можете скачать установщик MSI, так как прокси-сервер блокирует подключение, убедитесь, что этот прокси-сервер правильно настроен. В Windows 10 управление этими параметрами осуществляется в области `Settings > Network & Internet > Proxy`. Чтобы настроить обязательные параметры или решить вопросы, связанные с компьютером, который управляется с помощью конфигурации или для которого требуется расширенная настройка, обратитесь к системному администратору.

> [!IMPORTANT]
> Эти параметры также требуются для доступа к службам Azure с помощью CLI, PowerShell или командной строки. В PowerShell выполните для этого следующую команду:
>
> ```powershell
> (New-Object System.Net.WebClient).Proxy.Credentials = `
>   [System.Net.CredentialCache]::DefaultNetworkCredentials
> ```

Чтобы вы могли получить MSI, ваш прокси-сервер должен разрешать HTTPS-подключения по следующим адресам:

* `https://aka.ms/`
* `https://azurecliprod.blob.core.windows.net/`

## <a name="uninstall"></a>Удаление

[!INCLUDE [uninstall-boilerplate.md](includes/uninstall-boilerplate.md)]

Удалите Azure CLI из списка "Приложения и возможности" в Windows. Для удаления сделайте следующее:

| Платформа | Instructions |
|---|---|
| Windows 10 | Пуск > Параметры > Приложения |
| Windows 8<br/>Windows 7 | Пуск > Панель управления > Программы > Удалить программу |

На этом экране введите __Azure CLI__ в строке поиска программы. Программа для удаления называется __Microsoft CLI 2.0 для Azure__. Выберите это приложение, а затем нажмите кнопку `Uninstall`.

## <a name="next-steps"></a>Next Steps

Теперь вы можете пользоваться Azure CLI. Просмотрите общие сведения о его возможностях и список распространенных команд.

> [!div class="nextstepaction"]
> [Начало работы с Azure CLI](get-started-with-azure-cli.md)
