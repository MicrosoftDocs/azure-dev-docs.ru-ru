---
title: Краткое руководство по началу работы с Terraform в Windows
description: Из этого краткого руководства можно узнать, как установить и настроить Terraform для создания ресурсов Azure.
keywords: azure devops terraform установка настройка windows init план применение выполнение вход портал rbac субъект-служба автоматизированный сценарий cli powershell
ms.topic: quickstart
ms.date: 06/12/2020
ms.openlocfilehash: d28a79af9a59551153f297cebfb0c51ed2e72838
ms.sourcegitcommit: 2d6c9687b39e33a6b5e980d9a375c9f8f1f2cab7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/15/2020
ms.locfileid: "84783806"
---
# <a name="quickstart-getting-started-with-terraform-using-windows"></a>Краткое руководство. Начало работы с Terraform в Windows
 
[!INCLUDE [terraform-intro.md](includes/terraform-intro.md)]

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]

## <a name="configure-your-environment"></a>Настройка среды

1. Для работы в Azure PowerShell на всех платформах, включая Windows, мы рекомендуем использовать PowerShell 7 или более поздней версии. Если среда PowerShell уже установлена, версию можно проверить с помощью следующей команды в командной строке PowerShell:

    ```powershell
    $PSVersionTable.PSVersion
    ```
    
