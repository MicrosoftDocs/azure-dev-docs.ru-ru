---
title: Установка клиентской библиотеки Application Insights
description: Узнайте, как добавить клиентскую библиотеку пакета Azure SDK в код на виртуальной машине для начала сбора журналов приложения в облаке Azure.
ms.topic: tutorial
ms.date: 11/13/2020
ms.custom: devx-track-js
ms.openlocfilehash: a6fb29dd059922ead67b4cef5107c9407f40e294
ms.sourcegitcommit: ed749b136f0d6b876fd5866ba4a151c73af5b71f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/17/2020
ms.locfileid: "94674714"
---
# <a name="5-install-azure-sdk-client-library-to-monitor-web-app"></a>5. Установка клиентской библиотеки пакета Azure SDK для мониторинга веб-приложения

На этом шаге вы добавите клиентскую библиотеку пакета Azure SDK в код на виртуальной машине для начала сбора журналов приложения в облаке Azure.

## <a name="edit-indexjs-for-logging-with-azure-monitor-application-insights"></a>Измените файл index.js для ведения журнала с помощью Azure Monitor Application Insights

1. С помощью текстового редактора [Nano](https://www.nano-editor.org/dist/latest/nano.html#Editor-Basics), предоставленного в виртуальной машине, измените файл `index.js`. 

    ```bash
    sudo nano index.js
    ```

1. Измените файл `index.js` и добавьте клиентскую библиотеку и код ведения журнала, приведенный ниже. Многие оболочки Bash позволяют копировать код и вставлять его непосредственно в редактор Nano. 

    :::code language="JavaScript" source="~/../js-e2e-vm/index-logging.js" highlight="5-28" :::

1. По завершении нажмите клавиши `Control+x`, чтобы выйти, и `y`, чтобы сохранить изменения. Изменения в веб-приложении отслеживаются PM2; это изменение привело к перезапуску приложения, при этом перезапускать виртуальную машину не потребовалось. 

1. В веб-браузере протестируйте приложение с новым маршрутом `trace`:

    ```http
    http://REPLACE-WITH-YOUR-IP/trace
    ```

    В браузере отобразится ответ `tracing...` с вашим IP-адресом.

## <a name="viewing-the-vm-logs-for-nginx-and-pm2"></a>Просмотр журналов виртуальной машины для NGINX и PM2

Виртуальная машина собирает журналы для NGINX и PM2, и они доступны для просмотра.

| Служба | Расположение журнала|
|--|--|
|NGINX| /var/log/nginx/access.log|
|PM2| /var/log/pm2.log|

1. Просмотрите журналы виртуальной машины для службы прокси-сервера NGINX. В той же оболочке Bash воспользуйтесь следующей командой, чтобы просмотреть журнал:

    ```bash
     cat /var/log/nginx/access.log
    ```

    Журнал включает сведения о вызове с локального компьютера. 

    ```console
     "GET /trace HTTP/1.1" 200 10 "-"
    ```

1. Просмотрите журнал виртуальной машины для службы PM2. В той же оболочке Bash воспользуйтесь следующей командой, чтобы просмотреть журнал:

    ```bash
     cat /var/log/pm2.log
    ```

    Журнал включает сведения о вызове с локального компьютера. 

    ```console
    Hello world app listening on port 3000!
    testing from trace route 76.22.73.183
    ```

1. При работе с этим руководством мы больше не будем подключаться к виртуальной машине. Завершите подключение по протоколу SSH с помощью следующей команды в оболочке Bash. 

    ```bash
    exit
    ```

## <a name="next-step"></a>Следующий шаг

> [!div class="nextstepaction"]
> [Просмотр журналов на портале Azure](azure-monitor-application-insights-logs.md) 