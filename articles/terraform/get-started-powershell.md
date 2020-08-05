---
title: Краткое руководство. Начало работы с Terraform с использованием PowerShell
description: Из этого краткого руководства можно узнать, как установить и настроить Terraform для создания ресурсов Azure.
keywords: azure devops terraform установка настройка windows init план применение выполнение вход портал rbac субъект-служба автоматизированный сценарий powershell
ms.topic: quickstart
ms.date: 07/27/2020
ms.openlocfilehash: 40663f23d79066354cb7a78318eba7a8998676c5
ms.sourcegitcommit: cf23d382eee2431a3958b1c87c897b270587bde0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/29/2020
ms.locfileid: "87400662"
---
# <a name="quickstart-get-started-with-terraform-using-powershell"></a>Краткое руководство. Начало работы с Terraform с использованием PowerShell
 
[!INCLUDE [terraform-intro.md](includes/terraform-intro.md)]

В этой статье описывается, как начать работу с [Terraform в Azure](https://www.terraform.io/docs/providers/azurerm/index.html) с использованием PowerShell.

[!INCLUDE [hashicorp-support.md](includes/hashicorp-support.md)]

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]

## <a name="configure-your-environment"></a>Настройка среды

1. Последний модуль PowerShell, который позволяет взаимодействовать с ресурсами Azure, называется [модулем Az для Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az). При использовании модуля Az для Azure PowerShell для всех платформ рекомендуется наличие версии PowerShell 7 (или более поздней). Если среда PowerShell уже установлена, версию можно проверить с помощью следующей команды в командной строке PowerShell.

    ```powershell
    $PSVersionTable.PSVersion
    ```

