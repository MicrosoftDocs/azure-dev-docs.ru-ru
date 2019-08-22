---
author: sptramer
ms.author: sttramer
manager: carmonm
ms.date: 09/05/2018
ms.topic: include
ms.prod: azure
ms.technology: azure-cli
ms.openlocfilehash: a51c8667c74a2611ae8769aa42fd1a94f9253bc8
ms.sourcegitcommit: 4eee7d9a484e35eb695248c011a63b27603d354b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69642948"
---
[Пакет Azure SDK для Go](https://github.com/Azure/azure-sdk-for-go) совместим с Go версии 1.8 и выше. Для окружений, в которых используются [профили Azure Stack](/azure/azure-stack/user/azure-stack-version-profiles-go), требуется Go версии не ниже 1.9.
Чтобы установить Go, следуйте [соответствующим инструкциям](https://golang.org/doc/install).

Вы можете скачать пакет Azure SDK для Go и его зависимости с помощью команды `go get`.

```bash
go get -u -d github.com/Azure/azure-sdk-for-go/...
```

> [!WARNING]
> Убедитесь, что в URL-адресе слово `Azure` написано с прописной буквы. В противном случае при работе с пакетом SDK могут возникнуть проблемы импорта, связанные с регистром. `Azure` также нужно указывать с прописной буквы в операторах импорта.
