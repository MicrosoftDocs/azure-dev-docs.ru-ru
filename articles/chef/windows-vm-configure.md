---
title: Краткое руководство. Настройка виртуальной машины Windows в Azure с помощью Chef
description: Из этого краткого руководства вы узнаете, как с помощью Chef развернуть и настроить в Azure виртуальную машину Windows.
keywords: Chef, Azure, DevOps, виртуальная машина
ms.topic: tutorial
ms.service: chef
author: tomarchermsft
ms.author: tarcher
ms.date: 02/22/2020
ms.custom: devx-track-chef
ms.openlocfilehash: 6f01653deb6d9f3f4c1c34620d3832b6e4b32c51
ms.sourcegitcommit: 39f3f69e3be39e30df28421a30747f6711c37a7b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/21/2020
ms.locfileid: "90831460"
---
# <a name="quickstart---configure-a-windows-virtual-machine-in-azure-using-chef"></a>Краткое руководство. Настройка виртуальной машины Windows в Azure с помощью Chef

Chef предоставляет возможности для автоматизации и настройки требуемого состояния.

После последнего выпуска облачного API средство Chef обеспечивает прозрачную интеграцию с Azure, позволяя подготовить и развернуть состояния конфигурации одной командой.

В этой статье показано, как настроить среду Chef для подготовки виртуальных машин Azure. Кроме того, здесь представлена пошаговая процедура создания политики (или руководства), которая затем применяется к виртуальной машине Azure.

## <a name="chef-basics"></a>Основы использования Chef

