---
title: Jak używać szablonu startowego Spring Boot z interfejsem API języka SQL usługi Azure Cosmos DB
description: Dowiedz się, jak skonfigurować aplikację utworzoną za pomocą narzędzia Spring Boot Initializr przy użyciu interfejsu API języka SQL usługi Azure Cosmos DB.
services: cosmos-db
documentationcenter: java
author: KarlErickson
ms.author: karler
ms.date: 10/02/2019
ms.service: cosmos-db
ms.tgt_pltfrm: multiple
ms.topic: article
ms.workload: data-services
ms.openlocfilehash: 887925fd652194c38db99c30e1db505ecfcee918
ms.sourcegitcommit: 8c84b6db37d0f78d66779fb6489cc8fe063055b3
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/13/2020
ms.locfileid: "77000600"
---
# <a name="how-to-use-the-spring-boot-starter-with-the-azure-cosmos-db-sql-api"></a>Jak używać szablonu startowego Spring Boot z interfejsem API języka SQL usługi Azure Cosmos DB

Azure Cosmos DB to globalnie dystrybuowana usługa bazy danych, która umożliwia deweloperom pracę z danymi przy użyciu różnych standardowych interfejsów API, takich jak SQL, MongoDB, Graph i interfejsy API tabel. Szablon startowy Spring Boot firmy Microsoft umożliwia deweloperom korzystanie z aplikacji Spring Boot, które można łatwo zintegrować z usługą Azure Cosmos DB przy użyciu interfejsu API języka SQL.

W tym artykule przedstawiono tworzenie bazy danych usługi Azure Cosmos DB przy użyciu witryny Azure Portal, następnie użycie narzędzia **[Spring Initializr]** do utworzenia niestandardowej aplikacji Spring Boot, a następnie dodanie [Spring Boot Cosmos DB Starter for Azure] do aplikacji niestandardowej w celu przechowywania danych w usłudze Azure Cosmos DB oraz pobierania danych z tej usługi przy użyciu rozwiązania Spring Data oraz interfejsu API języka SQL usługi Cosmos DB.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać czynności opisane w tym artykule, wymagane są:

* Subskrypcja platformy Azure — jeśli nie masz jeszcze subskrypcji platformy Azure, możesz aktywować [korzyści dla subskrybentów MSDN] lub utworzyć [bezpłatne konto platformy Azure].
* Obsługiwany zestaw Java Development Kit (JDK). Aby uzyskać więcej informacji na temat zestawów JDK dostępnych do użycia podczas tworzenia aplikacji na platformie Azure, zobacz <https://aka.ms/azure-jdks>.

## <a name="create-an-azure-cosmos-db-by-using-the-azure-portal"></a>Tworzenie bazy danych usługi Azure Cosmos DB przy użyciu witryny Azure Portal

1. Przejdź do witryny Azure Portal pod adresem <https://portal.azure.com/> i kliknij pozycję **Utwórz zasób**.

1. Kliknij pozycję **Bazy danych**, a następnie **Azure Cosmos DB**.

    ![Azure Portal][AZ02]

1. Na stronie **Azure Cosmos DB** wprowadź następujące informacje:

    * W polu **Subskrypcja** wybierz subskrypcję, której chcesz użyć dla bazy danych.
    * W polu **Grupa zasobów** określ, czy dla bazy danych chcesz utworzyć nową grupę zasobów, czy wybrać istniejącą grupę zasobów.
    * W polu **Nazwa konta** wprowadź unikatową nazwę konta, która będzie używana jako identyfikator URI dla bazy danych. Na przykład: *wingtiptoysdata*.
    * Jako interfejs API wybierz pozycję **Core (SQL)** .
    * W polu **Lokalizacja** określ lokalizację bazy danych.

    Po określeniu tych opcji kliknij pozycję **Przejrzyj i utwórz**, sprawdź specyfikacje i kliknij przycisk **Utwórz**.

    ![Azure Portal][AZ03]

1. Po utworzeniu bazy danych znajduje się ona na liście na **pulpicie nawigacyjnym** platformy Azure, a także w obszarze stron **Wszystkie zasoby** i **Azure Cosmos DB**. Możesz kliknąć bazę danych w dowolnej z tych lokalizacji, aby otworzyć stronę właściwości pamięci podręcznej.

