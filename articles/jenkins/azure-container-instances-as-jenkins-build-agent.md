---
title: Руководство. Использование службы "Экземпляры контейнеров Azure" в качестве агента сборки Jenkins
description: Узнайте, как настроить сервер Jenkins для выполнения заданий сборки Экземпляров контейнеров Azure.
keywords: Jenkins, Azure, DevOps, экземпляры контейнеров, агент сборки
ms.topic: article
ms.date: 01/08/2021
ms.custom: devx-track-jenkins,devx-track-azurecli
ms.openlocfilehash: 7633d88897d76f4ed75fa1d7d6c5b0c620db4919
ms.sourcegitcommit: 593d177cfb5f56f236ea59389e43a984da30f104
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/18/2021
ms.locfileid: "98561600"
---
# <a name="tutorial-use-azure-container-instances-as-a-jenkins-build-agent"></a>Руководство по Использование Экземпляров контейнеров Azure в качестве агента сборки Jenkins

[!INCLUDE [jenkins-integration-with-azure.md](includes/jenkins-integration-with-azure.md)]

Экземпляры контейнеров Azure (ACI) предоставляют доступную по требованию изолированную среду выполнения с накапливаемыми ресурсами для контейнерных рабочих нагрузок. Эти характеристики делают ACI отличной платформой для масштабного выполнения заданий сборки Jenkins. В этой статье показано, как развернуть ACI и добавить эту службу в качестве постоянного агента сборки для контроллера Jenkins.

См. дополнительные сведения о [службе "Экземпляры контейнеров Azure"](/azure/container-instances/container-instances-overview).

## <a name="prerequisites"></a>Предварительные требования

- **Подписка Azure**: Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), прежде чем начинать работу.
- **Сервер Jenkins**. Если он у вас не установлен, [создайте сервер Jenkins в Azure](./configure-on-linux-vm.md).

## <a name="prepare-the-jenkins-controller"></a>Подготовка сервера Jenkins

1. Перейдите на портал Jenkins.

1. В меню выберите пункт **Manage Jenkins** (Управление Jenkins).

1. В разделе **System Configuration** (Конфигурация системы) выберите элемент **Configure System** (Настройка системы).

1. Убедитесь, что для параметра **Jenkins URL** (URL-адрес Jenkins) задан HTTP-адрес установки Jenkins: `http://<your_host>.<your_domain>:8080/`.

1. В меню выберите пункт **Manage Jenkins** (Управление Jenkins).

1. В разделе **Security** (Безопасность) выберите элемент **Configure Global Security** (Настройка глобальной безопасности).

1. В разделе **Agents** (Агенты) в качестве порта укажите значение **Fixed** (Фиксированный) и введите соответствующий номер порта для своей среды.

    Пример конфигурации:  ![Настройка TCP-порта](./media/azure-container-instances-as-jenkins-build-agent/agent-port.png)

1. Щелкните **Сохранить**.

## <a name="create-jenkins-work-agent"></a>Создание рабочего агента Jenkins

1. Перейдите на портал Jenkins.

1. В меню выберите пункт **Manage Jenkins** (Управление Jenkins).

1. В разделе **System Configuration** (Конфигурация системы) выберите элемент **Manage Nodes and Clouds** (Управление узлами и облаками).

1. В меню выберите пункт **New Node** (Новый узел).

1. Введите значение в поле **Node Name** (Имя узла).

1. Выберите элемент **Permanent Agent** (Постоянный агент).

1. Нажмите кнопку **ОК**.

1. Введите значение в поле **Remote root directory** (Удаленный корневой каталог). Например: `/home/jenkins/work`

1. При необходимости укажите метку. Метки используются для группирования нескольких агентов в одну логическую группу. Примером метки может быть `linux` для группирования агентов Linux.

1. Задайте для параметра **Launch method** (Метод запуска) значение **Launch agent by connecting to the master** (Запускать агент, подключившись к главному серверу).

1. Убедитесь, что заполнены все обязательные поля:

    ![Пример конфигурации агента Jenkins](./media/azure-container-instances-as-jenkins-build-agent/agent-config.png)

1. Щелкните **Сохранить**.

