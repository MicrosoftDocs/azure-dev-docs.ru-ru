---
title: Средства для разработчиков, использующих пакет Azure SDK для Go
description: Средства для работы с пакетом Azure SDK для Go и службами Azure
author: sptramer
ms.author: sttramer
manager: carmonm
ms.date: 09/05/2018
ms.topic: conceptual
ms.devlang: go
ms.openlocfilehash: 9db908f6da697e4e522064cff830d87278b70b8e
ms.sourcegitcommit: 2efdb9d8a8f8a2c1914bd545a8c22ae6fe0f463b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/15/2019
ms.locfileid: "68291808"
---
# <a name="tools-for-developers-using-the-azure-sdk-for-go"></a>Средства для разработчиков, использующих пакет Azure SDK для Go

Ниже приведены некоторые рекомендуемые средства, которые помогают эффективно писать код Go, а также обеспечивают его совместимость со службами Azure.

## <a name="azure-cli"></a>Инфраструктура CLI Azure

Azure CLI предоставляет интерфейс командной строки для создания и настройки ресурсов Azure в подписках. CLI поможет вам быстро приступить к созданию распространенных и общих ресурсов Azure, чтобы вы могли сосредоточиться на использовании более сложных служб. CLI содержит функции фильтрации и создания запросов, позволяя передавать выходные данные непосредственно в используемые вами программы командной строки. CLI можно установить в локальной системе как образ Docker или с помощью [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview).

> [!div class="nextstepaction"]
> [Установка Azure CLI](/cli/azure/install-azure-cli)

## <a name="visual-studio-code"></a>Visual Studio Code

Visual Studio Code — это упрощенный редактор, который поддерживает Go. Это расширение поддерживает такие функции, как автозаполнение, шаблоны `impl`, рефакторинг и отладка. Visual Studio Code также предоставляет доступ к системе управления версиями из редактора, а также включает расширения для работы со службами Azure.

* [Установка Visual Studio Code](https://code.visualstudio.com/Download)
* [Получение расширения Go для Visual Studio Code](https://code.visualstudio.com/docs/languages/go)
* [Получение расширения "Инструменты Azure" для Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-azureextensionpack)

## <a name="cicd-with-azure-devops-project"></a>Использование CI/CD с помощью проекта Azure DevOps

С помощью конвейеров проекта DevOps Azure можно настроить систему непрерывной интеграции для приложений Go. Все что вам нужно — это репозиторий Git. Это позволит вам выполнять развертывание и тестирование непосредственно в Azure.

> [!div class="nextstepaction"]
> [Создание конвейера CI/CD с помощью проекта Azure DevOps](/azure/devops-project/azure-devops-project-go)

## <a name="dependency-management-with-dep"></a>Управление зависимостями с помощью средства dep

Пакет Azure SDK для Go использует средство dep для управления зависимостями. Команда dep позволяет выполнять вытягивание и вендоринг требований для приложения Go, предотвращая появление конфликтов версий и обеспечивая правильную работу проектов.

> [!div class="nextstepaction"]
> [Получить диспетчер зависимостей dep](https://github.com/golang/dep)
