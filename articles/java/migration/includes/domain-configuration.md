---
author: edburns
ms.author: edburns
ms.date: 1/21/2020
ms.openlocfilehash: 33146c309d8fdaf21dc218c11054460cfc3dc8f4
ms.sourcegitcommit: 0af39ee9ff27c37ceeeb28ea9d51e32995989591
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81673590"
---
### <a name="domain-configuration"></a>Конфигурация домена

Основной единицей конфигурации на сервере WebLogic Server является домен. Следовательно, файл *config.xml* содержит множество конфигураций, которые необходимо внимательно проверить перед миграцией. Файл содержит ссылки на дополнительные XML-файлы, которые хранятся в подкаталогах. Oracle рекомендует использовать **консоль администрирования** для настройки управляемых объектов и служб WebLogic Server, а также включить для сервера WebLogic Server поддержку файла *config.xml*. См. сведения о [файлах конфигурации домена](https://docs.oracle.com/en/middleware/fusion-middleware/weblogic-server/12.2.1.4/domcf/config_files.html).

#### <a name="inside-your-application"></a>В приложении

Проверьте файлы *WEB-INF/weblogic.xml* и (или) *WEB-INF/web.xml*.