1. На странице состояния агента вы увидите `JENKINS_SECRET` и `AGENT_NAME`. На приведенном ниже снимке экрана показано, как определить значения. Оба значения необходимы при создании экземпляра Экземпляров контейнеров Azure.

    ![После успешного создания отображается секрет агента сборки.](./media/azure-container-instances-as-jenkins-build-agent/jenkins-secret.png)

## <a name="create-azure-container-instance-with-cli"></a>Создание экземпляра контейнера Azure с помощью CLI

1. Используйте [az group create](/cli/azure/group?#az_group_create), чтобы создать группу ресурсов Azure.

      ```azurecli
      az group create --name my-resourcegroup --location westus
      ```

1. Используйте [az container create](/cli/azure/container#az_container_create), чтобы создать экземпляр Экземпляров контейнеров Azure. Замените заполнители значениями, полученными при создании рабочего агента.

    ```azurecli
    az container create \
      --name my-dock \
      --resource-group my-resourcegroup \
      --ip-address Public --image jenkins/inbound-agent:latest \
      --os-type linux \
      --ports 80 \
      --command-line "jenkins-agent -url http://jenkinsserver:port <JENKINS_SECRET> <AGENT_NAME>"
    ```

    После запуска контейнер автоматически подключится к серверу контроллера Jenkins.

    ![Агент успешно запущен.](./media/azure-container-instances-as-jenkins-build-agent/agent-start.png)

## <a name="create-a-build-job"></a>Создание задания сборки

Теперь для демонстрации сборки Jenkins в экземпляре контейнера Azure необходимо создать задание сборки Jenkins.

1. Выберите **Новый элемент**, присвойте проекту сборки имя, например **aci-demo**, затем выберите тип **Универсальный проект** и щелкните **ОК**.

   ![Поле для ввода имени задания сборки и список типов проектов](./media/azure-container-instances-as-jenkins-build-agent/jenkins-new-job.png)

2. В разделе **Общие** проверьте, что установлен флажок **Ограничения для запуска этого проекта**. В поле выражения метки введите **linux**. Такая конфигурация гарантирует, что это задание сборки будет выполняться только в облаке экземпляров контейнеров Azure.

   ![Вкладка "Общие" с данными конфигурации](./media/azure-container-instances-as-jenkins-build-agent/jenkins-job-01.png)

3. В разделе **Сборка** выберите действие **Добавить шаг сборки** и щелкните **Выполнить оболочку**. Введите `echo "aci-demo"` в качестве команды.

   ![Вкладка сборки с выделенными значениями для шага создания](./media/azure-container-instances-as-jenkins-build-agent/jenkins-job-02.png)

5. Щелкните **Сохранить**.

## <a name="run-the-build-job"></a>Запустите задание сборки.

Чтобы проверить задание сборки и увидеть, как экземпляры контейнеров Azure используются в качестве платформы сборки, запустите сборку вручную.

1. Выберите **Начать сборку**, чтобы начать сборку. Запуск задания может занять несколько минут. Вы должны увидеть состояние, аналогичное показанному ниже:

   ![Сведения в журнале сборки с состоянием задания](./media/azure-container-instances-as-jenkins-build-agent/jenkins-job-status.png)

2. Пока выполняется задание, откройте портал Azure и найдите группу ресурсов Jenkins. Здесь вы увидите, что создан новый экземпляр контейнера. Внутри этого экземпляра выполняется задание Jenkins.

   ![Экземпляр контейнера в группе ресурсов](./media/azure-container-instances-as-jenkins-build-agent/jenkins-aci.png)

3. Если Jenkins выполняет больше заданий, чем настроенное число исполнителей Jenkins (по умолчанию 2), он создает несколько экземпляров контейнера.

   ![Созданные экземпляры контейнеров](./media/azure-container-instances-as-jenkins-build-agent/jenkins-aci-multi.png)

4. После завершения всех заданий сборки все экземпляры контейнеров удаляются.

   ![Группа ресурсов с экземпляром контейнеров удалена](./media/azure-container-instances-as-jenkins-build-agent/jenkins-aci-none.png)

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Развертывание в службе приложений Azure с использованием подключаемого модуля Jenkins](/azure/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service)