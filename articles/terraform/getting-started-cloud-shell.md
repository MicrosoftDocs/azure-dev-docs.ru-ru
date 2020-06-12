---
title: Краткое руководство. Начало работы с Terraform — Azure Cloud Shell
description: Из этого краткого руководства можно узнать, как установить и настроить Terraform для создания ресурсов Azure.
keywords: azure devops terraform установка настройка cloud shell init план применение выполнение вход портал rbac субъект-служба автоматизированный сценарий
ms.topic: quickstart
ms.date: 06/01/2020
ms.openlocfilehash: 184d2720e3e2259a6c909d0775ffee20c0f30419
ms.sourcegitcommit: db56786f046a3bde1bd9b0169b4f62f0c1970899
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/03/2020
ms.locfileid: "84329912"
---
# <a name="quickstart-getting-started-with-terraform---azure-cloud-shell"></a>Краткое руководство. Начало работы с Terraform — Azure Cloud Shell
 
[!INCLUDE [terraform-intro.md](includes/terraform-intro.md)]

В этой статье описывается, как начать работу с Terraform из среды [Azure Cloud Shell](/azure/cloud-shell/overview).

[!INCLUDE [hashicorp-support.md](includes/hashicorp-support.md)]

## <a name="configure-your-environment"></a>Настройка среды

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]

## <a name="open-cloud-shell"></a>Открытие Cloud Shell

