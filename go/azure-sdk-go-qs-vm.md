---
title: Развертывание виртуальной машины Azure с помощью Go
description: Разверните виртуальную машину с помощью пакета Azure SDK для Go.
ms.date: 09/05/2018
ms.topic: quickstart
ms.openlocfilehash: b777d638d0adb1b67351a7ccffb545e05309a53e
ms.sourcegitcommit: 4cf22356d6d4817421b551bd53fcba76bdb44cc1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76872101"
---
# <a name="quickstart-deploy-an-azure-virtual-machine-from-a-template-with-the-azure-sdk-for-go"></a>Краткое руководство. Развертывание виртуальной машины Azure из шаблона с помощью пакета Azure SDK для Go

В этом кратком руководстве показано, как развертывать ресурсы с помощью шаблона Azure Resource Manager и пакета Azure SDK для Go. Шаблоны — это моментальные снимки всех ресурсов, содержащихся в [группе ресурсов Azure](/azure/azure-resource-manager/resource-group-overview). Вы также ознакомитесь с функциями и соглашениями пакета SDK.

По завершении работы с этим кратким руководством у вас будет запущенная виртуальная машина, в которую можно войти с помощью имени пользователя и пароля.

> [!NOTE]
> Отслеживать создание виртуальной машины в Go без использования шаблона Resource Manager можно с помощью [императивного примера](https://github.com/Azure-Samples/azure-sdk-for-go-samples/blob/master/compute/vm.go). В нем показаны способы создания и настройки всех ресурсов виртуальной машины с помощью пакета SDK. Использование шаблона в этом примере позволяет сфокусироваться на соглашениях, связанных с пакетом SDK, не вникая в подробные сведения об архитектуре служб Azure.

[!INCLUDE [quickstarts-free-trial-note](includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](includes/cloud-shell-try-it.md)]

Если используется интерфейс Azure CLI, установленный локально, для этого краткого руководства нужно установить CLI __2.0.28__ или более поздней версии. Выполните команду `az --version`, чтобы убедиться, что установленный CLI соответствует этому требованию. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI](/cli/azure/install-azure-cli).

## <a name="install-the-azure-sdk-for-go"></a>Установка пакета Azure SDK для Go

[!INCLUDE [azure-sdk-go-get](includes/azure-sdk-go-get.md)]

## <a name="create-a-service-principal"></a>Создание субъекта-службы

Чтобы войти в Azure не в интерактивном режиме с помощью приложения, вам понадобится субъект-служба. Субъекты-службы помогают выполнять управление доступом на основе ролей, что предусматривает создание уникальных удостоверений пользователя. Чтобы создать субъект-службу с помощью CLI, выполните следующую команду:

```azurecli-interactive
az ad sp create-for-rbac --sdk-auth > quickstart.auth
```

Задайте в переменной среды `AZURE_AUTH_LOCATION` полный путь к этому файлу. Пакет SDK находит и считывает учетные данные непосредственно из этого файла. Вам не нужно вносить изменения или записывать информацию, полученную от субъекта-службы.

## <a name="get-the-code"></a>Получение кода

Получите пример кода и все его зависимости с помощью команды `go get`.

```bash
go get -u -d github.com/azure-samples/azure-sdk-for-go-samples/quickstarts/deploy-vm/...
```

Если переменная `AZURE_AUTH_LOCATION` правильно установлена, изменять исходный код не нужно. При выполнении программы из переменной загружаются все необходимые данные для аутентификации.

## <a name="running-the-code"></a>Выполнение кода

Выполните код быстрого запуска с помощью команды `go run`.

```bash
cd $GOPATH/src/github.com/azure-samples/azure-sdk-for-go-samples/quickstarts/deploy-vm
go run main.go
```

При успешном развертывании появится сообщение с именем пользователя, IP-адресом и паролем для входа в только что созданную виртуальную машину. Чтобы убедиться в работоспособности виртуальной машины, подключитесь к ней по протоколу SSH. 

## <a name="cleaning-up"></a>Очистка.

Чтобы очистить ресурсы, созданные при работе с этим кратким руководством, удалите группу ресурсов с помощью CLI.

