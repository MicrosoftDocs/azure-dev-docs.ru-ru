---
title: Руководство. Проверка периферийной сети и концентратора в Azure с помощью Terraform
description: Узнайте, как проверять звездообразную топологию сети со всеми виртуальными сетями, подключенными друг к другу.
ms.topic: tutorial
ms.date: 10/26/2019
ms.custom: devx-track-terraform
ms.openlocfilehash: ec21091b45dfe11839e4bfe0f4fe2f37261f16cc
ms.sourcegitcommit: 16ce1d00586dfa9c351b889ca7f469145a02fad6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/14/2020
ms.locfileid: "88241116"
---
# <a name="tutorial-validate-a-hub-and-spoke-network-in-azure-using-terraform"></a>Руководство по Проверка периферийной сети и концентратора в Azure с помощью Terraform

В этой статье вы выполните файлы Terraform, созданные в рамках предыдущей статьи этой серии. Результатом является проверка возможности подключения между демонстрационными виртуальными сетями.

В рамках этого руководства рассматриваются следующие задачи:

> [!div class="checklist"]
> * реализация виртуальной сети концентратора в звездообразной топологии с помощью языка HCL;
> * использование команды terraform plan для проверки ресурсов для развертывания;
> * использование команды terraform apply для создания ресурсов в Azure;
> * проверка подключения между различными виртуальными сетями;
> * использование Terraform для удаления всех ресурсов.

[!INCLUDE [hashicorp-support.md](includes/hashicorp-support.md)]

## <a name="prerequisites"></a>Предварительные требования

1. [Создайте гибридную сеть со звездообразной топологией с помощью Terraform в Azure](./hub-spoke-introduction.md).
1. [Создайте локальную виртуальную сеть с помощью Terraform в Azure](./hub-spoke-on-prem.md).
1. [Создайте центральную виртуальную сеть с помощью Terraform в Azure](./hub-spoke-hub-network.md).
1. [Создайте устройство центральной виртуальной сети с помощью Terraform в Azure](./hub-spoke-hub-nva.md).
1. [Создайте периферийные виртуальные сети с помощью Terraform в Azure](./hub-spoke-spoke-network.md).

## <a name="verify-your-configuration"></a>Проверка конфигурации

После выполнения [предварительных требований](#prerequisites) убедитесь, что присутствуют соответствующие файлы конфигурации.

1. Перейдите на [портал Azure](https://portal.azure.com).

1. Откройте [Azure Cloud Shell](/azure/cloud-shell/overview). Если ранее среда не была выбрана, то в качестве среды необходимо выбрать **Bash**.

    ![Командная строка Cloud Shell](./media/common/azure-portal-cloud-shell-button-min.png)

1. Перейдите в каталог `clouddrive`.

    ```bash
    cd clouddrive
    ```

1. Перейдите в новый каталог:

    ```bash
    cd hub-spoke
    ```

1. Выполните `ls` команду, чтобы убедиться, что перечислены файлы конфигурации `.tf`, созданные в рамках предыдущих учебников:

    ![Демонстрационные файлы конфигурации Terraform](./media/hub-and-spoke-tutorial-series/hub-spoke-config-files.png)

## <a name="deploy-the-resources"></a>Развертывание ресурсов

1. Инициализируйте поставщик Terraform:
    
    ```bash
    terraform init
    ```
    
    ![Пример результатов команды terraform init](./media/hub-and-spoke-tutorial-series/hub-spoke-terraform-init.png)
    
1. Выполните команду `terraform plan`, чтобы увидеть результат развертывания перед выполнением:

    ```bash
    terraform plan
    ```
    
    ![Пример результатов команды terraform plan](./media/hub-and-spoke-tutorial-series/hub-spoke-terraform-plan.png)

1. Разверните решение:

    ```bash
    terraform apply
    ```
    
    Введите `yes` при появлении запроса на подтверждение развертывания.

    ![Пример результатов команды terraform apply](./media/hub-and-spoke-tutorial-series/hub-spoke-terraform-apply.png)
    
## <a name="test-the-hub-vnet-and-spoke-vnets"></a>Проверка виртуальной сети концентратора и периферийных виртуальных сетей

В этом разделе показано, как проверить возможность подключения из имитированной локальной среды к виртуальной сети концентратора.

1. На портале Azure перейдите к группе ресурсов **onprem-vnet-rg**.

1. На вкладке **onprem-vnet-rg** выберите виртуальную машину с именем **onprem-vm**.

1. Выберите **Подключиться**.

1. Рядом с текстом **Вход с использованием локальной учетной записи ВМ** скопируйте команду **ssh** в буфер обмена.

1. В командной строке Linux запустите `ssh`, чтобы подключиться к моделируемой локальной среде. Используйте пароль, указанный в файле параметров `on-prem.tf`.

1. Выполните команду `ping` для проверки подключения к виртуальной машине jumpbox в виртуальной сети концентратора:

   ```bash
   ping 10.0.0.68
   ```

1. Выполните команду `ping`, чтобы проверить возможность подключения к виртуальным машинам jumpbox в каждой периферийной зоне:

   ```bash
   ping 10.1.0.68
   ping 10.2.0.68
   ```

1. Чтобы выйти из сеанса SSH на виртуальной машине **onprem-vm** введите `exit` и нажмите клавишу &lt;ВВОД>.

## <a name="troubleshoot-vpn-issues"></a>Устранение неполадок с VPN

Сведения об устранении ошибок VPN см. в статье [Устранение неполадок при гибридном VPN-подключении](/azure/architecture/reference-architectures/hybrid-networking/troubleshoot-vpn).

## <a name="clean-up-resources"></a>Очистка ресурсов

Удалите ресурсы Azure, созданные в рамках этой серии учебников, если они вам больше не нужны.

1. Удалите ресурсы, объявленные в плане:

    ```bash
    terraform destroy
    ```

    Введите `yes` при появлении запроса на подтверждение удаления ресурсов.

1. Перейдите в родительский каталог:

    ```bash
    cd ..
    ```

1. Удалите каталог `hub-scope` (включая все его файлы):

    ```bash
    rm -r hub-spoke
    ```

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"] 
> [Документация по Terraform в Azure](/azure/terraform)