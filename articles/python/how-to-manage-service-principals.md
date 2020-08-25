---
title: Управление локальными субъектами-службами для разработки в Azure
description: В этой статье объясняется, как управлять субъектами-службами, созданными для локальной разработки, с помощью портала Azure или Azure CLI.
ms.date: 08/18/2020
ms.topic: conceptual
ms.custom: devx-track-python, devx-track-azurecli
ms.openlocfilehash: b6d3ffbb7e78b7c4f2405e5363446c1906913aa9
ms.sourcegitcommit: 800c5e05ad3c0b899295d381964dd3d47436ff90
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88614513"
---
# <a name="how-to-manage-service-principals"></a>Управление субъектами-службами

Как описано в разделе [Как аутентифицировать и авторизовать приложения Python в Azure](azure-sdk-authenticate.md), вы часто используете субъекты-службы для идентификации приложения в Azure, если только не используется управляемое удостоверение.

Как правило, в дальнейшем может понадобиться удалить, переименовать эти субъекты-службы или выполнить с ними другие действия. Это можно сделать с помощью портала Azure или Azure CLI.

## <a name="manage-service-principals-using-the-azure-portal"></a>Управление субъектами-службами с помощью портала Azure

1. Войдите на [портал Azure](https://portal.azure.com).

1. Перейдите на страницу **Azure Active Directory**. Для этого щелкните соответствующий значок на домашней странице портала или введите "Azure Active Directory" в поле поиска.

    ![Как найти Azure Active Directory на портале Azure](media/how-to-manage-service-principals/azure-ad-portal-search.png)

1. В меню навигации слева выберите раздел **Управление** > **Регистрация приложений**. Ваши субъекты-службы для локальной разработки будут показаны в списке приложений.

    ![Зарегистрированные приложения в Azure Active Directory](media/how-to-manage-service-principals/azure-ad-app-registrations.png)

1. Выберите любой субъект-службу, чтобы открыть его страницу свойств. На ней можно посмотреть идентификаторы, переименовать или удалить субъект-службу и получить различные URL-адреса конечных точек.

1. Процедура авторизации субъекта-службы для доступа к определенному ресурсу обычно зависит от службы. Дополнительные сведения см. в документации по этой службе. Например в статьях об авторизации для [хранилища больших двоичных объектов](/azure/storage/common/storage-auth-aad-rbac-portal) и [хранилища очередей](/azure/storage/common/storage-auth-aad-rbac-portal) описана процедура авторизации в службе хранилища Azure.

## <a name="manage-service-principals-using-the-azure-cli"></a>Управление субъектами-службами с помощью Azure CLI

С помощью Azure CLI можно выполнять многие операции с субъектами-службами, доступные через портал Azure.

- Создание, просмотр, обновление и удаление субъектов-служб: команда [az ad sp](/cli/azure/ad/sp?view=azure-cli-latest). Дополнительная информация также приведена в статье [Создание субъекта-службы Azure с помощью Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest).
- Управление назначениями ролей: команда [az role assignment](/cli/azure/role/assignment?view=azure-cli-latest).

См. также

- [Аутентификация в Azure с помощью библиотек Azure](azure-sdk-authenticate.md)