```azurecli-interactive
az group delete -n GoVMQuickstart
```

Также удалите созданный субъект-службу. В файле `quickstart.auth` есть ключ JSON для `clientId`. Скопируйте это значение в переменную среды `CLIENT_ID_VALUE` и выполните следующую команду Azure CLI:

```azurecli-interactive
az ad sp delete --id ${CLIENT_ID_VALUE}
```

Значение для `CLIENT_ID_VALUE` можно указать из `quickstart.auth`.

> [!WARNING]
> Если субъект-службу для этого приложения удалить не удастся, он останется активным в клиенте Azure Active Directory.
> Хотя имя и пароль для субъекта-службы создаются как UUID, следуйте рекомендациям по обеспечению безопасности, удалив все неиспользуемые субъекты-службы и приложения Azure Active Directory.

## <a name="code-in-depth"></a>Подробные сведения о коде

Все операции в коде быстрого запуска состоят из блоков переменных и нескольких небольших функций. Все они рассматриваются в этой статье.

### <a name="variables-constants-and-types"></a>Переменные, константы и типы

Это краткое руководство является автономным документом, и в нем используются глобальные константы и переменные.

```go
const (
    resourceGroupName     = "GoVMQuickstart"
    resourceGroupLocation = "eastus"

    deploymentName = "VMDeployQuickstart"
    templateFile   = "vm-quickstart-template.json"
    parametersFile = "vm-quickstart-params.json"
)

// Information loaded from the authorization file to identify the client
type clientInfo struct {
    SubscriptionID string
    VMPassword     string
}

var (
    ctx        = context.Background()
    clientData clientInfo
    authorizer autorest.Authorizer
)
```

Значения объявляются, чтобы присвоить имена созданным ресурсам. Здесь также указывается расположение, которое можно изменить, чтобы понять, как выполняются развертывания в других центрах обработки данных. Не в каждом центре обработки данных доступны все необходимые ресурсы.

Тип `clientInfo` хранит информацию, загруженную из файла аутентификации для настройки клиентов в пакете SDK и установки пароля для виртуальной машины.

