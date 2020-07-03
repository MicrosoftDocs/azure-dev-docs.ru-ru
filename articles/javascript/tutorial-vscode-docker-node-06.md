---
title: Повторное развертывание контейнера в Службе приложений Azure с помощью Visual Studio Code после внесения изменений
description: Руководство, шаг 6. Простой процесс повторного создания и развертывания образа контейнера.
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: 7920bc9ddb2b9b7cc06f936fb97400a5c1d9dd7d
ms.sourcegitcommit: 553da4e9aa988e5bb823364244ea81961cee5bc7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/01/2020
ms.locfileid: "85792984"
---
# <a name="make-changes-and-redeploy"></a>Внесение изменений и повторное развертывание

[Предыдущий шаг. Развертывание образа приложения](tutorial-vscode-docker-node-05.md)

Рано или поздно вы обязательно внесете в приложение какие-либо изменения, а значит потребуется заново создать и развернуть контейнер. К счастью, этот процесс очень прост.

1. Внесите нужные изменения в приложение и протестируйте их локально.

1. В Visual Studio Code откройте **палитру команд** (с помощью клавиши **F1**) и выполните команду **Docker Images: Build Image** (Образы Docker: создание образа), чтобы повторно создать образ. Если вы измените только код приложения, сборка займет всего несколько секунд.

1. Чтобы отправить образ в реестр, откройте **палитру команд** (**F1**) снова и выполните команду **Docker Images: Push** (Образы Docker: отправка), выбрав только что созданный образ. Как и в прошлый раз, так как вы лишь немного изменили код приложения, этот процесс также завершится через несколько секунд, потому что передается только небольшой слой с изменениями.

1. В области **Azure: Служба приложений**, щелкните Службу приложений правой кнопкой мыши и выберите действие **Restart** (Перезапустить). При перезапуске Службы приложений автоматически извлекается из реестра последний образ контейнера.

1. Примерно через 15-20 секунд снова откройте URL-адрес Службы приложений, чтобы проверить обновления.

> [!div class="nextstepaction"]
> [Изменения отображаются](tutorial-vscode-docker-node-07.md) [Возникла ошибка](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-docker-extension&step=deploy-changes)