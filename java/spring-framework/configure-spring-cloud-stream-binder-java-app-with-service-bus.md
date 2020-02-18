---
title: Jak używać integratora strumienia Azure platformy Spring Cloud dla usługi Azure Service Bus
description: W tym artykule pokazano, jak używać integratora strumienia Spring Cloud do wysyłania komunikatów do usługi Azure Service Bus i odbierania komunikatów z usługi Azure Service Bus.
author: seanli1988
manager: kyliel
ms.author: seal
ms.date: 08/21/2019
ms.topic: article
ms.openlocfilehash: e8f9dd93da56b17efca059e00cf1483666f4cf33
ms.sourcegitcommit: 24795630044c10a07b5dedc0f51c280f090c097e
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2020
ms.locfileid: "77075271"
---
# <a name="how-to-use-spring-cloud-azure-stream-binder-for-azure-service-bus"></a>Jak używać integratora strumienia Azure platformy Spring Cloud dla usługi Azure Service Bus

[!INCLUDE [spring-boot-20-note.md](../includes/spring-boot-20-note.md)]

Platforma Azure udostępnia rozwiązanie do asynchronicznej obsługi komunikatów o nazwie [Azure Service Bus](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-messaging-overview) („Service Bus”) oparte na standardzie [Advanced Message Queueing Protocol 1.0](http://www.amqp.org/) („AMQP 1.0”). Usługi Service Bus można używać na różnych obsługiwanych platformach Azure.

W tym artykule pokazano, jak używać integratora strumienia Spring Cloud do wysyłania komunikatów do elementów `queues` i `topics` usługi Service Bus oraz odbierania komunikatów z usługi Service Bus.

## <a name="prerequisites"></a>Wymagania wstępne

Wykonywanie czynności opisanych w tym artykule wymaga spełnienia wymienionych warunków wstępnych:

1. Jeśli nie masz jeszcze subskrypcji platformy Azure, możesz aktywować [korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/free/).

1. Obsługiwany zestaw Java Development Kit (JDK) w wersji 8 lub nowszej. Aby uzyskać więcej informacji na temat zestawów JDK dostępnych do użycia podczas tworzenia aplikacji na platformie Azure, zobacz <https://aka.ms/azure-jdks>.

1. Narzędzie Apache [Maven](http://maven.apache.org/) w wersji 3.2 lub nowszej.

1. Jeśli już Ci się udało skonfigurować kolejkę lub temat usługi Service Bus, upewnij się, że przestrzeń nazw Service Bus spełnia następujące wymagania:

    1. Zezwala na dostęp ze wszystkich sieci
    1. Jest w warstwie Standardowa (lub wyższej)
    1. Ma wdrożone zasady dostępu z dostępem do odczytu/zapisu kolejki i tematu

1. Jeśli nie masz skonfigurowanej kolejki ani tematu usługi Service Bus, utwórz [kolejkę usługi Service Bus](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-quickstart-portal) lub [temat usługi Service Bus](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal) w witrynie Azure Portal. Upewnij się, że przestrzeń nazw spełnia wymagania określone w poprzednim kroku. Zanotuj również parametry połączenia w przestrzeni nazw, ponieważ będą one potrzebne podczas korzystania z aplikacji testowej tego samouczka.

1. Jeśli nie masz aplikacji Spring Boot, [utwórz projekt **Maven** za pomocą aplikacji Spring Initializr](https://start.spring.io/). Pamiętaj, aby wybrać opcję **Maven Project** (Projekt Maven), a w obszarze **Dependencies** (Zależności) dodać zależność **Web** (Internet).

## <a name="use-the-spring-cloud-stream-binder-starter"></a>Korzystanie z szablonu startowego integratora strumienia Spring Cloud

1. Znajdź plik *pom.xml* w katalogu nadrzędnym aplikacji. Na przykład:

    `C:\SpringBoot\servicebus\pom.xml`

    — lub —

    `/users/example/home/servicebus/pom.xml`

1. Otwórz plik *pom.xml* w edytorze tekstów.

1. W zależności od tego, czy używasz kolejki czy tematu usługi Service Bus, we wpisie **&lt;dependencies>** dodaj następujący blok kodu:

    **Kolejka usługi Service Bus**

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-azure-servicebus-queue-stream-binder</artifactId>
        <version>1.1.0.RC5</version>
    </dependency>
    ```

    ![W pliku pom.xml wprowadź informacje o kolejce usługi Service Bus.](./media/configure-spring-cloud-stream-binder-java-app-with-service-bus/add-stream-binder-starter-pom-file-dependency-for-service-bus-queue.png)

    **Temat usługi Service Bus**

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-azure-servicebus-topic-stream-binder</artifactId>
        <version>1.1.0.RC5</version>
    </dependency>
    ```

    ![W pliku pom.xml wprowadź informacje o temacie usługi Service Bus.](./media/configure-spring-cloud-stream-binder-java-app-with-service-bus/add-stream-binder-starter-pom-file-dependency-for-service-bus-topic.png)

1. Zapisz i zamknij plik *pom.xml*.

## <a name="configure-the-app-for-your-service-bus"></a>Konfigurowanie aplikacji dla wystąpienia usługi Service Bus

Aplikację można skonfigurować pod kątem używania parametrów połączenia lub pliku poświadczeń. W tym samouczku są używane parametry połączenia. Aby uzyskać więcej informacji o korzystaniu z plików poświadczeń, zapoznaj się z [przykładowym kodem integratora strumienia Azure platformy Spring Cloud dla kolejki usługi Service Bus](https://github.com/microsoft/spring-cloud-azure/tree/release/1.1.0.RC4/spring-cloud-azure-samples/servicebus-queue-binder-sample#credential-file-based-usage
) i [przykładowym kodem integratora strumienia Azure platformy Spring Cloud dla tematu usługi Service Bus](https://github.com/microsoft/spring-cloud-azure/tree/release/1.1.0.RC4/spring-cloud-azure-samples/servicebus-topic-binder-sample#credential-file-based-usage).

1. Znajdź plik *application.properties* w katalogu *resources* aplikacji, na przykład:

   `C:\SpringBoot\servicebus\src\main\resources\application.properties`

   — lub —

   `/users/example/home/servicebus/src/main/resources/application.properties`

1. Otwórz plik *application.properties* w edytorze tekstów.

1. Dołącz odpowiedni kod na końcu pliku *application.properties* w zależności od tego, czy używasz kolejki czy tematu usługi Service Bus. Przy użyciu [tabeli opisów pól](#fd) zastąp przykładowe wartości właściwościami odpowiednimi dla swojego wystąpienia usługi Service Bus.

    **Kolejka usługi Service Bus**

    ```yaml
    spring.cloud.azure.servicebus.connection-string=<ServiceBusNamespaceConnectionString>
    spring.cloud.stream.bindings.input.destination=examplequeue
    spring.cloud.stream.bindings.output.destination=examplequeue
    spring.cloud.stream.servicebus.queue.bindings.input.consumer.checkpoint-mode=MANUAL
    ```

    **Temat usługi Service Bus**

    ```yaml
    spring.cloud.azure.servicebus.connection-string=<ServiceBusNamespaceConnectionString>
    spring.cloud.stream.bindings.input.destination=exampletopic
    spring.cloud.stream.bindings.input.group=examplesubscription
    spring.cloud.stream.bindings.output.destination=exampletopic
    spring.cloud.stream.servicebus.topic.bindings.input.consumer.checkpoint-mode=MANUAL
    ```

    **<a name="fd">Opisy pól</a>**

    |                                        Pole                                   |                                                                                   Opis                                                                                    |
    |--------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    |               `spring.cloud.azure.servicebus.connection-string`                |                                        Podaj parametry połączenia uzyskane w przestrzeni nazw Service Bus w witrynie Azure Portal.                                   |
    |               `spring.cloud.stream.bindings.input.destination`                 |                            Podaj kolejkę lub temat usługi Service Bus, których użyto w tym samouczku.                         |
    |                  `spring.cloud.stream.bindings.input.group`                    |                                            Jeśli użyto tematu usługi Service Bus, podaj subskrypcję tematu.                                |
    |               `spring.cloud.stream.bindings.output.destination`                |                               Podaj tę samą wartość, której użyto dla miejsca docelowego elementu wejściowego.                        |
    | `spring.cloud.stream.servicebus.queue.bindings.input.consumer.checkpoint-mode` |                                                       Podaj wartość `MANUAL`.                                                   |
    | `spring.cloud.stream.servicebus.topic.bindings.input.consumer.checkpoint-mode` |                                                       Podaj wartość `MANUAL`.                                                   |

1. Zapisz i zamknij plik *application.properties*.

## <a name="implement-basic-service-bus-functionality"></a>Implementowanie podstawowych funkcji usługi Service Bus

W tej sekcji utworzysz niezbędne klasy języka Java na potrzeby wysyłania komunikatów do swojego wystąpienia usługi Service Bus.

### <a name="modify-the-main-application-class"></a>Modyfikowanie głównej klasy aplikacji

1. Znajdź główny plik Java aplikacji w katalogu pakietów aplikacji, na przykład:

    `C:\SpringBoot\servicebus\src\main\java\com\example\ServiceBusBinderApplication.java`

   — lub —

   `/users/example/home/servicebus/src/main/java/com/example/ServiceBusBinderApplication.java`

1. Otwórz główny plik Java aplikacji w edytorze tekstów.

1. Dodaj następujący kod do pliku:

    ```java
    package com.example;

    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;

    @SpringBootApplication
    public class ServiceBusBinderApplication {

        public static void main(String[] args) {
            SpringApplication.run(ServiceBusBinderApplication.class, args);
        }
    }
    ```

1. Zapisz i zamknij plik.

### <a name="create-a-new-class-for-the-source-connector"></a>Tworzenie nowej klasy dla łącznika źródła

1. Za pomocą edytora tekstów utwórz plik Java o nazwie *StreamBinderSource.java* w katalogu pakietów aplikacji.

1. Dodaj następujący kod do nowego pliku:

    ```java
    package com.example;

    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.cloud.stream.annotation.EnableBinding;
    import org.springframework.cloud.stream.messaging.Source;
    import org.springframework.messaging.support.GenericMessage;
    import org.springframework.web.bind.annotation.PostMapping;
    import org.springframework.web.bind.annotation.RequestParam;
    import org.springframework.web.bind.annotation.RestController;

    @EnableBinding(Source.class)
    @RestController
    public class StreamBinderSource {

        @Autowired
        private Source source;

        @PostMapping("/messages")
        public String postMessage(@RequestParam String message) {
            this.source.output().send(new GenericMessage<>(message));
            return message;
        }
    }
    ```

1. Zapisz i zamknij plik *StreamBinderSources.java*.

### <a name="create-a-new-class-for-the-sink-connector"></a>Tworzenie nowej klasy dla łącznika ujścia

1. Za pomocą edytora tekstów utwórz plik Java o nazwie *StreamBinderSink.java* w katalogu pakietów aplikacji.

1. Dodaj następujące wiersze kodu do nowego pliku:

    ```java
    package com.example;

    import com.microsoft.azure.spring.integration.core.AzureHeaders;
    import com.microsoft.azure.spring.integration.core.api.Checkpointer;
    import org.springframework.cloud.stream.annotation.EnableBinding;
    import org.springframework.cloud.stream.annotation.StreamListener;
    import org.springframework.cloud.stream.messaging.Sink;
    import org.springframework.messaging.handler.annotation.Header;

    @EnableBinding(Sink.class)
    public class StreamBinderSink {

        @StreamListener(Sink.INPUT)
        public void handleMessage(String message, @Header(AzureHeaders.CHECKPOINTER) Checkpointer checkpointer) {
            System.out.println(String.format("New message received: '%s'", message));
            checkpointer.success().handle((r, ex) -> {
                if (ex == null) {
                    System.out.println(String.format("Message '%s' successfully checkpointed", message));
                }
                return null;
            });
        }
    }
    ```

1. Zapisz i zamknij plik *StreamBinderSink.java*.

## <a name="build-and-test-your-application"></a>Kompilowanie i testowanie aplikacji

1. Otwórz wiersz polecenia.

1. Przejdź do lokalizacji pliku *pom.xml*, na przykład:

    `cd C:\SpringBoot\servicebus`

    — lub —

    `cd /users/example/home/servicebus`

2. Skompiluj aplikację Spring Boot przy użyciu narzędzia Maven i uruchom ją:

    ```shell
    mvn clean spring-boot:run
    ```

3. Gdy aplikacja jest uruchomiona, możesz użyć narzędzia *curl* w celu jej przetestowania:

    ```shell
    curl -X POST localhost:8080/messages?message=hello
    ```

    W dziennikach aplikacji powinien być widoczny opublikowany wpis „hello”:

    ```shell
    New message received: 'hello'
    Message 'hello' successfully checkpointed
    ```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy zasoby utworzone w tym artykule nie będą już potrzebne, usuń je w witrynie [Azure Portal](https://portal.azure.com/), aby uniknąć naliczania opłat.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Oprogramowanie Spring na platformie Azure](/java/azure/spring-framework)