---
title: Zarządzanie elastycznymi pulami usługi SQL Database za pomocą języka Java | Microsoft Docs
description: Przykładowy kod umożliwiający tworzenie i konfigurowanie baz danych Azure SQL Database przy użyciu zestawu Azure SDK dla języka Java
author: rloutlaw
ms.assetid: 9b461de8-46bc-4650-8e9e-59531f4e2a53
ms.topic: article
ms.date: 3/30/2017
ms.reviewer: asirveda
ms.openlocfilehash: 535490109824f0caf9e7d041114ada9a507b41ea
ms.sourcegitcommit: 6fa28ea675ae17ffb9ac825415e2e26a3dfe7107
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2020
ms.locfileid: "77002450"
---
# <a name="manage-azure-sql-databases-in-elastic-pools-from-your-java-applications"></a>Zarządzanie bazami danych Azure SQL Database w elastycznych pulach z poziomu aplikacji Java

[Ten przykład](https://github.com/Azure-Samples/sql-database-java-manage-sql-dbs-in-elastic-pool) tworzy serwer baz danych SQL z [elastyczną pulą](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool), który umożliwia skalowanie zasobów dla wielu baz danych w ramach jednego planu i zarządzanie tymi zasobami.

## <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej

Utwórz [plik uwierzytelniania](https://github.com/Azure/azure-sdk-for-java/blob/master/AUTH.md) i ustaw zmienną środowiskową `AZURE_AUTH_LOCATION` na pełną ścieżkę do pliku na komputerze. Następnie uruchom polecenie:

```
git clone https://github.com/Azure-Samples/sql-database-java-manage-sql-dbs-in-elastic-pool
cd sql-database-java-manage-sql-dbs-in-elastic-pool
mvn clean compile exec:java
```

[Zapoznaj się z kompletnym przykładowym kodem w witrynie GitHub](https://github.com/Azure-Samples/sql-database-java-manage-sql-dbs-in-elastic-pool)

## <a name="authenticate-with-azure"></a>Uwierzytelnianie na platformie Azure

[!INCLUDE [auth-include](includes/java-auth-include.md)]

## <a name="create-a-sql-database-server-with-an-elastic-pool"></a>Tworzenie serwera baz danych SQL z elastyczną pulą

```java
SqlServer sqlServer = azure.sqlServers().define(sqlServerName)
                    .withRegion(Region.US_EAST)
                    .withNewResourceGroup(rgName)
                    .withAdministratorLogin(administratorLogin)
                    .withAdministratorPassword(administratorPassword)
                    // Use ElasticPoolEditions.STANDARD as the edition
                    // and create two databases
                    .withNewElasticPool(elasticPoolName, ElasticPoolEditions.STANDARD, 
                        database1Name, database2Name)
                    .create();
```

Zobacz [dokumentację klasy ElasticPoolEditions](https://docs.microsoft.com/java/api/com.microsoft.azure.management.sql.elasticpooleditions), aby zapoznać się z wartościami w bieżącej wersji. Zapoznaj się z [dokumentacją elastycznych puli bazy danych SQL](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool), aby porównać cechy zasobów w danej wersji. 

## <a name="change-database-transaction-unit-dtu-settings-in-an-elastic-pool"></a>Zmiana ustawień jednostki transakcji bazy danych (DTU) w elastycznej puli

```java
// Set an pool of 200 eDTUs to share between the databases
// and ensure that a database always has 10 DTUs available but never uses more than 50
elasticPool = elasticPool.update()
                    .withDtu(200)
                    .withDatabaseDtuMin(10)
                    .withDatabaseDtuMax(50)
                    .apply();
```

Zapoznaj się z [dokumentacją jednostek DTU i eDTU](https://docs.microsoft.com/azure/sql-database/sql-database-what-is-a-dtu), aby dowiedzieć się więcej na temat alokowania zasobów do baz danych SQL.

## <a name="create-a-new-database-and-add-it-to-an-elastic-pool"></a>Tworzenie nowej bazy danych i dodawanie jej do elastycznej puli

```java
// Add a newly created database to the elastic pool
SqlDatabase anotherDatabase = sqlServer.databases().define(anotherDatabaseName).create();
elasticPool.update().withExistingDatabase(anotherDatabase).apply();            
```

Interfejs API tworzy wystąpienie `anotherDatabase` w [warstwie S0](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers) w pierwszej instrukcji. Przeniesienie wystąpienia `anotherDatabase` do elastycznej puli powoduje przypisanie zasobów bazy danych na podstawie ustawień puli.

## <a name="remove-a-database-from-an-elastic-pool"></a>Usuwanie bazy danych z elastycznej puli
```java
// Assign an edition since the database resources are no longer managed in the pool 
anotherDatabase = anotherDatabase.update()
                     .withoutElasticPool()
                     .withEdition(DatabaseEditions.STANDARD)
                     .apply();
```

Zapoznaj się z [dokumentacją klasy DatabaseEditions](https://docs.microsoft.com/java/api/com.microsoft.azure.management.sql.databaseeditions), aby uzyskać informacje o wartościach przekazywanych do metody `withEdition()`.

## <a name="list-current-database-activities-in-an-elastic-pool"></a>Wyświetlanie listy bieżących działań bazy danych w elastycznej puli
```java
// get a list of in-flight elastic operations on databases in the pool and log them 
for (ElasticPoolDatabaseActivity databaseActivity : elasticPool.listDatabaseActivities()) {
    System.out.println("Database " + databaseActivity.databaseName() 
        + " performing operation " + databaseActivity.operation() + 
        " with objective " + databaseActivity.requestedServiceObjective());
}
```

Działania bazy danych obejmują przenoszenie bazy danych do elastycznej puli lub z elastycznej puli oraz aktualizowanie baz danych w elastycznej puli.


## <a name="list-databases-in-an-elastic-pool"></a>Wyświetlanie listy baz danych w elastycznej puli
```java
// Log a list of databases in the elastic pool 
for (SqlDatabase databaseInServer : elasticPool.listDatabases()) {
    System.out.println(databaseInServer.name());
}
```

Zapoznaj się z metodami interfejsu [com.microsoft.azure.management.sql.SqlDatabase](https://docs.microsoft.com/java/api/com.microsoft.azure.management.sql.sqldatabase), które umożliwiają wykonywanie zapytań na bazach danych.

## <a name="delete-an-elastic-pool"></a>Usuwanie elastycznej puli
```java
sqlServer.elasticPools().delete(elasticPoolName);
```

Przed usunięciem elastycznej puli musisz ją opróżnić.

## <a name="sample-code-summary"></a>Podsumowanie przykładowego kodu

W tym przykładzie tworzony jest serwer SQL z dwiema bazami danych zarządzanymi w jednej elastycznej puli. Limity zasobów elastycznej puli są aktualizowane, a następnie do puli dodawane są kolejne bazy danych. Następnie odczytywane są informacje o konfiguracji i stanie elastycznej puli. 

Przed zamknięciem przykładu usuwane są wszystkie utworzone zasoby.

| Klasa używana w przykładzie | Uwagi |
|-------|-------|
| [SqlServer](https://docs.microsoft.com/java/api/com.microsoft.azure.management.sql.sqlserver) | Serwer SQL DB na platformie Azure utworzony przez płynny łańcuch `azure.sqlServers().define()...create()`. Udostępnia metody służące do tworzenia elastycznych puli i baz danych oraz korzystania z nich. 
| [SqlDatabase](https://docs.microsoft.com/java/api/com.microsoft.azure.management.sql.sqldatabase) | Obiekt po stronie klienta reprezentujący bazę danych SQL. Utworzony za pomocą łańcucha `sqlServer().define()...create()`. 
| [DatabaseEditions](https://docs.microsoft.com/java/api/com.microsoft.azure.management.sql.databaseeditions) | Stałe pola statyczne używane do ustawiania zasobów bazy danych podczas tworzenia bazy danych poza elastyczną pulą lub przenoszenia bazy danych z elastycznej puli.  
| [SqlElasticPool](https://docs.microsoft.com/java/api/com.microsoft.azure.management.sql.sqlelasticpool) | Utworzona w sekcji `withNewElasticPool()` płynnego łańcucha, w której zostało utworzone wystąpienie SqlServer na platformie Azure. Udostępnia metody do ustawiania limitów zasobów baz danych uruchomionych w elastycznej puli oraz limitów samej puli. 
| [ElasticPoolEditions](https://docs.microsoft.com/java/api/com.microsoft.azure.management.sql.elasticpooleditions) | Klasa stałych pól definiujących zasoby dostępne dla elastycznej puli. Aby uzyskać szczegółowe informacje na temat warstwy, zobacz [dokumentację elastycznych pul bazy danych SQL](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool). 
| [ElasticPoolDatabaseActivity](https://docs.microsoft.com/java/api/com.microsoft.azure.management.sql.elasticpooldatabaseactivity) | Pobierana za pomocą metody `SqlElasticPool.listDatabaseActivities()`. Każdy obiekt tego typu reprezentuje działanie wykonywane na bazie danych w elastycznej puli.
| [ElasticPoolActivity](https://docs.microsoft.com/java/api/com.microsoft.azure.management.sql.elasticpoolactivity) | Pobierana w postaci listy za pomocą metody `SqlElasticPool.listActivities()`. Każdy obiekt na liście reprezentuje działanie wykonywane na elastycznej puli (a nie na bazach danych w tej puli).

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [next-steps](includes/java-next-steps.md)]