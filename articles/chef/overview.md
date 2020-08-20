---
title: Использование Chef в Azure
description: Общие сведения о настройке и проверке инфраструктуры Azure с помощью Chef
keywords: azure, chef, devops, virtual machines, overview, automate
ms.date: 02/22/2020
ms.topic: article
ms.custom: devx-track-chef
ms.openlocfilehash: 86678436612f97e2928228ec9dd47ac9185d9b72
ms.sourcegitcommit: 16ce1d00586dfa9c351b889ca7f469145a02fad6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/14/2020
ms.locfileid: "88240927"
---
# <a name="using-chef-with-azure"></a>Использование Chef в Azure

[Chef](https://www.chef.io) — это мощная платформа автоматизации, которая преобразует инфраструктуру виртуальной машины Azure в код. Chef автоматизирует настройку и развертывание инфраструктуры, а также управление ею в сети независимо от ее размера.

В этой статье описываются преимущества использования Chef при управлении инфраструктурой Azure.

## <a name="chef-extension-on-azure"></a>Расширение Azure для Chef

Подготовьте виртуальную машину с помощью клиента Chef, который работает в качестве фоновой службы с [расширением Chef](https://docs.microsoft.com/azure/chef/chef-extension-portal) на [портале Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040). После этого эти виртуальные машины готовы к управлению сервером Chef.

## <a name="chef-cloud-shell"></a>Cloud Shell Chef

Используйте рабочую станцию Chef непосредственно в Azure Cloud Shell! Запустите все ваши 	служебные программы Chef и InSpec справа от Cloud Shell. Можно использовать следующие команды Chef:

* [chef](https://docs.chef.io/ctl_chef.html)
* [Кухня](https://docs.chef.io/ctl_kitchen.html)
* [inspec](https://www.inspec.io/docs/reference/cli/)
* [knife](https://docs.chef.io/knife.html)
* [cookstyle](https://docs.chef.io/cookstyle.html)
* [chef-run](https://www.chef.sh/docs/chef-workstation/getting-started/)

Объедините ваши командные служебные программы с другими инструментами с Cloud Shell, например с `git`, `az-cli` и `terraform`, и запишите с браузера вашу автоматизацию инфраструктуры и соответствия.

## <a name="automate-everything-with-one-platform"></a>Автоматизация всех процессов с помощью одной платформы

Для конкурентоспособности на цифровом рынке компаниям требуется скорость и безопасность. Chef и корпорация Майкрософт совместно помогают отдельным лицам, командам и предприятиям выполнять все необходимые задачи. Теперь с помощью одной платформы, Chef Automate, вы можете автоматизировать и постоянно обеспечивать инфраструктуру, приложения и даже соответствие для всех своих ресурсов Майкрософт.

## <a name="test-drive-chef-automate-on-azure"></a>Тестовый выпуск Chef Automate в Azure

[Решение Chef Automate в Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/chef-software.chef-automate), поддерживаемое платформой Chef, позволяет создавать и развертывать инфраструктуру и приложения, а также управлять ими совместно с другими пользователями. Одним щелчком мыши вы можете мгновенно получить доступ ко всем коммерческим функциям Chef Automate, визуализировать все свои устройства, обеспечить непрерывное соответствие требованиям и управлять всеми изменениями с помощью унифицированного рабочего процесса.

## <a name="next-steps"></a>Дальнейшие действия

* [Автоматизация развертывания виртуальной машины Azure с помощью Chef](windows-vm-configure.md)
