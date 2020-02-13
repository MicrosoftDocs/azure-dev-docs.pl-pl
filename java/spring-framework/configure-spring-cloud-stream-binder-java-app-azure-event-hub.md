---
title: Tworzenie aplikacji integratora strumienia Spring Cloud przy użyciu usługi Azure Event Hubs
description: Dowiedz się, jak skonfigurować opartą na języku Java aplikację integratora strumienia Spring Cloud utworzoną za pomocą narzędzia Spring Boot Initializr przy użyciu usługi Azure Event Hubs.
services: event-hubs
documentationcenter: java
ms.date: 12/19/2018
ms.service: event-hubs
ms.tgt_pltfrm: na
ms.topic: article
ms.openlocfilehash: 9eef2c48b076ae0fc84aea16bb3e5b7bba17d744
ms.sourcegitcommit: b3b7dc6332c0532f74d210b2a5cab137e38a6750
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2019
ms.locfileid: "76999767"
---
# <a name="how-to-create-a-spring-cloud-stream-binder-application-with-azure-event-hubs"></a>Tworzenie aplikacji integratora strumienia Spring Cloud przy użyciu usługi Azure Event Hubs

W tym artykule opisano, jak skonfigurować opartą na języku Java aplikację integratora strumienia Spring Cloud utworzoną za pomocą narzędzia Spring Boot Initializer przy użyciu usługi Azure Event Hubs.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać czynności opisane w tym artykule, wymagane są:

