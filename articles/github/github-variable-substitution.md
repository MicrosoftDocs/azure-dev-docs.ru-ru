---
title: Использование подстановки переменных в GitHub Actions для Azure
description: Действие GitHub для подстановки переменных в параметризованных файлах
author: juliakm
ms.author: jukullam
ms.topic: conceptual
ms.service: azure
ms.date: 01/25/2021
ms.custom: github-actions-azure
ms.openlocfilehash: e2a82fbcbe48269339dc672d46aca4cc3601ae12
ms.sourcegitcommit: 6fbf9e489b194586887a2c11152044be5b3a2b99
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/25/2021
ms.locfileid: "98759493"
---
# <a name="use-variable-substitution-with-github-actions"></a>Использование подстановки переменных в GitHub Actions

Узнайте, как использовать [действие подстановки переменных](https://github.com/marketplace/actions/variable-substitution) для замены значений в файлах конфигураций и параметров форматов XML, JSON и YAML.

Подстановка переменных позволяет вставлять в файлы из репозитория переменные значения, например [секреты GitHub](https://docs.github.com/en/actions/reference/encrypted-secrets), во время выполнения рабочего процесса. Например, так можно вставить в JSON-файл имя входа и пароль для API во время выполнения рабочего процесса.

Подстановку переменных можно выполнить только для ключей, заранее определенных в иерархии объектов. С помощью подстановки переменных нельзя создать новые ключи. Кроме того, для подстановки можно использовать только переменные, которые определены как [переменные среды](https://docs.github.com/en/actions/reference/environment-variables) в самом рабочем процессе или в уже доступных системных переменных.

## <a name="prerequisites"></a>Предварительные требования

- Учетная запись GitHub. Если у вас ее нет, зарегистрируйтесь [бесплатно](https://github.com/join).  

## <a name="use-the-variable-substitution-action"></a>Использование действия подстановки переменных

В этом примере рассматривается замена значений в `employee.json` с помощью [действия подстановки переменных](https://github.com/marketplace/actions/variable-substitution).

1. Создайте `employee.json` на корневом уровне репозитория.

    ```json
    {
        "first-name": "Toni",
        "last-name": "Cranz",
        "username": "",
        "password": "",
        "url": ""
    }
    ```

2. Откройте репозиторий GitHub и перейдите в раздел **Параметры**.

    :::image type="content" source="media/github-repo-settings.png" alt-text="Выбор раздела &quot;Параметры&quot; в области навигации":::

3. Выберите **Секреты**, а затем **Создать секрет**.

    :::image type="content" source="media/select-secrets.png" alt-text="Выбор действия добавления секрета":::

4. Добавьте новый секрет `PASSWORD` со значением `5v{W<$2B<GR2=t4#` (или с выбранным вами паролем). Сохраните этот секрет. 

5. Перейдите к разделу **Actions** (Действия) и выберите элемент **set up a workflow yourself** (настроить рабочий процесс самостоятельно).

6. Добавьте файл рабочего процесса Теперь значение username в файле JSON будет заменено значением `tcranz`. Значение password будет заменено указанным секретом GitHub. В поле url будет указан URL-адрес, который содержит переменную GitHub `github.repository`.

    ```yaml
    on: [push]
    name: variable substitution in json

    jobs:
    build:
        runs-on: ubuntu-latest
        steps:
        - uses: actions/checkout@v2
        - uses: microsoft/variable-substitution@v1 
        with:
            files: 'employee.json'
        env:
            username: tcranz
            password: ${{ secrets.PASSWORD }}
            url: https://github.com/${{github.repository}}

    ```

7. Перейдите к разделу **Actions** (Действия), чтобы просмотреть сведения о выполнении рабочего процесса. Откройте действие подстановки переменных. Здесь вы увидите, что все описанные выше переменные успешно заменены.

    ```text
    SubstitutingValueonKeyWithString username tcranz
    SubstitutingValueonKeyWithString password ***
    SubstitutingValueonKeyWithString url https://github.com/account/variable-sub
    Successfully updated file: employee.json
    ```

## <a name="clean-up-resources"></a>Очистка ресурсов

Удалите репозиторий GitHub, когда он больше не будет нужен.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Развертывание в Службе приложений Azure с помощью GitHub Actions](/azure/app-service/deploy-github-actions)
