---
title: Uwierzytelnianie za pomocą bibliotek zarządzania Azure dla języka Java
description: Uwierzytelnianie za pomocą jednostki usługi w bibliotekach zarządzania platformy Azure dla języka Java
keywords: Azure, Java, SDK, API, Maven, Gradle, uwierzytelnianie, active directory, jednostka usługi
author: rloutlaw
ms.date: 04/16/2017
ms.topic: article
ms.service: multiple
ms.assetid: 10f457e3-578b-4655-8cd1-51339226ee7d
ms.custom: seo-java-september2019
ms.openlocfilehash: 868849e9df89138d943421886961821d4d679db9
ms.sourcegitcommit: 5c65d22b5203b0c17806463d349a6ede93a99fa0
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/12/2019
ms.locfileid: "77000271"
---
# <a name="authenticate-with-the-azure-libraries-for-java"></a>Uwierzytelnianie za pomocą bibliotek Azure dla języka Java

W tym artykule przedstawiono sposób uwierzytelniania za pomocą bibliotek platformy Azure dla języka Java.

[!INCLUDE [chrome-note](includes/chrome-note.md)]

## <a name="connect-to-services-with-connection-strings"></a>Łączenie się z usługami przy użyciu parametrów połączenia

Większość bibliotek usług platformy Azure używa parametrów połączenia lub klucza bezpiecznego do uwierzytelniania. Na przykład SQL Database zawiera informacje o nazwie użytkownika i haśle w parametrach połączenia JDBC:

```java
String url = "jdbc:sqlserver://myazuredb.database.windows.net:1433;" +
        "database=testjavadb;" +
        "user=myazdbuser;" +
        "password=myazdbpass;" +
        "encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;";
        Connection conn = DriverManager.getConnection(url);
```

Usługa Azure Storage używa klucza magazynu do autoryzowania aplikacji:

```java
final String storageConnection = "DefaultEndpointsProtocol=https;"
        + "AccountName=" + storageName
        + ";AccountKey=" + storageKey
        + ";EndpointSuffix=core.windows.net";
```

Parametry połączenia usługi są używane do uwierzytelniania w innych usługach platformy Azure, takich jak [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/sql-api-java-application#UseService), [Redis Cache](https://docs.microsoft.com/azure/redis-cache/cache-java-get-started) i [Service Bus](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-java-how-to-use-queues). Parametry połączenia można uzyskać w portalu Azure lub w wierszu polecenia.  Można również użyć bibliotek zarządzania platformy Azure dla języka Java do wysyłania zapytań do zasobów w celu tworzenia parametrów połączenia w kodzie.

Na przykład ten kod używa bibliotek zarządzania do tworzenia parametrów połączenia konta magazynu:

```java
// create a new storage account
StorageAccount storage = azure.storageAccounts().getByResourceGroup("myResourceGroup","myStorageAccount");

// create a storage container to hold the file
List<StorageAccountKey> keys = storage.getKeys();
final String storageConnection = "DefaultEndpointsProtocol=https;"
        + "AccountName=" + storage.name()
        + ";AccountKey=" + keys.get(0).value()
        + ";EndpointSuffix=core.windows.net";
```

Inne biblioteki wymagają, aby aplikacja działała z [jednostką usługi](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-objects), która autoryzuje aplikację do działania z udzielonymi poświadczeniami. Ta konfiguracja jest podobna do kroków uwierzytelniania opartego na obiektach dla wymienionej poniżej biblioteki zarządzania.

<a name="mgmt-auth"></a>

##  <a name="authenticate-with-the-azure-management-libraries-for-java"></a>Uwierzytelnianie za pomocą bibliotek zarządzania Azure dla języka Java

Dostępne są dwie opcje uwierzytelniania aplikacji na platformie Azure z użyciem bibliotek zarządzania Java w celu tworzenia zasobów i zarządzania nimi.

### <a name="authenticate-with-an-applicationtokencredentials-object"></a>Uwierzytelnianie za pomocą obiektu ApplicationTokenCredentials

Utwórz wystąpienie obiektu `ApplicationTokenCredentials`, aby podać poświadczenia jednostki usługi do obiektu platformy `Azure` najwyższego poziomu w kodzie.

```java
import com.microsoft.azure.credentials.ApplicationTokenCredentials;
import com.microsoft.azure.AzureEnvironment;

// ...

ApplicationTokenCredentials credentials = new ApplicationTokenCredentials(client,
        tenant,
        key,
        AzureEnvironment.AZURE);

Azure azure = Azure
        .configure()
        .withLogLevel(LogLevel.NONE)
        .authenticate(credentials)
        .withDefaultSubscription();
```

`client`, `tenant` i `key` są tymi samymi wartościami jednostki usługi, używanymi podczas [uwierzytelniania opartego na plikach](#mgmt-file). Wartość `AzureEnvironment.AZURE` tworzy poświadczenia w chmurze publicznej Azure. Zmień ją na inną wartość, jeśli chcesz uzyskać dostęp do innej chmury (na przykład `AzureEnvironment.AZURE_GERMANY`).

 Odczytaj wartości jednostki usługi ze zmiennych środowiskowych lub magazynu zarządzania wpisami tajnymi, takiego jak [Key Vault](/azure/key-vault/key-vault-whatis). Należy unikać ustawiania tych wartości jako ciągów zwykłego tekstu w kodzie, aby zapobiec przypadkowemu uwidacznianiu poświadczeń w historii kontroli wersji.

<a name="mgmt-file"></a>

### <a name="file-based-authentication-preview"></a>Uwierzytelnianie oparte na plikach (wersja zapoznawcza)

Najprostszym sposobem uwierzytelniania jest utworzenie pliku właściwości, który zawiera poświadczenia dla [jednostki usługi platformy Azure](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-objects), przy użyciu następującego formatu:

```text
# sample management library properties file
subscription=########-####-####-####-############
client=########-####-####-####-############
key=XXXXXXXXXXXXXXXX
tenant=########-####-####-####-############
managementURI=https\://management.core.windows.net/
baseURL=https\://management.azure.com/
authURL=https\://login.windows.net/
graphURL=https\://graph.windows.net/
```

- subscription: użyj wartości *id* z `az account show` w interfejsie wiersza polecenia platformy Azure 2.0.
- client: użyj wartości *appId* z danych wyjściowych pobranych z jednostki usługi utworzonej w celu uruchomienia aplikacji. Jeśli nie masz jednostki usługi swojej aplikacji, [utwórz ją za pomocą interfejsu wiersza polecenia platformy Azure 2.0](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli).
- key: użyj wartości *password* z jednostki usługi utworzonej z danych wyjściowych w interfejsie wiersza polecenia
- tenant: użyj wartości *tenant* z jednostki usługi utworzonej z danych wyjściowych w interfejsie wiersza polecenia

Zapisz ten plik w bezpiecznej lokalizacji w systemie, w której kod może go odczytać. Ustaw zmienną środowiskową na pełną ścieżkę do pliku w powłoce:

```bash
export AZURE_AUTH_LOCATION=/Users/raisa/azureauth.properties
```

Utwórz obiekt `Azure` punktu wejścia, aby rozpocząć pracę z bibliotekami. Odczytaj lokalizację pliku właściwości za pomocą zmiennej środowiskowej.

```java
// pull in the location of the authentication properties file from the environment
final File credFile = new File(System.getenv("AZURE_AUTH_LOCATION"));

Azure azure = Azure
        .configure()
        .withLogLevel(LogLevel.NONE)
        .authenticate(credFile)
        .withDefaultSubscription();
```