* Subskrypcja platformy Azure — jeśli nie masz jeszcze subskrypcji platformy Azure, możesz uaktywnić [korzyści dla subskrybentów MSDN] lub utworzyć [bezpłatne konto platformy Azure].
* Obsługiwany zestaw Java Development Kit (JDK). Aby uzyskać więcej informacji na temat zestawów JDK dostępnych do użycia podczas tworzenia aplikacji na platformie Azure, zobacz <https://aka.ms/azure-jdks>.
* Narzędzie [Apache Maven](http://maven.apache.org/) w wersji 3.0 lub nowszej.

> [!IMPORTANT]
>
> Aby wykonać kroki opisane w tym artykule, potrzebny jest program Spring Boot w wersji 2.0 lub nowszej.
>

## <a name="create-an-azure-event-hub-using-the-azure-portal"></a>Tworzenie centrum zdarzeń platformy Azure za pomocą witryny Azure Portal

Poniższa procedura umożliwia utworzenie centrum zdarzeń platformy Azure.

### <a name="create-an-azure-event-hub-namespace"></a>Tworzenie przestrzeni nazw centrum zdarzeń Azure

1. Przejdź do witryny Azure Portal pod adresem <https://portal.azure.com/> i zaloguj się.

1. Kliknij pozycję **+Utwórz zasób**, a następnie wyszukaj ciąg *Event Hubs**.

1. Kliknij pozycję **Utwórz**.

   ![Tworzenie przestrzeni nazw centrum zdarzeń Azure][IMG01]

1. Na stronie **Tworzenie przestrzeni nazw** wprowadź następujące informacje:

   * Wprowadź unikatową **nazwę**, która stanie się częścią identyfikatora URI przestrzeni nazw centrum zdarzeń. Na przykład: w przypadku wprowadzenia **wingtiptoys** w polu **Nazwa** identyfikator URI przyjmie wartość *wingtiptoys.servicebus.windows.net*.
   * Warstwa cenowa.
   * Wybierz **subskrypcję**, której chcesz użyć do przestrzeni nazw.
   * Określ, czy chcesz utworzyć nową **grupę zasobów** dla przestrzeni nazw, czy wybrać istniejącą grupę zasobów.
   * Określ **lokalizację** dla przestrzeni nazw centrum zdarzeń.
   * Możesz również określić **jednostki przepływności** dla przestrzeni nazw.

   ![Określanie opcji przestrzeni nazw centrum zdarzeń Azure][IMG02]

1. Po określeniu opcji wymienionych powyżej kliknij pozycję **Utwórz**, aby utworzyć przestrzeń nazw.

## <a name="create-an-azure-event-hub-in-your-namespace"></a>Tworzenie centrum zdarzeń Azure w przestrzeni nazw

Po wdrożeniu przestrzeni nazw możesz utworzyć w niej centrum zdarzeń.

1. Nawiguj do przestrzeni nazw utworzonej w poprzednim kroku.

1. Kliknij pozycję **+ Centrum zdarzeń** w górnym pasku menu.

1. Nazwij centrum zdarzeń.

1. Kliknij pozycję **Utwórz**.

   ![Tworzenie centrum zdarzeń][IMG05]

### <a name="create-an-azure-storage-account-for-your-event-hub-checkpoints"></a>Utwórz konto usługi Azure Storage dla punktów kontrolnych centrum zdarzeń

Poniższa procedura umożliwia utworzenie konta magazynu dla punktów kontrolnych centrum zdarzeń.

1. Przejdź do witryny Azure Portal pod adresem <https://portal.azure.com/>.

1. Kliknij pozycję **+Utwórz**, następnie pozycję **Magazyn**, a następnie kliknij pozycję **Konto magazynu**.

1. Na stronie **Tworzenie konta magazynu** wprowadź następujące informacje:

   * Wybierz **subskrypcję**, której chcesz użyć dla konta magazynu.
   * Określ, czy chcesz utworzyć nową **grupę zasobów** dla konta magazynu, czy wybrać istniejącą grupę zasobów.
   * Podaj unikatową **nazwę** konta magazynu.
   * Wybierz **lokalizację** konta magazynu.

   ![Określanie opcji konta usługi Azure Storage][IMG08]

1. Po określeniu opcji wymienionych powyżej kliknij przycisk **Przejrzyj i utwórz**, aby utworzyć konto magazynu.

1. Przejrzyj specyfikacje i kliknij przycisk **Utwórz**.  Wdrożenie potrwa kilka minut.

## <a name="create-a-simple-spring-boot-application-with-the-spring-initializr"></a>Tworzenie prostej aplikacji Spring Boot za pomocą narzędzia Spring Initializr

Poniższa procedura umożliwia utworzenie aplikacji Spring Boot.

1. Przejdź do <https://start.spring.io/>.

1. Określ następujące opcje:

   * Wygeneruj projekt **Maven** z użyciem języka **Java**.
   * Określ wersję narzędzia **Spring Boot** równą lub większą niż 2.0.
   * Określ nazwy **Grupa** i **Artefakt** dla swojej aplikacji.
   * Dodaj zależność **Internet**.

      ![Podstawowe opcje narzędzia Spring Initializr][SI01]

   > [!NOTE]
   >
   > Narzędzie Spring Initializr używa nazw z pól **Group** (Grupa) i **Artifact** (Artefakt) do utworzenia nazwy pakietu. Na przykład: *com.wingtiptoys.eventhub*.
   >

1. Po określeniu opcji wymienionych powyżej kliknij pozycję **Generuj projekt**.

1. Po wyświetleniu monitu pobierz projekt do ścieżki na komputerze lokalnym.

1. Po wyodrębnieniu plików w systemie lokalnym Twoja prosta aplikacja Spring Boot będzie gotowa do edycji.

## <a name="configure-your-spring-boot-app-to-use-the-azure-event-hub-starter"></a>Konfigurowanie aplikacji Spring Boot do korzystania z szablonu startowego centrum zdarzeń Azure

1. Znajdź plik *pom.xml* w katalogu głównym aplikacji. Na przykład:

   `C:\SpringBoot\eventhub\pom.xml`

   — lub —

   `/users/example/home/eventhub/pom.xml`

1. Otwórz plik *pom.xml* w edytorze tekstów, a następnie dodaj szablon startowy integratora strumienia Spring Cloud centrum zdarzeń Azure do listy `<dependencies>`:

   ```xml
   <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>spring-cloud-azure-eventhubs-stream-binder</artifactId>
      <version>1.1.0.RC2</version>
   </dependency>
   ```

1. Zapisz i zamknij plik *pom.xml*.

## <a name="create-an-azure-credential-file"></a>Tworzenie pliku poświadczeń platformy Azure

1. Otwórz wiersz polecenia.

1. Nawiguj do katalogu *resources* aplikacji Spring Boot. Na przykład:

   ```shell
   cd C:\SpringBoot\eventhub\src\main\resources
   ```

   — lub —

   ```shell
   cd /users/example/home/eventhub/src/main/resources
   ```

1. Zaloguj się do swojego konta platformy Azure:

   ```azurecli
   az login
   ```

1. Wyświetl listę swoich subskrypcji:

   ```azurecli
   az account list
   ```
   Platforma Azure zwróci listę subskrypcji i trzeba będzie skopiować identyfikator GUID subskrypcji, która ma zostać użyta. Na przykład:

   ```json
   [
     {
       "cloudName": "AzureCloud",
       "id": "11111111-1111-1111-1111-111111111111",
       "isDefault": true,
       "name": "Converted Windows Azure MSDN - Visual Studio Ultimate",
       "state": "Enabled",
       "tenantId": "22222222-2222-2222-2222-222222222222",
       "user": {
         "name": "gena.soto@wingtiptoys.com",
         "type": "user"
       }
     }
   ]
   ```
   
1. Określ identyfikator GUID subskrypcji, która ma być używana z platformą Azure. Na przykład:

   ```azurecli
   az account set -s 11111111-1111-1111-1111-111111111111
   ```

1. Utwórz plik poświadczeń platformy Azure:

   ```azurecli
   az ad sp create-for-rbac --sdk-auth > my.azureauth
   ```

   To polecenie utworzy plik *my.azureauth* w katalogu *resources* z zawartością podobną do podanej w poniższym przykładzie:

   ```json
   {
     "clientId": "33333333-3333-3333-3333-333333333333",
     "clientSecret": "44444444-4444-4444-4444-444444444444",
     "subscriptionId": "11111111-1111-1111-1111-111111111111",
     "tenantId": "22222222-2222-2222-2222-222222222222",
     "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
     "resourceManagerEndpointUrl": "https://management.azure.com/",
     "activeDirectoryGraphResourceId": "https://graph.windows.net/",
     "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
     "galleryEndpointUrl": "https://gallery.azure.com/",
     "managementEndpointUrl": "https://management.core.windows.net/"
   }
   ```

## <a name="configure-your-spring-boot-app-to-use-your-azure-event-hub"></a>Konfigurowanie aplikacji Spring Boot do korzystania z centrum zdarzeń Azure

1. Znajdź plik *application.properties* w katalogu *resources* aplikacji. Na przykład:

   `C:\SpringBoot\eventhub\src\main\resources\application.properties`

   — lub —

   `/users/example/home/eventhub/src/main/resources/application.properties`

2. Otwórz plik *application.properties* w edytorze tekstów, a następnie dodaj w pliku poniższe wiersze kodu i zastąp przykładowe wartości odpowiednimi właściwościami dla centrum zdarzeń:

   ```yaml
   spring.cloud.azure.credential-file-path=my.azureauth
   spring.cloud.azure.resource-group=wingtiptoysresources
   spring.cloud.azure.region=West US
   spring.cloud.azure.eventhub.namespace=wingtiptoysnamespace
   spring.cloud.azure.eventhub.checkpoint-storage-account=wingtiptoysstorage
   spring.cloud.stream.bindings.input.destination=wingtiptoyshub
   spring.cloud.stream.bindings.input.group=$Default
   spring.cloud.stream.eventhub.bindings.input.consumer.checkpoint-mode=MANUAL
   ```
   Gdzie:

   |                          Pole                           |                                                                                   Opis                                                                                    |
   |----------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
   |        `spring.cloud.azure.credential-file-path`         |                                                    Określa plik poświadczeń Azure utworzony wcześniej w tym samouczku.                                                    |
   |           `spring.cloud.azure.resource-group`            |                                                      Określa grupę zasobów Azure, która zawiera Twoje centrum zdarzeń Azure.                                                      |
   |               `spring.cloud.azure.region`                |                                           Określa region geograficzny podany podczas tworzenia konta centrum zdarzeń Azure.                                            |
   |         `spring.cloud.azure.eventhub.namespace`          |                                          Określa unikatową nazwę podaną podczas tworzenia przestrzeni nazw centrum zdarzeń Azure.                                           |
   | `spring.cloud.azure.eventhub.checkpoint-storage-account` |                                                    Określa konto usługi Azure Storage utworzone wcześniej w tym samouczku.                                                    |
   |     `spring.cloud.stream.bindings.input.destination`     |                            Określa miejsce docelowe elementu wejściowego centrum zdarzeń Azure, którym w tym przypadku jest centrum utworzone wcześniej w tym samouczku.                            |
   |       `spring.cloud.stream.bindings.input.group `        | Określa grupę odbiorców z centrum zdarzeń Azure, którą można ustawić na „$Default” w celu użycia podstawowej grupy odbiorców utworzonej podczas tworzenia centrum zdarzeń Azure. |
   |    `spring.cloud.stream.bindings.output.destination`     |                               Określa miejsce docelowe elementu wyjściowego centrum zdarzeń Azure, które w tym samouczku będzie takie samo, jak miejsce docelowe elementu wejściowego.                               |


3. Zapisz i zamknij plik *application.properties*.

## <a name="add-sample-code-to-implement-basic-event-hub-functionality"></a>Dodawanie przykładowego kodu w celu zaimplementowania podstawowej funkcjonalności centrum zdarzeń

W tej sekcji utworzysz niezbędne klasy w języku Java do wysyłania zdarzeń do centrum zdarzeń.

### <a name="modify-the-main-application-class"></a>Modyfikowanie głównej klasy aplikacji

1. Znajdź główny plik Java aplikacji w katalogu pakietu aplikacji. Na przykład:

   `C:\SpringBoot\eventhub\src\main\java\com\wingtiptoys\eventhub\EventhubApplication.java`

   — lub —

   `/users/example/home/eventhub/src/main/java/com/wingtiptoys/eventhub/EventhubApplication.java`

1. Otwórz główny plik Java aplikacji w edytorze tekstów, a następnie dodaj poniższe wiersze kodu do pliku:

   ```java
   package com.wingtiptoys.eventhub;

   import org.springframework.boot.SpringApplication;
   import org.springframework.boot.autoconfigure.SpringBootApplication;

   @SpringBootApplication
   public class EventhubApplication {
      public static void main(String[] args) {
         SpringApplication.run(EventhubApplication.class, args);
      }
   }
   ```

1. Zapisz i zamknij główny plik Java aplikacji.

### <a name="create-a-new-class-for-the-source-connector"></a>Tworzenie nowej klasy dla łącznika źródła

1. Utwórz nowy plik Java o nazwie *EventhubSource.java* w katalogu pakietu aplikacji, a następnie otwórz plik w edytorze tekstów i dodaj poniższe wiersze kodu:

   ```java
   package com.wingtiptoys.eventhub;

   import org.springframework.beans.factory.annotation.Autowired;
   import org.springframework.cloud.stream.annotation.EnableBinding;
   import org.springframework.cloud.stream.messaging.Source;
   import org.springframework.messaging.support.GenericMessage;
   import org.springframework.web.bind.annotation.PostMapping;
   import org.springframework.web.bind.annotation.RequestBody;
   import org.springframework.web.bind.annotation.RestController;

   @EnableBinding(Source.class)
   @RestController
   public class EventhubSource {

      @Autowired
      private Source source;

      @PostMapping("/messages")
      public String postMessage(@RequestBody String message) {
         this.source.output().send(new GenericMessage<>(message));
         return message;
      }
   }
   ```
1. Zapisz i zamknij plik *EventhubSource.java*.

### <a name="create-a-new-class-for-the-sink-connector"></a>Tworzenie nowej klasy dla łącznika ujścia

1. Utwórz nowy plik Java o nazwie *EventhubSource.java* w katalogu pakietu aplikacji, a następnie otwórz plik w edytorze tekstów i dodaj poniższe wiersze kodu:

   ```java
   package com.wingtiptoys.eventhub;

   import com.microsoft.azure.spring.integration.core.AzureHeaders;
   import com.microsoft.azure.spring.integration.core.api.Checkpointer;
   import org.slf4j.Logger;
   import org.slf4j.LoggerFactory;
   import org.springframework.cloud.stream.annotation.EnableBinding;
   import org.springframework.cloud.stream.annotation.StreamListener;
   import org.springframework.cloud.stream.messaging.Sink;
   import org.springframework.messaging.handler.annotation.Header;

   @EnableBinding(Sink.class)
   public class EventhubSink {

      private static final Logger LOGGER = LoggerFactory.getLogger(EventhubSink.class);

      @StreamListener(Sink.INPUT)
      public void handleMessage(String message, @Header(AzureHeaders.CHECKPOINTER) Checkpointer checkpointer) {
         LOGGER.info("New message received: '{}'", message);
         checkpointer.success().handle((r, ex) -> {
            if (ex == null) {
               LOGGER.info("Message '{}' successfully checkpointed", message);
            }
            return null;
         });
      }
   }
   ```

1. Zapisz i zamknij plik *EventhubSink.java*.

## <a name="build-and-test-your-application"></a>Tworzenie i testowanie aplikacji

Aby skompilować i przetestować aplikację, należy wykonać poniższe procedury.

1. Otwórz wiersz polecenia i zmień katalog na folder, w którym znajduje się plik *pom.xml*. Na przykład:

   `cd C:\SpringBoot\eventhub`

   — lub —

   `cd /users/example/home/eventhub`

1. Skompiluj swoją aplikację Spring Boot przy użyciu narzędzia Maven i uruchom ją, na przykład:

   ```shell
   mvn clean package
   mvn spring-boot:run
   ```

1. Gdy aplikacja jest uruchomiona, możesz użyć narzędzia *curl* w celu przetestowania swojej aplikacji, na przykład:

   ```shell
   curl -X POST -H "Content-Type: text/plain" -d "hello" http://localhost:8080/messages
   ```
   W dziennikach aplikacji powinien być widoczny opublikowany wpis „hello”. Przykład:

   ```shell
   [Thread-13] INFO com.wingtiptoys.eventhub.EventhubSink - New message received: 'hello'
   [pool-10-thread-7] INFO com.wingtiptoys.eventhub.EventhubSink - Message 'hello' successfully checkpointed
   ```

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat oprogramowania Spring i platformy Azure, przejdź do centrum dokumentacji dotyczącej oprogramowania Spring na platformie Azure.

> [!div class="nextstepaction"]
> [Oprogramowanie Spring na platformie Azure](/azure/java/spring-framework)

### <a name="additional-resources"></a>Dodatkowe zasoby

Aby uzyskać więcej informacji na temat pomocy technicznej platformy Azure dotyczącej integratora strumienia centrum zdarzeń, zobacz następujące artykuły:

* [Co to jest usługa Azure Event Hubs?](/azure/event-hubs/event-hubs-about)

* [Tworzenie przestrzeni nazw usługi Event Hubs i centrum zdarzeń przy użyciu witryny Azure Portal](/azure/event-hubs/event-hubs-create)

* [How to use the Spring Boot Starter for Apache Kafka with Azure Event Hubs](configure-spring-cloud-stream-binder-java-app-kafka-azure-event-hub.md) (Używanie szablonu startowego Spring Boot dla platformy Apache Kafka oraz usługi Azure Event Hubs)

Aby uzyskać więcej informacji na temat używania Azure z językiem Java, zobacz witryny [Azure dla deweloperów języka Java] oraz [Working with Azure DevOps and Java] (Praca z narzędziami Azure DevOps i językiem Java).

**[Platforma Spring]** jest rozwiązaniem open-source, które pomaga deweloperom języka Java tworzyć aplikacje na poziomie przedsiębiorstwa. Jednym z bardziej popularnych projektów opartych na tej platformie jest [Spring Boot], który oferuje uproszczoną metodę tworzenia autonomicznych aplikacji Java. Aby ułatwić deweloperom rozpoczęcie pracy z aplikacją Spring Boot, pod adresem <https://github.com/spring-guides/> udostępniono przykładowe pakiety aplikacji Spring Boot. Oprócz możliwości wyboru z listy podstawowych projektów Spring Boot narzędzie **[Spring Initializr]** pomaga deweloperom rozpocząć tworzenie niestandardowych aplikacji Spring Boot.

<!-- URL List -->

[Bezpłatne konto platformy Azure]: https://azure.microsoft.com/pricing/free-trial/
[Azure dla deweloperów języka Java]: https://docs.microsoft.com/azure/java/
[Working with Azure DevOps and Java]: /azure/devops/ (Praca z narzędziami Azure DevOps i językiem Java)
[Korzyści dla subskrybentów MSDN]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Initializr]: https://start.spring.io/
[Platforma Spring]: https://spring.io/