1. Перейдите на [портал Azure](https://portal.azure.com).

1. Если вы еще не вошли в систему, на портале Azure отображается список доступных учетных записей Майкрософт. Выберите учетную запись Майкрософт, связанную с одной или несколькими активными подписками Azure, и введите учетные данные, чтобы продолжить.

1. Откройте Cloud Shell.

    ![Доступ к Cloud Shell](media/install-configure/portal-cloud-shell.png)

1. Если вы ранее не использовали Cloud Shell, настройте параметры среды и хранилища. В этой статье используется среда Bash.

## <a name="log-into-your-microsoft-account"></a>Вход в учетную запись Майкрософт

Cloud Shell автоматически проходит проверку подлинности под учетной записью Майкрософт, с помощью которой вы выполнили вход на портале Azure. Однако если у вас есть несколько учетных записей Майкрософт с подписками Azure, можно войти в одну из этих учетных записей с помощью команды [az login](/cli/azure/reference-index?view=azure-cli-latest#az-login). Ниже приведены два примера использования команды `az login`.

Выберите один из следующих способов, соответствующий вашему сценарию.
    
- **Вы хотите войти в систему как пользователь**: При выполнении команды `az login` без параметров отображается URL-адрес и код. Перейдите по URL-адресу, введите код и следуйте инструкциям по входу в Azure с помощью учетной записи Майкрософт. Как только вы войдете в систему, вернитесь на портал.

    ```azurecli-interactive
    az login
    ```

    **Примечания**
    - После успешного входа команда `az login` отобразит список подписок Azure, связанных с учетной записью Майкрософт, от имени которой был выполнен вход в систему.
    - Для каждой доступной подписки Azure отображается список свойств. Свойство `isDefault` определяет, какую подписку Azure вы используете. Чтобы узнать, как переключиться на другую подписку Azure, см. раздел [Определение текущей подписки Azure](#specify-the-current-azure-subscription).

- **Вы хотите использовать субъект-службу, но его у вас еще нет**: Разрешения для автоматизированных средств, которые развертывают или используют службы Azure (такие как Terraform), всегда должны быть ограничены. В качестве замены входу в приложения с использованием учетной записи пользователя с полными правами Azure предлагает субъекты-службы. Но что делать, если у вас нет субъекта-службы, с которым будет выполняться вход? В этом сценарии можно войти с использованием учетных данных пользователя, а затем создать субъект-службу. После создания субъекта-службы можно использовать его данные для будущих попыток входа.

    Существует множество вариантов [создания субъекта-службы](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest). В этой статье мы создадим субъект-службу с ролью **участник** (роль по умолчанию). Роль **участник** имеет все разрешения на чтение из учетной записи Azure и запись в нее. Дополнительные сведения об управлении доступом на основе ролей см. в статье [Встроенные роли для управления доступом на основе ролей в Azure](/azure/active-directory/role-based-access-built-in-roles). 
    
    С помощью команды [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) замените `<subscription_id>` идентификатором учетной записи подписки, которую вы хотите использовать.
    
    ```azurecli-interactive
    az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/<subscription_id>"
    ```

    **Примечания**
    - После успешного завершения команда `az ad sp create-for-rbac` выводит несколько значений, включая автоматически сформированный пароль. Пароль невозможно извлечь, если он утерян. Следовательно, пароль нужно хранить в надежном месте. Если вы забыли пароль, потребуется [сбросить учетные данные субъекта-службы](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest#reset-credentials).
    
- **Вход с помощью субъекта-службы Azure**: Замените заполнители в команде `az login` сведениями из субъекта-службы.

    ```azurecli-interactive
    az login --service-principal -u <service_principal_name> -p "<service_principal_password>" --tenant "<service_principal_tenant>"
    ```

    **Примечания**
    - После успешного входа команда `az login` отображает различные свойства подписки Azure, такие как `id` и `name`.

## <a name="specify-the-current-azure-subscription"></a>Определение текущей подписки Azure

Как уже объяснялось в предыдущем разделе, в следующих сценариях описываются два способа входа в Azure.

- **Вход с использованием учетной записи Майкрософт.** Учетную запись Майкрософт можно связать с несколькими подписками Azure, одна из которых является подпиской по умолчанию. Подписка по умолчанию используется, если вы входите в систему и не переключаетесь на другую подписку.
- **Вход с помощью субъекта-службы Azure**: Субъект-служба относится к подписке Azure. Помните, что при входе в систему отображаются сведения о подписке.

Перечисленные ниже шаги реализуют первый сценарий, где выполняются следующие задачи.

- Проверка текущей подписки Azure.
- Составление списка всех доступных подписок Azure для текущей учетной записи Майкрософт.
- Переключение на другую подписку Azure.

1. Чтобы проверить текущую подписку Azure, используйте команду [az account show](/cli/azure/account#az-account-show).

    ```azurecli-interactive
    az account show
    ```
    
1. Если у вас есть доступ к нескольким подпискам Azure, используйте команду [az account list](/cli/azure/account#az-account-list) для вывода списка значений идентификатора имени подписки:

    ```azurecli-interactive
    az account list --query "[].{name:name, subscriptionId:id}"
    ```

1. Чтобы использовать определенную подписку Azure для текущего сеанса Cloud Shell, используйте команду [az account set](/cli/azure/account#az-account-set). Замените заполнитель `<subscription_id>` идентификатором (или именем) подписки, которую вы хотите использовать.

    ```azurecli-interactive
    az account set --subscription="<subscription_id>"
    ```

    **Примечания**
    - Команда `az account set` не отображает результаты переключения на указанную подписку Azure. Однако с помощью команды `az account show` можно убедиться, что текущая подписка Azure изменилась.

## <a name="create-a-terraform-configuration-file"></a>Создание файла конфигурации Terraform

В этом разделе описано, как использовать [редактор Code Shell](/azure/cloud-shell/using-cloud-shell-editor) для определения файла конфигурации Terraform.

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

1. С помощью своего редактора создайте файл конфигурации Terraform. В этой статье используется встроенный редактор Cloud Shell.

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

В Cloud Shell автоматически установлена последняя версия Terraform. Кроме того, Terraform автоматически использует сведения из текущей подписки Azure. В результате установка или конфигурация не требуется. После создания файлов конфигурации необходимо выполнить несколько команд Terraform, чтобы создать план выполнения. После создания плана выполнения его можно проверить и развернуть.

1. Инициализируйте развертывание Terraform с помощью команды [terraform init](https://www.terraform.io/docs/commands/init.html). На этом шаге загружаются модули Azure, необходимые для создания группы ресурсов Azure.

    ```bash
    terraform init
    ```
    
1. Terraform позволяет выполнять предварительный просмотр действий с помощью команды [terraform plan](https://www.terraform.io/docs/commands/plan.html).

    ```bash
    terraform plan
    ```

    **Примечания.**
    - Команда `terraform plan` создает план выполнения, но не выполняет его. Вместо этого она определяет, какие действия необходимы для создания конфигурации, заданной в файлах конфигурации.
    - Команда `terraform plan` позволяет проверить, соответствует ли план выполнения вашим ожиданиям, прежде чем вносить изменения в фактические ресурсы.
    - Необязательный параметр `-out` позволяет указать выходной файл для плана. Дополнительные сведения об использовании параметра `-out` см. в разделе [Сохранение планов выполнения для последующего развертывания](#persist-an-execution-plan-for-later-deployment).

1. Примените план выполнения с помощью команды [terraform apply](https://www.terraform.io/docs/commands/apply.html).

    ```bash
    terraform apply
    ```
    
1. Terraform показывает, что произойдет, если применить план выполнения, и требует подтвердить его выполнение. Подтвердите команду, введя `yes` и нажав клавишу **ВВОД**.

1. После подтверждения выполнения плана убедитесь, что группа ресурсов была успешно создана с помощью команды [az group show](/cli/azure/group?view=azure-cli-latest#az-group-show).

    ```azurecli-interactive
    az group show -n "QuickstartTerraformTest-rg"
    ```

    В случае успеха команда отображает различные свойства только что созданной группы ресурсов.

## <a name="persist-an-execution-plan-for-later-deployment"></a>Сохранение плана выполнения для последующего развертывания

В предыдущем разделе вы узнали, как выполнить команду [terraform plan](https://www.terraform.io/docs/commands/plan.html) для создания плана выполнения. Затем вы увидели, что команда [terraform apply](https://www.terraform.io/docs/commands/apply.html) применяет этот план. Этот шаблон хорошо работает, когда шаги являются интерактивными и последовательными.

Для более сложных сценариев можно сохранить план выполнения в файл. Позднее или даже с другого компьютера можно применить этот план выполнения.

При использовании этой функции рекомендуется прочитать статью [Автоматический запуск Terraform](https://learn.hashicorp.com/terraform/development/running-terraform-in-automation).

Следующие шаги иллюстрируют базовый шаблон использования этой функции:

1. Запустите [terraform init](https://www.terraform.io/docs/commands/init.html).

    ```bash
    terraform init
    ```

1. Запустите `terraform plan` с параметром `-out`.

    ```bash
    terraform plan -out QuickstartTerraformTest.tfplan
    ```

1. Запустите `terraform apply`, указав имя файла из предыдущего шага.

    ```bash
    terraform apply QuickstartTerraformTest.tfplan
    ```

**Примечания**
- Чтобы включить использование с автоматизацией, запуск `terraform apply <filename>` не требует подтверждения.
- Если вы решили использовать эту функцию, ознакомьтесь с разделом [Предупреждение системы безопасности](https://www.terraform.io/docs/commands/plan.html#security-warning).

## <a name="clean-up-resources"></a>Очистка ресурсов

Удалите ресурсы Azure, созданные в рамках этой статьи, если они вам больше не нужны.

1. Выполните команду [terraform destroy](https://www.terraform.io/docs/commands/destroy.html), которая отменит текущий план выполнения.

    ```bash
    terraform destroy
    ```

1. Terraform показывает, что произойдет при отмене плана выполнения, и требует подтверждения. Подтвердите команду, введя `yes` и нажав клавишу **ВВОД**.

1. После подтверждения выполнения плана выходные данные будут выглядеть так, как показано в следующем примере. Убедитесь, что группа ресурсов была удалена с помощью команды [az group show](/cli/azure/group?view=azure-cli-latest#az-group-show).

    ```azurecli-interactive
    az group show -n "QuickstartTerraformTest-rg"
    ```

    **Примечания**
    - В случае успеха команда `az group show` подтвердит, что группа ресурсов не существует.

1. Перейдите в родительский каталог и удалите демонстрационный каталог. Параметр `-r` удаляет содержимое каталога перед удалением самого каталога. Среди содержимого каталога: файл конфигурации, созданный ранее, и файлы состояния Terraform.

    ```bash
    cd .. && rm -r QuickstartTerraformTest
    ```

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Создание готовой инфраструктуры виртуальных машин Linux в Azure с помощью Terraform](create-linux-virtual-machine-with-infrastructure.md)