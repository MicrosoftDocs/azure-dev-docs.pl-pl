---
title: Konfigurowanie aplikacji Spring Boot Initializer do korzystania z szablonu startowego Azure Application Insights SpringBoot
description: Konfigurowanie aplikacji Spring Boot utworzonej za pomocą narzędzia Spring Initializr do używania szablonu startowego Application Insights Spring Boot.
services: Application-Insights
documentationcenter: java
author: dhaval24
ms.author: dhdoshi
ms.date: 11/29/2019
ms.service: azure-monitor
ms.tgt_pltfrm: application-insights
ms.topic: article
ms.openlocfilehash: 083abdf87d2298c99b9898db3b17e1c0e5e64bd8
ms.sourcegitcommit: b3b7dc6332c0532f74d210b2a5cab137e38a6750
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2019
ms.locfileid: "76999844"
---
# <a name="configure-a-spring-boot-initializer-app-to-use-application-insights"></a>Konfigurowanie aplikacji Spring Boot Initializer do korzystania z Application Insights

W tym artykule przedstawiono sposób tworzenia aplikacji Spring Boot przy użyciu narzędzia **[Spring Initializr]** . Zastosowano w nim szablon startowy Azure Application Insights Spring Boot w celu kompleksowego monitorowania aplikacji Java w chmurze.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym artykule, wymagane są:

