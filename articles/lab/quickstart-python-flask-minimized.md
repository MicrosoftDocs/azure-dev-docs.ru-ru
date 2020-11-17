---
title: Краткое руководство. Создание приложения Python в Linux
description: Начните работу со Службой приложений Azure, развернув первое приложение Python в контейнере Linux в Службе приложений.
ms.topic: quickstart
ms.date: 09/22/2020
ms.custom: seo-python-october2019, cli-validate, devx-track-python, devx-track-azurecli
robots: noindex
ms.openlocfilehash: a37ad0969308ace65a4654cc4244d280abd286ea
ms.sourcegitcommit: 12f80b1e0fe08db707c198271d0c399c3aba343a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/11/2020
ms.locfileid: "94515215"
---
# <a name="quickstart-create-a-python-app-in-azure-app-service"></a>Краткое руководство. Создание приложения Python в Службе приложений 

В этом кратком руководстве описывается процесс развертывания веб-приложения Python для [службы приложений на платформе Linux](/azure/app-service/overview#app-service-on-linux), высокомасштабируемой службы веб-размещения Azure с самостоятельной установкой исправлений. Для развертывания примера с помощью платформ Flask или Django на компьютерах Mac, Linux или Windows используется [интерфейс командной строки Azure (CLI)](/cli/azure/install-azure-cli). Веб-приложение, которое вы настраиваете, использует бесплатный уровень Службы приложений, поэтому в процессе выполнения этого руководства затраты на ресурсы Azure не изменяются.

> [!TIP]
> Если вы привыкли работать с Visual Studio Code, ознакомьтесь со статьей **[Руководство по развертыванию приложений Python в Службе приложений Azure в Linux с помощью Visual Studio Code](/azure/developer/python/tutorial-deploy-app-service-on-linux-01)** .

<details>
<summary >1. Настройка начальной среды</summary>

<a name="set-up"></a>

1. Подготовьте учетную запись Azure с активной подпиской. [Создайте учетную запись](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) бесплатно.
1. Установите <a href="https://www.python.org/downloads/" target="_blank">Python 3.6 или более поздней версии</a>.
1. Установите <a href="/cli/azure/install-azure-cli" target="_blank">Azure CLI</a> версии 2.0.80 или более поздней для выполнения команд в любой оболочке для подготовки и настройки ресурсов Azure.

Откройте окно терминала и проверьте, что используется Python 3.6 или более поздней версии:

# <a name="bash"></a>[Bash](#tab/bash)

```bash
python3 --version
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```cmd
py -3 --version
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
py -3 --version
```

---

Убедитесь, что у вас есть Azure CLI версии 2.0.80 или выше.

```azurecli
az --version
```

Теперь войдите в Azure с помощью CLI.

```azurecli
az login
```

Эта команда открывает окно браузера для ввода учетных данных. После выполнения команда отображает выходные данные JSON с информацией о подписках.

Войдя, вы сможете выполнять в Azure CLI команды Azure для работы с ресурсами в подписке.

[Возникли проблемы? Сообщите нам!](https://aka.ms/FlaskCLIQuickstartHelp)

</details>

<details>
<summary>2. Клонирования репозитория</summary>

Клонируйте пример репозитория, выполнив указанную ниже команду, и перейдите к папке. ([Установите Git](https://git-scm.com/downloads), если у вас его еще нет.)

```terminal
git clone https://github.com/Azure-Samples/python-docs-hello-world
```

Затем перейдите в эту папку:

```terminal
cd python-docs-hello-world
```

Пример содержит код для определенной платформы, который Служба приложений Azure распознает при запуске приложения. Дополнительные сведения см. в разделе [Процесс запуска контейнера](/azure/app-service/configure-language-python#container-startup-process).

[Возникли проблемы? Сообщите нам!](https://aka.ms/FlaskCLIQuickstartHelp)

</details>

<details>
<summary>3. Запуск примера</summary>

1. Убедитесь, что вы находитесь в папке *python-docs-hello-world*. 

1. Создайте виртуальную среду и установите необходимые зависимости.

    [!include [virtual environment setup](includes/app-service-quickstart-python-venv.md)]

    Если вы видите ошибку "[Errno 2] Не существует такого файла или папки: requirements.txt", убедитесь, что вы находитесь в папке *python-docs-hello-world*.

1. Запустите сервер разработки.

    ```terminal  
    flask run
    ```
    
    По умолчанию сервер предполагает, что модулем записи приложения является *app.py*, как в примере. (Если вы используете другое имя модуля, задайте это имя для переменной среды `FLASK_APP`.)

1. Откройте веб-браузер и перейдите к примеру приложения по адресу `http://localhost:5000/`. Приложение отображает сообщение **Hello World!** .

    ![Локальный запуск примера приложения Python](./media/quickstart-python/run-hello-world-sample-python-app-in-browser-localhost.png)
    
1. В окне терминала нажмите клавиши **CTRL**+**C**, чтобы выйти из сервера разработки.


[Возникли проблемы? Сообщите нам!](https://aka.ms/FlaskCLIQuickstartHelp)

</details>

<details>
<summary>4. Развертывание примера</summary>

Разверните код в локальной папке (*python-docs-hello-world*) с помощью команды `az webapp up`.

```azurecli
az webapp up --sku F1 --name <app-name>
```

- Если команда `az` не распознана, проверьте, установили ли вы Azure CLI согласно сведениям, указанным в разделе [Настройка начальной среды](#set-up).
- Если команда `webapp` не распознается, убедитесь, что вы используете Azure CLI версии 2.0.80 или более поздней. Если нет, [установите последнюю версию](/cli/azure/install-azure-cli).
- Замените `<app_name>` именем, уникальным для всех регионов Azure (*допустимыми символами являются `a-z`, `0-9`и `-`* ). Рекомендуется использовать сочетание названия компании и идентификатора приложения.
- Аргумент `--sku F1` создает веб-приложение в ценовой категории "Бесплатный". Этот аргумент можно опустить, чтобы использовать более быструю ценовую категорию "Премиум" с почасовой оплатой.
- При необходимости вы можете использовать аргумент `--location <location-name>`, где `<location_name>` является доступным регионом Azure. Список допустимых регионов для учетной записи Azure можно получить, выполнив команду [`az account list-locations`](/cli/azure/appservice#az-appservice-list-locations).
- Если отображается сообщение об ошибке Could not auto-detect the runtime stack of your app (Не удалось автоматически определить стек среды выполнения приложения), убедитесь, что вы выполняете команду в папке *python-docs-hello-world* (Flask) или в папке *python-docs-hello-django* (Django), где находится файл *requirements.txt*. (см. статью об [устранение неполадок с автоматическим обнаружением с помощью команды az webapp up](https://github.com/Azure/app-service-linux-docs/blob/master/AzWebAppUP/runtime_detection.md) на сайте GitHub).

Выполнение этой команды может занять несколько минут. Во время выполнения она предоставляет сообщения о создании группы ресурсов, плане службы приложений и приложении размещения, настройке ведения журнала и последующем выполнении развертывания ZIP-файла. Затем оно выдает сообщение You can launch the app at http://&lt;app-name&gt;.azurewebsites.net (Вы можете запустить приложение по адресу http://<app-name>.azurewebsites.net). Это URL-адрес приложения в Azure.

![Пример выходных данных команды az webapp up](./media/quickstart-python/az-webapp-up-output.png)

[Возникли проблемы? Сообщите нам!](https://aka.ms/FlaskCLIQuickstartHelp)

[!include [az webapp up command note](includes/app-service-web-az-webapp-up-note.md)]
</details>

<details>
<summary>5. Переход в приложение</summary>

Найдите развернутое приложение в веб-браузере по URL-адресу `http://<app-name>.azurewebsites.net`. Для первоначального запуска приложения потребуется несколько секунд.

Пример кода Python запускает контейнер Linux в Службе приложений с помощью встроенного образа.

![Запуск примера приложения Python в Azure](./media/quickstart-python/run-hello-world-sample-python-app-in-browser.png)

**Поздравляем!** Вы развернули свое приложение Python в Cлужбе приложений.

[Возникли проблемы? Сообщите нам об этом.](https://aka.ms/FlaskCLIQuickstartHelp)
</details>

<details>
<summary>6. Повторное развертывание обновлений</summary>

В этом разделе вы внесете небольшое изменение в код, а затем повторно развернете код в Azure. Изменение кода включает в себя инструкцию `print` для создания выходных данных журнала, которые понадобятся в следующем разделе.

Откройте *app.py* в редакторе и обновите функцию `hello`, чтобы она соответствовала следующему коду. 

```python
def hello():
    print("Handling request to home page.")
    return "Hello, Azure!"
```

    
Сохраните изменения, а затем повторно разверните приложение с помощью команды `az webapp up`.

```azurecli
az webapp up
```

Эта команда использует значения, которые кэшируются локально в файле *.azure/config*, включая имя приложения, группу ресурсов и план службы приложений.

После завершения развертывания вернитесь в окно браузера и откройте `http://<app-name>.azurewebsites.net`. Обновите страницу, на которой должно отобразиться измененное сообщение:

![Запуск обновленного примера приложения Python в Azure](./media/quickstart-python/run-updated-hello-world-sample-python-app-in-browser.png)

[Возникли проблемы? Сообщите нам!](https://aka.ms/FlaskCLIQuickstartHelp)

> [!TIP]
> Visual Studio Code предоставляет разнообразные расширения для Python и Службы приложений Azure, которые упрощают процесс развертывания веб-приложений Python в службе приложений. Дополнительные сведения см. в статье [Развертывание приложений Python в Службе приложений из Visual Studio Code](/azure/python/tutorial-deploy-app-service-on-linux-01).
</details>

<details>
<summary>7. Журналы потоковой передачи</summary>

Вы можете получить доступ к журналам консоли, созданным в приложении, и контейнеру, в котором он выполняется. Журналы содержат все выходные данные, созданные с помощью инструкций `print`.

Чтобы выполнить потоковую передачу журналов, запустите команду [az webapp log tail](/cli/azure/webapp/log?view=azure-cli-latest&preserve-view=true#az_webapp_log_tail):

```azurecli
az webapp log tail
```

Кроме того, вы можете использовать параметр `--logs`, а потом — команду `az webapp up`, чтобы автоматически запустить потоковую передачу журналов для развертывания.

Обновите приложение в браузере, чтобы создать журналы консоли, которые содержат сообщения с HTTP-запросами к приложению. Если выходные данные не отображаются немедленно, повторите попытку через 30 секунд.

Вы также можете проверить файлы журнала в браузере на странице `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.

Чтобы предотвратить потоковую передачу журналов, нажмите **Ctrl**+**C** в окне терминала.

[Возникли проблемы? Сообщите нам об этом.](https://aka.ms/FlaskCLIQuickstartHelp)
</details>

<details>
<summary>8. Управление приложением Azure</summary>

Перейдите на <a href="https://portal.azure.com" target="_blank">портал Azure</a>, чтобы управлять созданным приложением. Найдите в поиске и выберите **Службы приложений**.

![Перейдите к разделу службы приложений на портале Azure](./media/quickstart-python/navigate-to-app-services-in-the-azure-portal.png)

Выберите имя приложения Azure.

![Переход к приложению Python в Службе приложений на портале Azure](./media/quickstart-python/navigate-to-app-in-app-services-in-the-azure-portal.png)

При выборе приложения открывается страница **Обзор**, где можно выполнять основные задачи управления, такие как обзор, завершение, запуск, перезапуск и удаление.

![Управление приложением Python на странице "Обзор" на портале Azure](./media/quickstart-python/manage-an-app-in-app-services-in-the-azure-portal.png)

В меню службы приложений слева доступно несколько страниц для настройки приложения.

[Возникли проблемы? Сообщите нам об этом.](https://aka.ms/FlaskCLIQuickstartHelp)
</details>

<details>
<summary>9. Очистка ресурсов</summary>

На предыдущем шаге вы создали ресурсы Azure в группе ресурсов. Группа ресурсов имеет имя, такое как "appsvc_rg_Linux_CentralUS", в зависимости от расположения. Если вы используете номер SKU Службы приложений, отличающийся от бесплатного уровня F1, за эти ресурсы будет взиматься плата (см. сведения о [ценах на Службу приложений](https://azure.microsoft.com/pricing/details/app-service/linux/)).

Если эти ресурсы вам не понадобятся в будущем, удалите группу ресурсов, выполнив следующие команды:

```azurecli
az group delete --no-wait
```

Эта команда использует имя группы ресурсов, кэшированное в файле *.azure/config*.

Аргумент `--no-wait` позволяет команде возвращать сведения до завершения операции.

[Возникли проблемы? Сообщите нам об этом.](https://aka.ms/FlaskCLIQuickstartHelp)
</details>

<details>
<summary>Дальнейшие действия</summary>

> [!div class="nextstepaction"]
> [Настройка приложения Python](/azure/app-service/configure-language-python)

> [!div class="nextstepaction"]
> [Добавление возможности входа пользователя в веб-приложение Python](/azure/active-directory/develop/quickstart-v2-python-webapp)

> [!div class="nextstepaction"]
> [Учебник. Запуск приложения Python в настраиваемом контейнере](/azure/app-service/tutorial-custom-container)
</details>