---
title: Używanie szablonu startowego Spring Boot dla interfejsu JMS usługi Azure Service Bus
description: W tym artykule pokazano, jak używać szablonu startowego Spring JMS do wysyłania komunikatów do usługi Azure Service Bus i odbierania komunikatów z usługi Azure Service Bus.
author: seanli1988
manager: kyliel
ms.author: seal
ms.date: 08/21/2019
ms.topic: article
ms.openlocfilehash: 17baf4899bd47b15bc60d02b853605c422d3c82b
ms.sourcegitcommit: 6fa28ea675ae17ffb9ac825415e2e26a3dfe7107
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2020
ms.locfileid: "77002318"
---
# <a name="how-to-use-the-spring-boot-starter-for-azure-service-bus-jms"></a>Używanie szablonu startowego Spring Boot dla interfejsu JMS usługi Azure Service Bus

[!INCLUDE [spring-boot-20-note.md](../includes/spring-boot-20-note.md)]

Platforma Azure udostępnia rozwiązanie do asynchronicznej obsługi komunikatów o nazwie [Azure Service Bus](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-messaging-overview) („Service Bus”) oparte na standardzie [Advanced Message Queueing Protocol 1.0](http://www.amqp.org/) („AMQP 1.0”). Usługi Service Bus można używać na różnych obsługiwanych platformach Azure.

Szablon startowy Spring Boot dla interfejsu JMS usługi Azure Service Bus zapewnia integrację platformy Spring z usługą Service Bus.

W tym artykule pokazano, jak używać szablonu startowego Spring Boot dla interfejsu JMS usługi Azure Service Bus do wysyłania komunikatów do elementów `queues` i `topics` usługi Service Bus oraz odbierania komunikatów z tej usługi.

## <a name="prerequisites"></a>Wymagania wstępne

Wykonywanie czynności opisanych w tym artykule wymaga spełnienia wymienionych warunków wstępnych:

1. Jeśli nie masz jeszcze subskrypcji platformy Azure, możesz aktywować [korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/free/).

1. Obsługiwany zestaw Java Development Kit (JDK) w wersji 8 lub nowszej. Aby uzyskać więcej informacji na temat zestawów JDK dostępnych do użycia podczas tworzenia aplikacji na platformie Azure, zobacz <https://aka.ms/azure-jdks>.

1. Narzędzie Apache [Maven](http://maven.apache.org/) w wersji 3.2 lub nowszej.

1. Jeśli już Ci się udało skonfigurować kolejkę lub temat usługi Service Bus, upewnij się, że przestrzeń nazw Service Bus spełnia następujące wymagania:

    1. Zezwala na dostęp ze wszystkich sieci
    1. Jest w warstwie Premium (lub wyższej)
    1. Ma wdrożone zasady dostępu z dostępem do odczytu/zapisu kolejki i tematu

1. Jeśli nie masz skonfigurowanej kolejki ani tematu usługi Service Bus, utwórz [kolejkę usługi Service Bus](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-quickstart-portal) lub [temat usługi Service Bus](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal) w witrynie Azure Portal. Upewnij się, że przestrzeń nazw spełnia wymagania określone w poprzednim kroku. Zanotuj również parametry połączenia w przestrzeni nazw, ponieważ będą one potrzebne podczas korzystania z aplikacji testowej tego samouczka.

1. Jeśli nie masz aplikacji Spring Boot, [utwórz projekt **Maven** za pomocą aplikacji Spring Initializer](https://start.spring.io/). Pamiętaj, aby wybrać opcję **Maven Project** (Projekt Maven), a w obszarze **Dependencies** (Zależności) dodać zależność **Web** (Internet).

## <a name="use-the-azure-service-bus-jms-starter"></a>Używanie szablonu startowego JMS usługi Azure Service Bus

1. Znajdź plik *pom.xml* w katalogu nadrzędnym aplikacji. Na przykład:

    `C:\SpringBoot\servicebus\pom.xml`

    — lub —

    `/users/example/home/servicebus/pom.xml`

1. Otwórz plik *pom.xml* w edytorze tekstów.

1. Dodaj szablon startowy Spring Boot dla interfejsu JMS usługi Azure Service Bus do listy `<dependencies>`:

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-servicebus-jms-spring-boot-starter</artifactId>
        <version>2.1.7</version>
    </dependency>
    ```

    ![Dodaj sekcję zależności do pliku pom.xml.](./media/configure-spring-boot-starter-java-app-with-azure-service-bus/add-dependency-section-new.png)

1. Zapisz i zamknij plik *pom.xml*.

## <a name="configure-the-app-for-your-service-bus"></a>Konfigurowanie aplikacji dla wystąpienia usługi Service Bus 

W tej sekcji opisano sposób konfigurowania aplikacji pod kątem korzystania z kolejki lub tematu usługi Service Bus.

### <a name="use-a-service-bus-queue"></a>Używanie kolejki usługi Service Bus

1. Znajdź plik *application.properties* w katalogu *resources* (zasoby) aplikacji. Na przykład:

    `C:\SpringBoot\servicebus\application.properties`

    — lub —

    `/users/example/home/servicebus/application.properties`

1. Otwórz plik *application.properties* w edytorze tekstów.

1. Dołącz następujący kod na końcu pliku *application.properties*. Przykładowe wartości zastąp wartościami odpowiednimi dla swojego wystąpienia usługi Service Bus:

    ```yml
    spring.jms.servicebus.connection-string=<ServiceBusNamespaceConnectionString>
    spring.jms.servicebus.idle-timeout=<IdleTimeout>
    ```

    **Opisy pól**

    | Pole                                     | Opis                                                                                     |
    |-------------------------------------------|-------------------------------------------------------------------------------------------------|
    | `spring.jms.servicebus.connection-string` | Podaj parametry połączenia uzyskane w przestrzeni nazw Service Bus w witrynie Azure Portal. |
    | `spring.jms.servicebus.idle-timeout`      | Określa limit czasu bezczynności (w milisekundach). Zalecana wartość w tym samouczku to 1 800 000.   |

1. Zapisz i zamknij plik *application.properties*.

### <a name="use-service-bus-topic"></a>Używanie tematu usługi Service Bus

1. Znajdź plik *application.properties* w katalogu *resources* (zasoby) aplikacji. Na przykład:

    `C:\SpringBoot\servicebus\application.properties`

    — lub —

    `/users/example/home/servicebus/application.properties`

1. Otwórz plik *application.properties* w edytorze tekstów.

1. Dołącz następujący kod na końcu pliku *application.properties*. Przykładowe wartości zastąp wartościami odpowiednimi dla swojego wystąpienia usługi Service Bus:

    ```yml
    spring.jms.servicebus.connection-string=<ServiceBusNamespaceConnectionString>
    spring.jms.servicebus.topic-client-id=<ServiceBusTopicClientId>
    spring.jms.servicebus.idle-timeout=<IdleTimeout>
    ```

    **Opisy pól**

    | Pole                                     | Opis                                                                                       |
    |-------------------------------------------|---------------------------------------------------------------------------------------------------|
    | `spring.jms.servicebus.connection-string` | Podaj parametry połączenia uzyskane w przestrzeni nazw Service Bus w witrynie Azure Portal.   |
    | `spring.jms.servicebus.topic-client-id`   | Określa identyfikator klienta JMS w przypadku korzystania z tematu Azure Service Bus w ramach trwałej subskrypcji. |
    | `spring.jms.servicebus.idle-timeout`      | Określa limit czasu bezczynności (w milisekundach). Zalecana wartość w tym samouczku to 1 800 000.     |

1. Zapisz i zamknij plik *application.properties*.

## <a name="implement-basic-service-bus-functionality"></a>Implementowanie podstawowych funkcji usługi Service Bus

W tej sekcji zostaną utworzone klasy języka Java wymagane do wysyłania komunikatów do kolejki lub tematu Service Bus oraz odbierania komunikatów z subskrypcji zawierającej odpowiednią kolejkę lub temat.

### <a name="modify-the-main-application-class"></a>Modyfikowanie głównej klasy aplikacji

1. Znajdź główny plik Java aplikacji w katalogu pakietów aplikacji, na przykład:

    `C:\SpringBoot\servicebus\src\main\java\com\wingtiptoys\servicebus\ServiceBusJmsStarterApplication.java`

    — lub —

    `/users/example/home/servicebus/src/main/java/com/wingtiptoys/servicebus/ServiceBusJmsStarterApplication.java`

1. Otwórz główny plik Java aplikacji w edytorze tekstów.

1. Dodaj następujący kod do pliku:

   ```java
    package com.wingtiptoys.servicebus;

    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;

    @SpringBootApplication
    public class ServiceBusJmsStarterApplication {

        public static void main(String[] args) {
            SpringApplication.run(ServiceBusJmsStarterApplication.class, args);
        }
    }
    ```

1. Zapisz i zamknij plik.

### <a name="define-a-test-java-class"></a>Definiowanie testowej klasy Java

1. Za pomocą edytora tekstów utwórz plik Java o nazwie *User.java* w katalogu pakietów aplikacji.

1. Zdefiniuj ogólną klasę użytkownika, która umożliwia przechowywanie i pobieranie nazwy użytkownika:

    ```java
    package com.wingtiptoys.servicebus;

    import java.io.Serializable;

    // Define a generic User class.
    public class User implements Serializable {

        private static final long serialVersionUID = -295422703255886286L;

        private String name;

        public User() {
        }

        public User(String name) {
            setName(name);
        }

        public String getName() {
            return name;
        }

        public void setName(String name) {
            this.name = name;
        }

    }
    ```

    Implementacja interfejsu `Serializable` obejmuje używanie metody `send` szablonu `JmsTemplate` platformy Spring. W przeciwnym razie należy zdefiniować dostosowaną funkcję Bean `MessageConverter`, która umożliwia serializowanie zawartości w pliku JSON w formacie tekstowym. Aby uzyskać więcej informacji na temat funkcji `MessageConverter`, zobacz oficjalną stronę [projektu szablonu startowego Spring JMS ](https://spring.io/guides/gs/messaging-jms/).

1. Zapisz i zamknij plik *User.java*.

### <a name="create-a-new-class-for-the-message-send-controller"></a>Tworzenie nowej klasy dla kontrolera wysyłania komunikatów

1. Za pomocą edytora tekstów utwórz plik Java o nazwie *SendController.java* w katalogu pakietów aplikacji.

1. Dodaj następujący kod do nowego pliku:

    ```java
    package com.wingtiptoys.servicebus;

    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.jms.core.JmsTemplate;
    import org.springframework.web.bind.annotation.PostMapping;
    import org.springframework.web.bind.annotation.RequestParam;
    import org.springframework.web.bind.annotation.RestController;

    @RestController
    public class SendController {

        private static final String DESTINATION_NAME = "<DestinationName>";

        private static final Logger logger = LoggerFactory.getLogger(SendController.class);

        @Autowired
        private JmsTemplate jmsTemplate;

        @PostMapping("/messages")
        public String postMessage(@RequestParam String message) {
            logger.info("Sending message");
            jmsTemplate.convertAndSend(DESTINATION_NAME, new User(message));
            return message;
        }
    }
    ```

    > [!NOTE]
    > Element `<DestinationName>` zastąp nazwą kolejki lub tematu skonfigurowaną w przestrzeni nazw usługi Service Bus.

1. Zapisz i zamknij plik *SendController.java*.

### <a name="create-a-class-for-the-message-receive-controller"></a>Tworzenie klasy dla kontrolera odbierania komunikatów

#### <a name="receive-messages-from-a-service-bus-queue"></a>Odbieranie komunikatów z kolejki usługi Service Bus

1. Za pomocą edytora tekstów utwórz plik Java o nazwie *QueueReceiveController.java* w katalogu pakietów aplikacji.

1. Dodaj następujący kod do nowego pliku:

    ```java
    package com.wingtiptoys.servicebus;

    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;
    import org.springframework.jms.annotation.JmsListener;
    import org.springframework.stereotype.Component;

    @Component
    public class QueueReceiveController {

        private static final String QUEUE_NAME = "<ServiceBusQueueName>";

        private final Logger logger = LoggerFactory.getLogger(QueueReceiveController.class);

        @JmsListener(destination = QUEUE_NAME, containerFactory = "jmsListenerContainerFactory")
        public void receiveMessage(User user) {
            logger.info("Received message: {}", user.getName());
        }
    }
    ```

    > [!NOTE]
    > Element `<ServiceBusQueueName>` zastąp nazwą kolejki skonfigurowaną w przestrzeni nazw usługi Service Bus.

1. Zapisz i zamknij plik *QueueReceiveController.java*.

#### <a name="receive-messages-from-a-service-bus-subscription"></a>Odbieranie komunikatów z subskrypcji usługi Service Bus

1. Za pomocą edytora tekstów utwórz plik Java o nazwie *TopicReceiveController.java* w katalogu pakietów aplikacji. 

1. Dodaj następujący kod do nowego pliku. Symbol zastępczy `<ServiceBusTopicName>` zastąp nazwą tematu skonfigurowaną w przestrzeni nazw usługi Service Bus. Symbol zastępczy `<ServiceBusSubscriptionName>` zastąp nazwą własnej subskrypcji dla tematu usługi Service Bus.

    ```java
    package com.wingtiptoys.servicebus;

    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;
    import org.springframework.jms.annotation.JmsListener;
    import org.springframework.stereotype.Component;

    @Component
    public class TopicReceiveController {

        private static final String TOPIC_NAME = "<ServiceBusTopicName>";

        private static final String SUBSCRIPTION_NAME = "<ServiceBusSubscriptionName>";

        private final Logger logger = LoggerFactory.getLogger(TopicReceiveController.class);

        @JmsListener(destination = TOPIC_NAME, containerFactory = "topicJmsListenerContainerFactory",
                subscription = SUBSCRIPTION_NAME)
        public void receiveMessage(User user) {
            logger.info("Received message: {}", user.getName());
        }
    }
    ```

1. Zapisz i zamknij plik *TopicReceiveController.java*.

## <a name="build-and-test-your-application"></a>Kompilowanie i testowanie aplikacji

1. Otwórz wiersz polecenia i przejdź do lokalizacji pliku *pom.xml*. Na przykład:

    `cd C:\SpringBoot\servicebus`

    — lub —

    `cd cd /users/example/home/servicebus`

1. Skompiluj aplikację Spring Boot przy użyciu narzędzia Maven i uruchom ją:

    ```shell
    mvn clean spring-boot:run
    ```

1. Gdy aplikacja jest uruchomiona, możesz użyć narzędzia *curl* w celu jej przetestowania:

    ```shell
    curl -X POST localhost:8080/messages?message=hello
    ```

    W dzienniku aplikacji powinny być widoczne wpisy „Sending message” i „hello”:

    ```shell
    [nio-8080-exec-1] com.wingtiptoys.servicebus.SendController : Sending message
    [enerContainer-1] com.wingtiptoys.servicebus.ReceiveController : Received message: hello
    ```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy zasoby utworzone w tym artykule nie będą już potrzebne, usuń je w witrynie [Azure Portal](https://portal.azure.com/), aby uniknąć naliczania opłat.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Jak używać interfejsu API JMS z usługą Service Bus i protokołem AMQP 1.0](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-java-how-to-use-jms-api-amqp)