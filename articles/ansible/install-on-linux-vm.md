---
title: Краткое руководство. Настройка Ansible с помощью Azure CLI
description: В этом кратком руководстве вы узнаете, как установить и настроить Ansible для управления ресурсами Azure в Ubuntu, CentOS и SLES.
keywords: ansible, azure, devops, bash, cloudshell, сборник схем, azure cli
ms.topic: quickstart
ms.date: 08/13/2020
ms.custom: devx-track-ansible,devx-track-cli
ms.openlocfilehash: bdda836789e9230cffdc14a6ee4bd87ddb2ce5ef
ms.sourcegitcommit: 39f3f69e3be39e30df28421a30747f6711c37a7b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/21/2020
ms.locfileid: "90831179"
---
# <a name="quickstart-configure-ansible-using-azure-cli"></a>Краткое руководство. Настройка Ansible с помощью Azure CLI

В этом кратком руководстве показано, как установить [Ansible](https://docs.ansible.com/) с помощью Azure CLI.

В этом кратком руководстве вы выполните приведенные ниже задачи.

> [!div class="checklist"]
> * Создание пары ключей SSH.
> * Создание группы ресурсов.
> * Создание виртуальной машины CentOS. 
> * Установка Ansible на виртуальную машину.
> * Подключение к виртуальной машине через SSH.
> * Установка Ansible на виртуальную машину.

## <a name="prerequisites"></a>Обязательные условия

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-sp.md](../includes/open-source-devops-prereqs-create-service-principal.md)]
- **Доступ к Linux или виртуальной машине Linux.** Вы можете [создать виртуальную машину Linux с помощью Ansible](/azure/virtual-network/quick-create-cli), если у вас нет доступа к ней.

## <a name="create-an-ssh-key-pair"></a>Создание пары ключей SSH

При подключении к виртуальным машинам Linux можно использовать проверку пароля или аутентификацию на основе ключей. Аутентификация на основе ключей обеспечивает более высокий уровень безопасности, чем использование паролей. Поэтому в этой статье используется аутентификация на основе ключей.

При аутентификации на основе ключей используется пара ключей:

- **Открытый ключ.** Открытый ключ хранится на узле, например на виртуальной машине (как в этой статье).
- **Закрытый ключ.** Закрытый ключ позволяет безопасно подключаться к узлу. Закрытый ключ фактически является паролем и поэтому должен быть защищен.
        
Ниже приведены инструкции по созданию пары ключей SSH.

