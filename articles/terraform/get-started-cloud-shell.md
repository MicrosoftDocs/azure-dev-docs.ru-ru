---
title: Краткое руководство. Начало работы с Terraform с использованием Azure Cloud Shell
description: Из этого краткого руководства можно узнать, как установить и настроить Terraform для создания ресурсов Azure.
keywords: azure devops terraform установка настройка cloud shell init план применение выполнение вход портал rbac субъект-служба автоматизированный сценарий
ms.topic: quickstart
ms.date: 08/08/2020
ms.openlocfilehash: 736c805b8dd8c95d1950537b754059cca9fc5712
ms.sourcegitcommit: 6a8485d659d6239569c4e3ecee12f924c437b235
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/09/2020
ms.locfileid: "88026145"
---
# <a name="quickstart-get-started-with-terraform-using-azure-cloud-shell"></a>Краткое руководство. Начало работы с Terraform с использованием Azure Cloud Shell
 
[!INCLUDE [terraform-intro.md](includes/terraform-intro.md)]

В этой статье описывается, как начать работу с [Terraform в Azure](https://www.terraform.io/docs/providers/azurerm/index.html).

Вы узнаете, как выполнять следующие задачи:
> [!div class="checklist"]
> * аутентификация в Azure с помощью команды `az login`;
> * создание субъекта-службы Azure с помощью Azure CLI;
> * аутентификация в Azure с помощью субъекта-службы;
> * настройка текущей подписки Azure для использования, если у вас несколько подписок;
> * написание скрипта Terraform для создания группы ресурсов Azure;
> * Создание и применение плана выполнения Terraform
> * использование флага `terraform plan -destroy` для отмены плана выполнения.

[!INCLUDE [hashicorp-support.md](includes/hashicorp-support.md)]

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]

## <a name="configure-your-environment"></a>Настройка среды