Перед началом работы с этой статьей [изучите основные принципы платформы Chef](https://www.chef.io/chef).

На приведенной ниже схеме представлена общая архитектура среды Chef.

![Архитектура Chef](media/windows-vm-configure/chef-architecure.png)

Среда Chef имеет три основных компонента архитектуры: 
- Сервер Chef — управляющий компонент, который доступен в двух вариантах: размещенное решение и локальное решение.
- Клиент Chef (узел) — агент, который находится на управляемых серверах.
- Рабочая станция Chef — так обозначается и рабочая станция администратора, где вы создаете политики и выполняете команды управления, и программный пакет инструментов Chef.

Обычно вы будете называть то расположение, в котором выполняете команды, **своей рабочей станцией**, а пакет программного обеспечения — **рабочей станцией Chef**.

Например, вы скачиваете команду knife в составе **рабочей станции Chef**, но команды knife для управления инфраструктурой выполняете уже со **своей рабочей станции**.

Кроме того, в Chef применяются понятия *руководства* и *инструкции*. Эти термины обозначают политики, определенные для серверов и примененные к ним соответственно.

## <a name="preparing-your-workstation"></a>Подготовка рабочей станции

Сначала подготовьте свою рабочую станцию, создав каталог для хранения файлов конфигурации и руководств Chef.

Создайте каталог с именем `C:\Chef`.

Скачайте и установите на своей рабочей станции последнюю версию [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="configure-azure-service-principal"></a>Настройка субъекта-службы Azure

Мы будем использовать субъект-службу, которая поможет создавать ресурсы Azure из рабочей станции Chef.  Чтобы создать соответствующий субъект-службу с необходимыми разрешениями, выполните в среде PowerShell следующие команды:

```powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "<yourSubscriptionName>"
$myApplication = New-AzureRmADApplication -DisplayName "automation-app" -HomePage "https://chef-automation-test.com" -IdentifierUris "https://chef-automation-test.com" -Password "#1234p$wdchef19"
New-AzureRmADServicePrincipal -ApplicationId $myApplication.ApplicationId
New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $myApplication.ApplicationId
```

Запишите идентификаторы SubscriptionID, TenantID, ClientID и секрет клиента (пароль, заданный ранее в этом руководстве), так как эти значения понадобятся вам позднее. 

## <a name="configure-chef-server"></a>Настройка сервера Chef

Для этого руководства вам нужно будет получить размещенную учетную запись Chef.

Если вы еще не используете сервер Chef, вы можете:

* зарегистрироваться для [получения размещенной учетной записи Chef](https://manage.chef.io/signup), которая является самым быстрым способом приступить к работе с Chef;
* установить автономный сервер Chef на компьютере под управлением Linux, следуя [инструкциям по установке](https://docs.chef.io/install_server.html) из [документации Chef](https://docs.chef.io/).

### <a name="create-a-hosted-chef-account"></a>Создание размещенной учетной записи Chef

Зарегистрируйте размещенную учетную запись Chef [здесь](https://manage.chef.io/signup).

В процессе регистрации вам будет предложено создать новую организацию.

![Окно создания организации](media/windows-vm-configure/create-organization.png)

После создания организации скачайте начальный набор.

![Настройка Chef](media/windows-vm-configure/configure-chef.png)

> [!NOTE]
> Если появится предупреждение о том, что ключи будут сброшены, можно спокойно продолжать процедуру, так как у нас пока нет никакой настроенной инфраструктуры.
>

Этот ZIP-файл начального набора содержит файлы конфигурации и пользовательский ключ организации в каталоге `.chef`.

`organization-validator.pem` следует скачать отдельно, так как это закрытый ключ, а закрытые ключи не должны храниться на сервере Chef. В [Chef Manage](https://manage.chef.io/) откройте раздел Administration (Администрирование) и выберите действие Reset Validation Key (Сбросить ключ проверки), который предоставляет файл для скачивания. Сохраните файл в каталоге C:\chef.

### <a name="configure-a-chef-workstation"></a>Настройка рабочей станции Chef

Извлеките содержимое `chef-starter.zip` в `c:\chef`.

Скопируйте все файлы из `chef-starter\chef-repo\.chef` в каталог `c:\chef`.

Скопируйте файл `organization-validator.pem` в `c:\chef`, если он размещен в `c:\Downloads`.

Теперь ваш каталог должен выглядеть, как показано ниже.

```powershell

Directory: C:\Users\username\chef

Mode           LastWriteTime    Length Name
----           -------------    ------ ----
d-----    12/6/2018   6:41 PM           .chef
d-----    12/6/2018   5:40 PM           chef-repo
d-----    12/6/2018   5:38 PM           cookbooks
d-----    12/6/2018   5:38 PM           roles
-a----    12/6/2018   5:38 PM       495 .gitignore
-a----    12/6/2018   5:37 PM      1678 azuredocs-validator.pem
-a----    12/6/2018   5:38 PM      1674 user.pem
-a----    12/6/2018   5:53 PM       414 knife.rb
-a----    12/6/2018   5:38 PM      2341 README.md
```

Вы добавили пять файлов и четыре каталога, в том числе пустой каталог chef-repo, в корень C:\chef.

### <a name="edit-kniferb"></a>Изменение файла knife.rb

PEM-файлы содержат закрытые ключи администратора и организации для обеспечения связи, а файл knife.rb содержит конфигурацию knife. Нам потребуется изменить файл knife.rb.

Откройте файл knife.rb в редакторе по своему усмотрению. Неизмененный файл должен выглядеть следующим образом:

```rb
current_dir = File.dirname(__FILE__)
log_level           :info
log_location        STDOUT
node_name           "mynode"
client_key          "#{current_dir}/user.pem"
chef_server_url     "https://api.chef.io/organizations/myorg"
cookbook_path       ["#{current_dir}/cookbooks"]
```

Добавьте следующие строки в файл knife.rb, заменяя заполнители фактическими сведениями:

```rb
validation_client_name   "myorg-validator"
validation_key           "#{current_dir}/myorg.pem"
knife[:azure_tenant_id] =         "0000000-1111-aaaa-bbbb-222222222222"
knife[:azure_subscription_id] =   "11111111-bbbbb-cccc-1111-222222222222"
knife[:azure_client_id] =         "11111111-bbbbb-cccc-1111-2222222222222"
knife[:azure_client_secret] =     "#1234p$wdchef19"
```

Эти строки предоставляют Knife ссылку на каталог руководств, размещенный в `c:\chef\cookbooks`.

Теперь файл `knife.rb` должен выглядеть как в показанном ниже примере:

![Пример файла Knife](./media/windows-vm-configure/knife-file-example.png)

```rb
current_dir = File.dirname(__FILE__)
log_level                :info
log_location             STDOUT
node_name                "myorg"
client_key               "#{current_dir}/myorg.pem"
validation_client_name   "myorg-validator"
validation_key           "#{current_dir}/myorg-validator.pem"
chef_server_url          "https://api.chef.io/organizations/myorg"
cookbook_path            ["#{current_dir}/../cookbooks"]
knife[:azure_tenant_id] = "0000000-1111-aaaa-bbbb-222222222222"
knife[:azure_subscription_id] = "11111111-bbbbb-cccc-1111-222222222222"
knife[:azure_client_id] = "11111111-bbbbb-cccc-1111-2222222222222"
knife[:azure_client_secret] = "#1234p$wdchef19"
```

## <a name="install-chef-workstation"></a>Установка рабочей станции Chef

Затем [скачайте и установите рабочую станцию Chef](https://downloads.chef.io/chef-workstation/).

Установите рабочую станцию Chef в расположение по умолчанию.

На рабочем столе появится значок CW PowerShell. Это средство используется для взаимодействия с продуктами Chef. Средство CW PowerShell предоставляет новые команды, например `chef-run`, а также обычные команды Chef CLI, такие как `chef`. Просмотрите установленную версию рабочей станции Chef и инструментов Chef с помощью `chef -v`. Кроме того, вы можете проверить версию рабочей станции, выбрав пункт **About Chef Workstation** (О рабочей станции Chef) в приложении рабочей станции Chef.

Команда `chef --version` должна вернуть примерно следующее:

```powershell
Chef Workstation: 0.4.2
  chef-run: 0.3.0
  chef-client: 15.0.300
  delivery-cli: 0.0.52 (9d07501a3b347cc687c902319d23dc32dd5fa621)
  berks: 7.0.8
  test-kitchen: 2.2.5
  inspec: 4.3.2
```

> [!NOTE]
> Порядок путей важен! Если пути opscode указаны в неправильном порядке, возникнут проблемы.
>

Прежде чем продолжить, перезагрузите рабочую станцию.

### <a name="install-knife-azure"></a>Установка Knife для Azure

В руководстве предполагается, что вы используете Azure Resource Manager для взаимодействия со своей виртуальной машиной.

Установите расширение Knife для Azure, в которое входит подключаемый модуль для Azure.

Выполните следующую команду:

```bash
chef gem install knife-azure ––pre
```

> [!NOTE]
> Указав аргумент `–-pre`, вы получите последнюю версию-кандидат подключаемого модуля Knife для Azure, который предоставляет доступ к новейшему набору API.
>
>

Вполне вероятно, что в это же время будет установлен набор зависимостей.

![Выходные данные установки knife-azure](./media/windows-vm-configure/install-knife-azure.png)

Чтобы проверить правильность настройки, выполните следующую команду.

```bash
knife azurerm server list
```

Если все настроено правильно, появится прокручиваемый список доступных образов Azure.

Поздравляем! Рабочая станция настроена.

## <a name="create-a-chef-cookbook"></a>Создание учебника по Chef

Руководство Chef определяет набор команд, которые вы намерены выполнить на управляемом клиенте. Создать руководство можно на основе шаблона. Для этого воспользуйтесь командой `chef generate cookbook`. Это руководство предназначено для веб-сервера, который автоматически развертывает службы IIS.

Выполните приведенную ниже команду в каталоге `C:\Chef directory`.

```bash
chef generate cookbook webserver
```

Эта команда создает набор файлов в каталоге C:\Chef\cookbooks\webserver. Затем определите набор команд, которые клиент Chef будет выполнять на управляемой виртуальной машине.

Команды хранятся в файле default.rb. В этом файле мы определим набор команд, который позволяет установить службы IIS, запустить их и скопировать файл шаблона в папку `wwwroot`.

Измените файл C:\chef\cookbooks\webserver\recipes\default.rb и добавьте в него указанные ниже строки.

```powershell
powershell_script 'Install IIS' do
        action :run
        code 'add-windowsfeature Web-Server'
end

service 'w3svc' do
        action [ :enable, :start ]
end

template 'c:\inetpub\wwwroot\Default.htm' do
        source 'Default.htm.erb'
        rights :read, 'Everyone'
end
```

После завершения работы сохраните файл.

## <a name="create-a-template"></a>Создание шаблона

На этом этапе вы создадите файл шаблона, который будет использоваться в качестве страницы `default.html`.

Выполните следующую команду, чтобы создать шаблон:

```bash
chef generate template webserver Default.htm
```

Перейдите к файлу `C:\chef\cookbooks\webserver\templates\default\Default.htm.erb`. Добавьте в него простой HTML-код *Hello World* и сохраните этот файл.

## <a name="upload-the-chef-cookbook-to-the-chef-server"></a>Передача учебника по Chef на сервер Chef

На этом этапе вы создадите копию руководства, созданного на локальном компьютере, и отправите ее на размещенный сервер Chef. После передачи это руководство появится на вкладке **Policy** (Политика).

```bash
knife cookbook upload webserver
```

![Результат установки руководства на сервере Chef](./media/windows-vm-configure/cookbook-installation-under-policy-tab.png)

## <a name="deploy-a-virtual-machine-with-knife-azure"></a>Развертывание виртуальной машины с помощью Knife Azure

Разверните виртуальную машину Azure и примените к ней руководство `Webserver` с помощью команды `knife`.

Кроме того, команда `knife` отвечает за установку веб-службы IIS и веб-страницы по умолчанию.

```bash
knife azurerm server create `
--azure-resource-group-name rg-chefdeployment `
--azure-storage-account store `
--azure-vm-name chefvm `
--azure-vm-size 'Standard_DS2_v2' `
--azure-service-location 'westus' `
--azure-image-reference-offer 'WindowsServer' `
--azure-image-reference-publisher 'MicrosoftWindowsServer' `
--azure-image-reference-sku '2016-Datacenter' `
--azure-image-reference-version 'latest' `
-x myuser -P myPassword123 `
--tcp-endpoints '80,3389' `
--chef-daemon-interval 1 `
-r "recipe[webserver]"
```

С помощью команды `knife` создается виртуальная машина категории *Standard_DS2_v2* с Windows Server 2016 в регионе "Западная часть США". Измените эти значения в соответствии с потребностями приложения.

После выполнения команды перейдите на портал Azure, чтобы наблюдать за подготовкой виртуальной машины.

![Виртуальная машина в процессе подготовки](./media/windows-vm-configure/virtual-machine-being-provisioned.png)

Откроется командная строка.

![Выходные данные Knife при создании виртуальной машины](./media/windows-vm-configure/knife-output-when-creating-vm.png)

Когда развертывание завершится, отобразится общедоступный IP-адрес новой виртуальной машины. Вставьте это значение в веб-браузер, чтобы открыть новый веб-сайт. Когда мы развернули виртуальную машину, открылся доступ извне к порту 80.

![Тестирование виртуальной машины](./media/windows-vm-configure/vm-test.png)

В этом примере используется изобретательный HTML-код.

Кроме того, можно узнать состояние узла [Chef Manage](https://manage.chef.io/).

![Просмотр сведений о состоянии узла](./media/windows-vm-configure/view-node-status.png)

Не забывайте, что вы также можете подключиться через сеанс протокола удаленного рабочего стола с портала Azure через порт 3389.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Chef в Azure](/azure/chef/)