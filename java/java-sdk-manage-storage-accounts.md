---
title: Zarządzanie kontami magazynu platformy Azure za pomocą języka Java | Microsoft Docs
description: Przykładowy kod umożliwiający zarządzanie kontami magazynu platformy Azure przy użyciu zestawu Azure SDK dla języka Java
author: rloutlaw
ms.assetid: 49be8b66-3b56-4c10-8f14-9d326d815cb4
ms.topic: article
ms.date: 3/30/2017
ms.reviewer: asirveda
ms.openlocfilehash: 88298088099ed61c97eb6cb4a900c2ce55f5c3f0
ms.sourcegitcommit: 6fa28ea675ae17ffb9ac825415e2e26a3dfe7107
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2020
ms.locfileid: "77002433"
---
# <a name="manage-azure-storage-accounts-from-your-java-applications"></a>Zarządzanie kontami magazynu platformy Azure z poziomu aplikacji Java

[Ten przykład](https://github.com/Azure-Samples/storage-java-manage-storage-accounts) obejmuje tworzenie konta usługi [Azure Storage](https://docs.microsoft.com/azure/storage/storage-introduction) i korzystanie z kluczy dostępu do konta za pomocą [bibliotek zarządzania Java](https://github.com/Azure/azure-sdk-for-java). 

## <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej

Utwórz [plik uwierzytelniania](https://github.com/Azure/azure-sdk-for-java/blob/master/AUTH.md) i ustaw zmienną środowiskową `AZURE_AUTH_LOCATION` na pełną ścieżkę do pliku na komputerze. Następnie uruchom polecenie:

```
git clone https://github.com/Azure-Samples/storage-java-manage-storage-accounts.git
cd storage-java-manage-storage-accounts
mvn clean compile exec:java
```

Zapoznaj się z [kompletnym przykładowym kodem w witrynie GitHub](https://github.com/Azure-Samples/storage-java-manage-storage-accounts).

## <a name="authenticate-with-azure"></a>Uwierzytelnianie na platformie Azure

[!INCLUDE [auth-include](includes/java-auth-include.md)] 

## <a name="create-a-storage-account"></a>Tworzenie konta magazynu

```java
// create a new storage account
StorageAccount storageAccount = azure.storageAccounts().define(storageAccountName)
                    .withRegion(Region.US_EAST)
                    .withNewResourceGroup(rgName)
                    .create();
```

Nazwa magazynu musi być unikatowa na platformie Azure i może zawierać tylko cyfry i małe litery. Domyślny profil wydajności i replikacji używany dla tego konta to [Standard_GRS](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs).

## <a name="list-keys-in-a-storage-account"></a>Wyświetlanie listy kluczy na koncie magazynu
```java
// list the name and value for each access key in the storage account
List<StorageAccountKey> storageAccountKeys = storageAccount.getKeys();
for(StorageAccountKey key : storageAccountKeys)    {
    System.out.println("Key name: " + key.keyName() + " with value "+ key.value());
}
```

Dla każdego konta magazynu platformy Azure udostępniane są dwa klucze, dzięki czemu można ponownie wygenerować jeden klucz, zachowując dostęp do magazynu przy użyciu drugiego klucza.

## <a name="regenerate-a-key-in-a-storage-account"></a>Ponowne generowanie klucza na koncie magazynu

```java
// regenerate the first key in a storage account and return an updated list of keys 
List<StorageAccountKey> updatedStorageAccountKeys =
    storageAccount.regenerateKey(storageAccountKeys.get(0).keyName());
```

Po wygenerowaniu nowego klucza należy przy jego użyciu zaktualizować wszystkie zasoby i aplikacje platformy Azure.

## <a name="list-all-storage-accounts-in-a-resource-group"></a>Wyświetlanie listy wszystkich kont magazynu w grupie zasobów
```java
// get a list of accounts in a resource group , log info about each one
List<StorageAccount> accounts = azure.storageAccounts().listByResourceGroup(rgName);
for (StorageAccount sa : accounts) {
    System.out.println("Storage Account " + sa.name() + " created @ " + sa.creationTime());
}
```

Interfejs [com.microsoft.azure.management.storage.StorageAccount](https://docs.microsoft.com/java/api/com.microsoft.azure.management.storage.storageaccount) udostępnia zestaw przydatnych metod do sprawdzania konfiguracji konta magazynu.

## <a name="delete-a-storage-account"></a>Usuwanie konta magazynu
```java
// delete by ID when you already have a storage account object
azure.storageAccounts().deleteById(storageAccount.id());

// delete by resource group and account name if you don't have an account object
azure.storageAccounts().deleteByResourceGroup(rgName,accountName);
```

> [!NOTE]
> Usunięcie kont magazynu z aktualnie używanymi obrazami dysków połączonymi z maszynami wirtualnymi lub dyskami używanymi przez inne artefakty za pomocą tych metod może okazać się niemożliwe. Przed usunięciem konta odłącz magazyn od tych zasobów.

## <a name="sample-explanation"></a>Wyjaśnienie działania przykładu

[Kod przykładowy w witrynie GitHub](https://github.com/Azure-Samples/storage-java-manage-storage-accounts):

- tworzy konto magazynu,
- odczytuje i ponownie generuje klucze dostępu,
- wyświetla listę wszystkich kont magazynu w grupie zasobów,
- usuwa konto magazynu. 

| Klasa używana w przykładzie | Uwagi
|-------|-------|
| [StorageAccount](https://docs.microsoft.com/java/api/com.microsoft.azure.management.storage.storageaccount)  | Reprezentacja konta usługi Azure Storage. Metody klasy umożliwiają uzyskiwanie informacji o koncie magazynu.
| [StorageAccountKey](https://docs.microsoft.com/java/api/com.microsoft.azure.management.storage.storageaccountkey) | Metoda `StorageAccount.getKeys()` zwraca klucze konta magazynu. Aby zaktualizować klucze, użyj metod `regenerateKey` interfejsu `StorageAccount`.

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [next-steps](includes/java-next-steps.md)]