1. Следуйте инструкциям в статье [Установка PowerShell в Windows](https://docs.microsoft.com/powershell/scripting/install/installing-powershell-core-on-windows?view=powershell-7), чтобы установить последнюю версию PowerShell.

1. В этой статье используется [модуль Azure PowerShell Az](https://docs.microsoft.com/powershell/azure/new-azureps-module-az). Следуйте инструкциям в статье [Установка Azure CLI в Windows](/cli/azure/install-azure-cli-windows?view=azure-cli-latest).

1. Откройте экземпляр только что установленной версии PowerShell.

## <a name="log-into-azure"></a>Вход на портал Azure

У вас есть несколько механизмов для входа в подписку Azure:

- [вход в учетную запись Майкрософт](#log-into-your-microsoft-account);
- [вход с помощью субъекта-службы Azure](#log-into-azure-using-an-azure-service-principal).

### <a name="log-into-your-microsoft-account"></a>Вход в учетную запись Майкрософт

При вызове команды [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/Connect-AzAccount) без параметров отображается URL-адрес и код. Перейдите по URL-адресу, введите код и следуйте инструкциям по входу в Azure с помощью учетной записи Майкрософт. После входа вернитесь на портал.

```powershell
Connect-AzAccount
```

Примечания.
- После успешного входа `Connect-AzAccount` отображает подписку Azure по умолчанию, которая связана с предоставленной для входа учетной записью Майкрософт. Чтобы узнать, как переключиться на другую подписку Azure, см. раздел [Определение текущей подписки Azure](#specify-the-current-azure-subscription).

### <a name="log-into-azure-using-an-azure-service-principal"></a>Вход в Azure с помощью субъекта-службы Azure

**Создайте субъект-службу Azure.** Чтобы войти в подписку Azure с помощью субъекта-службы, сначала необходимо получить доступ к этому субъекту-службе. Если у вас уже есть субъект-служба, можете пропустить эту часть раздела.

Разрешения для автоматизированных средств, которые развертывают или используют службы Azure (такие как Terraform), всегда должны быть ограничены. В качестве замены входу в приложения с использованием учетной записи пользователя с полными правами Azure предлагает субъекты-службы. Но что делать, если у вас нет субъекта-службы, с которым будет выполняться вход? В этом сценарии можно войти с использованием учетных данных пользователя, а затем создать субъект-службу. После создания субъекта-службы можно использовать его данные для будущих попыток входа.

Есть множество способов [создать субъект-службу с помощью PowerShell](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps). В рамках этой статьи мы создадим субъект-службу с ролью **участника**. Роль **Участник** (роль по умолчанию) предоставляет полные разрешения на чтение и запись в учетной записи Azure. Дополнительные сведения об управлении доступом на основе ролей см. в статье [Встроенные роли для управления доступом на основе ролей в Azure](/azure/active-directory/role-based-access-built-in-roles).

Вызов [New-AzADServicePrincipal](https://docs.microsoft.com/powershell/module/Az.Resources/New-AzADServicePrincipal) позволяет создать субъект-службу для указанной подписки. После успешного завершения отображаются сведения о созданном субъекте-службе, например имена и отображаемое имя субъекта-службы. При вызове `New-AzADServicePrincipal` без указания учетных данных для проверки подлинности пароль создается автоматически. Но этот пароль не отображается, так как возвращается с типом `SecureString`. Это означает, что вам нужно при вызове `New-AzADServicePrincipal` направить результаты в переменную. Затем из этой переменной вы сможете получить пароль.

1. Введите следующую команду, заменив `<subscription_id>` значением идентификатора учетной записи подписки, которую хотите использовать:

    ```powershell
    $sp = New-AzADServicePrincipal -Scope /subscriptions/<subscription_id>
    ```

1. Введите следующую команду, чтобы отобразить имена субъекта-службы:

    ```powershell
    $sp.ServicePrincipalNames
    ```

1. Вызовите `ConvertFrom-SecureString`, чтобы отобразить пароль в виде текста:

    ```powershell
    $UnsecureSecret = ConvertFrom-SecureString -SecureString $sp.Secret -AsPlainText
    ```

Примечания.
- На этом этапе вам известны имя и пароль для субъекта-службы. Эти значения вам нужны для входа в подписку с помощью субъекта-службы.
- Пароль невозможно извлечь, если он утерян. Следовательно, пароль нужно хранить в надежном месте. Если вы забыли пароль, потребуется [сбросить учетные данные субъекта-службы](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps#reset-credentials).

**Использование субъекта-службы Azure для входа.** Чтобы войти в подписку Azure с помощью субъекта-службы, вызовите `Connect-AzAccount` и передайте объект с типом [PsCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential). Вход можно выполнять интерактивно или из скрипта.

- **Интерактивный шаблон.** Вызовите [Get-Credential](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-credential) и введите учетные данные в ответ на соответствующий запрос. Вызов `Get-Credential` возвращает объект `PsCredential`, который затем передается в `Connect-AzAccount`.

    1. Вызовите `Get-Credential` и вручную введите имя и пароль субъекта-службы.

        ```powershell
        $Credential = Get-Credential
        ```

    2. Вызовите `Connect-AzAccount`, передав объект `PsCredential` (замените заполнитель `<azureSubscriptionTenantId>` значение идентификатора клиента подписки Azure).

        ```powershell
        Connect-AzAccount -Credential $Credential -Tenant <azureSubscriptionTenantId> -ServicePrincipal
        ```

- **Шаблон скрипта.** Создайте объект `PsCredential` и передайте его в `Connect-AzConnect`.

    1. Создайте `Get-Credential` (замените заполнители соответствующими значениями для подписки и субъекта-службы Azure).

        ```powershell
        $spName = "<servicePrincipalName>"
        $spPassword = ConvertTo-SecureString "<servicePrincipalPassword>" -AsPlainText -Force
        $psCredential = New-Object System.Management.Automation.PSCredential($spName , $spPassword)
        ```

    1. Вызовите `Connect-AzAccount`, передав созданный объект `PsCredential`.

        ```powershell
        Connect-AzAccount -Credential $psCredential -TenantId "<azureSubscriptionTenantId>"  -ServicePrincipal
        ```

## <a name="specify-the-current-azure-subscription"></a>Определение текущей подписки Azure

Как уже объяснялось в предыдущем разделе, в следующих сценариях описываются два способа входа в Azure.

- **Вход с использованием учетной записи Майкрософт.** Учетную запись Майкрософт можно связать с несколькими подписками Azure, одна из которых является подпиской по умолчанию. Подписка по умолчанию используется, если вы входите в систему и не переключаетесь на другую подписку.
- **Вход с помощью субъекта-службы Azure**: Субъект-служба относится к подписке Azure. Помните, что при входе в систему отображаются сведения о подписке.

Перечисленные ниже шаги реализуют первый сценарий, где выполняются следующие задачи.

- Просмотр сведений о текущей подписке Azure.
- Составление списка всех доступных подписок Azure для текущей учетной записи Майкрософт.
- Переключение на другую подписку Azure.

1. Чтобы узнать текущую подписку Azure, используйте [Get-AzContext](https://docs.microsoft.com/powershell/module/az.accounts/get-azcontext).

    ```powershell
    Get-AzContext
    ```

1. Если у вас несколько доступных подписок Azure, используйте `Get-AzContext -ListAvailable`.

    ```powershell
    Get-AzContext -ListAvailable | Select Name
    ```

1. Автоматически создаваемые имена контекста могут быть громоздкими. Чтобы имена контекста было проще читать (и использовать в качестве параметров), можно переименовать контексты. Для переименования используется [Rename-AzContext](https://docs.microsoft.com/powershell/module/az.accounts/rename-azcontext).

    ```powershell
    Rename-AzContext -SourceName <current_context_name> -TargetName <new_context_Name>
    ```

1. Чтобы применить конкретную подписку Azure к текущему сеансу PowerShell, используйте [Select-AzContext](https://docs.microsoft.com/powershell/module/az.accounts/select-azcontext).

    ```powershell
    Select-AzContext <context_name>
    ```

## <a name="configure-terraform"></a>Настройка Terraform

1. [Скачайте Terraform](https://www.terraform.io/downloads.html).

1. Извлеките исполняемый файл из скачанного содержимого в произвольный каталог.

1. [Измените глобальный путь в системе](https://stackoverflow.com/questions/1618280/where-can-i-set-path-to-make-exe-on-windows), чтобы включить в него путь к этому исполняемому файлу.

1. Проверьте конфигурацию глобального пути, выполнив команду `terraform`. Если средство Terraform успешно найдено, отображается список доступных параметров Terraform:

    ```powershell
    terraform
    ```

    Если средство Terraform успешно найдено, оно отобразит сведения о синтаксисе и доступных командах:

    ```output
    Usage: terraform [-version] [-help] <command> [args]

    The available commands for execution are listed below.
    The most common, useful commands are shown first, followed by
    less common or more advanced commands. If you're just getting
    started with Terraform, stick with the common commands. For the
    other commands, please read the help and docs before usage.
    ...
    ```

## <a name="create-a-terraform-configuration-file"></a>Создание файла конфигурации Terraform

Из этого раздела вы узнаете, как создать файл конфигурации Terraform, который позволяет создать группу ресурсов Azure.

1. Создайте каталог для хранения файлов Terraform для этой демонстрации.

1. Перейдите в каталог демонстрации.

1. С помощью любого редактора создайте файл конфигурации Terraform с именем `QuickstartTerraformTest.tf`.

1. Скопируйте приведенный ниже код HCL и вставьте его в новый файл.

    ```hcl
    provider "azurerm" {
      # The "feature" block is required for AzureRM provider 2.x.
      # If you are using version 1.x, the "features" block is not allowed.
      version = "~>2.0"
      features {}
    }
    resource "azurerm_resource_group" "rg" {
            name = "QuickstartTerraformTest-rg"
            location = "eastus"
    }
    ```

    Примечания.
    - Блок поставщика определяет использование [поставщика Azure (azurerm)](https://www.terraform.io/docs/providers/azurerm/index.html).
    - В блоке поставщика azurerm задаются параметры версии и атрибутов возможностей. Как указано в комментариях, их использование зависит от конкретной версии. Дополнительные сведения о настройке этих атрибутов для среды см. в разделе [Поставщик AzureRM версии 2.0](https://www.terraform.io/docs/providers/azurerm/guides/2.0-upgrade-guide.html).
    - Только [объявление ресурса](https://www.terraform.io/docs/configuration/resources.html) предназначено для типа ресурса [azurerm_resource_group](https://www.terraform.io/docs/providers/azurerm/r/resource_group.html). Для azure_resource_group нужно передать два обязательных параметра: имя и расположение.

## <a name="create-and-apply-a-terraform-execution-plan"></a>Создание и применение плана выполнения Terraform

После создания файлов конфигурации воспользуйтесь инструкциями в этом разделе, чтобы создать *план выполнения* и применить его к своей облачной инфраструктуре.

1. Инициализируйте развертывание Terraform с помощью команды [terraform init](https://www.terraform.io/docs/commands/init.html). На этом шаге загружаются модули Azure, необходимые для создания группы ресурсов Azure.

    ```powershell
    terraform init
    ```
    
1. Terraform позволяет выполнять предварительный просмотр действий с помощью команды [terraform plan](https://www.terraform.io/docs/commands/plan.html).

    ```powershell
    terraform plan
    ```

    **Примечания.**
    - Команда `terraform plan` создает план выполнения, но не выполняет его. Вместо этого она определяет, какие действия необходимы для создания конфигурации, заданной в файлах конфигурации.
    - Команда `terraform plan` позволяет проверить, соответствует ли план выполнения вашим ожиданиям, прежде чем вносить изменения в фактические ресурсы.
    - Необязательный параметр `-out` позволяет указать выходной файл для плана. Дополнительные сведения об использовании параметра `-out` см. в разделе [Сохранение планов выполнения для последующего развертывания](#persist-an-execution-plan-for-later-deployment).

1. Примените план выполнения с помощью команды [terraform apply](https://www.terraform.io/docs/commands/apply.html).

    ```powershell
    terraform apply
    ```
    
1. Terraform показывает, что произойдет, если применить план выполнения, и требует подтвердить его выполнение. Подтвердите команду, введя `yes` и нажав клавишу **ВВОД**.

1. После подтверждения выполнения плана убедитесь, что группа ресурсов была успешно создана с помощью команды [az group show](/cli/azure/group?view=azure-cli-latest#az-group-show).

    ```powershell
    Get-AzResourceGroup -Name QuickstartTerraformTest-rg
    ```

    В случае успеха команда отображает различные свойства только что созданной группы ресурсов.

## <a name="persist-an-execution-plan-for-later-deployment"></a>Сохранение плана выполнения для последующего развертывания

В предыдущем разделе вы узнали, как выполнить команду [terraform plan](https://www.terraform.io/docs/commands/plan.html) для создания плана выполнения. Затем вы увидели, что команда [terraform apply](https://www.terraform.io/docs/commands/apply.html) применяет этот план. Этот шаблон хорошо работает, когда шаги являются интерактивными и последовательными.

Для более сложных сценариев можно сохранить план выполнения в файл. Позднее или даже с другого компьютера можно применить этот план выполнения.

При использовании этой функции рекомендуется прочитать статью [Автоматический запуск Terraform](https://learn.hashicorp.com/terraform/development/running-terraform-in-automation).

Следующие шаги иллюстрируют базовый шаблон использования этой функции:

1. Запустите [terraform init](https://www.terraform.io/docs/commands/init.html).

    ```powershell
    terraform init
    ```

1. Запустите `terraform plan` с параметром `-out`.

    ```powershell
    terraform plan -out QuickstartTerraformTest.tfplan
    ```

1. Запустите `terraform apply`, указав имя файла из предыдущего шага.

    ```powershell
    terraform apply QuickstartTerraformTest.tfplan
    ```

Примечания.
- Чтобы включить использование с автоматизацией, запуск `terraform apply <filename>` не требует подтверждения.
- Если вы решили использовать эту функцию, ознакомьтесь с разделом [Предупреждение системы безопасности](https://www.terraform.io/docs/commands/plan.html#security-warning).

## <a name="clean-up-resources"></a>Очистка ресурсов

Удалите ресурсы Azure, созданные в рамках этой статьи, если они вам больше не нужны.

1. Выполните команду [terraform destroy](https://www.terraform.io/docs/commands/destroy.html), которая отменит текущий план выполнения.

    ```powershell
    terraform destroy
    ```

1. Terraform показывает, что произойдет при отмене плана выполнения, и требует подтверждения. Подтвердите команду, введя `yes` и нажав клавишу **ВВОД**.

1. После подтверждения выполнения плана выходные данные будут выглядеть так, как показано в следующем примере. Убедитесь, что группа ресурсов была удалена с помощью команды [az group show](/cli/azure/group?view=azure-cli-latest#az-group-show).

    ```powershell
    Get-AzResourceGroup -Name QuickstartTerraformTest-rg
    ```

    Примечания.
    - При успешном удалении `Get-AzResourceGroup` отобразит сведения о том, что группа ресурсов не существует.

1. Перейдите в родительский каталог и удалите демонстрационный каталог. Параметр `-r` удаляет содержимое каталога перед удалением самого каталога. Среди содержимого каталога: файл конфигурации, созданный ранее, и файлы состояния Terraform.

    ```bash
    cd .. && rm -r QuickstartTerraformTest
    ```

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Создание готовой инфраструктуры виртуальных машин Linux в Azure с помощью Terraform](create-linux-virtual-machine-with-infrastructure.md)