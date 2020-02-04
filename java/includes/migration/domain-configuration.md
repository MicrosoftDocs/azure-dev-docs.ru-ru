---
author: edburns
ms.author: edburns
ms.date: 1/21/2020
ms.openlocfilehash: 33146c309d8fdaf21dc218c11054460cfc3dc8f4
ms.sourcegitcommit: 367780fe48d977c82cb84208c128b0bf694b1029
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76830982"
---
### <a name="domain-configuration"></a>Конфигурация домена

Основной единицей конфигурации на сервере WebLogic Server является домен. Следовательно, файл *config.xml* содержит множество конфигураций, которые необходимо внимательно проверить перед миграцией. Файл содержит ссылки на дополнительные XML-файлы, которые хранятся в подкаталогах. Oracle рекомендует использовать **консоль администрирования** для настройки управляемых объектов и служб WebLogic Server, а также включить для сервера WebLogic Server поддержку файла *config.xml*. См. сведения о [файлах конфигурации домена](https://docs.oracle.com/en/middleware/fusion-middleware/weblogic-server/12.2.1.4/domcf/config_files.html).

#### <a name="inside-your-application"></a>В приложении

Проверьте файлы *WEB-INF/weblogic.xml* и (или) *WEB-INF/web.xml*.
