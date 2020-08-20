---
title: Руководство. Настройка комплексного тестирования Terratest в проектах Terraform
description: Сведения о комплексном тестировании с помощью Terratest в проекте Terraform.
ms.topic: tutorial
ms.date: 07/31/2020
ms.custom: devx-track-terraform
ms.openlocfilehash: 182d403ed227eca50961e9db2df0d6766c4b9f54
ms.sourcegitcommit: 16ce1d00586dfa9c351b889ca7f469145a02fad6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/14/2020
ms.locfileid: "88241296"
---
# <a name="tutorial-setup-end-to-end-terratest-testing-on-terraform-projects"></a>Руководство по настройке комплексного тестирования Terratest в проектах Terraform

[!INCLUDE [terraform-intro.md](includes/terraform-intro.md)]

В этой статье вы узнаете, как выполнить следующие задачи:

> [!div class="checklist"]
> * Общие сведения о комплексном тестировании с помощью [Terratest](https://github.com/gruntwork-io/terratest)
> * Сведения о том, как написать комплексный тест с помощью Golang
> * Сведения об использовании Azure DevOps для автоматического запуска комплексных тестов при фиксации кода в репозитории

[!INCLUDE [hashicorp-support.md](includes/hashicorp-support.md)]

## <a name="prerequisites"></a>Предварительные условия

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
- **Наличие Terraform.** [Скачайте и установите версию Terraform](https://www.terraform.io/downloads.html), которая соответствует вашей среде.
- **Создайте вилку примеров тестирования.** Чтобы быстро приступить к работе, создайте вилку [этого репозитория](https://github.com/Azure/terraform) в своей организации GitHub.
- **Язык Go**. Тестовые случаи Terraform написаны на языке [Go](https://golang.org/dl/). В примерах в этой статье применяются [модули Go](https://blog.golang.org/using-go-modules). Для работы с этой статьей рекомендуется использовать Go 1.13 или более поздней версии.

## <a name="what-is-end-to-end-testing"></a>Что такое комплексное тестирование

Комплексные тесты позволяют проверить, работает ли система как единое целое. Этот тип тестирования отличается от тестирования отдельных модулей. В проектах Terraform комплексное тестирование позволяет проверить, что было развернуто. Этот тип тестирования отличается от многих других, которые предназначены для проверки сценариев, выполняемых перед развертыванием. Комплексные тесты важны для тестирования сложных систем, которые состоят из нескольких модулей и работают с несколькими ресурсами. В таких сценариях комплексное тестирование — это единственный способ определить, правильно ли взаимодействуют разные модули.

В этой статье описано использование [Terratest](https://github.com/gruntwork-io/terratest) для комплексного тестирования. Terratest предоставляет все необходимые средства для решения следующих задач:

- создание конфигурации Terraform;
- создание теста на языке Go для проверки развернутых ресурсов;
- поэтапное управление тестами;
- тестирование развернутой инфраструктуры.

## <a name="tutorial-scenario"></a>Учебный сценарий

В этом руководстве используется пример из репозитория [Azure/terraform](https://github.com/Azure/terraform/blob/master/samples/end-to-end-testing/README.md).

Этот пример определяет конфигурацию Terraform, которая отвечает за развертывание двух виртуальных машин Linux в одной виртуальной сети. Одна из виртуальных машин (с именем `vm-linux-1`) имеет общедоступный IP-адрес. Для разрешения SSH-подключений открыт только порт 22. Вторая виртуальная машина (`vm-linux-2`) не имеет определенного общедоступного IP-адреса.

Наш тест предназначен для проверки следующих сценариев:

- инфраструктура развернута правильно;
- можно установить подключение через порт 22 к `vm-linux-1` по протоколу SSH;
- из сеанса SSH на `vm-linux-1` можно проверить связь с `vm-linux-2`.

![Пример сценария комплексного тестирования](media/best-practices-end-to-end-testing/scenario.png)

Если вы уже [скачали пример](#prerequisites), конфигурацию Terraform для этого сценария можно найти в файле `src/main.tf`. Этот файл содержит все необходимое для развертывания инфраструктуры Azure, представленной на предыдущем рисунке.

Если вы еще не работали с созданием виртуальных машин, см. статью [Создание виртуальной машины Linux с инфраструктурой в Azure с помощью Terraform](create-linux-virtual-machine-with-infrastructure.md).

> [!CAUTION]
> Приведенный в этой статье пример сценария предназначен только для иллюстрации. Мы намеренно сохранили в нем все простым, чтобы уделить больше внимания процессам комплексного тестирования. Мы не рекомендуем использовать рабочие виртуальные машины с открытым портом SSH на общедоступном IP-адресе.

## <a name="end-to-end-test"></a>Комплексный тест

Комплексный тест написан на языке Go и использует платформу Terratest. Если вы [скачали пример](#prerequisites), он определен в файле `src/test/end2end_test.go`.

В следующем исходном коде показана стандартная структура теста Golang с использованием Terratest:

```Go
package test

import (
    "testing"

    "github.com/gruntwork-io/terratest/modules/terraform"
    test_structure "github.com/gruntwork-io/terratest/modules/test-structure"
)

func TestEndToEndDeploymentScenario(t *testing.T) {
    t.Parallel()

    fixtureFolder := "../"

    // User Terratest to deploy the infrastructure
    test_structure.RunTestStage(t, "setup", func() {
        terraformOptions := &terraform.Options{
            // Indicate the directory that contains the Terraform configuration to deploy
            TerraformDir: fixtureFolder,
        }

        // Save options for later test stages
        test_structure.SaveTerraformOptions(t, fixtureFolder, terraformOptions)

        // Triggers the terraform init and terraform apply command
        terraform.InitAndApply(t, terraformOptions)
    })

    test_structure.RunTestStage(t, "validate", func() {
        // run validation checks here
        terraformOptions := test_structure.LoadTerraformOptions(t, fixtureFolder)
            publicIpAddress := terraform.Output(t, terraformOptions, "public_ip_address")
    })

    // When the test is completed, teardown the infrastructure by calling terraform destroy
    test_structure.RunTestStage(t, "teardown", func() {
        terraformOptions := test_structure.LoadTerraformOptions(t, fixtureFolder)
        terraform.Destroy(t, terraformOptions)
    })
}
```

Как видно из предыдущего фрагмента кода, этот тест включает три этапа.

- **Настройка.** Terraform запускается для развертывания конфигурации.
- **Проверка.** Выполняются проверки и утверждения.
- **Демонтаж.** Инфраструктура очищается после выполнения теста.

В следующем списке перечислены некоторые ключевые возможности, предоставляемые платформой Terratest:

- **terraform.InitAndApply.** Обеспечивает выполнение `terraform init` и `terraform apply` из кода Go.
- **terraform.Output.** Позволяет получить значение выходной переменной развертывания.
- **terraform.Destroy.** Отвечает за выполнение команды `terraform destroy` из кода Go.
- **test_structure.LoadTerraformOptions.** Позволяет загрузить параметры Terraform, такие как конфигурация и переменные, из состояния.
- **test_structure.SaveTerraformOptions.** Отвечает за сохранение параметров Terraform, таких как конфигурация и переменные, в состояние.

## <a name="run-the-end-to-end-test"></a>Запуск комплексного теста

В рамках этого раздела вы протестируете наш пример конфигурации и развертывания. 

1. Откройте терминал или окно Bash.

1. Войдите в свою учетную запись Azure. Сведения о входе в подписку Azure см. в разделе о [проверке подлинности Azure](get-started-cloud-shell.md#authenticate-to-azure).

1. Чтобы выполнить этот пример теста, вам нужна пара закрытого и открытого ключей SSH (`id_rsa` и `id_rsa.pub`) для домашнего каталога. Замените `USER` именем корневого каталога.

```bash
export TEST_SSH_KEY_PATH="/home/USER/.ssh/id_rsa"
```

1. Перейдите в каталог `test`.

1. Запустите тест.

```go
go test -v ./ -timeout 10m
```

После выполнения этого теста отобразятся примерно такие результаты:

```output
--- PASS: TestEndToEndDeploymentScenario (390.99s)
PASS
ok      test    391.052s
```

## <a name="next-steps"></a>Дальнейшие шаги

> [!div class="nextstepaction"]
> [Общие сведения о тестировании Terraform](best-practices-testing-overview.md)