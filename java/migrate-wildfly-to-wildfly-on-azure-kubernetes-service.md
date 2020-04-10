---
title: Migrowanie aplikacji serwera WildFly do serwera WildFly w usłudze Azure Kubernetes Service
description: W tym przewodniku opisano, na co należy zwrócić uwagę, aby przeprowadzić migrację istniejącej aplikacji serwera WildFly w celu uruchomienia jej na serwerze WildFly w kontenerze usługi Azure Kubernetes Service.
author: mriem
ms.author: manriem
ms.topic: conceptual
ms.date: 3/16/2020
ms.openlocfilehash: 892000065b26a11dd332481abc75f8b73d207890
ms.sourcegitcommit: 951fc116a9519577b5d35b6fb584abee6ae72b0f
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80613117"
---
# <a name="migrate-wildfly-applications-to-wildfly-on-azure-kubernetes-service"></a>Migrowanie aplikacji serwera WildFly do serwera WildFly w usłudze Azure Kubernetes Service

W tym przewodniku opisano, na co należy zwrócić uwagę, aby przeprowadzić migrację istniejącej aplikacji serwera WildFly w celu uruchomienia jej na serwerze WildFly w kontenerze usługi Azure Kubernetes Service.

## <a name="pre-migration"></a>Czynności przed migracją

[!INCLUDE [inventory-server-capacity-aks](includes/migration/inventory-server-capacity-aks.md)]

### <a name="inventory-all-secrets"></a>Utworzenie spisu wszystkich wpisów tajnych

Sprawdź wszystkie pliki właściwości i konfiguracji na serwerach produkcyjnych kątem jakichkolwiek wpisów tajnych i haseł. Sprawdź plik *jboss-web.xml* w swoich plikach WAR. Wewnątrz aplikacji możesz również znaleźć pliki konfiguracji zawierające hasła lub poświadczenia.

Rozważ przechowywanie tych wpisów tajnych w usłudze Azure Key Vault. Aby uzyskać więcej informacji, zobacz [Podstawowe pojęcia dotyczące usługi Azure Key Vault](/azure/key-vault/basic-concepts).

[!INCLUDE [inventory-all-certificates](includes/migration/inventory-all-certificates.md)]

### <a name="validate-that-the-supported-java-version-works-correctly"></a>Sprawdzanie, czy obsługiwana wersja języka Java działa poprawnie

Korzystanie z serwera WildFly w usłudze Azure Kubernetes Service wymaga określonej wersji środowiska Java. Dlatego musisz sprawdzić, czy Twoja aplikacja może działać prawidłowo, używając tej obsługiwanej wersji. Ta weryfikacja jest szczególnie ważna, jeśli bieżący serwer używa obsługiwanego zestawu JDK (na przykład Oracle JDK lub IBM OpenJ9).

Aby uzyskać informacje na temat bieżącej wersji, zaloguj się na serwerze produkcyjnym i uruchom to polecenie:

```bash
java -version
```

