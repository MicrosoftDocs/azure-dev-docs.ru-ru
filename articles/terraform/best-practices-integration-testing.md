---
title: Руководство по интеграционному тестированию с помощью Terraform и Azure
description: Сведения об интеграционном тестировании и применении Azure DevOps для настройки непрерывной интеграции для проектов Terraform.
ms.topic: tutorial
ms.date: 10/08/2020
ms.custom: devx-track-terraform
ms.openlocfilehash: 7b10693907feedf94db60315d8819d45fdd47412
ms.sourcegitcommit: b19420d1a8d526a81e0835012cccc46717fadd69
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/15/2021
ms.locfileid: "100524899"
---
# <a name="tutorial-configure-integration-tests-for-terraform-projects-in-azure"></a>Руководство по Настройка интеграционного тестирования для проектов Terraform в Azure

[!INCLUDE [terraform-intro.md](includes/terraform-intro.md)]

В этой статье вы узнаете, как выполнить следующие задачи:

> [!div class="checklist"]
> * Ознакомитесь с основами интеграционного тестирования для проектов Terraform.
> * Примените Azure DevOps для настройки конвейера непрерывной интеграции.
> * Выполните статический анализ кода для кода Terraform.
> * Выполните `terraform validate`, чтобы проверить файлы конфигурации Terraform на локальном компьютере.
> * Выполните `terraform plan`, чтобы проверить файлы конфигурации Terraform с точки зрения удаленных служб.
> * Примените конвейер Azure для автоматизации непрерывной интеграции.

