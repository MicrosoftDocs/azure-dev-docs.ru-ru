---
title: Руководство. Создание конвейера Jenkins с помощью GitHub и Docker
description: Узнайте, как создать виртуальную машину Jenkins в Azure, которая получает данные из GitHub при каждой фиксации кода и создает новый контейнер Docker для выполнения приложения.
keywords: Jenkins, Azure, DevOps, конвейер, CI/CD, Docker
ms.topic: tutorial
ms.date: 03/27/2017
ms.custom: devx-track-jenkins
ms.openlocfilehash: 2a9bc23852a04b42b72628adda116585d354f860
ms.sourcegitcommit: 16ce1d00586dfa9c351b889ca7f469145a02fad6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/14/2020
ms.locfileid: "88240706"
---
# <a name="tutorial-create-a-jenkins-pipeline-using-github-and-docker"></a>Руководство по созданию конвейера Jenkins с помощью GitHub и Docker

Чтобы автоматизировать этапы создания и тестирования приложения, вы можете использовать конвейер непрерывной интеграции и развертывания (CI/CD). В этом учебнике мы создадим конвейер CI/CD на виртуальной машине Azure, включая следующие задачи:

> [!div class="checklist"]
> * Создание виртуальной машины Jenkins
> * Установка и настройка Jenkins
> * Создание интеграции webhook между GitHub и Jenkins
> * Создание и активация заданий сборки Jenkins из фиксаций GitHub
> * Создание образа Docker для приложения
> * Проверка создания фиксацией GitHub образа Docker и изменения выполняющегося приложения

При работе с этим руководством используется интерфейс командной строки (CLI) в [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview), который всегда обновлен до последней версии. Чтобы открыть Cloud Shell, выберите **Попробовать** в верхнем углу любого блока кода.

