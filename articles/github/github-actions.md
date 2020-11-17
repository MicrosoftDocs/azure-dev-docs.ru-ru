---
title: Что такое GitHub Actions для Azure?
description: Узнайте о создании рабочих процессов в репозитории для сборки, тестирования, упаковки, выпуска и развертывания в Azure.
author: N-Usha
ms.author: ushan
ms.topic: conceptual
ms.service: azure
ms.date: 10/30/2020
ms.custom: github-actions-azure
ms.openlocfilehash: bbb87890f4db4b744ae4b2794c86e86a18c0e352
ms.sourcegitcommit: 12f80b1e0fe08db707c198271d0c399c3aba343a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/11/2020
ms.locfileid: "94515125"
---
# <a name="what-is-github-actions-for-azure"></a>Что такое GitHub Actions для Azure

Компонент [GitHub Actions](https://help.github.com/articles/about-github-actions) помогает автоматизировать рабочие процессы разработки программного обеспечения из GitHub. Вы можете развертывать рабочие процессы там же, где храните код, и вместе с другими пользователями создавать запросы на вытягивание и решать проблемы.

В GitHub Actions [рабочим процессом](https://help.github.com/articles/about-github-actions#workflow) считается автоматизированный процесс, который вы настроили в своем репозитории GitHub. С помощью рабочего процесса вы можете выполнять сборку, тестирование, упаковку, выпуск и развертывание любого проекта в GitHub.

Каждый рабочий процесс включает отдельные [действия](https://docs.github.com/en/free-pro-team@latest/actions/learn-github-actions/introduction-to-github-actions), которые запускаются после определенного события (например, запроса на вытягивание).  Отдельные действия представлены упакованными скриптами, которые автоматизируют задачи разработки программного обеспечения.

С помощью GitHub Actions для Azure вы можете создавать рабочие процессы в репозитории для сборки, тестирования, упаковки, выпуска и развертывания в Azure. Компонент GitHub Actions для Azure поддерживает службы Azure, в том числе Службу приложений Azure, Функции Azure и Azure Key Vault.

GitHub Actions также обеспечивает поддержку разных инструментов, таких как шаблоны Azure Resource Manager, Azure CLI и Политика Azure.

## <a name="why-should-i-use-github-actions-for-azure"></a>Преимущества GitHub Actions для Azure

Компонент GitHub Actions для Azure разработан Майкрософт и предназначен для использования с Azure. Вы можете ознакомиться со всеми возможностями GitHub Actions для Azure в [GitHub Marketplace](https://github.com/marketplace?query=Azure&type=actions). Чтобы узнать больше об использовании действий с рабочими процессами, см. статью о [поиске и настройке действий](https://docs.github.com/en/free-pro-team@latest/actions/learn-github-actions/finding-and-customizing-actions).

## <a name="what-is-the-difference-between-github-actions-and-azure-pipelines"></a>Различия между GitHub Actions и Azure Pipelines

Как Azure Pipelines, так и GitHub Actions помогают автоматизировать рабочие процессы разработки ПО. Сведения о различиях между службами и переходе с Azure Pipelines к GitHub Actions см. [здесь](https://docs.github.com/en/free-pro-team@latest/actions/learn-github-actions/migrating-from-azure-pipelines-to-github-actions).

## <a name="what-do-i-need-to-use-github-actions-for-azure"></a>Предварительные требования для использования GitHub Actions для Azure

Вам потребуются учетные записи Azure и GitHub:

* Учетная запись Azure с активной подпиской. [Создайте учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) бесплатно.
* Учетная запись GitHub. Если у вас ее нет, зарегистрируйтесь [бесплатно](https://github.com/join).  

## <a name="how-do-i-connect-github-actions-and-azure"></a>Подключение GitHub Actions к Azure

В зависимости от действия вы будете использовать субъект-службу или профиль публикации для подключения к Azure из GitHub. Субъект-служба задействуется каждый раз при использовании действия [входа в Azure](https://github.com/marketplace/actions/azure-login). [Действия Службы приложений Azure](https://github.com/marketplace/actions/azure-webapp) поддерживают использование профиля публикации или субъекта-службы. Дополнительные сведения о субъектах-службах см. в статье [Объекты приложения и субъекта-службы в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object).  

Вы можете использовать действие входа в Azure в сочетании с действиями [Azure CLI](https://github.com/marketplace/actions/azure-cli-action) и [Azure PowerShell](https://github.com/marketplace/actions/azure-powershell-action). Действие входа в Azure также работает с большинством других действий GitHub для Azure, в том числе с [развертыванием в веб-приложениях](https://github.com/marketplace/actions/azure-webapp) и [получением доступа к секретам в хранилище ключей](https://github.com/marketplace/actions/azure-key-vault-get-secrets).

## <a name="what-is-included-in-a-github-actions-workflow"></a>Составляющие рабочего процесса GitHub Actions

Рабочие процессы включают одно или несколько заданий. Задание состоит из шагов, представляющих собой отдельные действия. Дополнительные сведения о концепциях GitHub Actions см. в статье с [общим обзором GitHub Actions](https://docs.github.com/en/free-pro-team@latest/actions/learn-github-actions/introduction-to-github-actions).  

## <a name="where-can-i-see-complete-workflow-examples"></a>Ресурсы с примерами рабочих процессов

В [репозитории с начальным набором рабочих процессов и действий для Azure](https://github.com/Azure/actions-workflow-samples) доступны комплексные рабочие процессы для сборки и развертывания в Azure веб-приложений на основе любого языка и любой экосистемы.

## <a name="where-can-i-see-all-the-available-actions"></a>Ресурсы с доступными действиями

В [Marketplace для GitHub Actions](https://github.com/marketplace?query=Azure&type=actions) вы найдете все доступные в GitHub Actions действия для Azure.

* [Развертывание в статическом веб-приложении](/azure/static-web-apps/getting-started?tabs=angular)
* [Параметры Службы приложений Azure](https://github.com/Azure/appservice-settings)  
* [Развертывание в Функции Azure](https://github.com/Azure/functions-action)  
* [Развертывание в Функциях Azure для контейнеров](https://github.com/Azure/webapps-container-deploy)  
* [Вход в Docker](https://github.com/Azure/docker-login)  
* [Развертывание в Экземплярах контейнеров Azure](https://github.com/Azure/aci-deploy)
* [Действие сканирования контейнера](https://github.com/Azure/container-scan)
* [Установщик средств Kubectl](https://github.com/Azure/setup-kubectl)  
* [Настройка контекста выполнения Kubernetes](https://github.com/Azure/k8s-set-context)  
* [Настройка контекста выполнения AKS](https://github.com/Azure/aks-set-context)  
* [Создание секрета Kubernetes](https://github.com/Azure/k8s-create-secret)  
* [Развертывание Kubernetes](https://github.com/Azure/k8s-deploy)  
* [Настройка Helm](https://github.com/Azure/setup-helm)  
* [Моделирование Kubernetes](https://github.com/Azure/k8s-bake)  
* [Создание образов виртуальных машин Azure](https://github.com/Azure/build-vm-image)
* [Вход в службу машинного обучения](https://github.com/Azure/aml-workspace)
* [Машинное обучение](https://github.com/Azure/aml-run)
* [Машинное обучение — развертывание модели](https://github.com/Azure/aml-deploy)
* [Развертывание в базе данных SQL Azure](https://github.com/Azure/sql-action)  
* [Действие развертывания в Azure MySQL](https://github.com/Azure/mysql-action)  
* [Проверка соответствия в Политике Azure](https://github.com/Azure/policy-compliance-scan)
* [Управление Политикой Azure](https://github.com/Azure/manage-azure-policy)
* [Запуск выполнения в Azure Pipelines](https://github.com/Azure/pipelines)  
* [Подстановка переменных](https://github.com/Microsoft/variable-substitution)

## <a name="next-steps"></a>Next Steps

> [!div class="nextstepaction"]
> [Схема обучения. Автоматизация рабочего процесса с помощью GitHub Actions](https://docs.microsoft.com/learn/modules/github-actions-automate-tasks/)

> [!div class="nextstepaction"]
> [Учебное задание по непрерывной поставке для Azure](https://lab.github.com/githubtraining/github-actions:-continuous-delivery-with-azure)
