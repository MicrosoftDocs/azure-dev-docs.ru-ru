---
title: Краткое руководство. Настройка Terraform с помощью Azure PowerShell
description: В этом кратком руководстве показано, как установить и настроить Terraform с помощью Azure PowerShell.
keywords: azure devops terraform установка настройка windows init план применение выполнение вход портал rbac субъект-служба автоматизированный сценарий powershell
ms.topic: quickstart
ms.date: 09/27/2020
ms.custom: devx-track-terraform
ms.openlocfilehash: 8f95d0bb09d7e9e7ea789b90a27178cdf5426d74
ms.sourcegitcommit: e20f6c150bfb0f76cd99c269fcef1dc5ee1ab647
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/28/2020
ms.locfileid: "91401566"
---
# <a name="quickstart-configure-terraform-using-azure-powershell"></a>Краткое руководство. Настройка Terraform с помощью Azure PowerShell
 
[!INCLUDE [terraform-intro.md](includes/terraform-intro.md)]

В этой статье описывается, как начать работу с [Terraform в Azure](https://www.terraform.io/docs/providers/azurerm/index.html) с использованием PowerShell.

Вы узнаете, как выполнять следующие задачи:
> [!div class="checklist"]
> * установка последней версии PowerShell;
> * установка нового модуля Az PowerShell;
> * Установка Azure CLI
> * Установка Terraform
> * создание субъекта-службы Azure для аутентфикации;
> * вход в Azure с помощью субъекта-службы; 
> * настройка переменных среды для корректной аутентификации с помощью Terraform в подписке Azure;
> * создание базового файла конфигурации Terraform;
> * Создание и применение плана выполнения Terraform
> * отмена плана выполнения.

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]

## <a name="configure-your-environment"></a>Настройка среды

1. Последний модуль PowerShell, который позволяет взаимодействовать с ресурсами Azure, называется [модулем Az для Azure PowerShell](/powershell/azure/new-azureps-module-az). При использовании модуля Az для Azure PowerShell для всех платформ рекомендуется наличие версии PowerShell 7 (или более поздней). Если среда PowerShell уже установлена, версию можно проверить с помощью следующей команды в командной строке PowerShell.

    ```powershell
    $PSVersionTable.PSVersion
    ```

1. [Установите PowerShell.](/powershell/scripting/install/installing-powershell-core-on-windows) Эта демонстрация была протестирована с использованием PowerShell 7.0.2 в Windows 10.

