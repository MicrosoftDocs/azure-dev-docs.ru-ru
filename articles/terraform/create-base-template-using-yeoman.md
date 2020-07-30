---
title: Учебник. Создание базового шаблона Terraform в Azure с использованием Yeoman
description: Здесь вы узнаете, как создать базовый шаблон Terraform в Azure с использованием Yeoman.
ms.topic: tutorial
ms.date: 05/25/2020
ms.openlocfilehash: 8e9c4444d95f052fc5872565b316ec15e033be82
ms.sourcegitcommit: 8cd0ddf1651c3b64bb72dedc2890108c2cfe3bcb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87334469"
---
# <a name="tutorial-create-a-terraform-base-template-in-azure-using-yeoman"></a>Руководство по Создание базового шаблона Terraform в Azure с использованием Yeoman

В этом руководстве вы узнаете, как использовать сочетание [Terraform](/azure/terraform/) и [Yeoman](https://yeoman.io/). Terraform — это инструмент для создания инфраструктуры в Azure. Yeoman упрощает создание модулей Terraform.

В этой статье вы узнаете, как выполнить следующие задачи:
> [!div class="checklist"]
> * Создание базового шаблона Terraform с помощью генератора модулей Yeoman.
> * Тестирование шаблона Terraform с помощью двух различных методов.
> * Запуск модуля Terraform с помощью файла Docker.
> * Запуск модуля Terraform в собственном режиме в Azure Cloud Shell.

[!INCLUDE [hashicorp-support.md](includes/hashicorp-support.md)]

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
- **Visual Studio Code**. [Скачайте Visual Studio Code](https://code.visualstudio.com/download) для вашей платформы
- **Terraform**. Чтобы запускать модуль, созданный с помощью Yeoman, необходимо [установить Terraform](get-started-cloud-shell.md).
- **Docker**. [Установка Docker](https://www.docker.com/get-started) используется для запуска модуля, созданного с помощью генератора Yeoman.
- **Язык программирования Go**. [Установка Go](https://golang.org/) в виде тестовых случаев, создаваемых Yeoman, — это код, использующий язык Go.

>[!NOTE]
>Большая часть процедур, описанных в этом руководстве включают в себя интерфейс командной строки. Описанные шаги применяются для всех операционных систем и средств командной строки. Для этих примеров мы используем PowerShell для локальной среды и Git Bash для среды Cloud Shell.

## <a name="prepare-your-environment"></a>Подготовка среды

### <a name="install-nodejs"></a>Установка Node.js

Чтобы использовать Terraform в Cloud Shell, вам нужно [установить Node.js](https://nodejs.org/en/download/) версии 6.0 или более поздней.

>[!NOTE]
>Чтобы убедиться, что Node.js установлен, откройте окно терминала и введите команду `node --version`.

### <a name="install-yeoman"></a>установка Yeoman;

Выполните следующую команду:

```bash
npm install -g yo
```

![установка Yeoman;](media/create-a-base-template-using-yeoman/ymg-npm-install-yo.png)

### <a name="install-the-yeoman-template-for-terraform-module"></a>Установка шаблона Yeoman для модуля Terraform

Выполните следующую команду:

```bash
npm install -g generator-az-terra-module
```

![Установить generator-az-terra-module](media/create-a-base-template-using-yeoman/ymg-pm-install-generator-module.png)

Чтобы проверить установку Yeoman, выполните следующую команду:

```bash
yo --version
```

### <a name="create-a-directory-for-the-yeoman-generated-module"></a>Создание каталога для модуля, созданного Yeoman

Шаблон Yeoman создает файлы в текущем каталоге. По этому вам необходимо создать каталог.

Этот пустой каталог необходимо поместить под $GOPATH/src. Дополнительные сведения об этом пути см. в статье [Настройка GOPATH](https://github.com/golang/go/wiki/SettingGOPATH).

1. Перейдите к родительскому каталогу, из которого нужно создать новый каталог.

1. Выполните следующую команду, заменив заполнитель. В этом примере используется имя каталога `GeneratorDocSample`.

    ```bash
    mkdir <new-directory-name>
    ```

    ![mkdir](media/create-a-base-template-using-yeoman/ymg-mkdir-GeneratorDocSample.png)

1. Перейдите в новый каталог:

    ```bash
    cd <new-directory-name>
    ```

    ![Перейдите в новый каталог.](media/create-a-base-template-using-yeoman/ymg-cd-GeneratorDocSample.png)

## <a name="create-a-base-module-template"></a>Создание шаблона базового модуля

1. Выполните следующую команду:

    ```bash
    yo az-terra-module
    ```

1. Чтобы предоставить следующие сведения, следуйте инструкциям на экране.

    - **Имя проекта модуля Terraform** — для примера используется значение `doc-sample-module`.

        ![Имя проекта](media/create-a-base-template-using-yeoman/ymg-project-name.png)


    - **Использовать файл образа Docker?** Введите `y`. Если ввести `n`, созданный код модуля будет поддерживать выполнение только в собственном режиме.

        ![Использовать файл образа Docker?](media/create-a-base-template-using-yeoman/ymg-include-docker-image-file.png) 

1. Для просмотра созданных конечных файлов, перечислите содержимое каталога.

    ```bash
    ls
    ```

    ![Созданный список файлов](media/create-a-base-template-using-yeoman/ymg-ls-GeneratorDocSample-files.png)

## <a name="review-the-generated-module-code"></a>Просмотр созданного кода модуля

1. Запустите Visual Studio Code.

1. На панели меню выберите **Файл > Открыть папку**, а затем найдите и выберите созданную папку.

    ![Visual Studio Code](media/create-a-base-template-using-yeoman/ymg-open-in-vscode.png)

Следующие файлы созданы генератором модуля Yeoman:

- `main.tf` — определяет модуль, именуемый `random-shuffle`. Входные данные — это `string_list`. Выходные данные — это количество перестановок.
- `variables.tf` — определяет входные и выходные переменные, которые использует модуль.
- `outputs.tf` — определяет выходные данные модуля. Здесь это значение, возвращаемое `random_shuffle`, которое является встроенным модулем Terraform.
- `Rakefile` — определяет шаги сборки. А именно:
    - `build` — проверяет форматирование файла main.tf.
    - `unit` — сгенерированная структура модуля не содержит код для модульного теста. Чтобы добавить здесь код, необходимо указать сценарий модульного теста.
    - `e2e` — запускает комплексный тест модуля.
- `test`
    - Тестовые случаи написаны на языке Go.
    - Все коды в тесте — комплексные.
    - Комплексные тесты пытаются подготавливать все элементы, определенные в разделе `fixture`. Результаты в файле `template_output.go` сравниваются с предварительно определенными ожидаемыми значениями.
    - `Gopkg.lock` и `Gopkg.toml`: Определяет зависимости.

Дополнительные сведения о генераторе Yeoman для Azure https://github.com/Azure/generator-az-terra-module см. в [документации по Terratest](https://terratest.gruntwork.io/docs/).

## <a name="test-your-new-terraform-module-using-a-docker-file"></a>Тестирование нового модуля Terraform с помощью файла Docker

В этом разделе показано, как тестировать модуль Terraform с помощью файла Docker.

>[!NOTE]
>В этом примере модуль выполняется не в Azure, а локально.

### <a name="confirm-docker-is-installed-and-running"></a>Убедитесь, что Docker установлен и запущен

Из командной строки введите `docker version`.

![Версия Docker](media/create-a-base-template-using-yeoman/ymg-docker-version.png)

Полученный результат подтверждает, что платформа Docker установлена.

Введите `docker info`, чтобы убедиться, что Docker действительно запущен.

![Сведения о Docker](media/create-a-base-template-using-yeoman/ymg-docker-info.png)

### <a name="set-up-a-docker-container"></a>Настройка контейнера Docker

1. Из командной строки введите

    `docker build --build-arg BUILD_ARM_SUBSCRIPTION_ID= --build-arg BUILD_ARM_CLIENT_ID= --build-arg BUILD_ARM_CLIENT_SECRET= --build-arg BUILD_ARM_TENANT_ID= -t terra-mod-example .`.

    Будет отображаться сообщение **Успешно создан**

    ![Сообщение, указывающее на успешную сборку](media/create-a-base-template-using-yeoman/ymg-successfully-built.png)

1. В командной строке введите `docker image ls`, чтобы увидеть в списке созданный модуль `terra-mod-example`.

    ![Список, содержащий новый модуль](media/create-a-base-template-using-yeoman/ymg-repository-results.png)

1. Введите `docker run -it terra-mod-example /bin/sh`. После выполнения команды `docker run` вы перейдете в среду Docker. На этом этапе файл можно обнаружить с помощью команды `ls`.

    ![Список файлов в Docker](media/create-a-base-template-using-yeoman/ymg-list-docker-file.png)

### <a name="build-the-module"></a>Сборка модуля

1. Выполните следующую команду:

    ```bash
    bundle install
    ```

1. Выполните следующую команду:

    ```bash
    rake build
    ```

    ![Сборка с помощью Rake](media/create-a-base-template-using-yeoman/ymg-rake-build.png)

### <a name="run-the-end-to-end-test"></a>Запуск комплексного теста

1. Выполните следующую команду:

    ```bash
    rake e2e
    ```

1. Через несколько секунд появится сообщение **Проверка пройдена**.

    ![PASS](media/create-a-base-template-using-yeoman/ymg-pass.png)

1. Введите `exit`, чтобы завершить тест и выйти из среды Docker.

## <a name="use-yeoman-generator-to-create-and-test-a-module-in-cloud-shell"></a>Создание и тестирование модуля в Cloud Shell с использованием генератора Yeoman

В этом разделе вы создадите и протестируете модуль в Cloud Shell, используя генератор Yeoman. Использование Cloud Shell вместо файла Docker значительно упрощает процесс. При использовании Cloud Shell все перечисленные ниже продукты предварительно установлены.

- Node.js
- Yeoman
- Terraform

### <a name="start-a-cloud-shell-session"></a>Запуск сеанса Cloud Shell

1. Запустите сеанс Azure Cloud Shell через портал [Azure](https://portal.azure.com/), [shell.azure.com](https://shell.azure.com) или [мобильное приложение Azure](https://azure.microsoft.com/features/azure-portal/mobile-app/).

1. Откроется страница **приветствия Azure Cloud Shell**. Выберите **Bash (Linux)** .

    ![Страница приветствия Azure Cloud Shell](media/create-a-base-template-using-yeoman/ymg-welcome-to-azure-cloud-shell.png)

1. Если вы еще не настроили учетную запись хранения Azure, откроется следующий экран. Щелкните **Создать хранилище**.

    ![Подключенное хранилище отсутствует](media/create-a-base-template-using-yeoman/ymg-you-have-no-storage-mounted.png)

1. Azure Cloud Shell запускается в оболочке, которую вы выбрали ранее, и отображает сведения о только что созданном облачном диске.

    ![Ваш облачный диск успешно создан](media/create-a-base-template-using-yeoman/ymg-your-cloud-drive-has-been-created-in.png)

### <a name="prepare-a-directory-to-hold-your-terraform-module"></a>Подготовка каталога для хранения модуля Terraform

1. На этом этапе служба Cloud Shell уже настроила для вас расположение GOPATH в переменных среды. Введите `go env`, чтобы увидеть путь.

1. Создайте каталог $GOPATH, если он еще не существует. Введите `mkdir ~/go`.

1. Создайте каталог в каталоге $GOPATH. Этот каталог используется для хранения различных каталогов проектов, созданных в этом примере. 

    ```bash
    mkdir ~/go/src
    ```

1. Создайте каталог для хранения модуля Terraform, заменив заполнитель. В этом примере используется имя каталога `my-module-name`.

    ```bash
    mkdir ~/go/src/<your-module-name>
    ```

1. Перейдите в каталог модуля: 

    ```bash
    cd ~/go/src/<your-module-name>
    ```

### <a name="create-and-test-your-terraform-module"></a>Создание и тестирование модуля Terraform

1. Выполните следующую команду и следуйте инструкциям. Когда появится вопрос о необходимости создания файлов Docker, введите `N`.

    ```bash
    yo az-terra-module
    ```

1. Выполните следующую команду, чтобы установить зависимости:

    ```bash
    bundle install
    ```

1. Запустите сборку модуля с помощью следующей команды:

    ```bash
    rake build
    ```

    ![Сборка с помощью Rake](media/create-a-base-template-using-yeoman/ymg-rake-build.png)

1. Запустите тест с помощью следующей команды:

    ```bash
    rake e2e
    ```

    ![Тест — результаты теста](media/create-a-base-template-using-yeoman/ymg-pass.png)

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Установка и использование расширения Visual Studio Code для Azure Terraform](/azure/terraform/terraform-vscode-extension).
