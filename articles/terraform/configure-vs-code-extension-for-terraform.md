---
title: Настройка расширения Visual Studio Code для Azure Terraform
description: Сведения о том, как установить и использовать расширение Azure Terraform в Visual Studio Code.
ms.topic: how-to
ms.date: 10/26/2019
ms.custom: devx-track-terraform
ms.openlocfilehash: ac8c5c765237a065fbb2531f71de7f39644b70dc
ms.sourcegitcommit: e20f6c150bfb0f76cd99c269fcef1dc5ee1ab647
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/28/2020
ms.locfileid: "91401764"
---
# <a name="configure-the-azure-terraform-visual-studio-code-extension"></a>Настройка расширения Visual Studio Code для Azure Terraform

Расширение Visual Studio Code для Azure Terraform позволяет работать с Terraform в редакторе. С помощью этого расширения можно создавать, тестировать и выполнять конфигурации Terraform. Это расширение также поддерживает визуализацию графа ресурсов.

Вы узнаете, как выполнять следующие задачи:
> [!div class="checklist"]
> * автоматизация подготовки служб Azure с помощью Terraform;
> * установка и использование расширения Visual Studio Code Terraform для служб Azure;
> * использование Visual Studio Code для разработки, проектирования и выполнения планов Terraform.

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
- **Terraform**. [Установите и настройте Terraform](get-started-cloud-shell.md).
- **Visual Studio Code**. Установите подходящую для вашей среды версию [Visual Studio Code](https://code.visualstudio.com/download).

## <a name="prepare-your-dev-environment"></a>Подготовка среды разработки

### <a name="install-git"></a>Установка Git

Чтобы выполнить упражнения из этой статьи, вам нужно [установить Git](https://git-scm.com/).

### <a name="install-hashicorp-terraform"></a>Установка HashiCorp Terraform

Следуйте инструкциям по [установке Terraform](https://www.terraform.io/intro/getting-started/install.html) на странице HashiCorp:

- Скачивание Terraform
- Установка Terraform
- Проверка правильности установки Terraform

>[!Tip]
>Обязательно выполните инструкции по настройке системной переменной PATH.

### <a name="install-nodejs"></a>Установка Node.js

Чтобы использовать Terraform в Cloud Shell, вам нужно [установить Node.js](https://nodejs.org/) версии 6.0 или более поздней.

>[!NOTE]
>Чтобы проверить наличие Node.js, откройте окно терминала и введите команду `node -v`.

### <a name="install-graphviz"></a>Установка GraphViz

Чтобы использовать функцию визуализации в Terraform, вам нужно [установить GraphViz](https://graphviz.org/).

>[!NOTE]
>Чтобы проверить наличие GraphViz, откройте окно терминала и введите команду `dot -V`.

### <a name="install-the-azure-terraform-visual-studio-code-extension"></a>Установка расширения Visual Studio Code для Azure Terraform

1. Запустите Visual Studio Code.

1. Выберите **Расширения**.

    ![Кнопка "Расширения"](media/configure-vs-code-extension-for-terraform/tf-vscode-extensions-button.png)

1. Используйте текстовое поле **Поиск расширений в Marketplace** для поиска расширения Azure Terraform:

    ![Поиск расширений Visual Studio Code в Marketplace](media/configure-vs-code-extension-for-terraform/tf-search-extensions.png)

1. Выберите пункт **Установить**.

    >[!NOTE]
    >Когда вы щелкнете **Установить** рядом с расширением Azure Terraform, Visual Studio Code автоматически установит расширение Azure Account. Azure Account — это файл зависимостей для расширения Azure Terraform, который используется для аутентификации подписки Azure и расширений, имеющих отношение к Azure.

#### <a name="verify-the-terraform-extension-is-installed-in-visual-studio-code"></a>Проверка наличия расширения Terraform в Visual Studio Code

1. Выберите **Расширения**.

1. В текстовое поле поиска введите `@installed`.

    ![Установленные расширения](media/configure-vs-code-extension-for-terraform/tf-installed-extensions.png)

Расширение Azure Terraform появится в списке установленных расширений.

![Установленные расширения Terraform](media/configure-vs-code-extension-for-terraform/tf-installed-terraform-extension-button.png)

Теперь вы можете выполнять из Visual Studio Code все поддерживаемые команды Terraform в среде Cloud Shell.

## <a name="exercise-1-basic-terraform-commands-walk-through"></a>Упражнение 1. Пошаговые инструкции по основным командам Terraform

В этом упражнении вы создадите и выполните базовый файл конфигурации Terraform, который подготавливает новую группу ресурсов Azure.

### <a name="prepare-a-test-plan-file"></a>Подготовка файла для плана тестирования

1. В строке меню Visual Studio Code выберите **Файл > Создать файл**.

1. В браузере перейдите на страницу Terraform с описанием [azurerm_resource_group](https://www.terraform.io/docs/providers/azurerm/r/resource_group.html#) и скопируйте код из блока **Example Usage** (Пример использования).

    ![Example Usage (Пример использования)](media/configure-vs-code-extension-for-terraform/tf-azurerm-resource-group-example-usage.png)

1. Вставьте скопированный код в новый файл, созданный в Visual Studio Code.

    ![Вставка кода блока Example Usage](media/configure-vs-code-extension-for-terraform/tf-paste-example-usage-code.png)

    >[!NOTE]
    >Вы можете изменить значение **Имя** для группы ресурсов, но это имя должно быть уникальным в пределах подписки Azure.

1. В строке меню выберите **Файл > Сохранить как**.

1. В диалоговом окне **Сохранение как** перейдите в любую удобную папку и щелкните **Создать папку**. (Вместо имени *Новая папка* присвойте этой папке более понятное имя.)

    >[!NOTE]
    >В нашем примере папка получает имя TERRAFORM-TEST-PLAN.

1. Убедитесь, что новая папка выделена (выбрана) и щелкните **Открыть**.

1. В диалоговом окне **Сохранение как** измените имя файла на *main.tf*.

    ![Сохранение файла с именем main.tf](media/configure-vs-code-extension-for-terraform/tf-save-as-main.png)

1. Щелкните **Сохранить**.
1. В строке меню выберите **Файл > Открыть папку**. Найдите и выберите папку, которую вы только что создали.

### <a name="run-terraform-init-command"></a>Выполнение команды Terraform *init*

1. Запустите Visual Studio Code.

1. В строке меню Visual Studio Code выберите **Файл > Открыть папку**, а затем найдите и выберите созданный файл *main.tf*.

    ![Файл main.tf](media/configure-vs-code-extension-for-terraform/tf-main-tf.png)

1. В строке меню выберите **Просмотр > Палитра команд... > Azure Terraform: init**.

1. Когда появится запрос на подтверждение, щелкните **ОК**.

1. При первом запуске Cloud Shell из новой папки вам будет предложено создать веб-приложение. Щелкните **Open**(Открыть).

    ![Первый запуск Cloud Shell](media/configure-vs-code-extension-for-terraform/tf-first-launch-of-cloud-shell.png)

1. Откроется страница приветствия Azure Cloud Shell. Выберите Bash или PowerShell.

    ![Страница приветствия Azure Cloud Shell](media/configure-vs-code-extension-for-terraform/tf-welcome-to-azure-cloud-shell.png)

    >[!NOTE]
    >В нашем примере выбран вариант Bash (Linux).

1. Если вы еще не настроили учетную запись хранения Azure, откроется следующий экран. Щелкните **Создать хранилище**.

    ![Подключенное хранилище отсутствует](media/configure-vs-code-extension-for-terraform/tf-you-have-no-storage-mounted.png)

1. Azure Cloud Shell запускается в оболочке, которую вы выбрали ранее, и отображает сведения о только что созданном облачном диске.

    ![Ваш облачный диск успешно создан](media/configure-vs-code-extension-for-terraform/tf-your-cloud-drive-has-been-created-in.png)

1. Теперь можно выйти из Cloud Shell.

1. В строке меню выберите **Просмотр** > **Палитра команд** > **Azure Terraform: init**.

    ![Terraform успешно инициализирован](media/configure-vs-code-extension-for-terraform/tf-terraform-has-been-successfully-initialized.png)

### <a name="visualize-the-plan"></a>Визуализация плана

Ранее по инструкциям из этой статьи вы установили GraphViz. Terraform с помощью GraphViz может создать визуальное представление плана выполнения или конфигурации. Расширение Azure Terraform для Visual Studio Code реализует эту функцию с помощью команды *visualize*.

- В строке меню выберите **Просмотр > Палитра команд > Azure Terraform: visualize**.

    ![Визуализация плана](media/configure-vs-code-extension-for-terraform/tf-graph.png)

### <a name="run-terraform-plan-command"></a>Выполнение команды Terraform *plan*

Команда Terraform *plan* позволяет проверить, будет ли план выполнения с некоторым набором изменений выполнять ожидаемые действия.

>[!NOTE]
>Команда Terraform *plan* не изменяет реальные ресурсы Azure. Чтобы внести изменения в план, используется команда Terraform *apply*.

- В строке меню выберите **Просмотр** > **Палитра команд** > **Azure Terraform: plan**.

    ![План Terraform](media/configure-vs-code-extension-for-terraform/tf-terraform-plan.png)

### <a name="run-terraform-apply-command"></a>Выполнение команды Terraform *apply*

Если вы полностью довольны результатами выполнения команды Terraform *plan*, примените команду *apply*.

1. В строке меню выберите **Просмотр** > **Палитра команд** > **Azure Terraform: apply**.

    ![Применение Terraform](media/configure-vs-code-extension-for-terraform/tf-terraform-apply.png)

1. Введите `yes`.

    ![Согласие на выполнение команды Terraform apply](media/configure-vs-code-extension-for-terraform/tf-terraform-apply-yes.png)

### <a name="verify-your-terraform-plan-was-executed"></a>Проверка выполнения команды Terraform plan

Чтобы проверить создание группы ресурсов Azure, сделайте следующее:

1. Откройте портал Azure.

1. В области навигации слева выберите **Группа ресурсов**.

    ![Проверка нового ресурса](media/configure-vs-code-extension-for-terraform/tf-verify-resource-group-created.png)

Новая группа ресурсов должна быть указана в столбце **Имя**.

>[!NOTE]
>Вам можете не закрывать окно портала Azure — оно нам понадобится на следующем шаге.

### <a name="run-terraform-destroy-command"></a>Выполнение команды Terraform *destroy*

1. В строке меню выберите **Просмотр** > **Палитра команд** > **Azure Terraform: destroy**.

    ![Команда Terraform destroy](media/configure-vs-code-extension-for-terraform/tf-terraform-destroy.png)

1. Введите *Yes* (Да).

    ![Согласие на выполнение команды Terraform destroy](media/configure-vs-code-extension-for-terraform/tf-terraform-destroy-yes.png)

### <a name="verify-your-resource-group-was-destroyed"></a>Проверка удаления группы ресурсов

Чтобы проверить удаление группы ресурсов в Terraform, сделайте следующее:

1. Щелкните **Обновить** на странице **Группы ресурсов** на портале Azure.

1. Удаленная группа ресурсов должна исчезнуть из этого списка.

    ![Проверка удаления группы ресурсов](media/configure-vs-code-extension-for-terraform/tf-refresh-resource-groups-button.png)

## <a name="exercise-2-terraform-compute-module"></a>Упражнение 2. Модуль Terraform *compute*

В этом упражнении показано, как загрузить модуль Terraform *compute* в среду Visual Studio Code.

### <a name="clone-the-terraform-azurerm-compute-module"></a>Клонирование модуля terraform-azurerm-compute

1. Используйте [эту ссылку](https://github.com/Azure/terraform-azurerm-compute), чтобы открыть страниц модуля Terraform Azure Rm Compute на GitHub.

1. Выберите **Clone or download**(Клонировать или скачать).

    ![Клонирование или скачивание](media/configure-vs-code-extension-for-terraform/tf-clone-with-https.png)

    >[!NOTE]
    >В нашем примере папке присвоено имя *terraform-azurerm-compute*.

### <a name="open-the-folder-in-visual-studio-code"></a>Откройте эту папку в Visual Studio Code.

1. Запустите Visual Studio Code.

1. На панели меню выберите **Файл > Открыть папку**, а затем найдите и выберите созданную на предыдущем шаге папку.

    ![Папка terraform-azurerm-compute](media/configure-vs-code-extension-for-terraform/tf-terraform-azurerm-compute-folder.png)

### <a name="initialize-terraform"></a>Инициализация Terraform

Прежде чем выполнять команды Terraform в Visual Studio Code, нужно скачать подключаемые модули для двух поставщиков Azure: random и azurerm.

1. На панели терминала в интегрированной среде разработки Visual Studio Code введите следующую команду: `terraform init`.

    ![Команда Terraform init](media/configure-vs-code-extension-for-terraform/tf-terraform-init-command.png)

1. Введите `az login`, нажмите клавишу **ВВОД** и выполните инструкции на экране.

### <a name="module-test-lint"></a>Проверка модуля: *lint*

1. В строке меню выберите **Просмотр > Палитра команд > Azure Terraform: execute test**.

1. В списке вариантов типа теста выберите **lint**.

    ![Выберите lint в качестве типа теста.](media/configure-vs-code-extension-for-terraform/tf-select-type-of-test-lint.png)

1. Когда появится запрос на подтверждение, щелкните **ОК** и следуйте инструкциям на экране.

>[!NOTE]
>При выполнении тестов **lint** или **end-to-end** Azure с использованием службы контейнеров подготавливает тестовый компьютер к выполнению реального теста. Это означает, что результаты тестов обычно возвращаются только через несколько минут.

Через некоторое время вы увидите на панели терминала список, который выглядит примерно так:

![Результаты теста Lint](media/configure-vs-code-extension-for-terraform/tf-lint-test-results.png)

### <a name="test-the-module"></a>Тестирование модуля

1. В строке меню выберите **Просмотр > Палитра команд > Azure Terraform: execute test**.

1. В списке вариантов типа теста выберите **end-to-end**.

    ![Выберите end to end в качестве типа теста.](media/configure-vs-code-extension-for-terraform/tf-select-type-of-test-end-to-end.png)

1. Когда появится запрос на подтверждение, щелкните **ОК** и следуйте инструкциям на экране.

>[!NOTE]
>При выполнении тестов **lint** или **end-to-end** Azure с использованием службы контейнеров подготавливает тестовый компьютер к выполнению реального теста. Это означает, что результаты тестов обычно возвращаются только через несколько минут.

Через некоторое время вы увидите на панели терминала список, который выглядит примерно так:

![Результаты проверки](media/configure-vs-code-extension-for-terraform/tf-end-to-end-test-results.png)

[!INCLUDE [terraform-troubleshooting.md](includes/terraform-troubleshooting.md)]

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Список модулей Terraform, доступных для Azure и других поддерживаемых поставщиков](https://registry.terraform.io/)