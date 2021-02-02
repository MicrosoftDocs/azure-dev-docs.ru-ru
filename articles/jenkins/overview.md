---
title: Общие сведения о Jenkins и Azure
description: Размещение сборки Jenkins и развертывание сервера автоматизации в Azure, а также использование вычислительных ресурсов и ресурсов службы хранилища Azure для расширения конвейеров непрерывной интеграции и непрерывного развертывания (CI/CD).
keywords: jenkins, azure, devops, overview
ms.topic: overview
ms.date: 11/10/2020
ms.custom: devx-track-jenkins
ms.openlocfilehash: 38703bbd857c1990571f6255ab3a4d9089ad7a46
ms.sourcegitcommit: 3d906f265b748fbc0a070fce252098675674c8d9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/22/2021
ms.locfileid: "98699832"
---
# <a name="azure-and-jenkins"></a>Azure и Jenkins

[Jenkins](https://jenkins.io/) — это популярный сервер автоматизации с открытым кодом, используемый для внедрения непрерывной интеграции и непрерывной доставки (CI/CD) для проектов программного обеспечения. Вы можете разместить развертывание Jenkins в Azure либо расширить имеющуюся конфигурацию Jenkins с помощью ресурсов Azure. Кроме того, можно использовать подключаемые модули Jenkins, чтобы упростить непрерывную интеграцию и непрерывную доставку приложений в Azure.

Это вводная статья об использовании Azure с Jenkins, в которой подробно рассматриваются основные возможности Azure, доступные пользователям Jenkins. См. дополнительные сведения о [создании сервера Jenkins в Azure](configure-on-linux-vm.md).

## <a name="host-your-jenkins-servers-in-azure"></a>Размещение серверов Jenkins в Azure

Разместите Jenkins в Azure, чтобы централизовать автоматизацию выполнения сборки и масштабировать развертывание по мере роста потребностей проектов программного обеспечения. Сведения об установке и настройке Jenkins в виртуальной машине Linux см. в статье [Краткое руководство. Начало работы с Jenkins](configure-on-linux-vm.md). Для мониторинга развертывания Jenkins в Azure и управления им можно использовать [журналы Azure Monitor](/azure/log-analytics/log-analytics-overview) и [Azure CLI](/cli/azure).

## <a name="scale-your-build-automation-on-demand"></a>Масштабирование по требованию автоматизации выполнения сборки

Вы можете добавлять агенты сборки в существующее развертывание Jenkins, чтобы масштабировать производительность выполнения сборки Jenkins по мере увеличения числа сборок и усложнения заданий и конвейеров. Эти агенты сборки можно запускать на виртуальных машинах Azure с помощью [подключаемого модуля агентов виртуальных машин Azure](https://plugins.jenkins.io/azure-vm-agents). Ознакомьтесь с нашим [руководством](/azure/jenkins/jenkins-azure-vm-agents), чтобы получить дополнительные сведения.

После настройки [субъекта-службы Azure](/azure/azure-resource-manager/resource-group-overview) для заданий и конвейеров Jenkins эти учетные данные можно будет использовать для следующего:

- Для безопасного хранения и архивации артефактов сборки в [службе хранилища Azure](/azure/storage/common/storage-introduction) с помощью [подключаемого модуля службы хранилища Azure](https://plugins.jenkins.io/windows-azure-storage). Просмотрите [практическое руководство по хранилищу Jenkins](azure-storage-blobs-as-build-artifact-repository.md), чтобы получить дополнительные сведения.
- Для управления и настройки ресурсов Azure с помощью [Azure CLI](deploy-to-azure-app-service-using-azure-cli.md).

## <a name="deploy-your-code-into-azure-services"></a>Развертывание кода в службах Azure

Используйте подключаемые модули Jenkins для развертывания приложений в Azure в рамках конвейеров непрерывной интеграции и непрерывной доставки Jenkins. Развертывание в [службе приложений Azure](/azure/app-service/) и [Службе контейнеров Azure](/azure/container-service/kubernetes/) позволяет поэтапно создавать и тестировать приложения, а также выпускать обновления для них без необходимости управлять базовой инфраструктурой.

 Доступны подключаемые модули для развертывания в следующих службах и средах.

- [Служба приложений Azure в Linux](/azure/app-service/containers/app-service-linux-intro). Чтобы приступить к работе, ознакомьтесь с этим [руководством](deploy-to-azure-app-service-using-azure-cli.md).