1. Чтобы разрешить [Terraform выполнять аутентификацию в Azure](https://www.terraform.io/docs/providers/azurerm/guides/azure_cli.html), вам нужно [установить Azure CLI](/cli/azure/install-azure-cli-windows). Эта демонстрация была протестирована с использованием Azure CLI версии 2.9.1.

1. [Скачайте Terraform](https://www.terraform.io/downloads.html).

1. Извлеките исполняемый файл из скачанного содержимого в произвольный каталог.

1. [Измените глобальный путь в системе](https://stackoverflow.com/questions/1618280/where-can-i-set-path-to-make-exe-on-windows), чтобы включить в него путь к этому исполняемому файлу.

1. Проверьте конфигурацию глобального пути, выполнив команду `terraform`.

    ```powershell
    terraform
    ```

    **Примечания**
    - Если исполняемый файл Terraform будет найден, команда выведет данные о синтаксисе и список доступных команд.

## <a name="authenticate-to-azure"></a>Проверка подлинности в Azure

При использовании PowerShell и Terraform вам нужно выполнить вход с помощью субъекта-службы. В следующих двух разделах проиллюстрированы следующие задачи:

- [создание субъекта-службы Azure](#create-an-azure-service-principal);
- [Вход в Azure с помощью субъекта-службы](#log-in-to-azure-using-a-service-principal)


### <a name="span-idcreate-an-azure-service-principalcreate-an-azure-service-principal"></a><span id="create-an-azure-service-principal"/>Создание субъекта-службы Azure

Чтобы войти в подписку Azure с помощью субъекта-службы, сначала необходимо получить доступ к этому субъекту-службе. Если у вас уже есть субъект-служба, можете пропустить этот раздел.

Есть множество способов [создать субъект-службу с помощью PowerShell](/powershell/azure/create-azure-service-principal-azureps). В рамках этой статьи мы создадим субъект-службу с ролью **участника**. Роль **Участник** (роль по умолчанию) предоставляет полные разрешения на чтение и запись в учетной записи Azure. Дополнительные сведения об управлении доступом на основе ролей см. в статье [Встроенные роли для управления доступом на основе ролей в Azure](/azure/active-directory/role-based-access-built-in-roles).

Вызов [New-AzADServicePrincipal](/powershell/module/Az.Resources/New-AzADServicePrincipal) позволяет создать субъект-службу для указанной подписки. После успешного завершения отображаются сведения о созданном субъекте-службе, например имена и отображаемое имя субъекта-службы. При вызове `New-AzADServicePrincipal` без указания учетных данных для проверки подлинности пароль создается автоматически. Но этот пароль не отображается, так как он возвращается в типе `SecureString`. Поэтому вам нужно при вызове `New-AzADServicePrincipal` направить результаты в переменную. Затем вы сможете преобразовать переменную в обычный текст для ее отображения.

1. Получите идентификатор подписки Azure, которую вы хотите использовать. Если вы не знаете идентификатор подписки, это значение можно получить на [портале Azure](https://portal.azure.com/).

    1. Войдите на [портал Azure](https://portal.azure.com/).
    1. В разделе **Службы Azure** выберите **Подписки**.
    1. Таблица подписок содержит столбец с идентификаторами всех подписок.

1. Запустите PowerShell.

1. Создайте новый субъект-службу с помощью командлета [New-AzADServicePrincipal](/powershell/module/az.resources/new-azadserviceprincipal). Замените `<azure_subscription_id>` идентификатором подписки Azure, которую вы хотите использовать.

    ```powershell
    $sp = New-AzADServicePrincipal -Scope /subscriptions/<azure_subscription_id>
    ```

1. Отобразите имена субъектов-служб.

    ```powershell
    $sp.ServicePrincipalNames
    ```

1. Отобразите автоматически созданный пароль в виде текста с помощью командлета [ConvertFrom-SecureString](/powershell/module/microsoft.powershell.security/convertfrom-securestring).

    ```powershell
    $UnsecureSecret = ConvertFrom-SecureString -SecureString $sp.Secret -AsPlainText
    ```

**Примечания**

- Имена субъектов-служб и пароли нужны для входа в подписку с вашим субъектом-службой.
- Пароль невозможно извлечь, если он утерян. Следовательно, пароль нужно хранить в надежном месте. Если вы забыли пароль, потребуется [сбросить учетные данные субъекта-службы](/powershell/azure/create-azure-service-principal-azureps#reset-credentials).

### <a name="span-idlog-in-to-azure-using-a-service-principallog-in-to-azure-using-a-service-principal"></a><span id="log-in-to-azure-using-a-service-principal"/>Вход в Azure с помощью субъекта-службы

Чтобы войти в подписку Azure с помощью субъекта-службы, вызовите [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) и укажите объект с типом [PsCredential](/dotnet/api/system.management.automation.pscredential).

1. Запустите PowerShell.

1. Получите объект [PsCredential](/dotnet/api/system.management.automation.pscredential) с помощью одного из следующих способов.

    1. Вызовите [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential) и введите имя субъекта-службы и пароль по запросу.

        ```powershell
        $spCredential = Get-Credential
        ```

    1. Создайте объект `PsCredential` в памяти. Замените заполнители соответствующими значениями для субъекта-службы. Этот шаблон позволяет выполнить вход из скрипта.

        ```powershell
        $spName = "<service_principal_name>"
        $spPassword = ConvertTo-SecureString "<service_principal_password>" -AsPlainText -Force
        $spCredential = New-Object System.Management.Automation.PSCredential($spName , $spPassword)
        ```

1. Вызовите `Connect-AzAccount`, передав объект `PsCredential` (замените заполнитель `<azure_subscription_tenant_id>` значением идентификатора арендатора подписки Azure).

    ```powershell
    Connect-AzAccount -Credential $spCredential -Tenant "<azure_subscription_tenant_id>" -ServicePrincipal
    ```

## <a name="set-environment-variables"></a>Настройка переменных среды

Чтобы разрешить Terraform использовать нужную подписку Azure, задайте переменные среды. Это можно сделать на уровне системы Windows или в определенном сеансе PowerShell. Если вы хотите задать переменные среды для определенного сеанса, используйте следующий код. Замените заполнители соответствующими значениями для вашей среды.

```powershell
$env:ARM_CLIENT_ID="<service_principal_app_id>"
$env:ARM_SUBSCRIPTION_ID="<azure_subscription_id>"
$env:ARM_TENANT_ID="<azure_subscription_tenant_id>"
```

[!INCLUDE [terraform-create-base-config-file.md](includes/terraform-create-base-config-file.md)]

[!INCLUDE [terraform-create-and-apply-execution-plan.md](includes/terraform-create-and-apply-execution-plan.md)]

[!INCLUDE [terraform-reverse-execution-plan.md](includes/terraform-reverse-execution-plan.md)]

[!INCLUDE [terraform-troubleshooting.md](includes/terraform-troubleshooting.md)]

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Создание виртуальной машины Linux с помощью Terraform](create-linux-virtual-machine-with-infrastructure.md)
