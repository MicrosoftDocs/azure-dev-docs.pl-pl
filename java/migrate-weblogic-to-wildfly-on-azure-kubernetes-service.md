---
title: Migrowanie aplikacji serwera WebLogic do serwera WildFly w usłudze Azure Kubernetes Service
description: W tym przewodniku opisano, na co należy zwrócić uwagę, aby przeprowadzić migrację istniejącej aplikacji serwera WebLogic w celu uruchomienia jej na serwerze WildFly w kontenerze usługi Azure Kubernetes Service.
author: mriem
ms.author: manriem
ms.topic: conceptual
ms.date: 2/28/2020
ms.openlocfilehash: 4ec631d6b426e039dbb6ec6933fc5146bed69166
ms.sourcegitcommit: 56e5f51daf6f671f7b6e84d4c6512473b35d31d2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2020
ms.locfileid: "78897389"
---
# <a name="migrate-weblogic-applications-to-wildfly-on-azure-kubernetes-service"></a>Migrowanie aplikacji serwera WebLogic do serwera WildFly w usłudze Azure Kubernetes Service

W tym przewodniku opisano, na co należy zwrócić uwagę, aby przeprowadzić migrację istniejącej aplikacji serwera WebLogic w celu uruchomienia jej na serwerze WildFly w kontenerze usługi Azure Kubernetes Service.

## <a name="before-you-start"></a>Przed rozpoczęciem

Jeśli nie możesz spełnić wymagań wstępnych dla migracji, zapoznaj się z towarzyszącym przewodnikiem dotyczącymi migracji:

* [Migrowanie aplikacji serwera WebLogic do usługi Azure Virtual Machines](migrate-weblogic-to-virtual-machines.md)

## <a name="pre-migration"></a>Czynności przed migracją

[!INCLUDE [inventory-server-capacity-aks](includes/migration/inventory-server-capacity-aks.md)]

[!INCLUDE [inventory-all-secrets](includes/migration/inventory-all-secrets.md)]

[!INCLUDE [inventory-all-certificates](includes/migration/inventory-all-certificates.md)]

[!INCLUDE [inventory-jndi-resources](includes/migration/inventory-jndi-resources.md)]

### <a name="determine-whether-session-replication-is-used"></a>Określenie, czy jest używana replikacja sesji

Jeśli aplikacja korzysta z replikacji sesji, z modułem Oracle Coherence*Web lub bez niego, masz dwie możliwości:

* Refaktoryzacja aplikacji w celu użycia bazy danych do zarządzania sesją.
* Refaktoryzacja aplikacji w celu eksternalizacji sesji do usługi Azure Redis. Aby uzyskać więcej informacji, zobacz [Pamięć podręczna Azure Cache for Redis](/azure/azure-cache-for-redis/cache-overview).

[!INCLUDE [document-datasources](includes/migration/document-datasources.md)]

[!INCLUDE [determine-whether-weblogic-has-been-customized](includes/migration/determine-whether-weblogic-has-been-customized.md)]

[!INCLUDE [determine-whether-a-connection-to-on-premises-is-needed](includes/migration/determine-whether-a-connection-to-on-premises-is-needed.md)]

[!INCLUDE [determine-whether-jms-queues-or-topics-are-in-use](includes/migration/determine-whether-jms-queues-or-topics-are-in-use.md)]

[!INCLUDE [determine-whether-you-are-using-your-own-custom-created-shared-java-ee-libraries](includes/migration/determine-whether-you-are-using-your-own-custom-created-shared-java-ee-libraries.md)]

[!INCLUDE [determine-whether-osgi-bundles-are-used](includes/migration/determine-whether-osgi-bundles-are-used.md)]

[!INCLUDE [determine-whether-your-application-contains-os-specific-code](includes/migration/determine-whether-your-application-contains-os-specific-code.md)]

[!INCLUDE [determine-whether-oracle-service-bus-is-in-use](includes/migration/determine-whether-oracle-service-bus-is-in-use.md)]

[!INCLUDE [determine-whether-your-application-is-composed-of-multiple-wars](includes/migration/determine-whether-your-application-is-composed-of-multiple-wars.md)]

[!INCLUDE [determine-whether-your-application-is-packaged-as-an-ear](includes/migration/determine-whether-your-application-is-packaged-as-an-ear.md)]

