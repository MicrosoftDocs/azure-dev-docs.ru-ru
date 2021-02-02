---
title: Краткое руководство. Настройка Jenkins с помощью Azure CLI
description: Узнайте, как установить Jenkins на виртуальной машине Azure под управлением Linux и создать пример приложения Java.
keywords: Jenkins, Azure, DevOps, портал, Linux, виртуальная машина
ms.topic: quickstart
ms.date: 08/21/2020
ms.custom: devx-track-jenkins, devx-track-azurecli
ms.openlocfilehash: 3ec8c309bfb88d1735bc523dca54bf71195f5c26
ms.sourcegitcommit: 3d906f265b748fbc0a070fce252098675674c8d9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/22/2021
ms.locfileid: "98699902"
---
# <a name="quickstart-configure-jenkins-using-azure-cli"></a>Краткое руководство. Настройка Jenkins с помощью Azure CLI

В этом кратком руководстве описано, как установить [Jenkins](https://jenkins.io) на виртуальной машине под управлением Ubuntu Linux с помощью средств и подключаемых модулей, настроенных для работы с Azure.

В этом кратком руководстве вы выполните приведенные ниже задачи.

> [!div class="checklist"]
> * Создание файла установки, который скачивает и устанавливает Jenkins
> * Создание группы ресурсов
> * Создание виртуальной машины с файлом установки
> * Откройте порт 8080, чтобы получить доступ к Jenkins на виртуальной машине.
> * Подключение к виртуальной машине через SSH
> * Настройка примера задания Jenkins на основе примера приложения Java в GitHub
> * Построение примера задания Jenkins

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]

## <a name="troubleshooting"></a>Устранение неполадок

