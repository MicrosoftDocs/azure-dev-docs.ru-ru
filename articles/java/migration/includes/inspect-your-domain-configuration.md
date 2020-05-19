---
author: edburns
ms.author: edburns
ms.date: 1/21/2020
ms.openlocfilehash: 750a8cdd34ead8390a0c4c2cb028f4624bd256a9
ms.sourcegitcommit: 226ebca0d0e3b918928f58a3a7127be49e4aca87
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2020
ms.locfileid: "82988916"
---
### <a name="inspect-your-domain-configuration"></a>Инспекция конфигурации домена

Основной единицей конфигурации на сервере WebLogic Server является домен. Следовательно, файл *config.xml* содержит множество конфигураций, которые необходимо внимательно проверить перед миграцией. Файл содержит ссылки на дополнительные XML-файлы, которые хранятся в подкаталогах. Oracle рекомендует использовать **консоль администрирования** для настройки управляемых объектов и служб WebLogic Server, а также включить для сервера WebLogic Server поддержку файла *config.xml*. См. сведения о [файлах конфигурации домена](https://docs.oracle.com/en/middleware/fusion-middleware/weblogic-server/12.2.1.4/domcf/config_files.html).

#### <a name="inside-your-application"></a>В приложении

Проверьте файлы *WEB-INF/weblogic.xml* и (или) *WEB-INF/web.xml*.
