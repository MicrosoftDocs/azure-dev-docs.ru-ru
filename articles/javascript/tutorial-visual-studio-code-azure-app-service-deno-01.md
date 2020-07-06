---
title: Развертывание приложений Deno в Службе приложений Azure с помощью Azure CLI
description: Руководство, часть 1. Общие сведения и предварительные требования.
ms.topic: conceptual
ms.date: 06/01/2020
ms.openlocfilehash: 817efd11f5eaf072d10a54efa083823e9b73e356
ms.sourcegitcommit: 553da4e9aa988e5bb823364244ea81961cee5bc7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/01/2020
ms.locfileid: "85792567"
---
# <a name="deploy-to-azure-app-service-using-visual-studio-code"></a>Развертывание в Службе приложений Azure с помощью Visual Studio Code

В этом руководстве объясняется, как развернуть приложение Deno в Службе приложений Azure (в Linux или Windows) с помощью Azure CLI.

## <a name="prerequisites"></a>Предварительные требования

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

[!INCLUDE [interactive-login](../azure-cli/includes/interactive-login.md)]

После входа в систему появится список подписок, связанных с учетной записью Azure. В сведениях о подписке с `isDefault: true` указана текущая активная подписка, отображаемая после входа. Чтобы выбрать другую подписку, укажите идентификатор подписки с помощью команды [az account set](/cli/azure/account#az-account-set). См. дополнительные сведения о [выборе нужной подписки при использовании нескольких подписок Azure](/cli/azure/manage-azure-subscriptions-azure-cli).

> [!div class="nextstepaction"]
> [Инструмент Azure CLI установлен, и вход в него выполнен](tutorial-visual-studio-code-azure-app-service-deno-02.md) [Возникла проблема](https://www.research.net/r/PWZWZ52?tutorial=deno-deployment-azureappservice&step=getting-started)

## <a name="next-steps"></a>Дальнейшие действия

[!INCLUDE [tutorial-next-steps](includes/tutorial-next-steps.md)]

> [!div class="nextstepaction"]
> [Готово](node-howto-deploy-web-app.md) [Возникла проблема](https://www.research.net/r/PWZWZ52?tutorial=deno-deployment-azureappservice&step=clean-up-resources)
