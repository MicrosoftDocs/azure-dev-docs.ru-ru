---
title: include file azure-sign-in.md
description: include file azure-sign-in.md
ms.date: 10/13/2020
ms.topic: include
ms.custom: devx-track-javascript
ms.openlocfilehash: e500a760430d92fa6640d964320a3ef3f161b7cc
ms.sourcegitcommit: 5f64710b2b0822e789c7f15acba5a3a257c033f9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/05/2020
ms.locfileid: "93405242"
---
В этом разделе вы внесете небольшое изменение в код, а затем повторно развернете код в Azure. Изменение кода включает в себя инструкцию `print` для создания выходных данных журнала, которые понадобятся в следующем разделе.

Откройте *app.py* в редакторе и обновите функцию `hello`, чтобы она соответствовала следующему коду. 

```python
def hello():
    print("Handling request to home page.")
    return "Hello, Azure!"
```

    
Сохраните изменения, а затем повторно разверните приложение с помощью команды `az webapp up`.

```azurecli
az webapp up
```

Эта команда использует значения, которые кэшируются локально в файле *.azure/config* , включая имя приложения, группу ресурсов и план службы приложений.

После завершения развертывания вернитесь в окно браузера и откройте `http://<app-name>.azurewebsites.net`. Обновите страницу, на которой должно отобразиться измененное сообщение:

![Запуск обновленного примера приложения Python в Azure](../../media/quickstart-python/run-updated-hello-world-sample-python-app-in-browser.png)

> [!TIP]
> Visual Studio Code предоставляет разнообразные расширения для Python и Службы приложений Azure, которые упрощают процесс развертывания веб-приложений Python в службе приложений. Дополнительные сведения см. в статье [Развертывание приложений Python в Службе приложений из Visual Studio Code](/azure/python/tutorial-deploy-app-service-on-linux-01).