1. Перейдите на [портал Azure](https://portal.azure.com).

1. Если вы еще не вошли в систему, на портале Azure отображается список доступных учетных записей Майкрософт. Выберите учетную запись Майкрософт, связанную с одной или несколькими активными подписками Azure, и введите учетные данные, чтобы продолжить.

1. Откройте Cloud Shell.

    ![Доступ к Cloud Shell](media/install-configure/portal-cloud-shell.png)

1. Если вы ранее не использовали Cloud Shell, настройте параметры среды и хранилища. В этой статье используется среда Bash.

**Примечания**
- В Cloud Shell автоматически установлена последняя версия Terraform. Кроме того, Terraform автоматически использует сведения из текущей подписки Azure. В результате установка или конфигурация не требуется.

## <a name="authenticate-to-azure"></a>Проверка подлинности в Azure

Cloud Shell автоматически проходит аутентификацию под учетной записью Майкрософт, с помощью которой вы выполнили вход на портале Azure. Если в вашей учетной записи есть несколько подписок Azure, вы можете [перейти на другую подписку](#set-the-current-azure-subscription).

Terraform поддерживает несколько вариантов аутентификации в Azure. В этой статье описаны следующие техники:

- При использовании Terraform в интерактивном режиме рекомендуется [аутентификация через учетную запись Майкрософт](#authenticate-via-microsoft-account).
- При использовании Terraform из кода рекомендуется [аутентификация через субъект-службу Azure](#authenticate-via-azure-service-principal).

### <a name="authenticate-via-microsoft-account"></a>Аутентификация через учетную запись Майкрософт

При вызове `az login` без параметров отображается URL-адрес и код. Перейдите по URL-адресу, введите код и следуйте инструкциям по входу в Azure с помощью учетной записи Майкрософт. После входа вернитесь на портал.

```azurecli
az login
```

**Примечания**

- После успешного входа `az login` отобразит список подписок Azure, связанных с учетной записью Майкрософт, от имени которой был выполнен вход.
- Для каждой доступной подписки Azure отображается список свойств. Свойство `isDefault` определяет, какую подписку Azure вы используете. Чтобы узнать, как переключиться на другую подписку Azure, см. раздел [Настройка текущей подписки Azure](#set-the-current-azure-subscription).

### <a name="authenticate-via-azure-service-principal"></a>Аутентификация через субъект-службу Azure

**Создайте субъект-службу Azure.** Чтобы войти в подписку Azure с помощью субъекта-службы, сначала необходимо получить доступ к этому субъекту-службе. Если у вас уже есть субъект-служба, можете пропустить эту часть раздела.

Разрешения для автоматизированных средств, которые развертывают или используют службы Azure (такие как Terraform), всегда должны быть ограничены. В качестве замены входу в приложения с использованием учетной записи пользователя с полными правами Azure предлагает субъекты-службы. Но что делать, если у вас нет субъекта-службы, с которым будет выполняться вход? В этом сценарии можно войти с использованием учетных данных пользователя, а затем создать субъект-службу. После создания субъекта-службы можно использовать его данные для будущих попыток входа.

Существует множество способов [создать субъект-службу с помощью Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli?). В этой статье мы с помощью команды [az ad sp create-for-rbac](/cli/azure/ad/sp?#az-ad-sp-create-for-rbac) создадим субъект-службу с ролью **участника**. Роль **Участник** (по умолчанию) предоставляет полные разрешения на чтение и запись в учетной записи Azure. Дополнительные сведения об управлении доступом на основе ролей см. в статье [Встроенные роли для управления доступом на основе ролей в Azure](/azure/active-directory/role-based-access-built-in-roles).

Введите следующую команду, заменив `<subscription_id>` значением идентификатора учетной записи подписки, которую вы хотите использовать.

```azurecli
az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/<subscription_id>"
```

**Примечания**

- После успешного выполнения `az ad sp create-for-rbac` отображает несколько значений. Значения `name`, `password` и `tenant` используются на следующем шаге.
- Пароль невозможно извлечь, если он утерян. Следовательно, пароль нужно хранить в надежном месте. Если вы забыли пароль, потребуется [сбросить учетные данные субъекта-службы](/cli/azure/create-an-azure-service-principal-azure-cli#reset-credentials).

**Вход с помощью субъекта-службы Azure**: В следующем вызове `az login` замените заполнители реальными данными для субъекта-службы.

```azurecli
az login --service-principal -u <service_principal_name> -p "<service_principal_password>" --tenant "<service_principal_tenant>"
```

## <a name="set-the-current-azure-subscription"></a>Настройка текущей подписки Azure

Учетную запись Майкрософт можно связать с несколькими подписками Azure. Ниже описано, как переключаться между подписками:

1. Чтобы узнать текущую подписку Azure, используйте [az account show](/cli/azure/account#az-account-show).

    ```azurecli
    az account show
    ```

1. Если у вас есть доступ к нескольким подпискам Azure, используйте команду [az account list](/cli/azure/account#az-account-list) для вывода списка значений идентификатора имени подписки:

    ```azurecli
    az account list --query "[].{name:name, subscriptionId:id}"
    ```

1. Чтобы использовать определенную подписку Azure для текущего сеанса Cloud Shell, выполните [az account set](/cli/azure/account#az-account-set). Замените заполнитель `<subscription_id>` идентификатором (или именем) подписки, которую вы хотите использовать.

    ```azurecli
    az account set --subscription="<subscription_id>"
    ```

    **Примечания**

    - При вызове `az account set` не отобразятся результаты переключения на указанную подписку Azure. Однако с помощью `az account show` можно убедиться, что текущая подписка Azure изменилась.

## <a name="create-a-terraform-configuration-file"></a>Создание файла конфигурации Terraform

Из этого раздела вы узнаете, как создать файл конфигурации Terraform, который позволяет создать группу ресурсов Azure.

1. Измените каталоги на подключенную общую папку, где сохраняется ваша работа в Cloud Shell. Дополнительные сведения о том, как Cloud Shell сохраняет файлы, см. в разделе [Подключение хранилища файлов Microsoft Azure](/azure/cloud-shell/overview#connect-your-microsoft-azure-files-storage).

    ```bash
    cd clouddrive
    ```

1. Создайте каталог для хранения файлов Terraform для этой демонстрации.

    ```bash
    mkdir QuickstartTerraformTest
    ```

1. Перейдите в каталог демонстрации.

    ```bash
    cd QuickstartTerraformTest
    ```

1. С помощью своего редактора создайте файл конфигурации Terraform. Для инструкций из этой статьи используется встроенный [редактор Cloud Shell](/azure/cloud-shell/using-cloud-shell-editor).

    ```bash
    code QuickstartTerraformTest.tf
    ```
 
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

    **Примечания**

    - Блок `provider` указывает, что используется [поставщик Azure (`azurerm`)](https://www.terraform.io/docs/providers/azurerm/index.html).
    - В блоке поставщика `azurerm` заданы атрибуты `version` и `features`. Как указано в комментариях, их использование зависит от конкретной версии. Дополнительные сведения о настройке этих атрибутов для среды см. в разделе [Поставщик AzureRM версии 2.0](https://www.terraform.io/docs/providers/azurerm/guides/2.0-upgrade-guide.html).
    - Только [объявление ресурса](https://www.terraform.io/docs/configuration/resources.html) предназначено для типа ресурса [azurerm_resource_group](https://www.terraform.io/docs/providers/azurerm/r/resource_group.html). Два обязательных аргумента для `azure_resource_group`: `name` и `location`.

1. Сохраните файл ( **&lt;CTRL+S**).

1. Выйдите из редактора ( **&lt;CTRL+Q**).

## <a name="create-and-apply-a-terraform-execution-plan"></a>Создание и применение плана выполнения Terraform

В этом разделе описывается, как создать *план выполнения* и применить его к облачной инфраструктуре.

1. Инициализируйте развертывание Terraform с помощью команды [terraform init](https://www.terraform.io/docs/commands/init.html). На этом шаге загружаются модули Azure, необходимые для создания группы ресурсов Azure.

    ```bash
    terraform init
    ```

1. Выполните команду [terraform plan](https://www.terraform.io/docs/commands/plan.html), чтобы создать план выполнения из файла конфигурации Terraform.

    ```bash
    terraform plan -out QuickstartTerraformTest.tfplan
    ```

    **Примечания.**
    - Команда `terraform plan` создает план выполнения, но не выполняет его. Вместо этого она определяет, какие действия необходимы для создания конфигурации, заданной в файлах конфигурации. Этот шаблон позволяет проверить, соответствует ли план выполнения вашим ожиданиям, прежде чем вы начнете вносить изменения в фактические ресурсы.
    - Необязательный параметр `-out` позволяет указать выходной файл для плана. Использование параметра `-out` гарантирует, что проверяемый план полностью соответствует применяемому.
    - Дополнительные сведения о сохранении планов выполнения и обеспечении безопасности см. в разделе о [предупреждениях безопасности](https://www.terraform.io/docs/commands/plan.html#security-warning).

1. Выполните команду [terraform apply](https://www.terraform.io/docs/commands/apply.html), чтобы применить план выполнения.

    ```bash
    terraform apply QuickstartTerraformTest.tfplan
    ```

1. После применения плана выполнения вы можете проверить, создана ли группа ресурсов, выполнив команду [az group show](/cli/azure/group?#az-group-show).

    ```azurecli
    az group show -n "QuickstartTerraformTest-rg"
    ```

    **Примечания**

    - В случае успеха `az group show` отображает различные свойства только что созданной группы ресурсов.

## <a name="clean-up-resources"></a>Очистка ресурсов

Удалите ресурсы Azure, созданные в рамках этой статьи, если они вам больше не нужны.

1. Выполните команду [terraform plan](https://www.terraform.io/docs/commands/plan.html), чтобы создать план выполнения для удаления ресурсов, указанных в файле конфигурации Terraform.

    ```bash
    terraform plan -destroy -out QuickstartTerraformTest.destroy.tfplan
    ```

    **Примечания**
    - Команда `terraform plan` создает план выполнения, но не выполняет его. Вместо этого она определяет, какие действия необходимы для создания конфигурации, заданной в файлах конфигурации. Этот шаблон позволяет проверить, соответствует ли план выполнения вашим ожиданиям, прежде чем вы начнете вносить изменения в фактические ресурсы.
    - Параметр `-destroy` создает план для удаления ресурсов.
    - Необязательный параметр `-out` позволяет указать выходной файл для плана. Использование параметра `-out` гарантирует, что проверяемый план полностью соответствует применяемому.
    - Дополнительные сведения о сохранении планов выполнения и обеспечении безопасности см. в разделе о [предупреждениях безопасности](https://www.terraform.io/docs/commands/plan.html#security-warning).

1. Выполните команду [terraform apply](https://www.terraform.io/docs/commands/apply.html), чтобы применить план выполнения.

    ```bash
    terraform apply QuickstartTerraformTest.destroy.tfplan
    ```

1. Убедитесь, что группа ресурсов была удалена, с помощью команды [az group show](/cli/azure/group?#az-group-show).

    ```azurecli
    az group show -n "QuickstartTerraformTest-rg"
    ```

    **Примечания**
    - При успешном удалении `az group show` отобразит сведения о том, что группа ресурсов не существует.

1. Перейдите в родительский каталог и удалите демонстрационный каталог. Параметр `-r` удаляет содержимое каталога перед удалением самого каталога. Среди содержимого каталога: файл конфигурации, созданный ранее, и файлы состояния Terraform.

    ```bash
    cd .. && rm -r QuickstartTerraformTest
    ```

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Создание готовой инфраструктуры виртуальных машин Linux в Azure с помощью Terraform](create-linux-virtual-machine-with-infrastructure.md)