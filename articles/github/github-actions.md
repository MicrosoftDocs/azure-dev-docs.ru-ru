---
title: Развертывание в Azure с помощью GitHub Actions
description: Создание рабочих процессов в репозитории для сборки, тестирования, упаковки, выпуска и развертывания в Azure.
ms.author: kaelli
author: KathrynEE
ms.topic: conceptual
ms.service: azure
ms.date: 05/05/2020
ms.openlocfilehash: 16ebc2d8013b2199911c0648f4bb8364c4d6de06
ms.sourcegitcommit: a631b36ec1277ee9397a860c597ffdd5495d88e7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/13/2020
ms.locfileid: "83369924"
---
# <a name="deploy-to-azure-using-github-actions"></a>Развертывание в Azure с помощью GitHub Actions

GitHub Actions для Azure позволяет автоматизировать рабочие процессы для развертывания в Azure. Чтобы начать работу, вам достаточно следующих действий GitHub Actions: 

- [Azure/webapps-deploy](https://github.com/Azure/webapps-deploy) выполняет развертывание в службах Azure "Веб-приложения" и "Веб-приложение для контейнеров". 
- [Azure/appservice-settings](https://github.com/Azure/appservice-settings) позволяет массово настраивать параметры приложения, строки подключения и другие общие параметры с использованием синтаксиса JSON в веб-приложении Azure (для Windows или Linux) и для любых поддерживаемых слотов развертывания.

> [!NOTE]   
> Приведенные в этой статье ссылки ведут на статьи и репозитории в GitHub. 

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
  
Ссылки на все действия GitHub Actions для Azure собраны на следующей странице: 
   
- [Действия для Azure](https://github.com/marketplace?query=Azure&type=actions)  

## <a name="connect-to-azure"></a>Подключение к Azure

Примеры рабочих процессов для подключения к Azure см. в описании следующих действий GitHub:  

- [Вход в Azure](https://github.com/Azure/login)  
- [Azure CLI](https://github.com/Azure/CLI)  


## <a name="starter-templates-and-end-to-end-cicd-workflow-samples"></a>Шаблоны для начала работы и комплексные примеры рабочих процессов CI/CD 

В следующих примерах представлены комплексные рабочие процессы, позволяющие развертывать веб-приложения в Azure. 

- [Развертывание веб-приложения с поддержкой ASP.NET](https://github.com/Azure-Samples/dotnet-sample)  
- [Развертывание приложения ASP.NET Core](https://github.com/Azure-Samples/dotnet_core_sample)  
- [Развертывание веб-приложения Node.js](https://github.com/Azure-Samples/node_express_app)  
- [Развертывание веб-приложения Java](https://github.com/Azure-Samples/java-spring-petclinic)  
- [Развертывание приложения Java Spring](https://github.com/Azure-Samples/Java-application-petstore-ee7)  
- [Развертывание веб-приложения Python](https://github.com/Azure-Samples/pythonSample_thecatsaidno)  
- [Развертывание с помощью Docker](https://github.com/Azure-Samples/Node_express_container)  


## <a name="deploy-a-web-app"></a>Развертывание веб-приложения

- [Веб-приложение Azure](https://github.com/Azure/webapps-deploy)  
- [Веб-приложение Azure для контейнеров](https://github.com/Azure/webapps-container-deploy)  
- [Параметры Службы приложений Azure](https://github.com/Azure/appservice-settings)  

## <a name="deploy-a-serverless-app"></a>Развертывание бессерверного приложения

- [Функции Azure](https://github.com/Azure/functions-action)  
- [Функции Azure для контейнеров](https://github.com/Azure/webapps-container-deploy)  
 
## <a name="build-and-deploy-containerized-apps"></a>Сборка и развертывание контейнерных приложений

- [Вход в Docker](https://github.com/Azure/docker-login)  

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

## <a name="trigger-a-run-in-azure-pipelines"></a>Запуск выполнения в Azure Pipelines

- [Azure Pipelines](https://github.com/Azure/pipelines)  
 
## <a name="utility-actions"></a>Действия для Unity

- [Подстановка переменных](https://github.com/Microsoft/variable-substitution) 


## <a name="additional-resources"></a>Дополнительные ресурсы

Следующие ресурсы GitHub помогают применять GitHub для развертывания приложений в Azure.  

- [GitHub Actions для Azure Marketplace](https://github.com/marketplace?query=Azure&type=actions)
- [Учебное задание по непрерывной поставке для Azure](https://lab.github.com/githubtraining/github-actions:-continuous-delivery-with-azure)
- [Базовые рабочие процессы с действиями для развертывания в Azure](https://github.com/Azure/actions-workflow-samples)
