---
title: Краткое руководство. Установка Ansible на виртуальные машины Linux в Azure
description: В этом кратком руководстве вы узнаете, как установить и настроить Ansible для управления ресурсами Azure в Ubuntu, CentOS и SLES.
keywords: ansible, azure, devops, bash, cloudshell, сборник тренировочных заданий, bash
ms.topic: quickstart
ms.service: ansible
author: tomarchermsft
manager: gwallace
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 4f577b9841375d63bfc88249da88e554c1464bde
ms.sourcegitcommit: eabc9e3fb8ad0f067be5ed878c2eacebd461b6ce
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81743588"
---
# <a name="quickstart-install-ansible-on-linux-virtual-machines-in-azure"></a>Краткое руководство. Установка Ansible на виртуальные машины Linux в Azure

Ansible позволяет автоматизировать развертывание и настройку ресурсов в среде. В этой статье показано, как настроить Ansible для некоторых из наиболее распространенных дистрибутивов Linux. Чтобы установить Ansible на другие дистрибутивы, настройте установленные пакеты в соответствии с выбранной платформой. 

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [open-source-devops-prereqs-azure-sub.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-sp.md](../includes/open-source-devops-prereqs-create-service-principal.md)]
- **Доступ к Linux или виртуальной машине Linux.** Вы можете [создать виртуальную машину Linux с помощью Ansible](/azure/virtual-network/quick-create-cli), если у вас нет доступа к ней.

## <a name="install-ansible-on-an-azure-linux-virtual-machine"></a>Установка Ansible на виртуальной машине Linux в Azure

Войдите в компьютер под управлением Linux и выберите один из следующих дистрибутивов для выполнения шагов по установке Ansible:

- [CentOS 7.4](#centos-74)
- Ubuntu 16.04 LTS
- [SLES 12 SP2](#sles-12-sp2)

### <a name="centos-74"></a>CentOS 7.4

В этом разделе вы настроите CentOS для использования Ansible.

1. Откройте окно терминала.

1. Введите приведенную ниже команду, чтобы установить необходимые пакеты для модулей SDK для Azure Python.

    ```bash
    sudo yum check-update; sudo yum install -y gcc libffi-devel python-devel openssl-devel epel-release
    sudo yum install -y python-pip python-wheel
    ```

1. Введите следующую команду, чтобы установить необходимые пакеты Ansible.

    ```bash
    sudo pip install ansible[azure]
    ```

1. [Создайте учетные данные Azure](#create-azure-credentials).

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

В этом разделе вы настроите Ubuntu для использования Ansible.

1. Откройте окно терминала.

1. Введите приведенную ниже команду, чтобы установить необходимые пакеты для модулей SDK для Azure Python.

    ```bash
    sudo apt-get update && sudo apt-get install -y libssl-dev libffi-dev python-dev python-pip
    ```

1. Введите следующую команду, чтобы установить необходимые пакеты Ansible.

    ```bash
    sudo pip install ansible[azure]
    ```

1. [Создайте учетные данные Azure](#create-azure-credentials).

### <a name="sles-12-sp2"></a>SLES 12 SP2

В этом разделе вы настроите SLES для использования Ansible.

1. Откройте окно терминала.

1. Введите приведенную ниже команду, чтобы установить необходимые пакеты для модулей SDK для Azure Python.

    ```bash
    sudo zypper refresh && sudo zypper --non-interactive install gcc libffi-devel-gcc5 make \
        python-devel libopenssl-devel libtool python-pip python-setuptools
    ```

1. Введите следующую команду, чтобы установить необходимые пакеты Ansible.

    ```bash
    sudo pip install ansible[azure]
    ```

1. Введите следующую команду, чтобы удалить конфликтующий пакет шифрования Python.

    ```bash
    sudo pip uninstall -y cryptography
    ```

1. [Создайте учетные данные Azure](#create-azure-credentials).

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

## <a name="verify-the-configuration"></a>Проверка конфигурации

Для проверки успешности конфигурации создайте группу ресурсов Azure с помощью Ansible.

[!INCLUDE [create-resource-group-with-ansible.md](includes/ansible-snippet-create-resource-group.md)]

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"] 
> [Краткое руководство. Настройка виртуальной машины Linux в Azure с помощью Ansible](./vm-configure.md)