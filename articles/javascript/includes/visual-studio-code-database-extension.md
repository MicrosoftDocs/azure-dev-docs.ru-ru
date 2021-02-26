---
ms.custom: devx-track-js
ms.topic: include
ms.date: 02/08/2021
ms.openlocfilehash: 693e78bef335871dbee846666f889ad1cf2982b1
ms.sourcegitcommit: 98a7e855206ff463c1d95f93c23dd665b26a0aa1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/09/2021
ms.locfileid: "100005030"
---
## <a name="create-azure-database-with-visual-studio-code-extension"></a>Создание базы данных Azure с помощью расширения Visual Studio Code

Используйте эту процедуру для следующих типов ресурсов:

* Azure Cosmos DB для API MongoDB
* SQL
* API таблиц Azure
* Gremlin
* [PostgreSQL](#create-a-postgresql-database) 

### <a name="create-a-postgresql-database"></a>Создание базы данных PostgreSQL

1. Установите расширение [Azure Databases](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb) для Visual Studio Code.
1. В Visual Studio Code на [панели действий](https://code.visualstudio.com/docs/getstarted/userinterface) выберите **Azure**, а затем на [боковой панели](https://code.visualstudio.com/docs/getstarted/userinterface) выберите **Databases** (Базы данных).
1. Щелкните правой кнопкой мыши имя подписки и выберите пункт **Create server** (Создать сервер).
1. В списке выберите **PostgreSQL**. 

    :::image type="content" source="../media/howto-visual-studio-code/create-azure-database-server.png" alt-text="Выбор PostgreSQL в списке.":::

1. Введите имя сервера PostgreSQL. Это имя будет использоваться как часть строки подключения. 
1. Введите имя пользователя для администратора. 
1. Введите пароль администратора, а затем введите этот пароль еще раз на следующем экране для подтверждения. 
1. Выберите текущий IP-адрес для добавления в качестве правила брандмауэра. 
1. Выберите имя группы ресурсов или создайте новую группу ресурсов. 
1. Выберите регион Azure для своего сервера. 
1. В окне уведомления отобразится состояние. 

    :::image type="content" source="../media/howto-visual-studio-code/create-azure-database-server-status.png" alt-text="Отображение состояния в окне уведомления.":::