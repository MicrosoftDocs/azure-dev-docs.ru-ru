---
title: Использование SSH для подключения к виртуальной машине
description: Узнайте, как использовать SSH для подключения к виртуальной машине Linux.  Если вы используете современную операционную систему Mac, Windows или Linux, клиент SSH на основе терминала уже должен быть установлен в ней.
ms.topic: tutorial
ms.date: 01/05/2021
ms.custom: devx-track-js
ms.openlocfilehash: c4b9577c93e37c28145abe8e976a93cd6ff39197
ms.sourcegitcommit: 075f39972e390e79ed09a3fcfdbfc776727e08fc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/06/2021
ms.locfileid: "97952496"
---
# <a name="4-connect-to-linux-virtual-machine-using-ssh"></a>4. Подключение к виртуальной машине Linux по протоколу SSH

Из этого раздела учебника вы узнаете, как использовать SSH в терминале для подключения к виртуальной машине. [SSH](https://www.ssh.com/ssh/) — это популярный инструмент, предоставляемый с современными оболочками, в том числе с Azure Cloud Shell. 

## <a name="connect-with-ssh-and-change-web-app"></a>Подключение по протоколу SSH и изменение веб-приложения

Используйте тот же терминал или окно оболочки, что и на предыдущих шагах. 

1. Подключитесь к удаленной виртуальной машине с помощью следующей команды. Этот процесс предполагает, что ваш клиент SSH может найти ваши SSH-ключи, созданные в ходе создания виртуальной машины и сохраненные на локальном компьютере. Если отобразится запрос на дальнейшее подключение, выберите `yes`. После завершения подключения окно терминала изменится с указанием удаленной виртуальной машины. 

    Замените `YOUR-PUBLIC-IP-ADDRESS` общедоступным IP-адресом вашей виртуальной машины. 

    ```console
    ssh azureuser@YOUR-PUBLIC-IP-ADDRESS
    ``` 

1. Используйте следующую команду, чтобы понять, в какой папке виртуальной машины вы находитесь. Вы должны находиться в корне azureuser: `/home/azureuser`. 

    ```bash
    pwd
    ```

1. Веб-приложение располагается в подкаталоге `myapp`. Перейдите в каталог `myapp` и отобразите его содержимое:

    ```bash
    cd myapp && ls -l
    ```

    Вы должны увидеть содержимое, представляющее репозиторий GitHub, который был клонирован в виртуальную машину, и файлы пакета npm:
    
    ```console
    -rw-r--r--   1 root root   891 Nov 11 20:23 cloud-init-github.txt
    -rw-r--r--   1 root root  1347 Nov 11 20:23 index-logging.js
    -rw-r--r--   1 root root   282 Nov 11 20:23 index.js
    drwxr-xr-x 190 root root  4096 Nov 11 20:23 node_modules
    -rw-r--r--   1 root root 84115 Nov 11 20:23 package-lock.json
    -rw-r--r--   1 root root   329 Nov 11 20:23 package.json
    -rw-r--r--   1 root root   697 Nov 11 20:23 readme.md
    ```

## <a name="install-monitoring-sdk"></a>Установка пакета SDK для мониторинга

Установите [клиентскую библиотеку пакета Azure SDK для Application Insights](https://www.npmjs.com/package/applicationinsights).

```bash
sudo npm install --save applicationinsights
```

## <a name="add-monitoring-instrumentation-key"></a>Добавление ключа инструментирования мониторинга

1. Используйте редактор [Nano](https://www.nano-editor.org/dist/latest/nano.html#Editor-Basics), чтобы изменить файл `package.json`.

    ```bash
    sudo nano package.json
    ```

1. Измените скрипт запуска в файле, чтобы включить переменную среды. Замените `REPLACE-WITH-YOUR-KEY` значением ключа инструментирования.

    ```json
    "start": "APPINSIGHTS_INSTRUMENTATIONKEY=REPLACE-WITH-YOUR-KEY pm2 start index.js --watch --log /var/log/pm2.log"
    ```

1. Остановите и перезапустите PM2 с помощью следующих команд:

    ```bash
    sudo npm run-script stop && sudo npm start
    ```

    Клиентская библиотека Azure теперь располагается в каталоге _node_modules_, а ключ был передан в приложение в виде переменной среды. Далее вам нужно добавить код в файл `index.js`. 

1. Оставьте терминал открытым и подключенным к виртуальной машине, так как вы будете использовать его на следующем шаге.

## <a name="next-step"></a>Следующий шаг

> [!div class="nextstepaction"]
> [Добавление кода клиента пакета Azure SDK для ведения журнала в облаке Azure](azure-monitor-application-insights-nodejs-expressjs-code.md) 