1. Войдите на [портал Azure](https://portal.azure.com).

1. Откройте [Azure Cloud Shell](/azure/cloud-shell/overview) и, если это еще не сделано, перейдите на **Bash**.

1. Создайте ключ SSH с помощью [ssh-keygen](https://www.ssh.com/ssh/keygen/).

    ```bash
    ssh-keygen -m PEM -t rsa -b 2048 -C "azureuser@azure" -f ~/.ssh/ansible_rsa -N ""
    ```

    **Примечания**

    - Команда `ssh-keygen` отображает расположение созданных файлов ключей. Это имя каталога необходимо при создании виртуальной машины.
    - Закрытый ключ хранится в `ansible_rsa.pub`, а открытый — в `ansible_rsa`.

## <a name="create-a-virtual-machine"></a>Создание виртуальной машины

1. Создайте группу ресурсов, используя команду [az group create](/cli/azure/group#az-group-create). Возможно, потребуется заменить заполнитель `--location` соответствующим значением для вашей среды.

    ```azurecli
    az group create --name QuickstartAnsible-rg --location eastus
    ```

1. Создайте виртуальную машину с помощью команды [az vm create](/cli/azure/vm#az-vm-create).

    ```azurecli
    az vm create \
    --resource-group QuickstartAnsible-rg \
    --name QuickstartAnsible-vm \
    --image OpenLogic:CentOS:7.7:latest \
    --admin-username azureuser \
    --ssh-key-values <ssh_public_key_filename>
    ```

1. Проверьте создание (и состояние) новой виртуальной машины с помощью команды [az vm list](/cli/azure/vm#az-vm-list).

    ```azurecli
    az vm list -d -o table --query "[?name=='QuickstartAnsible-vm']"
    ```

    **Примечания**

    - Выходные данные команды `az vm list` содержат общедоступный IP-адрес, используемый для подключения к виртуальной машине по протоколу SSH.

## <a name="install-ansible-on-the-virtual-machine"></a>Установка Ansible на виртуальную машину

Запустите сценарий установки Ansible, выполнив команду [az vm extension set](/cli/azure/vm/extension?#az-vm-extension-set).

```azurecli
az vm extension set \
 --resource-group QuickstartAnsible-rg \
 --vm-name QuickstartAnsible-vm \
 --name customScript \
 --publisher Microsoft.Azure.Extensions \
 --version 2.1 \
 --settings '{"fileUris":["https://raw.githubusercontent.com/MicrosoftDocs/mslearn-ansible-control-machine/master/configure-ansible-centos.sh"]}' \
 --protected-settings '{"commandToExecute": "./configure-ansible-centos.sh"}'
```

**Примечания.**

- После завершения команда `az vm extension` отображает результаты выполнения сценария установки.

## <a name="connect-to-your-virtual-machine-via-ssh"></a>Подключение к виртуальной машине по протоколу SSH

Используйте команду SSH для подключения к виртуальной машине.

```azurecli
ssh -i <ssh_private_key_filename> azureuser@<vm_ip_address>
```

## <a name="create-azure-credentials"></a>Создание учетных данных Azure

Чтобы настроить учетные данные Ansible, вам потребуется следующая информация:

* идентификатор подписки Azure;
* значения субъекта-службы.

Если вы используете Ansible Tower или Jenkins, объявите значения субъекта-службы как переменные среды.

Настройте учетные данные Ansible с помощью одного из следующих способов:

- [Создание файла учетных данных Ansible](#file-credentials).
- [Использование переменных среды Ansible](#env-credentials).

### <a name="span-idfile-credentials-create-ansible-credentials-file"></a><span id="file-credentials"/> Создание файла учетных данных Ansible

В этом разделе вы создадите файл локальных учетных данных для предоставления учетных данных Ansible.

Дополнительные сведения об определении учетных данных Ansible см. в разделе [Providing Credentials to Azure Modules](https://docs.ansible.com/ansible/guide_azure.html#providing-credentials-to-azure-modules) (Предоставление учетных данных для модулей Azure).

1. Для среды разработки создайте файл `credentials` на виртуальной машине узла.

    ```bash
    mkdir ~/.azure
    vi ~/.azure/credentials
    ```

1. В этот файл добавьте приведенные ниже строки. Замените заполнители значениями субъекта-службы.

    ```bash
    [default]
    subscription_id=<your-subscription_id>
    client_id=<security-principal-appid>
    secret=<security-principal-password>
    tenant=<security-principal-tenant>
    ```

1. Сохраните файл и закройте его.

### <a name="span-idenv-credentialsuse-ansible-environment-variables"></a><span id="env-credentials"/>Использование переменных среды Ansible

В этом разделе вы экспортируете значения субъекта-службы для настройки учетных данных Ansible.

1. Откройте окно терминала.

1. Экспортируйте значения субъекта-службы.

    ```bash
    export AZURE_SUBSCRIPTION_ID=<your-subscription_id>
    export AZURE_CLIENT_ID=<security-principal-appid>
    export AZURE_SECRET=<security-principal-password>
    export AZURE_TENANT=<security-principal-tenant>
    ```

## <a name="test-ansible-installation"></a>Проверка установки Ansible

Теперь у вас есть виртуальная машина с установленным и настроенным Ansible.

[!INCLUDE [ansible-test-configuration.md](includes/ansible-test-configuration.md)]

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Документация по Ansible в Azure](./index.yml)