## <a name="prerequisites"></a>Предварительные условия

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
- **Организация и проект Azure DevOps.** Если у вас нет организации Azure DevOps, [создайте ее](/azure/devops/organizations/projects/create-project).
- **Расширение сборки и выпуска Terraform.** [Установите расширение сборки и выпуска Terraform](https://marketplace.visualstudio.com/items?itemName=charleszipp.azure-pipelines-tasks-terraform) в организации Azure DevOps.
- **Доступ Azure DevOps к подписке Azure.** Создайте [подключение службы Azure](/azure/devops/pipelines/library/connect-to-azure) с именем `terraform-basic-testing-azure-connection`, чтобы разрешить Azure Pipelines подключаться к подпискам Azure.
- **Наличие Terraform.** [Скачайте и установите версию Terraform](https://www.terraform.io/downloads.html), которая соответствует вашей среде.
- **Вилка примеров тестирования.** Создайте вилку [примера проекта Terraform на GitHub](https://github.com/Azure/terraform) и клонируйте его на локальный компьютер разработки и тестирования.

## <a name="validate-a-local-terraform-configuration"></a>Проверка конфигурации Terraform на локальном компьютере

Команда [terraform validate](https://www.terraform.io/docs/commands/validate.html) выполняется из командной строки в каталоге, который содержит файлы Terraform. Основной задачей этой команды является проверка синтаксиса.

1. Откройте любую среду командной строки. Интерфейс командной строки предоставляют многие редакторы кода, например Visual Studio Code.

1. Перейдите в каталог `samples/integration-testing/src` локального репозитория. Он содержит простой проект Terraform.

1. Инициализируйте развертывание Terraform с помощью команды [terraform init](https://www.terraform.io/docs/commands/init.html). На этом шаге загружаются модули Azure, необходимые для создания группы ресурсов Azure.

    ```bash
    terraform init
    ```

1. Проверьте тестовый файл Terraform с помощью команды [terraform validate](https://www.terraform.io/docs/commands/validate.html).

    ```bash
    terraform validate
    ```

    Поступит сообщение о том, что конфигурация является допустимой.

1. Откройте файл `main.tf` в редакторе кода.

1. В строке 5 добавьте опечатку, которая сделает синтаксис недопустимым. Например, измените `var.location` на `var.loaction`.

1. Сохраните файл.

1. Запустите проверку еще раз.

    ```bash
    terraform validate
    ```

    На этот раз вы увидите сообщение об ошибке с номером проблемной строки и описанием ошибки.

Как видите, средство Terraform обнаружило ошибку в синтаксисе кода конфигурации. Эта проблема не позволит развернуть конфигурацию.

Мы рекомендуем всегда запускать `terraform validate` для файлов Terraform, прежде чем отправлять их в систему управления версиями. Кроме того, этот уровень проверки следует включить в конвейер непрерывной интеграции. Далее в этом руководстве объясняется, [как настроить конвейер Azure для автоматической проверки](#automate-integration-tests-using-azure-pipeline).

## <a name="validate-terraform-configuration-can-be-deployed-on-azure"></a>Проверка того, что конфигурация Terraform может быть развернута в Azure

Из предыдущего раздела вы узнали, как проверить конфигурацию Terraform. Этот уровень тестирования относился только к синтаксису. В этом тесте не учитывалось, какие элементы конфигурации уже развернуты в Azure.

Terraform является *декларативным языком*, то есть с его помощью вы объявляете желаемый конечный результат. Предположим, у вас есть 10 виртуальных машин в группе ресурсов. После этого вы создаете файл Terraform с определением трех виртуальных машин. Применение этого плана не приведет к увеличению числа виртуальных машин до 13. Наоборот, Terraform удалит семь виртуальных машин, чтобы в итоге их стало ровно три. Выполнение `terraform plan` позволяет заранее проверить результаты применения плана выполнения, чтобы избежать неожиданностей подобного рода.

Чтобы создать план выполнения Terraform, выполните команду [terraform plan](https://www.terraform.io/docs/commands/plan.html). Эта команда подключается к целевой подписке Azure и проверяет, какая часть целевой конфигурации уже развернута. Затем Terraform определяет изменения, которые нужно внести для выполнения требований, указанных в файле Terraform. На этом этапе Terraform ничего не развертывает. Вам лишь предоставляется информация о том, что произойдет при применении этого плана.

Если вы применяете примеры из этого руководства и уже выполнили действия, описанные в предыдущем разделе, выполните команду `terraform plan`:

```bash
terraform plan
```

После выполнения `terraform plan` Terraform отобразит потенциальный результат применения созданного плана выполнения. В выходных данных вы увидите ресурсы Azure, которые будут добавлены, изменены и уничтожены.

По умолчанию Terraform сохраняет состояние в том же локальном каталоге, где размещается файл Terraform. Этот шаблон хорошо подходит для сценариев работы одного пользователя. Но если с некоторыми ресурсами Azure работают несколько пользователей, локальные файлы состояния могут утратить синхронизацию. Чтобы устранить эту проблему, Terraform поддерживает запись файлов состояния в удаленное хранилище данных (например, в службу хранилища Azure). В этом сценарии могут возникнуть проблемы при выполнении `terraform plan` на локальном компьютере и сохранении данных на удаленный компьютер. Возможно, будет лучше [автоматизировать этот этап проверки в конвейере непрерывной интеграции](#automate-integration-tests-using-azure-pipeline).

## <a name="run-static-code-analysis"></a>Выполнение статического анализа кода

Статический анализ кода можно провести для кода конфигурации Terraform напрямую, без реального выполнения. Такой анализ полезен для обнаружения проблем безопасности и несоответствия нормативным требованиям.

Следующие средства поддерживают статический анализ для файлов Terraform:

- [Checkov](https://github.com/bridgecrewio/checkov/);
- [Terrascan](https://github.com/accurics/terrascan);
- [tfsec](https://github.com/tfsec/tfsec);
- [Deepsource](https://deepsource.io/blog/release-terraform-static-analysis/). 

Статический анализ часто выполняется в конвейерах непрерывной интеграции. Для этих тестов не придется создавать план выполнения или развертывание. Это позволяет проводить их быстрее, чем другие тесты, поэтому они обычно выполняются первыми в процессе непрерывной интеграции.

## <a name="automate-integration-tests-using-azure-pipeline"></a>Автоматизация интеграционных тестов с помощью конвейера Azure Pipelines

Непрерывная интеграция подразумевает тестирование всей системы при любом внесении изменений. В рамках этого раздела вы изучите конфигурацию конвейера Azure Pipelines, которая используется для реализации непрерывной интеграции.

1. В любом редакторе кода перейдите к локальному клону [примера проекта Terraform на сайте GitHub](https://github.com/Azure/terraform).

1. Откройте файл `samples/integration-testing/src/azure-pipeline.yaml` .

1. Прокрутите вниз до раздела **steps**, где вы увидите стандартный набор действий для выполнения процедур установки и проверки.

1. Найдите здесь строку с текстом **Step 1: run the Checkov Static Code Analysis** (Шаг 1. Выполнение статического анализа кода Checkov). На этом этапе в рамках упомянутого ранее проекта `Checkov` выполняется статический анализ кода для примера конфигурации Terraform. 

    ```yaml
    - bash: $(terraformWorkingDirectory)/checkov.sh $(terraformWorkingDirectory)
      displayName: Checkov Static Code Analysis
    ```
    
    Примечание.
    
    - Этот скрипт позволяет выполнить запуск Checkov в рабочей области Terraform, подключенной к контейнеру Docker. Все агенты, управляемые Майкрософт, поддерживают Docker. Так упрощается запуск средств внутри контейнера Docker и вам не придется устанавливать Checkov на агенте Azure Pipelines.
    - Переменная `$(terraformWorkingDirectory)` определена в файле `azure-pipeline.yaml`.

1. Найдите строку с текстом **Step 2: install Terraform on the Azure Pipelines agent** (Шаг 2. Установка Terraform на агенте Azure Pipelines). [Расширение сборки и выпуска Terraform](https://marketplace.visualstudio.com/items?itemName=charleszipp.azure-pipelines-tasks-terraform), которое вы установили ранее, содержит команду для установки Terraform в агенте, который выполняет конвейер Azure Pipelines. Именно такая задача выполняется на этом этапе.

    ```yaml
    - task: charleszipp.azure-pipelines-tasks-terraform.azure-pipelines-tasks-terraform-installer.TerraformInstaller@0
      displayName: 'Install Terraform'
      inputs:
        terraformVersion: $(terraformVersion)
    ```
    
    Примечание.

    - Версия Terraform, которую нужно установить, указывается в переменной Azure Pipelines с именем `terraformVersion`, которая определяется в файле `azure-pipeline.yaml`.

1. Найдите строку с текстом **Step 3: run Terraform init to initialize the workspace** (Шаг 3. Выполнение Terraform init для инициализации рабочей области). Теперь, когда средство Terraform установлено в агенте, можно инициализировать каталог Terraform.

    ```yaml
    - task: charleszipp.azure-pipelines-tasks-terraform.azure-pipelines-tasks-terraform-cli.TerraformCLI@0
      displayName: 'Run terraform init'
      inputs:
        command: init
        workingDirectory: $(terraformWorkingDirectory)
    ```
    
    Примечания.

    - Входной параметр `command` указывает, какую команду Terraform нужно выполнить.
    - Входной параметр `workingDirectory` указывает путь к каталогу Terraform.
    - Переменная `$(terraformWorkingDirectory)` определена в файле `azure-pipeline.yaml`.

1. Найдите строку с текстом **Step 4: run Terraform validate to validate HCL syntax** (Шаг 4. Выполнение Terraform validate для проверки синтаксиса HCL). После инициализации каталога проекта запускается `terraform validate`, чтобы проверить конфигурацию на сервере.

    ```yaml
    - task: charleszipp.azure-pipelines-tasks-terraform.azure-pipelines-tasks-terraform-cli.TerraformCLI@0
      displayName: 'Run terraform validate'
      inputs:
        command: validate
        workingDirectory: $(terraformWorkingDirectory)
    ```
    
1. Найдите строку с текстом **Step 5: run Terraform plan to validate HCL syntax** (Шаг 5. Выполнение Terraform plan для проверки синтаксиса HCL). Как упоминалось ранее, создание плана выполнения нужно для проверки допустимости конфигурации Terraform перед реальным развертыванием.

    ```yaml
    - task: charleszipp.azure-pipelines-tasks-terraform.azure-pipelines-tasks-terraform-cli.TerraformCLI@0
      displayName: 'Run terraform plan'
      inputs:
        command: plan
        workingDirectory: $(terraformWorkingDirectory)
        environmentServiceName: $(serviceConnection)
        commandOptions: -var location=$(azureLocation)
    ```
    
    Примечания.

    - Параметр `environmentServiceName` указывает имя подключения к службе Azure, созданного в рамках раздела [предварительных требований](#prerequisites). Это подключение позволяет Terraform получить доступ к подписке Azure.
    - Параметр `commandOptions` используется для передачи аргументов в команду Terraform. В нашем примере здесь указывается расположение. Переменная `$(azureLocation)` определена ранее в файле YAML.

### <a name="import-the-pipeline-into-azure-devops"></a>Импорт конвейера в Azure DevOps

1. Откройте проект Azure DevOps и перейдите к разделу Azure Pipelines.
 
1. Нажмите кнопку **Создать конвейер**.

1. В окне **Где ваш код?** выберите вариант **GitHub (YAML)** .

    ![Где ваш код?](media/best-practices-integration-testing/new-pipeline-where-github-yaml.png)

1. На этом этапе, возможно, потребуется авторизовать для Azure DevOps доступ к вашей организации. Дополнительные сведения на эту тему см. в статье [Создание репозиториев GitHub](/azure/devops/pipelines/repos/github).

1. В списке репозиториев выберите вилку репозитория, которую вы создали в своей организации GitHub.

1. На шаге **Настройка конвейера** выберите запуск из существующего конвейера YAML.

    ![Существующий конвейер YAML](media/best-practices-integration-testing/new-pipeline-existing-yaml.png)

1. Когда откроется страница **выбора существующего конвейера YAML**, укажите ветвь `master` и путь к конвейеру YAML: `samples/integration-testing/src/azure-pipeline.yaml`.

    ![Выбор существующего конвейера YAML](media/best-practices-integration-testing/select-existing-yaml-pipeline.png)

1. Щелкните элемент **Продолжить**, чтобы загрузить из GitHub конвейер Azure YAML.

1. Когда откроется страница **проверки конвейера YAML**, щелкните команду **Запустить**, чтобы создать и вручную запустить первое выполнение конвейера.

    ![Запуск конвейера Azure](media/best-practices-integration-testing/run-pipeline.png)

### <a name="run-the-pipeline"></a>Запуск конвейера

Вы можете запустить конвейер вручную через пользовательский интерфейс Azure DevOps. Но эта статья посвящена созданию автоматизированной непрерывной интеграции. Протестируйте процесс, зафиксировав любое изменение в папке `samples/integration-testing/src` локальной ветви репозитория. Это изменение приведет к автоматическому запуску нового конвейера для той ветви, в которую вы отправляете код.

![Конвейер, выполняемый для GitHub](media/best-practices-integration-testing/pipeline-running-from-github.png)

После завершения этого этапа изучите представленные в Azure DevOps сведения и убедитесь, что все выполнено правильно.

![Зеленый конвейер Azure DevOps](media/best-practices-integration-testing/azure-devops-green-pipeline.png)

[!INCLUDE [terraform-troubleshooting.md](includes/terraform-troubleshooting.md)]

## <a name="next-steps"></a>Дальнейшие шаги

> [!div class="nextstepaction"]
> [Создание и выполнение тестов соответствия в проектах Terraform](best-practices-compliance-testing.md)
