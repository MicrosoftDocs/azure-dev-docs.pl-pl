---
title: Migrowanie aplikacji serwera Tomcat do serwera Tomcat w usłudze Azure App Service
description: W tym przewodniku opisano, na co należy zwrócić uwagę, aby przeprowadzić migrację istniejącej aplikacji serwera Tomcat w celu uruchomienia jej w usłudze Azure App Service przy użyciu serwera Tomcat.
author: yevster
ms.author: yebronsh
ms.topic: conceptual
ms.date: 1/20/2020
ms.openlocfilehash: 6e14e8a18f87b67eb0ecb5ce08541058a964c988
ms.sourcegitcommit: 951fc116a9519577b5d35b6fb584abee6ae72b0f
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80612100"
---
# <a name="migrate-tomcat-applications-to-tomcat-on-azure-app-service"></a>Migrowanie aplikacji serwera Tomcat do serwera Tomcat w usłudze Azure App Service

W tym przewodniku opisano, na co należy zwrócić uwagę, aby przeprowadzić migrację istniejącej aplikacji serwera Tomcat w celu uruchomienia jej w usłudze Azure App Service przy użyciu serwera Tomcat 9.0.

## <a name="before-you-start"></a>Przed rozpoczęciem

Jeśli nie można spełnić wymagań wstępnych dla migracji, należy zapoznać się z następującymi przewodnikami dotyczącymi migracji:

* [Migrowanie aplikacji serwera Tomcat do kontenerów w usłudze Azure Kubernetes Service](migrate-tomcat-to-containers-on-azure-kubernetes-service.md)
* Migrowanie aplikacji serwera Tomcat do usługi Azure Virtual Machines (zaplanowane wskazówki)

## <a name="pre-migration"></a>Czynności przed migracją

### <a name="switch-to-a-supported-platform"></a>Przełączanie na obsługiwaną platformę

Usługa App Service oferuje określone wersje serwera Tomcat w określonych wersjach języka Java. Aby zapewnić zgodność, przed wykonaniem wszystkich pozostałych kroków przeprowadź migrację aplikacji do jednej z obsługiwanych wersji serwera Tomcat i języka Java w jej bieżącym środowisku. Pamiętaj, aby w pełni przetestować konfigurację wynikową. W tych testach użyj najnowszej stabilnej wersji dystrybucji systemu Linux.

[!INCLUDE [note-obtain-your-current-java-version](includes/migration/note-obtain-your-current-java-version.md)]

Aby uzyskać informacje na temat bieżącej wersji serwera Tomcat, zaloguj się na serwerze produkcyjnym i uruchom następujące polecenie:

```bash
${CATALINA_HOME}/bin/version.sh
```

