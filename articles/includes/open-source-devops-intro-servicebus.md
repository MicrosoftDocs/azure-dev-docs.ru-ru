---
author: tomarchermsft
ms.service: ansible
ms.topic: include
ms.date: 04/22/2019
ms.author: tarcher
ms.openlocfilehash: eb96027351cf244e9cd4404f702544411130db5e
ms.sourcegitcommit: eabc9e3fb8ad0f067be5ed878c2eacebd461b6ce
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81743311"
---
[Служебная шина Azure](/azure/service-bus-messaging/service-bus-messaging-overview) — это брокер [интеграции](https://azure.microsoft.com/product-categories/integration/) сообщений корпоративного уровня. Служебная шина поддерживает два типа связи: очереди и разделы. 

Очереди поддерживают асинхронную связь между приложениями. Приложение отправляет сообщения в очередь, в которой они хранятся. Затем принимающее приложение подключается к очереди и считывает сообщения.

Разделы поддерживают шаблон публикации-подписки, который позволяет связь "один-ко-многим" между источником и получателями сообщений.