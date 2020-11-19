---
title: Руководство. Тестирование модулей Terraform в Azure с помощью Terratest
description: Узнайте, как с помощью Terratest тестировать модули Terraform.
ms.topic: tutorial
ms.date: 10/26/2019
ms.custom: devx-track-terraform, devx-track-azurecli
ms.openlocfilehash: a8e5f674e28ed3ba7265b3f97541f49f27013bdd
ms.sourcegitcommit: dc74b60217abce66fe6cc93923e869e63ac86a8f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/18/2020
ms.locfileid: "94872855"
---
# <a name="tutorial-test-terraform-modules-in-azure-using-terratest"></a>Руководство по Тестирование модулей Terraform в Azure с помощью Terratest

> [!NOTE]
> Пример кода из этой статьи не работает в версии 012 и последующих.

С помощью модулей Terraform можно создавать многократно используемые, составные и тестируемые компоненты. Модули Terraform внедряют инкапсуляцию, которая удобна при реализации инфраструктуры в качестве процессов кода.

Очень важно реализовать контроль качества при создании модулей Terraform. К сожалению, доступно мало документации, в которой объясняется, как создавать модульные тесты и тесты интеграции в модулях Terraform. В этом руководстве описывается инфраструктура тестирования и представлены рекомендации, реализованные при создании [модулей Azure Terraform](https://registry.terraform.io/browse?provider=azurerm).

Мы рассмотрели все наиболее популярные инфраструктуры тестирования и решили использовать [Terratest](https://github.com/gruntwork-io/terratest) для тестирования модулей Terraform. Terratest реализуется в виде библиотеки Go. Terratest предоставляет набор вспомогательных функций и шаблонов для распространенных задач тестирования инфраструктуры, таких как создание HTTP-запросов и доступ к определенной виртуальной машине по протоколу SSH. В следующем списке приведены некоторые из основных преимуществ использования Terratest:

- **Удобные вспомогательные приложения для проверки инфраструктуры**. Эта функция полезна, если вы хотите проверить реальную инфраструктуру в реальной среде.
- **Упорядоченная структура папок**. Тестовые случаи хорошо упорядочены и соответствуют [стандартной структуре папок модуля Terraform](https://www.terraform.io/docs/modules/create.html#standard-module-structure).
- **Тестовые случаи написаны на языке Go.** Большинство разработчиков, использующих Terraform, работают с Go. Если это ваш случай, вам не нужно осваивать еще один язык для использования Terratest.
- **Расширяемая инфраструктура**. Вы можете расширить дополнительные функции, включая связанные с Azure, на Terratest.

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
- **Язык программирования Go**. Тестовые случаи Terraform написаны на языке [Go](https://golang.org/dl/).
- **dep**. [dep](https://github.com/golang/dep#installation) — это средство управления зависимостями для Go.
- **Azure CLI.** [Azure CLI](/cli/azure/install-azure-cli) — это программа командной строки, которую можно использовать для управления ресурсами Azure. (Terraform поддерживает проверку подлинности в Azure с помощью субъекта-службы или [через Azure CLI](https://www.terraform.io/docs/providers/azurerm/authenticating_via_azure_cli.html).)
- **mage**. Мы используем [исполняемый файл mage](https://github.com/magefile/mage/releases), чтобы показать, как упростить выполнение случаев Terratest. 

## <a name="create-a-static-webpage-module"></a>Создание модуля статической веб-страницы

В этом руководстве вы создаете модуль Terraform, который подготавливает к работе статическую веб-страницу, отправив один HTML-файл в большой двоичный объект службы хранилища Azure. Этот модуль предоставляет пользователям по всему миру доступ к веб-странице через URL-адрес, который возвращает модуль.

> [!NOTE]
> Создайте все файлы, описанные в этом разделе, в вашем расположении [GOPATH](https://github.com/golang/go/wiki/SettingGOPATH).

Сначала создайте папку с именем `staticwebpage` в папке `src` GoPath. Общая структура папок для работы с этим руководством показана в следующем примере. (Файлы, отмеченные звездочкой `(*)`, являются основным приоритетом в этом разделе.)

```
 📁 GoPath/src/staticwebpage
   ├ 📁 examples
   │   └ 📁 hello-world
   │       ├ 📄 index.html
   │       └ 📄 main.tf
   ├ 📁 test
   │   ├ 📁 fixtures
   │   │   └ 📁 storage-account-name
   │   │       ├ 📄 empty.html
   │   │       └ 📄 main.tf
   │   ├ 📄 hello_world_example_test.go
   │   └ 📄 storage_account_name_unit_test.go
   ├ 📄 main.tf      (*)
   ├ 📄 outputs.tf   (*)
   └ 📄 variables.tf (*)
```

Модуль статической веб-страницы принимает три вида входных данных. Входные данные объявлены в файле `./variables.tf`:

```hcl
variable "location" {
  description = "The Azure region in which to create all resources."
}

variable "website_name" {
  description = "The website name to use to create related resources in Azure."
}

variable "html_path" {
  description = "The file path of the static home page HTML in your local file system."
  default     = "index.html"
}
```

Как упоминалось ранее в этой статье, этот модуль также выводит URL-адрес, объявленный в файле `./outputs.tf`:

```hcl
output "homepage_url" {
  value = azurerm_storage_blob.homepage.url
}
```

Основная логика модуля подготавливает четыре ресурса:
- **Группа ресурсов**. Имя группы ресурсов — входные данные `website_name` с добавлением `-staging-rg`.
- **Учетная запись хранения**. Имя учетной записи хранения — входные данные `website_name` с добавлением `data001`. Для соблюдения ограничений имени учетной записи хранения модуль удаляет все специальные символы и использует буквы в нижнем регистре во всем имени учетной записи хранения.
- **Контейнер с фиксированным именем**. Контейнеру присваивается имя `wwwroot`, и он создается в учетной записи хранения.
- **Один HTML-файл**. HTML-файл считывается из входных данных `html_path` и отправляется в `wwwroot/index.html`.

Логика модуля статической веб-страницы реализована в `./main.tf`:

```hcl
resource "azurerm_resource_group" "main" {
  name     = "${var.website_name}-staging-rg"
  location = var.location
}

resource "azurerm_storage_account" "main" {
  name                     = "${lower(replace(var.website_name, "/[[:^alnum:]]/", ""))}data001"
  resource_group_name      = azurerm_resource_group.main.name
  location                 = azurerm_resource_group.main.location
  account_tier             = "Standard"
  account_replication_type = "LRS"
}

resource "azurerm_storage_container" "main" {
  name                  = "wwwroot"
  resource_group_name   = azurerm_resource_group.main.name
  storage_account_name  = azurerm_storage_account.main.name
  container_access_type = "blob"
}

resource "azurerm_storage_blob" "homepage" {
  name                   = "index.html"
  resource_group_name    = azurerm_resource_group.main.name
  storage_account_name   = azurerm_storage_account.main.name
  storage_container_name = azurerm_storage_container.main.name
  source                 = var.html_path
  type                   = "block"
  content_type           = "text/html"
}
```

### <a name="unit-test"></a>Модульный тест

Terratest предназначен для тестов интеграции. Для этой цели Terratest подготавливает реальные ресурсы в реальной среде. Иногда задания тестов интеграции могут стать чрезвычайно большими, особенно в том случае, если необходимо подготовить к работе множество ресурсов. Хорошим примером является упомянутая в предыдущем разделе логика, которая преобразовывает имена учетных записей хранения. 

Но нам не нужно подготавливать какие-либо ресурсы. Требуется только убедиться в правильности логики преобразования имен. Благодаря гибкости Terratest мы можем использовать модульные тесты. Это выполняемые локально тестовые случаи (хотя требуется доступ к Интернету). Модульные тестовые случаи выполняют команды `terraform init` и `terraform plan`, чтобы проанализировать выходные данные `terraform plan` и найти значения атрибутов для сравнения.

В оставшейся части этого раздела описывается, как мы используем Terratest для реализации модульного теста, чтобы убедиться в правильности логики, используемой для преобразования имен учетных записей хранения. Нас интересуют только файлы, отмеченные звездочкой `(*)`.

```
 📁 GoPath/src/staticwebpage
   ├ 📁 examples
   │   └ 📁 hello-world
   │       ├ 📄 index.html
   │       └ 📄 main.tf
   ├ 📁 test
   │   ├ 📁 fixtures
   │   │   └ 📁 storage-account-name
   │   │       ├ 📄 empty.html                (*)
   │   │       └ 📄 main.tf                   (*)
   │   ├ 📄 hello_world_example_test.go
   │   └ 📄 storage_account_name_unit_test.go (*)
   ├ 📄 main.tf
   ├ 📄 outputs.tf
   └ 📄 variables.tf
```

Сначала мы используем пустой HTML-файл с именем `./test/fixtures/storage-account-name/empty.html` в качестве заполнителя.

В файле `./test/fixtures/storage-account-name/main.tf` приведена структура тестового случая. Он принимает одни входные данные `website_name`, которые также являются входными данными модульных тестов. Ниже приведена логика:

```hcl
variable "website_name" {
  description = "The name of your static website."
}

module "staticwebpage" {
  source       = "../../../"
  location     = "West US"
  website_name = var.website_name
  html_path    = "empty.html"
}
```

Основной компонент — это реализация модульных тестов в `./test/storage_account_name_unit_test.go`

Разработчик Go, вероятно, заметит, что модульный тест соответствует подписи классической функции проверки Go, принимая аргумент типа `*testing.T`.

В тексте модульного теста есть всего пять случаев, определенных в переменной `testCases` (`key` — входные данные, а `value` — ожидаемые выходные). Для каждого модульного тестового случая сначала выполняется команда `terraform init`, целью которой является папка средства тестирования (`./test/fixtures/storage-account-name/`). 

Далее команда `terraform plan` с определенными входными данными тестового случая (ознакомьтесь с определением `website_name` в `tfOptions`) сохраняет результат в файл `./test/fixtures/storage-account-name/terraform.tfplan` (не указан в общей структуре папок).

Этот файл результатов анализируется и преобразовывается в структуру для считывания в коде с помощью официального средства синтаксического анализа плана Terraform.

Теперь мы ищем атрибуты, которые нас интересуют (в данном случае `name` из `azurerm_storage_account`), и сравниваем результаты с ожидаемыми выходными данными:

```go
package test

import (
    "os"
    "path"
    "testing"

    "github.com/gruntwork-io/terratest/modules/terraform"
    terraformCore "github.com/hashicorp/terraform/terraform"
)

func TestUT_StorageAccountName(t *testing.T) {
    t.Parallel()

    // Test cases for storage account name conversion logic
    testCases := map[string]string{
        "TestWebsiteName": "testwebsitenamedata001",
        "ALLCAPS":         "allcapsdata001",
        "S_p-e(c)i.a_l":   "specialdata001",
        "A1phaNum321":     "a1phanum321data001",
        "E5e-y7h_ng":      "e5ey7hngdata001",
    }

    for input, expected := range testCases {
        // Specify the test case folder and "-var" options
        tfOptions := &terraform.Options{
            TerraformDir: "./fixtures/storage-account-name",
            Vars: map[string]interface{}{
                "website_name": input,
            },
        }

        // Terraform init and plan only
        tfPlanOutput := "terraform.tfplan"
        terraform.Init(t, tfOptions)
        terraform.RunTerraformCommand(t, tfOptions, terraform.FormatArgs(tfOptions, "plan", "-out="+tfPlanOutput)...)

        // Read and parse the plan output
        f, err := os.Open(path.Join(tfOptions.TerraformDir, tfPlanOutput))
        if err != nil {
            t.Fatal(err)
        }
        defer f.Close()
        plan, err := terraformCore.ReadPlan(f)
        if err != nil {
            t.Fatal(err)
        }

        // Validate the test result
        for _, mod := range plan.Diff.Modules {
            if len(mod.Path) == 2 && mod.Path[0] == "root" && mod.Path[1] == "staticwebpage" {
                actual := mod.Resources["azurerm_storage_account.main"].Attributes["name"].New
                if actual != expected {
                    t.Fatalf("Expect %v, but found %v", expected, actual)
                }
            }
        }
    }
}
```

Для выполнения модульных тестов сделайте следующее в командной строке.

```azurecli
az login    # Required when no service principal environment variables are present
```

```shell
cd [Your GoPath]/src/staticwebpage
dep init    # Run only once for this folder
dep ensure  # Required to run if you imported new packages in test cases
cd test
go fmt
go test -run TestUT_StorageAccountName
```

Традиционный результат теста Go возвращается в течение одной минуты.

### <a name="integration-test"></a>Тест интеграции

В отличие от модульных тестов тесты интеграции должны подготавливать ресурсы к реальной среде с точки зрения комплексной перспективы. Terratest очень хорошо справляется с задачами такого рода. 

Рекомендации для модулей Terraform включают установку папки `examples`. Папка `examples` содержит некоторые полноценные примеры. Чтобы избежать работы с реальными данными, почему бы не протестировать эти примеры как тесты интеграции? В этом разделе мы сосредоточимся на трех файлах, помеченных звездочкой `(*)`, в следующей структуре папок:

```
 📁 GoPath/src/staticwebpage
   ├ 📁 examples
   │   └ 📁 hello-world
   │       ├ 📄 index.html              (*)
   │       └ 📄 main.tf                 (*)
   ├ 📁 test
   │   ├ 📁 fixtures
   │   │   └ 📁 storage-account-name
   │   │       ├ 📄 empty.html
   │   │       └ 📄 main.tf
   │   ├ 📄 hello_world_example_test.go (*)
   │   └ 📄 storage_account_name_unit_test.go
   ├ 📄 main.tf
   ├ 📄 outputs.tf
   └ 📄 variables.tf
```

Давайте начнем с примеров. В папке `./examples/` создается пример папки с именем `hello-world/`. Здесь представлена простая страница HTML для отправки: `./examples/hello-world/index.html`:

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>Hello World</title>
</head>
<body>
    <h1>Hi, Terraform Module</h1>
    <p>This is a sample web page to demonstrate Terratest.</p>
</body>
</html>
```

Пример Terraform `./examples/hello-world/main.tf` подобен примеру, показанному в модульном тесте. Есть одно существенное различие: пример также выводит URL-адрес отправленного HTML-файла в качестве веб-страницы с именем `homepage`.

```hcl
variable "website_name" {
  description = "The name of your static website."
  default     = "Hello-World"
}

module "staticwebpage" {
  source       = "../../"
  location     = "West US"
  website_name = var.website_name
}

output "homepage" {
  value = module.staticwebpage.homepage_url
}
```

Мы используем Terratest и классические функции проверки Go еще раз в файле теста интеграции `./test/hello_world_example_test.go`.

В отличие от модульных тестов тесты интеграции создают фактические ресурсы в Azure. Вот почему необходимо быть осторожными, чтобы избежать конфликтов имен. (Обратите особое внимание на некоторые глобально уникальные имена, такие как имена учетных записей хранения.) Таким образом, первым шагом логики тестирования является создание случайного `websiteName` с помощью функции `UniqueId()`, предоставляемой Terratest. Эта функция создает случайное имя, которое содержит буквы в нижнем и верхнем регистре или цифры. `tfOptions` выполняет все команды Terraform, нацеленные на папку `./examples/hello-world/`. Она также позволяет убедиться, что для параметра `website_name` задано случайное `websiteName`.

Затем одна за другой выполняются команды `terraform init`, `terraform apply`, и `terraform output`. Мы используем другую вспомогательную функцию `HttpGetWithCustomValidation()`, предоставляемую Terratest, чтобы убедиться в том, что HTML передается в URL-адрес `homepage` выходных данных, возвращаемый в `terraform output`. Мы сравниваем код состояния HTTP GET с `200` и выполняем поиск некоторых ключевых слов в содержимом HTML. Наконец, обеспечивается выполнение `terraform destroy` с помощью функции `defer` Go.

```go
package test

import (
    "fmt"
    "strings"
    "testing"

    "github.com/gruntwork-io/terratest/modules/http-helper"
    "github.com/gruntwork-io/terratest/modules/random"
    "github.com/gruntwork-io/terratest/modules/terraform"
)

func TestIT_HelloWorldExample(t *testing.T) {
    t.Parallel()

    // Generate a random website name to prevent a naming conflict
    uniqueID := random.UniqueId()
    websiteName := fmt.Sprintf("Hello-World-%s", uniqueID)

    // Specify the test case folder and "-var" options
    tfOptions := &terraform.Options{
        TerraformDir: "../examples/hello-world",
        Vars: map[string]interface{}{
            "website_name": websiteName,
        },
    }

    // Terraform init, apply, output, and destroy
    defer terraform.Destroy(t, tfOptions)
    terraform.InitAndApply(t, tfOptions)
    homepage := terraform.Output(t, tfOptions, "homepage")

    // Validate the provisioned webpage
    http_helper.HttpGetWithCustomValidation(t, homepage, func(status int, content string) bool {
        return status == 200 &&
            strings.Contains(content, "Hi, Terraform Module") &&
            strings.Contains(content, "This is a sample web page to demonstrate Terratest.")
    })
}
```

Для выполнения тестов интеграции сделайте следующее в командной строке:

```azurecli
az login    # Required when no service principal environment variables are present
```

```shell
cd [Your GoPath]/src/staticwebpage
dep init    # Run only once for this folder
dep ensure  # Required to run if you imported new packages in test cases
cd test
go fmt
go test -run TestIT_HelloWorldExample
```

Традиционный результат теста Go возвращается в течение двух минут. Вы также можете запустить и модульные тесты, и тесты интеграции, выполнив эти команды:

```shell
go fmt
go test
```

Тесты интеграции занимают гораздо больше времени, чем модульные (две минуты для одного случая интеграции в сравнении с одной минутой для пяти модульных случаев). Но вам решать, что использовать в сценарии: модульные тесты или тесты интеграции. Как правило, мы предпочитаем применять модульные тесты для сложной логики с использованием функций Terraform HCL, а тесты интеграции — с точки зрения комплексной перспективы пользователя.

## <a name="use-mage-to-simplify-running-terratest-cases"></a>Упрощение обработки случаев Terratest с помощью mage 
Для запуска тестовых случаев в Azure Cloud Shell требуется выполнить разные команды в разных каталогах. Чтобы сделать этот процесс более эффективным, мы представляем систему сборки в нашем проекте. В этом разделе для выполнения задания мы используем систему сборки Go — mage.

Единственное, что требуется mage, это файл `magefile.go` в корневом каталоге вашего проекта (помечен символом `(+)` в следующем примере).

```
 📁 GoPath/src/staticwebpage
   ├ 📁 examples
   │   └ 📁 hello-world
   │       ├ 📄 index.html
   │       └ 📄 main.tf
   ├ 📁 test
   │   ├ 📁 fixtures
   │   │   └ 📁 storage-account-name
   │   │       ├ 📄 empty.html
   │   │       └ 📄 main.tf
   │   ├ 📄 hello_world_example_test.go
   │   └ 📄 storage_account_name_unit_test.go
   ├ 📄 magefile.go (+)
   ├ 📄 main.tf
   ├ 📄 outputs.tf
   └ 📄 variables.tf
```

Ниже приведен пример `./magefile.go`. В этом скрипте сборки на языке Go мы реализовали пять шагов сборки:
- `Clean`: На этом шаге удаляются все сгенерированные и временные файлы, созданные во время выполнения тестов.
- `Format`: На этом шаге выполняется `terraform fmt` и `go fmt` для форматирования базы кода.
- `Unit`: На этом шаге выполняются все модульные тесты (с использованием соглашения об именовании функции `TestUT_*`) в папке `./test/`.
- `Integration`: Этот шаг аналогичен `Unit`, но в отличие от модульных тестов на этом этапе выполняются тесты интеграции (`TestIT_*`).
- `Full`: На этом шаге последовательно выполняются `Clean`, `Format`, `Unit` и `Integration`.

```go
// +build mage

// Build a script to format and run tests of a Terraform module project
package main

import (
    "fmt"
    "os"
    "path/filepath"

    "github.com/magefile/mage/mg"
    "github.com/magefile/mage/sh"
)

// The default target when the command executes `mage` in Cloud Shell
var Default = Full

// A build step that runs Clean, Format, Unit and Integration in sequence
func Full() {
    mg.Deps(Unit)
    mg.Deps(Integration)
}

// A build step that runs unit tests
func Unit() error {
    mg.Deps(Clean)
    mg.Deps(Format)
    fmt.Println("Running unit tests...")
    return sh.RunV("go", "test", "./test/", "-run", "TestUT_", "-v")
}

// A build step that runs integration tests
func Integration() error {
    mg.Deps(Clean)
    mg.Deps(Format)
    fmt.Println("Running integration tests...")
    return sh.RunV("go", "test", "./test/", "-run", "TestIT_", "-v")
}

// A build step that formats both Terraform code and Go code
func Format() error {
    fmt.Println("Formatting...")
    if err := sh.RunV("terraform", "fmt", "."); err != nil {
        return err
    }
    return sh.RunV("go", "fmt", "./test/")
}

// A build step that removes temporary build and test files
func Clean() error {
    fmt.Println("Cleaning...")
    return filepath.Walk(".", func(path string, info os.FileInfo, err error) error {
        if err != nil {
            return err
        }
        if info.IsDir() && info.Name() == "vendor" {
            return filepath.SkipDir
        }
        if info.IsDir() && info.Name() == ".terraform" {
            os.RemoveAll(path)
            fmt.Printf("Removed \"%v\"\n", path)
            return filepath.SkipDir
        }
        if !info.IsDir() && (info.Name() == "terraform.tfstate" ||
            info.Name() == "terraform.tfplan" ||
            info.Name() == "terraform.tfstate.backup") {
            os.Remove(path)
            fmt.Printf("Removed \"%v\"\n", path)
        }
        return nil
    })
}
```

Для выполнения полного набора тестов можно использовать следующие команды. Код аналогичен шагам, которые выполнялись в предыдущем разделе. 

```azurecli
az login    # Required when no service principal environment variables are present
```

```shell
cd [Your GoPath]/src/staticwebpage
dep init    # Run only once for this folder
dep ensure  # Required to run if you imported new packages in magefile or test cases
go fmt      # Only required when you change the magefile
mage
```

Последнюю командную строку можно заменить дополнительными действиями mage. Например, вы можете использовать `mage unit` или `mage clean`. Хорошая идея — внедрить команды `dep` и `az login` в magefile. Мы не показываем код здесь. 

С mage вы можете также предоставить общий доступ к действиям, используя пакетную систему Go. В этом случае файлы magefile во всех модулях можно упростить, просто ссылаясь на общую реализацию и объявляя зависимости (`mg.Deps()`).

**Необязательно. Установка переменных среды субъекта-службы для выполнения тестов приемки**
 
Вместо выполнения команды `az login` перед тестом проверку подлинности Azure можно выполнить, задав переменные среды субъекта-службы. Terraform выводит [список имен переменных среды](https://www.terraform.io/docs/providers/azurerm/index.html#testing). (Требуются только первые четыре переменных среды.) Terraform также выводит подробные инструкции о том, как [получить значение этих переменных среды](https://www.terraform.io/docs/providers/azurerm/authenticating_via_service_principal.html).

[!INCLUDE [terraform-troubleshooting.md](includes/terraform-troubleshooting.md)]

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"] 
> [Страница GitHub Terratest](https://github.com/gruntwork-io/terratest).
