---
title: Jak używać interfejsu API Apache Cassandra w rozwiązaniu Spring Data z usługą Azure Cosmos DB
description: Dowiedz się, jak używać interfejsu API Apache Cassandra w rozwiązaniu Spring Data z usługą Azure Cosmos DB.
services: cosmos-db
documentationcenter: java
ms.date: 12/19/2018
ms.service: cosmos-db
ms.tgt_pltfrm: multiple
ms.topic: article
ms.openlocfilehash: dee7a5b21c1b0273f82ead32a3f18a609a36730c
ms.sourcegitcommit: b3b7dc6332c0532f74d210b2a5cab137e38a6750
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2019
ms.locfileid: "76999725"
---
# <a name="how-to-use-spring-data-apache-cassandra-api-with-azure-cosmos-db"></a>Jak używać interfejsu API Apache Cassandra w rozwiązaniu Spring Data z usługą Azure Cosmos DB

W tym artykule przedstawiono tworzenie przykładowej aplikacji, która używa rozwiązania [Spring Data] do przechowywania i pobierania informacji przy użyciu [interfejsu API Apache Cassandra usługi Azure Cosmos DB](/azure/cosmos-db/cassandra-introduction).

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym artykule, wymagane są:

* Subskrypcja platformy Azure — jeśli nie masz jeszcze subskrypcji platformy Azure, możesz aktywować [korzyści dla subskrybentów MSDN] lub utworzyć [bezpłatne konto platformy Azure].
* Obsługiwany zestaw Java Development Kit (JDK). Aby uzyskać więcej informacji na temat zestawów JDK dostępnych do użycia podczas tworzenia aplikacji na platformie Azure, zobacz <https://aka.ms/azure-jdks>.
* Narzędzie [Apache Maven](http://maven.apache.org/), wersja 3.0 lub nowsza.
* [Curl](https://curl.haxx.se/) lub podobne narzędzie HTTP do testowania funkcjonalności.
* Klient usługi [Git](https://git-scm.com/downloads).

## <a name="create-an-azure-cosmos-db-account"></a>Tworzenie konta usługi Azure Cosmos DB

Poniższa procedura służy do tworzenia i konfigurowania konta usługi Cosmos w witrynie Azure Portal.

### <a name="create-a-cosmos-db-account-using-the-azure-portal"></a>Tworzenie konta usługi Cosmos DB przy użyciu witryny Azure Portal

> [!NOTE]
> 
> Więcej szczegółowych informacji na temat tworzenia kont usługi Azure Cosmos DB można znaleźć w [dokumentacji usługi Azure Cosmos DB](/azure/cosmos-db/).

1. Przejdź do witryny Azure Portal pod adresem <https://portal.azure.com/> i zaloguj się.

1. Kliknij pozycję **+ Utwórz zasób**, pozycję **Bazy danych**, a następnie pozycję **Azure Cosmos DB**.

   ![Tworzenie konta usługi Azure Cosmos DB][COSMOSDB01]

1. Wprowadź następujące informacje:

   - **Subskrypcja**: określ subskrypcję platformy Azure, która ma zostać użyta.
   - **Grupa zasobów**: określ, czy chcesz utworzyć nową grupę zasobów lub wybierz istniejącą grupę.
   - **Nazwa konta**: wybierz unikatową nazwę konta usługi Cosmos DB — ta wartość zostanie użyta do utworzenia w pełni kwalifikowanej nazwy domeny, takiej jak *wingtiptoyscassandra.documents.azure.com*.
   - **Interfejs API**: na potrzeby tego samouczka podaj `Cassandra`.
   - **Lokalizacja**: określ najbliższy region geograficzny bazy danych.

   ![Określanie ustawień konta usługi Cosmos DB][COSMOSDB02]
   
1. Po wprowadzeniu wszystkich powyższych informacji kliknij przycisk **Przeglądanie + tworzenie**.

1. Jeśli na stronie przeglądu wszystko wygląda poprawnie, kliknij przycisk **Utwórz**.

   ![Przegląd ustawień konta usługi Cosmos DB][COSMOSDB03]

Wdrożenie bazy danych potrwa klika minut.

### <a name="add-a-keyspace-to-your-azure-cosmos-db-account"></a>Dodawanie przestrzeni kluczy do konta usługi Azure Cosmos DB

1. Przejdź do witryny Azure Portal pod adresem <https://portal.azure.com/> i zaloguj się.

1. Kliknij pozycję **Wszystkie zasoby**, a następnie kliknij utworzone teraz konto usługi Azure Cosmos DB.

1. Kliknij pozycję **Eksplorator danych**, a następnie pozycję **Nowa przestrzeń kluczy**. Wprowadź unikatowy identyfikator w polu **Identyfikator przestrzeni kluczy**, a następnie kliknij przycisk **OK**.

   ![Tworzenie przestrzeni kluczy usługi Cosmos DB][COSMOSDB05]

### <a name="retrieve-the-connection-settings-for-your-azure-cosmos-db-account"></a>Pobieranie ustawień połączenia dla konta usługi Azure Cosmos DB

1. Przejdź do witryny Azure Portal pod adresem <https://portal.azure.com/> i zaloguj się.

1. Kliknij pozycję **Wszystkie zasoby**, a następnie kliknij utworzone teraz konto usługi Azure Cosmos DB.

1. Kliknij pozycję **Parametry połączenia** i skopiuj wartości pól **Punkt kontaktowy**, **Port**, **Nazwa użytkownika** i **Hasło podstawowe** — te wartości posłużą później do skonfigurowania aplikacji.

   ![Pobieranie ustawień połączenia z usługą Cosmos DB][COSMOSDB06]

## <a name="configure-the-sample-application"></a>Konfigurowanie przykładowej aplikacji

Poniższa procedura umożliwia skonfigurowanie aplikacji testowej.

1. Otwórz powłokę poleceń i sklonuj przykładowy projekt przy użyciu polecenia git, jak w poniższym przykładzie:

   ```shell
   git clone https://github.com/Azure-Samples/spring-data-cassandra-on-azure.git
   ```

1. Znajdź plik *application.properties* w katalogu *resources* projektu przykładowego lub utwórz ten plik, jeśli jeszcze nie istnieje.

1. Otwórz plik *application.properties* w edytorze tekstów, a następnie dodaj lub skonfiguruj następujące wiersze w pliku i zastąp przykładowe wartości odpowiednimi wartościami skopiowanymi wcześniej:

   ```yaml
   spring.data.cassandra.contact-points=wingtiptoyscassandra.cassandra.cosmosdb.azure.com
   spring.data.cassandra.port=10350
   spring.data.cassandra.username=wingtiptoyscassandra
   spring.data.cassandra.password=********
   ```
   Gdzie:

   | Parametr | Opis |
   |---|---|
   | `spring.data.cassandra.contact-points` | Podaj wartość pola **Punkt kontaktowy** z wcześniejszej części tego artykułu. |
   | `spring.data.cassandra.port` | Podaj wartość pola **Port** z wcześniejszej części tego artykułu. |
   | `spring.data.cassandra.username` | Podaj wartość pola **Nazwa użytkownika** z wcześniejszej części tego artykułu. |
   | `spring.data.cassandra.password` | Podaj wartość pola **Hasło podstawowe** z wcześniejszej części tego artykułu. |

1. Zapisz i zamknij plik *application.properties*.

## <a name="package-and-test-the-sample-application"></a>Pakowanie i testowanie aplikacji przykładowej 

Przejdź do katalogu, który zawiera plik pom, aby skompilować i przetestować aplikację.

1. Skompiluj aplikację przykładową przy użyciu narzędzia Maven, na przykład:

   ```shell
   mvn clean package
   ```

1. Uruchom aplikację przykładową, na przykład:

   ```shell
   java -jar target/spring-data-cassandra-on-azure-0.1.0-SNAPSHOT.jar
   ```

1. Utwórz nowe rekordy przy użyciu narzędzia `curl` z poziomu wiersza polecenia, tak jak w następujących przykładach:

   ```shell
   curl -s -d "{\"name\":\"dog\",\"species\":\"canine\"}" -H "Content-Type: application/json" -X POST http://localhost:8080/pets

   curl -s -d "{\"name\":\"cat\",\"species\":\"feline\"}" -H "Content-Type: application/json" -X POST http://localhost:8080/pets
   ```

   Aplikacja powinna zwracać wartości podobne do następujących:

   ```shell
   Added Pet{id=60fa8cb0-0423-11e9-9a70-39311962166b, name='dog', species='canine'}.

   Added Pet{id=72c1c9e0-0423-11e9-9a70-39311962166b, name='cat', species='feline'}.
   ```

1. Pobierz wszystkie istniejące rekordy przy użyciu narzędzia `curl` z poziomu wiersza polecenia, tak jak w następujących przykładach:

   ```shell
   curl -s http://localhost:8080/pets
   ```

   Aplikacja powinna zwracać wartości podobne do następujących:

   ```json
   [{"id":"60fa8cb0-0423-11e9-9a70-39311962166b","name":"dog","species":"canine"},{"id":"72c1c9e0-0423-11e9-9a70-39311962166b","name":"cat","species":"feline"}]
   ```

## <a name="summary"></a>Podsumowanie

W tym samouczku utworzono przykładową aplikację Java, która używa rozwiązania Spring Data do przechowywania i pobierania informacji przy użyciu interfejsu API Apache Cassandra usługi Azure Cosmos DB.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat środowiska Spring i platformy Azure, przejdź do informacji na temat Spring w centrum dokumentacji platformy Azure.

> [!div class="nextstepaction"]
> [Oprogramowanie Spring na platformie Azure](/azure/java/spring-framework)

### <a name="additional-resources"></a>Dodatkowe zasoby

Aby uzyskać więcej informacji na temat korzystania z platformy Azure z językiem Java, zapoznaj się z tematem [Azure dla deweloperów języka Java] i [Praca z usługą Azure DevOps i językiem Java].

<!-- URL List -->

[Azure dla deweloperów języka Java]: /azure/java/
[bezpłatne konto platformy Azure]: https://azure.microsoft.com/pricing/free-trial/
[Praca z usługą Azure DevOps i językiem Java]: /azure/devops/
[Korzyści dla subskrybentów MSDN]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Data]: https://spring.io/projects/spring-data
[Spring Initializr]: https://start.spring.io/
[Spring Framework]: https://spring.io/

<!-- IMG List -->

[COSMOSDB01]: media/configure-spring-data-apache-cassandra-with-cosmos-db/create-cosmos-db-01.png
[COSMOSDB02]: media/configure-spring-data-apache-cassandra-with-cosmos-db/create-cosmos-db-02.png
[COSMOSDB03]: media/configure-spring-data-apache-cassandra-with-cosmos-db/create-cosmos-db-03.png
[COSMOSDB04]: media/configure-spring-data-apache-cassandra-with-cosmos-db/create-cosmos-db-04.png
[COSMOSDB05]: media/configure-spring-data-apache-cassandra-with-cosmos-db/create-cosmos-db-05.png
[COSMOSDB06]: media/configure-spring-data-apache-cassandra-with-cosmos-db/create-cosmos-db-06.png
