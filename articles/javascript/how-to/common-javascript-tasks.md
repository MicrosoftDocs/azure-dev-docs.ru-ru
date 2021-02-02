---
title: Популярные задачи Azure для разработчиков JavaScript
description: Здесь вы можете найти примеры текущих задач.
ms.topic: reference
ms.date: 01/20/2021
ms.custom: devx-track-js
ms.openlocfilehash: 2d7a3091529adb530af76069d9f70880a68e40d3
ms.sourcegitcommit: 681ea211dc8e1809dd299b5cd64eebd974c073d7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/21/2021
ms.locfileid: "98658771"
---
# <a name="top-tasks-for-javascript-developers"></a>Распространенные задачи для разработчиков JavaScript

Здесь вы можете найти пример текущей задачи. Если вы не можете найти задачу, оставьте отзыв с запросом задачи. 

## <a name="app-registration"></a>Регистрация приложений

|Задача|using|
|--|--|
|Создание регистрации приложения|[Портал](../tutorial/single-page-application-azure-login-button-sdk-msal.md#3-create-app-registration-for-authentication)|

## <a name="application-settings"></a>Параметры приложений

|Задача|using|
|--|--|
|Задание переменных локальной среды|[Bash](../tutorial/static-web-app/create-computer-vision-resource-use-in-code.md#add-environment-variables-to-your-local-environment)|
|Создание маркера подписанного URL-адреса для контейнера службы хранилища|[Портал](../tutorial/browser-file-upload-azure-storage-blob.md#5-generate-your-shared-access-signature-sas-token)|
|Задание маркера SAS в коде|[TypeScript](../tutorial/browser-file-upload-azure-storage-blob.md#set-sas-token-in-code-file)|
|Настройка CORS для службы хранилища|[Портал](../tutorial/browser-file-upload-azure-storage-blob.md#6-configure-cors-for-azure-storage-resource)|

## <a name="authentication"></a>Аутентификация

|Задача|using|
|--|--|
|Кнопка входа и выхода на портале Майкрософт с использованием `@azure/msal-browser`|[React/TypeScript](../tutorial/single-page-application-azure-login-button-sdk-msal.md#5-add-login-and-logoff-buttons)|
|Отмена разрешения AAD|[https://myapplications.microsoft.com/](https://myapplications.microsoft.com/)|
|Отмена разрешения объекта-получателя|[https://account.live.com/consent/manage](https://account.live.com/consent/manage)|

## <a name="azure-login"></a>Вход в Azure

|Задача|using|
|--|--|
|Вход|[Azure CLI](../tutorial/deploy-deno-app-azure-app-service-azure-cli.md#2-sign-in-to-azure-cli)<br>[Расширение Visual Studio Code](../tutorial/tutorial-vscode-docker-node/tutorial-vscode-docker-node-01.md#sign-in-to-azure)|


## <a name="azure-resource-groups"></a>Группы ресурсов Azure

|Задача|using|
|--|--|
|Создать группу ресурсов|[Azure CLI](../tutorial/static-web-app/create-computer-vision-resource-use-in-code.md#create-azure-resources)|
|Удалить группу ресурсов|[Azure CLI](../tutorial/static-web-app/clean-up-resources.md#remove-all-the-resources-by-removing-resource-group)|

## <a name="apps"></a>Приложения

### <a name="static-web-apps"></a>Статические веб-приложения

|Задача|using|
|--|--|
|Создание приложения Angular|[Bash](../tutorial/tutorial-vscode-static-website-node/tutorial-vscode-static-website-node-02.md?tabs=angular)|
|Создание приложения Deno|[Bash](../tutorial/deploy-deno-app-azure-app-service-azure-cli.md#3-create-local-deno-api-app)|
|Создание приложения React для языка JavaScript|[Bash](../tutorial/tutorial-vscode-static-website-node/tutorial-vscode-static-website-node-02.md?tabs=react)|
|Создание приложения React для языка TypeScript|[Bash](../tutorial/single-page-application-azure-login-button-sdk-msal.md#4-create-react-single-page-application-for-typescript)|
|Создание приложения Vue|[Bash](../tutorial/tutorial-vscode-static-website-node/tutorial-vscode-static-website-node-02.md?tabs=vue)|
|Создание приложения Svelte|[Bash](../tutorial/tutorial-vscode-static-website-node/tutorial-vscode-static-website-node-02.md?tabs=svelte)|
|Создание статического веб-приложения|[Расширение Visual Studio Code](../tutorial/static-web-app/create-static-web-app-visual-studio-code-extension.md#create-a-static-web-app-resource)|
|Создание статического приложения, размещенного в службе хранилища|[Расширение Visual Studio Code](../tutorial/tutorial-vscode-static-website-node/tutorial-vscode-static-website-node-03.md)|
|Развертывание статического приложения, размещенного в службе хранилища|[Расширение Visual Studio Code](../tutorial/tutorial-vscode-static-website-node/tutorial-vscode-static-website-node-04.md)|
|Просмотр сайта|[Расширение Visual Studio Code](../tutorial/static-web-app/create-static-web-app-visual-studio-code-extension.md#view-azure-static-web-site-in-browser)|

### <a name="function-serverless-apps"></a>Приложения Функции (бессерверные)

|Задача|using|
|--|--|
|Локальное создание приложений Функций|[Расширение Visual Studio Code](../tutorial/tutorial-vscode-serverless-node-create-local.md)|
|Код триггера HTTP|[JavaScript](../tutorial/tutorial-vscode-serverless-node-create-local.md#http-function-javascript-template-code)|
|Локальная отладка и тестирование функции|[Visual Studio Code](../tutorial/tutorial-vscode-serverless-node-test-local.md)|
|Развертывание функции в облаке Azure|[Расширение Visual Studio Code](../tutorial/tutorial-vscode-serverless-node-deploy-hosting.md)|
|Функция проверки доступна по общедоступному URL-адресу|[Расширение Visual Studio Code/браузер](../tutorial/tutorial-vscode-serverless-node-deploy-hosting.md#verify-functions-app-is-publicly-available-with-browser)|
|Удаление ресурса приложения-функции|[Расширение Visual Studio Code](../tutorial/tutorial-vscode-serverless-node-remove-resource.md)|

### <a name="app-service---full-stack-server-only-or-client-only-apps"></a>Служба приложений — полный стек, только серверные или только клиентские приложения

|Задача|using|
|--|--|
|Создание локального приложения Express.js|[Bash](../tutorial/deploy-nodejs-azure-app-service-with-visual-studio-code.md?tabs=bash#3-create-a-local-expressjs-app)|
|Создание ресурса приложения — включает развертывание приложения Express.js, журналы потоковых данных|[Расширение Visual Studio Code](../tutorial/deploy-nodejs-mongodb-app-service-from-visual-studio-code.md#create-web-app-resource-and-deploy-expressjs-app)|
|Создание ресурса приложения — включает развертывание приложения Express.js, настройку параметров приложения, запуск установки npm, переход на развернутый веб-сайт в браузере|[Расширение Visual Studio Code](../tutorial/deploy-nodejs-azure-app-service-with-visual-studio-code.md?tabs=bash#6-create-app-service-resource-in-visual-studio-code)|
|Создание ресурса приложения|[Azure CLI](../tutorial/tutorial-vscode-azure-cli-node/tutorial-vscode-azure-cli-node-03.md)|
|Создание приложения, развертывание, браузерное приложение, просмотр журналов|[Azure CLI](../tutorial/tutorial-vscode-azure-cli-node/tutorial-vscode-azure-cli-node-03.md)|
|Удаление ресурса приложения|[Расширение Visual Studio Code](../tutorial/deploy-nodejs-mongodb-app-service-from-visual-studio-code.md#clean-up-resources)<br>[Azure CLI](../tutorial/tutorial-vscode-azure-cli-node/tutorial-vscode-azure-cli-node-07.md)|
|Развертывание или компоновка приложения|[Расширение Visual Studio Code](deploy-web-app.md#deploy-or-redeploy-to-app-service-with-visual-studio-code)|
|Удаленные журналы потоковой передачи|[Расширение Visual Studio Code](../tutorial/deploy-nodejs-azure-app-service-with-visual-studio-code.md?tabs=bash#7-stream-remote-service-logs-in-visual-studio-code)<br>[Azure CLI](../tutorial/tutorial-vscode-azure-cli-node/tutorial-vscode-azure-cli-node-05.md)|

## <a name="cognitive-services"></a>Cognitive Services

|Задача|using|
|--|--|
|Создание ресурса _ComputerVision_ Cognitive Services|[Azure CLI](../tutorial/static-web-app/create-computer-vision-resource-use-in-code.md#create-azure-resources)|
|Получение ресурса _ComputerVision_ Cognitive Services|[Azure CLI](../tutorial/static-web-app/create-computer-vision-resource-use-in-code.md#create-azure-resources)|
|Установка пакета Azure SDK|[Bash](../tutorial/static-web-app/add-computer-vision-react-app.md#add-computer-vision-to-local-react-app)|
|Анализ изображения с помощью [`@azure/cognitiveservices-computervision`](https://www.npmjs.com/package/@azure/cognitiveservices-computervision)|[Visual Studio Code](../tutorial/static-web-app/add-computer-vision-react-app.md#add-computer-vision-code-as-separate-module)|

## <a name="containers"></a>Контейнеры

Проверьте [задачи Docker](#docker), если вы не нашли нужное. 

|Задача|using|
|--|--|
|Создание ресурса реестра контейнеров|[Расширение Visual Studio Code](../tutorial/tutorial-vscode-docker-node/tutorial-vscode-docker-node-02.md#create-an-azure-container-registry)|
|Отправка образа в ресурс реестра|[Расширение Visual Studio Code](../tutorial/tutorial-vscode-docker-node/tutorial-vscode-docker-node-04.md#push-the-image-to-a-registry)|
|Предоставление административного доступа к реестру|[Расширение Visual Studio Code](../tutorial/tutorial-vscode-docker-node/tutorial-vscode-docker-node-05.md#enable-admin-access-on-the-registry)|
|Развертывание образа в службе приложений|[Расширение Visual Studio Code](../tutorial/tutorial-vscode-docker-node/tutorial-vscode-docker-node-05.md?branch=main#deploy-image)|


## <a name="databases"></a>Базы данных

|Задача|using|
|--|--|
|Создание ресурса CosmosDB — MongoDB|[Расширение Visual Studio Code](../tutorial/deploy-nodejs-mongodb-app-service-from-visual-studio-code.md)|
|Получение строки подключения CosmosDB|[Расширение Visual Studio Code](../tutorial/deploy-nodejs-mongodb-app-service-from-visual-studio-code.md#get-cosmosdb-connection-string)|

## <a name="docker"></a>Docker

Проверьте [задачи контейнера](#containers), если вы не нашли нужное. 

|Задача|using|
|--|--|
|Проверка версии Docker|[Bash](../tutorial/tutorial-vscode-docker-node/tutorial-vscode-docker-node-01.md#verify-docker-install)|
|Добавление файлов Docker в локальный проект|[Расширение Visual Studio Code](../tutorial/tutorial-vscode-docker-node/tutorial-vscode-docker-node-04.md#add-docker-files)|
|Сборка образа Docker из локального проекта|[Bash](../tutorial/tutorial-vscode-docker-node/tutorial-vscode-docker-node-04.md#build-a-docker-image)|

## <a name="git"></a>Git

|Задача|using|
|--|--|
|Инициализация локального репозитория Git|[Расширение Visual Studio Code](../tutorial/deploy-nodejs-azure-app-service-with-visual-studio-code.md?tabs=bash#5-initialize-git-in-visual-studio-code-for-current-app)|

## <a name="github"></a>GitHub 

### <a name="actions"></a>Actions 

|Задача|using|
|--|--|
|Добавление секретов|[Visual Studio Code](../tutorial/static-web-app/create-static-web-app-visual-studio-code-extension.md#create-a-static-web-app-resource)|
|Просмотр процесса сборки|[Веб-сайт GitHub](../tutorial/static-web-app/create-static-web-app-visual-studio-code-extension.md#view-the-github-action-build-process)|


## <a name="monitoring"></a>Наблюдение

|Задача|using|
|--|--|
|Создайте ресурс|[Azure CLI](../tutorial/nodejs-virtual-machine-vm/create-azure-monitoring-application-insights-web-resource.md#create-azure-monitor-resource-with-azure-cli)|



## <a name="storage"></a>Память

|Задача|using|
|--|--|
|Создайте ресурс|[Расширение Visual Studio Code](../tutorial/browser-file-upload-azure-storage-blob.md#3-create-storage-resource-with-visual-studio-extension)|
|Удалить ресурс|[Расширение Visual Studio Code](../tutorial/browser-file-upload-azure-storage-blob.md#clean-up-resources)|
|Создание статического приложения, размещенного в службе хранилища|[Расширение Visual Studio Code](../tutorial/tutorial-vscode-static-website-node/tutorial-vscode-static-website-node-03.md)|
|Развертывание статического приложения, размещенного в службе хранилища|[Расширение Visual Studio Code](../tutorial/tutorial-vscode-static-website-node/tutorial-vscode-static-website-node-04.md)|

### <a name="blobs"></a>BLOB-объекты

|Задача|using|
|--|--|
|Создание контейнера в службе хранилища с помощью [`@azure/storage-blob`](https://www.npmjs.com/package/@azure/storage-blob)|[React/TypeScript](../tutorial/browser-file-upload-azure-storage-blob.md#create-storage-client-and-manage-steps)|
|Отправка файла в хранилище с помощью [`@azure/storage-blob`](https://www.npmjs.com/package/@azure/storage-blob)|[React/TypeScript](../tutorial/browser-file-upload-azure-storage-blob.md#upload-button-functionality)|
|Вывод файлов в контейнере хранилища с помощью [`@azure/storage-blob`](https://www.npmjs.com/package/@azure/storage-blob)|[React/TypeScript](../tutorial/browser-file-upload-azure-storage-blob.md#get-list-of-blobs)|

## <a name="virtual-machines"></a>Виртуальные машины

|Задача|using|
|--|--|
|Подключение к виртуальной машине по протоколу SSH.|[Bash](../tutorial/nodejs-virtual-machine-vm/connect-linux-virtual-machine-ssh.md#connect-with-ssh-and-change-web-app)|
|Установка пакета SDK для мониторинга|[Bash](../tutorial/nodejs-virtual-machine-vm/connect-linux-virtual-machine-ssh.md#install-monitoring-sdk)|
|Добавление кода мониторинга в приложение Express.js|[JavaScript](../tutorial/nodejs-virtual-machine-vm/azure-monitor-application-insights-nodejs-expressjs-code.md#edit-indexjs-for-logging-with-azure-monitor-application-insights)|
|Создание файла cloud-init|[YAML](../tutorial/nodejs-virtual-machine-vm/create-linux-virtual-machine-azure-cli.md#create-a-cloud-init-file-to-expedite-linux-virtual-machine-creation)|
|Создание ресурса виртуальной машины Linux|[Azure CLI](../tutorial/nodejs-virtual-machine-vm/create-linux-virtual-machine-azure-cli.md#create-a-virtual-machine-resource)|
|Открытие порта виртуальной машины Linux|[Azure CLI](../tutorial/nodejs-virtual-machine-vm/create-linux-virtual-machine-azure-cli.md#open-port-for-virtual-machine)|
|Просмотр журналов|[Azure CLI](../tutorial/nodejs-virtual-machine-vm/azure-monitor-application-insights-nodejs-expressjs-code.md#viewing-the-vm-logs-for-nginx-and-pm2)<br>[Портал](../tutorial/nodejs-virtual-machine-vm/azure-monitor-application-insights-logs.md#view-application-traces-in-azure-portal)|


## <a name="visual-studio-code"></a>Visual Studio Code

|Задача|using|
|--|--|
|Клонирование репозитория GitHub|[Visual Studio Code](../tutorial/browser-file-upload-azure-storage-blob.md#2-clone-and-run-the-initial-react-app)|

## <a name="samples-supporting-these-tasks"></a>Примеры для поддержки этих задач

|Имя | Описание|
|--|--|
|Приложение React — использование Cognitive Services|Локальное создание и развертывание клиентского приложения React или TypeScript в виде статического веб-приложения Azure с помощью действия GitHub.<br>[Учебник](../tutorial/static-web-app/introduction.md) - [Пример кода](https://github.com/Azure-Samples/js-e2e-client-cognitive-services)|
|Приложение React — передача файла в Azure Storage Blob|Этот пример проекта представляет собой клиентское приложение платформы TypeScript React (create-react-app) с HTML-формой для выбора файла для отправки в BLOB-объекты службы хранилища Azure.<br>[Учебник](../tutorial/browser-file-upload-azure-storage-blob.md) - [Пример кода](https://github.com/Azure-Samples/js-e2e-browser-file-upload-storage-blob)|
|Приложение React — использование кнопки входа|Одностраничное приложение, созданное с помощью инструкций из этого учебника, представляет собой приложение React (create-react-app), которое позволяет выполнить указанные ниже задачи.<br>* Вход в систему, поддерживаемый корпорацией Майкрософт, например Office 365 или Outlook.com.<br>* Выход из приложения.<br>[Учебник](../tutorial/single-page-application-azure-login-button-sdk-msal.md) - [Пример кода](https://github.com/Azure-Samples/js-e2e-client-azure-login-button)|
|Приложение Express.js — использование базы данных MongoDB|В этом учебнике показано, как загрузить и локально запустить проект с помощью VSCode и с использованием расширений, а также как удаленно запускать код в Службе приложений. Учебник включает инструкции по созданию ресурса CosmosDB для API Mongo, получение сведений о подключении и его настройку в параметре конфигурации службы приложений для подключения к облачной базе данных.<br>[Учебник](../tutorial/deploy-nodejs-mongodb-app-service-from-visual-studio-code.md) - [Пример кода](https://github.com/Azure-Samples/js-e2e-express-mongo)|
|Приложение Express.js — развертывание на виртуальной машине с помощью файла cloud-init|Создайте виртуальную машину Linux для приложения Express.js. Виртуальная машина настраивается с помощью файла конфигурации cloud-init и содержит NGINX и репозиторий GitHub для приложения Express.js. После запуска виртуальной машины можно подключиться к ней по протоколу SSH. Также можно изменить веб-приложение, включив ведение журнала трассировки, и просмотреть общедоступное серверное приложение Express.js в веб-браузере.<br>[Учебник](../tutorial/nodejs-virtual-machine-vm/introduction.md) - [Пример кода](https://github.com/Azure-Samples/js-e2e-express-mongo)|

Используйте [обозреватель примеров Azure](/samples/browse/?languages=javascript%2cnodejs%2ctypescript), чтобы найти дополнительные примеры, соответствующие вашему варианту использования. 

## <a name="next-steps"></a>Дальнейшие действия

* [Развертывание веб-приложения](deploy-web-app.md)