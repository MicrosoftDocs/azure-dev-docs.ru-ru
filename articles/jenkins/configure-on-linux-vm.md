---
title: Краткое руководство. Создание сервера Jenkins в Azure
description: Узнайте, как установить Jenkins на виртуальной машине Azure под управлением Linux на основе шаблона решения Jenkins и создать пример приложения Java.
keywords: Jenkins, Azure, DevOps, портал, Linux, виртуальная машина, шаблон решения
ms.topic: quickstart
ms.date: 03/03/2020
ms.openlocfilehash: 2ea038dad2294784804f45026ea26198a9b12d79
ms.sourcegitcommit: 9ff9b51ab21c93bfd61e480c6ff8e39c9d4bf02e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/27/2020
ms.locfileid: "82170340"
---
# <a name="quickstart-create-a-jenkins-server-on-an-azure-linux-vm"></a>Краткое руководство. Создание сервера Jenkins на виртуальной машине Azure под управлением Linux.

В этом кратком руководстве описано, как установить [Jenkins](https://jenkins.io) на виртуальной машине под управлением Ubuntu Linux с помощью средств и подключаемых модулей, настроенных для работы с Azure. Мы создадим в Azure сервер Jenkins для сборки образца приложения Java из [GitHub](https://github.com).

## <a name="prerequisites"></a>Предварительные требования

* Доступ к SSH в командной строке компьютера (например, в оболочке Bash или [PuTTY](https://www.putty.org/))

## <a name="create-the-jenkins-vm-from-the-solution-template"></a>Создание виртуальной машины Jenkins на основе шаблона решения

Jenkins поддерживает модель, в которой делегаты сервера Jenkins работают с одним или несколькими агентами, чтобы позволить одной установке Jenkins размещать большое количество проектов или предоставлять различные среды, необходимые для сборки или тестирования. Шаги, представленные в этом разделе, помогут вам установить и настроить сервер Jenkins в Azure.

1. В браузере откройте [образ Azure Marketplace для Jenkins](https://azuremarketplace.microsoft.com/marketplace/apps/azure-oss.jenkins?tab=Overview).

1. Выберите **Загрузить сейчас**.

    ![Выберите "Загрузить сейчас", чтобы начать процесс установки образа Jenkins Marketplace.](./media/install-from-azure-marketplace-image/jenkins-install-get-it-now.png)

1. Просмотрев информацию о ценах и условиях, выберите **Продолжить**.

    ![Сведения о ценах и условиях образов Jenkins Marketplace.](./media/install-from-azure-marketplace-image/jenkins-install-pricing-and-terms.png)

1. Чтобы настроить сервер Jenkins на портале Azure, выберите **Создать**. 

    ![Установите образ Jenkins Marketplace.](./media/install-from-azure-marketplace-image/jenkins-install-create.png)

1. На вкладке **Основные сведения** укажите следующие значения:

   - **Name** (Имя). Введите `Jenkins`.
   - **Имя пользователя.** Введите имя пользователя для входа на виртуальную машину, на которой запущен сервер Jenkins. Имя пользователя должно соответствовать [особым требованиям](/azure/virtual-machines/linux/faq#what-are-the-username-requirements-when-creating-a-vm).
   - Для параметра **Тип проверки подлинности** выберите значение **Открытый ключ SSH**.
   - **Открытый ключ SSH.** Скопируйте и вставьте в это поле открытый ключ RSA в однострочном формате (начиная с `ssh-rsa`) или в формате многострочного текста PEM. Ключи SSH можно создать с помощью ssh-keygen (в Linux и macOS) или PuTTYGen (в Windows). Дополнительные сведения см. в статье об [использовании ключей SSH с Windows в Azure](/azure/virtual-machines/linux/ssh-from-windows).
   - **Подписка**. Выберите подписку Azure, в которой необходимо установить Jenkins.
   - **Группа ресурсов**. Выберите **Создать новый** и введите имя для группы ресурсов, которая используется как логический контейнер для коллекции ресурсов, которые составляют установку Jenkins.
   - **Расположение**. Выберите **Восточная часть США**.

     ![Введите сведения о проверке подлинности и группе ресурсов для Jenkins на вкладке "Основные сведения".](./media/install-from-azure-marketplace-image/jenkins-configure-basic.png)

1. Щелкните **ОК**, чтобы открыть вкладку **Дополнительные параметры**. 

1. На вкладке **Дополнительные параметры** укажите следующие значения:

   - **Размер**. Выберите соответствующий вариант для виртуальной машины Jenkins.
   - **Тип диска виртуальной машины**. Выберите тип диска, HDD (жесткий диск) или SSD (твердотельный накопитель), чтобы указать тип диска хранилища, разрешенный для виртуальных машин Jenkins.
   - **Виртуальная сеть** (необязательно) — выберите **виртуальную сеть**, чтобы применить ее вместо настроек по умолчанию.
   - **Подсети** — выберите **подсети**, проверьте информацию о них и щелкните **ОК**.
   - **Общедоступный IP-адрес**. Для имени IP-адреса по умолчанию присваивается имя Jenkins, указанное на предыдущей странице с суффиксом IP. Вы можете изменить это значение по умолчанию.
   - **Метка доменного имени**. Укажите значение полного URL-адреса для виртуальной машины Jenkins.
   - **Тип выпуска Jenkins** — выберите нужный тип выпуска (доступны следующие варианты: `LTS`, `Weekly build` или `Azure Verified`). Варианты `LTS` и `Weekly build` описаны в статье [о строке выпуска Jenkins LTS](https://jenkins.io/download/lts/). Вариант `Azure Verified` указывает на [версию Jenkins LTS](https://jenkins.io/download/lts/), которая проверена на совместимость с Azure. 
   - **Тип JDK**. Требуемый тип JDK для установки. По умолчанию используются протестированные и сертифицированные сборки Zulu OpenJDK.

     ![Введите параметры виртуальной машины Jenkins на вкладке "Параметры".](./media/install-from-azure-marketplace-image/jenkins-configure-settings.png)

1. Щелкните **ОК**, чтобы открыть вкладку **Параметры интеграции**.

1. На вкладке **Параметры интеграции** укажите следующие значения:

    - **Субъект-служба** — добавленный в Jenkins субъект-служба применяется как учетные данные для аутентификации в Azure. Вариант `Auto` означает, что этот субъект будет создан службой MSI (управляемые удостоверения службы). Вариант `Manual` означает, что вы сами создадите этот субъект. 
        - **Идентификатор приложения** и **Секрет** — если для параметра **Субъект-служба** выбран вариант `Manual`, то для этого субъекта-службы нужно указать `Application ID` и `Secret`. Когда вы [создаете субъект-службу](/cli/azure/create-an-azure-service-principal-azure-cli), учитывайте, что по умолчанию назначается роль **участника**, которой вполне достаточно для работы с ресурсами Azure.
    - **Включить агенты облака** — укажите облачный шаблон по умолчанию для агентов, где `ACI` обозначает экземпляр контейнера Azure, а `VM` относится к виртуальным машинам. Также здесь можно указать `No`, если вы не хотите включать агент облака.

1. Нажмите кнопку **ОК**, чтобы перейти на вкладку **Сводка**.

1. Когда отобразится вкладка **Сводка**, проверьте введенные данные. Когда в верхней части вкладки появится сообщение **Проверка пройдена**, нажмите кнопку **ОК**. 

     ![Отобразится вкладка "Сводка", и будет выполнена проверка выбранных параметров.](./media/install-from-azure-marketplace-image/jenkins-configure-summary.png)

1. Когда отобразится вкладка **Создание**, выберите **Создать**, чтобы создать виртуальную машину Jenkins. Когда сервер будет готов, отобразится уведомление на портале Azure.

     ![Уведомление о готовности Jenkins.](./media/install-from-azure-marketplace-image/jenkins-install-notification.png)

## <a name="connect-to-jenkins"></a>Подключение к Jenkins

1. Перейдите к виртуальной машине (например, `http://jenkins2517454.eastus.cloudapp.azure.com/`) в веб-браузере. Консоль Jenkins недоступна по незащищенному протоколу HTTP, поэтому на странице приведены инструкции по безопасному доступу к консоли Jenkins с компьютера с использованием туннеля SSH.

    ![Разблокировка Jenkins](./media/install-solution-template-steps/jenkins-ssh-instructions.png)

1. Настройте туннель на странице из командной строки, используя команду `ssh`. Для этого замените `username` именем администратора виртуальной машины, указанным ранее при настройке виртуальной машины на основе шаблона решения.

    ```bash
    ssh -L 127.0.0.1:8080:localhost:8080 jenkinsadmin@jenkins2517454.eastus.cloudapp.azure.com
    ```
    
1. После запуска туннеля перейдите по адресу `http://localhost:8080/` с локального компьютера. 

1. Получите первоначальный пароль, выполнив команду ниже в командной строке при подключении к виртуальной машине Jenkins через SSH.

    ```bash
    sudo cat /var/lib/jenkins/secrets/initialAdminPassword
    ```
    
1. При первом использовании разблокируйте панель мониторинга Jenkins с помощью этого пароля.

    ![Разблокировка Jenkins](./media/install-solution-template-steps/jenkins-unlock.png)

1. Выберите **Install suggested plugins** (Установить предлагаемые подключаемые модули) на следующей странице и создайте учетную запись администратора Jenkins для доступа к панели мониторинга Jenkins.

    ![Экземпляр Jenkins готов!](./media/install-solution-template-steps/jenkins-welcome.png)

Сервер Jenkins готов к созданию кода.

## <a name="create-your-first-job"></a>Создание первого задания

1. Выберите **Create new jobs** (Создать задания) в консоли Jenkins. Назовите задание **mySampleApp**, выберите **Freestyle project** (Универсальный проект) и нажмите **ОК**.

    ![Создание задания](./media/install-solution-template-steps/jenkins-new-job.png) 

1. Выберите вкладку **Управление исходным кодом**, включите **Git** и введите следующий URL-адрес в поле **URL-адрес репозитория**: `https://github.com/spring-guides/gs-spring-boot.git`

    ![Определение репозитория Git](./media/install-solution-template-steps/jenkins-job-git-configuration.png) 

1. Выберите вкладку **Создание**, нажмите **Добавление шага сборки** и **Invoke Gradle script** (Вызов сценария Gradle). Выберите **Use Gradle Wrapper** (Использовать программу-оболочку Gradle), затем введите значение `complete` для параметра **Wrapper location** (Расположение программы-оболочки) и `build` для параметра **Задачи**.

    ![Использование программы-оболочки Gradle для сборки](./media/install-solution-template-steps/jenkins-job-gradle-config.png) 

1. Выберите **Advanced** (Дополнительно) и введите `complete` в поле **Root Build script** (Корневой скрипт сборки). Щелкните **Сохранить**.

    ![Установка дополнительных параметров на этапе сборки программы-оболочки Gradle](./media/install-solution-template-steps/jenkins-job-gradle-advances.png) 

## <a name="build-the-code"></a>Сборка кода

1. Выберите **Build Now** (Собрать сейчас), чтобы скомпилировать код и создать пакет для примера приложения. По завершении сборки выберите для проекта ссылку **Рабочая область**.

    ![Перейдите в рабочую область, чтобы получить JAR-файл из сборки](./media/install-solution-template-steps/jenkins-access-workspace.png) 

1. Перейдите к `complete/build/libs` и проверьте наличие `gs-spring-boot-0.1.0.jar`, чтобы удостовериться в успешности сборки. Теперь сервер Jenkins готов к сборке ваших собственных проектов в Azure.

## <a name="troubleshooting-the-jenkins-solution-template"></a>Устранение неполадок шаблона решения Jenkins

Если вы столкнулись с ошибками, которые касаются шаблона решения Jenkins, сообщите о проблеме в [репозиторий Jenkins на GitHub](https://github.com/azure/jenkins/issues).

## <a name="next-steps"></a>Next Steps

> [!div class="nextstepaction"]
> [Jenkins в Azure](/azure/developer/jenkins)