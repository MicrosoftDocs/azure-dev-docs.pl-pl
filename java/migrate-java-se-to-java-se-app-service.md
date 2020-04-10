---
title: Migrowanie aplikacji Java SE do środowiska Java SE w usłudze Azure App Service
description: W tym przewodniku opisano, co należy wiedzieć, aby zmigrować istniejącą aplikację Spring Boot lub inną autonomiczną aplikację internetową do usługi Azure App Service przy użyciu środowiska Java SE.
author: yevster
ms.author: yebronsh
ms.topic: conceptual
ms.date: 01/22/2019
ms.openlocfilehash: 91292d50f49bde2b76084f8a09119ae74a20f72f
ms.sourcegitcommit: 951fc116a9519577b5d35b6fb584abee6ae72b0f
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80612111"
---
# <a name="migrate-executable-jar-web-applications-to-java-se-on-azure-app-service"></a>Migrowanie wykonywalnych aplikacji internetowych JAR do środowiska Java SE w usłudze Azure App Service

W tym przewodniku opisano, co należy wiedzieć, aby zmigrować istniejącą aplikację Spring Boot lub inną aplikację internetową z osadzonym serwerem do usługi Azure App Service przy użyciu środowiska Java SE.

## <a name="before-you-start"></a>Przed rozpoczęciem

Jeśli nie można spełnić wymagań wstępnych dla migracji, należy zapoznać się z następującymi przewodnikami dotyczącymi migracji:

* Migrowanie wykonywalnych aplikacji JAR do kontenerów w usłudze Azure Kubernetes Service (zaplanowane wskazówki)
* Migrowanie wykonywalnych aplikacji JAR do usługi Azure Virtual Machines (zaplanowane wskazówki)

## <a name="pre-migration"></a>Czynności przed migracją

### <a name="switch-to-a-supported-platform"></a>Przełączanie na obsługiwaną platformę

Usługa App Service oferuje określone wersje środowiska Java SE. Aby zapewnić zgodność, należy przeprowadzić migrację aplikacji do jednej z obsługiwanych wersji bieżącego środowiska przed kontynuowaniem pracy i wykonaniem pozostałych kroków. Pamiętaj, aby w pełni przetestować konfigurację wynikową. W tych testach użyj najnowszej stabilnej wersji dystrybucji systemu Linux.

[!INCLUDE [note-obtain-your-current-java-version](includes/migration/note-obtain-your-current-java-version.md)]

### <a name="inventory-external-resources"></a>Utworzenie spisu zasobów zewnętrznych

Zidentyfikuj zasoby zewnętrzne, takie jak źródła danych, brokery komunikatów JMS i adresy URL innych usług. W aplikacjach Spring Boot można zwykle znaleźć konfigurację dla takich zasobów w katalogu *src/main/directory* w pliku mającym zazwyczaj nazwę *application.properties* lub *application.yml*. Ponadto sprawdź zmienne środowiskowe wdrożenia produkcyjnego pod kątem odpowiednich ustawień konfiguracji.

#### <a name="databases"></a>Bazy danych

W przypadku dowolnej bazy danych SQL zidentyfikuj parametry połączenia.

W przypadku aplikacji Spring Boot parametry połączenia zwykle są widoczne w plikach konfiguracji.

Oto przykład z pliku *application.properties*:

```properties
spring.datasource.url=jdbc:mysql://localhost:3306/mysql_db
spring.datasource.username=dbuser
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
```

Oto przykład z pliku *application.yaml*:

```yaml
spring:
  data:
    mongodb:
      uri: mongodb://mongouser:deepsecret@mongoserver.contoso.com:27017
```

