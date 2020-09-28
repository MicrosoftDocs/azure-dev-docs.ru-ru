---
title: Руководство по Развертывание приложений Python в Службе приложений Azure в Linux с помощью Visual Studio Code
description: Шаг 1 руководства по настройке среды для Службы приложений.
ms.topic: conceptual
ms.date: 09/12/2019
ms.custom: devx-track-python, seo-python-october2019
ms.openlocfilehash: b35fc41707b31bec8e889d2b60becdad56f4e7d9
ms.sourcegitcommit: 69933dcce571b2686897b295b7822e207d944617
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/18/2020
ms.locfileid: "90773077"
---
# <a name="tutorial-deploy-python-apps-to-azure-app-service-on-linux-from-visual-studio-code"></a>Руководство по Развертывание приложений Python в Службе приложений Azure в Linux с помощью Visual Studio Code

В этой статье описано, как развернуть приложение Python в Службе приложений Azure в Linux с использованием Visual Studio Code и расширения [Службы приложений Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice).

Если на любом из шагов этого руководства возникнут проблемы, сообщите нам об этом. Используйте ссылку **Возникли проблемы? Сообщите нам!** в конце каждой статьи, чтобы отправить отзыв.

Демонстрационное видео с виртуальной конференции PyCon 2020: <a href="https://www.youtube.com/watch?v=dNVvFttc-sA&feature=youtu.be&ocid=AID3006292" target="_blank">Создание веб-приложений с помощью VS Code и Службы приложений Azure</a> (YouTube.com).

> [!NOTE]
> Если вы предпочитаете развертывать приложения с помощью интерфейса командной строки, см. статью **[Краткое руководство. Создание приложения Python в Службе приложений Azure в Linux](/azure/app-service/quickstart-python)** .

> [!TIP]
> [Служба приложений Azure в Linux](/azure/app-service/overview#app-service-on-linux) предназначена для выполнения исходного кода в предварительно определенном контейнере Docker. Этот контейнер запускает приложения в среде Python 3.7, используя веб-сервер [Gunicorn](https://gunicorn.org). Характеристики этого контейнера описаны в статье [Настройка приложения Python в Linux для Службы приложений Azure](/azure/app-service/configure-language-python). Само определение контейнера находится по адресу [github.com/Azure-App-Service/python](https://github.com/Azure-App-Service/python/tree/master/3.7).

## <a name="configure-your-environment"></a>Настройка среды

- [Подписка Azure](#azure-subscription).
- [Visual Studio Code с расширением Azure Dev Spaces](#visual-studio-code-python-and-the-azure-app-service-extension).
- Среда Python

### <a name="azure-subscription"></a>Подписка Azure.

Если у вас нет подписки Azure, [зарегистрируйтесь прямо сейчас](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-appservice-extension&mktingSource=vscode-tutorial-appservice-extension) и получите бесплатную учетную запись с кредитами Azure на сумму 200 долл. США, которые позволят проверить любое сочетание служб.

### <a name="visual-studio-code-python-and-the-azure-app-service-extension"></a>Visual Studio Code, Python и расширение Azure App Service

Установите следующее программное обеспечение:

- [Visual Studio Code](https://code.visualstudio.com/).
- Python и [расширение Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python), как описано в разделе [предварительные требования](https://code.visualstudio.com/docs/python/python-tutorial) руководства по работе с Python в VS Code.
- [Расширение Службы приложений Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice), которое организует взаимодействие со Службой приложений Azure из VS Code. Для получения общих сведений ознакомьтесь с [руководством](https://code.visualstudio.com/tutorials/app-service-extension/getting-started) по расширению Службы приложений и посетите репозиторий [vscode-azureappservice](https://github.com/Microsoft/vscode-azureappservice) на сайте GitHub.

## <a name="sign-in-to-azure"></a>Вход в Azure

[!INCLUDE [azure-sign-in](includes/azure-sign-in.md)]

> [!div class="nextstepaction"]
> [Вход в Azure выполнен — перейти к шагу 2 >>>](tutorial-deploy-app-service-on-linux-02.md)

[Возникли проблемы? Сообщите нам!](https://aka.ms/FlaskVSCQuickstartHelp)
