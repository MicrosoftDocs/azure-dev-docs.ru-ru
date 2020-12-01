---
title: Руководство по Развертывание приложений Python в Службе приложений Azure в Linux с помощью Visual Studio Code
description: Шаг 1 руководства по настройке среды для Службы приложений.
ms.topic: conceptual
ms.date: 11/20/2020
ms.custom: devx-track-python, seo-python-october2019
ms.openlocfilehash: 31695cb929188723cc608547849eb88a76b2d003
ms.sourcegitcommit: 29930f1593563c5e968b86117945c3452bdefac1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/23/2020
ms.locfileid: "96035520"
---
# <a name="tutorial-deploy-python-apps-to-azure-app-service-on-linux-from-visual-studio-code"></a>Руководство по Развертывание приложений Python в Службе приложений Azure в Linux с помощью Visual Studio Code

В этой статье описано, как развернуть приложение Python в Службе приложений Azure в Linux с использованием Visual Studio Code и расширения [Службы приложений Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice).

Если на любом из шагов этого руководства возникнут проблемы, сообщите нам об этом. Используйте ссылку **Возникли проблемы? Сообщите нам!** в конце каждой статьи, чтобы отправить отзыв.

Демонстрационное видео с виртуальной конференции PyCon 2020: <a href="https://www.youtube.com/watch?v=dNVvFttc-sA&feature=youtu.be&ocid=AID3006292" target="_blank">Создание веб-приложений с помощью VS Code и Службы приложений Azure</a> (YouTube.com).

> [!NOTE]
> Если вы предпочитаете развертывать приложения с помощью интерфейса командной строки, см. статью **[Краткое руководство. Создание приложения Python в Службе приложений Azure в Linux](/azure/app-service/quickstart-python)** .

> [!TIP]
> [Служба приложений Azure в Linux](/azure/app-service/overview#app-service-on-linux) предназначена для выполнения исходного кода в предварительно определенном контейнере Docker. Этот контейнер запускает приложения в среде Python 3.6 и выше, используя веб-сервер [Gunicorn](https://gunicorn.org). Характеристики этого контейнера описаны в статье [Настройка приложения Python в Linux для Службы приложений Azure](/azure/app-service/configure-language-python). Определения контейнеров находятся по адресу [github.com/Azure-App-Service/python](https://github.com/Azure-App-Service/python/tree/master/).

## <a name="configure-your-environment"></a>Настройка среды

- Если у вас нет учетной записи Azure с активной подпиской, [создайте учетную запись бесплатно](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-appservice-extension&mktingSource=vscode-tutorial-appservice-extension).

- Убедитесь, что у вас есть [локальная установка Python 3.7 или 3.8](https://python.org/downloads). Чтобы проверить версию, выполните следующую команду:

    ```bash
    python --version
    ```

- Установите следующее программное обеспечение:
  - [Visual Studio Code](https://code.visualstudio.com/).
  - Python и [расширение Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python), как описано в разделе [предварительные требования](https://code.visualstudio.com/docs/python/python-tutorial) руководства по работе с Python в VS Code.
  - [Расширение Службы приложений Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice), которое организует взаимодействие со Службой приложений Azure из VS Code. Для получения общих сведений ознакомьтесь с [руководством](https://code.visualstudio.com/tutorials/app-service-extension/getting-started) по расширению Службы приложений и посетите репозиторий [vscode-azureappservice](https://github.com/Microsoft/vscode-azureappservice) на сайте GitHub.

## <a name="sign-in-to-azure"></a>Вход в Azure

[!INCLUDE [azure-sign-in](includes/azure-sign-in.md)]

> [!div class="nextstepaction"]
> [Вход в Azure выполнен — перейти к шагу 2 >>>](tutorial-deploy-app-service-on-linux-02.md)

[Возникли проблемы? Сообщите нам!](https://aka.ms/FlaskVSCQuickstartHelp)
