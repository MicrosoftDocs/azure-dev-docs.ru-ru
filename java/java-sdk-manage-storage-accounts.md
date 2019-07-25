---
title: Управление учетными записями хранения Azure с помощью Java | Документация Майкрософт
description: Пример кода для управления учетными записями хранения Azure с помощью пакета Azure SDK для Java
author: rloutlaw
manager: douge
ms.assetid: 49be8b66-3b56-4c10-8f14-9d326d815cb4
ms.devlang: java
ms.topic: article
ms.service: Azure
ms.technology: Azure
ms.date: 3/30/2017
ms.author: brendm;asirveda
ms.openlocfilehash: 187190f34572cf048eb6e5c37f2531a3a634390f
ms.sourcegitcommit: 2efdb9d8a8f8a2c1914bd545a8c22ae6fe0f463b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/15/2019
ms.locfileid: "68284315"
---
# <a name="manage-azure-storage-accounts-from-your-java-applications"></a>Управление учетными записями хранения Azure из приложений Java

[В этом примере кода](https://github.com/Azure-Samples/storage-java-manage-storage-accounts) создается учетная запись [хранения Azure](https://docs.microsoft.com/azure/storage/storage-introduction) и используются ключи доступа учетной записи с помощью [библиотек управления Java](https://github.com/Azure/azure-sdk-for-java). 

## <a name="run-the-sample"></a>Запуск примера

Создайте [файл проверки подлинности](https://github.com/Azure/azure-sdk-for-java/blob/master/AUTH.md), задайте переменную среды `AZURE_AUTH_LOCATION` и укажите полный путь к файлу на компьютере. Далее выполните:

```
git clone https://github.com/Azure-Samples/storage-java-manage-storage-accounts.git
cd storage-java-manage-storage-accounts
mvn clean compile exec:java
```

Просмотрите [полный пример кода на GitHub](https://github.com/Azure-Samples/storage-java-manage-storage-accounts).

## <a name="authenticate-with-azure"></a>Проверка подлинности с помощью Azure

[!INCLUDE [auth-include](includes/java-auth-include.md)] 

## <a name="create-a-storage-account"></a>Создание учетной записи хранения

```java
// create a new storage account
StorageAccount storageAccount = azure.storageAccounts().define(storageAccountName)
                    .withRegion(Region.US_EAST)
                    .withNewResourceGroup(rgName)
                    .create();
```

Указанное имя хранилища должно быть уникальным в Azure и содержать только строчные буквы и цифры. Для этой учетной записи по умолчанию используется профиль производительности и репликации [Standard_GRS](https://docs.microsoft.com/azure/storage/storage-redundancy#geo-redundant-storage).

## <a name="list-keys-in-a-storage-account"></a>Получение списка ключей в учетной записи хранения
```java
// list the name and value for each access key in the storage account
List<StorageAccountKey> storageAccountKeys = storageAccount.getKeys();
for(StorageAccountKey key : storageAccountKeys)    {
    System.out.println("Key name: " + key.keyName() + " with value "+ key.value());
}
```

Для каждой учетной записи хранения Azure предоставляется два ключа, что позволяет повторно создать один ключ, а для доступа к хранилищу использовать другой.

## <a name="regenerate-a-key-in-a-storage-account"></a>Повторное создание ключа в учетной записи хранения

```java
// regenerate the first key in a storage account and return an updated list of keys 
List<StorageAccountKey> updatedStorageAccountKeys =
    storageAccount.regenerateKey(storageAccountKeys.get(0).keyName());
```

После повторного создания ключа вам потребуется обновить ключ во всех ресурсах и приложениях Azure.

## <a name="list-all-storage-accounts-in-a-resource-group"></a>Получение списка всех учетных записей хранения в группе ресурсов
```java
// get a list of accounts in a resource group , log info about each one
List<StorageAccount> accounts = azure.storageAccounts().listByResourceGroup(rgName);
for (StorageAccount sa : accounts) {
    System.out.println("Storage Account " + sa.name() + " created @ " + sa.creationTime());
}
```

На странице [com.microsoft.azure.management.storage.StorageAccount](https://docs.microsoft.com/java/api/com.microsoft.azure.management.storage._storage_account) доступен набор полезных методов для проверки настроек учетной записи хранения.

## <a name="delete-a-storage-account"></a>Удаление учетной записи хранения
```java
// delete by ID when you already have a storage account object
azure.storageAccounts().deleteById(storageAccount.id());

// delete by resource group and account name if you don't have an account object
azure.storageAccounts().deleteByResourceGroup(rgName,accountName);
```

> [!NOTE]
> Учетные записи хранения с используемыми образами дисков, которые подключены к виртуальным машинам, или дисками, которые используются другими артефактами, невозможно удалить с помощью этих методов. Отключите хранилище от этих ресурсов, чтобы удалить учетную запись.

## <a name="sample-explanation"></a>Описание примера

[Пример кода на GitHub](https://github.com/Azure-Samples/storage-java-manage-storage-accounts) позволяет:

- создать учетную запись хранения;
- считывать и повторно создавать ключи доступа;
- выводить список всех учетных записей хранения в группе ресурсов;
- удалить учетную запись хранения. 

| Класс, используемый в примере | Примечания
|-------|-------|
| [StorageAccount](https://docs.microsoft.com/java/api/com.microsoft.azure.management.storage._storage_account)  | Представляет учетную запись хранения Azure. Чтобы получить сведения об учетной записи хранения, используйте методы в классе.
| [StorageAccountKey](https://docs.microsoft.com/java/api/com.microsoft.azure.management.storage._storage_account_key) | `StorageAccount.getKeys()` возвращает ключи учетной записи хранения. Используйте методы `regenerateKey` в `StorageAccount`, чтобы обновить ключи.

## <a name="next-steps"></a>Дополнительная информация

[!INCLUDE [next-steps](includes/java-next-steps.md)]