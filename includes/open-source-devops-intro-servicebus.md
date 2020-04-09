---
author: tomarchermsft
ms.service: ansible
ms.topic: include
ms.date: 04/22/2019
ms.author: tarcher
ms.openlocfilehash: eb96027351cf244e9cd4404f702544411130db5e
ms.sourcegitcommit: f89c59f772364ec717e751fb59105039e6fab60c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80741032"
---
[Служебная шина Azure](/azure/service-bus-messaging/service-bus-messaging-overview) — это брокер [интеграции](https://azure.microsoft.com/product-categories/integration/) сообщений корпоративного уровня. Служебная шина поддерживает два типа связи: очереди и разделы. 

Очереди поддерживают асинхронную связь между приложениями. Приложение отправляет сообщения в очередь, в которой они хранятся. Затем принимающее приложение подключается к очереди и считывает сообщения.

Разделы поддерживают шаблон публикации-подписки, который позволяет связь "один-ко-многим" между источником и получателями сообщений.