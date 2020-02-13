---
title: Jak używać szablonu startowego Spring Data Gremlin z interfejsem API języka SQL w usłudze Azure Cosmos DB
description: Dowiedz się, jak skonfigurować aplikację utworzoną za pomocą narzędzia Spring Boot Initializr przy użyciu interfejsu API języka SQL usługi Azure Cosmos DB.
services: cosmos-db
documentationcenter: java
ms.date: 01/10/2020
ms.service: cosmos-db
ms.tgt_pltfrm: multiple
ms.topic: article
ms.workload: data-services
ms.openlocfilehash: 61bf7d78edf2fcdc755d90588fe1c839d319f823
ms.sourcegitcommit: ac68fb174d606c7af2bfa79fe32b8ca7b73c86a1
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2020
ms.locfileid: "77000642"
---
# <a name="how-to-use-the-spring-data-gremlin-starter-with-the-azure-cosmos-db-sql-api"></a>Jak używać szablonu startowego Spring Data Gremlin z interfejsem API języka SQL w usłudze Azure Cosmos DB

## <a name="overview"></a>Omówienie

Szablon startowy Spring Data Gremlin zapewnia wsparcie narzędzia Spring Data dla języka zapytań Apache Gremlin, którego deweloperzy mogą używać z dowolnym magazynem danych zgodnym z językiem Gremlin.

W tym artykule opisano tworzenie bazy danych usługi Azure Cosmos przy użyciu witryny Azure Portal do użytku z interfejsem API języka Gremlin, następnie użycie narzędzia **[Spring Initializr]** do utworzenia niestandardowej aplikacji java, a następnie dodanie funkcjonalności szablonu startowego Spring Data Gremlin do aplikacji niestandardowej w celu przechowywania danych w usłudze Azure Cosmos DB oraz pobierania danych z tej usługi przy użyciu języka Gremlin.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać czynności opisane w tym artykule, wymagane są:

