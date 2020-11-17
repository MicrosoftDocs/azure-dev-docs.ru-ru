---
title: Включить файл 1
description: Включить файл 1
ms.topic: include
ms.date: 06/01/2020
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: 8f0bda8929699f61fd3c79f9ae6fb38762e622c9
ms.sourcegitcommit: 5541f993c01ce356e1b0eaa8f95aea9051c3c21e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/03/2020
ms.locfileid: "93278690"
---
- Учетная запись Azure с активной подпиской. [Создать бесплатно](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-appservice-deno&mktingSource=vscode-tutorial-appservice-deno)
- [Visual Studio Code](https://code.visualstudio.com/)
- [Deno](https://deno.land/#installation)
- Выполненный вход в установленный инструмент Azure CLI.

## <a name="install-or-run-in-azure-cloud-shell"></a>Установка или запуск в Azure Cloud Shell

Самый простой способ приступить к работе с Azure CLI —запустить CLI в среде Azure Cloud Shell через браузер. Дополнительные сведения о Cloud Shell см. в [кратком руководстве по Bash в Azure Cloud Shell](/azure/cloud-shell/quickstart).

Сведения об установке CLI см. в разделе [Установка Azure CLI 2.0](/cli/azure/install-azure-cli).

Установив CLI впервые, проверьте правильность установки и версии, выполнив команду `az --version`.

> [!NOTE]
> Если вы используете классическую модель развертывания Azure, [установите классический интерфейс командной строки Azure](/cli/azure/install-classic-cli).

## <a name="sign-in"></a>Вход

Прежде чем использовать команды CLI при локальной установке, необходимо войти с помощью команды [az login](/cli/azure/reference-index#az-login).

[!INCLUDE [interactive-login](../../../azure-cli/includes/interactive-login.md)]

После входа в систему появится список подписок, связанных с учетной записью Azure. В сведениях о подписке с `isDefault: true` указана текущая активная подписка, отображаемая после входа. Чтобы выбрать другую подписку, укажите идентификатор подписки с помощью команды [az account set](/cli/azure/account#az-account-set). См. дополнительные сведения о [выборе нужной подписки при использовании нескольких подписок Azure](/cli/azure/manage-azure-subscriptions-azure-cli).