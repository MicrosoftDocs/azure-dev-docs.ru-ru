---
title: Руководство. Масштабирование развертываний Jenkins с помощью виртуальной машины, запущенной в Azure
description: Узнайте, как увеличить емкость конвейеров Jenkins с помощью виртуальных машин Azure.
keywords: jenkins, azure, devops, virtual machine, agents
ms.topic: tutorial
ms.date: 01/08/2021
ms.custom: devx-track-jenkins,devx-track-jenkins
ms.openlocfilehash: c498a43d5a8d57a75a5592de279b79a75b8e6360
ms.sourcegitcommit: 347bfa3b6c34579c567d1324efc63c1d6672a75b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/12/2021
ms.locfileid: "98109096"
---
# <a name="tutorial-scale-jenkins-deployments-with-vm-running-in-azure"></a>Руководство по Масштабирование развертываний Jenkins с помощью виртуальной машины, запущенной в Azure

[!INCLUDE [jenkins-integration-with-azure.md](includes/jenkins-integration-with-azure.md)]

В этом руководстве показано, как создать виртуальные машины Linux в Azure и добавить виртуальную машину в качестве рабочего узла в Jenkins.

Выполняя данное руководство, вы сделаете следующее:

> [!div class="checklist"]
> * создадите компьютер агента;
> * добавите агент в Jenkins;
> * создавать универсальное задание Jenkins;
> * запускать задание в агенте виртуальной машины Azure.

## <a name="prerequisites"></a>Обязательные условия

- **Установка Jenkins.** Если у вас нет доступа к установленному экземпляру Jenkins, [настройте его с помощью Azure CLI](configure-on-linux-vm.md).

## <a name="configure-agent-virtual-machine"></a>Настройка виртуальной машины агента

