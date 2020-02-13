---
title: Rozpoczynanie pracy z usługą Logz.io dla aplikacji Java działających na platformie Azure
description: W tym samouczku przedstawiono sposób integrowania i konfigurowania usługi Logz.io dla aplikacji Java działających na platformie Azure.
author: jdubois
manager: bborges
ms.topic: tutorial
ms.date: 11/05/2019
ms.author: judubois
ms.openlocfilehash: d7f90939701bfbcdcd895b8baf3cabcae4d5efa9
ms.sourcegitcommit: b3b7dc6332c0532f74d210b2a5cab137e38a6750
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2019
ms.locfileid: "77000187"
---
# <a name="tutorial-getting-started-with-monitoring-and-logging-using-logzio-for-java-apps-running-on-azure"></a>Samouczek: Wprowadzenie do monitorowania i rejestrowania przy użyciu usługi Logz.io dla aplikacji Java działających na platformie Azure

W tym samouczku przedstawiono sposób konfigurowania klasycznej aplikacji Java do wysyłania dzienników do usługi [Logz.io](https://logz.io/) na potrzeby pozyskiwania i analizy. Logz.io zapewnia pełne rozwiązanie do monitorowania oparte na zestawie Elasticsearch/Logstash/Kibana (ELK) i platformie Grafana.

W samouczku przyjęto założenie, że używasz narzędzia Log4J lub Logback. Te biblioteki są najczęściej używane do rejestrowania w języku Java, dlatego samouczek powinien działać w przypadku większości aplikacji uruchomionych na platformie Azure. Jeśli używasz już zestawu Elastic do monitorowania swojej aplikacji Java, dowiesz się tu, jak zmienić punkt końcowy na usługę Logz.io.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Wysyłanie dzienników z istniejącej aplikacji Java do usługi Logz.io.
> * Wysyłanie dzienników diagnostycznych i metryk z usług platformy Azure do usługi Logz.io.

## <a name="prerequisites"></a>Wymagania wstępne

* Zestaw [Java Developer Kit](https://aka.ms/azure-jdks), wersja 8 lub nowsza
* Konto w usłudze Logz.io z platformy [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/logz.logzio-elk-as-a-service-pro)
* Istniejące aplikacje Java korzystające z narzędzi Log4J lub Logback

## <a name="send-java-application-logs-to-logzio"></a>Wysyłanie dzienników aplikacji Java do usługi Logz.io

Najpierw dowiesz się, jak skonfigurować aplikację Java przy użyciu tokenu zapewniającego dostęp do konta Logz.io.

### <a name="get-your-logzio-access-token"></a>Pobieranie tokenu dostępu Logz.io

Aby uzyskać token, zaloguj się do konta Logz.io, wybierz ikonę koła zębatego w prawym górnym rogu, a następnie wybierz pozycję **Ustawienia > Ogólne**. Skopiuj token dostępu wyświetlany w ustawieniach konta, aby można było go później użyć.

### <a name="install-and-configure-the-logzio-library-for-log4j-or-logback"></a>Instalowanie i konfigurowanie biblioteki Logz.io pod kątem narzędzi Log4J lub Logback

Biblioteka Java Logz.io jest dostępna w repozytorium Maven Central, więc można ją dodać jako zależność do konfiguracji aplikacji. Sprawdź numer wersji w witrynie Maven Central i użyj najnowszej wersji w poniższych ustawieniach konfiguracyjnych.

Jeśli używasz Maven, dodaj następującą zależność do pliku `pom.xml`:

**Log4J:**

```xml
<dependency>
    <groupId>io.logz.log4j2</groupId>
    <artifactId>logzio-log4j2-appender</artifactId>
    <version>1.0.11</version>
</dependency>
```

**Logback:**

```xml
<dependency>
    <groupId>io.logz.logback</groupId>
    <artifactId>logzio-logback-appender</artifactId>
    <version>1.0.22</version>
</dependency>
```

Jeśli używasz narzędzia Gradle, dodaj następującą zależność do skryptu kompilacji:

**Log4J:**

```
implementation 'io.logz.log4j:logzio-log4j-appender:1.0.11'
```

**Logback:**

```
implementation 'io.logz.logback:logzio-logback-appender:1.0.22'
```

Następnie zaktualizuj plik konfiguracji Log4J lub Logback:

**Log4J:**

```xml
<Appenders>
    <LogzioAppender name="Logzio">
        <logzioToken><your-logz-io-token></logzioToken>
        <logzioType>java-application</logzioType>
        <logzioUrl>https://<your-logz-io-listener-host>:8071</logzioUrl>
    </LogzioAppender>
</Appenders>

<Loggers>
    <Root level="info">
        <AppenderRef ref="Logzio"/>
    </Root>
</Loggers>
```

**Logback:**

```xml
<configuration>
    <!-- Use shutdownHook so that we can close gracefully and finish the log drain -->
    <shutdownHook class="ch.qos.logback.core.hook.DelayingShutdownHook"/>
    <appender name="LogzioLogbackAppender" class="io.logz.logback.LogzioLogbackAppender">
        <token><your-logz-io-token></token>
        <logzioUrl>https://<your-logz-io-listener-host>:8071</logzioUrl>
        <logzioType>java-application</logzioType>
        <filter class="ch.qos.logback.classic.filter.ThresholdFilter">
            <level>INFO</level>
        </filter>
    </appender>

    <root level="debug">
        <appender-ref ref="LogzioLogbackAppender"/>
    </root>
</configuration>
```

Zastąp symbol zastępczy `<your-logz-io-token>` tokenem dostępu, a symbol zastępczy `<your-logz-io-listener-host>` hostem odbiorcy Twojego regionu (na przykład listener.logz.io). Aby uzyskać więcej informacji na temat znajdowania regionu konta, zobacz [Region konta](https://docs.logz.io/user-guide/accounts/account-region.html).

Element `logzioType` odnosi się do pola logicznego aparatu Elasticsearch używanego do rozdzielania dokumentów. Aby w jak największym stopniu skorzystać z usługi Logz.io, należy poprawnie skonfigurować ten parametr.

Typ Logz.io jest formatem dziennika (na przykład: Apache, NGinx, MySQL), a nie źródła (na przykład: serwer1, serwer2, serwer3). Na potrzeby tego samouczka wywołujemy typ `java-application`, ponieważ konfigurujemy aplikacje Java i oczekujemy, że wszystkie te aplikacje mają ten sam format.

Aby uzyskać zaawansowane informacje o użyciu, można zgrupować aplikacje Java w różne typy, które mają własny format dziennika (konfigurowalny z Log4J i Logback). Można na przykład utworzyć typy „spring-boot-monolith” i „spring-boot-microservice”.

### <a name="test-your-configuration-and-log-analysis-on-logzio"></a>Testowanie konfiguracji i analizy dzienników w usłudze Logz.io

Po skonfigurowaniu biblioteki Logz.io aplikacja powinna wysyłać dzienniki bezpośrednio do niej. Aby sprawdzić, czy wszystko działa prawidłowo, przejdź do konsoli Logz.io, wybierz kartę **Live Tail**, a następnie wybierz pozycję **Uruchom**. Powinien zostać wyświetlony komunikat podobny do poniższego, z informacją, że połączenie działa:

```output
Requesting Live Tail access...
Access granted. Opening connection...
Connected. Tailing...
````

Następnie uruchom aplikację lub użyj jej w celu utworzenia dzienników. Dzienniki powinny być widoczne bezpośrednio na ekranie. Na przykład poniżej przedstawiono pierwsze komunikaty uruchamiania aplikacji Spring Boot:

```output
2019-09-19 12:54:40.685Z Starting JavaApp on javaapp-default-9-5cfcb8797f-dfp46 with PID 1 (/workspace/BOOT-INF/classes started by cnb in /workspace)
2019-09-19 12:54:40.686Z The following profiles are active: prod
2019-09-19 12:54:42.052Z Bootstrapping Spring Data repositories in DEFAULT mode.
2019-09-19 12:54:42.169Z Finished Spring Data repository scanning in 103ms. Found 6 repository interfaces.
2019-09-19 12:54:43.426Z Bean 'spring.task.execution-org.springframework.boot.autoconfigure.task.TaskExecutionProperties' of type [org.springframework.boot.autoconfigure.task.TaskExecutionProperties] is not eligible for getting processed by all BeanPostProcessors (for example: not eligible for auto-proxying)
```

Teraz, gdy dzienniki są przetwarzane przez Logz.io, możesz korzystać ze wszystkich usług platformy.

## <a name="send-azure-services-data-to-logzio"></a>Wysyłanie danych usług platformy Azure do Logz.io

Następnie dowiesz się, jak wysyłać dzienniki i metryki z zasobów platformy Azure do usługi Logz.io.

### <a name="deploy-the-template"></a>Wdrożenie szablonu

Pierwszym krokiem jest wdrożenie szablonu integracji Logz.io z Azure. Integracja jest oparta na gotowym szablonie wdrożenia platformy Azure, który konfiguruje wszystkie niezbędne bloki konstrukcyjne potoku. Szablon tworzy przestrzeń nazw centrum zdarzeń, centrum zdarzeń, dwa obiekty blob magazynu oraz wszystkie wymagane uprawnienia i połączenia. Zasoby skonfigurowane przez automatyczne wdrożenie mogą zbierać dane dla jednego regionu świadczenia usługi Azure i dostarczać je do usługi Logz.io.

Znajdź przycisk **Wdróż na platformie Azure** wyświetlany w [pierwszym kroku pliku readme repozytorium](https://github.com/logzio/logzio-azure-serverless).

Po wybraniu opcji **Wdróż w usłudze Azure**zostanie wyświetlona strona **Wdrożenie niestandardowe** w witrynie Azure Portal z listą wstępnie wypełnionych pól.

Większość pól możesz pozostawić bez zmian, ale pamiętaj o wprowadzeniu następujących ustawień:

* **Grupa zasobów**: Wybierz istniejącą grupę lub utwórz nową.
* **Host dzienników/metryk Logz.io**: Wprowadź adres URL odbiorcy Logz.io. Jeśli nie masz pewności co do adresu URL, sprawdź adres URL logowania. Jeśli jest to app.logz.io, użyj adresu listener.logz.io (czyli ustawienia domyślnego). Jeśli jest to app-eu.logz.io, użyj adresu listener-eu.logz.io.
* **Token dzienników/metryk Logz.io**: Wprowadź token konta Logz.io, do którego chcesz wysyłać dzienniki lub metryki platformy Azure. Ten token można znaleźć na stronie konta w interfejsie użytkownika Logz.io.

Zaakceptuj warunki w dolnej części strony, a następnie wybierz pozycję **Purchase** (Kup). Na platformie Azure zostanie wdrożony szablon, co może potrwać minutę lub dwie. W górnej części portalu zobaczysz komunikat „Wdrożenie powiodło się”.

Można odwiedzić zdefiniowaną grupę zasobów, aby przejrzeć wdrożone zasoby.

Aby dowiedzieć się, jak skonfigurować funkcję logzio-azure-serverless do tworzenia kopii zapasowej danych na platformie Azure Blob Storage, zobacz [Ship Azure activity logs](https://docs.logz.io/shipping/log-sources/azure-activity-logs.html) (Dostarczanie dzienników aktywności platformy Azure).

### <a name="stream-azure-logs-and-metrics-to-logzio"></a>Przesyłanie strumieniowe dzienników i metryk platformy Azure do Logz.io

Po wdrożeniu szablonu integracji musisz skonfigurować platformę Azure do przesyłania strumieniowego danych diagnostycznych do centrum zdarzeń, które właśnie wdrożono. Gdy dane są dostarczane do centrum zdarzeń, aplikacja funkcji przekazuje je do Logz.io.

1. Na pasku wyszukiwania wpisz „Diagnostyczne”, a następnie wybierz pozycję **Ustawienia diagnostyczne**.

2. Wybierz zasób z listy zasobów, a następnie wybierz pozycję **Dodaj ustawienia diagnostyczne**, aby otworzyć panel **Ustawienia diagnostyki** dla tego zasobu.

    ![Panel ustawień diagnostycznych](media/java-get-started-with-logzio/diagnostics-settings.png)

3. Określ **nazwę** ustawień diagnostycznych.

4. Wybierz pozycję **Przesyłaj strumieniowo do centrum zdarzeń**, a następnie wybierz pozycję **Skonfiguruj**, aby otworzyć panel **Wybierz centrum zdarzeń**.

5. Wybierz centrum zdarzeń:

    * **Wybierz przestrzeń nazw centrum zdarzeń**: Wybierz przestrzeń nazw, która rozpoczyna się od **Logzio** (na przykład `LogzioNS6nvkqdcci10p`).
    * **Wybierz nazwę centrum zdarzeń**: Dla dzienników wybierz pozycję **insights-operational-logs** a dla metryk — pozycję **insights-operational-metrics**.
    * **Wybierz nazwę zasad centrum zdarzeń**: Wybierz nazwę **LogzioSharedAccessKey**.

6. Naciśnij przycisk **OK**, aby wrócić do panelu **Ustawienia diagnostyczne**.

7. W sekcji Dziennik wybierz dane, które chcesz przesyłać strumieniowo, a następnie wybierz pozycję **Zapisz**.

Wybrane dane zostaną teraz przesłane strumieniowo do centrum zdarzeń.

### <a name="visualize-your-data"></a>Wizualizacja danych

Poczekaj chwilę na przesłanie danych z systemu do usługi Logz.io, a następnie otwórz pulpit Kibana. Na pulpitach nawigacyjnych powinny zostać wyświetlone dane (z typem _eventhub_). Aby uzyskać więcej informacji na temat tworzenia pulpitów nawigacyjnych, zobacz [Creating the Perfect Kibana Dashboard](https://logz.io/blog/perfect-kibana-dashboard/) (Tworzenie najlepszego pulpitu nawigacyjnego Kibana).

W tym miejscu można wykonywać zapytania dotyczące określonych danych na karcie **Odnajdywanie** lub tworzyć obiekty Kibana w celu wizualizacji danych na karcie **Wizualizacja**.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu pracy z zasobami platformy Azure, które zostały utworzone w tym samouczku, można je usunąć za pomocą następującego polecenia:

```azurecli-interactive
az group delete --name <resource group>
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób konfigurowania aplikacji Java i usług platformy Azure w celu wysyłania dzienników i metryk do usługi Logz.io.

Teraz dowiedz się więcej o używaniu centrum zdarzeń do monitorowania aplikacji:

> [!div class="nextstepaction"]
> [Przesyłanie strumieniowe danych monitorowania platformy Azure do centrum zdarzeń](/azure/azure-monitor/platform/stream-monitoring-data-event-hubs)
