---
title: Руководство по Повторное развертывание контейнера в Службе приложений Azure с помощью Visual Studio Code после внесения изменений
description: 'Шаг руководства 3: простой процесс повторного создания и развертывания образа контейнера.'
services: app-service
author: kraigb
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: kraigb
ms.custom: seo-python-october2019
ms.openlocfilehash: 0c57729fd3ab1f671b2533f3e15056aec99cb6c9
ms.sourcegitcommit: bed07b313eeab51281d1a6d4eba67a75524b2f57
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2019
ms.locfileid: "72172451"
---
# <a name="tutorial-redeploy-a-container-to-azure-app-service-after-making-changes"></a>Руководство по Повторное развертывание контейнера в Службе приложений Azure после внесения изменений

[Предыдущий шаг. Развертывание образа в Службе приложений Azure](tutorial-deploy-containers-02.md)

Рано или поздно вы обязательно внесете в приложение какие-либо изменения, а значит потребуется заново создать и развернуть контейнер. К счастью, этот процесс очень прост.

1. Внесите нужные изменения в приложение и протестируйте их локально. (Этот шаг и два следующих подробно описаны в учебнике [по созданию контейнера Python в VS Code](https://code.visualstudio.com/docs/python/tutorial-create-container).)

1. Повторно соберите образ Docker. Если вы измените только код приложения, сборка займет всего несколько секунд.

1. Отправьте образ в реестр. Если вы изменили только код приложения, этот процесс также завершится через несколько секунд, поскольку передается только небольшой слой с изменениями.

1. В области **Azure: Служба приложений**, щелкните Службу приложений правой кнопкой мыши и выберите действие **Restart** (Перезапустить). При перезапуске Службы приложений автоматически извлекается из реестра последний образ контейнера.

1. Примерно через 15-20 секунд снова откройте URL-адрес Службы приложений, чтобы проверить обновления.

> [!div class="nextstepaction"]
> [Я внес изменения и повторно развернул приложение](tutorial-deploy-containers-04.md)

[У меня есть проблема](https://www.research.net/r/PWZWZ52?tutorial=vscode-appservice-containers&step=03-make-changes-redeploy)
