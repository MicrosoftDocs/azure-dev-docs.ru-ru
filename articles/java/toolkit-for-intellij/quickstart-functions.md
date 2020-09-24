---
title: Создание первой функции в Azure с помощью IntelliJ IDEA
description: Создание и публикация в Azure простой функции, активируемой по HTTP, с помощью набора средств Azure для IntelliJ.
ms.topic: quickstart
ms.date: 03/26/2020
ms.custom: devx-track-java
ms.openlocfilehash: b27655657d763e295a1817acf200a67c090ff67d
ms.sourcegitcommit: 39f3f69e3be39e30df28421a30747f6711c37a7b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/21/2020
ms.locfileid: "90831410"
---
# <a name="quickstart-create-an-azure-functions-project-using-intellij-idea"></a>Краткое руководство. Создание проекта Функций Azure с помощью IntelliJ IDEA

В этой статье IntelliJ IDEA используется для создания функции, которая отвечает на HTTP-запросы. После тестирования кода в локальной среде его необходимо развернуть в бессерверной среде Функций Azure. Выполнение этого краткого руководства предполагает небольшую дополнительную плату в несколько центов США в учетной записи Azure.

## <a name="configure-your-environment"></a>Настройка среды

Перед началом работы убедитесь, что выполнены следующие предварительные требования.

