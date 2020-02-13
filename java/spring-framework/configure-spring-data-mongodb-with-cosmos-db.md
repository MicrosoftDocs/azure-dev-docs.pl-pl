---
title: Jak używać interfejsu API usługi MongoDB w rozwiązaniu Spring Data z usługą Azure Cosmos DB
description: Dowiedz się, jak używać interfejsu API usługi MongoDB w rozwiązaniu Spring Data z usługą Azure Cosmos DB.
services: cosmos-db
documentationcenter: java
ms.date: 12/19/2018
ms.service: cosmos-db
ms.tgt_pltfrm: multiple
ms.topic: article
ms.openlocfilehash: 0284f89f6a37497709947649fba3b1284416a95c
ms.sourcegitcommit: b3b7dc6332c0532f74d210b2a5cab137e38a6750
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2019
ms.locfileid: "76999718"
---
# <a name="how-to-use-spring-data-mongodb-api-with-azure-cosmos-db"></a>Jak używać interfejsu API usługi MongoDB w rozwiązaniu Spring Data z usługą Azure Cosmos DB

## <a name="overview"></a>Omówienie

W tym artykule przedstawiono tworzenie przykładowej aplikacji, która używa rozwiązania [Spring Data] do przechowywania i pobierania informacji przy użyciu [interfejsu API usługi MongoDB w usłudze Azure Cosmos DB](/azure/cosmos-db/mongodb-introduction).

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym artykule, wymagane są następujące elementy:

