---
title: Przewodnik dewelopera usługi Azure Cosmos DB w rozwiązaniu Spring Data
description: W tym przewodniku opisano, na co należy zwrócić uwagę, korzystając z zestawu SDK Spring Data Azure Cosmos DB.
author: kushagraThapar
ms.author: kuthapar
ms.topic: conceptual
ms.date: 1/9/2019
ms.openlocfilehash: 6721a85ca00d277776545c5d717ccdb2948da207
ms.sourcegitcommit: d9f585bea70b01ba6657a75ea245d8519d4a5aad
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/03/2020
ms.locfileid: "77001153"
---
# <a name="spring-data-azure-cosmos-db-developers-guide"></a>Przewodnik dewelopera usługi Azure Cosmos DB w rozwiązaniu Spring Data

W tym temacie opisano funkcje usługi [Cosmos DB w rozwiązaniu Spring Data](https://github.com/microsoft/spring-data-cosmosdb) w przypadku korzystania z interfejsu API SQL. Ten temat zawiera również wskazówki dotyczące typowych problemów, obejść i czynności diagnostycznych.

[Azure Cosmos DB](/azure/cosmos-db/introduction) to globalnie dystrybuowana usługa bazy danych, która umożliwia deweloperom pracę z danymi przy użyciu różnych standardowych interfejsów API. Zestaw SDK Spring Data Cosmos DB oparty na platformie [Spring Data](https://spring.io/projects/spring-data) zapewnia integrację z usługą Azure Cosmos DB przy użyciu interfejsu API SQL. Informacje pomocy technicznej na temat innych interfejsów API można znaleźć w następujących tematach:

- [Jak używać interfejsu API usługi MongoDB w rozwiązaniu Spring Data z usługą Azure Cosmos DB](/azure/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
- [Jak używać interfejsu API Apache Cassandra w rozwiązaniu Spring Data z usługą Azure Cosmos DB](/azure/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
- [Jak używać szablonu startowego Spring Data Gremlin z interfejsem API języka SQL w usłudze Azure Cosmos DB](/azure/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)

Zestaw SDK Spring Data Cosmos DB jest dostępny w postaci rozwiązania open source w repozytorium [spring-data-cosmosdb](https://github.com/microsoft/spring-data-cosmosdb) w witrynie GitHub. To repozytorium zawiera aktywną listę [problemów](https://github.com/microsoft/spring-data-cosmosdb/issues), na której można zgłaszać błędy lub sprawdzać obejścia problemów, które zostały już zgłoszone. Na liście [wersji](https://github.com/microsoft/spring-data-cosmosdb/releases) można również sprawdzić, czy problem nie został rozwiązany w nowszej wersji. Zestaw SDK Spring Data Cosmos DB w wersjach 2.2.x obsługuje repozytoria spring-data-common w wersji 2.2.0.RELEASE, natomiast wersje 2.1.x zestawu SDK obsługują repozytorium spring-data-common w wersji 2.1.0.RELEASE.

## <a name="available-features"></a>Dostępne funkcje

W poniższych sekcjach opisano aktualnie dostępne funkcje.

### <a name="crudrepository-and-reactivecrudrepository-support"></a>Obsługa interfejsów CrudRepository i ReactiveCrudRepository

Zestaw SDK Spring Data Cosmos DB udostępnia interfejsy `CosmosRepository` i `ReactiveCosmosRepository`, które rozszerzają interfejsy `CrudRepository` i `ReactiveCrudRepository` rozwiązania Spring Data.

W poniższym przykładzie pokazano, jak rozszerzyć te interfejsy.

```java
@Repository
public interface SampleRepository extends CosmosRepository<SampleEntity, String> {
    List<SampleEntity> findByName(String name);
}

@Repository
public interface ReactiveSampleRepository extends ReactiveCosmosRepository<SampleEntity, String> {
    Flux<SampleEntity> findByName(String name);
}
```

W zależności od sposobu użycia należy osobno włączyć oba repozytoria w klasie `Configuration`. Przykład:

```java
@Configuration
@PropertySource(value = {"classpath:application.properties"})
@EnableCosmosRepositories
@EnableReactiveCosmosRepositories
public class TestRepositoryConfig extends AbstractCosmosConfiguration {
    ...
}
```

### <a name="define-a-simple-entity"></a>Definiowanie prostej jednostki

Jednostki można definiować, dodając adnotację `@Document` i określając właściwości związane z kolekcją, takie jak nazwa kolekcji, jednostki żądań (RU), czas wygaśnięcia i flaga automatycznego tworzenia kolekcji.

Domyślnie nazwa kolekcji będzie nazwą klasy user-domain. Aby ją dostosować, dodaj do klasy domeny adnotację `@Document(collection="myCustomCollectionName")`. Pole kolekcji obsługuje również wyrażenia języka [Spring Expression Language](https://docs.spring.io/spring/docs/3.0.x/reference/expressions.html) (SpEL), dzięki czemu można programowo udostępniać nazwy kolekcji za pośrednictwem właściwości konfiguracji. Na przykład można używać wyrażeń, takich jak `collection = "${dynamic.collection.name}"` i `collection = "#{@someBean.getCollectionName()}"`.

Istnieją dwa sposoby mapowania pola w klasie domeny na pole `id` dokumentu Azure Cosmos DB:

- Dodaj do pola adnotację `@Id`.
- Ustaw nazwę pola na `id`.

W poniższym przykładzie przedstawiono użycie adnotacji `@Document` i `@Id`.

```java
@Document(collection = "myCollection")
class MyDocument {

    @Id
    private String myId;

    @PartitionKey
    private String data;

    @Version
    private String _etag;
}
```

Domyślnie właściwość `IndexingPolicy` jest ustawiana przez usługę platformy Azure. Aby dostosować ją, dodaj do klasy domeny adnotację `@DocumentIndexingPolicy`. Ta adnotacja ma cztery atrybuty:

```java
boolean automatic;     // Indicates whether the indexing policy is automatic.
IndexingMode mode;     // The indexing policy mode; the options are Consistent, Lazy, or None.
String[] includePaths; // Included paths for indexing.
String[] excludePaths; // Excluded paths for indexing.
```

Zestaw SDK obsługuje również partycjonowanie. Aby uzyskać więcej informacji, zobacz [Partycjonowanie i skalowanie w poziomie w usłudze Azure Cosmos DB](/azure/cosmos-db/partition-data). Aby określić, że pole klasy domeny ma być polem klucza partycji, dodaj do niego adnotację `@PartitionKey`. Następnie podczas wykonywania operacji CRUD określ wartość partycji.

W poniższym przykładzie pokazano, jak używać adnotacji `@PartitionKey` podczas wykonywania operacji CRUD.

```java
@Document(ru = "400")
public class Address {
    @Id
    String postalCode;

    @PartitionKey
    String city;

    String street;
    String country;
    String phoneNumber;

    ...
}

class AddressService {

    @Autowired
    AddressRepository repository;

    final Address newAddress = new Address("12345", "city");

    // There's no need to specify a partition key in the save operation.
    repository.save(updatedAddress);

    // Provide a partition key when performing a find-by-id operation.
    final Optional<Address> addressById = repository.findById("12345", new PartitionKey("city"));

    final Address foundAddress = addressById.get();

    // Provide a partition key when performing a delete-by-id operation.
    repository.deleteById(foundAddress.getPostalCode(), new PartitionKey(foundAddress.getCity())); 
}
```

Zestaw SDK obsługuje również operacje znajdowania zapytań niestandardowych rozwiązania Spring Data, takie jak `findByAFieldAndBField`. Aby uzyskać więcej informacji, zobacz [Defining Query Methods](https://docs.spring.io/spring-data/commons/docs/current/reference/html/#repositories.query-methods.details) (Definiowanie metod zapytań) w dokumentacji platformy Spring.

## <a name="best-practices"></a>Najlepsze rozwiązania

W poniższych sekcjach opisano najlepsze rozwiązania dotyczące korzystania z zestawu SDK.

### <a name="configuring-the-application"></a>Konfigurowanie aplikacji

Aby skonfigurować aplikację, wykonaj następujące kroki:

1. Rozwiń klasę `AbstractCosmosConfiguration`, aby określić konfigurację aplikacji (klucz Cosmos DB, adres URL, nazwę bazy danych itd.).
1. Dodaj adnotację `@Configuration`.
1. W zależności od sposobu użycia repozytorium dodaj jedną lub obie adnotacje `@EnableCosmosRepositories` i `@EnableReactiveCosmosRepositories`.

Funkcja `CosmosKeyCredential` umożliwia wymianę kluczy na bieżąco. Klucze można przełączać za pomocą metody `switchToSecondaryKey`.

W poniższym przykładowym kodzie przedstawiono konfigurację aplikacji i pokazano użycie metody `switchToSecondaryKey`.

```java
@Configuration
@EnableCosmosRepositories
public class AppConfiguration extends AbstractCosmosConfiguration {

    @Value("${azure.cosmosdb.uri}")
    private String uri;

    @Value("${azure.cosmosdb.key}")
    private String key;

    @Value("${azure.cosmosdb.secondaryKey}")
    private String secondaryKey;

    @Value("${azure.cosmosdb.database}")
    private String dbName;

    @Value("${azure.cosmosdb.populateQueryMetrics}")
    private boolean populateQueryMetrics;

    private CosmosKeyCredential cosmosKeyCredential;

    @Bean
    public CosmosDBConfig getConfig() {
        this.cosmosKeyCredential = new CosmosKeyCredential(key);
        CosmosDbConfig cosmosdbConfig = CosmosDBConfig.builder(uri,
            this.cosmosKeyCredential, dbName).build();
        cosmosdbConfig.setPopulateQueryMetrics(populateQueryMetrics);
        cosmosdbConfig.setResponseDiagnosticsProcessor(new ResponseDiagnosticsProcessorImplementation());
        return cosmosdbConfig;
    }

    public void switchToSecondaryKey() {
        this.cosmosKeyCredential.key(secondaryKey);
    }
}
```

Konfigurację można również dostosować, aby zmienić tryb połączenia, maksymalny rozmiar puli połączeń, limit czasu żądania itd., jak pokazano w poniższym przykładzie.

```java
public CosmosDBConfig getConfig() {

    this.cosmosKeyCredential = new CosmosKeyCredential(key);
    ConnectionPolicy customizedConnectionPolicy = new ConnectionPolicy();

    // Set the connection mode to Direct (TCP).
    customizedConnectionPolicy.setConnectionMode(ConnectionMode.DIRECT);

    // Set the maximum number of HTTP/TCP connections to 1000 per application.
    customizedConnectionPolicy.setMaxPoolSize(1000);

    // Set the request timeout to 10 seconds.
    customizedConnectionPolicy.requestTimeoutInMillis(10000);

    // Set the idle connection timeout to two minutes.
    customizedConnectionPolicy.idleConnectionTimeoutInMillis(120000);
    CosmosDBConfig cosmosDbConfig = CosmosDBConfig.builder(uri,   this.cosmosKeyCredential, dbName)
                                                  .connectionPolicy  (customizedConnectionPolic  y)
                                                  .build();
    return cosmosDbConfig;
}
```

### <a name="response-diagnostics-and-query-metrics"></a>Diagnostyka odpowiedzi i metryki zapytań

Wersja 2.2.x zestawu SDK Spring Data Cosmos DB obsługuje ciąg diagnostyki odpowiedzi i metryki zapytań.

Aby włączyć metryki zapytań, ustaw flagę `populateQueryMetrics` na **true** w pliku `application.properties`. Następnie rozszerz interfejs `ResponseDiagnosticsProcessor` i zaimplementuj metodę `processResponseDiagnostics`, aby rejestrować informacje diagnostyczne. Na koniec przekaż wystąpienie implementacji do metody `CosmosDbConfig.setResponseDiagnosticsProcessor`. Poniższy kod przedstawia przykładową implementację.

```java
@Configuration
@EnableCosmosRepositories
public class AppConfiguration extends AbstractCosmosConfiguration {

    ...

    @Value("${azure.cosmosdb.populateQueryMetrics}")
    private boolean populateQueryMetrics;

    private static class ResponseDiagnosticsProcessorImplementation implements ResponseDiagnosticsProcessor {

        @Override
        public void processResponseDiagnostics(@Nullable ResponseDiagnostics responseDiagnostics) {
            log.info("Response Diagnostics {}", responseDiagnostics);
        }
    }

    @Bean
    public CosmosDBConfig getConfig() {
        this.cosmosKeyCredential = new CosmosKeyCredential(key);
        CosmosDbConfig cosmosdbConfig = CosmosDBConfig.builder(uri, this.cosmosKeyCredential, dbName).build();
        cosmosdbConfig.setPopulateQueryMetrics(populateQueryMetrics);
        cosmosdbConfig.setResponseDiagnosticsProcessor(new ResponseDiagnosticsProcessorImplementation());
        return cosmosdbConfig;
    }
}
```

### <a name="pagination-and-sorting"></a>Stronicowanie i sortowanie

Zestaw SDK Spring Data Cosmos DB obsługuje stronicowanie i sortowanie w rozwiązaniu Spring Data. Aby uzyskać więcej informacji, zobacz [Special parameter handling](https://docs.spring.io/spring-data/commons/docs/current/reference/html/#repositories.special-parameters) (Obsługa parametrów specjalnych) w dokumentacji platformy Spring.

W oparciu o dostępne jednostki żądań (RU) na koncie bazy danych usługa Cosmos DB może zwracać dokumenty o rozmiarze nie większym od żądanego. Aby uzyskać więcej informacji, zobacz [Jednostki żądań w usłudze Azure Cosmos DB](/azure/cosmos-db/request-units).

Nie należy polegać na wartości `totalPageSize`, ponieważ liczba zwracanych dokumentów w każdej iteracji jest zmienna. W zamian należy wykonać iterację po obiekcie `Pageable`, jak pokazano w poniższym przykładzie.

```java
final Sort sort = Sort.by(Sort.Direction.DESC, "name");
final CosmosPageRequest pageRequest = new CosmosPageRequest(0, pageSize,   null, sort);
Page<T> page = tRepository.findAll(pageRequest);
List<T> pageContent = page.getContent();
while(page.hasNext()) {
    Pageable nextPageable = page.nextPageable();
    page = repository.findAll(nextPageable);
    pageContent = page.getContent();
}
```

## <a name="common-issues-and-workarounds"></a>Typowe problemy i ich rozwiązania

W poniższych sekcjach opisano problemy, na które należy zwrócić uwagę, korzystając z zestawu SDK Spring Data Cosmos DB.

### <a name="getting-the-correct-cosmos-db-configuration"></a>Pobieranie poprawnej konfiguracji usługi Cosmos DB

Rozszerzanie interfejsu `AbstractCosmosConfiguration` może być trudne ze względu na różne adnotacje i konfiguracje obecne w klasie. Najczęstszy problem dotyczy adnotacji `Enable Repositories`.

Jeśli repozytoria rozszerzają interfejs `CosmosRepository`, dodaj adnotację `@EnableCosmosRepositories`. Jeśli repozytoria rozszerzają interfejs `ReactiveCosmosRepository`, dodaj adnotację `@EnableReactiveCosmosRepositories`. W poniższym przykładzie pokazano sposób użycia tych adnotacji.

```java
@Configuration
@PropertySource(value = {"classpath:application.properties"})
@EnableCosmosRepositories
@EnableReactiveCosmosRepositories
public class TestRepositoryConfig extends AbstractCosmosConfiguration {
    ...
}
```

Podczas tworzenia lub dostosowywania ziarna `CosmosDBConfig` użyj obiektu `CosmosKeyCredential` zamiast bezpośrednio klucza.

Funkcja `CosmosKeyCredential` umożliwia wymianę kluczy na bieżąco. Klucze można przełączać za pomocą metody `switchToSecondaryKey`.

Funkcja `CosmosKeyCredential` powinna być pojedynczym obiektem, ponieważ zestaw SDK Cosmos DB używa tego samego obiektu wewnętrznie do wykrywania zmian wartości klucza w tym obiekcie.

### <a name="custom-query-execution"></a>Wykonywanie zapytań niestandardowych

Funkcja adnotacji zapytań nie jest jeszcze obsługiwana przez zestaw SDK Spring Data Cosmos DB. Do tego czasu zapytania niestandardowe i złożone można wykonywać bezpośrednio w ziarnie `cosmosClient` uwidocznionym przez kontekst aplikacji platformy Spring.

Poniższy kod przedstawia prosty przykład, jak wykonać przesunięcie i ograniczyć zapytania przy użyciu ziarna `cosmosClient`.

```java
final FeedOptions feedOptions = new FeedOptions();

// Enable cross-partition queries.
feedOptions.enableCrossPartitionQuery(true);

// Set the page size.
feedOptions.maxItemCount(20);

// Set the number of parallel operations on the client-side SDK when executing parallel queries.
feedOptions.maxDegreeOfParallelism(2);

// Populate query metrics from Cosmos DB.
feedOptions.populateQueryMetrics(true);

final String query = "SELECT * from c OFFSET " + skipCount + " LIMIT " + takeCount;

final CosmosClient cosmosClient = applicationContext.getBean(CosmosClient.class);

Flux<FeedResponse<CosmosItemProperties>> feedResponseFlux =
    cosmosClient.getDatabase(databaseId)
                .getContainer(collectionId)
                .queryItems(query, feedOptions);
    feedResponseFlux.subscribeOn(Schedulers.parallel())
                    .flatMap(feedResponse -> {
                        List<CosmosItemProperties> results =
                        feedResponseFlux.results();
                        log.info("Results are {}", results);
                        return feedResponse;
                    })
                    .subscribe();
```

### <a name="enable-diagnostics-and-query-metrics"></a>Włączanie diagnostyki i metryk zapytań

Podczas debugowania przydaje się ciąg diagnostyki odpowiedzi i metryki zapytań z zestawu SDK Cosmos DB. Zestaw SDK Cosmos DB rejestruje ciąg diagnostyki odpowiedzi po stronie klienta. Zaplecze rejestruje metryki zapytań i dostarcza je do zestawu SDK Cosmos DB.

Przy każdym wywołaniu interfejsu API w zestawie SDK Spring Data Cosmos DB jest wywoływana metoda `ResponseDiagnosticsProcessor.processResponseDiagnostics`. Dlatego implementacja musi być wolna od błędów i nieskomplikowana, aby mogła zapewniać wysoką wydajność. Na przykład w tej metodzie nie należy rejestrować pełnego zestawu informacji diagnostycznych, ponieważ ilość informacji mogłaby wpłynąć na znaczny spadek wydajności. Aby uniknąć wpływu na wydajność aplikacji, należy również używać poziomu rejestrowania `Debug`.

Poniższy kod przedstawia przykładowy sposób implementacji interfejsu `ResponseDiagnosticsProcessor`.

```java
private static class ResponseDiagnosticsProcessorImplementation implements ResponseDiagnosticsProcessor {

    @Override
    public void processResponseDiagnostics(@Nullable ResponseDiagnostics responseDiagnostics) {

        // To log everything:
        if (log.isDebugEnabled()) {
            log.debug("Response diagnostics {}", responseDiagnostics);
        }

        // To log Cosmos DB response diagnostics:
        if (responseDiagnostics != null && log.isDebugEnabled()) {
            CosmosResponseDiagnostics cosmosResponseDiagnostics = responseDiagnostics.getCosmosResponseDiagnostics();
            log.debug("Cosmos DB response diagnostics {}", cosmosResponseDiagnostics);
        }

        // To log just the request latency:
        if (responseDiagnostics != null && log.isDebugEnabled()) {
            CosmosResponseDiagnostics cosmosResponseDiagnostics = responseDiagnostics.getCosmosResponseDiagnostics();
            log.debug("Request latency {}", cosmosResponseDiagnostics.requestLatency());
        }

        // To log query metrics:
        if (responseDiagnostics != null && log.isDebugEnabled()) {
            FeedResponseDiagnostics feedResponseDiagnostics =
                responseDiagnostics.getFeedResponseDiagnostics();
            log.debug("Query metrics {}", feedResponseDiagnostics);
        }
    }
}
```

## <a name="how-to-troubleshoot"></a>Sposoby rozwiązywania problemów

W poniższych sekcjach opisano sposoby rozwiązywania typowych problemów.

### <a name="connection-issues"></a>Problemy z połączeniem

Jeśli występują problemy z połączeniem, upewnij się, że w klasie konfiguracji znajdują się wszystkie wymagane adnotacje i są one poprawne, zgodnie z opisem w sekcji [Pobieranie poprawnej konfiguracji usługi Cosmos DB](#getting-the-correct-cosmos-db-configuration).

### <a name="api-exceptions"></a>Wyjątki interfejsu API

Wersja 2.2.1 zestawu SDK Spring Data Cosmos DB zawiera następujące ulepszenia obsługi wyjątków:

- Wszystkie interfejsy API zwracają wyjątek `CosmosDBAccessException`, który uwidacznia pole `cosmosClientException` za pomocą metody pobierającej.
- Zestaw SDK Cosmos DB zgłasza wyjątek `CosmosClientException`, za pomocą którego można zaimplementować dowolną logikę ponawiania po stronie klienta.
- Typowymi wyjątkami wymagającymi ponowienia są te, które zawierają komunikaty `Resource already exists`, `Request rate too large`, `Request timeout exception` itd.

### <a name="api-or-query-slowness"></a>Powolne działanie zapytań lub interfejsu API

Jeśli w interfejsach API lub w czasie wykonywania zapytań występują duże opóźnienia, wypróbuj rejestrowanie ciągów diagnostyki i metryk zapytań zgodnie z opisem w sekcji [Włączanie diagnostyki i metryk zapytań](#enable-diagnostics-and-query-metrics). Sprawdź użycie procesora, przepustowość sieci i miejsce na dysku we/wy, które mogą być głównymi przyczynami spowolnienia po stronie klienta.
