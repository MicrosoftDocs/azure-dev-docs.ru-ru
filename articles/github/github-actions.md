---
title: Развертывание в Azure с помощью GitHub Actions
description: Создание рабочих процессов в репозитории для сборки, тестирования, упаковки, выпуска и развертывания в Azure.
author: N-Usha
ms.author: ushan
ms.topic: conceptual
ms.service: azure
ms.date: 05/05/2020
ms.openlocfilehash: 1b3906eeacf48e2561f49dc8480ce83565aeca3a
ms.sourcegitcommit: fbbc341a0b9e17da305bd877027b779f5b0694cc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83631670"
---
# <a name="deploy-to-azure-using-github-actions"></a>Развертывание в Azure с помощью GitHub Actions

Компонент [GitHub Actions](https://help.github.com/articles/about-github-actions) позволяет разработчикам создавать автоматизированные рабочие процессы жизненного цикла разработки программного обеспечения.  

С помощью GitHub Actions для Azure вы можете создавать рабочие процессы в репозитории для сборки, тестирования, упаковки, выпуска и **развертывания** в Azure. [См. сведения о других возможностях интеграции с Azure.](https://aka.ms/GitHubonAzure)

Начните работу уже сегодня с [бесплатной учетной записью Azure](https://azure.com/free/open-source)!

> [!NOTE]   
> Приведенные в этой статье ссылки ведут на статьи и репозитории в GitHub. 

- [Действие Azure/webapps-deploy](https://github.com/Azure/webapps-deploy) выполняет развертывание в Веб-приложениях Azure и Веб-приложении для контейнеров Azure. 
- [Azure/appservice-settings](https://github.com/Azure/appservice-settings) позволяет массово настраивать параметры приложения, строки подключения и другие общие параметры с использованием синтаксиса JSON в веб-приложении Azure (для Windows или Linux) и для любых поддерживаемых слотов развертывания.



## <a name="key-concepts"></a>Основные понятия

Функция GitHub Actions позволяет создавать пользовательские рабочие процессы для жизненного цикла разработки программного обеспечения (SDLC) непосредственно в репозитории GitHub. Общие сведения и основные понятия GitHub Actions описаны в следующих статьях. 

- [Сведения о GitHub Actions](https://help.github.com/actions/getting-started-with-github-actions/about-github-actions)
- [Основные понятия](https://help.github.com/actions/getting-started-with-github-actions/core-concepts-for-github-actions)
- [Упаковка с помощью GitHub Actions](https://help.github.com/en/actions/publishing-packages-with-github-actions/about-packaging-with-github-actions)

## <a name="get-started"></a>Начало работы 

GitHub Actions содержат предварительно настроенные шаблоны и действия для Marketplace. 

- [Использование предварительно настроенных шаблонов](https://help.github.com/actions/getting-started-with-github-actions/starting-with-preconfigured-workflow-templates)  
- [Использование действий из GitHub Marketplace](https://help.github.com/en/actions/getting-started-with-github-actions/using-actions-from-github-marketplace)  
- [Действия GitHub Actions для Marketplace, развертывание в Azure](https://github.com/marketplace?type=actions&query=Azure)  
  
См. сведения о GitHub Actions для Azure на следующих страницах: 
   
- [Действия для Azure](https://github.com/marketplace?query=Azure&type=actions)  
- [Базовые рабочие процессы с действиями для развертывания в Azure](https://github.com/Azure/actions-workflow-samples)


## <a name="connect-to-azure"></a>Подключение к Azure

Примеры рабочих процессов для подключения к Azure см. в описании следующих действий GitHub:  

- [Вход в Azure](https://github.com/Azure/login)  
- [Azure CLI](https://github.com/Azure/CLI)  


## <a name="sample-apps-with-cicd-workflow-samples"></a>Примеры приложений с примерами рабочих процессов CI/CD 

В следующих примерах представлены комплексные рабочие процессы для сборки и развертывания в Azure веб-приложений на основе любого языка и любой экосистемы. 

- [Развертывание веб-приложения с поддержкой ASP.NET](https://github.com/Azure-Samples/dotnet-sample)  
- [Развертывание приложения ASP.NET Core](https://github.com/Azure-Samples/dotnet_core_sample)  
- [Развертывание веб-приложения Node.js](https://github.com/Azure-Samples/node_express_app)  
- [Развертывание веб-приложения Java](https://github.com/Azure-Samples/java-spring-petclinic)  
- [Развертывание приложения Java Spring](https://github.com/Azure-Samples/Java-application-petstore-ee7)  
- [Развертывание веб-приложения Python](https://github.com/Azure-Samples/pythonSample_thecatsaidno)  
- [Развертывание с помощью Docker](https://github.com/Azure-Samples/Node_express_container)  


## <a name="deploy-a-web-app"></a>Развертывание веб-приложения

- [Веб-приложение Azure](https://github.com/Azure/webapps-deploy)  
Настройте параметры приложения и строки подключения с помощью действий:

- [Веб-приложение Azure для контейнеров](https://github.com/Azure/webapps-container-deploy)  
- [Параметры Службы приложений Azure](https://github.com/Azure/appservice-settings)  

## <a name="deploy-a-serverless-app"></a>Развертывание бессерверного приложения

- [Функции Azure](https://github.com/Azure/functions-action)  
- [Функции Azure для контейнеров](https://github.com/Azure/webapps-container-deploy)  
 
## <a name="build-and-deploy-containerized-apps"></a>Сборка и развертывание контейнерных приложений

- [Вход в Docker](https://github.com/Azure/docker-login)  
- [Развертывание в Экземплярах контейнеров Azure](https://github.com/Azure/aci-deploy)

## <a name="deploy-to-kubernetes"></a>Развертывание в Kubernetes

- [Установщик средств Kubectl](https://github.com/Azure/setup-kubectl)  
- [Настройка контекста выполнения Kubernetes](https://github.com/Azure/k8s-set-context)  
- [Настройка контекста выполнения AKS](https://github.com/Azure/aks-set-context)  
- [Создание секрета Kubernetes](https://github.com/Azure/k8s-create-secret)  
- [Развертывание Kubernetes](https://github.com/Azure/k8s-deploy)  
- [Настройка Helm](https://github.com/Azure/setup-helm)  
- [Моделирование Kubernetes](https://github.com/Azure/k8s-bake)  

## <a name="train-and-deploy-a-machine-learning-model"></a>Обучение и развертывание модели машинного обучения 

- [Имя входа](https://github.com/Azure/aml-workspace) 
- [Обучение](https://github.com/Azure/aml-run)
- [Развертывание модели](https://github.com/Azure/aml-deploy)

## <a name="deploy-to-databases"></a>Развертывание в базах данных

- [База данных SQL Azure](https://github.com/Azure/sql-action)  
- [Действие MySQL Azure](https://github.com/Azure/mysql-action)  

## <a name="deploy-machine-learning-models"></a>Развертывание моделей Машинного обучения

- [Развертывание Машинного обучения Azure](https://github.com/Azure/aml-deploy)  

## <a name="trigger-a-run-in-azure-pipelines"></a>Запуск выполнения в Azure Pipelines

- [Azure Pipelines](https://github.com/Azure/pipelines)  
 
## <a name="utility-actions"></a>Действия для Unity

- [Подстановка переменных](https://github.com/Microsoft/variable-substitution) 


## <a name="additional-resources"></a>Дополнительные ресурсы

Следующие ресурсы GitHub помогают применять GitHub для развертывания приложений в Azure.  

- [Marketplace для GitHub Actions для Azure](https://github.com/marketplace?query=Azure&type=actions)
- [Учебное задание по непрерывной поставке для Azure](https://lab.github.com/githubtraining/github-actions:-continuous-delivery-with-azure)
- [Базовые рабочие процессы с действиями для развертывания в Azure](https://github.com/Azure/actions-workflow-samples)