* Subskrypcja platformy Azure. Jeśli nie masz jeszcze subskrypcji platformy Azure, możesz uaktywnić [korzyści dla subskrybentów MSDN] lub utworzyć [bezpłatne konto platformy Azure].
* Obsługiwany zestaw Java Development Kit (JDK). Aby uzyskać więcej informacji na temat zestawów JDK dostępnych do użycia podczas tworzenia aplikacji na platformie Azure, zobacz <https://aka.ms/azure-jdks>.
* Narzędzie [Apache Maven](http://maven.apache.org/) w wersji 3.0 lub nowszej.
* Klient usługi [Git](https://git-scm.com/downloads).

## <a name="create-an-azure-cosmos-db-account"></a>Tworzenie konta usługi Azure Cosmos DB

### <a name="create-a-cosmos-db-account-using-the-azure-portal"></a>Tworzenie konta usługi Cosmos DB przy użyciu witryny Azure Portal

> [!NOTE]
> 
> Więcej szczegółowych informacji na temat tworzenia kont usługi Azure Cosmos DB można znaleźć w [dokumentacji usługi Azure Cosmos DB](/azure/cosmos-db/).

1. Przejdź do witryny Azure Portal pod adresem <https://portal.azure.com/> i zaloguj się.

1. Kliknij pozycję **+ Utwórz zasób**, pozycję **Bazy danych**, a następnie pozycję **Azure Cosmos DB**.

   ![Tworzenie konta usługi Azure Cosmos DB][COSMOSDB01]

1. Wprowadź następujące informacje:

   - **Subskrypcja**: określ subskrypcję platformy Azure, której chcesz użyć.
   - **Grupa zasobów**: określ, czy chcesz utworzyć nową grupę zasobów lub wybierz istniejącą grupę.
   - **Nazwa konta**: wybierz unikatową nazwę konta usługi Cosmos DB — ta wartość zostanie użyta do utworzenia w pełni kwalifikowanej nazwy domeny, takiej jak *wingtiptoysmongodb.documents.azure.com*.
   - **API**: na potrzeby tego samouczka określ `Azure Cosmos DB for MongoDB API`.
   - **Lokalizacja**: określ najbliższy region geograficzny dla bazy danych.

   ![Określanie ustawień konta usługi Cosmos DB][COSMOSDB02]
   
1. Po wprowadzeniu wszystkich powyższych informacji kliknij przycisk **Przeglądanie + tworzenie**.

1. Jeśli na stronie przeglądu wszystko wygląda poprawnie, kliknij przycisk **Utwórz**.

   ![Przegląd ustawień konta usługi Cosmos DB][COSMOSDB03]

### <a name="retrieve-the-connection-string-for-your-azure-cosmos-db-account"></a>Pobieranie parametrów połączenia dla konta usługi Azure Cosmos DB

1. Przejdź do witryny Azure Portal pod adresem <https://portal.azure.com/> i zaloguj się.

1. Kliknij pozycję **Wszystkie zasoby**, a następnie kliknij utworzone teraz konto usługi Azure Cosmos DB.

1. Kliknij pozycję **Parametry połączenia** i skopiuj wartość pola **Podstawowe parametry połączenia**. Użyjesz tej wartości później w celu skonfigurowania aplikacji.

   ![Pobieranie parametrów połączenia usługi Cosmos DB][COSMOSDB06]

## <a name="configure-the-sample-application"></a>Konfigurowanie przykładowej aplikacji

1. Otwórz powłokę poleceń i sklonuj przykładowy projekt przy użyciu polecenia git, jak w poniższym przykładzie:

   ```shell
   git clone https://github.com/spring-guides/gs-accessing-data-mongodb.git
   ```

1. Utwórz katalog *resources* (zasoby) w katalogu *&lt;katalog główny projektu&gt;/complete/src/main* projektu przykładowego, a następnie utwórz plik *application.properties* w katalogu *resources*.

1. Otwórz plik *application.properties* w edytorze tekstów, a następnie dodaj następujące wiersze w pliku i zastąp przykładowe wartości odpowiednimi wartościami skopiowanymi wcześniej:

   ```yaml
   spring.data.mongodb.database=wingtiptoysmongodb
   spring.data.mongodb.uri=mongodb://wingtiptoysmongodb:AbCdEfGhIjKlMnOpQrStUvWxYz==@wingtiptoysmongodb.documents.azure.com:10255/?ssl=true&replicaSet=globaldb
   ```
   Gdzie:

   | Parametr | Opis |
   |---|---|
   | `spring.data.mongodb.database` | Określa nazwę konta usługi Cosmos DB z wcześniejszej części tego artykułu. |
   | `spring.data.mongodb.uri` | Określa **podstawowe parametry połączenia** z wcześniejszej części tego artykułu. |

1. Zapisz i zamknij plik *application.properties*.

## <a name="package-and-test-the-sample-application"></a>Pakowanie i testowanie aplikacji przykładowej

Aby skompilować aplikację, przejdź do katalogu */gs-accessing-data-mongodb/complete*, który zawiera plik pom.xml.

1. Skompiluj przykładową aplikację przy użyciu narzędzia Maven i skonfiguruj narzędzie Maven w celu pomijania testów, na przykład:

   ```shell
   mvn clean package -DskipTests
   ```

1. Uruchom aplikację przykładową, na przykład:

   ```shell
   java -jar target/accessing-data-mongodb-0.0.1-SNAPSHOT.jar
   ```
    
   Aplikacja powinna zwracać wartości podobne do następujących:

   ```json
   Customers found with findAll():
   -------------------------------
   Customer[id=5c1b4ae4d0b5080ac105cc13, firstName='Alice', lastName='Smith']
   Customer[id=5c1b4ae4d0b5080ac105cc14, firstName='Bob', lastName='Smith']
   
   Customer found with findByFirstName('Alice'):
   --------------------------------
   Customer[id=5c1b4ae4d0b5080ac105cc13, firstName='Alice', lastName='Smith']
   Customers found with findByLastName('Smith'):
   --------------------------------
   Customer[id=5c1b4ae4d0b5080ac105cc13, firstName='Alice', lastName='Smith']
   Customer[id=5c1b4ae4d0b5080ac105cc14, firstName='Bob', lastName='Smith']
   ```

## <a name="summary"></a>Podsumowanie

W tym samouczku utworzono przykładową aplikację Java, która używa rozwiązania Spring Data do przechowywania i pobierania informacji przy użyciu interfejsu API usługi MongoDB w usłudze Azure Cosmos DB.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat oprogramowania Spring i platformy Azure, przejdź do centrum dokumentacji dotyczącej oprogramowania Spring na platformie Azure.

> [!div class="nextstepaction"]
> [Oprogramowanie Spring na platformie Azure](/azure/java/spring-framework)

### <a name="additional-resources"></a>Dodatkowe zasoby

Aby uzyskać więcej informacji na temat używania platformy Azure z językiem Java, zapoznaj się z tematem [Azure dla deweloperów języka Java] i [Praca z usługą Azure DevOps i językiem Java].

<!-- URL List -->

[Azure dla deweloperów języka Java]: /azure/java/
[Bezpłatne konto platformy Azure]: https://azure.microsoft.com/pricing/free-trial/
[Praca z usługą Azure DevOps i językiem Java]: /azure/devops/
[Korzyści dla subskrybentów MSDN]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Data]: https://spring.io/projects/spring-data
[Spring Initializr]: https://start.spring.io/
[Spring Framework]: https://spring.io/

<!-- IMG List -->

[COSMOSDB01]: media/configure-spring-data-mongodb-with-cosmos-db/create-cosmos-db-01.png
[COSMOSDB02]: media/configure-spring-data-mongodb-with-cosmos-db/create-cosmos-db-02.png
[COSMOSDB03]: media/configure-spring-data-mongodb-with-cosmos-db/create-cosmos-db-03.png
[COSMOSDB04]: media/configure-spring-data-mongodb-with-cosmos-db/create-cosmos-db-04.png
[COSMOSDB06]: media/configure-spring-data-mongodb-with-cosmos-db/create-cosmos-db-06.png