<!-- AKS-specific extension of the last INCLUDE. -->
> [!NOTE]
> Aby mieć możliwość niezależnego skalowania poszczególnych aplikacji internetowych w celu lepszego wykorzystania zasobów usługi AKS, należy podzielić plik EAR na oddzielne aplikacje internetowe.
<!-- end extension -->

[!INCLUDE [identify-all-outside-processes-and-daemons-running-on-the-production-servers](includes/migration/identify-all-outside-processes-and-daemons-running-on-the-production-servers.md)]

### <a name="validate-that-the-supported-java-version-works-correctly"></a>Sprawdzanie, czy obsługiwana wersja języka Java działa poprawnie

Korzystanie z serwera WildFly w usłudze Azure Kubernetes Service wymaga określonej wersji środowiska Java. Dlatego musisz sprawdzić, czy Twoja aplikacja może działać prawidłowo, używając tej obsługiwanej wersji. Ta weryfikacja jest szczególnie ważna, jeśli bieżący serwer używa obsługiwanego zestawu JDK (na przykład Oracle JDK lub IBM OpenJ9).

Aby uzyskać informacje na temat bieżącej wersji, zaloguj się na serwerze produkcyjnym i uruchom następujące polecenie:

```bash
java -version
```

[!INCLUDE [determine-whether-your-application-relies-on-scheduled-jobs](includes/migration/determine-whether-your-application-relies-on-scheduled-jobs.md)]

### <a name="determine-whether-weblogic-scripting-tool-wlst-is-used"></a>Ustalanie, czy jest używane narzędzie WebLogic Scripting Tool (WLST)

Jeśli wdrożenie jest wykonywane za pomocą narzędzia WLST, należy ocenić jego działanie. Jeśli narzędzie WLST zmienia jakiekolwiek parametry czasu wykonywania aplikacji w ramach wdrożenia, należy upewnić się, że te parametry są zgodne z jedną z następujących opcji:

* Są one uzewnętrzniane jako ustawienia aplikacji.
* Są one osadzone w aplikacji.
* Podczas wdrażania korzystają one z interfejsu wiersza polecenia JBoss.

Jeśli narzędzie WLST wykonuje więcej niż wspomniano powyżej, będziesz mieć kilka dodatkowych rzeczy do zrobienia podczas migracji.

### <a name="determine-whether-your-application-uses-weblogic-specific-apis"></a>Określanie, czy aplikacja używa interfejsów API specyficznych dla serwera WebLogic

