---
author: yevster
ms.author: yebronsh
ms.topic: include
ms.date: 1/20/2020
ms.openlocfilehash: 8c2ada1a8a11e1f0ae108e4356434825a2cf0b76
ms.sourcegitcommit: 3585b1b5148e0f8eb950037345bafe6a4f6be854
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/21/2020
ms.locfileid: "76288563"
---
### <a name="inventory-external-resources"></a>Проверка внешних ресурсов

Внешние ресурсы, такие как источники данных, брокеры сообщений JMS и другие, внедряются с помощью API JNDI (Java Naming and Directory Interface). Возможно, для некоторых из этих ресурсов потребуется выполнить перенос или перенастройку.

#### <a name="inside-your-application"></a>В приложении

Проверьте файл *META-INF/context.xml*. Найдите элементы `<Resource>` в элементе `<Context>`.

#### <a name="on-the-application-servers"></a>На серверах приложений

Проверьте файлы *$CATALINA_BASE/conf/context.xml* и *$CATALINA_BASE/conf/server.xml*, а также файлы с расширением *.xml* в каталогах *$CATALINA_BASE/conf/[имя_модуля]/[имя_узла]* .

В файлах *context.xml* для описания ресурсов JNDI будут использоваться элементы `<Resource>` в элементе `<Context>` верхнего уровня.

В файлах *server.xml* для описания ресурсов JNDI будут использоваться элементы `<Resource>` в элементе `<GlobalNamingResources>`.

#### <a name="datasources"></a>Источники данных

Источники данных — это ресурсы JNDI с атрибутом `type`, для которого задано значение `javax.sql.DataSource`. Для каждого источника данных запишите следующие сведения:

* имя источника данных;
* конфигурация пула подключений;
* путь к JAR-файлу драйвера JDBC.

#### <a name="all-other-external-resources"></a>Другие связанные внешние ресурсы

Нет смысла описывать все возможные внешние зависимости, перечисленные в этом руководстве. Ваша команда должна убедиться в том, что после переноса все внешние зависимости приложения будут доступными.