* Subskrypcja platformy Azure — jeśli nie masz jeszcze subskrypcji platformy Azure, możesz aktywować [korzyści dla subskrybentów MSDN] lub utworzyć [bezpłatne konto platformy Azure].
* Obsługiwany zestaw Java Development Kit (JDK). Aby uzyskać więcej informacji na temat zestawów JDK dostępnych do użycia podczas tworzenia aplikacji na platformie Azure, zobacz <https://aka.ms/azure-jdks>.
* Narzędzie [Apache Maven](http://maven.apache.org/) w wersji 3.0 lub nowszej.

> [!IMPORTANT]
>
> Aby wykonać kroki opisane w tym artykule, potrzebny jest program Spring Boot w wersji 2.0 lub nowszej.
>

## <a name="create-an-azure-cosmos-db-using-the-azure-portal"></a>Tworzenie bazy danych usługi Azure Cosmos DB przy użyciu witryny Azure Portal

### <a name="create-your-azure-cosmos-database-for-use-with-gremlin-api"></a>Tworzenie bazy danych usługi Azure Cosmos DB do użytku z interfejsem API języka Gremlin

1. Przejdź do witryny Azure Portal pod adresem <https://portal.azure.com/> i kliknij pozycję **+Utwórz zasób**.

1. Kliknij pozycję **Bazy danych**, a następnie **Azure Cosmos DB**.

   ![Tworzenie bazy danych usługi Azure Cosmos DB][AZ02]

1. Na stronie **Azure Cosmos DB** wprowadź następujące informacje:

   * W polu **Subskrypcja** wybierz subskrypcję, której chcesz użyć dla bazy danych.
   * W polu **Grupa zasobów** określ, czy dla bazy danych chcesz utworzyć nową grupę zasobów, czy wybrać istniejącą grupę zasobów.
   * Wprowadź w polu **Nazwa konta** unikatową nazwę konta, która będzie używana jako część identyfikatora URI języka Gremlin dla bazy danych. Na przykład: jeśli w polu **Nazwa konta** wprowadzisz **wingtiptoysdata**, identyfikatorem URI języka Gremlin będzie *wingtiptoysdata.gremlin.cosmosdb.azure.com*.
   * Jako interfejs API wybierz pozycję **Gremlin (Graph)** .
   * W polu **Lokalizacja** określ lokalizację bazy danych.
   
Po określeniu tych opcji kliknij przycisk **Przejrzyj i utwórz**.

   ![Określanie opcji usługi Azure Cosmos DB][AZ03]

Przejrzyj specyfikację, a następnie kliknij przycisk **Utwórz**, aby utworzyć bazę danych.

1. Po utworzeniu bazy danych kliknij pozycję **Przejdź do zasobu**. Baza danych znajduje się również na liście na **pulpicie nawigacyjnym** platformy Azure , a także w obszarze stron **Wszystkie zasoby** i **Azure Cosmos DB**. Możesz kliknąć bazę danych w dowolnej z tych lokalizacji, aby otworzyć stronę właściwości pamięci podręcznej.

1. Gdy zostanie wyświetlona strona właściwości bazy danych, kliknij pozycję **Klucze** i skopiuj identyfikator URI oraz klucze dostępu do bazy danych. Te wartości będą używane w aplikacji Spring Boot.

   ![Klawisze dostępu][AZ05]

### <a name="add-a-graph-to-your-azure-cosmos-database"></a>Dodawanie grafu do bazy danych usługi Azure Cosmos DB

1. Kliknij pozycję **Eksplorator danych**, a następnie pozycję **Nowy graf**.

   ![Nowy graf][AZ06]

1. Gdy zostanie wyświetlone okno **Dodaj graf**, wprowadź następujące informacje:

   * Określ unikatowy **Identyfikator bazy danych** dla bazy danych.
   * Możesz określić **Pojemność magazynu** lub zaakceptować wartość domyślną.
   * Określ unikatowy **Identyfikator grafu** dla grafu.
   * Wprowadź **Klucz partycji**. Więcej informacji znajduje się w temacie [Using a partitioned graph in Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/graph-partitioning) (Używanie grafu podzielonego na partycje w usłudze Azure Cosmos DB).
Kliknij przycisk **OK**.
   
   Po określeniu tych opcji kliknij przycisk **OK**, aby utworzyć graf.

   ![Dodawanie grafu][AZ07]

1. Po utworzeniu grafu można go wyświetlić za pomocą opcji **Eksplorator danych**.

   ![Wyświetlane właściwości grafu][AZ08]

## <a name="create-a-simple-spring-boot-application-with-the-spring-initializr"></a>Tworzenie prostej aplikacji Spring Boot za pomocą narzędzia Spring Initializr

1. Przejdź do <https://start.spring.io/>.

1. Określ, że chcesz wygenerować projekt **Maven** za pomocą języka **Java**, wprowadź nazwy w polach **Group** (Grupa) i **Artifact** (Artefakt) dla aplikacji, określ wersję aplikacji **Spring Boot** 2.0 lub nowszą, a następnie kliknij przycisk **Generate Project** (Generuj projekt).

   ![Podstawowe opcje narzędzia Spring Initializr][SI01]

   > [!NOTE]
   >
   > Narzędzie Spring Initializr używa nazw z pól **Group** (Grupa) i **Artifact** (Artefakt) do utworzenia nazwy pakietu, na przykład: *com.example.wintiptoysdata.
   >

1. Po wyświetleniu monitu pobierz projekt do ścieżki na komputerze lokalnym.

1. Po wyodrębnieniu plików w systemie lokalnym Twoja prosta aplikacja Spring Boot będzie gotowa do edycji.

   ![Pliki niestandardowego projektu Spring Boot][SI03]

## <a name="configure-your-spring-boot-app-to-use-the-spring-data-gremlin-starter"></a>Konfigurowanie aplikacji Spring Boot do korzystania z szablonu startowego Spring Data Gremlin

1. W katalogu aplikacji znajdź plik *pom.xml*, na przykład:

   `C:\SpringBoot\wingtiptoysdata\pom.xml`

   — lub —

   `/users/example/home/wingtiptoysdata/pom.xml`

1. Otwórz plik *pom.xml* w edytorze tekstów, a następnie dodaj szablon startowy Spring Data Gremlin do listy `<dependencies>`:

   ```xml
   <dependency>
      <groupId>com.microsoft.spring.data.gremlin</groupId>
      <artifactId>spring-data-gremlin</artifactId>
      <version>2.0.0</version>
   </dependency>
   ```

   ![Edytowanie pliku pom.xml][PM02]

1. Zapisz i zamknij plik *pom.xml*.

## <a name="configure-your-spring-boot-app-to-use-your-azure-cosmos-db"></a>Konfigurowanie aplikacji Spring Boot do korzystania z usługi Azure Cosmos DB

1. Znajdź katalog *resources* aplikacji i utwórz nowy plik o nazwie *application.yml*. Przykład:

   `C:\SpringBoot\wingtiptoysdata\src\main\resources\application.yml`

   — lub —

   `/users/example/home/wingtiptoysdata/src/main/resources/application.yml`

   ![Tworzenie pliku application.yml][RE01]

1. Otwórz plik *application.yml* w edytorze tekstów, a następnie dodaj w pliku następujące wiersze i zastąp przykładowe wartości odpowiednimi właściwościami dla bazy danych:

   ```yaml
   gremlin:
      endpoint: wingtiptoys.gremlin.cosmosdb.azure.com
      port: 443
      username: /dbs/wingtiptoysdb/colls/wingtiptoysgraph
      password: 57686f6120447564652c20426f6220526f636b73==
      telemetryAllowed: false
   ```
   
   Gdzie:
   
   | Pole | Opis |
   |---|---|
   | `endpoint` | Określa identyfikator URI języka Gremlin dla bazy danych, który pochodzi od unikatowego **identyfikatora** określonego podczas tworzenia bazy danych usługi Azure Cosmos DB we wcześniej części tego samouczka. |
   | `port` | Określa port TCP/IP, którym powinien być **443** dla protokołu HTTPS. |
   | `username` | Określa unikatowy **Identyfikator bazy danych** oraz **Identyfikator grafu**, których użyto podczas dodawania grafu wcześniej w tym samouczku. Należy je wprowadzić, używając następującej składni: „/dbs/ **{Database id}** /colls/ **{Graph id}** ”. |
   | `password` | Określa podstawowy lub pomocniczy **Klucz dostępu** skopiowany wcześniej w tym samouczku. |
   | `telemetryAllowed` | Podaj wartość **true**, jeśli chcesz włączyć telemetrię, w przeciwnym razie podaj wartość **false**.

1. Zapisz i zamknij plik *application.yml*.

## <a name="add-sample-code-to-implement-basic-database-functionality"></a>Dodawanie przykładowego kodu w celu zaimplementowania podstawowej funkcjonalności bazy danych

W tej sekcji utworzysz niezbędne klasy języka Java do przechowywania danych w bazie danych.

### <a name="modify-the-main-application-class"></a>Modyfikowanie głównej klasy aplikacji

1. Znajdź główny plik Java aplikacji w katalogu pakietu aplikacji, na przykład:

   `C:\SpringBoot\wingtiptoysdata\src\main\java\com\example\wingtiptoysdata\WingtiptoysdataApplication.java`

   — lub —

   `/users/example/home/wingtiptoysdata/src/main/java/com/example/wingtiptoysdata/WingtiptoysdataApplication.java`

   ![Lokalizowanie pliku Java aplikacji][JV01]

1. Otwórz główny plik Java aplikacji w edytorze tekstów, a następnie dodaj w nim następujące wiersze:

   ```java
   package com.example.wingtiptoysdata;
   
   // These imports are required for the application.
   import com.microsoft.spring.data.gremlin.common.GremlinFactory;
   import com.example.wingtiptoysdata.domain.Network;
   import com.example.wingtiptoysdata.domain.Person;
   import com.example.wingtiptoysdata.domain.Relation;
   import com.example.wingtiptoysdata.repository.NetworkRepository;
   import com.example.wingtiptoysdata.repository.PersonRepository;
   import com.example.wingtiptoysdata.repository.RelationRepository;
   import org.springframework.beans.factory.annotation.Autowired;
   import org.springframework.boot.SpringApplication;
   import org.springframework.boot.autoconfigure.SpringBootApplication;
   import javax.annotation.PostConstruct;
   import javax.annotation.PreDestroy;
   
   @SpringBootApplication
   public class WingtiptoysdataApplication {
   
       // Define several person classes to store personal data.
       private final Person person1 = new Person("01", "Nellie Hughes", "23");
       private final Person person2 = new Person("02", "Delmar Alfred", "34");
       private final Person person3 = new Person("03", "Kelley Hunter", "45");
       private final Person person4 = new Person("04", "Roscoe Guerin", "56");
       private final Person person5 = new Person("05", "Gracie Chavez", "67");
   
       // Define relationship classes to define the relationships between some of the persons.
       private final Relation relation1 = new Relation("0102", "siblings", person1, person2);
       private final Relation relation2 = new Relation("0203", "coworkers", person2, person3);
       private final Relation relation3 = new Relation("0301", "parent", person3, person1);
       private final Relation relation4 = new Relation("0302", "parent", person3, person2);
       private final Relation relation5 = new Relation("0501", "grandparent", person5, person1);
       private final Relation relation6 = new Relation("0502", "grandparent", person5, person2);
   
       // Define the network.
       private final Network network = new Network();
   
       // Autowire the repositories and factory.
       @Autowired
       private PersonRepository personRepo;
       @Autowired
       private RelationRepository relationRepo;
       @Autowired
       private NetworkRepository networkRepo;
       @Autowired
       private GremlinFactory factory;
   
       // Run the Spring application and exit.
    public static void main(String[] args) {
           SpringApplication.run(WingtiptoysdataApplication.class, args);
           System.exit(0);
    }
   
       // Perform post-construct operations.    
       @PostConstruct
       public void setup() {
           // Delete any existing data from the database.
           this.networkRepo.deleteAll();
   
           // Add the relationship classes as edges.
           this.network.getEdges().add(this.relation1);
           this.network.getEdges().add(this.relation2);
           this.network.getEdges().add(this.relation3);
           this.network.getEdges().add(this.relation4);
           this.network.getEdges().add(this.relation5);
           this.network.getEdges().add(this.relation6);
   
           // Add the person classes as vertices.
           this.network.getVertexes().add(this.person1);
           this.network.getVertexes().add(this.person2);
           this.network.getVertexes().add(this.person3);
           this.network.getVertexes().add(this.person4);
           this.network.getVertexes().add(this.person5);
   
           // Save the network.
           this.networkRepo.save(this.network);
       }
   }
   ```

1. Zapisz i zamknij główny plik Java aplikacji.

### <a name="define-a-basic-class-for-storing-configuration-information"></a>Definiowanie podstawowej klasy do przechowywania informacji o konfiguracji

1. Utwórz folder o nazwie *config* w katalogu pakietu aplikacji, na przykład:

   `C:\SpringBoot\wingtiptoysdata\src\main\java\com\example\wingtiptoysdata\config`

   — lub —

   `/users/example/home/wingtiptoysdata/src/main/java/com/example/wingtiptoysdata/config`

1. Utwórz nowy plik Java o nazwie *UserRepositoryConfiguration.java* w katalogu *config*, a następnie otwórz ten plik w edytorze tekstów i dodaj następujące wiersze:

   ```java
   package com.example.wingtiptoysdata.config;

   import com.microsoft.spring.data.gremlin.common.GremlinConfiguration;
   import com.microsoft.spring.data.gremlin.common.GremlinFactory;
   import com.microsoft.spring.data.gremlin.config.AbstractGremlinConfiguration;
   import com.microsoft.spring.data.gremlin.repository.config.EnableGremlinRepositories;
   import org.springframework.beans.factory.annotation.Autowired;
   import org.springframework.boot.context.properties.EnableConfigurationProperties;
   import org.springframework.context.annotation.Bean;
   import org.springframework.context.annotation.Configuration;
   import org.springframework.context.annotation.PropertySource;
   
   @Configuration
   @EnableGremlinRepositories(basePackages = "com.example.wingtiptoysdata.repository")
   @EnableConfigurationProperties(GremlinConfiguration.class)
   @PropertySource("classpath:application.yml")
   public class UserRepositoryConfiguration extends AbstractGremlinConfiguration {
   
       @Autowired
       private GremlinConfiguration config;
   
       @Override
       public GremlinConfiguration getGremlinConfiguration() {
           return this.config;
       }
   }
   ```

1. Zapisz i zamknij plik *UserRepositoryConfiguration.java*.

### <a name="define-a-set-of-classes-that-define-the-elements-of-your-graph-database"></a>Definiowanie zestawu klas, które definiują elementy bazy danych grafu

1. Utwórz folder o nazwie *domain* w katalogu pakietu aplikacji, na przykład:

   `C:\SpringBoot\wingtiptoysdata\src\main\java\com\example\wingtiptoysdata\domain`

   — lub —

   `/users/example/home/wingtiptoysdata/src/main/java/com/example/wingtiptoysdata/domain`

1. Utwórz nowy plik Java o nazwie *Person.java* w katalogu *domain*, następnie otwórz plik w edytorze tekstów i dodaj następujące wiersze:

   ```java
   package com.example.wingtiptoysdata.domain;
   
   import com.microsoft.spring.data.gremlin.annotation.Vertex;
   import lombok.AllArgsConstructor;
   import lombok.Data;
   import lombok.NoArgsConstructor;
   import org.springframework.data.annotation.Id;
   
   @Data
   @Vertex
   @AllArgsConstructor
   @NoArgsConstructor
   public class Person {
   
       @Id
       private String id;
   
       private String name;
   
       private String age;
   }
   ```

1. Zapisz i zamknij plik *Person.java*.

1. Utwórz nowy plik Java o nazwie *Relation.java* w katalogu *domain*, następnie otwórz plik w edytorze tekstów i dodaj następujące wiersze:

   ```java
   package com.example.wingtiptoysdata.domain;
   
   import com.microsoft.spring.data.gremlin.annotation.Edge;
   import com.microsoft.spring.data.gremlin.annotation.EdgeFrom;
   import com.microsoft.spring.data.gremlin.annotation.EdgeTo;
   import lombok.AllArgsConstructor;
   import lombok.Data;
   import lombok.NoArgsConstructor;
   import org.springframework.data.annotation.Id;
   
   @Data
   @Edge
   @AllArgsConstructor
   @NoArgsConstructor
   public class Relation {
   
       @Id
       private String id;
   
       private String name;
   
       @EdgeFrom
       private Person personFrom;
   
       @EdgeTo
       private Person personTo;
   }
   ```

1. Zapisz i zamknij plik *Relation.java*.

1. Utwórz nowy plik Java o nazwie *Network.java* w katalogu *domain*, następnie otwórz plik w edytorze tekstów i dodaj następujące wiersze:

   ```java
   package com.example.wingtiptoysdata.domain;
   
   import com.microsoft.spring.data.gremlin.annotation.EdgeSet;
   import com.microsoft.spring.data.gremlin.annotation.Graph;
   import com.microsoft.spring.data.gremlin.annotation.VertexSet;
   import lombok.Getter;
   import org.springframework.data.annotation.Id;
   
   import java.util.ArrayList;
   import java.util.List;
   
   @Graph
   public class Network {
   
       @Id
       private String id;
   
       public Network() {
           this.edges = new ArrayList<Object>();
           this.vertexes = new ArrayList<Object>();
       }
   
       @EdgeSet
       @Getter
       private List<Object> edges;
   
       @VertexSet
       @Getter
       private List<Object> vertexes;
   }
   ```

1. Zapisz i zamknij plik *Network.java*.

### <a name="define-a-set-of-classes-that-define-the-repositories-for-your-graph-database"></a>Definiowanie zestawu klas, które definiują repozytoria dla bazy danych grafu

1. Utwórz folder o nazwie *repository* w katalogu pakietu aplikacji, na przykład:

   `C:\SpringBoot\wingtiptoysdata\src\main\java\com\example\wingtiptoysdata\repository`

   — lub —

   `/users/example/home/wingtiptoysdata/src/main/java/com/example/wingtiptoysdata/repository`

1. Utwórz nowy plik Java o nazwie *NetworkRepository.java* w katalogu *repository*, następnie otwórz ten plik w edytorze tekstów i dodaj następujące wiersze:

   ```java
   package com.example.wingtiptoysdata.repository;
   
   import com.microsoft.spring.data.gremlin.repository.GremlinRepository;
   import com.example.wingtiptoysdata.domain.Network;
   import org.springframework.stereotype.Repository;
   
   @Repository
   public interface NetworkRepository extends GremlinRepository<Network, String> {
   }
   ```

1. Zapisz i zamknij plik *NetworkRepository.java*.

1. Utwórz nowy plik Java o nazwie *PersonRepository.java* w katalogu *repository*, następnie otwórz ten plik w edytorze tekstów i dodaj następujące wiersze:

   ```java
   package com.example.wingtiptoysdata.repository;
   
   import com.microsoft.spring.data.gremlin.repository.GremlinRepository;
   import com.example.wingtiptoysdata.domain.Person;
   import org.springframework.stereotype.Repository;
   
   @Repository
   public interface PersonRepository extends GremlinRepository<Person, String> {
   }
   ```

1. Zapisz i zamknij plik *PersonRepository.java*.

1. Utwórz nowy plik Java o nazwie *RelationRepository.java* w katalogu *repository*, następnie otwórz ten plik w edytorze tekstów i dodaj następujące wiersze:

   ```java
   package com.example.wingtiptoysdata.repository;
   
   import com.microsoft.spring.data.gremlin.repository.GremlinRepository;
   import com.example.wingtiptoysdata.domain.Relation;
   import org.springframework.stereotype.Repository;
   
   @Repository
   public interface RelationRepository extends GremlinRepository<Relation, String> {
   }
   ```

1. Zapisz i zamknij plik *RelationRepository.java*.

## <a name="build-and-test-your-app"></a>Kompilowanie i testowanie aplikacji

1. Otwórz wiersz polecenia i zmień katalog na folder, w którym znajduje się plik *pom.xml*. Na przykład:

   `cd C:\SpringBoot\wingtiptoysdata`

   — lub —

   `cd /users/example/home/wingtiptoysdata`

1. Skompiluj swoją aplikację Spring Boot przy użyciu narzędzia Maven i uruchom ją, na przykład:

   ```shell
   mvn clean package
   mvn spring-boot:run
   ```

1. W aplikacji zostanie wyświetlonych kilka komunikatów środowiska uruchomieniowego, a jeśli nie wystąpiły żadne błędy, można użyć witryny Azure Portal do wyświetlenia zawartości usługi Azure Cosmos DB. W tym celu kliknij pozycję **Data Explorer** (Eksplorator danych) na stronie właściwości bazy danych, następnie kliknij przycisk **Execute Gremlin Query** (Wykonaj zapytanie Gremlin) i wybierz pozycję z listy wyników, aby wyświetlić dane.

   ![Używanie Eksploratora dokumentów do wyświetlania danych][JV03]

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat oprogramowania Spring i platformy Azure, przejdź do centrum dokumentacji dotyczącej oprogramowania Spring na platformie Azure.

> [!div class="nextstepaction"]
> [Oprogramowanie Spring na platformie Azure](/azure/java/spring-framework)

### <a name="additional-resources"></a>Dodatkowe zasoby

Aby uzyskać więcej informacji na temat obsługi języka Gremlin oraz interfejsu Graph API na platformie Azure, zobacz następujące artykuły:

* [Wprowadzenie do usługi Azure Cosmos DB: interfejs Graph API](/azure/cosmos-db/graph-introduction)

* [Azure Cosmos DB Gremlin graph support](/azure/cosmos-db/gremlin-support) (Obsługa grafów języka Gremlin w usłudze Azure Cosmos DB)

* [Azure Cosmos DB: tworzenie bazy danych grafów przy użyciu języka Java i witryny Azure Portal](/azure/cosmos-db/create-graph-java)

* [Samouczek: Wykonywanie zapytań w interfejsie Graph API w usłudze Azure Cosmos DB przy użyciu języka Gremlin](/azure/cosmos-db/tutorial-query-graph)

* [Spring Data Gremlin Starter] (Szablon startowy narzędzia Spring Data Gremlin)

Aby uzyskać więcej informacji o korzystaniu z usługi Azure Cosmos DB i języka Java, zobacz następujące artykuły:

* [Dokumentacja usługi Azure Cosmos DB].

* [Azure Cosmos DB: tworzenie bazy danych dokumentów przy użyciu języka Java i witryny Azure Portal][Build a SQL API app with Java]

* [Spring Data for Azure Cosmos DB SQL API] (Projekt Spring Data do interfejsu API języka SQL usługi Azure Cosmos DB)

Aby uzyskać więcej informacji o korzystaniu z aplikacji Spring Boot na platformie Azure, zobacz następujące artykuły:

* [Deploy a Spring Boot Application to the Azure App Service](deploy-spring-boot-java-app-from-container-registry-using-maven-plugin.md) (Wdrażanie aplikacji Spring Boot w usłudze Azure App Service)

* [Running a Spring Boot Application on a Kubernetes Cluster in the Azure Container Service](deploy-spring-boot-java-app-on-kubernetes.md) (Uruchamianie aplikacji Spring Boot w klastrze Kubernetes w usłudze Azure Container Service)

Aby uzyskać więcej informacji na temat korzystania z platformy Azure z językiem Java, zapoznaj się z tematami [Azure dla deweloperów języka Java] i [Working with Azure DevOps and Java] (Praca z narzędziami Azure DevOps i językiem Java).

**[Platforma Spring]** jest rozwiązaniem open-source, które pomaga deweloperom języka Java tworzyć aplikacje na poziomie przedsiębiorstwa. Jednym z bardziej popularnych projektów opartych na tej platformie jest [Spring Boot], który oferuje uproszczoną metodę tworzenia autonomicznych aplikacji Java. Aby ułatwić deweloperom rozpoczęcie pracy z aplikacją Spring Boot, pod adresem <https://github.com/spring-guides/> udostępniono przykładowe pakiety aplikacji Spring Boot. Oprócz możliwości wyboru z listy podstawowych projektów Spring Boot narzędzie **[Spring Initializr]** pomaga deweloperom rozpocząć tworzenie niestandardowych aplikacji Spring Boot.

<!-- URL List -->

[Dokumentacja usługi Azure Cosmos DB]: /azure/cosmos-db/
[Azure dla deweloperów języka Java]: /azure/java/
[Build a SQL API app with Java]: /azure/cosmos-db/create-sql-api-java 
[Spring Data for Azure Cosmos DB SQL API]: https://azure.microsoft.com/blog/spring-data-azure-cosmos-db-nosql-data-access-on-azure/ (Projekt Spring Data do interfejsu API języka SQL usługi Azure Cosmos DB)
[Spring Data Gremlin Starter]: https://github.com/Microsoft/spring-data-gremlin (Szablon startowy narzędzia Spring Data Gremlin)
[Bezpłatne konto platformy Azure]: https://azure.microsoft.com/pricing/free-trial/
[Working with Azure DevOps and Java]: /azure/devops/ (Praca z narzędziami Azure DevOps i językiem Java)
[Korzyści dla subskrybentów MSDN]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Initializr]: https://start.spring.io/
[Platforma Spring]: https://spring.io/

