---
title: Руководство. Использование Azure Dev Spaces со Службой Azure Kubernetes
description: Из этой статьи вы узнаете, как использовать подключаемый модуль Azure Dev Spaces в конвейере непрерывной интеграции.
keywords: Jenkins, Azure, DevOps, Azure Dev Spaces, AKS, Служба Azure Kubernetes
ms.topic: tutorial
ms.date: 10/23/2019
ms.openlocfilehash: 427b53642b4159a82dba699c631da8948a6b744b
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/05/2020
ms.locfileid: "82170290"
---
# <a name="tutorial-use-azure-dev-spaces-with-azure-kubernetes-service"></a>Руководство по использованию Azure Dev Spaces со Службой Azure Kubernetes

Azure Dev Spaces позволяет тестировать и итеративно разрабатывать приложение микрослужб, выполняемое в Службе Azure Kubernetes (AKS), без необходимости реплицировать или имитировать зависимости. Подключаемый модуль Azure Dev Spaces для Jenkins помогает использовать Azure Dev Spaces в конвейере непрерывной интеграции и непрерывной доставки (CI/CD).

В этом руководстве используется Реестр контейнеров Azure (ACR). ACR хранит образы, а задача ACR компилирует артефакты Docker и Helm. Применение ACR и задачи ACR для создания артефактов позволяет избавиться от установки дополнительного программного обеспечения, например Docker, на сервере Jenkins. 

При работе с этим руководством вы выполните следующие задачи:

> [!div class="checklist"]
> * Создание кластера AKS с поддержкой Azure Dev Spaces
> * Развертывание в AKS приложения с несколькими службами
> * подготовка сервера Jenkins;
> * Применение подключаемого модуля Azure Dev Spaces в конвейере Jenkins позволяет просмотреть изменения в коде, прежде чем включать их в проект.

