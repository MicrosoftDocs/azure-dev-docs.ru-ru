---
title: Руководство. Развертывание из GitHub в Службе Azure Kubernetes с помощью Jenkins
description: Узнайте, как настроить Jenkins для обеспечения непрерывной интеграции (CI) и непрерывного развертывания (CD) из GitHub в Службе Azure Kubernetes (AKS).
keywords: Jenkins, Azure, DevOps, AKS, Служба Azure Kubernetes, GitHub
ms.topic: article
ms.date: 10/29/2019
ms.custom: devx-track-jenkins, devx-track-azurecli
ms.openlocfilehash: 51b0531946d4fde0e9141744e62bab35a3e1a734
ms.sourcegitcommit: e1175aa94709b14b283645986a34a385999fb3f7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/03/2020
ms.locfileid: "93192506"
---
# <a name="tutorial-deploy-from-github-to-azure-kubernetes-service-using-jenkins"></a>Руководство по развертыванию из GitHub в Службе Azure Kubernetes с помощью Jenkins

В рамках этого руководства мы развернем пример приложения из GitHub в [Службе Azure Kubernetes (AKS) на платформе Linux](/azure/aks/intro-kubernetes), настроив непрерывную интеграцию (CI) и непрерывное развертывание (CD) в Jenkins. Таким образом, когда вы решите обновить приложение, отправив фиксации в GitHub, Jenkins автоматически запустит новую сборку контейнера, поместит образы контейнеров в Реестр контейнеров Azure (ACR), а затем запустит приложение в AKS. 

При работе с этим руководством вы выполните следующие задачи:

> [!div class="checklist"]
> * развертывание примера приложения для голосования Azure в кластере AKS;
> * создание базового проекта Jenkins;
> * настройка учетных данных Jenkins для взаимодействия с ACR;
> * создание задания сборки Jenkins и веб-перехватчика GitHub для автоматических сборок;
> * тестирование конвейера CI/CD для обновления приложения в AKS на основе фиксаций кода в GitHub.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим руководством вам потребуется следующее:

- Базовое представление о Kubernetes, Git, непрерывной интеграции и непрерывной поставке, а также об образах контейнеров.

- [Кластер AKS](/azure/aks/kubernetes-walkthrough) и `kubectl`, настроенные с [учетными данными кластера AKS](/cli/azure/aks#az-aks-get-credentials).

- [Реестр службы "Реестр контейнеров Azure" (ACR)](/azure/container-registry/container-registry-get-started-azure-cli), имя сервера ACR для входа и кластер AKS, настроенный для [проверки подлинности в реестре ACR](/azure/aks/cluster-container-registry-integration).

- Установленная и настроенная версия Azure CLI 2.0.46 или более поздняя. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0](/cli/azure/install-azure-cli).

