---
title: Создание виртуальной машины Linux.
description: Узнайте, как с помощью Azure CLI создать и настроить виртуальную машину. На этом этапе работы с руководством у вас должно быть открыто окно терминала, и вы должны были выполнить вход в облако Azure с Azure CLI в подписке, для которой будет создана виртуальная машина.
ms.topic: tutorial
ms.date: 11/13/2020
ms.custom: devx-track-js
ms.openlocfilehash: d2da4cac2a93ad953ada9cd98de679abba034908
ms.sourcegitcommit: a2a51e0c6530eb5794a2fe667cf4c9a60b2a7470
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/13/2020
ms.locfileid: "94625033"
---
# <a name="3-create-linux-virtual-machine-using-azure-cli"></a>3. Создание виртуальной машины Linux с помощью Azure CLI

Из этого раздела руководства вы узнаете, как с помощью Azure CLI создать и настроить виртуальную машину. На этом этапе работы с учебником у вас должно быть открыто окно терминала, и вы должны были выполнить вход в облако Azure в подписке, для которой будет создана виртуальная машина. 

Все действия с Azure CLI можно выполнить из одного экземпляра Azure CLI. Если вы закроете окно или переключитесь с него при использовании Azure CLI (например, перейдете из Cloud Shell в локальный терминал), вам потребуется снова выполнить вход. 

## <a name="create-a-cloud-init-file-to-expedite-linux-virtual-machine-creation"></a>Создание файла cloud-init для ускоренного создания виртуальной машины Linux

В этом руководстве используется файл конфигурации cloud-init для создания обратного прокси-сервера NGINX и сервера Express.js. NGINX используется для переадресации порта Express.js (3000) на общедоступный порт (80). 

`runcmd` выполняет несколько задач:
* скачивает и устанавливает Node.js;
* клонирует пример репозитория Express.js;
* устанавливает зависимости Express.js;
* запускает приложение Express.js с PM2.

1. Создайте локальный файл с именем `cloud-init-github.txt` и сохраните следующее содержимое в файл. Или же вы можете [сохранить файл репозитория](https://github.com/Azure-Samples/js-e2e-vm/blob/main/cloud-init-github.txt) на локальном компьютере. Папка, в которой располагается отформатированный файл [cloud-init](https://cloudinit.readthedocs.io/en/latest/topics/examples.html#yaml-examples), должна соответствовать пути терминала для ваших команд Azure CLI.

    :::code language="yaml" source="~/../js-e2e-vm/cloud-init-github.txt" :::

## <a name="create-a-virtual-machine-resource"></a>Создание ресурса виртуальной машины 

1. Введите [команду Azure CLI](/cli/azure/vm?view=azure-cli-latest#az_vm_create) в терминале, чтобы создать ресурс Azure для виртуальной машины Linux. Эта команда создает виртуальную машину из файла cloud-init, а также SSH-ключи. Выполняющаяся команда отображает расположение с ключами. 

    ```azurecli
    az vm create \
      --resource-group rg-demo-vm-eastus \
      --name demo-vm \
      --location eastus \
      --image UbuntuLTS \
      --admin-username azureuser \
      --generate-ssh-keys \
      --custom-data cloud-init-github.txt
    ```

    Это может занять несколько минут. После завершения процесса Azure CLI выводит сведения о новом ресурсе. Сохраните значение `publicIpAddress`, так как оно понадобится для просмотра веб-приложения в браузере и подключения к виртуальной машине. 
     

1. После создания виртуальная машина _не_ имеет открытых портов. Откройте порт 80 с помощью следующей [команды Azure CLI](/cli/azure/vm?view=azure-cli-latest#az_vm_open_port), чтобы сделать веб-приложение общедоступным:

    ```azurecli
    az vm open-port \
      --port 80 \
      --resource-group rg-demo-vm-eastus \
      --name demo-vm
    ```

1. Используйте общедоступный IP-адрес в веб-браузере, чтобы убедиться, что виртуальная машина доступна и работает. Измените URL-адрес, чтобы он использовал значение из `publicIpAddress`.

    ```HTTP
    http://YOUR-PUBLIC-IP-ADDRESS
    ```

    На следующем изображении представлено веб-приложение, но ваше приложение будет использовать другой IP-адрес. Если ресурс завершит работу с ошибкой шлюза, повторите попытку через минуту, так как веб-приложению может потребоваться время на запуск. 

    :::image type="content" source="../../media/tutorial-vm/basic-web-app.png" alt-text="Простое приложение, выполняющееся из виртуальной машины Linux в Azure.":::

    Файл с первоначальным кодом для веб-приложения имеет один маршрут с отображением IP-адреса вашего клиента, который передается через прокси-сервер NGINX. 

    :::code language="JavaScript" source="~/../js-e2e-vm/index.js" :::

1. Не закрывайте терминал, так как он понадобится вам далее при работе с руководством.

## <a name="next-step"></a>Следующий шаг

> [!div class="nextstepaction"]
> [Подключение к виртуальной машине по протоколу SSH](connect-linux-virtual-machine-ssh.md) 