В этом руководстве предполагается, что у вас есть по меньшей мере средний уровень понимания служб Azure, AKS, ACR, Azure Dev Spaces, [конвейеров](https://jenkins.io/doc/book/pipeline/) и подключаемых модулей Jenkins, а также репозитория GitHub. Будут полезными и базовые знания вспомогательных средств, таких как Helm и kubectl.

## <a name="prerequisites"></a>Предварительные требования

* Учетная запись Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

* Учетная запись GitHub. Если у вас нет учетной записи GitHub, [создайте бесплатную учетную запись](https://github.com/), прежде чем начинать работу.

* Установленная платформа [Visual Studio Code](https://code.visualstudio.com/download) с расширением [Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds).

* [Установленное средство Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) версии 2.0.43 или выше.

* Главный узел Jenkins. Если у вас еще нет главного узла Jenkins, разверните [Jenkins](https://aka.ms/jenkins-on-azure) в Azure, выполнив инструкции из  [этого краткого руководства](https://docs.microsoft.com/azure/jenkins/install-jenkins-solution-template). 

* На сервере Jenkins нужно установить Helm и kubectl, а также предоставить учетной записи Jenkins доступ к ним, как описано далее в этом руководстве.

* VS Code, терминал VS Code или WSL и Bash. 


## <a name="create-azure-resources"></a>Создание ресурсов Azure

В этом разделе показано, как создать следующие ресурсы Azure:

* группу ресурсов, вмещающую в себя все необходимые ресурсами для работы с этим руководством;
* кластер [Службы Azure Kubernetes](https://docs.microsoft.com/azure/aks/) (AKS);
* [реестр контейнеров Azure](https://docs.microsoft.com/azure/container-registry/) (ACR) для сборки (с помощью задач ACR) и хранения образов Docker.

1. Создайте группу ресурсов.

    ```azurecli
    az group create --name MyResourceGroup --location westus2
    ```

2. Создайте кластер AKS. Создайте кластер AKS в [регионе, который поддерживает Dev Spaces](/azure/dev-spaces/about.md#supported-regions-and-configurations).

    ```azurecli
    az aks create --resource-group MyResourceGroup --name MyAKS --location westus2 --kubernetes-version 1.11.9 --enable-addons http_application_routing --generate-ssh-keys --node-count 1 --node-vm-size Standard_D1_v2
    ```

3. Настройка AKS для использования Dev Spaces.

    ```azurecli
    az aks use-dev-spaces --resource-group MyResourceGroup --name MyAKS
    ```
    На этом шаге выполняется установка расширения CLI `azds`.

4. Создайте реестр контейнеров.

    ```azurecli
    az acr create -n MyACR -g MyResourceGroup --sku Basic --admin-enabled true
    ```

## <a name="deploy-sample-apps-to-the-aks-cluster"></a>Развертывание примеров приложений в кластере AKS

В этом разделе показано, как настроить пространство разработки и развернуть пример приложения в кластере AKS, который вы создали ранее. Наше приложение состоит из двух частей: *webfrontend* и *mywebapi*. Оба компонента развертываются в пространстве разработки. Позже вы отправите запрос на вытягивание в интерфейс mywebapi, который запустит в Jenkins конвейер непрерывной интеграции.

Дополнительные сведения об использовании Azure Dev Spaces и систем с несколькими службами в Azure Dev Spaces вы найдете в статьях [Начало работы в Azure Dev Spaces с использованием Java](https://docs.microsoft.com/azure/dev-spaces/get-started-java) и [Разработка с использованием нескольких служб с помощью Azure Dev Spaces](https://docs.microsoft.com/azure/dev-spaces/multi-service-java). Эти руководства содержат дополнительные базовые сведения, которые здесь не приводятся.

1. Скачайте репозиторий https://github.com/Azure/dev-spaces с сайта GitHub.

2. Откройте папку `samples/java/getting-started/webfrontend` в VS Code. (Вы можете игнорировать любые запросы по умолчанию на добавление ресурсов отладки или восстановления проекта.)

3. Обновите файл `/src/main/java/com/ms/sample/webfrontend/Application.java`, чтобы он выглядел следующим образом.

    ```java
    package com.ms.sample.webfrontend;

    import java.io.*;
    import java.net.*;
    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    import org.springframework.web.bind.annotation.*;

    @SpringBootApplication
    @RestController
    public class Application {
        public static void main(String[] args) {
            SpringApplication.run(Application.class, args);
        }

        @RequestMapping(value = "/greeting", produces = "text/plain")
        public String greeting(@RequestHeader(value = "azds-route-as", required = false) String azdsRouteAs) throws Exception {
            URLConnection conn = new URL("http://mywebapi/").openConnection();
            conn.setRequestProperty("azds-route-as", azdsRouteAs); // propagate dev space routing header
            try (BufferedReader reader = new BufferedReader(new InputStreamReader(conn.getInputStream())))
            {
                return "Hello from webfrontend and " + reader.lines().reduce("\n", String::concat);
            }
        }
    }
    ```

4. Щелкните **Вид** и **Терминал**, чтобы открыть интегрированный терминал в VS Code.

5. Выполните команду `azds prep`, чтобы подготовить приложение к запуску в пространстве разработки. Эта команда должна выполняться из `dev-spaces/samples/java/getting-started/webfrontend`, чтобы правильно подготовить приложение.

    ```bash
    azds prep --public
    ```

    Команда `azds prep` в Dev Spaces CLI создает ресурсы Docker и Kubernetes с настройками по умолчанию. Эти файлы сохраняются в течение всего жизненного цикла проекта, и вы можете изменить их:

    * `./Dockerfile` и `./Dockerfile.develop` описывают образ контейнера приложения, а также способ создания и выполнения исходного кода в контейнере.
    * В [диаграмме Helm](https://helm.sh/docs/topics/charts/) в разделе `./charts/webfrontend` описано, как развернуть контейнер в Kubernetes.
    * `./azds.yaml` выполняет роль файла конфигурации для Azure Dev Spaces.

    Дополнительные сведения см. в статье о [принципах работы и настройки Azure Dev Spaces](https://docs.microsoft.com/azure/dev-spaces/how-dev-spaces-works).

6. Скомпилируйте и выполните приложение в AKS, запустив команду `azds up`:

    ```bash
    azds up
    ```
    <a name="test_endpoint"></a>Просмотрите выходные данные консоли, чтобы найти сведения об URL-адресе, который был создан командой `up`. Он будет выглядеть следующим образом:

    ```bash
    (pending registration) Service 'webfrontend' port 'http' will be available at '<url>'
    Service 'webfrontend' port 80 (TCP) is available at 'http://localhost:<port>'
    ```
    Перейдите по этому URL-адресу в браузере, чтобы открыть веб-приложение. По мере выполнения контейнера в окно терминала передаются выходные данные `stdout` и `stderr`.

8. Теперь настройте и разверните *mywebapi*:

    1. Перейдите в каталог `dev-spaces/samples/java/getting-started/mywebapi`.

    2. Run (Запустить)

        ```bash
        azds prep
        ```

    3. Run (Запустить)

        ```bash
        azds up -d
        ```

## <a name="prepare-jenkins-server"></a>Подготовка сервера Jenkins

В этом разделе показано, как подготовить сервер Jenkins для выполнения примера конвейера непрерывной интеграции.

* Установка подключаемых модулей
* Установка Helm и Kubernetes CLI
* Добавление учетных данных

### <a name="install-plug-ins"></a>Установка подключаемых модулей

1. Выполните вход на сервер Jenkins. Выберите действие **Manage Jenkins (Управление Jenkins) > Manage Plugins (Управление подключаемыми модулями)** .
2. На вкладке **Available** (Доступные) выберите такие подключаемые модули:
    * [Azure Dev Spaces](https://plugins.jenkins.io/azure-dev-spaces);
    * [Задачи Реестра контейнеров Azure](https://plugins.jenkins.io/azure-container-registry-tasks)
    * [Environment Injector](https://plugins.jenkins.io/envinject);
    * [интеграция с GitHub](https://plugins.jenkins.io/github-pullrequest);

    Если этих подключаемых модулей нет в списке, перейдите на вкладку **Installed** (Установленные) и проверьте, установлены ли они.

3. Чтобы установить подключаемые модули, выберите **Download now and install after restart** (Скачать сейчас и установить после перезагрузки).

4. Чтобы завершить установку, перезагрузите сервер Jenkins.

### <a name="install-helm-and-kubectl"></a>Установка Helm и kubectl

Этот пример конвейера использует Helm и kubectl для развертывания в пространстве разработки. При установке Jenkins создается учетная запись администратора с именем *jenkins*. Helm и kubectl должны быть доступны этому пользователю jenkins.

1. Установите SSH-подключение к главному экземпляру Jenkins. 

2. Переключитесь на пользователя `jenkins`.
    ```bash
    sudo su jenkins
    ```

3. Установите Helm CLI. Дополнительные сведения об установке Helm см. в [этой статье](https://helm.sh/docs/using_helm/#installing-helm).

4. Установите kubectl. Дополнительные сведения см. в описании команды [**az acs kubernetes install-cli**](https://helm.sh/docs/using_helm/#installing-helm).

### <a name="add-credentials-to-jenkins"></a>Добавление учетных данных в Jenkins

1. Серверу Jenkins требуется субъект-служба Azure для аутентификации и доступа к ресурсам Azure. Сведения о создании субъекта-службы см. в  [этом разделе](https://docs.microsoft.com/azure/jenkins/deploy-from-github-to-azure-app-service#create-service-principal)  руководства по развертыванию в Службе приложений Azure. Не забудьте сохранить копию выходных данных команды `create-for-rbac`, так как эти сведения вам потребуются для выполнения следующего шага. Результат должен выглядеть следующим образом.

    ```json
    {
      "appId": "f4150da1-xxx-xxxx-xxx-xxxxxxxxxxxx",
      "displayName": "xxxxxxxjenkinssp",
      "name": "http://xxxxxxxjenkinssp",
      "password": "f6e4d839-xxx-xxxx-xxx-xxxxxxxxxxxx",
      "tenant": "72f988bf--xxx-xxxx-xxx-xxxxxxxxxxxx"
    }
    ```

2. Добавьте в Jenkins тип учетных данных *Microsoft Azure Service Principal* (Субъект-служба Microsoft Azure), используя сведения о субъекте-службе, собранные на предыдущем шаге. На следующем снимке экрана имена соответствуют выходным данным `create-for-rbac`.

    Поле **ID** содержит учетное имя Jenkins для субъекта-службы. В этом примере используется значение `displayName` (в нашем случае это `xxxxxxxjenkinssp`), но вы можете указать здесь любой текст. На следующем шаге это имя учетных данных будет сохранено в переменной среды AZURE_CRED_ID.

    ![Добавление учетных данных субъекта-службы в Jenkins](media/azure-dev-spaces-and-aks/add-service-principal-credentials.png)

    Поле **Description** (Описание) заполнять необязательно. Подробные сведения см. в [разделе о добавлении субъекта-службы в Jenkins](https://docs.microsoft.com/azure/jenkins/deploy-from-github-to-azure-app-service#add-service-principal-to-jenkins)  руководства по развертыванию в Службе приложений Azure. 



3. Чтобы отобразить учетные данные ACR, выполните следующую команду:

    ```azurecli
    az acr credential show -n <yourRegistryName>
    ```

    Сохраните копию выходных данных JSON, которые должны выглядеть примерно так:

    ```json
    {
      "passwords": [
        {
          "name": "password",
          "value": "vGBP=zzzzzzzzzzzzzzzzzzzzzzzzzzz"
        },
        {
          "name": "password2",
          "value": "zzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzz"
        }
      ],
      "username": "acr01"
    }
    ```

4. Добавьте в Jenkins тип учетных данных *Username with password* (Имя пользователя и пароль). В поле **Username** нужно указать имя пользователя из последнего шага, в нашем примере это `acr01`. В поле **Password** нужно указать значение первого пароля, в нашем примере это `vGBP=zzzzzzzzzzzzzzzzzzzzzzzzzzz`. В поле **ID** нужно указать значение ACR_CRED_ID для этих учетных данных.

5. Настройте учетные данные AKS. Добавьте в Jenkins тип учетных данных *Kubernetes configuration (kubeconfig)* (Конфигурация Kubernetes (kubeconfig), выбрав вариант Enter directly (Ввести самостоятельно). Чтобы получить учетные данные для доступа к кластеру AKS, выполните следующую команду:

    ```azurecli
    az aks get-credentials -g MyResourceGroup -n <yourAKSName> -f -
    ```

   Значение учетных данных в поле **ID** будет использоваться для KUBE_CONFIG_ID на следующем шаге.

## <a name="create-a-pipeline"></a>Создание конвейера

Сценарий, выбранный для этого примера конвейера, основан на ситуации из реальной жизни. Запрос на вытягивание запускает конвейер непрерывной интеграции, который собирает предлагаемые изменения и развертывает их в пространство разработки Azure для тестирования и проверки. В зависимости от результата этой проверки изменения объединяются и развертываются в AKS или отвергаются. В завершение процесса пространство разработки удаляется.

Конфигурация конвейера Jenkins и Jenkinsfile определяют этапы работы конвейера непрерывной интеграции. Следующая блок-схема демонстрирует этапы работы конвейера и точки принятия решений, определенные в Jenkinsfile:

![Поток конвейера Jenkins](media/azure-dev-spaces-and-aks/jenkins-pipeline-flow.png)

1. Скачайте измененную версию проекта *mywebapi* по адресу [https://github.com/azure-devops/mywebapi](https://github.com/azure-devops/mywebapi). Этот проект содержит несколько файлов, которые потребуются для создания конвейера, в том числе *Jenkinsfile*, *Dockerfiles* и чарт Helm.

2. Войдите в Jenkins. В меню слева выберите **Add Item** (Добавить элемент).

3. Выберите **Pipeline** (Конвейер) и введите для него имя в поле **Enter an item name** (Введите имя элемента). Щелкните **ОК**. Автоматически откроется экран настройки конвейера.

4. На вкладке **General** (Общие) установите флажок **Prepare an environment for the run** (Подготовить среду для запуска). 

5. Также установите флажки **Keep Jenkins Environment Variables** (Сохранить переменные среды Jenkins) и **Keep Jenkins Build Variables** (Сохранить переменные сборки Jenkins).

6. В области **Properties Content** (Содержимое переменных) установите следующие переменные среды:

    ```
    AZURE_CRED_ID=[your credential ID of service principal]
    RES_GROUP=[your resource group of the function app]
    ACR_RES_GROUP=[your ACR resource group]
    ACR_NAME=[your ACR name]
    ACR_REGISTRY=[your ACR registry url, without http schema]
    ACR_CRED_ID=[your credential id of your ACR account]
    AKS_RES_GROUP=[your AKS resource group]
    AKS_NAME=[your AKS name]
    IMAGE_NAME=[name of Docker image you will push to ACR, without registry prefix]
    KUBE_CONFIG_ID=[your kubeconfig id]
    PARENT_DEV_SPACE=[shared dev space name]
    TEST_ENDPOINT=[your web frontend end point for testing. Should be webfrontend.XXXXXXXXXXXXXXXXXXX.xxxxxx.aksapp.io]
    ```

    Если применить все примеры значений, приведенные в предыдущих разделах, список переменных среды будет выглядеть примерно так:

    ![Переменные среды для конвейера Jenkins](media/azure-dev-spaces-and-aks/jenkins-pipeline-environment.png)

7. Выберите **Pipeline script from SCM** (Скрипт конвейера из SCM) в разделе **Pipeline > Definition** (Конвейер > Определение).
8. В области **SCM** выберите **Git** и введите URL-адрес репозитория.
9. В параметре **Branch Specifier** (Описатель ветви) введите значение `refs/remotes/origin/${GITHUB_PR_SOURCE_BRANCH}`.
10. Введите URL-адрес репозитория SCM и путь к скрипту Jenkinsfile.
11. Установите флажок **Lightweight checkout** (Упрощенное извлечение).

## <a name="create-a-pull-request-to-trigger-the-pipeline"></a>Создание запроса на вытягивание для активации конвейера

Чтобы выполнить шаг 3 из этого раздела, следует закомментировать часть файла Jenkinsfile, иначе вы получите сообщение об ошибке 404 при попытке параллельно просмотреть новые и старые версии. При попытке слияния запроса на вытягивание предыдущая версия mywebapi по умолчанию удаляется и заменяется новой версией. Внесите следующие изменения в Jenkinsfile, прежде чем выполнять шаг 1:

```Groovy
    if (userInput == true) {
        stage('deploy') {
            // Apply the deployment to shared namespace in AKS using acsDeploy, Helm or kubectl   
        }
        
        stage('Verify') {
            // verify the staging environment is working properly
        }
        
        /* Comment the cleanup stage to allow side by side comparison with the new child dev space

        stage('cleanup') {
            devSpacesCleanup aksName: env.AKS_NAME, 
                azureCredentialsId: env.AZURE_CRED_ID, 
                devSpaceName: devSpaceNamespace, 
                kubeConfigId: env.KUBE_CONFIG_ID, 
                resourceGroupName: env.AKS_RES_GROUP,
                helmReleaseName: releaseName
        }
        */

    } else {
        // Send a notification
    }
```

1. Внесите изменения в `mywebapi/src/main/java/com/ms/sample/mywebapi/Application.java` и затем создайте запрос на вытягивание. Пример:

    ```java
    public String index() {
        return "Hello from mywebapi in my private dev space";
    }
    ```

2. Войдите в Jenkins выберите имя конвейера, а затем щелкните **Build Now** (Собрать сейчас). 

    Можно также настроить *веб-перехватчик*, который будет автоматически запускать конвейер Jenkins. При вводе запроса на вытягивание GitHub отправляет в Jenkins запрос POST, который запускает конвейер. Дополнительные сведения о настройке веб-перехватчика см. в разделе о [подключении Jenkins к GitHub](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/jenkins/deploy-from-github-to-azure-app-service.md#connect-jenkins-to-github).

3. Сравните изменения с текущей общедоступной версией.

    1. Откройте браузер и перейдите к странице общедоступной версии `https://webfrontend.XXXXXXXXXXXXXXXXXXX.eastus.aksapp.io`. TEST_ENDPOINT содержит нужный URL-адрес.

    2. Откройте другую вкладку и введите в ней URL-адрес запроса на вытягивание в пространстве разработки. Он будет выглядеть аналогично `https://<yourdevspacename>.s.webfrontend.XXXXXXXXXXXXXXXXXXX.eastus.aksapp.io`. Эту ссылку для задания Jenkins можно найти в разделе **Build History > <build#> > Console Output** (История сборки > <номер сборки> > Выходные данные консоли). Найдите на странице строку `aksapp` или `azdsprefix`, если вас интересует только префикс.

 

### <a name="constructing-the-url-to-the-child-dev-space"></a>Создание URL-адреса в дочернем пространстве разработки

При отправке запроса на вытягивание Jenkins создает дочернее пространство разработки на основе общего пространства разработки команды разработчиков и выполняет в этом дочернем пространстве код из запроса на вытягивание. URL-адрес дочернего пространства разработки имеет вид `http://$env.azdsprefix.<test_endpoint>`. 

Значение для **$env.azdsprefix** присваивается во время выполнения конвейера подключаемым модулем Azure Dev Spaces с помощью **devSpacesCreate**:

```Groovy
stage('create dev space') {
    devSpacesCreate aksName: env.AKS_NAME,
        azureCredentialsId: env.AZURE_CRED_ID,
        kubeconfigId: env.KUBE_CONFIG_ID,
        resourceGroupName: env.AKS_RES_GROUP,
        sharedSpaceName: env.PARENT_DEV_SPACE,
        spaceName: devSpaceNamespace
}
```

URL-адрес `test_endpoint` указывает на интерфейсное приложение, которое вы ранее развернули с помощью `azds up`, выполняя инструкции из раздела [Развертывание примеров приложений в кластере AKS](#test_endpoint). Значение `$env.TEST_ENDPOINT` задается в конфигурации конвейера. 

Следующий фрагмент кода демонстрирует, как используется URL-адрес дочернего пространства разработки на этапе `smoketest`. Этот код проверяет, доступно ли дочернее пространство разработки TEST_ENDPOINT, и при его наличии передает в stdout текст приветствия:

```Groovy
stage('smoketest') {
    // CI testing against http://$env.azdsprefix.$env.TEST_ENDPOINT" 
    SLEEP_TIME = 30
    SITE_UP = false
    for (int i = 0; i < 10; i++) {
        sh "sleep ${SLEEP_TIME}"
        code = "0"
        try {
            code = sh returnStdout: true, script: "curl -sL -w '%{http_code}' 'http://$env.azdsprefix.$env.TEST_ENDPOINT/greeting' -o /dev/null"
        } catch (Exception e){
            // ignore
        }
        if (code == "200") {
            sh "curl http://$env.azdsprefix.$env.TEST_ENDPOINT/greeting"
            SITE_UP = true
            break
        }
    }
    if(!SITE_UP) {
        echo "The site has not been up after five minutes"
    }
}
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Завершив работу с примером приложения, очистите ресурсы Azure путем удаления группы ресурсов:

```azurecli
az group delete -y --no-wait -n MyResourceGroup
```

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [CI/CD с использованием Jenkins в Azure](deploy-from-github-to-aks.md)