Jeśli Twoja aplikacja używa interfejsów API specyficznych dla serwera WebLogic, należy wykonać jej refaktoryzację w celu usunięcia tych zależności. Na przykład jeśli użyto klasy wymienionej w [dokumentacji interfejsu Java API dla programu Oracle WebLogic Server](https://docs.oracle.com/en/middleware/fusion-middleware/weblogic-server/12.2.1.4/wlapi/index.html?overview-summary.html), to w aplikacji zastosowano interfejs API specyficzny dla serwera WebLogic.

[!INCLUDE [determine-whether-your-application-uses-entity-beans](includes/migration/determine-whether-your-application-uses-entity-beans.md)]

[!INCLUDE [determine-whether-the-java-ee-application-client-feature-is-in-use-aks](includes/migration/determine-whether-the-java-ee-application-client-feature-is-in-use-aks.md)]

### <a name="determine-whether-a-deployment-plan-was-used"></a>Określanie, czy użyto planu wdrożenia

Jeśli aplikacja została wdrożona przy użyciu planu wdrożenia, należy ocenić, co ten plan robi. Jeśli plan wdrożenia to proste wdrożenie, będzie można wdrożyć aplikację internetową bez wprowadzania żadnych zmian. Jeśli plan wdrożenia jest bardziej rozbudowany, będzie trzeba określić, czy da się użyć interfejsu wiersza polecenia JBoss do poprawnego skonfigurowania aplikacji w ramach wdrożenia. Jeśli nie da się użyć interfejsu wiersza polecenia JBoss, konieczna będzie refaktoryzacja aplikacji w taki sposób, aby plan wdrożenia nie był już potrzebny.

[!INCLUDE [determine-whether-ejb-timers-are-in-use](includes/migration/determine-whether-ejb-timers-are-in-use.md)]

### <a name="validate-whether-and-how-the-file-system-is-used"></a>Sprawdzanie, czy i jak jest używany system plików

Każde użycie systemu plików na serwerze aplikacji będzie wymagało ponownej konfiguracji lub, w rzadkich przypadkach, zmiany architektury. System plików może być używany przez moduły udostępnione serwera WebLogic lub przez kod aplikacji. Można zidentyfikować niektóre lub wszystkie scenariusze opisane w poniższych sekcjach.

#### <a name="read-only-static-content"></a>Zawartość statyczna tylko do odczytu

Jeśli aplikacja aktualnie obsługuje zawartość statyczną, potrzebna jest dodatkowa lokalizacja. Warto rozważyć przeniesienie zawartości statycznej do usługi Azure Blob Storage i dodanie usługi Azure CDN, aby zapewnić błyskawiczne pobieranie na całym świecie. Aby uzyskać więcej informacji, zobacz [Hostowanie statycznej witryny internetowej w usłudze Azure Storage](/azure/storage/blobs/storage-blob-static-website) oraz [przewodnik Szybki start: integracja konta magazynu platformy Azure z usługą Azure CDN](/azure/cdn/cdn-create-a-storage-account-with-cdn).

#### <a name="dynamically-published-static-content"></a>Dynamicznie publikowana zawartość statyczna

Jeśli aplikacja zezwala na zawartość statyczną, która została przekazana/utworzona przez aplikację, ale pozostaje niezmienna po jej utworzeniu, możesz użyć usług Azure Blob Storage i Azure CDN, jak opisano powyżej, oraz usługi Azure Function do obsługiwania przekazywania i odświeżania usługi CDN. Udostępniliśmy przykładową implementację do użycia w temacie [Przekazywanie zawartości statycznej i jej wstępne ładowanie w usłudze CDN za pomocą usługi Azure Functions](https://github.com/Azure-Samples/functions-java-push-static-contents-to-cdn).

#### <a name="dynamic-or-internal-content"></a>Zawartość dynamiczna lub wewnętrzna

Na potrzeby plików często zapisywanych i odczytywanych przez aplikację (na przykład plików danych tymczasowych) lub plików statycznych, które są widoczne tylko dla aplikacji, możesz zainstalować udziały plików usługi Azure Storage jako trwałe woluminy. Aby uzyskać więcej informacji, zobacz [Dynamiczne tworzenie i korzystanie z trwałego woluminu za pomocą usługi Azure Files w usłudze Azure Kubernetes Service](/azure/aks/azure-files-dynamic-pv).

### <a name="determine-whether-jca-connectors-are-used"></a>Określanie, czy są używane łączniki JCA

Jeśli Twoja aplikacja korzysta z łączników JCA, musisz sprawdzić, czy łącznik JCA może być używany na serwerze WildFly. Jeśli implementacja JCA jest powiązana z serwerem WebLogic, trzeba będzie wykonać refaktoryzację aplikacji w celu usunięcia tej zależności. Jeśli można go używać, trzeba będzie dodać pliki JAR do ścieżki klas serwera i umieścić niezbędne pliki konfiguracyjne w prawidłowej lokalizacji w katalogach serwera WildFly, aby były dostępne.

[!INCLUDE [determine-whether-your-application-uses-a-resource-adapter](includes/migration/determine-whether-your-application-uses-a-resource-adapter.md)]

[!INCLUDE [determine-whether-jaas-is-in-use](includes/migration/determine-whether-jaas-is-in-use.md)]

### <a name="determine-whether-weblogic-clustering-is-used"></a>Ustalanie, czy jest używane klastrowanie serwera WebLogic

W większości przypadków aplikację wdraża się na wielu serwerach WebLogic w celu zapewnienia wysokiej dostępności. Usługa Azure Kubernetes Service jest w stanie wykonywać skalowanie, ale jeśli korzystasz z interfejsu Cluster API serwera WebLogic, będzie trzeba przeprowadzić refaktoryzację kodu, aby wyeliminować użycie tego interfejsu API.

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

Po przeprowadzeniu migracji aplikacji do usługi Azure Kubernetes Service należy sprawdzić, czy działa ona zgodnie z oczekiwaniami. Po wykonaniu tej czynności zapoznaj się z poniższymi rekomendacjami, aby uczynić aplikację bardziej natywną dla chmury.

[!INCLUDE [recommendations-wildfly-on-aks](includes/migration/recommendations-wildfly-on-aks.md)]