- [Установленное средство Docker](https://docs.docker.com/install/) в системе разработки.

- Учетная запись GitHub, [личный маркер доступа GitHub](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/) и клиент Git, установленный в системе разработки.

- Если вы используете собственный экземпляр Jenkins вместо описанного здесь примера скрипта развертывания Jenkins, для него понадобится [установленное и настроенное средство Docker](https://docs.docker.com/install/), а также [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/).

## <a name="prepare-your-app"></a>Подготовка приложения

Для работы с этой статьей используется пример приложения для голосования Azure, которое содержит веб-интерфейс, размещенный в одном или нескольких pod, и еще один pod с размещенной службой Redis для хранения временных данных. Прежде чем интегрировать Jenkins и AKS для автоматических развертываний, вручную подготовьте и разверните приложение для голосования Azure в кластере AKS. Приложение, развернутое вручную, представляет собой первую версию приложения. Это позволяет увидеть приложение в действии.

> [!NOTE]
> В примере приложения Azure для голосования используется объект pod Linux, для которого запланирован запуск в узле Linux. Поток, описанный в этой статье, можно использовать и для объекта pod Windows Server, для которого запланирован запуск в узле Windows Server.

Создайте вилку следующего репозитория GitHub для примера приложения: [https://github.com/Azure-Samples/azure-voting-app-redis](https://github.com/Azure-Samples/azure-voting-app-redis). Чтобы создать разветвление репозитория для своей учетной записи GitHub, нажмите кнопку **Fork** (Разветвление) в правом верхнем углу.

Клонируйте вилку в свою систему разработки. При клонировании этого репозитория убедитесь, что используется URL-адрес вашей вилки:

```console
git clone https://github.com/<your-github-account>/azure-voting-app-redis.git
```

Перейдите в каталог клонированной вилки:

```console
cd azure-voting-app-redis
```

Чтобы создать образы контейнеров, необходимые для примера приложения, используйте файл *docker-compose.yaml* с командой `docker-compose`:

```console
docker-compose up -d
```

Будут извлечены необходимые базовые образы и созданы контейнеры приложения. Затем можете воспользоваться командой [docker images](https://docs.docker.com/engine/reference/commandline/images/), чтобы просмотреть данные о созданном образе. Было создано или скачано три образа. Образ `azure-vote-front` содержит приложение и использует образ `nginx-flask` в качестве основы. Образ `redis` используется для запуска экземпляра Redis:

```
$ docker images

REPOSITORY                   TAG        IMAGE ID            CREATED             SIZE
azure-vote-front             latest     9cc914e25834        40 seconds ago      694MB
redis                        latest     a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask      788ca94b2313        9 months ago        694MB
```

Прежде чем отправить образ контейнера *azure-vote-front* в ACR, получите данные о сервере входа ACR с помощью команды [az acr list](/cli/azure/acr#az-acr-list). В следующем примере возвращается адрес сервера входа ACR для реестра в группе ресурсов с именем *myResourceGroup* :

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Используйте команду [docker tag](https://docs.docker.com/engine/reference/commandline/tag/), чтобы добавить для образа теги имени сервера входа ACR и номера версии `v1`. Вместо `<acrLoginServer>` укажите имя сервера, полученное на предыдущем шаге.

```console
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v1
```

Наконец, отправьте образ *azure-vote-front* в реестр ACR. Еще раз замените `<acrLoginServer>` именем сервера входа собственного реестра ACR, например `myacrregistry.azurecr.io`:

```console
docker push <acrLoginServer>/azure-vote-front:v1
```

## <a name="deploy-the-sample-application-to-aks"></a>Развертывание примера приложения в AKS

Чтобы развернуть пример приложения в кластере AKS, можно использовать файл манифеста Kubernetes в корне репозитория приложения для голосования Azure. Откройте файл манифеста *azure-vote-all-in-one-redis.yaml* с помощью редактора, например `vi`. Замените `microsoft` именем сервера для входа ACR. Это значение можно найти в строке **47** файла манифеста:

```yaml
containers:
- name: azure-vote-front
  image: azuredocs/azure-vote-front
```

Затем используйте команду [kubectl apply](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply), чтобы развернуть приложение в кластере AKS:

```console
kubectl apply -f azure-vote-all-in-one-redis.yaml
```

Будет создана служба балансировки нагрузки Kubernetes, которая открывает доступ к приложению через Интернет. Это может занять несколько минут. Отслеживать, как выполняется развертывание подсистемы балансировки нагрузки, можно с применением команды [kubectl get service](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) с аргументом `--watch`. Как только *ВНЕШНИЙ IP-АДРЕС* изменится с состояния *ожидания* на *IP-адрес* , используйте команду `Control + C`, чтобы остановить процесс отслеживания kubectl.

```console
$ kubectl get service azure-vote-front --watch

NAME               TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.215.27   <pending>     80:30747/TCP   22s
azure-vote-front   LoadBalancer   10.0.215.27   40.117.57.239   80:30747/TCP   2m
```

Чтобы увидеть приложение в действии, откройте в веб-браузере внешний IP-адрес вашей службы. Отобразится приложение для голосования Azure, как показано в следующем примере:

![Пример приложения для голосования Azure, запущенного в AKS](media/deploy-from-github-to-aks/azure-vote.png)

## <a name="deploy-jenkins-to-an-azure-vm"></a>Развертывание Jenkins на виртуальной машине Azure

Чтобы быстро развернуть Jenkins для использования в рамках этой статьи, можно воспользоваться описанным ниже скриптом для развертывания виртуальной машины Azure, настройки доступа к сети и выполнения базовой установки Jenkins. Для проверки подлинности между Jenkins и кластером AKS этот скрипт копирует файл конфигурации Kubernetes из системы разработки в систему Jenkins.

> [!WARNING]
> Этот сценарий используется в качестве примера для быстрой подготовки среды Jenkins, работающей на виртуальной машине Azure. В нем применяется расширение пользовательских сценариев Azure для настройки виртуальной машины и отображения необходимых учетных данных. Каталог *~/.kube/config* скопирован на виртуальную машину Jenkins.

Выполните следующие команды, чтобы скачать и запустить этот сценарий. Обязательно ознакомьтесь с содержимым того или иного скрипта перед его запуском — [https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh](https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh).

```console
curl https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh > azure-jenkins.sh
sh azure-jenkins.sh
```

Создание виртуальной машины и развертывание необходимых компонентов для Docker и Jenkins занимает несколько минут. После завершения скрипта выводится адрес сервера Jenkins и ключ для разблокировки панели мониторинга, как показано в следующем примере выходных данных:

```
Open a browser to http://40.115.43.83:8080
Enter the following to Unlock Jenkins:
667e24bba78f4de6b51d330ad89ec6c6
```

Откройте в веб-браузере отображенный URL-адрес и введите ключ разблокировки. Следуйте указаниям на экране, чтобы завершить настройку Jenkins:

- Выберите **Install suggested plugins** (Установить предлагаемые подключаемые модули).
- Создайте первого администратора. Введите имя пользователя, например *azureuser* , а затем укажите надежный пароль. Наконец введите полное имя и адрес электронной почты.
- Выберите **Сохранить и завершить**.
- Когда настройка Jenkins будет завершена, щелкните **Start using Jenkins** (Начать работу с Jenkins).
    - Если веб-браузере отображает пустую страницу при запуске Jenkins, перезапустите службу Jenkins. Чтобы перезапустить службу, установите SSH-подключение к общедоступному IP-адресу экземпляра Jenkins и введите `sudo service jenkins restart`. После перезапуска службы обновите веб-браузер.
- Войдите в Jenkins с именем пользователя и паролем, созданными в процессе установки.

## <a name="create-a-jenkins-environment-variable"></a>Создание переменной среды Jenkins

Переменная среды Jenkins используется для хранения имени сервера входа ACR. Эта переменная указывается при выполнении задания сборки Jenkins. Чтобы создать эту переменную среды, выполните следующие действия.

- В левой части портала Jenkins выберите **Manage Jenkins** > **Configure System** (Управление Jenkins > Настройка системы).
- В разделе **Global Properties** (Глобальные свойства) выберите **Environment variables** (Переменные среды). Добавьте переменную с именем `ACR_LOGINSERVER`, а в качестве значения укажите имя сервера входа ACR.

    ![Переменные среды Jenkins](media/deploy-from-github-to-aks/env-variables.png)

- По завершении нажмите кнопку **Save** (Сохранить) внизу страницы конфигурации Jenkins.

## <a name="create-a-jenkins-credential-for-acr"></a>Создание учетных данных Jenkins для ACR

Чтобы разрешить Jenkins выполнить сборку и последующую отправку обновленных образов контейнеров в ACR, необходимо указать учетные данные для ACR. Для этой проверки подлинности можно использовать субъекты-службы Azure Active Directory. В предварительных требованиях вы настроили субъект-службу для кластера AKS с разрешениями *Reader* (Читатель) в реестре ACR. Эти разрешения позволяют кластеру AKS *извлекать* образы из реестра ACR. В процессе CI/CD Jenkins создает новые образы контейнеров на основе обновлений приложения, после чего необходимо *отправить* эти образы в реестр ACR. Для разделения ролей и разрешений теперь следует настроить субъект-службу для Jenkins с разрешениями *Contributor* (Участник) в реестре ACR.

### <a name="create-a-service-principal-for-jenkins-to-use-acr"></a>Создание субъекта-службы для использования ACR в Jenkins

Сначала создайте субъект-службу с помощью команды [az ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac):

```azurecli
$ az ad sp create-for-rbac --skip-assignment

{
  "appId": "626dd8ea-042d-4043-a8df-4ef56273670f",
  "displayName": "azure-cli-2018-09-28-22-19-34",
  "name": "http://azure-cli-2018-09-28-22-19-34",
  "password": "1ceb4df3-c567-4fb6-955e-f95ac9460297",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db48"
}
```

Запишите значения параметров *appId* и *password* , показанные в выходных данных. Эти значения используются в следующих шагах для настройки ресурса учетных данных в Jenkins.

Получите идентификатор ресурса для реестра ACR с помощью команды [az acr show](/cli/azure/acr#az-acr-show) и сохраните его как переменную. Укажите имя группы ресурсов и имя ACR.

```azurecli
ACR_ID=$(az acr show --resource-group myResourceGroup --name <acrLoginServer> --query "id" --output tsv)
```

Теперь создайте назначение ролей, чтобы назначить субъекту-службе права *Contributor* (Участник) в реестре ACR. В следующем примере укажите собственный *идентификатор приложения* , показанный в выходных данных предыдущей команды для создания субъекта-службы:

```azurecli
az role assignment create --assignee 626dd8ea-042d-4043-a8df-4ef56273670f --role Contributor --scope $ACR_ID
```

### <a name="create-a-credential-resource-in-jenkins-for-the-acr-service-principal"></a>Создание ресурса учетных данных в Jenkins для субъекта-службы ACR

Создав назначения ролей в Azure, сохраните учетные данные ACR в объекте учетных данных Jenkins. Эти учетные данные указываются во время выполнения задания сборки Jenkins.

В левой части портала Jenkins и выберите **Credentials** > **Jenkins** > **Global credentials (unrestricted)**  > **Add Credentials** (Учетные данные > Jenkins > Глобальные учетные данные (без ограничений) > Добавить учетные данные).

Убедитесь, что выбран тип учетных данных **Username with password** (Имя пользователя с паролем), и введите следующие данные.

- **Username** (Имя пользователя) — значение параметра *appId* субъекта-службы, созданного для проверки подлинности в реестре ACR.
- **Password** (Пароль) — значение параметра *password* субъекта-службы, созданного для проверки подлинности в реестре ACR.
- **ID** (Идентификатор) — идентификатор учетных данных, например *acr-credentials*.

По завершении форма учетных данных будет выглядеть примерно так:

![Создание объекта учетных данных Jenkins с данными субъекта-службы](media/deploy-from-github-to-aks/acr-credentials.png)

Нажмите кнопку **ОК** и вернитесь на портал Jenkins.

## <a name="create-a-jenkins-project"></a>Создание проекта Jenkins

На домашней странице портала Jenkins выберите **New item** (Создать элемент) с левой стороны:

1. Введите *azure-vote* в качестве имени задания. Выберите **Freestyle project** (Универсальный проект) и нажмите кнопку **ОК**.
1. В разделе **General** (Общие) выберите проект **GitHub** и введите URL-адрес вилки репозитория, например *https:\//github.com/\<your-github-account\>/azure-voting-app-redis*.
1. В разделе **Source code management** (Управление исходным кодом) выберите **Git** и введите URL-адрес *GIT-файла* вилки репозитория, например *https:\//github.com/\<your-github-account\>/azure-voting-app-redis.git*.

1. В разделе **Build Triggers** (Создание триггеров) выберите **GitHub hook trigger for GITScm polling** (Обработчик триггера GitHub для опроса GITScm).
1. В разделе **Build Environment** (Среда сборки) выберите **Use secret texts or files** (Использовать секретные тексты или файлы).
1. В разделе **Bindings** (Привязки) выберите **Add** > **Username and password (separated)** (Добавить > Имя пользователя и пароль (отдельно)).
   - Введите `ACR_ID` в поле **Username Variable** (Переменная имени пользователя) и `ACR_PASSWORD` — в поле **Password Variable** (Переменная пароля).

     ![Привязки Jenkins](media/deploy-from-github-to-aks/bindings.png)

1. Добавьте **Build Step** (Шаг сборки) типа **Execute shell** (Запустить оболочку) и используйте следующий текст. Этот сценарий создает новый образ контейнера и отправляет его в реестр ACR.

    ```bash
    # Build new image and push to ACR.
    WEB_IMAGE_NAME="${ACR_LOGINSERVER}/azure-vote-front:kube${BUILD_NUMBER}"
    docker build -t $WEB_IMAGE_NAME ./azure-vote
    docker login ${ACR_LOGINSERVER} -u ${ACR_ID} -p ${ACR_PASSWORD}
    docker push $WEB_IMAGE_NAME
    ```

1. Добавьте другой **шаг сборки** типа **Execute shell** (Запустить оболочку) и используйте следующий текст. Этот скрипт обновляет развертывание приложения в AKS новым образом контейнера из ACR.

    ```bash
    # Update kubernetes deployment with new image.
    WEB_IMAGE_NAME="${ACR_LOGINSERVER}/azure-vote-front:kube${BUILD_NUMBER}"
    kubectl set image deployment/azure-vote-front azure-vote-front=$WEB_IMAGE_NAME --kubeconfig /var/lib/jenkins/config
    ```

1. После завершения щелкните **Save** (Сохранить).

## <a name="test-the-jenkins-build"></a>Тестирование сборки Jenkins

Прежде чем автоматизировать задание на основе фиксаций GitHub, вручную протестируйте сборку Jenkins. Это позволяет проверить, правильно ли настроено задание сборки, имеется ли соответствующий файл проверки подлинности Kubernetes и работает ли проверка подлинности с помощью ACR.

В левом меню проекта выберите **Build Now** (Выполнить сборку).

![Тестовая сборка Jenkins](media/deploy-from-github-to-aks/test-build.png)

Первая сборка занимает минуту или две, пока слои образа Docker извлекаются на сервер Jenkins. При последующих сборках могут использоваться кэшированные слои образа для сокращения времени сборки.

В процессе сборки репозиторий GitHub клонируется на сервер сборки Jenkins. Создается новый образ контейнера, который отправляется в реестр ACR. Наконец, приложение для голосования Azure в кластере AKS обновляется для использования нового образа. Так как код приложения не был изменен, приложение не изменится при просмотре примера приложения в веб-браузере.

Когда завершится задание сборки, щелкните **build #1** (сборка № 1) в журнале сборки. Выберите **Console Output** (Выходные данные консоли) и просмотрите выходные данные процесса сборки. Последняя строка должна указывать, что сборка выполнена успешно.

## <a name="create-a-github-webhook"></a>Создание веб-перехватчика GitHub

После успешного выполнения ручной сборки теперь следует интегрировать GitHub в сборку Jenkins. Веб-перехватчик позволяет запускать задание сборки Jenkins каждый раз, когда в GitHub выполняется фиксация кода. Чтобы создать веб-перехватчик GitHub, выполните следующие действия.

1. Перейдите к вилке репозитория GitHub в веб-браузере.
1. Щелкните **Settings** (Параметры), а затем выберите **Webhooks** (Веб-перехватчики).
1. Выберите **Add webhook** (Добавить веб-перехватчик). В поле *Payload URL* (URL-адрес полезных данных) введите `http://<publicIp:8080>/github-webhook/`, где `<publicIp>` — IP-адрес сервера Jenkins. Убедитесь, что адрес содержит завершающую косую черту (/). Оставьте другие значения по умолчанию для типа содержимого и триггера событий *принудительной отправки*.
1. Выберите **Add webhook** (Добавить веб-перехватчик).

    ![Создание веб-перехватчика GitHub для Jenkins](media/deploy-from-github-to-aks/webhook.png)

## <a name="test-the-complete-cicd-pipeline"></a>Тестирование всего конвейера CI/CD

Теперь можно протестировать весь конвейер CI/CD. При отправке фиксации кода в GitHub происходит следующее:

1. Веб-перехватчик GitHub связывается с Jenkins.
1. Jenkins запускает задание сборки и получает данные о последней фиксации кода из GitHub.
1. Запускается сборка Docker с использованием обновленного кода, и новый образ контейнера помечается тегом с номером последней сборки.
1. Этот новый образ контейнера отправляется в Реестр контейнеров Azure.
1. Ваше приложение, развернутое в службе Azure Kubernetes, обновляется последним образом контейнера из реестра службы "Реестр контейнеров Azure".

На компьютере разработки откройте клонированное приложении с помощью редактора кода. В каталоге */azure-vote/azure-vote* откройте файл **config_file.cfg**. Обновите значения для голосования в этом файле, указав что-нибудь иное вместо кошек и собак, как показано в следующем примере:

```
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Blue'
VOTE2VALUE = 'Purple'
SHOWHOST = 'false'
```

После обновления сохраните файл, зафиксируйте изменения и отправьте их в вилку репозитория GitHub. Веб-перехватчик GitHub активирует новое задание сборки в Jenkins. Наблюдайте за процессом сборки в веб-панели мониторинга Jenkins. Извлечение последней версии кода, создание и отправка обновленного образа и развертывание обновленного приложения в AKS занимают несколько секунд.

После завершения сборки обновите страницу веб-браузера с примером приложения для голосования Azure. Отобразятся внесенные изменения, как показано в следующем примере:

![Пример приложения для голосования Azure в AKS, обновленный с помощью задания сборки Jenkins](media/deploy-from-github-to-aks/azure-vote-updated.png)

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Jenkins в Azure](/azure/jenkins)