<!-- IMG List -->

[AZ02]: ./media/configure-spring-data-gremlin-java-app-with-cosmos-db/AZ02.png
[AZ03]: ./media/configure-spring-data-gremlin-java-app-with-cosmos-db/AZ03.png
[AZ05]: ./media/configure-spring-data-gremlin-java-app-with-cosmos-db/AZ05.png
[AZ06]: ./media/configure-spring-data-gremlin-java-app-with-cosmos-db/AZ06.png
[AZ07]: ./media/configure-spring-data-gremlin-java-app-with-cosmos-db/AZ07.png
[AZ08]: ./media/configure-spring-data-gremlin-java-app-with-cosmos-db/AZ08.png

[SI01]: ./media/configure-spring-data-gremlin-java-app-with-cosmos-db/SI01.png
[SI03]: ./media/configure-spring-data-gremlin-java-app-with-cosmos-db/SI03.png

[RE01]: ./media/configure-spring-data-gremlin-java-app-with-cosmos-db/RE01.png
[RE02]: ./media/configure-spring-data-gremlin-java-app-with-cosmos-db/RE02.png

[PM02]: ./media/configure-spring-data-gremlin-java-app-with-cosmos-db/PM02.png

[JV01]: ./media/configure-spring-data-gremlin-java-app-with-cosmos-db/JV01.png
[JV02]: ./media/configure-spring-data-gremlin-java-app-with-cosmos-db/JV02.png
[JV03]: ./media/configure-spring-data-gremlin-java-app-with-cosmos-db/JV03.png