1. Используйте [az group create](/cli/azure/group?#az_group_create), чтобы создать группу ресурсов Azure.

    ```azurecli
    az group create --name <resource_group> --location <location>
    ```

1. Используйте [az vm create](/cli/azure/vm#az_vm_create), чтобы создать виртуальную машину.

    ```azurecli
    az vm create --resource-group <resource-group> --name <vm_name> --image UbuntuLTS --admin-username azureuser --admin-password "<password>"
    ```

    **Примечания**

    - Кроме того, вы можете передать ключ SSH с помощью следующей команды: `--ssh-key-value <ssh_path>`.

1. Установите JDK.  

    #### <a name="linux"></a>[Linux](#tab/linux)
    
    1. Войдите на виртуальную машину с помощью средства SSH.
    
        ```bash
        ssh username@123.123.123.123
        ```
        
    1. Установите JDK, используя apt. Также возможна установка с помощью других диспетчеров пакетов, например yum или pacman.
    
        ```bash
        sudo apt-get install -y default-jdk
        ```
    
    1. После установки выполните `java -version`, чтобы проверить окружение Java. Выходные данные будут содержать номера версий, связанные с разными частями JDK.
    
    #### <a name="windows"></a>[Windows](#tab/windows)
    
    1. Войдите на виртуальную машину с помощью средства SSH или путем подключения к удаленному рабочему столу.
    
    1. [Скачайте JDK](https://www.oracle.com/java/technologies/javase-downloads.html) для своей среды.
    
    1. Установите JDK.
    
## <a name="configure-jenkins-url"></a>Настройка URL-адреса Jenkins

При использовании JNLP необходимо настроить URL-адрес Jenkins.

1. В меню выберите пункт **Manage Jenkins** (Управление Jenkins).

1. В разделе **System Configuration** (Конфигурация системы) выберите элемент **Configure System** (Настройка системы).

1. Убедитесь, что для параметра **Jenkins URL** (URL-адрес Jenkins) задан HTTP-адрес установки Jenkins: `http://<your_host>.<your_domain>:8080/`.

1. Щелкните **Сохранить**.

## <a name="add-agent-to-jenkins"></a>Добавление агента в Jenkins

1. В меню выберите пункт **Manage Jenkins** (Управление Jenkins).

1. В разделе **System Configuration** (Конфигурация системы) выберите элемент **Manage Nodes and Clouds** (Управление узлами и облаками).

1. В меню выберите пункт **New Node** (Новый узел).

1. Введите значение в поле **Node Name** (Имя узла).

1. Выберите элемент **Permanent Agent** (Постоянный агент).

1. Нажмите кнопку **ОК**.

1. Укажите значения для следующих полей:

    - **Name** (Имя). Укажите уникальное имя, идентифицирующее агент в новой установке Jenkins. Это значение может отличаться от имени узла агента. Но вы можете сделать эти два значения одинаковыми. В качестве значения имени можно использовать любой специальный символ из следующего списка: `?*/\%!@#$^&|<>[]:;`.

    - **Удаленный корневой каталог.** У агента должен быть каталог, выделенный для Jenkins. Укажите путь к этому каталогу в агенте. Лучше использовать абсолютный путь, например `/home/azureuser/work` или `c:\jenkins`. Он должен быть локальным для компьютера агента. Этот путь может быть невидимым для главного сервера. Если используется относительный путь, например ./jenkins-agent, путь будет относительным для рабочего каталога, предоставленного методом запуска.

    - **Метки.** Метки используются для группирования семантически связанных агентов в одну логическую группу. Например, можно определить метку `UBUNTU` для всех агентов, работающих под управлением такого дистрибутива Linux, как Ubuntu.

    - **Метод запуска.** Есть два варианта запуска удаленного узла Jenkins: **запуск агентов с помощью SSH** и **запуск агента с помощью команды на главном сервере**:

        - **Запуск агентов с помощью SSH.** Укажите значения для следующих полей:

            - **Узел**: Общедоступный IP-адрес виртуальной машины или доменное имя. Например, `123.123.123.123` или `example.com`

            - **Учетные данные**: Выберите учетные данные, которые будут использоваться для входа в удаленный узел. Кроме того, вы можете нажать кнопку **Add** (Добавить), чтобы определить новые учетные данные, а затем выбрать эти новые учетные данные после создания.

            - **Стратегия проверки ключей узла.** Управляет тем, как Jenkins проверяет ключ SSH, представленный удаленным узлом, при подключении.

            ![Пример конфигурации узла для указания метода запуска агентов с помощью SSH.](./media/scale-deployments-using-vm-agents/ssh2.png)

        - **Запуск агента с помощью команды на главном сервере.**

            - Скачайте `agent.jar` из `https://<your_jenkins_host_name>/jnlpJars/agent.jar`. Например, `https://localhost:8443/jnlpJars/agent.jar`.

            - Отправьте `agent.jar` на виртуальную машину.

            - Запустите Jenkins с помощью команды `ssh <node_host> java -jar <remote_agentjar_path>`. Например, `ssh azureuser@99.99.999.9 java -jar /home/azureuser/agent.jar`.

            ![Пример конфигурации узла для указания метода запуска агентов с помощью команды на главном сервере.](./media/scale-deployments-using-vm-agents/config.png)

1. Щелкните **Сохранить**.

После определения конфигураций Jenkins добавляет виртуальную машину в качестве нового рабочего узла.

![Пример виртуальной машины, используемой в качестве нового рабочего узла](./media/scale-deployments-using-vm-agents/commandstart.png)

## <a name="create-a-job-in-jenkins"></a>Создание задания в Jenkins

1. В меню выберите пункт **New Item** (Новый элемент).

1. В поле имени введите `demoproject1`.

1. Выберите **Freestyle project** (Универсальный проект).

1. Щелкните **ОК**.

1. На вкладке **General** (Общие) выберите **Restrict where project can be run** (Ограничения для запуска проекта) и введите для **выражения метки** значение `ubuntu`. Появится сообщение, подтверждающее обработку метки конфигурацией облака, созданной на предыдущем шаге.

   ![Настройка нового задания Jenkins](./media/scale-deployments-using-vm-agents/job-config.png)

1. На вкладке **Source Code Management** (Управление исходным кодом) выберите **Git** и добавьте следующий URL-адрес в поле **Repository URL** (URL-адрес репозитория): `https://github.com/spring-projects/spring-petclinic.git`.

1. На вкладке **Build** (Сборка) выберите **Add build step** (Добавить шаг сборки), а затем — **Invoke top-level Maven targets** (Вызов целевых объектов Maven верхнего уровня). В поле **Goals** (Цели) введите `package`.

1. Щелкните **Сохранить**.

## <a name="build-the-new-job-on-an-azure-vm-agent"></a>Сборка задания в агенте виртуальной машины Azure

1. Выберите задание, созданное на предыдущем шаге.

1. Выберите **Build now** (Собрать). Новая сборка поставлена в очередь, но она не будет запущена до создания агента виртуальной машины в подписке Azure.

1. По завершении сборки перейдите к окну **Console output** (Вывод на консоль). Вы увидите, что сборка выполнена в агенте Azure удаленно.

    ![Вывод на консоль](./media/scale-deployments-using-vm-agents/console-output.png)

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Развертывание в службе приложений Azure с использованием подключаемого модуля Jenkins](deploy-from-github-to-azure-app-service.md)