1. [Установите PowerShell.](https://docs.microsoft.com/powershell/scripting/install/installing-powershell-core-on-windows?view=powershell-7) Эта демонстрация была протестирована с использованием PowerShell 7.0.2 в Windows 10.

1. Чтобы разрешить [Terraform выполнять аутентификацию в Azure](https://www.terraform.io/docs/providers/azurerm/guides/azure_cli.html), вам нужно [установить Azure CLI](/cli/azure/install-azure-cli-windows?view=azure-cli-latest). Эта демонстрация была протестирована с использованием Azure CLI версии 2.9.1.

1. [Скачайте Terraform](https://www.terraform.io/downloads.html).

1. Извлеките исполняемый файл из скачанного содержимого в произвольный каталог.

1. [Измените глобальный путь в системе](https://stackoverflow.com/questions/1618280/where-can-i-set-path-to-make-exe-on-windows), чтобы включить в него путь к этому исполняемому файлу.

1. Проверьте конфигурацию глобального пути, выполнив команду `terraform`.

    ```powershell
    terraform
    ```

    **Примечания**
    - Если исполняемый файл Terraform будет найден, команда выведет данные о синтаксисе и список доступных команд.

## <a name="create-an-azure-service-principal"></a>Создание субъекта-службы Azure

При использовании PowerShell и Terraform вам нужно выполнить вход с помощью субъекта-службы.

Чтобы войти в подписку Azure с помощью субъекта-службы, сначала необходимо получить доступ к этому субъекту-службе. Если у вас уже есть субъект-служба, можете пропустить этот раздел.

Есть множество способов [создать субъект-службу с помощью PowerShell](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps). В рамках этой статьи мы создадим субъект-службу с ролью **участника**. Роль **Участник** (роль по умолчанию) предоставляет полные разрешения на чтение и запись в учетной записи Azure. Дополнительные сведения об управлении доступом на основе ролей см. в статье [Встроенные роли для управления доступом на основе ролей в Azure](/azure/active-directory/role-based-access-built-in-roles).

Вызов [New-AzADServicePrincipal](https://docs.microsoft.com/powershell/module/Az.Resources/New-AzADServicePrincipal) позволяет создать субъект-службу для указанной подписки. После успешного завершения отображаются сведения о созданном субъекте-службе, например имена и отображаемое имя субъекта-службы. При вызове `New-AzADServicePrincipal` без указания учетных данных для проверки подлинности пароль создается автоматически. Но этот пароль не отображается, так как он возвращается в типе `SecureString`. Поэтому вам нужно при вызове `New-AzADServicePrincipal` направить результаты в переменную. Затем вы сможете преобразовать переменную в обычный текст для ее отображения.

1. Получите идентификатор подписки Azure, которую вы хотите использовать. Если вы не знаете идентификатор подписки, это значение можно получить на [портале Azure](https://portal.azure.com/).

    1. Войдите на [портал Azure](https://portal.azure.com/).
    1. В разделе **Службы Azure** выберите **Подписки**.
    1. Таблица подписок содержит столбец с идентификаторами всех подписок.

1. Запустите PowerShell.

1. Создайте новый субъект-службу с помощью командлета [New-AzADServicePrincipal](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal). Замените `<azure_subscription_id>` идентификатором подписки Azure, которую вы хотите использовать.

    ```powershell
    $sp = New-AzADServicePrincipal -Scope /subscriptions/<azure_subscription_id>
    ```

1. Отобразите имена субъектов-служб.

    ```powershell
    $sp.ServicePrincipalNames
    ```

1. Отобразите автоматически созданный пароль в виде текста с помощью командлета [ConvertFrom-SecureString](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/convertfrom-securestring).

    ```powershell
    $UnsecureSecret = ConvertFrom-SecureString -SecureString $sp.Secret -AsPlainText
    ```

**Примечания**

- Имена субъектов-служб и пароли нужны для входа в подписку с вашим субъектом-службой.
- Пароль невозможно извлечь, если он утерян. Следовательно, пароль нужно хранить в надежном месте. Если вы забыли пароль, потребуется [сбросить учетные данные субъекта-службы](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps#reset-credentials).

## <a name="log-in-to-azure-using-a-service-principal"></a>Вход в Azure с помощью субъекта-службы

Чтобы войти в подписку Azure с помощью субъекта-службы, вызовите [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/Connect-AzAccount) и укажите объект с типом [PsCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential).

1. Запустите PowerShell.

1. Получите объект [PsCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential) с помощью одного из следующих способов.

    1. Вызовите [Get-Credential](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-credential) и введите имя субъекта-службы и пароль по запросу.

        ```powershell
        $psCredential = Get-Credential
        ```

    1. Создайте объект `PsCredential` в памяти. Замените заполнители соответствующими значениями для субъекта-службы. Этот шаблон позволяет выполнить вход из скрипта.

        ```powershell
        $spName = "<service_principle_name>"
        $spPassword = ConvertTo-SecureString "<service_principle_password>" -AsPlainText -Force
        $spCredential = New-Object System.Management.Automation.PSCredential($spName , $spPassword)
        ```

1. Вызовите `Connect-AzAccount`, передав объект `PsCredential` (замените заполнитель `<azure_subscription_tenant_id>` значением идентификатора арендатора подписки Azure).

    ```powershell
    Connect-AzAccount -Credential $spCredential -Tenant "<azure_subscription_tenant_id>" -ServicePrincipal
    ```

## <a name="create-a-terraform-configuration-file"></a>Создание файла конфигурации Terraform

Выполнив инструкции из этого раздела, вы создадите файл конфигурации Terraform, который позволяет создать группу ресурсов Azure.

1. Создайте каталог для хранения файлов Terraform для этой демонстрации.

    ```powershell
    mkdir QuickstartTerraformTest
    ```

1. Перейдите в каталог демонстрации.

    ```powershell
    cd QuickstartTerraformTest
    ```

1. С помощью своего редактора создайте файл конфигурации Terraform. В этой статье используется [Visual Studio Code](https://code.visualstudio.com/Download).

    ```powershell
    code QuickstartTerraformTest.tf
    ```

1. Вставьте следующий код HCL в новый файл. Дополнительные сведения см. в примечаниях после списка кода.

    ```hcl
    provider "azurerm" {
      # The "feature" block is required for AzureRM provider 2.x.
      # If you're using version 1.x, the "features" block isn't allowed.
      version = "~>2.0"
      features {}
    }

    resource "azurerm_resource_group" "rg" {
      name     = "QuickstartTerraformTest-rg"
      location = "eastus"
    }
    ```

    **Примечания**
    - Блок поставщика определяет использование [поставщика Azure (azurerm)](https://www.terraform.io/docs/providers/azurerm/index.html).
    - В блоке поставщика `azurerm` заданы атрибуты `version` и `features`. Как указано в комментариях, их использование зависит от конкретной версии. Дополнительные сведения о настройке этих атрибутов см. в статье [Поставщик AzureRM версии 2.0](https://www.terraform.io/docs/providers/azurerm/guides/2.0-upgrade-guide.html).
    - Только [объявление ресурса](https://www.terraform.io/docs/configuration/resources.html) предназначено для типа ресурса [azurerm_resource_group](https://www.terraform.io/docs/providers/azurerm/r/resource_group.html). Для azure_resource_group нужно передать два обязательных параметра: имя и расположение.

## <a name="set-environment-variables"></a>Настройка переменных среды

Чтобы разрешить Terraform использовать нужную подписку Azure, задайте переменные среды. Это можно сделать на уровне системы Windows или в определенном сеансе PowerShell. Если вы хотите задать переменные среды для определенного сеанса, используйте следующий код. Замените заполнители соответствующими значениями для вашей среды.

```powershell
$env:ARM_CLIENT_ID=<service_principle_app_id>
$env:ARM_SUBSCRIPTION_ID=<azure_subscription_id>
$env:ARM_TENANT_ID=<azure_subscription_tenant_id>
```

## <a name="create-and-apply-a-terraform-execution-plan"></a>Создание и применение плана выполнения Terraform

В этом разделе описывается, как создать *план выполнения* и применить его к облачной инфраструктуре.

1. Инициализируйте развертывание Terraform с помощью команды [terraform init](https://www.terraform.io/docs/commands/init.html). На этом шаге загружаются модули Azure, необходимые для создания группы ресурсов Azure.

    ```powershell
    terraform init
    ```

1. Выполните команду [terraform plan](https://www.terraform.io/docs/commands/plan.html), чтобы создать план выполнения из файла конфигурации Terraform.

    ```powershell
    terraform plan -out QuickstartTerraformTest.tfplan
    ```

    **Примечания.**
    - Команда `terraform plan` создает план выполнения, но не выполняет его. Вместо этого она определяет, какие действия необходимы для создания конфигурации, заданной в файлах конфигурации. Этот шаблон позволяет проверить, соответствует ли план выполнения вашим ожиданиям, прежде чем вы начнете вносить изменения в фактические ресурсы.
    - Необязательный параметр `-out` позволяет указать выходной файл для плана. Использование параметра `-out` гарантирует, что проверяемый план полностью соответствует применяемому.
    - Дополнительные сведения о сохранении планов выполнения и обеспечении безопасности см. в разделе о [предупреждениях безопасности](https://www.terraform.io/docs/commands/plan.html#security-warning).

1. Выполните команду [terraform apply](https://www.terraform.io/docs/commands/apply.html), чтобы применить план выполнения.

    ```powershell
    terraform apply QuickstartTerraformTest.tfplan
    ```

1. После применения плана выполнения вы можете проверить, создана ли группа ресурсов, выполнив командлет [Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/Get-AzResourceGroup).

    ```powershell
    Get-AzResourceGroup -Name QuickstartTerraformTest-rg
    ```

    В случае успеха команда отображает различные свойства только что созданной группы ресурсов.

## <a name="clean-up-resources"></a>Очистка ресурсов

Удалите ресурсы Azure, созданные в рамках этой статьи, если они вам больше не нужны.

1. Выполните команду [terraform plan](https://www.terraform.io/docs/commands/plan.html), чтобы создать план выполнения для удаления ресурсов, указанных в файле конфигурации Terraform.

    ```powershell
    terraform plan -destroy -out QuickstartTerraformTest.destroy.tfplan
    ```

    **Примечания.**
    - Команда `terraform plan` создает план выполнения, но не выполняет его. Вместо этого она определяет, какие действия необходимы для создания конфигурации, заданной в файлах конфигурации. Этот шаблон позволяет проверить, соответствует ли план выполнения вашим ожиданиям, прежде чем вы начнете вносить изменения в фактические ресурсы.
    - Параметр `-destroy` создает план для удаления ресурсов.
    - Необязательный параметр `-out` позволяет указать выходной файл для плана. Использование параметра `-out` гарантирует, что проверяемый план полностью соответствует применяемому.
    - Дополнительные сведения о сохранении планов выполнения и обеспечении безопасности см. в разделе о [предупреждениях безопасности](https://www.terraform.io/docs/commands/plan.html#security-warning).

1. Выполните команду [terraform apply](https://www.terraform.io/docs/commands/apply.html), чтобы применить план выполнения.

    ```powershell
    terraform apply QuickstartTerraformTest.destroy.tfplan
    ```

1. Убедитесь, что группа ресурсов была удалена, выполнив командлет [Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/Get-AzResourceGroup).

    ```powershell
    Get-AzResourceGroup -Name QuickstartTerraformTest-rg
    ```

    **Примечания**
    - При успешном удалении `Get-AzResourceGroup` отобразит сведения о том, что группа ресурсов не существует.

1. Перейдите в родительский каталог и удалите демонстрационный каталог. Параметр `-r` удаляет содержимое каталога перед удалением самого каталога. Среди содержимого каталога: файл конфигурации, созданный ранее, и файлы состояния Terraform.

    ```powershell
    cd .. && rm -r QuickstartTerraformTest
    ```

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Создание готовой инфраструктуры виртуальных машин Linux в Azure с помощью Terraform](create-linux-virtual-machine-with-infrastructure.md)