Если вы решили установить и использовать интерфейс командной строки локально, то для работы с этим руководством вам понадобится Azure CLI 2.0.30 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="create-jenkins-instance"></a>Создание экземпляра Jenkins
В предыдущем руководстве [Как настроить виртуальную машину Linux при первой загрузке](/azure/virtual-machines/linux/tutorial-automate-vm-deployment) вы узнали, как автоматизировать настройку виртуальной машины с помощью cloud-init. В этом учебнике используется файл cloud-init для установки Jenkins и Docker на виртуальной машине. Jenkins — это популярный сервер автоматизации с открытым исходным кодом, который легко интегрируется с Azure для обеспечения непрерывной интеграции и непрерывной поставки (CI/CD). Другие руководства по использованию Jenkins см. в документации [Jenkins® в Azure](https://docs.microsoft.com/azure/jenkins/).

В текущей оболочке создайте файл *cloud-init-jenkins.txt* и вставьте в него приведенную ниже конфигурацию. Например, создайте файл в Cloud Shell, не на локальном компьютере. Введите `sensible-editor cloud-init-jenkins.txt`, чтобы создать файл и просмотреть список доступных редакторов. Убедитесь, что весь файл cloud-init скопирован правильно, особенно первая строка:

```yaml
#cloud-config
package_upgrade: true
write_files:
  - path: /etc/systemd/system/docker.service.d/docker.conf
    content: |
      [Service]
        ExecStart=
        ExecStart=/usr/bin/dockerd
  - path: /etc/docker/daemon.json
    content: |
      {
        "hosts": ["fd://","tcp://127.0.0.1:2375"]
      }
runcmd:
  - apt install openjdk-8-jre-headless -y
  - wget -q -O - https://pkg.jenkins.io/debian/jenkins-ci.org.key | sudo apt-key add -
  - sh -c 'echo deb https://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
  - apt-get update && apt-get install jenkins -y
  - curl -sSL https://get.docker.com/ | sh
  - usermod -aG docker azureuser
  - usermod -aG docker jenkins
  - service jenkins restart
```

Прежде чем создать виртуальную машину, выполните команду [az group create](/cli/azure/group), чтобы создать группу ресурсов. В следующем примере создается группа ресурсов с именем *myResourceGroupJenkins* в расположении *eastus*.

```azurecli-interactive 
az group create --name myResourceGroupJenkins --location eastus
```

Теперь создайте виртуальную машину командой [az vm create](/cli/azure/vm). Используйте параметр `--custom-data`, чтобы передать файл конфигурации cloud-init. Укажите полный путь к конфигурации *cloud-init-jenkins.txt*, если этот файл сохранен вне текущего рабочего каталога.

```azurecli-interactive 
az vm create --resource-group myResourceGroupJenkins \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init-jenkins.txt
```

Создание и настройка виртуальной машины может занять несколько минут.

Чтобы разрешить веб-трафик к вашей виртуальной машине, используйте команду [az vm open-port](/cli/azure/vm) для открытия порта *8080* для трафика Jenkins и порта *1337* для приложения Node.js, которое используется для запуска примера приложения:

```azurecli-interactive 
az vm open-port --resource-group myResourceGroupJenkins --name myVM --port 8080 --priority 1001
az vm open-port --resource-group myResourceGroupJenkins --name myVM --port 1337 --priority 1002
```


## <a name="configure-jenkins"></a>Настройка Jenkins
Чтобы получить доступ к экземпляру Jenkins, получите общедоступный IP-адрес виртуальной машины:

```azurecli-interactive 
az vm show --resource-group myResourceGroupJenkins --name myVM -d --query [publicIps] --o tsv
```

В целях безопасности для запуска установки Jenkins необходимо ввести первоначальный пароль администратора, хранящийся в текстовом файле на виртуальной машине. Используйте общедоступный IP-адрес, полученный на предыдущем шаге, чтобы настроить подключение SSH для виртуальной машины:

```bash
ssh azureuser@<publicIps>
```

С помощью команды `service` проверьте, запущен ли сервер Jenkins:

```bash
$ service jenkins status
● jenkins.service - LSB: Start Jenkins at boot time
   Loaded: loaded (/etc/init.d/jenkins; generated)
   Active: active (exited) since Tue 2019-02-12 16:16:11 UTC; 55s ago
     Docs: man:systemd-sysv-generator(8)
    Tasks: 0 (limit: 4103)
   CGroup: /system.slice/jenkins.service

Feb 12 16:16:10 myVM systemd[1]: Starting LSB: Start Jenkins at boot time...
...
```

Просмотрите `initialAdminPassword` для установки Jenkins и скопируйте его:

```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

Если файл еще не доступен, подождите несколько минут, пока файл cloud-init не завершит установку Jenkins и Docker.

Теперь откройте браузер и перейдите к `http://<publicIps>:8080`. Выполните начальную настройку Jenkins следующим образом:

- Щелкните **Select plugins to install** (Выбрать подключаемые модули для установки).
- Выполните поиск по слову *GitHub* в текстовом поле вверху. Установите флажок для *GitHub*, а затем выберите **Установить**.
- Создайте первого администратора. Введите имя пользователя, например **admin**, а затем укажите безопасный пароль. Наконец введите полное имя и адрес электронной почты.
- Выберите **Сохранить и завершить**.
- Когда настройка Jenkins будет завершена, щелкните **Start using Jenkins** (Начать работу с Jenkins).
  - Если веб-браузере отображает пустую страницу при запуске Jenkins, перезапустите службу Jenkins. В сеансе SSH введите `sudo service jenkins restart`, а затем обновите веб-браузера.
- При необходимости войдите в Jenkins, введя созданные имя пользователя и пароль.


## <a name="create-github-webhook"></a>Создание объекта webhook GitHub
Чтобы настроить интеграцию с GitHub, откройте [пример приложения Node.js Hello World](https://github.com/Azure-Samples/nodejs-docs-hello-world) из репозитория примеров Azure. Чтобы создать разветвление репозитория для своей учетной записи GitHub, нажмите кнопку **Fork** (Разветвление) в правом верхнем углу.

Создание объекта webhook в созданном разветвлении

- Щелкните **Settings** (Параметры), а затем выберите **Webhooks** (Веб-перехватчики).
- Нажмите кнопку **Add webhook** (Добавить веб-перехватчик) и в поле фильтра введите *Jenkins*.
- В поле **URL-адреса полезных данных** введите `http://<publicIps>:8080/github-webhook/`. Убедитесь, что адрес содержит завершающую косую черту (/).
- В поле **типа содержимого** выберите *application/x-www-form-urlencoded*.
- Для параметра **Which events would you like to trigger this webhook?** (Какие события должен активировать этот веб-перехватчик?) выберите *Just the push event* (Только событие отправки).
- Установите флажок **Active** (Активно).
- Щелкните **Add webhook** (Добавить веб-перехватчик).

![Добавление объекта webhook GitHub в разветвление репозитория](media/pipeline-with-github-and-docker/github-webhook.png)


## <a name="create-jenkins-job"></a>Создание задания Jenkins
Чтобы система Jenkins реагировала на событие в GitHub, такое как фиксация кода, создайте задание Jenkins. Используйте URL-адреса для собственной вилки GitHub.

На веб-сайте Jenkins щелкните **Create new jobs** (Создание заданий) на домашней странице.

- Введите *HelloWorld* в качестве имени задания. Выберите **Freestyle project** (Универсальный проект) и нажмите кнопку **ОК**.
- В разделе **Общие** выберите проект **GitHub** и введите URL-адрес вилки репозитория, например `https://github.com/cynthn/nodejs-docs-hello-world`.
- В разделе **Управление исходным кодом** выберите **Git** и введите URL-адрес *GIT-файла* вилки репозитория, например `https://github.com/cynthn/nodejs-docs-hello-world.git`.
- В разделе **Build Triggers** (Создание триггеров) выберите **GitHub hook trigger for GITScm polling** (Обработчик триггера Github для опроса GITScm).
- В разделе **Build** (Сборка) щелкните **Add build step** (Добавить шаг сборки). Выберите **Execute shell** (Выполнение оболочки), затем введите `echo "Test"` в командном окне.
- В нижней части окна заданий нажмите кнопку **Save** (Сохранить).


## <a name="test-github-integration"></a>Тестирование интеграции GitHub
Для проверки интеграции GitHub с Jenkins зафиксируйте изменение в разветвлении. 

Вернитесь к веб-интерфейсу пользователя GitHub, выберите разветвление репозитория и щелкните файл **index.js**. Щелкните значок карандаша и измените этот файл так, чтобы строка 6 выглядела следующим образом.

```javascript
response.end("Hello World!");
```

Чтобы зафиксировать изменения, нажмите кнопку **Commit changes** (Зафиксировать изменения) внизу.

В Jenkins запускается новая сборка в разделе **Build history** (Журнал сборок) в левом нижнем углу страницы задания. Щелкните ссылку с номером сборки и выберите **Console output** (Вывод консоли) слева. Вы можете просмотреть действия, выполняемые Jenkins по мере получения кода из GitHub и вывода действием сборки сообщения `Test` на консоли. Каждый раз, когда в GitHub выполняется фиксация, веб-перехватчик достигает Jenkins и активирует новый процесс сборки.


## <a name="define-docker-build-image"></a>Определение образа сборки Docker
Чтобы увидеть, как приложение Node.js выполняется в зависимости от фиксаций GitHub, мы создадим образ Docker для выполнения приложения. Образ строится на основе файла Dockerfile, определяющего конфигурацию контейнера, в котором выполняется приложение. 

Измените путь SSH-подключения к виртуальной машине, задав каталог рабочей области Jenkins, имя которого соответствует заданию, созданному на предыдущем шаге. В текущем примере это будет *HelloWorld*.

```bash
cd /var/lib/jenkins/workspace/HelloWorld
```

Создайте файл в этом каталоге рабочей области с `sudo sensible-editor Dockerfile` и вставьте следующее содержимое. Убедитесь, что весь файл Dockerfile скопирован правильно, особенно первая строка:

```yaml
FROM node:alpine

EXPOSE 1337

WORKDIR /var/www
COPY package.json /var/www/
RUN npm install
COPY index.js /var/www/
```

Этот файл Dockerfile использует базовый образ Node.js с помощью Alpine Linux, предоставляет порт 1337, по которому выполняется приложение Hello World, а затем копирует файлы приложения и инициализирует его.


## <a name="create-jenkins-build-rules"></a>Создание правил сборки Jenkins
На предыдущем шаге мы создали базовое правило сборки Jenkins, которое выводит сообщения на консоль. Теперь создадим шаг сборки, который будет использовать наш Dockerfile и запускать приложение.

Вернитесь в экземпляр Jenkins и выберите задание, созданное на предыдущем шаге. Щелкните **Configure** (Настройка) в левой части и прокрутите страницу вниз до раздела **Build** (Сборка).

- Удалите существующий шаг сборки `echo "Test"`. Щелкните красный крест в верхнем правом углу поля существующего шага сборки.
- Щелкните **Add build step** (Добавить шаг сборки), а затем выберите **Execute shell** (Выполнение оболочки).
- В поле **Command** (Команда) введите следующие команды Docker. Затем нажмите кнопку **Save** (Сохранить):

  ```bash
  docker build --tag helloworld:$BUILD_NUMBER .
  docker stop helloworld && docker rm helloworld
  docker run --name helloworld -p 1337:1337 helloworld:$BUILD_NUMBER node /var/www/index.js &
  ```

Шаги сборок Docker создают образ и помечают его номером сборки Jenkins, чтобы вы могли вести журнал образов. Любые существующие контейнеры, выполняющие приложение, будут остановлены, а затем удалены. Затем с помощью образа запускается новый контейнер, который выполняет ваше приложение Node.js в зависимости от последней фиксации в GitHub.


## <a name="test-your-pipeline"></a>Тестирование конвейера
Чтобы увидеть весь конвейер в действии, снова измените файл *index.js* в разветвлении репозитория GitHub и нажмите кнопку **Commit changes** (Зафиксировать изменения). Новое задание запускается Jenkins в зависимости от объекта webhook для GitHub. Создание образа Docker и запуск вашего приложения в новом контейнере занимает несколько секунд.

При необходимости снова получите общедоступный IP-адрес виртуальной машины:

```azurecli-interactive 
az vm show --resource-group myResourceGroupJenkins --name myVM -d --query [publicIps] --o tsv
```

Откройте веб-браузер и введите `http://<publicIps>:1337`. Приложение Node.js отображается и отражает последние фиксации в разветвлении GitHub следующим образом:

![Запуск приложения Node.js](media/pipeline-with-github-and-docker/running-nodejs-app.png)

Теперь внесите другое изменение в файл *index.js* в GitHub и зафиксируйте изменение. Подождите несколько секунд, пока завершится задание в Jenkins, а затем обновите веб-браузер, чтобы увидеть измененную версию приложения, выполняющегося в новом контейнере, следующим образом:

![Запуск приложения Node.js после другой фиксации GitHub](media/pipeline-with-github-and-docker/another-running-nodejs-app.png)


## <a name="next-steps"></a>Дальнейшие действия
В рамках этого учебника мы настроили GitHub для выполнения задания сборки Jenkins согласно каждой фиксации кода, а затем развернули контейнер Docker для тестирования приложения. Вы ознакомились с выполнением следующих задач:

> [!div class="checklist"]
> * Создание виртуальной машины Jenkins
> * Установка и настройка Jenkins
> * Создание интеграции webhook между GitHub и Jenkins
> * Создание и активация заданий сборки Jenkins из фиксаций GitHub
> * Создание образа Docker для приложения
> * Проверка создания фиксацией GitHub образа Docker и изменения выполняющегося приложения

Перейдите к следующему руководству, чтобы узнать, как интегрировать Jenkins в Azure DevOps Services.

> [!div class="nextstepaction"]
> [Руководство. Развертывание приложения на виртуальных машинах Linux в Azure с помощью Jenkins и Visual Studio Team Services](deploy-to-linux-vm-using-azure-devops-services.md)