Aby uzyskać informacje na temat bieżącej wersji używanej przez usługę Azure App Service, pobierz oprogramowanie [Tomcat 9](https://tomcat.apache.org/download-90.cgi), w zależności od wersji, która ma być używana w usłudze Azure App Service.

[!INCLUDE [inventory-external-resources](includes/migration/inventory-external-resources.md)]

[!INCLUDE [inventory-secrets](includes/migration/inventory-secrets.md)]

[!INCLUDE [inventory-certificates](includes/migration/inventory-certificates.md)]

[!INCLUDE [inventory-persistence-usage](includes/migration/inventory-persistence-usage.md)]

<!-- App-Service-specific addendum to inventory-persistence-usage -->
#### <a name="dynamic-or-internal-content"></a>Zawartość dynamiczna lub wewnętrzna

Na potrzeby plików często zapisywanych i odczytywanych przez aplikację (na przykład plików danych tymczasowych) lub plików statycznych, które są widoczne tylko dla aplikacji, możesz zainstalować usługę Azure Storage w systemie plików usługi App Service. Aby uzyskać więcej informacji, zobacz [Obsługa zawartości z usługi Azure Storage w usłudze App Service w systemie Linux](/azure/app-service/containers/how-to-serve-content-from-azure-storage).

### <a name="identify-session-persistence-mechanism"></a>Określanie mechanizmu trwałości sesji

Aby zidentyfikować używanego menedżera trwałości sesji, sprawdź pliki *context.xml* w aplikacji i konfiguracji serwera Tomcat. Poszukaj elementu `<Manager>`, a następnie zanotuj wartość atrybutu `className`.

Wbudowane na serwerze Tomcat implementacje aplikacji [PersistentManager](https://tomcat.apache.org/tomcat-9.0-doc/config/manager.html), na przykład [StandardManager](https://tomcat.apache.org/tomcat-9.0-doc/config/manager.html#Standard_Implementation) czy [FileStore](https://tomcat.apache.org/tomcat-9.0-doc/config/manager.html#Nested_Components), nie są przeznaczone do użycia z rozproszoną, skalowaną platformą, taką jak usługa App Service. Ponieważ usługa App Service może równoważyć obciążenie między kilkoma wystąpieniami i w dowolnym momencie w niewidoczny sposób uruchomić ponownie dowolne wystąpienie, nie zaleca się utrwalania modyfikowalnego stanu w systemie plików.

Jeśli jest wymagana trwałość sesji, musisz użyć alternatywnej implementacji aplikacji `PersistentManager`, która będzie zapisywać dane w zewnętrznym magazynie danych, takim jak Pivotal Session Manager z usługą Redis Cache. Aby uzyskać więcej informacji, zobacz [Korzystanie z usługi Redis jako pamięci podręcznej sesji na serwerze Tomcat](/azure/app-service/containers/configure-language-java#use-redis-as-a-session-cache-with-tomcat).

### <a name="special-cases"></a>Specjalne przypadki

Niektóre scenariusze produkcyjne mogą wymagać dodatkowych zmian lub wprowadzać dodatkowe ograniczenia. Chociaż takie scenariusze mogą być rzadko spotykane, ważne jest, aby upewnić się, że nie dotyczą aplikacji lub są prawidłowo obsługiwane.

#### <a name="determine-whether-application-relies-on-scheduled-jobs"></a>Określanie, czy aplikacja korzysta z zaplanowanych zadań

Z usługą App Service nie można używać zaplanowanych zadań, takich jak zadania Quartz Scheduler lub cron. Usługa App Service nie uniemożliwi wdrożenia aplikacji zawierającej wewnętrznie zaplanowane zadania. Jeśli jednak aplikacja jest skalowana w poziomie, to samo zaplanowane zadanie może zostać uruchomione więcej niż raz w zaplanowanym okresie. Ta sytuacja może prowadzić do niezamierzonych konsekwencji.

Utwórz spis wszystkich zaplanowanych zadań, wewnątrz lub na zewnątrz serwera aplikacji.

#### <a name="determine-whether-your-application-contains-os-specific-code"></a>Określanie, czy aplikacja zawiera kod właściwy dla systemu operacyjnego

Jeśli aplikacja zawiera dowolny kod z zależnościami w systemie operacyjnym hosta, musisz przeprowadzić jej refaktoryzację, aby usunąć te zależności. Na przykład może być konieczne zastąpienie każdego użycia symbolu `/` lub `\` w ścieżkach systemu plików przez [`File.Separator`](https://docs.oracle.com/javase/8/docs/api/java/io/File.html#separator) lub [`Paths.get`](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Paths.html#get-java.lang.String-java.lang.String...-).

#### <a name="determine-whether-tomcat-clustering-is-used"></a>Określanie, czy jest używane klastrowanie serwera Tomcat

Usługa Azure App Service nie obsługuje [klastrowania serwera Tomcat](https://tomcat.apache.org/tomcat-9.0-doc/cluster-howto.html). W zamian można skonfigurować skalowanie i równoważenie obciążenia oraz zarządzać nimi za pośrednictwem usługi Azure App Service bez funkcji specyficznych dla serwera Tomcat. Stan sesji można utrwalić w innej lokalizacji, aby udostępnić ją w replikach. Aby uzyskać więcej informacji, zobacz [Określanie mechanizmu trwałości sesji](#identify-session-persistence-mechanism).

Aby ustalić, czy aplikacja używa klastrowania, poszukaj elementu `<Cluster>` wewnątrz elementów `<Host>` lub `<Engine>` w pliku *server.xml*.

#### <a name="identify-all-outside-processesdaemons-running-on-the-production-servers"></a>Identyfikacja wszystkich procesów/demonów zewnętrznych działających na serwerach produkcyjnych

Wszystkie procesy działające poza serwerem aplikacji, takie jak demony monitorowania, musisz zmigrować do innego miejsca lub wyeliminować.

#### <a name="determine-whether-non-http-connectors-are-used"></a>Określanie, czy są używane łączniki inne niż HTTP

Usługa App Service obsługuje tylko pojedynczy łącznik HTTP. Jeśli aplikacja wymaga dodatkowych łączników, na przykład łącznika AJP, nie używaj usługi App Service.

Aby zidentyfikować łączniki HTTP używane przez aplikację, poszukaj elementów `<Connector>` wewnątrz pliku *server.xml* w konfiguracji serwera Tomcat.

#### <a name="determine-whether-memoryrealm-is-used"></a>Określanie, czy jest używana klasa MemoryRealm

Klasa [MemoryRealm](https://tomcat.apache.org/tomcat-9.0-doc/api/org/apache/catalina/realm/MemoryRealm.html) wymaga utrwalonego pliku XML. W usłudze Azure AppService musisz przekazać ten plik do katalogu */home* lub jego podkatalogu bądź do zainstalowanego magazynu. Musisz odpowiednio zmodyfikować parametr `pathName`.

Aby ustalić, czy klasa `MemoryRealm` jest aktualnie używana, sprawdź, czy w plikach *server.xml* i *context.xml* znajdują się elementy `<Realm>`, których atrybut `className` jest ustawiony na `org.apache.catalina.realm.MemoryRealm`.

#### <a name="determine-whether-ssl-session-tracking-is-used"></a>Określanie, czy jest używane śledzenie sesji SSL

Usługa App Service wykonuje odciążanie sesji poza środowiskiem uruchomieniowym serwera Tomcat. W związku z tym nie możesz używać [śledzenia sesji SSL](https://tomcat.apache.org/tomcat-9.0-doc/servletapi/javax/servlet/SessionTrackingMode.html#SSL). W zamian użyj innego trybu śledzenia sesji (`COOKIE` lub `URL`). Jeśli potrzebujesz śledzenia sesji SSL, nie używaj usługi App Service.

#### <a name="determine-whether-accesslogvalve-is-used"></a>Określanie, czy jest używana klasa AccessLogValve

Jeśli używasz klasy [AccessLogValve](https://tomcat.apache.org/tomcat-9.0-doc/api/org/apache/catalina/valves/AccessLogValve.html), musisz ustawić parametr `directory` na `/home/LogFiles` lub jego podkatalog.

## <a name="migration"></a>Migracja

### <a name="parameterize-the-configuration"></a>Parametryzacja konfiguracji

Podczas wstępnej migracji w plikach *server.xml* i *context.xml* prawdopodobnie zostały zidentyfikowane klucze tajne i zależności zewnętrzne, takie jak źródła danych. W przypadku każdego zidentyfikowanego w ten sposób elementu zastąp wszelkie nazwy użytkownika, hasła, parametry połączenia lub adresy URL zmienną środowiskową.

Na przykład załóżmy, że plik *context.xml* zawiera następujący element:

```xml
<Resource
    name="jdbc/dbconnection"
    type="javax.sql.DataSource"
    url="jdbc:postgresql://postgresdb.contoso.com/wickedsecret?ssl=true"
    driverClassName="org.postgresql.Driver"
    username="postgres"
    password="t00secure2gue$$"
/>
```

W takim przypadku można go zmienić w sposób pokazany w następującym przykładzie:

```xml
<Resource
    name="jdbc/dbconnection"
    type="javax.sql.DataSource"
    url="${postgresdb.connectionString}"
    driverClassName="org.postgresql.Driver"
    username="${postgresdb.username}"
    password="${postgresdb.password}"
/>
```

### <a name="provision-an-app-service-plan"></a>Aprowizacja planu usługi App Service

Z listy dostępnych planów usług na stronie [App Service — cennik](https://azure.microsoft.com/pricing/details/app-service/linux/) wybierz plan, którego specyfikacje spełniają lub przekraczają specyfikacje obecnego sprzętu produkcyjnego.

> [!NOTE]
> Jeśli planujesz uruchamianie wdrożeń przejściowych/kanarkowych lub korzystanie z miejsc wdrożenia, plan usługi App Service musi uwzględniać dodatkową pojemność. W przypadku aplikacji języka Java zalecamy korzystanie z planów Premium lub wyższych. Aby uzyskać więcej informacji, zobacz [Konfigurowanie środowisk przejściowych w usłudze Azure App Service](/azure/app-service/deploy-staging-slots).

Następnie utwórz plan usługi App Service. Aby uzyskać więcej informacji, zobacz [Zarządzanie planem usługi App Service na platformie Azure](/azure/app-service/app-service-plan-manage).

### <a name="create-and-deploy-web-apps"></a>Tworzenie i wdrażanie aplikacji internetowych

Dla każdego pliku WAR wdrożonego na serwerze Tomcat musisz utworzyć aplikację internetową w ramach planu usługi App Service (wybierając wersję serwera Tomcat jako stos środowiska uruchomieniowego).

> [!NOTE]
> Chociaż istnieje możliwość wdrożenia wielu plików WAR w pojedynczej aplikacji internetowej, jest to wysoce niepożądane. Wdrożenie wielu plików WAR w pojedynczej aplikacji internetowej uniemożliwia skalowanie poszczególnych aplikacji zgodnie z ich wymaganiami dotyczącymi użycia. Zwiększa również złożoność kolejnych potoków wdrażania. Jeśli pod pojedynczym adresem URL musi być dostępnych wiele aplikacji, rozważ użycie rozwiązania routingu, takiego jak [Azure Application Gateway](/azure/application-gateway/).

#### <a name="maven-applications"></a>Aplikacje Maven

Jeśli aplikacja została skompilowana z pliku POM narzędzia Maven, [użyj wtyczki Webapp dla narzędzia Maven](/azure/app-service/containers/quickstart-java#configure-the-maven-plugin), aby utworzyć aplikację internetową i wdrożyć aplikację.

#### <a name="non-maven-applications"></a>Aplikacje inne niż Maven

Jeśli nie możesz użyć wtyczki Maven, musisz zaaprowizować aplikację internetową za pomocą innych mechanizmów, takich jak:

* [Azure Portal](https://portal.azure.com/#create/Microsoft.WebSite)
* [Interfejs wiersza polecenia platformy Azure](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create)
* [Azure PowerShell](/powershell/module/az.websites/new-azwebapp)

Po utworzeniu aplikacji internetowej użyj jednego z [dostępnych mechanizmów wdrażania](/azure/app-service/deploy-zip), aby wdrożyć aplikację.

### <a name="migrate-jvm-runtime-options"></a>Migracja opcji środowiska uruchomieniowego JVM

Jeśli aplikacja wymaga określonych opcji środowiska uruchomieniowego, [użyj najbardziej odpowiedniego mechanizmu, aby je określić](/azure/app-service/containers/configure-language-java#set-java-runtime-options).

### <a name="populate-secrets"></a>Wypełnianie wpisów tajnych

Do przechowywania wszelkich wpisów tajnych specyficznych dla aplikacji użyj ustawień aplikacji. Jeśli zamierzasz używać tych samych wpisów tajnych w wielu aplikacjach lub potrzebujesz szczegółowych zasad dostępu i funkcji inspekcji, zamiast tego [skorzystaj z usługi Azure Key Vault](/azure/app-service/containers/configure-language-java#use-keyvault-references).

[!INCLUDE [configure-custom-domain-and-ssl](includes/migration/configure-custom-domain-and-ssl.md)]

[!INCLUDE [import-backend-certificates](includes/migration/import-backend-certificates.md)]

### <a name="migrate-data-sources-libraries-and-jndi-resources"></a>Migracja źródeł danych, bibliotek i zasobów JNDI

Aby poznać kroki konfiguracji źródła danych, zapoznaj się z sekcją [Źródła danych](/azure/app-service/containers/configure-language-java#data-sources) artykułu [Konfigurowanie aplikacji Java systemu Linux dla usługi Azure App Service](/azure/app-service/containers/configure-language-java).

[!INCLUDE[Tomcat datasource additional instructions](includes/migration/tomcat-datasource-additional-instructions.md)]

Przeprowadź migrację wszelkich dodatkowych zależności ścieżki klasy na poziomie serwera, wykonując [te same czynności co w przypadku plików JAR źródła danych](/azure/app-service/containers/configure-language-java#finalize-configuration).

Przeprowadź migrację wszelkich dodatkowych [udostępnionych zasobów JDNI na poziomie serwera](/azure/app-service/containers/configure-language-java#shared-server-level-resources).

> [!NOTE]
> Jeśli korzystasz z zalecanej architektury obejmującej jeden plik WAR na każdą aplikację internetową, rozważ przeprowadzenie migracji bibliotek ścieżki klasy i zasobów JNDI na poziomie serwera do swojej aplikacji. Znacznie uprości to nadzór nad składnikami i zarządzanie zmianami.

### <a name="migrate-remaining-configuration"></a>Migracja pozostałej konfiguracji

Po ukończeniu poprzedniej sekcji w katalogu */home/tomcat/conf* powinna znajdować się dostosowywalna konfiguracja serwera.

Przeprowadź migrację, kopiując wszelkie dodatkowe konfiguracje (na przykład [obszary](https://tomcat.apache.org/tomcat-9.0-doc/config/realm.html) i [JASPIC](https://tomcat.apache.org/tomcat-9.0-doc/config/jaspic.html))

[!INCLUDE [migrate-scheduled-jobs](includes/migration/migrate-scheduled-jobs.md)]

### <a name="restart-and-smoke-test"></a>Ponowne uruchamianie i test dymny

Na koniec musisz ponownie uruchomić aplikację internetową, aby zastosować wszystkie zmiany konfiguracji. Po zakończeniu ponownego uruchamiania sprawdź, czy aplikacja działa prawidłowo.

## <a name="post-migration"></a>Czynności po migracji

Po przeprowadzeniu migracji aplikacji do usługi Azure App Service sprawdź, czy działa ona zgodnie z oczekiwaniami. Po wykonaniu tych czynności skorzystaj z naszych zaleceń, dzięki którym Twoja aplikacja będzie bardziej natywna dla chmury.

### <a name="recommendations"></a>Zalecenia

* Jeśli przechowujesz pliki w katalogu */home*, rozważ [zastąpienie go usługą Azure Storage](/azure/app-service/containers/how-to-serve-content-from-azure-storage).

* Jeśli konfiguracja znajduje się w katalogu */home*, który zawiera parametry połączenia, klucze SSL i inne informacje dotyczące wpisów tajnych, rozważ użycie usługi [Azure Key Vault](/azure/app-service/app-service-key-vault-references) w połączeniu ze [wstrzykiwaniem parametrów z ustawieniami aplikacji](/azure/app-service/configure-common#configure-app-settings) (tam, gdzie to możliwe).

* Rozważ [użycie miejsc wdrożenia](/azure/app-service/deploy-staging-slots), aby uzyskać niezawodne wdrożenia bez przestojów.

* Zaprojektuj i zaimplementuj strategię DevOps. Aby zachować niezawodność przy jednoczesnym zwiększaniu szybkości tworzenia rozwiązań, rozważ [automatyzację wdrożeń i testowania przy użyciu usługi Azure Pipelines](/azure/devops/pipelines/ecosystems/java-webapp). Jeśli używasz miejsc wdrożenia, możesz [zautomatyzować wdrożenie w miejscu](/azure/devops/pipelines/targets/webapp?view=azure-devops&tabs=yaml#deploy-to-a-slot) i kolejną zamianę miejsc.

* Zaprojektuj i zaimplementuj strategię ciągłości działania i odzyskiwania po awarii. W przypadku aplikacji o krytycznym znaczeniu rozważ zastosowanie [architektury wdrażania w wielu regionach](/azure/architecture/reference-architectures/app-service-web-app/multi-region).
