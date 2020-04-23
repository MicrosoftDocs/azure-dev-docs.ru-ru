---
author: edburns
ms.author: edburns
ms.date: 1/21/2020
ms.openlocfilehash: 9403614c7ae2990a35fcbcce6d2e104f87724da5
ms.sourcegitcommit: 0af39ee9ff27c37ceeeb28ea9d51e32995989591
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81673530"
---
### <a name="determine-the-network-topology"></a>Определение топологии сети

Текущий набор предложений Marketplace представляет собой отправную точку для выполнения миграции. Если предложение охватывает не все аспекты архитектуры, которую вы хотите перенести, вам нужно воспроизвести в Azure топологию сети существующего развертывания, даже после развертывания базового предложения на основе одного из шаблонов решений.

Хотя это очень обширная тема, следующие ресурсы помогут вам спланировать миграцию:

* Список общих статей, которые имеют отношение к переносу топологии сети в Azure: [Руководство по развертыванию Fast Track](https://docs.oracle.com/en/middleware/fusion-middleware/weblogic-server/12.2.1.4/intro/deploying.html#GUID-E0BE4A3E-44CD-4C95-9540-7A850BF02F6A).
* Рекомендации по кластеризации, которая влияет на топологию сети: [Кластеризация WebLogic Server](https://docs.oracle.com/en/middleware/fusion-middleware/weblogic-server/12.2.1.4/intro/clustering.html#GUID-E39A18C2-B990-485F-BFB1-0549250FABFE).
* Так как источники данных в системе WebLogic представляют собой отдельные серверы, их необходимо учитывать при анализе топологии сети: [Источники данных в WebLogic Server](https://docs.oracle.com/en/middleware/fusion-middleware/weblogic-server/12.2.1.4/intro/jdbc.html#GUID-9FD5F552-B2E4-4FEC-8C10-503A08764B52).
* Источники служб сообщений также являются отдельными серверами: [Обмен сообщениями в WebLogic Server](https://docs.oracle.com/en/middleware/fusion-middleware/weblogic-server/12.2.1.4/intro/jms.html#GUID-3B5F647D-E001-413B-AC6A-1E103BDBA93F).
* Балансировка нагрузки на стороне WebLogic Server является важным требованием: [Балансировка нагрузки в кластере](https://docs.oracle.com/en/middleware/fusion-middleware/weblogic-server/12.2.1.4/clust/load_balancing.html#GUID-B8F6DE4B-1AAC-428B-878B-BFDCE161C054).
