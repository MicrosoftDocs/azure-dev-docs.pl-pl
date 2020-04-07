---
title: Jak używać interfejsu Spring Data R2DBC z usługą Azure Database for MySQL
description: Dowiedz się, jak używać interfejsu Spring Data R2DBC z bazą danych usługi Azure Database for MySQL.
documentationcenter: java
ms.date: 03/18/2020
ms.service: mysql
ms.tgt_pltfrm: multiple
ms.topic: article
ms.author: judubois
ms.openlocfilehash: f5c9f3d0cccad7f6e1a8b5ab199f841925f416cc
ms.sourcegitcommit: a65fa8dbb168bd39e225a293d9ee73d18ece1864
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366755"
---
# <a name="how-to-use-spring-data-r2dbc-with-azure-mysql"></a>Jak używać interfejsu Spring Data R2DBC z usługą Azure MySQL

W tym temacie przedstawiono tworzenie przykładowej aplikacji, która używa rozwiązania [Spring Data R2DBC](https://spring.io/projects/spring-data-r2dbc) do przechowywania informacji w bazie danych usługi [Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/) przy użyciu implementacji łączności R2DBC dla programu MySQL z [https://github.com/mirromutth/r2dbc-mysql](https://github.com/mirromutth/r2dbc-mysql).

Łączność [R2DBC](https://r2dbc.io/) wprowadza reaktywne interfejsy API do tradycyjnych relacyjnych baz danych. Można jej używać razem z platformą Spring WebFlux do tworzenia w pełni reaktywnych aplikacji Spring Boot, korzystając z nieblokujących interfejsów API i zapewniając lepszą skalowalność niż w przypadku klasycznego podejścia „jeden wątek na połączenie”.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure. Jeśli go nie masz, [skorzystaj z bezpłatnej wersji próbnej](https://azure.microsoft.com/free/).
- [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/quickstart) lub [interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Zalecamy usługę Azure Cloud Shell, aby móc logować się automatycznie i mieć dostęp do wszystkich potrzebnych narzędzi.
- [Java 8](https://www.azul.com/downloads/zulu/) (język uwzględniony w usłudze Azure Cloud Shell).
- [cURL](https://curl.haxx.se) lub podobne narzędzie HTTP do testowania funkcjonalności.

## <a name="prepare-the-working-environment"></a>Przygotowanie środowiska pracy

Najpierw skonfiguruj niektóre zmienne środowiskowe przy użyciu następujących poleceń:

```bash
AZ_RESOURCE_GROUP=r2dbc-workshop
AZ_DATABASE_NAME=<YOUR_DATABASE_NAME>
AZ_LOCATION=<YOUR_AZURE_REGION>
AZ_MYSQL_USERNAME=r2dbc
AZ_MYSQL_PASSWORD=<YOUR_MYSQL_PASSWORD>
AZ_LOCAL_IP_ADDRESS=<YOUR_LOCAL_IP_ADDRESS>
```

Zastąp symbole zastępcze następującymi wartościami używanymi w tym temacie:

- `<YOUR_DATABASE_NAME>`: Nazwa wystąpienia serwera MySQL. Powinna być unikatowa w obrębie całej platformy Azure.
- `<YOUR_AZURE_REGION>`: region świadczenia usługi Azure, który będzie używany. Domyślnie możesz użyć regionu `eastus`, ale zalecamy skonfigurowanie regionu znajdującego się bliżej Twojej lokalizacji. Pełną listę dostępnych regionów można uzyskać, wpisując `az account list-locations`.
- `<YOUR_MYSQL_PASSWORD>`: hasło serwera bazy danych MySQL. To hasło powinno zawierać co najmniej 8 znaków z trzech z następujących kategorii: wielkie litery angielskie, małe litery angielskie, cyfry (0–9) i znaki inne niż alfanumeryczne (!, $, #, % itp.).
- `<YOUR_LOCAL_IP_ADDRESS>`: adres IP komputera lokalnego, z którego zostanie uruchomiona aplikacja Spring Boot. Można go znaleźć, przechodząc pod adres [http://ipv4.icanhazip.com](http://ipv4.icanhazip.com) w przeglądarce.

Następnie utwórz grupę zasobów.

```azurecli
az group create \
    --name $AZ_RESOURCE_GROUP \
    --location $AZ_LOCATION \
    | jq
```

> [!NOTE]
> Używamy narzędzia `jq`, które jest instalowane domyślnie w usłudze [Azure Cloud Shell](https://shell.azure.com/), aby wyświetlać dane JSON i zwiększać ich czytelność.
> Jeśli nie lubisz tego narzędzia, możesz bezpiecznie usunąć fragment `| jq` ze wszystkich poleceń, których będziemy używać.

## <a name="create-an-azure-database-for-mysql"></a>Tworzenie usługi Azure Database for MySQL

Pierwszym krokiem jest utworzenie zarządzanego wystąpienia serwera MySQL.

> [!NOTE]
> 
> Więcej szczegółowych informacji na temat tworzenia baz danych MySQL można znaleźć w temacie [Create an Azure Database for MySQL server by using the Azure portal](/azure/mysql/quickstart-create-mysql-server-database-using-azure-portal) (Tworzenie serwera usługi Azure Database for MySQL przy użyciu witryny Azure Portal).

Będąc ciągle w wystąpieniu usługi [Azure Shell](https://shell.azure.com/), wykonaj następujący skrypt:

```azurecli
az mysql server create \
    --resource-group $AZ_RESOURCE_GROUP \
    --name $AZ_DATABASE_NAME \
    --location $AZ_LOCATION \
    --sku-name B_Gen5_1 \
    --storage-size 5120 \
    --admin-user $AZ_MYSQL_USERNAME \
    --admin-password $AZ_MYSQL_PASSWORD \
    | jq
```

To polecenie spowoduje utworzenie małego wystąpienia serwera MySQL.

### <a name="configure-a-firewall-rule-for-your-mysql-server-instance"></a>Konfigurowanie reguły zapory dla wystąpienia serwera MySQL

Wystąpienia usługi Azure Database for MySQL są domyślnie zabezpieczone: mają zaporę, która nie zezwala na żadne połączenie przychodzące. Aby móc korzystać z naszej bazy danych, musimy dodać regułę zapory, która umożliwi uzyskiwanie dostępu do serwera bazy danych z naszego lokalnego adresu IP.

Ponieważ skonfigurowaliśmy lokalny adres IP na początku tego artykułu, możesz otworzyć zaporę serwera, uruchamiając następujące polecenie:

```azurecli
az mysql server firewall-rule create \
    --resource-group $AZ_RESOURCE_GROUP \
    --name $AZ_DATABASE_NAME-database-allow-local-ip \
    --server $AZ_DATABASE_NAME \
    --start-ip-address $AZ_LOCAL_IP_ADDRESS \
    --end-ip-address $AZ_LOCAL_IP_ADDRESS \
    | jq
```

### <a name="configure-a-mysql-database"></a>Konfigurowanie bazy danych MySQL

Utworzony wcześniej serwer MySQL jest pusty: nie ma żadnej bazy danych, której można używać z naszą aplikacją Spring Boot. Utwórz nową bazę danych o nazwie `r2dbc`:

```azurecli
az mysql db create \
    --resource-group $AZ_RESOURCE_GROUP \
    --name r2dbc \
    --server-name $AZ_DATABASE_NAME \
    | jq
```

## <a name="create-a-reactive-spring-boot-application"></a>Tworzenie reaktywnej aplikacji Spring Boot

Aby utworzyć reaktywną aplikację Spring Boot, użyjemy narzędzia [Spring Initializr](https://start.spring.io/). Aplikacja, którą utworzymy, używa następujących zasobów:

- Spring Boot 2.3.0 M3
- Java 8 (ale również będzie działała z nowszymi wersjami, takimi jak Java 11)
- Następujące zależności: Spring Reactive Web (nazywane także „Spring WebFlux”) i Spring Data R2DBC.

### <a name="generate-the-application-using-spring-initializr"></a>Generowanie aplikacji przy użyciu narzędzia Spring Initializr

Wygeneruj tę aplikację przy użyciu wiersza polecenia, wpisując:

```bash
curl https://start.spring.io/starter.tgz -d dependencies=webflux,data-r2dbc -d baseDir=azure-r2dbc-workshop -d bootVersion=2.3.0.M3 -d javaVersion=8 | tar -xzvf -
```

### <a name="add-the-reactive-mysql-driver-implementation"></a>Dodawanie reaktywnej implementacji sterownika MySQL

Otwórz plik *pom.xml* z wygenerowanego projektu, aby dodać reaktywny sterownik MySQL z repozytorium [https://github.com/mirromutth/r2dbc-mysql](https://github.com/mirromutth/r2dbc-mysql).

Za zależnością `spring-boot-starter-webflux` dodaj następujący fragment kodu:

```xml
<dependency>
   <groupId>dev.miku</groupId>
   <artifactId>r2dbc-mysql</artifactId>
   <version>0.8.1.RELEASE</version>
   <scope>runtime</scope>
</dependency>
```

### <a name="configure-spring-boot-to-use-the-azure-database-for-mysql"></a>Konfigurowanie platformy Spring Boot do używania usługi Azure Database for MySQL

Otwórz plik *src/main/resources/application.properties* i dodaj następujący kod:

```properties
logging.level.org.springframework.data.r2dbc=DEBUG

spring.r2dbc.url=r2dbc:mysql://$AZ_DATABASE_NAME.mysql.database.azure.com:3306/r2dbc
spring.r2dbc.username=r2dbc@$AZ_DATABASE_NAME
spring.r2dbc.password=$AZ_MYSQL_USERNAME
```

- Zastąp dwie zmienne `$AZ_DATABASE_NAME` wartością skonfigurowaną na początku tego artykułu.
- Zastąp zmienną `$AZ_MYSQL_USERNAME` wartością skonfigurowaną na początku tego artykułu.

Teraz powinno być możliwe uruchomienie aplikacji przy użyciu dostarczonej otoki Maven:

```bash
./mvnw spring-boot:run
```

Poniżej znajduje się zrzut ekranu aplikacji uruchomionej po raz pierwszy:

![Uruchamianie aplikacji][R2DBC-MYSQL01]

### <a name="create-the-database-schema"></a>Tworzenie schematu bazy danych

Wewnątrz głównej klasy `DemoApplication` skonfiguruj nowe ziarno Spring; utworzy ono schemat bazy danych, którego będziesz używać:

```java
    @Bean
    public ConnectionFactoryInitializer initializer(ConnectionFactory connectionFactory) {
        ConnectionFactoryInitializer initializer = new ConnectionFactoryInitializer();
        initializer.setConnectionFactory(connectionFactory);
        ResourceDatabasePopulator populator = new ResourceDatabasePopulator(new ClassPathResource("schema.sql"));
        initializer.setDatabasePopulator(populator);
        return initializer;
    }
```

Ziarno Spring używa pliku o nazwie *schema.sql*. Utwórz ten plik w folderze *src/main/resources*:

```sql
DROP TABLE IF EXISTS todo;
CREATE TABLE todo (id SERIAL PRIMARY KEY, description VARCHAR(255), details VARCHAR(4096), done BOOLEAN);
```

Użyj następującego polecenia, aby zatrzymać aplikację i uruchomić ją ponownie. Aplikacja będzie teraz korzystać z utworzonej wcześniej bazy danych `r2dbc` i utworzy w niej tabelę `todo`.

```bash
./mvnw spring-boot:run
```

Oto zrzut ekranu przedstawiający utworzoną tabelę bazy danych:

   ![Tworzenie tabeli bazy danych][R2DBC-MYSQL02]

## <a name="code-the-application"></a>Kodowanie aplikacji

Następnie dodaj kod Java, który będzie używać łączności R2DBC do przechowywania danych w wystąpieniu serwera MySQL i pobierania ich.

Teraz utwórz nową klasę Java `Todo` obok klasy `DemoApplication`:

```java
package com.example.demo;

import org.springframework.data.annotation.Id;

public class Todo {

    public Todo() {
    }

    public Todo(String description, String details, boolean done) {
        this.description = description;
        this.details = details;
        this.done = done;
    }

    @Id
    private Long id;

    private String description;

    private String details;

    private boolean done;

    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getDescription() {
        return description;
    }

    public void setDescription(String description) {
        this.description = description;
    }

    public String getDetails() {
        return details;
    }

    public void setDetails(String details) {
        this.details = details;
    }

    public boolean isDone() {
        return done;
    }

    public void setDone(boolean done) {
        this.done = done;
    }
}
```

Ta klasa jest modelem domeny zamapowanym na utworzonej wcześniej tabeli `todo`.

Do zarządzania tą klasą potrzebne jest repozytorium. Zdefiniuj nowy interfejs `TodoRepository` w tym samym pakiecie:

```java
package com.example.demo;

import org.springframework.data.repository.reactive.ReactiveCrudRepository;

public interface TodoRepository extends ReactiveCrudRepository<Todo, Long> {
}
```

To repozytorium jest reaktywnym repozytorium zarządzanym przez interfejs Spring Data R2DBC.

Następnie zakończ aplikację, tworząc kontroler, który może przechowywać i pobierać dane. Zaimplementuj klasę `TodoController` w tym samym pakiecie i dodaj następujący kod:

```java
package com.example.demo;

import org.springframework.http.HttpStatus;
import org.springframework.web.bind.annotation.*;
import reactor.core.publisher.Flux;
import reactor.core.publisher.Mono;

@RestController
@RequestMapping("/")
public class TodoController {

    private final TodoRepository todoRepository;

    public TodoController(TodoRepository todoRepository) {
        this.todoRepository = todoRepository;
    }

    @PostMapping("/")
    @ResponseStatus(HttpStatus.CREATED)
    public Mono<Todo> createTodo(@RequestBody Todo todo) {
        return todoRepository.save(todo);
    }

    @GetMapping("/")
    public Flux<Todo> getTodos() {
        return todoRepository.findAll();
    }
}
```

Na koniec zatrzymaj działanie aplikacji i uruchom ją ponownie:

```bash
./mvnw spring-boot:run
```

## <a name="test-the-application"></a>Testowanie aplikacji

Do przetestowania aplikacji można użyć programu cURL.

Najpierw utwórz nowy element „todo” w bazie danych:

```bash
curl  --header "Content-Type: application/json" \
          --request POST \
          --data '{"description":"configuration","details":"congratulations, you have set up R2DBC correctly!","done": "true"}' \
          http://127.0.0.1:8080
```

To polecenie powinno zwrócić utworzony element:

```json
{"id":1,"description":"configuration","details":"congratulations, you have set up R2DBC correctly!","done":true}
```

Następnie pobierz dane przy użyciu nowego żądania programu cURL:

```bash
curl http://127.0.0.1:8080
```

To polecenie zwróci listę elementów „todo”, w tym utworzony element:

```json
[{"id":1,"description":"configuration","details":"congratulations, you have set up R2DBC correctly!","done":true}]
```

Oto zrzut ekranu przedstawiający te żądania programu cURL:

   ![Testowanie za pomocą programu cURL][R2DBC-MYSQL03]

Gratulacje! Utworzono w pełni aktywną aplikację Spring Boot, która używa łączności R2DBC do przechowywania i pobierania danych z usługi Azure Database for MySQL.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Aby wyczyścić wszystkie zasoby używane w ramach tego przewodnika Szybki start, usuń grupę zasobów:

```azurecli
az group delete \
    --name $AZ_RESOURCE_GROUP \
    --yes
```

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat oprogramowania Spring i platformy Azure, przejdź do centrum dokumentacji dotyczącej oprogramowania Spring na platformie Azure.

> [!div class="nextstepaction"]
> [Oprogramowanie Spring na platformie Azure](/azure/java/spring-framework)

### <a name="additional-resources"></a>Dodatkowe zasoby

Aby uzyskać więcej informacji na temat interfejsu Spring Data R2DBC, zobacz [dokumentację referencyjną](https://docs.spring.io/spring-data/r2dbc/docs/1.0.x/reference/html/#reference).

Aby uzyskać więcej informacji na temat korzystania z platformy Azure przy użyciu języka Java, zapoznaj się ze stronami [Platforma Azure dla deweloperów języka Java](/azure/java/) oraz [Praca z narzędziami Azure DevOps i językiem Java](/azure/devops/).

<!-- IMG List -->

[R2DBC-MYSQL01]: media/configure-spring-data-r2dbc-with-azure-mysql/create-mysql-01.png
[R2DBC-MYSQL02]: media/configure-spring-data-r2dbc-with-azure-mysql/create-mysql-02.png
[R2DBC-MYSQL03]: media/configure-spring-data-r2dbc-with-azure-mysql/create-mysql-03.png