1. Gdy zostanie wyświetlona strona właściwości bazy danych, kliknij pozycję **Klucze** i skopiuj identyfikator URI oraz klucze dostępu do bazy danych. Te wartości będą używane w aplikacji Spring Boot.

    ![Azure Portal][AZ05]

## <a name="create-a-simple-spring-boot-application-with-the-spring-initializr"></a>Tworzenie prostej aplikacji Spring Boot za pomocą narzędzia Spring Initializr

Wykonaj następujące czynności, aby utworzyć nowy projekt aplikacji Spring Boot, korzystając z obsługi platformy Azure. Możesz też użyć przykładowego projektu [azure-cosmosdb-spring-boot-sample](https://github.com/microsoft/azure-spring-boot/tree/master/azure-spring-boot-samples/azure-cosmosdb-spring-boot-sample) z repozytorium [azure-spring-boot](https://github.com/microsoft/azure-spring-boot). Następnie możesz przejść bezpośrednio do sekcji [Kompilowanie i testowanie aplikacji](#build-and-test-your-app).

1. Przejdź do <https://start.spring.io/>.

1. Określ, że chcesz wygenerować projekt Maven (opcja **Maven Project**) za pomocą języka **Java**, określ wersję programu **Spring Boot**, wprowadź nazwy w polach **Group** (Grupa) i **Artifact** (Artefakt) dla aplikacji, dodaj pozycję **Azure Support** (Obsługa platformy Azure) w sekcji Dependencies (Zależności), a następnie kliknij przycisk **Generate Project** (Generuj projekt).

    ![Podstawowe opcje narzędzia Spring Initializr][SI01]

    > [!NOTE]
    >
    > Narzędzie Spring Initializr używa nazw z pól **Group** (Grupa) i **Artifact** (Artefakt) do utworzenia nazwy pakietu, na przykład: *com.example.wintiptoysdata*.

1. Po wyświetleniu monitu pobierz projekt do ścieżki na komputerze lokalnym i wyodrębnij pliki.

Prosta aplikacja Spring Boot jest teraz gotowa do edycji.

## <a name="configure-your-spring-boot-application-to-use-the-azure-spring-boot-starter"></a>Konfigurowanie aplikacji Spring Boot do korzystania z szablonu startowego Azure Spring Boot

1. W katalogu aplikacji znajdź plik *pom.xml*, na przykład:

    `C:\SpringBoot\wingtiptoysdata\pom.xml`

    — lub —

    `/users/example/home/wingtiptoysdata/pom.xml`

1. Otwórz plik *pom.xml* w edytorze tekstów i dodaj do elementu `<dependencies>` następujące pozycje:

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-cosmosdb-spring-boot-starter</artifactId>
    </dependency>
    ```

1. Sprawdź, czy element *properties* wskazuje wymagane wersje języka Java i platformy Azure:

    ```xml
    <properties>
       <java.version>1.8</java.version>
       <azure.version>2.2.0</azure.version>
    </properties>
    ```

1. Zapisz i zamknij plik *pom.xml*.

## <a name="configure-your-spring-boot-application-to-use-your-azure-cosmos-db"></a>Konfigurowanie aplikacji Spring Boot do korzystania z usługi Azure Cosmos DB

1. Znajdź plik *application.properties* w katalogu *resources* aplikacji, na przykład:

    `C:\SpringBoot\wingtiptoysdata\src\main\resources\application.properties`

    — lub —

    `/users/example/home/wingtiptoysdata/src/main/resources/application.properties`

1. Otwórz plik *application.properties* w edytorze tekstów, a następnie dodaj w pliku następujące wiersze i zastąp przykładowe wartości odpowiednimi właściwościami dla bazy danych:

    ```text
    # Specify the DNS URI of your Azure Cosmos DB.
    azure.cosmosdb.uri=https://wingtiptoys.documents.azure.com:443/

    # Specify the access key for your database.
    azure.cosmosdb.key=57686f6120447564652c20426f6220526f636b73==

    # Specify the name of your database.
    azure.cosmosdb.database=wingtiptoysdata
    ```

1. Zapisz i zamknij plik *application.properties*.

## <a name="add-sample-code-to-implement-basic-database-functionality"></a>Dodawanie przykładowego kodu w celu zaimplementowania podstawowej funkcjonalności bazy danych

W tej sekcji utworzysz dwie klasy języka Java do przechowywania danych użytkownika, a następnie zmodyfikujesz główną klasę aplikacji, aby utworzyć wystąpienie klasy *User* i zapisać je w bazie danych.

### <a name="define-a-base-class-for-storing-user-data"></a>Definiowanie klasy bazowej do przechowywania danych użytkownika

1. Utwórz nowy plik o nazwie *User.java* w tym samym katalogu, w którym znajduje się główny plik Java aplikacji.

1. Otwórz plik *User.java* w edytorze tekstów i dodaj w nim następujące wiersze, aby zdefiniować ogólną klasę użytkownika, która będzie przechowywać wartości w bazie danych oraz je z niej pobierać:

    ```java
    package com.example.wingtiptoysdata;

    import com.microsoft.azure.spring.data.cosmosdb.core.mapping.Document;
    import com.microsoft.azure.spring.data.cosmosdb.core.mapping.PartitionKey;
    import org.springframework.data.annotation.Id;

    @Document(collection = "mycollection")
    public class User {

        @Id
        private String id;
        private String firstName;

        @PartitionKey
        private String lastName;
        private String address;

        public User(String id, String firstName, String lastName, String address) {
            this.id = id;
            this.firstName = firstName;
            this.lastName = lastName;
            this.address = address;
        }

        public User() {
        }

        public String getId() {
            return id;
        }

        public void setId(String id) {
            this.id = id;
        }

        public String getFirstName() {
            return firstName;
        }

        public void setFirstName(String firstName) {
            this.firstName = firstName;
        }

        public String getLastName() {
            return lastName;
        }

        public void setLastName(String lastName) {
            this.lastName = lastName;
        }

        public String getAddress() {
            return address;
        }

        public void setAddress(String address) {
            this.address = address;
        }

        @Override
        public String toString() {
            return String.format("%s %s, %s", firstName, lastName, address);
        }
    }
    ```

1. Zapisz i zamknij plik *User.java*.

### <a name="define-a-data-repository-interface"></a>Definiowanie interfejsu repozytorium danych

1. Utwórz nowy plik o nazwie *UserRepository.java* w tym samym katalogu, w którym znajduje się główny plik Java aplikacji.

1. Otwórz plik *UserRepository.java* w edytorze tekstów i dodaj w pliku następujące wiersze, aby zdefiniować interfejs repozytorium użytkownika, który będzie poszerzać domyślny interfejs `ReactiveCosmosRepository`:

    ```java
    package com.example.wingtiptoysdata;

    import com.microsoft.azure.spring.data.cosmosdb.repository.ReactiveCosmosRepository;
    import org.springframework.stereotype.Repository;
    import reactor.core.publisher.Flux;

    @Repository
    public interface UserRepository extends ReactiveCosmosRepository<User, String> {
        Flux<User> findByFirstName(String firstName);
    }
    ```

    Interfejs `ReactiveCosmosRepository` zastępuje interfejs `DocumentDbRepository` z poprzedniej wersji szablonu startowego. Nowy interfejs zapewnia synchroniczne i reaktywne interfejsy API dla podstawowych operacji zapisywania, usuwania i znajdowania.

1. Zapisz i zamknij plik *UserRepository.java*.

### <a name="modify-the-main-application-class"></a>Modyfikowanie głównej klasy aplikacji

1. Znajdź główny plik Java aplikacji w katalogu pakietu aplikacji, na przykład:

    `C:\SpringBoot\wingtiptoysdata\src\main\java\com\example\wingtiptoysdata\WingtiptoysdataApplication.java`

    — lub —

    `/users/example/home/wingtiptoysdata/src/main/java/com/example/wingtiptoysdata/WingtiptoysdataApplication.java`

1. Otwórz główny plik Java aplikacji w edytorze tekstów, a następnie dodaj w nim następujące wiersze:

    ```java
    package com.example.wingtiptoysdata;

    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.boot.CommandLineRunner;
    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    import org.springframework.util.Assert;
    import reactor.core.publisher.Flux;
    import reactor.core.publisher.Mono;

    import javax.annotation.PostConstruct;
    import javax.annotation.PreDestroy;
    import java.util.Optional;

    @SpringBootApplication
    public class WingtiptoysdataApplication implements CommandLineRunner {

        private static final Logger LOGGER = LoggerFactory.getLogger(WingtiptoysdataApplication.class);

        @Autowired
        private UserRepository repository;

        public static void main(String[] args) {
            SpringApplication.run(WingtiptoysdataApplication.class, args);
        }

        public void run(String... var1) throws Exception {
            final User testUser = new User("1", "Tasha", "Calderon", "4567 Main St Buffalo, NY 98052");

            LOGGER.info("Saving user: {}", testUser);

            // Save the User class to Azure CosmosDB database.
            final Mono<User> saveUserMono = repository.save(testUser);

            final Flux<User> firstNameUserFlux = repository.findByFirstName("testFirstName");

            //  Nothing happens until we subscribe to these Monos.
            //  findById will not return the user as user is not present.
            final Mono<User> findByIdMono = repository.findById(testUser.getId());
            final User findByIdUser = findByIdMono.block();
            Assert.isNull(findByIdUser, "User must be null");

            final User savedUser = saveUserMono.block();
            Assert.state(savedUser != null, "Saved user must not be null");
            Assert.state(savedUser.getFirstName().equals(testUser.getFirstName()), "Saved user first name doesn't match");

            LOGGER.info("Saved user");

            firstNameUserFlux.collectList().block();

            final Optional<User> optionalUserResult = repository.findById(testUser.getId()).blockOptional();
            Assert.isTrue(optionalUserResult.isPresent(), "Cannot find user.");

            final User result = optionalUserResult.get();
            Assert.state(result.getFirstName().equals(testUser.getFirstName()), "query result firstName doesn't match!");
            Assert.state(result.getLastName().equals(testUser.getLastName()), "query result lastName doesn't match!");

            LOGGER.info("Found user by findById : {}", result);
        }

        @PostConstruct
        public void setup() {
            LOGGER.info("Clear the database");
            this.repository.deleteAll().block();
        }

        @PreDestroy
        public void cleanup() {
            LOGGER.info("Cleaning up users");
            this.repository.deleteAll().block();
        }
    }
    ```

1. Zapisz i zamknij główny plik Java aplikacji.

## <a name="build-and-test-your-app"></a>Kompilowanie i testowanie aplikacji

1. Otwórz wiersz polecenia i przejdź do folderu, w którym znajduje się plik *pom.xml*, na przykład:

    `cd C:\SpringBoot\wingtiptoysdata`

    — lub —

    `cd /users/example/home/wingtiptoysdata`

1. Użyj następującego polecenia, aby skompilować i uruchomić aplikację:

    ```console
    mvnw clean test
    ```

    To polecenie uruchamia aplikację automatycznie w ramach fazy testowej. Możesz również użyć polecenia:

    ```console
    mvnw clean spring-boot:run
    ```

    Po otrzymaniu danych wyjściowych kompilacji i testowania w oknie konsoli zostanie wyświetlony komunikat wyglądający podobnie do poniższego:

    ```console
      .   ____          _            __ _ _
     /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
    ( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
     \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
      '  |____| .__|_| |_|_| |_\__, | / / / /
     =========|_|==============|___/=/_/_/_/
     :: Spring Boot ::            (v2.2.0.RC1)
    >
    > 2019-10-04 15:19:06.817  INFO 30013 --- [           main] c.e.w.WingtiptoysdataApplicationTests    : Starting WingtiptoysdataApplicationTests on devmachine03 with PID 30013 (started by <user> in /d/source/repos/wingtiptoysdata)
    > 2019-10-04 15:19:06.818  INFO 30013 --- [           main] c.e.w.WingtiptoysdataApplicationTests    : No active profile set, falling back to default profiles: default
    > 2019-10-04 15:19:08.329  INFO 30013 --- [           main] .s.d.r.c.RepositoryConfigurationDelegate : Bootstrapping Spring Data repositories in DEFAULT mode.
    > 2019-10-04 15:19:09.720  INFO 30013 --- [           main] .s.d.r.c.RepositoryConfigurationDelegate : Finished Spring Data repository scanning in 1369ms. Found 1 repository interfaces.
    > 2019-10-04 15:19:09.734  INFO 30013 --- [           main] .s.d.r.c.RepositoryConfigurationDelegate : Bootstrapping Spring Data repositories in DEFAULT mode.
    > 2019-10-04 15:19:09.748  INFO 30013 --- [           main] .s.d.r.c.RepositoryConfigurationDelegate : Finished Spring Data repository scanning in 13ms. Found 0 repository interfaces.

    ... (omitting Cosmos DB connection output) ...

    > 2019-10-04 15:19:46.584  INFO 30013 --- [           main] c.e.w.WingtiptoysdataApplicationTests    : Started WingtiptoysdataApplicationTests in 40.702 seconds (JVM running for 44.647)
    > 2019-10-04 15:19:46.587  INFO 30013 --- [           main] c.e.w.WingtiptoysdataApplication         : Saving user: Tasha Calderon, 4567 Main St Buffalo, NY 98052
    > 2019-10-04 15:19:47.122  INFO 30013 --- [           main] c.e.w.WingtiptoysdataApplication         : Saved user
    > 2019-10-04 15:19:47.289  INFO 30013 --- [           main] c.e.w.WingtiptoysdataApplication         : Found user by findById : Tasha Calderon, 4567 Main St Buffalo, NY 98052
    > [INFO] Tests run: 1, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 44.003 s - in com.example.wingtiptoysdata.WingtiptoysdataApplicationTests
    > 2019-10-04 15:19:48.124  INFO 30013 --- [extShutdownHook] c.a.d.c.internal.RxDocumentClientImpl    : Shutting down ...
    > 2019-10-04 15:19:48.194  INFO 30013 --- [extShutdownHook] c.a.d.c.internal.RxDocumentClientImpl    : Shutting down ...
    > 2019-10-04 15:19:48.200  INFO 30013 --- [extShutdownHook] c.e.w.WingtiptoysdataApplication         : Cleaning up users
    > [INFO]
    > [INFO] Results:
    > [INFO]
    > [INFO] Tests run: 1, Failures: 0, Errors: 0, Skipped: 0
    > [INFO]
    > [INFO]
    > [INFO] --- maven-jar-plugin:3.1.2:jar (default-jar) @ wingtiptoysdata ---
    > [INFO] Building jar: /d/source/repos/wingtiptoysdata/target/wingtiptoysdata-0.0.1-SNAPSHOT.jar
    > [INFO]
    > [INFO] --- spring-boot-maven-plugin:2.2.0.RC1:repackage (repackage) @ wingtiptoysdata ---
    > [INFO] Replacing main artifact with repackaged archive
    > [INFO] ------------------------------------------------------------------------
    > [INFO] BUILD SUCCESS
    > [INFO] ------------------------------------------------------------------------
    > [INFO] Total time:  02:18 min
    > [INFO] Finished at: 2019-10-04T15:20:05-07:00
    > [INFO] ------------------------------------------------------------------------
    ```

    ![Dane wyjściowe po pomyślnym skompilowaniu aplikacji][JV02]

    Komunikaty `Saved user` i `Found user` wskazują, że dane zostały pomyślnie zapisane w usłudze Cosmos DB, a następnie pobrane ponownie.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie chcesz już korzystać z tej aplikacji, pamiętaj, aby usunąć grupę zasobów zawierającą utworzoną wcześniej bazę danych Cosmos DB. Możesz to zrobić w witrynie Azure Portal.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat oprogramowania Spring i platformy Azure, przejdź do centrum dokumentacji dotyczącej oprogramowania Spring na platformie Azure.

> [!div class="nextstepaction"]
> [Oprogramowanie Spring na platformie Azure](/azure/java/spring-framework)

### <a name="additional-resources"></a>Dodatkowe zasoby

Aby uzyskać więcej informacji o korzystaniu z usługi Azure Cosmos DB i języka Java, zobacz następujące artykuły:

* [Dokumentacja usługi Azure Cosmos DB].

* [Azure Cosmos DB: tworzenie bazy danych dokumentów przy użyciu języka Java i witryny Azure Portal][Build a SQL API app with Java]

* [Spring Data for Azure Cosmos DB SQL API] (Projekt Spring Data do interfejsu API języka SQL usługi Azure Cosmos DB)

Aby uzyskać więcej informacji o korzystaniu z aplikacji Spring Boot na platformie Azure, zobacz następujące artykuły:

* [Spring Boot Cosmos DB Starter for Azure] (Szablon startowy Spring Boot dla usługi Azure Cosmos DB)

* [Deploy a Spring Boot Application to the Azure App Service](deploy-spring-boot-java-app-from-container-registry-using-maven-plugin.md) (Wdrażanie aplikacji Spring Boot w usłudze Azure App Service)

* [Running a Spring Boot Application on a Kubernetes Cluster in the Azure Container Service](deploy-spring-boot-java-app-on-kubernetes.md) (Uruchamianie aplikacji Spring Boot w klastrze Kubernetes w usłudze Azure Container Service)

Aby uzyskać więcej informacji na temat korzystania z platformy Azure z językiem Java, zapoznaj się z tematami [Azure dla deweloperów języka Java] i [Working with Azure DevOps and Java] (Praca z narzędziami Azure DevOps i językiem Java).

**[Platforma Spring]** jest rozwiązaniem open-source, które pomaga deweloperom języka Java tworzyć aplikacje na poziomie przedsiębiorstwa. Jednym z bardziej popularnych projektów opartych na tej platformie jest [Spring Boot], który oferuje uproszczoną metodę tworzenia autonomicznych aplikacji Java. Aby ułatwić deweloperom rozpoczęcie pracy z aplikacją Spring Boot, pod adresem <https://github.com/spring-guides/> udostępniono przykładowe pakiety aplikacji Spring Boot. Oprócz możliwości wyboru z listy podstawowych projektów Spring Boot narzędzie **[Spring Initializr]** pomaga deweloperom rozpocząć tworzenie niestandardowych aplikacji Spring Boot.

<!-- URL List -->

[Dokumentacja usługi Azure Cosmos DB]: /azure/cosmos-db/
[Azure dla deweloperów języka Java]: /azure/java/
[Build a SQL API app with Java]: /azure/cosmos-db/create-sql-api-java 
[Spring Data for Azure Cosmos DB SQL API]: https://azure.microsoft.com/blog/spring-data-azure-cosmos-db-nosql-data-access-on-azure/ (Projekt Spring Data do interfejsu API języka SQL usługi Azure Cosmos DB)
[Spring Boot Cosmos DB Starter for Azure]: https://github.com/microsoft/azure-spring-boot/tree/master/azure-spring-boot-starters/azure-cosmosdb-spring-boot-starter (Szablon startowy Spring Boot dla usługi Azure Cosmos DB)
[bezpłatne konto platformy Azure]: https://azure.microsoft.com/pricing/free-trial/
[Working with Azure DevOps and Java]: https://azure.microsoft.com/services/devops/java/ (Praca z narzędziami Azure DevOps i językiem Java)
[Korzyści dla subskrybentów MSDN]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Initializr]: https://start.spring.io/
[Platforma Spring]: https://spring.io/

<!-- IMG List -->

[AZ01]: ./media/configure-spring-boot-starter-java-app-with-cosmos-db/AZ01.png
[AZ02]: ./media/configure-spring-boot-starter-java-app-with-cosmos-db/AZ02.png
[AZ03]: ./media/configure-spring-boot-starter-java-app-with-cosmos-db/AZ03.png
[AZ04]: ./media/configure-spring-boot-starter-java-app-with-cosmos-db/AZ04.png
[AZ05]: ./media/configure-spring-boot-starter-java-app-with-cosmos-db/AZ05.png

[SI01]: ./media/configure-spring-boot-starter-java-app-with-cosmos-db/SI01.png

[JV02]: ./media/configure-spring-boot-starter-java-app-with-cosmos-db/JV02.png
