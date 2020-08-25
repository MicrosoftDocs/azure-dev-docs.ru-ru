---
title: Руководство. Масштабирование развертываний Jenkins с помощью агентов виртуальных машин Azure
description: Узнайте, как увеличить емкость конвейеров Jenkins с помощью виртуальных машин Azure с подключаемым модулем Jenkins для агентов виртуальных машин Azure.
keywords: jenkins, azure, devops, virtual machine, agents
ms.topic: tutorial
ms.date: 08/19/2020
ms.custom: devx-track-jenkins
ms.openlocfilehash: d081861eac98495d125a1a5eb5dd9700fb7783a8
ms.sourcegitcommit: 800c5e05ad3c0b899295d381964dd3d47436ff90
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88614544"
---
# <a name="tutorial-scale-jenkins-deployments-with-azure-vm-agents"></a>Руководство по масштабированию развертываний Jenkins с помощью агентов виртуальных машин Azure

В этом руководстве описывается, как с помощью [подключаемого модуля агентов виртуальных машин Azure](https://plugins.jenkins.io/azure-vm-agents) для Jenkins добавить емкость для виртуальных машин Linux в Azure.

Выполняя данное руководство, вы сделаете следующее:

> [!div class="checklist"]
> * устанавливать подключаемый модуль агентов виртуальных машин Azure;
> * настраивать подключаемый модуль для создания ресурсов в подписке Azure;
> * устанавливать вычислительные ресурсы, доступные для каждого агента;
> * настраивать операционную систему и инструменты на каждом агенте;
> * создавать универсальное задание Jenkins;
> * запускать задание в агенте виртуальной машины Azure.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Continuous-Integration-with-Jenkins-Using-Azure-VM-Agents/player]

## <a name="prerequisites"></a>Обязательные условия

- **Установка Jenkins.** Если у вас нет доступа к установленному экземпляру Jenkins, [настройте его с помощью Azure CLI](configure-on-linux-vm.md).

## <a name="install-azure-vm-agents-plugin"></a>Установка подключаемого модуля агентов виртуальных машин

1. На панели мониторинга Jenkins выберите **Manage Jenkins** (Управление Jenkins), а затем — **Manage Plugins** (Управление подключаемыми модулями).

1. Перейдите на вкладку **Available** (Доступно), а затем найдите **Azure VM Agents** (Агенты виртуальных машин Azure). Установите флажок рядом с записью для подключаемого модуля, а затем в нижней части панели мониторинга выберите **Install without restart** (Установить без перезагрузки).

## <a name="configure-the-azure-vm-agents-plugin"></a>Настройка подключаемого модуля агентов виртуальных машин Azure

1. На панели мониторинга Jenkins выберите **Manage Jenkins** (Управление Jenkins), а затем — **Configure System** (Настройка системы).

1. Прокрутите до нижней части страницы и найдите раздел **Cloud** (Облако) с раскрывающимся списком **Add new cloud** (Добавление нового облака) и выберите **Microsoft Azure VM Agents** (Агенты виртуальных машин Microsoft Azure).

1. Выберите имеющийся субъект-службу в раскрывающемся списке **Add** (Добавить) в разделе **Azure Credentials** (Учетные данные Azure). Если его в списке нет, выполните действия ниже, чтобы [создать субъект-службу](/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager) для учетной записи Azure и добавить его в конфигурацию Jenkins.

    а. Щелкните **Add** (Добавить) рядом с разделом **Azure Credentials** (Учетные данные Azure) и выберите **Jenkins**.
    b. В диалоговом окне **добавления учетных данных** выберите **субъект-службу Microsoft Azure** в раскрывающемся списке **Kind** (Вид).
    c. Создайте субъект-службу Active Directory с помощью Azure CLI или [Cloud Shell](/azure/cloud-shell/overview).
    
    ```azurecli-interactive
    az ad sp create-for-rbac --name jenkins_sp --password secure_password
    ```

    ```json
    {
        "appId": "BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBB",
        "displayName": "jenkins_sp",
        "name": "http://jenkins_sp",
        "password": "secure_password",
        "tenant": "CCCCCCCC-CCCC-CCCC-CCCCCCCCCCC"
    }
    ```
    d. Введите учетные данные из субъекта-службы в диалоговом окне **добавления учетных данных**. Если вы не знаете идентификатор подписки Azure, выполните следующий запрос в интерфейсе командной строки:
     
     ```azurecli-interactive
     az account list
     ```

     ```json
        {
            "cloudName": "AzureCloud",
            "id": "AAAAAAAA-AAAA-AAAA-AAAA-AAAAAAAAAAAA",
            "isDefault": true,
            "name": "Visual Studio Enterprise",
            "state": "Enabled",
            "tenantId": "CCCCCCCC-CCCC-CCCC-CCCC-CCCCCCCCCCC",
            "user": {
            "name": "raisa@fabrikam.com",
            "type": "user"
            }
     ```

    Готовый субъект-служба должен использовать поле `id` для **идентификатора подписки**, значение `appId` для **идентификатора клиента**, `password` для **секрета клиента** и `tenant` для **идентификатора клиента**. Щелкните **Add** (Добавить), чтобы добавить субъект-службу, а затем настроить подключаемый модуль для использования созданных учетных данных.

    ![Настройка субъекта-службы Azure](./media/scale-deployments-using-vm-agents/new-service-principal.png)