* Subskrypcja platformy Azure. Jeśli nie masz jeszcze subskrypcji platformy Azure, możesz aktywować [korzyści dla subskrybentów MSDN] lub założyć [bezpłatne konto platformy Azure].
* Obsługiwany zestaw Java Development Kit (JDK). Aby uzyskać więcej informacji na temat zestawów JDK dostępnych do użycia podczas tworzenia aplikacji na platformie Azure, zobacz <https://aka.ms/azure-jdks>.
* Narzędzie [Apache Maven](http://maven.apache.org/) w wersji 3.0 lub nowszej.
* Interfejsy API Web Flux i Netty są **obecnie nieobsługiwane** z szablonem startowym Application Insights Spring Boot.

## <a name="create-a-custom-application-using-spring-initializr"></a>Tworzenie aplikacji niestandardowej przy użyciu narzędzia Spring Initializr

Utwórz aplikację przy użyciu poniższej procedury.

1. Przejdź do [https://start.spring.io/](https://start.spring.io/).

1. Określ, że chcesz wygenerować projekt **Maven** przy użyciu języka **Java**, wprowadź dla aplikacji nazwy w polach **Group** (Grupa) i **Artifact** (Artefakt), a następnie wybierz zależności sieci Web w sekcji zależności.

   ![Podstawowe opcje narzędzia Spring Initializr][SI01]

   > [!NOTE]
   >
   > Narzędzie Spring Initializr użyje nazw z pól **Group** (Grupa) i **Artifact** (Artefakt), aby utworzyć nazwę pakietu. Na przykład: *com.vged.appinsights*.
   >

1. Kliknij przycisk **Generate** (Generuj).

1. Po wyświetleniu monitu pobierz projekt do ścieżki na komputerze lokalnym.

1. Po wyodrębnieniu plików w systemie lokalnym niestandardowa aplikacja Spring Boot będzie gotowa do edycji.

## <a name="create-an-application-insights-resource-on-azure"></a>Tworzenie zasobu usługi Application Insights na platformie Azure

Utwórz zasób usługi Application Insights przy użyciu poniższej procedury.

1. Przejdź do platformy Azure w witrynie <https://portal.azure.com/> i kliknij pozycję **+Utwórz nowy zasób**.

1. Kliknij pozycję **IT i narzędzia do zarządzania**, a następnie kliknij pozycję **Application Insights**.

1. Na stronie **Nowy zasób usługi Application Insights** wprowadź poniższe informacje:

* Określ **Subskrypcję** i **Grupę zasobów**.
* Wprowadź **Nazwę** zasobu usługi Application Insights.
* Wybierz **Region**.

   Po określeniu tych opcji kliknij przycisk **Przejrzyj i utwórz**.

   ![Azure][AZ03]

* Przejrzyj specyfikacje i kliknij przycisk **Utwórz**.

Utworzony zasób zostanie wyświetlony na liście na **Pulpicie nawigacyjnym** platformy Azure, a także na stronach **Wszystkie zasoby**. Możesz kliknąć zasób w dowolnej z tych lokalizacji, aby otworzyć stronę Przegląd zasobu usługi Application Insights.

Na stronie przeglądu skopiuj **klucz instrumentacji**.

   ![Azure][AZ04]

## <a name="configure-your-downloaded-spring-boot-application-to-use-application-insights"></a>Konfigurowanie pobranej aplikacji Spring Boot do korzystania z usługi Application Insights

Aby skonfigurować aplikację, wykonaj poniższą procedurę.

1. Zlokalizuj plik *POM.xml* w katalogu głównym aplikacji, a następnie dodaj poniższą zależność w sekcji zależności.

```XML
 <dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>applicationinsights-spring-boot-starter</artifactId>
    <version>1.1.1</version>
</dependency>
```

1. Znajdź plik *application.properties* w katalogu *resources* aplikacji lub utwórz ten plik, jeśli jeszcze nie istnieje.

1. Otwórz plik *application.properties* w edytorze tekstów, a następnie dodaj w pliku poniższe wiersze kodu i zastąp przykładowe wartości odpowiednimi właściwościami z odpowiednimi poświadczeniami:

   ```yaml
   # Specify the instrumentation key of your Application Insights resource.
   azure.application-insights.instrumentation-key=[your ikey from the resource]
   # Specify the name of your springboot application. This can be any logical name you would like to give to your app.
   spring.application.name=[your app name]
   ```

   Aby dowiedzieć się więcej o sposobach dostosowywania usługi Application Insights, zobacz plik [readme szablonu startowego Application Insights Springboot](https://github.com/Microsoft/ApplicationInsights-Java/blob/master/azure-application-insights-spring-boot-starter/README.md).

   > [!NOTE]
   > 
   > Użyj różnych kluczy instrumentacji usługi Application Insights (takich jak różne zasoby) dla różnych profilów, takich jak PROD, DEV itp. Aby uzyskać dodatkowe informacje, zapoznaj się z [Właściwości specyficzne dla profilu Spring Boot]. 

1. Zapisz i zamknij plik *application.properties*.

1. Utwórz folder o nazwie *controller* w folderze źródłowym pakietu. Na przykład:

   `D:\Microsoft\demo\src\main\java\com\example\demo\controller`

   — lub —

   `/users/example/home/demo/src/main/java/com/example/demo/controller`

1. Utwórz nowy plik o nazwie *TestController.java* w folderze *controller*. Otwórz plik w edytorze tekstów i wpisz do niego poniższy kod:

   ```java
    package com.example.demo;

    import com.microsoft.applicationinsights.TelemetryClient;
    import java.io.IOException;
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.web.bind.annotation.GetMapping;
    import org.springframework.web.bind.annotation.RequestMapping;
    import org.springframework.web.bind.annotation.RestController;
    import com.microsoft.applicationinsights.telemetry.Duration;

    @RestController
    @RequestMapping("/sample")
    public class TestController {

        @Autowired
        TelemetryClient telemetryClient;

        @GetMapping("/hello")
        public String hello() {

            //track a custom event  
            telemetryClient.trackEvent("Sending a custom event...");

            //trace a custom trace
            telemetryClient.trackTrace("Sending a custom trace....");

            //track a custom metric
            telemetryClient.trackMetric("custom metric", 1.0);

            //track a custom dependency
            telemetryClient.trackDependency("SQL", "Insert", new Duration(0, 0, 1, 1, 1), true);

            return "hello";
        }
    }
   ```

   Zastąp `com.example.demo` nazwą pakietu projektu.

1. Zapisz i zamknij plik *TestController.java*.

1. Skompiluj aplikację Spring Boot przy użyciu narzędzia Maven i uruchom ją. Przykład:

   ```shell
   mvn clean package
   mvn spring-boot:run
   ```

1. Aby przetestować aplikację sieci Web, przejdź do adresu http://localhost:8080/sample/hello w przeglądarce internetowej, lub jeśli masz dostęp do narzędzia **curl**, użyj składni jak w poniższym przykładzie:

   ```shell
   curl http://localhost:8080/sample/hello
   ```

   Powinien zostać wyświetlony komunikat „hello!” pochodzący z przykładowego kontrolera. Usługa Application Insights będzie automatycznie zbierać to żądanie i wysyłać je jako element telemetrii ze skojarzonym niestandardowym zdarzeniem, niestandardową metryką, niestandardową zależnością i niestandardowym śladem stosu, jak określono w logice kontrolera. 

   Po kilku sekundach dane powinny być widoczne na platformie Azure. 

   ![Azure][AZ05]

Kliknij kafelek **Mapa aplikacji**, aby wyświetlić składniki wysokiego poziomu i ich interakcje ze sobą. Zaleca się uzyskiwać w tym miejscu ogólny przegląd całej aplikacji. Poszczególne mikrousługi Spring Boot są rozpoznawane za pomocą nazwy aplikacji Spring. Należy pamiętać o jej ustawieniu.

   ![Azure][AZ08] 

## <a name="configure-springboot-application-to-send-log4j-logs-to-application-insights"></a>Konfigurowanie aplikacji Springboot do wysyłania dzienników log4j do usługi Application Insights

Aby skonfigurować aplikację do wysyłania dzienników, wykonaj poniższą procedurę.

1. Zmodyfikuj plik POM.xml projektu i dodaj/zmodyfikuj sekcję zależności w następujący sposób. 

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
        <exclusions>
            <exclusion>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-logging</artifactId>
            </exclusion>
        </exclusions>
    </dependency>

    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
    </dependency>

    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-log4j2</artifactId>
    </dependency>

    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>applicationinsights-spring-boot-starter</artifactId>
        <version>1.1.1</version>
    </dependency>

    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>applicationinsights-logging-log4j2</artifactId>
        <version>2.1.1</version>
    </dependency>
</dependencies>
```

2. Zapisz i zamknij plik *POM.xml*.

3. W folderze \src\main\resources utwórz nowy plik *log4j2.xml* i skonfiguruj go. Przykład:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<Configuration packages="com.microsoft.applicationinsights.log4j.v2">
  <Properties>
    <Property name="LOG_PATTERN">
      %d{yyyy-MM-dd HH:mm:ss.SSS} %5p ${hostName} --- [%15.15t] %-40.40c{1.} : %m%n%ex
    </Property>
  </Properties>
  <Appenders>
    <Console name="Console" target="SYSTEM_OUT">
      <PatternLayout pattern="%d{HH:mm:ss.SSS} [%t] %-5level %logger{36} - %msg%n"/>
    </Console>
    <ApplicationInsightsAppender name="aiAppender">
    </ApplicationInsightsAppender>
  </Appenders>
  <Loggers>
    <Root level="trace">
      <AppenderRef ref="Console"  />
      <AppenderRef ref="aiAppender"  />
    </Root>
  </Loggers>
</Configuration>
```

4. Skompiluj i uruchom ponownie aplikację Spring Boot, jak pokazano powyżej.

W ciągu kilku sekund powinny być widoczne wszystkie dzienniki Spring dostępne na platformie Azure. Możesz zapoznać się z szczegółowymi komunikatami dziennika i dokonać analiz na portalu Analytics.

![Azure][AZ07]

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat oprogramowania Spring i platformy Azure, przejdź do centrum dokumentacji dotyczącej oprogramowania Spring na platformie Azure.

> [!div class="nextstepaction"]
> [Oprogramowanie Spring na platformie Azure](/azure/java/spring-framework)

### <a name="additional-resources"></a>Dodatkowe zasoby

Aby uzyskać więcej informacji o korzystaniu z aplikacji Spring Boot na platformie Azure, zobacz następujące artykuły:

* [Deploy a Spring Boot Application to the Azure App Service](deploy-spring-boot-java-app-from-container-registry-using-maven-plugin.md) (Wdrażanie aplikacji Spring Boot w usłudze Azure App Service)

* [Running a Spring Boot Application on a Kubernetes Cluster in the Azure Container Service](deploy-spring-boot-java-app-on-kubernetes.md) (Uruchamianie aplikacji Spring Boot w klastrze Kubernetes w usłudze Azure Container Service)

Usługa Application Insights obsługuje automatyczne zbieranie zależności zewnętrznych i korelację z żądaniami przychodzącymi. Obecnie obsługujemy automatyczne zbieranie danych Oracle, MsSQL, MySQL i Redis. Aby uzyskać więcej informacji na temat włączania automatycznego zbierania, postępuj zgodnie z artykułem na temat [używania agenta Java usługi Application Insights](/azure/application-insights/app-insights-java-agent).

Aby uzyskać więcej informacji na temat usługi Azure Application Insights i jej możliwości monitorowania, zobacz stronę główną **[Application Insights]** .

Aby uzyskać więcej informacji na temat dodatkowych szczegółów konfiguracji szablonu startowego Application Insights Spring Boot, zapoznaj się z tym [linkiem](https://github.com/Microsoft/ApplicationInsights-Java/blob/master/azure-application-insights-spring-boot-starter/README.md).

Żądania dotyczące funkcji i potencjalnych usterek można otwierać w repozytorium [ GitHub](https://github.com/Microsoft/ApplicationInsights-Java/issues).

Aby uzyskać więcej informacji na temat używania Azure z językiem Java, zobacz witryny [Azure dla deweloperów języka Java] oraz [Working with Azure DevOps and Java] (Praca z narzędziami Azure DevOps i językiem Java).

**[Platforma Spring]** jest rozwiązaniem open-source, które pomaga deweloperom języka Java tworzyć aplikacje na poziomie przedsiębiorstwa. Jednym z bardziej popularnych projektów opartych na tej platformie jest [Spring Boot], który oferuje uproszczoną metodę tworzenia autonomicznych aplikacji Java. Aby ułatwić deweloperom rozpoczęcie pracy z aplikacją Spring Boot, pod adresem [https://github.com/spring-guides/](https://github.com/spring-guides/) udostępniono przykładowe pakiety aplikacji Spring Boot. Oprócz możliwości wyboru z listy podstawowych projektów Spring Boot narzędzie **[Spring Initializr]** pomaga deweloperom rozpocząć tworzenie niestandardowych aplikacji Spring Boot.

<!-- URL List -->

[Azure dla deweloperów języka Java]: /azure/java/
[Bezpłatne konto platformy Azure]: https://azure.microsoft.com/pricing/free-trial/
[Working with Azure DevOps and Java]: /azure/devops/ (Praca z narzędziami Azure DevOps i językiem Java)
[Korzyści dla subskrybentów MSDN]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Właściwości specyficzne dla profilu Spring Boot]: https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html#boot-features-external-config-profile-specific-properties
[Spring Initializr]: https://start.spring.io/
[Platforma Spring]: https://spring.io/
[Application Insights]: /azure/application-insights/

<!-- IMG List -->

[AZ01]: ./media/configure-spring-boot-starter-java-app-with-azure-application-insights/Create_resource.png
[AZ02]: ./media/configure-spring-boot-starter-java-app-with-azure-application-insights/Create_resource_2.png
[AZ03]: ./media/configure-spring-boot-starter-java-app-with-azure-application-insights/Create_resource_3.png
[AZ04]: ./media/configure-spring-boot-starter-java-app-with-azure-application-insights/Get_IKey.png
[AZ05]: ./media/configure-spring-boot-starter-java-app-with-azure-application-insights/OverviewBladeResults.png
[AZ06]: ./media/configure-spring-boot-starter-java-app-with-azure-application-insights/Search_and_traces.png
[AZ07]: ./media/configure-spring-boot-starter-java-app-with-azure-application-insights/traces_details.png
[AZ08]: ./media/configure-spring-boot-starter-java-app-with-azure-application-insights/AppMap.png

[SI01]: ./media/configure-spring-boot-starter-java-app-with-azure-application-insights/spring_start.PNG
[SI02]: ./media/configure-spring-boot-starter-java-app-with-azure-application-insights/After_extract.png

[RE01]: ./media/configure-spring-boot-starter-java-app-with-azure-application-insights/applicationproperties_loc.png
[RE02]: ./media/configure-spring-boot-starter-java-app-with-azure-application-insights/applicationinsightsproperties.png