Если у вас возникли проблемы с настройкой Jenkins, перейдите на [страницу установки Cloudbees Jenkins](https://www.jenkins.io/doc/book/installing/), чтобы получить последние инструкции и ознакомиться с известными проблемами.

## <a name="create-a-virtual-machine"></a>Создание виртуальной машины

1. Войдите на [портал Azure](https://portal.azure.com).

1. Откройте [Azure Cloud Shell](/azure/cloud-shell/overview) и, если это еще не сделано, перейдите на **Bash**.

1. Создайте файл с именем `cloud-init-jenkins.txt`.

    ```bash
    code cloud-init-jenkins.txt
    ```

1. Скопируйте приведенный ниже код и вставьте его в новый файл:

    ```json
    #cloud-config
    package_upgrade: true
    runcmd:
      - apt install openjdk-8-jdk -y
      - wget -qO - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -
      - sh -c 'echo deb https://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
      - apt-get update && apt-get install jenkins -y
      - service jenkins restart
    ```

1. Сохраните файл ( **&lt;Ctrl>S**) и закройте редактор ( **&lt;Ctrl>Q**).

1. Создайте группу ресурсов, используя команду [az group create](/cli/azure/group#az-group-create). Возможно, потребуется заменить заполнитель `--location` соответствующим значением для вашей среды.

    ```azurecli
    az group create \
    --name QuickstartJenkins-rg \
    --location eastus
    ```

1. Создайте виртуальную машину с помощью команды [az vm create](/cli/azure/vm#az-vm-create).

    ```azurecli
    az vm create \
    --resource-group QuickstartJenkins-rg \
    --name QuickstartJenkins-vm \
    --image UbuntuLTS \
    --admin-username "azureuser" \
    --generate-ssh-keys \
    --custom-data cloud-init-jenkins.txt
    ```

1. Проверьте создание (и состояние) новой виртуальной машины с помощью команды [az vm list](/cli/azure/vm#az-vm-list).

    ```azurecli
    az vm list -d -o table --query "[?name=='QuickstartJenkins-vm']"
    ```

1. По умолчанию Jenkins работает через порт 8080. Следовательно, откройте порт 8080 на новой виртуальной машине, используя [az vm open](/cli/azure/vm#az-vm-open-port).

    ```azurecli
    az vm open-port \
    --resource-group QuickstartJenkins-rg \
    --name QuickstartJenkins-vm  \
    --port 8080 --priority 1010
    ```

## <a name="configure-jenkins"></a>Настройка Jenkins

1. Получите общедоступный IP-адрес для примера виртуальной машины, используя [az vm show](/cli/azure/vm#az-vm-show).

    ```azurecli
    az vm show \
    --resource-group QuickstartJenkins-rg \
    --name QuickstartJenkins-vm -d \
    --query [publicIps] \
    --output tsv
    ```

    **Примечания**

    - Параметр `--query` ограничивает выходные данные к общедоступным IP-адресам виртуальной машины.

1. Используйте IP-адрес, полученный на предыдущем шаге, для подключения к виртуальной машине по протоколу SSH. Необходимо подтвердить запрос на подключение.

    ```azurecli
    ssh azureuser@<ip_address>
    ```

    **Примечания**

    - После успешного подключения в запросе Cloud Shell содержится имя пользователя и имя виртуальной машины: `azureuser@QuickstartJenkins-vm`.

1. Убедитесь, что Jenkins выполняется и получает состояние от службы Jenkins.

    ```bash
    service jenkins status
    ```

1. Получите автоматически сформированный пароль Jenkins.

    ```bash
    sudo cat /var/lib/jenkins/secrets/initialAdminPassword
    ```

1. Используя IP-адрес, откройте следующий URL-адрес в браузере: `http://<ip_address>:8080`

1. Введите ранее полученный пароль и выберите **Продолжить**.

    ![Начальная страница для разблокировки Jenkins](./media/configure-on-linux-vm/unlock-jenkins.png)

1. Щелкните **Select plugins to install** (Выбрать подключаемые модули для установки).

    ![Выбор варианта установки выбранных подключаемых модулей](./media/configure-on-linux-vm/select-plugins.png)

1. В поле фильтра в верхней части страницы введите `github`. Выберите подключаемый модуль GitHub, а затем — **Установить**.

    ![Установка подключаемых модулей GitHub](./media/configure-on-linux-vm/install-github-plugin.png)

1. Введите сведения для первого администратора и выберите **Сохранить и продолжить**.

    ![Ввод сведений для первого администратора](./media/configure-on-linux-vm/create-first-user.png)

1. На странице **Конфигурация экземпляра** выберите **Сохранить и завершить**.

    ![Страница подтверждения для конфигурации экземпляра](./media/configure-on-linux-vm/instance-configuration.png)

1. Выберите **Start using Jenkins** (Начать работу с Jenkins).

    ![Экземпляр Jenkins готов!](./media/configure-on-linux-vm/start-using-jenkins.png)

## <a name="create-your-first-job"></a>Создание первого задания

1. На домашней странице Jenkins выберите **Create a job** (Создание задания).

    ![Домашняя страница консоли Jenkins](./media/configure-on-linux-vm/jenkins-home-page.png)

1. Введите имя задания `mySampleApp`, выберите **Freestyle project** (Универсальный проект) и нажмите кнопку **ОК**.

    ![Создание задания](./media/configure-on-linux-vm/new-job.png)

1. Выберите вкладку **Source Code Management** (Управление исходным кодом). Включите **Git** и введите следующий URL-адрес для значения **Repository URL** (URL-адреса репозитория): `https://github.com/spring-guides/gs-spring-boot.git`

    ![Определение репозитория Git](./media/configure-on-linux-vm/source-code-management.png)

1. Выберите вкладку **Build** (Создание), нажмите **Add build step** (Добавление шага сборки).

    ![Добавление нового шага сборки](./media/configure-on-linux-vm/add-build-step.png)

1. Из раскрывающегося меню выберите **Invoke Gradle script** (Вызов сценария Gradle).

    ![Выбор параметра сценария Gradle](./media/configure-on-linux-vm/invoke-gradle-script-option.png)

1. Выберите **Use Gradle Wrapper** (Использовать программу-оболочку Gradle), затем введите значение `complete` для параметра **Wrapper location** (Расположение программы-оболочки) и `build` для параметра **Задачи**.

    ![Параметры сценария Gradle](./media/configure-on-linux-vm/gradle-script-options.png)

1. Выберите **Advanced** (Дополнительно) и введите `complete` в поле **Root Build script** (Корневой скрипт сборки).

    ![Дополнительные параметры сценария Gradle](./media/configure-on-linux-vm/root-build-script.png)

1. Прокрутите к нижней части страницы и выберите **Сохранить**.

## <a name="build-the-sample-java-app"></a>Сборка примера приложения Java

1. Когда откроется домашняя страница проекта, выберите **Build Now** (Собрать сейчас), чтобы скомпилировать код и создать пакет для примера приложения.

    ![Домашняя страница проекта](./media/configure-on-linux-vm/project-home-page.png)

1. Графическое изображение под заголовком **Build History** (История сборки) указывает на процесс сборки задания.

    ![Выполнение сборки задания](./media/configure-on-linux-vm/job-currently-building.png)

1. По завершении сборки выберите ссылку **Workspace** (Рабочая область).

    ![Выбор рабочей области](./media/configure-on-linux-vm/job-workspace.png)

1. Перейдите к `complete/build/libs`, чтобы увидеть, что файл `.jar` был успешно построен.

    ![Проверка целевой библиотекой того, что сборка прошла удачно](./media/configure-on-linux-vm/successful-build.png)

1. Теперь сервер Jenkins готов к сборке ваших собственных проектов в Azure.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Jenkins в Azure](./index.yml)