<!-- IMG List -->

[IMG01]: ./media/configure-spring-cloud-stream-binder-java-app-azure-event-hub/create-event-hub-01.png
[IMG02]: ./media/configure-spring-cloud-stream-binder-java-app-azure-event-hub/create-event-hub-02.png
[IMG03]: ./media/configure-spring-cloud-stream-binder-java-app-azure-event-hub/create-event-hub-03.png
[IMG04]: ./media/configure-spring-cloud-stream-binder-java-app-azure-event-hub/create-event-hub-04.png
[IMG05]: ./media/configure-spring-cloud-stream-binder-java-app-azure-event-hub/create-event-hub-05.png
[IMG06]: ./media/configure-spring-cloud-stream-binder-java-app-azure-event-hub/create-event-hub-06.png
[IMG07]: ./media/configure-spring-cloud-stream-binder-java-app-azure-event-hub/create-event-hub-07.png
[IMG08]: ./media/configure-spring-cloud-stream-binder-java-app-azure-event-hub/create-event-hub-08.png

[SI01]: ./media/configure-spring-cloud-stream-binder-java-app-azure-event-hub/create-project-01.png
[SI02]: ./media/configure-spring-cloud-stream-binder-java-app-azure-event-hub/create-project-02.png
[SI03]: ./media/configure-spring-cloud-stream-binder-java-app-azure-event-hub/create-project-03.png
