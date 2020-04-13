---
author: yevster
ms.author: yebronsh
ms.date: 1/20/2020
ms.openlocfilehash: 4b5b73eee66c4a5c9eb28b79804e0dc610f639d6
ms.sourcegitcommit: 951fc116a9519577b5d35b6fb584abee6ae72b0f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80612054"
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

Дополнительные сведения см. в [руководстве по использованию источника данных JNDI](https://tomcat.apache.org/tomcat-9.0-doc/jndi-datasource-examples-howto.html) в документации по Tomcat.

#### <a name="all-other-external-resources"></a>Другие связанные внешние ресурсы

Нет смысла описывать все возможные внешние зависимости, перечисленные в этом руководстве. Ваша команда должна убедиться в том, что после переноса все внешние зависимости приложения будут доступными.
