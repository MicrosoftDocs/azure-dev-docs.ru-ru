---
title: Создание виртуальной машины Linux с помощью Azure CLI
description: Узнайте, как создать виртуальную машину Linux в Azure с помощью клона приложения на основе Express.js из репозитория GitHub.
ms.topic: tutorial
ms.date: 11/13/2020
ms.custom: devx-track-js
ms.openlocfilehash: c68a8442862eb03291f80609bb9767fb1c9b1b6b
ms.sourcegitcommit: ed31f841fa9680335658df6708f107e170d47ff0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/07/2021
ms.locfileid: "97974551"
---
# <a name="1-create-linux-virtual-machine-with-expressjs-app-using-azure-cli"></a>1. Создание виртуальной машины Linux на основе приложения Express.js с помощью Azure CLI

В этом руководстве описано, как создать виртуальную машину Linux для приложения Express.js. Виртуальная машина настраивается с помощью файла конфигурации cloud-init и содержит NGINX и репозиторий GitHub для приложения Express.js. После запуска виртуальной машины можно подключиться к ней по протоколу SSH. Также можно изменить веб-приложение, включив ведение журнала трассировки, и просмотреть общедоступное серверное приложение Express.js в веб-браузере.

* [**Образец кода**](https://github.com/Azure-Samples/js-e2e-vm)

Учебник включает в себя следующие разделы:

* Вход в Azure с помощью Azure CLI
* Создание ресурса виртуальной машины Linux в Azure с помощью Azure CLI
    * Открытие общедоступного порта 80.
    * Установка демонстрационного веб-приложения Express.js из репозитория GitHub.
    * Установка зависимостей веб-приложения.
    * Запуск веб-приложения.
* Создание ресурса Azure Monitoring с помощью Azure CLI.
    * Подключение к виртуальной машине по протоколу SSH.
    * Установка клиентской библиотеки пакета Azure SDK с помощью npm.
    * Добавление кода клиентской библиотеки Application Insights для создания настраиваемой трассировки.
* Просмотр веб-приложения из браузера.
    * Запрос маршрута `/trace` для создания настраиваемой трассировки в журнале Application Insights.
    * Просмотр числа трассировок, собранных в журнале, с помощью Azure CLI.
    * Просмотр списка трассировок на портале Azure.
* Удаление ресурсов с помощью Azure CLI.

[!INCLUDE [Create or use existing Azure Subscription ](../../includes/environment-subscription-h2.md)]

## <a name="prerequisites"></a>Предварительные требования

- Протокол SSH для подключения к виртуальной машине: Используйте современный терминал, например оболочку Bash, с поддержкой протокола SSH.
[!INCLUDE [Azure CLI](../../../includes/azure-cli-prepare-your-environment-no-header.md)]


## <a name="next-step"></a>Следующий шаг

> [!div class="nextstepaction"]
> [Создание группы ресурсов для ресурсов Azure](create-azure-monitoring-application-insights-web-resource.md) 