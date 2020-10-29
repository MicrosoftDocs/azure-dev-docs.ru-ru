---
title: Краткое руководство. Настройка Terraform с помощью Azure Cloud Shell
description: В этом кратком руководстве показано, как установить и настроить Terraform с помощью Azure Cloud Shell.
keywords: azure devops terraform установка настройка cloud shell init план применение выполнение вход портал rbac субъект-служба автоматизированный сценарий
ms.topic: quickstart
ms.date: 09/27/2020
ms.custom: devx-track-terraform, devx-track-azurecli
ms.openlocfilehash: 70a7c1dc9db76c51d5923fc3b82200eca2976b2c
ms.sourcegitcommit: 1ddcb0f24d2ae3d1f813ec0f4369865a1c6ef322
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/27/2020
ms.locfileid: "92688911"
---
# <a name="quickstart-configure-terraform-using-azure-cloud-shell"></a>Краткое руководство. Настройка Terraform с помощью Azure Cloud Shell
 
[!INCLUDE [terraform-intro.md](includes/terraform-intro.md)]

В этой статье описывается, как начать работу с [Terraform в Azure](https://www.terraform.io/docs/providers/azurerm/index.html).

Вы узнаете, как выполнять следующие задачи:
> [!div class="checklist"]
> * Проверка подлинности в Azure
> * создание субъекта-службы Azure с помощью Azure CLI;
> * аутентификация в Azure с помощью субъекта-службы;
> * настройка текущей подписки Azure для использования, если у вас несколько подписок;
> * создание базового файла конфигурации Terraform;
> * Создание и применение плана выполнения Terraform
> * отмена плана выполнения.

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

Существует множество способов [создать субъект-службу с помощью Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli?). В этой статье мы с помощью команды [az ad sp create-for-rbac](/cli/azure/ad/sp?#az-ad-sp-create-for-rbac) создадим субъект-службу с ролью **участника** . Роль **Участник** (по умолчанию) предоставляет полные разрешения на чтение и запись в учетной записи Azure. Дополнительные сведения об управлении доступом на основе ролей см. в статье [Встроенные роли для управления доступом на основе ролей в Azure](/azure/active-directory/role-based-access-built-in-roles).

Введите следующую команду, заменив `<subscription_id>` значением идентификатора учетной записи подписки, которую вы хотите использовать.

```azurecli
az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/<subscription_id>"
```

**Примечания**

- После успешного выполнения `az ad sp create-for-rbac` отображает несколько значений. Значения `name`, `password` и `tenant` используются на следующем шаге.
- Пароль невозможно извлечь, если он утерян. Следовательно, пароль нужно хранить в надежном месте. Если вы забыли пароль, потребуется [сбросить учетные данные субъекта-службы](/cli/azure/create-an-azure-service-principal-azure-cli#reset-credentials).

**Вход с помощью субъекта-службы Azure** : В следующем вызове `az login` замените заполнители реальными данными для субъекта-службы.

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

[!INCLUDE [terraform-create-base-config-file.md](includes/terraform-create-base-config-file.md)]

[!INCLUDE [terraform-create-and-apply-execution-plan.md](includes/terraform-create-and-apply-execution-plan.md)]

[!INCLUDE [terraform-reverse-execution-plan.md](includes/terraform-reverse-execution-plan.md)]

[!INCLUDE [terraform-troubleshooting.md](includes/terraform-troubleshooting.md)]

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Создание виртуальной машины Linux с помощью Terraform](create-linux-virtual-machine-with-infrastructure.md)
