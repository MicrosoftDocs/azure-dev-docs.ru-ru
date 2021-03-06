---
author: yevster
ms.author: yebronsh
ms.date: 8/25/2020
ms.openlocfilehash: 787b31e71f630c91f952afab4cc5c682d2ae9dd6
ms.sourcegitcommit: 9e282fc2ec967bee181c3034e7e70b28ae308905
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/04/2020
ms.locfileid: "89494333"
---
Итак, вы завершили миграцию. Теперь убедитесь, что приложение работает правильно. Применив указанные ниже рекомендации, вы сможете сделать приложение более удобным для использования в облаке.

* Оцените возможность настроить работу приложения с реестром Spring Cloud. Это позволит другим развернутым микрослужбам и клиентам динамически обнаруживать это приложение. Дополнительные сведения см. в статье [Учебник. по подготовке приложения Java Spring для развертывания в Azure Spring Cloud](/azure/spring-cloud/spring-cloud-tutorial-prepare-app-deployment). Затем измените все клиенты приложений, чтобы они использовали подсистему балансировки нагрузки клиентов Spring. Это позволит клиенту получать адреса всех работающих экземпляров приложения и находить работающий экземпляр, когда нарушается работа другого экземпляра или он не отвечает. Дополнительные сведения см. в записи [Spring Tips: подсистема балансировки нагрузки в Spring Cloud](https://spring.io/blog/2020/03/25/spring-tips-spring-cloud-loadbalancer) в блоге Spring.

* Вместо того, чтобы делать приложение общедоступным, попробуйте добавить экземпляр [шлюза Spring Cloud](https://cloud.spring.io/spring-cloud-gateway/reference/html/). Шлюз Spring Cloud выполняет роль единой конечной точки для всех приложений и микрослужб, развернутых в экземпляре Spring Cloud. Если шлюз Spring Cloud уже развернут, настройте маршрутизацию трафика в только что развернутое приложение.

* Попробуйте добавить сервер облачной конфигурации Spring Cloud для централизованного управления всеми микрослужбами Spring Cloud и настройки управления версиями. Сначала создайте репозиторий Git для размещения конфигурации и настройте его использование в экземпляре Azure Spring Cloud. Дополнительные сведения см. в статье [Учебник. Настройка экземпляра сервера конфигурации Spring Cloud для службы](/azure/spring-cloud/spring-cloud-tutorial-config-server). Затем перенесите конфигурацию, выполнив следующие действия:

  1. В каталоге *src/main/resources* приложения создайте файл *bootstrap.yml* со следующим содержимым:

        ```yml
          spring:
            application:
              name: <your-application-name>
        ```

  1. В репозитории конфигураций Git создайте файл *<имя_вашего_приложения>.yml*, где значение `your-application-name` такое же, как на предыдущем этапе. Переместите параметры из файла *application.yml* в *src/main/resources* в новый файл, который вы только что создали. Если ранее параметры размещались в файле *.properties*, сначала преобразуйте их в формат YAML. Можно найти веб-инструменты или подключаемые модули IntelliJ, которые помогут выполнить это преобразование.

  1. Создайте файл *application.yml* в указанном выше каталоге. С помощью этого файла можно определить параметры и ресурсы, которые будут совместно использоваться всеми приложениями в экземпляре Azure Spring Cloud. К таким параметрам обычно относятся источники данных, параметры ведения журнала, конфигурация Spring Boot Actuator и др.

  1. Зафиксируйте эти изменения и отправьте их в репозиторий Git.

  1. Удалите из приложения файл *application.properties* или *application.yml*.

* Обдумайте возможность добавить конвейер развертывания для автоматизации и обеспечения согласованности этого процесса. Для этого существуют инструкции по настройке [Azure Pipelines](/azure/spring-cloud/spring-cloud-howto-cicd), [GitHub Actions](/azure/spring-cloud/spring-cloud-howto-github-actions) и [Jenkins](/azure/jenkins/tutorial-jenkins-deploy-cli-spring-cloud-service).

* Обдумайте возможность применить промежуточные развертывания для тестирования изменений кода в рабочей среде, прежде чем предоставлять их некоторым или всем пользователям. Дополнительные сведения см. в статье [Настройка промежуточной среды в Azure Spring Cloud](/azure/spring-cloud/spring-cloud-howto-staging-environment).

* Обдумайте возможность добавить привязки служб для подключения приложения к поддерживаемым базам данных Azure. Такие привязки служб избавляют от необходимости предоставлять сведения о подключении, включая учетные данные, для приложений Spring Cloud.

* Попробуйте применить распределенную трассировку и Azure App Insights для мониторинга производительности и взаимодействий приложений. См. статью [Использование распределенной трассировки с помощью Azure Spring Cloud](/azure/spring-cloud/spring-cloud-tutorial-distributed-tracing).

* Рассмотрите возможность добавить правила генерации оповещений и группы действий Azure Monitor для быстрого обнаружения и устранения отклонений от обязательных условий. Дополнительные сведения см. в статье [Учебник. Мониторинг ресурсов Spring Cloud с помощью оповещений и групп действий](/azure/spring-cloud/spring-cloud-tutorial-alerts-action-groups).

* Обдумайте возможность реплицировать развертывание Azure Spring Cloud в другом регионе, чтобы снизить задержку, повысить надежность и отказоустойчивость. Примените [Диспетчер трафика Azure](/azure/traffic-manager) для балансировки нагрузки между развертываниями или [Azure Front Door](/azure/frontdoor) для добавления разгрузки SSL и брандмауэра веб-приложения с защитой от атак DDoS.

* Если георепликация не требуется, попробуйте добавить [Шлюз приложений Azure](/azure/application-gateway) для добавления разгрузки SSL и брандмауэра веб-приложения с защитой от атак DDoS.
