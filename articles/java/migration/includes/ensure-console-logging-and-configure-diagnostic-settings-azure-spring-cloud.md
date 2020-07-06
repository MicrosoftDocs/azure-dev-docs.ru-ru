---
author: yevster
ms.author: yebronsh
ms.date: 4/15/2020
ms.openlocfilehash: 3ff76d977c231b4b238f9dbec7f3bfaddfe5e484
ms.sourcegitcommit: 81577378a4c570ced1e9c6765f4a9eee8453c889
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/08/2020
ms.locfileid: "84507612"
---
### <a name="ensure-console-logging-and-configure-diagnostic-settings"></a>Настройка записи журналов в консоль и параметров диагностики

Настройте ведение журналов так, чтобы все они выводили данные в консоль, а не в файлы.

Завершив развертывание приложения в Azure Spring Cloud, [добавьте параметр диагностики](/azure/spring-cloud/diagnostic-services) для подготовки зарегистрированных событий к использованию, например чрез Azure Monitor Log Analytics.

#### <a name="logstashelk-stack"></a>Стек LogStash/ELK

Если вы используете стек LogStash/ELK для объединения журналов, настройте параметр диагностики для потоковой передачи выходных данных консоли в [концентратор событий Azure](/azure/event-hubs/). Затем настройте [подключаемый модуль LogStash EventHub](https://github.com/logstash-plugins/logstash-input-azure_event_hubs) для приема регистрируемых событий в LogStash.

#### <a name="splunk"></a>Splunk

Если вы используете стек Splunk для объединения журналов, настройте потоковый вывод диагностики для передачи выходных данных консоли в [хранилище BLOB-объектов Azure](/azure/storage/blobs/). Затем примените [надстройку Splunk для облачных служб (Майкрософт)](https://splunkbase.splunk.com/app/3757/) для приема событий в Splunk.