1. В разделе **Resource Group Name** (Имя группы ресурсов) с параметром **Create new** (Создать) введите `myJenkinsAgentGroup`.
1. Выберите **Verify configuration** (Проверить конфигурацию), чтобы подключиться к Azure и проверить параметры профиля.
1. Выберите **Apply** (Применить), чтобы обновить конфигурацию подключаемого модуля.

## <a name="configure-agent-resources"></a>Настройка ресурсов агента

Настройте шаблон для определения агента виртуальных машин Azure. Этот шаблон определяет вычислительные ресурсы, созданные каждым агентом.

1. Выберите **Add** (Добавить) рядом с параметром **Add Azure Virtual Machine Template** (Добавить шаблон виртуальной машины Azure).
1. В поле **Name** (Имя) введите `defaulttemplate`.
1. В поле **Label** (Метка) введите `ubuntu`.
1. Выберите нужный [регион Azure](https://azure.microsoft.com/regions/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) в поле со списком.
1. Выберите [размер виртуальной машины](/azure/virtual-machines/linux/sizes) в раскрывающемся списке в разделе **Virtual Machine Size** (Размер виртуальной машины). В этом руководстве используется размер общего назначения `Standard_DS1_v2`.   
1. Для параметра **Retention time** (Время хранения) задайте значение `60`. Этот параметр определяет время ожидания (в минутах) Jenkins перед его освобождением от неактивных агентов. Укажите 0, если не хотите, чтобы неактивные агенты удалялись автоматически.

   ![Общая конфигурация виртуальной машины](./media/scale-deployments-using-vm-agents/general-config.png)

## <a name="configure-agent-operating-system-and-tools"></a>Настройка операционной системы и инструментов агента

В разделе **настройки образа** подключаемого модуля выберите **Ubuntu 16.04 LTS**. Установите флажки рядом с пунктами **Install Git (Latest)** (Установить Git (последняя версия)), **Install Maven (V3.5.0)** (Установить Maven (V3.5.0)) и **Install Docker** (Установить Docker), чтобы установить эти инструменты на созданных агентах.

![Настройка ОС и инструментов виртуальной машины](./media/scale-deployments-using-vm-agents/jenkins-os-config.png)

Выберите **Add** (Добавить) рядом с параметром **Admin Credentials** (Учетные данные администратора), а затем щелкните **Jenkins**. Введите имя пользователя и пароль, используемые для входа в агенты. Убедитесь, что они соответствуют [политике имен пользователей и паролей](/azure/virtual-machines/linux/faq#what-are-the-username-requirements-when-creating-a-vm) административных учетных записей на виртуальных машинах Azure.

Выберите **Verify Template** (Проверка шаблона), чтобы проверить конфигурацию, а затем щелкните **Save** (Сохранить), чтобы сохранить изменения и вернуться на панель мониторинга Jenkins.

## <a name="create-a-job-in-jenkins"></a>Создание задания в Jenkins

1. На панели мониторинга Jenkins щелкните элемент **New Item**(Создать элемент). 
1. В поле имени введите `demoproject1`, выберите **Freestyle project** (Универсальный проект) и нажмите кнопку **ОК**.
1. На вкладке **General** (Общие) выберите **Restrict where project can be run** (Ограничения для запуска проекта) и введите для **выражения метки** значение `ubuntu`. Появится сообщение, подтверждающее обработку метки конфигурацией облака, созданной на предыдущем шаге. 
   ![Настройка задания](./media/scale-deployments-using-vm-agents/job-config.png)
1. На вкладке **Source Code Management** (Управление исходным кодом) выберите **Git** и добавьте следующий URL-адрес в поле **Repository URL** (URL-адрес репозитория): `https://github.com/spring-projects/spring-petclinic.git`.
1. На вкладке **Build** (Сборка) выберите **Add build step** (Добавить шаг сборки), а затем — **Invoke top-level Maven targets** (Вызов целевых объектов Maven верхнего уровня). В поле **Goals** (Цели) введите `package`.
1. Чтобы сохранить определение задания, выберите **Save** (Сохранить).

## <a name="build-the-new-job-on-an-azure-vm-agent"></a>Сборка задания в агенте виртуальной машины Azure

1. Вернитесь на панель мониторинга Jenkins.
1. Выберите задание, созданное на предыдущем шаге, и щелкните **Build now** (Построить сейчас). Новая сборка поставлена в очередь, но не запускается до создания агента виртуальной машины в подписке Azure.
1. По завершении сборки перейдите к окну **Console output** (Вывод на консоль). Вы увидите, что сборка выполнена в агенте Azure удаленно.

![Вывод на консоль](./media/scale-deployments-using-vm-agents/console-output.png)

## <a name="troubleshooting-the-jenkins-plugin"></a>Устранение неполадок подключаемого модуля Jenkins

Если вы столкнулись с ошибками, которые касаются подключаемых модулей Jenkins, сообщите о проблеме конкретного компонента в [JENKS JIRA](https://issues.jenkins-ci.org/).

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Развертывание в службе приложений Azure с использованием подключаемого модуля Jenkins](deploy-from-github-to-azure-app-service.md)