Aby uzyskać więcej informacji, zobacz sekcje [JPA Repositories](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#jpa.repositories) (Repozytoria JPA) i [JDBC Repositories](https://docs.spring.io/spring-data/jdbc/docs/current/reference/html/#jdbc.repositories) (Repozytoria JDBC) w dokumentacji platformy Spring.

#### <a name="jms-message-brokers"></a>Brokery komunikatów JMS

Określ, które brokery są używane. Można to osiągnąć poprzez zbadanie manifestu kompilacji (zwykle w pliku *pom.xml* lub *build.gradle*) pod kątem odpowiednich zależności.

Na przykład aplikacja Spring Boot korzystająca z brokera ActiveMQ zwykle zawiera w pliku *pom.xml* następującą zależność:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-activemq</artifactId>
</dependency>
```

Aplikacje Spring Boot używające zastrzeżonych brokerów zwykle zawierają zależności bezpośrednio w bibliotekach sterowników JMS brokerów. Oto przykład z pliku *build.gradle*:

```json
    dependencies {
      ...
      compile("com.ibm.mq:com.ibm.mq.allclient:9.0.4.0")
      ...
    }
```

Po zidentyfikowaniu używanych brokerów znajdź odpowiednie ustawienia, które zwykle znajdują się w plikach *application.properties* i *application.yml* dla aplikacji Spring Boot.

Oto przykład z pliku *application.properties*:

```properties
spring.activemq.brokerurl=broker:(tcp://localhost:61616,network:static:tcp://remotehost:61616)?persistent=false&useJmx=true
spring.activemq.user=admin
spring.activemq.password=tryandguess
```

Oto przykład z pliku *application.yaml*:

```yaml
ibm:
  mq:
    queueManager: qm1
    channel: dev.ORDERS
    connName: localhost(14)
    user: admin
    password: big$ecr3t
```

#### <a name="all-other-external-resources"></a>Wszystkie inne zasoby zewnętrzne

Nie jest możliwe udokumentowanie każdej możliwej zależności zewnętrznej w tym przewodniku. Twój zespół odpowiada za sprawdzenie, czy każda zależność zewnętrzna aplikacji może być spełniona po migracji do usługi App Service.

### <a name="inventory-secrets"></a>Utworzenie spisu wpisów tajnych

#### <a name="passwords-and-secure-strings"></a>Hasła i bezpieczne ciągi

Sprawdź wszystkie pliki właściwości i konfiguracji oraz wszystkie zmienne środowiskowe we wdrożeniach produkcyjnych pod kątem wszelkich ciągów wpisów tajnych i haseł. W aplikacji Spring Boot takie ciągi będą prawdopodobnie znajdować się w plikach *application.properties* lub *application.yml*.

[!INCLUDE [inventory-certificates](includes/migration/inventory-certificates.md)]

[!INCLUDE [inventory-persistence-usage](includes/migration/inventory-persistence-usage.md)]

### <a name="special-cases"></a>Specjalne przypadki

Niektóre scenariusze produkcyjne mogą wymagać dodatkowych zmian lub wprowadzać dodatkowe ograniczenia. Chociaż takie scenariusze mogą być rzadko spotykane, ważne jest, aby upewnić się, że nie dotyczą aplikacji lub są prawidłowo obsługiwane.

#### <a name="determine-whether-application-relies-on-scheduled-jobs"></a>Określanie, czy aplikacja korzysta z zaplanowanych zadań

Z usługą App Service nie można używać zaplanowanych zadań, takich jak zadania Quartz Scheduler lub cron. Usługa App Service nie uniemożliwi wdrożenia aplikacji zawierającej wewnętrznie zaplanowane zadania. Jeśli jednak aplikacja jest skalowana w poziomie, to samo zaplanowane zadanie może zostać uruchomione więcej niż raz w zaplanowanym okresie. Ta sytuacja może prowadzić do niezamierzonych konsekwencji.

Utwórz spis wszystkich zaplanowanych zadań, wewnątrz lub na zewnątrz procesu aplikacji.

#### <a name="determine-whether-your-application-contains-os-specific-code"></a>Określanie, czy aplikacja zawiera kod właściwy dla systemu operacyjnego

Jeśli aplikacja zawiera dowolny kod uwzględniający system operacyjny, w którym jest uruchomiona aplikacja, należy refaktoryzować aplikację, aby NIE POLEGAŁA na bazowym systemie operacyjnym. Na przykład może być konieczne zastąpienie wszystkich przypadków użycia elementów `/` lub `\` w ścieżkach systemu plików za pomocą elementów [`File.Separator`](https://docs.oracle.com/javase/8/docs/api/java/io/File.html#separator) lub [`Path.get`](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Paths.html#get-java.lang.String-java.lang.String...-).

#### <a name="identify-all-outside-processesdaemons-running-on-the-production-servers"></a>Identyfikacja wszystkich procesów/demonów zewnętrznych działających na serwerach produkcyjnych

Procesy działające poza serwerem aplikacji, takie jak demony monitorowania, należy migrować do innego miejsca lub wyeliminować je.

#### <a name="identify-handling-of-non-http-requests-or-multiple-ports"></a>Identyfikacja obsługi żądań innych niż HTTP lub wielu portów

Usługa App Service obsługuje tylko jeden punkt końcowy HTTP na jednym porcie. Jeśli aplikacja prowadzi nasłuch na wielu portach lub akceptuje żądania przy użyciu protokołów innych niż HTTP, nie należy używać usługi Azure App Service.

## <a name="migration"></a>Migracja

### <a name="parameterize-the-configuration"></a>Parametryzacja konfiguracji

Upewnij się, że wszystkie współrzędne zasobów zewnętrznych (takie jak parametry połączenia bazy danych) i inne dostosowywalne ustawienia mogą być odczytywane ze zmiennych środowiskowych. W przypadku migrowania aplikacji Spring Boot wszystkie ustawienia konfiguracji powinny już być wystąpieniami typu Externalizable. Aby uzyskać więcej informacji, zobacz sekcję [Externalized Configuration](https://docs.spring.io/spring-boot/docs/current/reference/html/spring-boot-features.html#boot-features-external-config) (Konfiguracja uzewnętrzniona) w dokumentacji środowiska Spring Boot.

Oto przykład, który odwołuje się do zmiennej środowiskowej `SERVICEBUS_CONNECTION_STRING` z pliku *application.properties*:

```properties
spring.jms.servicebus.connection-string=${SERVICEBUS_CONNECTION_STRING}
spring.jms.servicebus.topic-client-id=contoso1
spring.jms.servicebus.idle-timeout=10000
```

### <a name="provision-an-app-service-plan"></a>Aprowizacja planu usługi App Service

Z [listy dostępnych planów usług](https://azure.microsoft.com/pricing/details/app-service/linux/) wybierz plan, którego specyfikacje spełniają lub przekraczają specyfikacje obecnego sprzętu produkcyjnego.

> [!NOTE]
> Jeśli planujesz uruchamianie wdrożeń przejściowych/kanarkowych lub korzystanie z [miejsc wdrożenia](/azure/app-service/deploy-staging-slots), plan usługi App Service musi uwzględniać dodatkową pojemność. W przypadku aplikacji języka Java zalecamy korzystanie z planów Premium lub wyższych.

[Utwórz plan usługi App Service](/azure/app-service/app-service-plan-manage#create-an-app-service-plan).

### <a name="create-and-deploy-web-apps"></a>Tworzenie i wdrażanie aplikacji internetowych

Musisz utworzyć aplikację internetową w planie usługi App Service (wybierając opcję „Java SE” jako stos środowiska uruchomieniowego) dla każdego wykonywalnego pliku JAR, który zamierzasz uruchomić.

#### <a name="maven-applications"></a>Aplikacje Maven

Jeśli aplikacja została skompilowana z pliku POM narzędzia Maven, [użyj wtyczki Webapp dla narzędzia Maven](/azure/java/spring-framework/deploy-spring-boot-java-app-with-maven-plugin#configure-maven-plugin-for-azure-app-service), aby utworzyć aplikację internetową i wdrożyć aplikację.

#### <a name="non-maven-applications"></a>Aplikacje inne niż Maven

Jeśli nie możesz użyć wtyczki Maven, musisz zaaprowizować aplikację internetową za pomocą innych mechanizmów, takich jak:

* [Azure Portal](https://portal.azure.com/#create/Microsoft.WebSite)
* [Interfejs wiersza polecenia platformy Azure](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create)
* [Azure PowerShell](/powershell/module/az.websites/new-azwebapp)

Po utworzeniu aplikacji internetowej użyj jednego z [dostępnych mechanizmów wdrażania](/azure/app-service/deploy-ftp), aby wdrożyć aplikację. Jeśli to możliwe, aplikacja powinna zostać przekazana do lokalizacji */home/site/wwwroot/app.jar*. Jeśli nie chcesz zmienić nazwy pliku JAR na app.jar, możesz przekazać skrypt powłoki z poleceniem uruchamiającym plik JAR. Następnie wklej pełną ścieżkę do tego skryptu w polu tekstowym [Plik startowy](/azure/app-service/containers/app-service-linux-faq#built-in-images) w sekcji Konfiguracja w portalu. Skrypt startowy nie jest uruchamiany z katalogu, w którym został umieszczony. W związku z tym zawsze należy używać ścieżek bezwzględnych do odwoływania się do plików w skrypcie startowym (na przykład: `java -jar /home/myapp/myapp.jar`).

### <a name="migrate-jvm-runtime-options"></a>Migracja opcji środowiska uruchomieniowego JVM

Jeśli aplikacja wymaga określonych opcji środowiska uruchomieniowego, [użyj najbardziej odpowiedniego mechanizmu, aby je określić](/azure/app-service/containers/configure-language-java#set-java-runtime-options).

[!INCLUDE [configure-custom-domain-and-ssl](includes/migration/configure-custom-domain-and-ssl.md)]

[!INCLUDE [import-backend-certificates](includes/migration/import-backend-certificates.md)]

### <a name="migrate-external-resource-coordinates-and-other-settings"></a>Migrowanie współrzędnych zasobów zewnętrznych i innych ustawień

Wykonaj [poniższe czynności, aby przeprowadzić migrację parametrów połączenia i innych ustawień](/azure/app-service/containers/configure-language-java#spring-boot-1).

> [!NOTE]
> W przypadku jakichkolwiek ustawień aplikacji Spring Boot parametryzowanych przy użyciu zmiennych w sekcji [Parametryzacja konfiguracji](#parameterize-the-configuration) należy określić te zmienne w konfiguracji aplikacji. Wszystkie ustawienia aplikacji Spring Boot, które nie są jawnie parametryzowane przy użyciu zmiennych środowiskowych, mogą być w dalszym ciągu zastępowane przez konfigurację aplikacji. Przykład:

  ```properties
  spring.jms.servicebus.connection-string=${CUSTOMCONNSTR_SERVICE_BUS}
  spring.jms.servicebus.topic-client-id=contoso1
  spring.jms.servicebus.idle-timeout=1800000
  ```

![Konfiguracja aplikacji usługi App Service](media/migrate-java-se-to-java-se-app-service/app-service-parameterized-spring-boot-app-settings.png)

[!INCLUDE [migrate-scheduled-jobs](includes/migration/migrate-scheduled-jobs.md)]

### <a name="restart-and-smoke-test"></a>Ponowne uruchamianie i test dymny

Na koniec musisz ponownie uruchomić aplikację internetową, aby zastosować wszystkie zmiany konfiguracji. Po zakończeniu ponownego uruchamiania sprawdź, czy aplikacja działa prawidłowo.

## <a name="post-migration"></a>Czynności po migracji

Po przeprowadzeniu migracji aplikacji do usługi Azure App Service sprawdź, czy działa ona zgodnie z oczekiwaniami. Po wykonaniu tych czynności skorzystaj z naszych zaleceń, które mogą sprawić, że aplikacja będzie bardziej natywna w chmurze.

### <a name="recommendations"></a>Zalecenia

* Jeśli przechowujesz pliki w katalogu */home*, rozważ [zastąpienie go usługą Azure Storage](/azure/app-service/containers/how-to-serve-content-from-azure-storage).

* Jeśli konfiguracja znajduje się w katalogu */home*, który zawiera parametry połączenia, klucze SSL i inne informacje dotyczące wpisów tajnych, rozważ użycie usługi [Azure Key Vault](/azure/app-service/app-service-key-vault-references) i/lub [iniekcji parametrów z ustawieniami aplikacji](/azure/app-service/configure-common#configure-app-settings), jeśli jest to możliwe.

* Rozważ [użycie miejsc wdrożenia](/azure/app-service/deploy-staging-slots), aby uzyskać niezawodne wdrożenia bez przestojów.

* Zaprojektuj i zaimplementuj strategię DevOps. Aby zachować niezawodność przy jednoczesnym zwiększaniu szybkości tworzenia rozwiązań, rozważ [automatyzację wdrożeń i testowania przy użyciu usługi Azure Pipelines](/azure/devops/pipelines/ecosystems/java-webapp). Jeśli używasz miejsc wdrożenia, możesz [zautomatyzować wdrożenie w miejscu](/azure/devops/pipelines/targets/webapp?view=azure-devops&tabs=yaml#deploy-to-a-slot) i kolejną zamianę miejsc.

* Zaprojektuj i zaimplementuj strategię ciągłości działania i odzyskiwania po awarii. W przypadku aplikacji o krytycznym znaczeniu rozważ zastosowanie [architektury wdrażania w wielu regionach](/azure/architecture/reference-architectures/app-service-web-app/multi-region).