Zobacz sekcję [Requirements](http://docs.wildfly.org/19/Getting_Started_Guide.html#requirements) (Wymagania), aby dowiedzieć się, której wersji należy użyć, aby uruchomić serwer WildFly.

### <a name="inventory-jndi-resources"></a>Utworzenie spisu zasobów JNDI

Utwórz spis wszystkich zasobów JNDI. Niektóre elementy, takie jak broker komunikatów JMS, mogą wymagać migracji lub ponownej konfiguracji.

### <a name="determine-whether-session-replication-is-used"></a>Określenie, czy jest używana replikacja sesji

Jeśli Twoja aplikacja korzysta z replikacji sesji, należy zmienić aplikację, aby usunąć tę zależność.

#### <a name="inside-your-application"></a>W aplikacji

Sprawdź plik *WEB-INF/jboss-web.xml* i/lub plik *WEB-INF/web.xml*.

### <a name="document-datasources"></a>Udokumentowanie źródeł danych

Jeśli aplikacja korzysta z dowolnych baz danych, należy przechwycić następujące informacje:

* Jaka jest nazwa źródła danych?
* Jaka jest konfiguracja puli połączeń?
* Gdzie mogę znaleźć plik JAR sterownika JDBC?

Aby uzyskać więcej informacji, zobacz sekcję [DataSource Configuration](http://docs.wildfly.org/19/Admin_Guide.html#DataSource) (Konfiguracja źródła danych) w dokumentacji serwera WildFly.

### <a name="determine-whether-and-how-the-file-system-is-used"></a>Określanie, czy i jak jest używany system plików

Każde użycie systemu plików na serwerze aplikacji będzie wymagało ponownej konfiguracji lub, w rzadkich przypadkach, zmiany architektury. System plików może być używany przez moduły serwera WildFly lub kod aplikacji. Można zidentyfikować niektóre lub wszystkie scenariusze opisane w poniższych sekcjach.

#### <a name="read-only-static-content"></a>Zawartość statyczna tylko do odczytu

Jeśli aplikacja aktualnie obsługuje zawartość statyczną, potrzebna jest dodatkowa lokalizacja. Warto rozważyć przeniesienie zawartości statycznej do usługi Azure Blob Storage i dodanie usługi Azure CDN, aby zapewnić błyskawiczne pobieranie na całym świecie. Aby uzyskać więcej informacji, zobacz [Hostowanie statycznej witryny internetowej w usłudze Azure Storage](/azure/storage/blobs/storage-blob-static-website) oraz [przewodnik Szybki start: integracja konta magazynu platformy Azure z usługą Azure CDN](/azure/cdn/cdn-create-a-storage-account-with-cdn).

#### <a name="dynamically-published-static-content"></a>Dynamicznie publikowana zawartość statyczna

Jeśli aplikacja zezwala na zawartość statyczną, która została przekazana/utworzona przez aplikację, ale pozostaje niezmienna po jej utworzeniu, możesz użyć usług Azure Blob Storage i Azure CDN, jak opisano powyżej, oraz usługi Azure Function do obsługiwania przekazywania i odświeżania usługi CDN. Udostępniliśmy przykładową implementację do użycia w temacie [Przekazywanie zawartości statycznej i jej wstępne ładowanie w usłudze CDN za pomocą usługi Azure Functions](https://github.com/Azure-Samples/functions-java-push-static-contents-to-cdn).

#### <a name="dynamic-or-internal-content"></a>Zawartość dynamiczna lub wewnętrzna

Na potrzeby plików często zapisywanych i odczytywanych przez aplikację (na przykład plików danych tymczasowych) lub plików statycznych, które są widoczne tylko dla aplikacji, możesz zainstalować udziały plików usługi Azure Storage jako trwałe woluminy. Aby uzyskać więcej informacji, zobacz [Dynamiczne tworzenie i korzystanie z trwałego woluminu za pomocą usługi Azure Files w usłudze Azure Kubernetes Service](/azure/aks/azure-files-dynamic-pv).

[!INCLUDE [determine-whether-your-application-relies-on-scheduled-jobs](includes/migration/determine-whether-your-application-relies-on-scheduled-jobs.md)]

[!INCLUDE [determine-whether-a-connection-to-on-premises-is-needed](includes/migration/determine-whether-a-connection-to-on-premises-is-needed.md)]

[!INCLUDE [determine-whether-jms-queues-or-topics-are-in-use](includes/migration/determine-whether-jms-queues-or-topics-are-in-use.md)]

[!INCLUDE [determine-whether-your-application-uses-entity-beans](includes/migration/determine-whether-your-application-uses-entity-beans.md)]

[!INCLUDE [determine-whether-the-java-ee-application-client-feature-is-in-use-aks](includes/migration/determine-whether-the-java-ee-application-client-feature-is-in-use-aks.md)]

[!INCLUDE [determine-whether-your-application-contains-os-specific-code](includes/migration/determine-whether-your-application-contains-os-specific-code.md)]

[!INCLUDE [determine-whether-ejb-timers-are-in-use](includes/migration/determine-whether-ejb-timers-are-in-use.md)]

### <a name="determine-whether-jca-connectors-are-in-use"></a>Określanie, czy są używane łączniki JCA

Jeśli Twoja aplikacja korzysta z łączników JCA, musisz sprawdzić, czy łącznik JCA może być używany na serwerze WildFly. Jeśli implementacja JCA jest powiązana z serwerem WildFly, trzeba będzie wykonać refaktoryzację aplikacji w celu usunięcia tej zależności. Jeśli można go używać, trzeba będzie dodać pliki JAR do ścieżki klas serwera i umieścić niezbędne pliki konfiguracyjne w prawidłowej lokalizacji w katalogach serwera WildFly, aby były dostępne.

[!INCLUDE [determine-whether-jaas-is-in-use](includes/migration/determine-whether-jaas-is-in-use.md)]

[!INCLUDE [determine-whether-your-application-uses-a-resource-adapter](includes/migration/determine-whether-your-application-uses-a-resource-adapter.md)]

[!INCLUDE [determine-whether-your-application-is-composed-of-multiple-wars](includes/migration/determine-whether-your-application-is-composed-of-multiple-wars.md)]

### <a name="determine-whether-your-application-is-packaged-as-an-ear"></a>Ustalanie, czy aplikacja jest spakowana jako plik EAR

Jeśli Twoja aplikacja jest spakowana jako plik EAR, przejrzyj i zarejestruj konfigurację pliku *application.xml*.

> [!NOTE]
> Aby mieć możliwość niezależnego skalowania poszczególnych aplikacji internetowych w celu lepszego wykorzystania zasobów usługi AKS, należy podzielić plik EAR na oddzielne aplikacje internetowe.

[!INCLUDE [identify-all-outside-processes-and-daemons-running-on-the-production-servers](includes/migration/identify-all-outside-processes-and-daemons-running-on-the-production-servers.md)]

[!INCLUDE [perform-in-place-testing](includes/migration/perform-in-place-testing.md)]

## <a name="migration"></a>Migracja

[!INCLUDE [provision-azure-container-registry-and-azure-kubernetes-service](includes/migration/provision-azure-container-registry-and-azure-kubernetes-service.md)]

[!INCLUDE [create-a-docker-image-for-wildfly](includes/migration/create-a-docker-image-for-wildfly.md)]

[!INCLUDE [build-and-push-the-docker-image-to-azure-container-registry](includes/migration/build-and-push-the-docker-image-to-azure-container-registry.md)]

[!INCLUDE [provision-a-public-ip-address](includes/migration/provision-a-public-ip-address.md)]

[!INCLUDE [deploy-to-aks](includes/migration/deploy-to-aks.md)]

### <a name="configure-persistent-storage"></a>Konfigurowanie magazynu trwałego

Jeśli aplikacja wymaga magazynu trwałego, należy skonfigurować co najmniej jeden [wolumin trwały](/azure/aks/azure-disks-dynamic-pv).

[!INCLUDE [migrate-scheduled-jobs-aks](includes/migration/migrate-scheduled-jobs-aks.md)]

## <a name="post-migration"></a>Czynności po migracji

Po przeprowadzeniu migracji aplikacji do usługi Azure Kubernetes Service należy sprawdzić, czy działa ona zgodnie z oczekiwaniami. Po wykonaniu tych czynności skorzystaj z naszych zaleceń, które mogą sprawić, że aplikacja będzie bardziej natywna dla chmury.

[!INCLUDE [recommendations-wildfly-on-aks](includes/migration/recommendations-wildfly-on-aks.md)]