+ Учетная запись Azure с активной подпиской. [Создайте учетную запись](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) бесплатно.
+ Поддерживаемый [пакет средств разработки Java для Azure(JDK)](../fundamentals/java-jdk-long-term-support.md) для Java 8
+ Установленный [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) Ultimate Edition или Community Edition.
+ [Maven 3.5.0+](https://maven.apache.org/download.cgi)
+ Последняя версия [Function Core Tools](https://github.com/Azure/azure-functions-core-tools)

## <a name="installation-and-sign-in"></a>Установка и вход

1. В диалоговом окне Settings/Preferences (Параметры и предпочтения) в IntelliJ IDEA (CTRL+ALT+S) выберите **Plugins** (Подключаемые модули). Затем найдите модуль **Azure Toolkit for IntelliJ** в **Marketplace** и щелкните **Install** (Установить). После установки щелкните **Restart** (Перезапуск), чтобы активировать подключаемый модуль. 

   ![Подключаемый модуль Azure Toolkit for IntelliJ в Marketplace][marketplace]

2. Чтобы войти в учетную запись Azure, откройте боковую панель **Azure Explorer**, а затем щелкните значок **Azure Sign In** (Вход в Azure) в верхней строке (или в меню IDEA **Tools/Azure/Azure Sign in** (Средства/Azure/Вход в Azure)).

   ![Команда Azure Sign In (Вход в Azure) в IntelliJ][I01]

3. В окне **Azure Sign In** (Вход в Azure) выберите **Device Login** (Имя пользователя устройства) и щелкните **Sign in** (Вход) ([другие варианты входа](sign-in-instructions.md)).

   ![Окно Azure Sign In (Вход в Azure) с выбранным именем пользователя устройства][I02]

4. В диалоговом окне **Azure Device Login** (Вход в систему устройства Azure) щелкните **Copy&Open** (Копировать и открыть).

   ![Диалоговое окно входа Azure][I03]

5. В браузере вставьте код устройства (скопированный при нажатии **Copy&Open** (Копировать и открыть) на последнем шаге), а затем нажмите кнопку **Далее**.

   ![Вход в систему устройства в браузере][I04]

6. В диалоговом окне **Select Subscriptions** (Выбор подписок) выберите нужные подписки и нажмите кнопку **ОК**.

   ![Диалоговое окно выбора подписок][I05]

## <a name="create-your-local-project"></a>Создание локального проекта

В этом разделе вы используете набор средств Azure для IntelliJ, чтобы создать локальный проект Функций Azure. Далее в этой статье вы опубликуете код функции в Azure. 

1. Откройте диалоговое окно приветствия IntelliJ, выберите *Создать новый проект* чтобы открыть мастер создания проекта, выберите *Azure Functions*.

    ![Создание проекта Функций Azure](media/quickstart-functions/create-functions-project.png)

1. Выберите *триггер HTTP*, нажмите кнопку *Далее* и следуйте указаниям мастера, чтобы выполнить все конфигурации на следующих страницах. Подтвердите расположение проекта и нажмите кнопку *Завершить*; после этого Intellj IDEA откроет новый проект.

    ![Завершение проекта Функций Azure](media/quickstart-functions/create-functions-project-finish.png)

## <a name="run-the-function-app-locally"></a>Локальное выполнение приложения-функции

1. Перейдите к `src/main/java/org/example/functions/HttpTriggerFunction.java`, чтобы увидеть созданный код. Рядом со строкой *17* обратите внимание на зеленую кнопку *Выполнить*, щелкните ее и выберите *Запустить "azure-function-exam..."* . Вы увидите, что приложение-функция выполняется локально с несколькими журналами.

    ![Локальный запуск проекта функций](media/quickstart-functions/local-run-functions-project.png)

    ![Выходные данные локального запуска функций](media/quickstart-functions/local-run-functions-output.png)

1. Вы можете попробовать выполнить функцию, обратившись к печатной конечной точке из браузера, например `http://localhost:7071/api/HttpTrigger-Java?name=Azure`.

    ![Результаты проверки локального выполнения функций](media/quickstart-functions/local-run-functions-test.png)

1. Кроме того, журнал выводится в IDEA. Теперь эту функцию можно отключить, нажав кнопку *Завершить*.

    ![Журнал проверки для локального запуска функций](media/quickstart-functions/local-run-functions-log.png)

## <a name="debug-the-function-app-locally"></a>Локальная отладка приложения-функции.

1. Теперь попробуем выполнить отладку приложения-функции на локальном компьютере. Нажмите кнопку *Отладка* на панели инструментов (если она не отображается, щелкните *Вид > Представление > Панель инструментов*, чтобы включить панель инструментов).

    ![Кнопка локальной отладки функций](media/quickstart-functions/local-debug-functions-button.png)

1. Нажмите на строку *20* файла `src/main/java/org/example/functions/HttpTriggerFunction.java`, чтобы добавить точку останова. Перейдя снова к конечной точке `http://localhost:7071/api/HttpTrigger-Java?name=Azure`, вы обнаружите попадание в точку останова. Вы можете опробовать больше отладочных функций, таких как *шаг*, *обзор*, *оценка*. Чтобы отключить сеанс отладки, нажмите кнопку "Остановить"

    ![Прерывание локальной отладки функций](media/quickstart-functions/local-debug-functions-break.png)

## <a name="deploy-your-function-app-to-azure"></a>Развертывание приложения-функции в Azure

1. Щелкните правой кнопкой мыши проект в обозревателе проектов IntelliJ и выберите *Azure > Развернуть в Функциях Azure*

    ![Развертывание функций в Azure](media/quickstart-functions/deploy-functions-to-azure.png)

1. Если у вас еще нет приложения-функции, щелкните *No available function, click to create a new one* (Нет доступной функции, щелкните, чтобы создать новую).

    ![Развертывание функций для создания приложения Azure](media/quickstart-functions/deploy-functions-create-app.png)

1. Введите имя приложения-функции и выберите соответствующую подписку (платформу, группу ресурсов или план службы приложений). Здесь также можно создать план службы "Группа ресурсов" или "Служба приложений". Затем сохраните параметры приложения без изменений, нажмите кнопку *ОК* и подождите несколько минут, пока создастся новая функция. После *Создания нового приложения функции...* индикатор выполнения исчезнет.

    ![Развертывание функций для создания мастера приложений](media/quickstart-functions/deploy-functions-create-app-wizard.png)

1. Выберите приложение-функцию, в которое вы хотите выполнить развертывание (автоматически будет выбрано новое приложение-функция, которое вы только что создали). Щелкните *Запустить*, чтобы развернуть функции.

    ![Развертывание функций для запуска Azure](media/quickstart-functions/deploy-functions-run.png)

    ![Развертывание функций для журнала Azure](media/quickstart-functions/deploy-functions-log.png)

## <a name="manage-azure-functions-from-idea"></a>Управление функциями Azure из IDEA

1. Вы можете управлять функциями с помощью *Azure Explorer* в своем IDEA. Щелкнув *приложение функцию*, вы увидите здесь все свои функции.

    ![Просмотр функций в обозревателе](media/quickstart-functions/explorer-view-functions.png)

1. Щелкните, чтобы выбрать одну из функций, и, нажав правой кнопкой мыши, выберите *Показывать свойства*, чтобы открыть страницу сведений. 

    ![Отображение свойств функций](media/quickstart-functions/explorer-functions-show-properties.png)

1. Щелкните правой кнопкой мыши функцию *HttpTrigger-Java*и выберите *Функция для триггеров*. Вы увидите, что браузер открыт с URL-адресом триггера.

    ![Развертывание функций для запуска Azure](media/quickstart-functions/explorer-trigger-functions.png)

## <a name="add-more-functions-to-the-project"></a>Добавление дополнительных функций в проект

1. Щелкните правой кнопкой мыши пакет *org.example.functions* и выберите *Создать > Класс Функций Azure*. 

    ![Добавление функций в запись проекта](media/quickstart-functions/add-functions-entry.png)

1. Заполните поля имени класса *HttpTest*, выберите *HttpTrigger* в мастере создания классов функций, а затем нажмите *ОК*, чтобы создать. Таким образом, вы сможете создавать новые функции.

    ![Добавление функций в выбор триггера проекта](media/quickstart-functions/add-functions-trigger.png)
    
    ![Добавление функций в выходные данные проекта](media/quickstart-functions/add-functions-output.png)

## <a name="cleaning-up-functions"></a>Очистка Функций

1. Удаление Функций Azure в Azure Explorer
      
      ![Добавление функций в выбор триггера проекта](media/quickstart-functions/delete-function.png)
      

## <a name="next-steps"></a>Дальнейшие действия

Итак, вы создали проект с функцией Java, активируемой по HTTP-запросу, запустили ее на своем локальном компьютере и развернули в Azure. Теперь расширьте свою функцию путем...

> [!div class="nextstepaction"]
> [Добавления выходной привязки очереди службы хранилища Azure](/azure/azure-functions/functions-add-output-binding-storage-queue-java)


[marketplace]:./media/create-hello-world-web-app/marketplace.png
[I01]: media/sign-in-instructions/I01.png
[I02]: media/sign-in-instructions/I02.png
[I03]: media/sign-in-instructions/I03.png
[I04]: media/sign-in-instructions/I04.png
[I05]: media/sign-in-instructions/I05.png