Константы `templateFile` и `parametersFile` указывают на файлы, необходимые для развертывания. `authorizer` настраивается для аутентификации с помощью пакета SDK для Go. Переменная `ctx` — это [контекст Go](https://blog.golang.org/context) для сетевых операций.

### <a name="authentication-and-initialization"></a>Аутентификация и инициализация

Функция `init` отвечает за настройку аутентификации. Так как аутентификации является необходимым условием для всех операций в коде быстрого запуска, целесообразно включить ее в инициализацию. Кроме того, из файла аутентификации загружаются определенные сведения, необходимые для настройки клиентов и виртуальной машины.

```go
func init() {
    var err error
    authorizer, err = auth.NewAuthorizerFromFile(azure.PublicCloud.ResourceManagerEndpoint)
    if err != nil {
        log.Fatalf("Failed to get OAuth config: %v", err)
    }

    authInfo, err := readJSON(os.Getenv("AZURE_AUTH_LOCATION"))
    clientData.SubscriptionID = (*authInfo)["subscriptionId"].(string)
    clientData.VMPassword = (*authInfo)["clientSecret"].(string)
}
```

Сначала вызывается [auth.NewAuthorizerFromFile](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#NewAuthorizerFromFile), чтобы загрузить данные для аутентификации из файла в расположении `AZURE_AUTH_LOCATION`. Затем этот файл вручную загружается с помощью функции `readJSON` (здесь не описана), чтобы извлечь два значения, необходимые для запуска оставшейся части программы: идентификатор подписки клиента и секрет субъекта-службы, который также используется для пароля виртуальной машины.

> [!WARNING]
> Для упрощения пароль субъекта-службы используется повторно. __Ни в коем случае__ не используйте пароль, который предоставляет доступ к вашим ресурсам Azure, повторно в рабочей среде.

### <a name="flow-of-operations-in-main"></a>Поток операций в функции main()

`main` — это простая функция, которая только определяет поток операций и выполняет проверку ошибок.

```go
func main() {
    group, err := createGroup()
    if err != nil {
        log.Fatalf("failed to create group: %v", err)
    }
    log.Printf("Created group: %v", *group.Name)

    log.Printf("Starting deployment: %s", deploymentName)
    result, err := createDeployment()
    if err != nil {
        log.Fatalf("Failed to deploy: %v", err)
    }
    if result.Name != nil {
        log.Printf("Completed deployment %v: %v", deploymentName, *result.Properties.ProvisioningState)
    } else {
        log.Printf("Completed deployment %v (no data returned to SDK)", deploymentName)
    }
    getLogin()
}
```

Действия выполняются в коде в следующем порядке:

* создание группы ресурсов, в которую будет выполняться развертывание (`createGroup`);
* создание развертывания в этой группе (`createDeployment`);
* получение и вывод информации для входа в развернутую виртуальную машину (`getLogin`).

### <a name="create-the-resource-group"></a>Создание группы ресурсов

Функция `createGroup` создает группу ресурсов. При работе с потоком вызова и аргументами можно увидеть, как структурируются взаимодействия служб в пакете SDK.

```go
func createGroup() (group resources.Group, err error) {
    groupsClient := resources.NewGroupsClient(clientData.SubscriptionID)
    groupsClient.Authorizer = authorizer

        return groupsClient.CreateOrUpdate(
                ctx,
                resourceGroupName,
                resources.Group{
                        Location: to.StringPtr(resourceGroupLocation)})
}
```

Общий поток взаимодействия со службой Azure:

* Создайте клиент, используя метод `service.New*Client()`, где `*` — это тип ресурса для объекта `service`, с которым нужно взаимодействовать. Эта функция всегда принимает идентификатор подписки.
* Задайте метод авторизации для клиента, чтобы он мог взаимодействовать с удаленным API.
* Используя метод, вызовите клиент, соответствующий удаленному API. Как правило, методы клиента службы принимают имя ресурса и объект метаданных.

В этом коде функция [`to.StringPtr`](https://godoc.org/github.com/Azure/go-autorest/autorest/to#StringPtr) используется для преобразования типа. Так как параметры для методов пакета SDK используют только указатели, чтобы упростить преобразования типов, предоставляются вспомогательные методы. Полный список вспомогательных преобразователей и их функций см. в документации по модулю [autorest/to](https://godoc.org/github.com/Azure/go-autorest/autorest/to).

Метод `groupsClient.CreateOrUpdate` возвращает указатель для типа данных, представляющей группу ресурсов. Возвращаемое напрямую значение такого типа указывает на быстро выполняющуюся операцию, которая должна быть синхронной. В следующем разделе приводится пример долго выполняющейся операции и описываются способы взаимодействия с ней.

### <a name="perform-the-deployment"></a>Выполнение развертывания

Когда группа ресурсов будет создана, можно перейти к развертыванию. Этот код разделен на небольшие разделы в соответствии с его логикой.

```go
func createDeployment() (deployment resources.DeploymentExtended, err error) {
    template, err := readJSON(templateFile)
    if err != nil {
        return
    }
    params, err := readJSON(parametersFile)
    if err != nil {
        return
    }
    (*params)["vm_password"] = map[string]string{
        "value": clientData.VMPassword,
    }
        // ...
```

Файлы для развертывания загружаются с помощью функции `readJSON`, подробные сведения о которой здесь пропущены. Эта функция возвращает тип `*map[string]interface{}`, который используется при создании метаданных для вызова развертывания ресурсов. Пароль для виртуальной машины также устанавливается вручную в параметрах развертывания.

```go
        // ...

    deploymentsClient := resources.NewDeploymentsClient(clientData.SubscriptionID)
    deploymentsClient.Authorizer = authorizer

    deploymentFuture, err := deploymentsClient.CreateOrUpdate(
        ctx,
        resourceGroupName,
        deploymentName,
        resources.Deployment{
            Properties: &resources.DeploymentProperties{
                Template:   template,
                Parameters: params,
                Mode:       resources.Incremental,
            },
        },
    )
    if err != nil {
        return
    }
```

Для этого кода используется тот же шаблон, что и при создании группы ресурсов. Создается клиент, которому предоставляется возможность проходить аутентификацию в Azure, а затем вызывается метод.
Метод имеет такое же имя (`CreateOrUpdate`), что и соответствующий метод для групп ресурсов. Этот шаблон используется во всем пакете SDK.
Методы, которые выполняют одинаковые функции, обычно имеют одно и то же имя.

Главное отличие заключается в возвращаемом значении метода `deploymentsClient.CreateOrUpdate`. Это значение имеет тип [Future](https://godoc.org/github.com/Azure/go-autorest/autorest/azure#Future), который основывается на [шаблоне разработки future](https://en.wikipedia.org/wiki/Futures_and_promises). Шаблоны future — это длительные операции в Azure, которые можно опрашивать, отменить или заблокировать по завершении.

```go
        //...
    err = deploymentFuture.Future.WaitForCompletion(ctx, deploymentsClient.BaseClient.Client)
    if err != nil {
        return
    }
    return deploymentFuture.Result(deploymentsClient)
}
```

В нашем примере лучше дождаться завершения операции. Для получения значения future требуется [объект контекста](https://blog.golang.org/context) и клиент, который создал `Future`. В этом случае могут произойти две ошибки: ошибка, возникшая на стороне клиента при попытке вызвать метод, и ошибка при получении ответа от сервера. Последняя возвращается при вызове `deploymentFuture.Result`.

### <a name="get-the-assigned-ip-address"></a>Получение назначенного IP-адреса

Чтобы выполнять действия с созданной виртуальной машиной, требуется назначенный IP-адрес. IP-адреса представляют отдельные ресурсы Azure виртуальной машины, связанные с ресурсами адаптера сетевого интерфейса (NIC).

```go
func getLogin() {
    params, err := readJSON(parametersFile)
    if err != nil {
        log.Fatalf("Unable to read parameters. Get login information with `az network public-ip list -g %s", resourceGroupName)
    }

    addressClient := network.NewPublicIPAddressesClient(clientData.SubscriptionID)
    addressClient.Authorizer = authorizer
    ipName := (*params)["publicIPAddresses_QuickstartVM_ip_name"].(map[string]interface{})
    ipAddress, err := addressClient.Get(ctx, resourceGroupName, ipName["value"].(string), "")
    if err != nil {
        log.Fatalf("Unable to get IP information. Try using `az network public-ip list -g %s", resourceGroupName)
    }

    vmUser := (*params)["vm_user"].(map[string]interface{})

    log.Printf("Log in with ssh: %s@%s, password: %s",
        vmUser["value"].(string),
        *ipAddress.PublicIPAddressPropertiesFormat.IPAddress,
        clientData.VMPassword)
}
```

Этот метод использует сведения, хранящиеся в файле параметров. Код может напрямую отправить запрос к виртуальной машине для получения данных ее сетевого адаптера, отправить запрос к сетевому адаптеру для получения ресурса IP-адреса, а затем напрямую отправить запрос к ресурсу IP-адреса. Это длинная цепочка зависимостей и операций, выполнять которые затратно. Вместо этого мы можем загрузить данные JSON, так как они хранятся локально.

Значение для пользователя виртуальной машины также загружается из JSON. Пароль для виртуальной машины был ранее загружен из файла аутентификации.

## <a name="next-steps"></a>Дальнейшие действия

В этом кратком руководстве вы использовали существующий шаблон и развернули его с помощью Go. Затем вы подключились к созданной виртуальной машине по протоколу SSH.

Чтобы узнать больше о работе с виртуальными машинами в среде Azure с использованием Go, ознакомьтесь с [примерами вычислительных ресурсов Azure для Go](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/compute) и [примерами управления ресурсами Azure для Go](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/resources).

Дополнительные сведения о методах аутентификации, доступных в пакете SDK, и поддерживаемых типах аутентификации см. в статье [Аутентификация с помощью пакета Azure SDK для Go](azure-sdk-go-authorization.md).
