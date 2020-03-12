---
title: Migrowanie aplikacji serwera Tomcat do kontenerów w usłudze Azure Kubernetes Service
description: W tym przewodniku opisano, na co należy zwrócić uwagę, aby przeprowadzić migrację istniejącej aplikacji serwera Tomcat w celu uruchomienia jej w kontenerze usługi Azure Kubernetes Service.
author: yevster
ms.author: yebronsh
ms.topic: conceptual
ms.date: 1/20/2020
ms.openlocfilehash: fafe7b16b14f43f6fe97090de8964c4e78796bda
ms.sourcegitcommit: 56e5f51daf6f671f7b6e84d4c6512473b35d31d2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2020
ms.locfileid: "78893744"
---
# <a name="migrate-tomcat-applications-to-containers-on-azure-kubernetes-service"></a>Migrowanie aplikacji serwera Tomcat do kontenerów w usłudze Azure Kubernetes Service

W tym przewodniku opisano, na co należy zwrócić uwagę, aby przeprowadzić migrację istniejącej aplikacji serwera Tomcat w celu uruchomienia jej w usłudze Azure Kubernetes Service (AKS).

## <a name="pre-migration"></a>Czynności przed migracją

[!INCLUDE [inventory-external-resources](includes/migration/inventory-external-resources.md)]

[!INCLUDE [inventory-secrets](includes/migration/inventory-secrets.md)]

[!INCLUDE [inventory-persistence-usage](includes/migration/inventory-persistence-usage.md)]

<!-- AKS-specific addendum to inventory-persistence-usage -->
#### <a name="dynamic-or-internal-content"></a>Zawartość dynamiczna lub wewnętrzna

Na potrzeby plików często zapisywanych i odczytywanych przez aplikację (na przykład plików danych tymczasowych) lub plików statycznych, które są widoczne tylko dla aplikacji, możesz zainstalować udziały plików usługi Azure Storage jako trwałe woluminy. Aby uzyskać więcej informacji, zobacz [Dynamiczne tworzenie i korzystanie z trwałego woluminu za pomocą usługi Azure Files w usłudze Azure Kubernetes Service](/azure/aks/azure-files-dynamic-pv).

### <a name="identify-session-persistence-mechanism"></a>Określanie mechanizmu trwałości sesji

Aby zidentyfikować używanego menedżera trwałości sesji, sprawdź pliki *context.xml* w aplikacji i konfiguracji serwera Tomcat. Poszukaj elementu `<Manager>`, a następnie zanotuj wartość atrybutu `className`.

Wbudowane na serwerze Tomcat implementacje aplikacji [PersistentManager](https://tomcat.apache.org/tomcat-8.5-doc/config/manager.html), na przykład [StandardManager](https://tomcat.apache.org/tomcat-8.5-doc/config/manager.html#Standard_Implementation) czy [FileStore](https://tomcat.apache.org/tomcat-8.5-doc/config/manager.html#Nested_Components), nie są przeznaczone do użycia z rozproszoną, skalowaną platformą, taką jak usługa Kubernetes. Usługa AKS może równoważyć obciążenie między kilkoma zasobnikami i w dowolnym momencie w niewidoczny sposób uruchomić ponownie dowolny zasobnik, więc nie zaleca się utrwalania modyfikowalnego stanu w systemie plików.

Jeśli jest wymagana trwałość sesji, musisz użyć alternatywnej implementacji aplikacji `PersistentManager`, która będzie zapisywać dane w zewnętrznym magazynie danych, takim jak Pivotal Session Manager z usługą Redis Cache. Aby uzyskać więcej informacji, zobacz [Korzystanie z usługi Redis jako pamięci podręcznej sesji na serwerze Tomcat](/azure/app-service/containers/configure-language-java#use-redis-as-a-session-cache-with-tomcat).

### <a name="special-cases"></a>Specjalne przypadki

Niektóre scenariusze produkcyjne mogą wymagać dodatkowych zmian lub wprowadzać dodatkowe ograniczenia. Chociaż takie scenariusze mogą być rzadko spotykane, ważne jest, aby upewnić się, że nie dotyczą aplikacji lub są prawidłowo obsługiwane.

#### <a name="determine-whether-application-relies-on-scheduled-jobs"></a>Określanie, czy aplikacja korzysta z zaplanowanych zadań

Nie można używać zaplanowanych zadań, takich jak zadania Quartz Scheduler lub cron, z konteneryzowanymi wdrożeniami usługi Tomcat. Jeśli aplikacja jest skalowana w poziomie, jedno zaplanowane zadanie może być uruchamiane więcej niż jeden raz w zaplanowanym okresie. Ta sytuacja może prowadzić do niezamierzonych konsekwencji.

Utwórz spis wszystkich zaplanowanych zadań, wewnątrz lub na zewnątrz serwera aplikacji.

#### <a name="determine-whether-your-application-contains-os-specific-code"></a>Określanie, czy aplikacja zawiera kod właściwy dla systemu operacyjnego

Jeśli aplikacja zawiera dowolny kod uwzględniający system operacyjny, w którym jest uruchomiona aplikacja, należy refaktoryzować aplikację, aby NIE POLEGAŁA na bazowym systemie operacyjnym. Na przykład może być konieczne zastąpienie wszystkich przypadków użycia elementów `/` lub `\` w ścieżkach systemu plików za pomocą elementów [`File.Separator`](https://docs.oracle.com/javase/8/docs/api/java/io/File.html#separator) lub [`Path.get`](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Paths.html#get-java.lang.String-java.lang.String...-).

#### <a name="determine-whether-memoryrealm-is-used"></a>Określanie, czy jest używana klasa MemoryRealm

Klasa [MemoryRealm](https://tomcat.apache.org/tomcat-9.0-doc/api/org/apache/catalina/realm/MemoryRealm.html) wymaga utrwalonego pliku XML. W usłudze Kubernetes ten plik musi zostać dodany do obrazu kontenera lub przekazany do [udostępnionego magazynu, który jest dostępny dla kontenerów](#identify-session-persistence-mechanism). Należy odpowiednio zmodyfikować parametr `pathName`.

Aby ustalić, czy klasa `MemoryRealm` jest aktualnie używana, sprawdź, czy w plikach *server.xml* i *context.xml* znajdują się elementy `<Realm>`, których atrybut `className` jest ustawiony na `org.apache.catalina.realm.MemoryRealm`.

#### <a name="determine-whether-ssl-session-tracking-is-used"></a>Określanie, czy jest używane śledzenie sesji SSL

W przypadku konteneryzowanych wdrożeń sesje SSL są zwykle odciążane poza kontenerem aplikacji, najczęściej przez kontroler ruchu przychodzącego. Jeśli aplikacja wymaga [śledzenia sesji SSL](https://tomcat.apache.org/tomcat-9.0-doc/servletapi/javax/servlet/SessionTrackingMode.html#SSL), upewnij się, że ruch SSL jest bezpośrednio przesyłany do kontenera aplikacji.

#### <a name="determine-whether-accesslogvalve-is-used"></a>Określanie, czy jest używana klasa AccessLogValve

W przypadku użycia klasy [AccessLogValve](https://tomcat.apache.org/tomcat-9.0-doc/api/org/apache/catalina/valves/AccessLogValve.html) parametr `directory` należy ustawić na [zainstalowany udział plików usługi Azure Files](/azure/aks/azure-files-dynamic-pv) lub jeden z jego podkatalogów.

### <a name="in-place-testing"></a>Testowanie w miejscu

Przed utworzeniem obrazów kontenerów dokonaj migracji aplikacji do zestawu JDK i serwera Tomcat, których zamierzasz używać w usłudze AKS. Dokładnie przetestuj swoją aplikację, aby zapewnić jej zgodność i wydajność.

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

## <a name="migration"></a>Migracja

Z wyjątkiem pierwszego kroku („Aprowizacja rejestru kontenerów i usługi AKS”) zalecamy wykonanie poniższych czynności osobno dla każdej aplikacji (plik WAR), którą chcesz migrować.

> [!NOTE]
> Niektóre wdrożenia produktu Tomcat mogą mieć wiele aplikacji uruchomionych na jednym serwerze Tomcat. Jeśli tak jest w tym wdrożeniu, zdecydowanie zalecamy uruchomienie każdej aplikacji w oddzielnym zasobniku. Pozwala to zoptymalizować wykorzystanie zasobów dla każdej aplikacji przy jednoczesnym zminimalizowaniu złożoności i sprzężenia klas.

### <a name="provision-container-registry-and-aks"></a>Aprowizacja rejestru kontenerów i usługi AKS

Utwórz rejestr kontenerów i klaster usługi Azure Kubernetes, którego nazwa główna usługi ma rolę czytelnika w rejestrze. Upewnij się, że [wybrano odpowiedni model sieci](/azure/aks/operator-best-practices-network#choose-the-appropriate-network-model) dopasowany do wymagań w zakresie łączności sieciowej klastra.

```bash
az group create -g $resourceGroup -l eastus
az acr create -g $resourceGroup -n $acrName --sku Standard
az aks create -g $resourceGroup -n $aksName --attach-acr $acrName --network-plugin azure
```

### <a name="prepare-the-deployment-artifacts"></a>Przygotowanie komputera do wdrożenia

Sklonuj [repozytorium GitHub z przewodnika szybkiego startu „Tomcat w kontenerach”](https://github.com/Azure/tomcat-container-quickstart). Repozytorium zawiera pliki konfiguracji Dockerfile i Tomcat z wieloma zalecanymi optymalizacjami. W poniższych krokach przedstawimy zmiany, które prawdopodobnie trzeba będzie wprowadzić w tych plikach przed skompilowaniem obrazu kontenera i wdrożeniem go do usługi AKS.

#### <a name="open-ports-for-clustering-if-needed"></a>Otwieranie portów dla klastrowania, jeśli jest konieczne

Jeśli zamierzasz używać [klastrowania Tomcat](https://tomcat.apache.org/tomcat-9.0-doc/cluster-howto.html) w usłudze AKS, upewnij się, że wymagane zakresy portów są uwidocznione w pliku Dockerfile. Aby określić adres IP serwera w pliku *server.xml*, należy użyć wartości ze zmiennej, która jest inicjowana przy uruchamianiu kontenera pod adresem IP zasobnika.

Alternatywnie stan sesji można [utrwalić w lokalizacji alternatywnej](#identify-session-persistence-mechanism), aby był dostępny w różnych replikach.

Aby ustalić, czy aplikacja używa klastrowania, poszukaj elementu `<Cluster>` wewnątrz elementów `<Host>` lub `<Engine>` w pliku *server.xml*.

#### <a name="add-jndi-resources"></a>Dodawanie zasobów JNDI

Edytuj plik *server.xml*, aby dodać zasoby przygotowane w ramach kroków poprzedzających migrację, takie jak źródła danych.

Przykład:

```xml
<!-- Global JNDI resources
      Documentation at /docs/jndi-resources-howto.html
-->
<GlobalNamingResources>
    <!-- Editable user database that can also be used by
         UserDatabaseRealm to authenticate users
    -->
    <Resource name="UserDatabase" auth="Container"
              type="org.apache.catalina.UserDatabase"
              description="User database that can be updated and saved"
              factory="org.apache.catalina.users.MemoryUserDatabaseFactory"
              pathname="conf/tomcat-users.xml"
               />

    <!-- Migrated datasources here: -->
    <Resource
        name="jdbc/dbconnection"
        type="javax.sql.DataSource"
        url="${postgresdb.connectionString}"
        driverClassName="org.postgresql.Driver"
        username="${postgresdb.username}"
        password="${postgresdb.password}"
    />
    <!-- End of migrated datasources -->
</GlobalNamingResources>
```

### <a name="build-and-push-the-image"></a>Kompilacja i wypychanie obrazu

Najprostszym sposobem kompilowania i przekazywania obrazu do usługi Azure Container Registry (ACR) do użycia przez usługę AKS jest zastosowanie polecenia `az acr build`. To polecenie nie wymaga, aby platforma Docker była zainstalowana na komputerze. Na przykład jeśli masz powyższy plik Dockerfile oraz pakiet aplikacji *petclinic.war* w bieżącym katalogu, możesz utworzyć obraz kontenera w usłudze ACR przy użyciu jednego kroku:

```bash
az acr build -t "${acrName}.azurecr.io/petclinic:{{.Run.ID}}" -r $acrName --build-arg APP_FILE=petclinic.war --build-arg=prod.server.xml .
```

Możesz pominąć parametr `--build-arg APP_FILE...`, jeśli plik WAR ma nazwę *ROOT.war*. Możesz pominąć parametr `--build-arg SERVER_XML...`, jeśli plik XML serwera ma nazwę *server.xml*. Oba pliki muszą znajdować się w tym samym katalogu co *plik Dockerfile*.

Można też użyć interfejsu wiersza polecenia platformy Docker, aby utworzyć obraz lokalnie. Takie podejście może uprościć testowanie i udoskonalanie obrazu przed początkowym wdrożeniem w usłudze ACR. Jednak wymaga instalacji interfejsu wiersza polecenia platformy Docker i uruchomienia demona platformy Docker.

```bash
# Build the image locally
sudo docker build . --build-arg APP_FILE=petclinic.war -t "${acrName}.azurecr.io/petclinic:1"

# Run the image locally
sudo docker run -d -p 8080:8080 "${acrName}.azurecr.io/petclinic:1"

# Your application can now be accessed with a browser at http://localhost:8080.

# Log into ACR
sudo az acr login -n $acrName

# Push the image to ACR
sudo docker push "${acrName}.azurecr.io/petclinic:1"
```

Aby uzyskać bardziej szczegółowe informacje na temat tworzenia i przechowywania obrazów kontenerów na platformie Azure, zobacz odpowiednie [kursy Microsoft Learn](/learn/modules/build-and-store-container-images/).

### <a name="provision-a-public-ip-address"></a>Aprowizacja publicznego adresu IP

Jeśli aplikacja ma być dostępna spoza sieci wewnętrznej lub wirtualnej, będzie wymagany publiczny, statyczny adres IP. Ten adres IP powinien być aprowizowany wewnątrz grupy zasobów węzła klastra.

```bash
nodeResourceGroup=$(az aks show -g $resourceGroup -n $aksName --query 'nodeResourceGroup' -o tsv)
publicIp=$(az network public-ip create -g $nodeResourceGroup -n applicationIp --sku Standard --allocation-method Static --query 'publicIp.ipAddress' -o tsv)
echo "Your public IP address is ${publicIp}."
```

### <a name="deploy-to-aks"></a>Wdrażanie w usłudze AKS

[Twórz i stosuj pliki Kubernetes YAML](/azure/aks/kubernetes-walkthrough#run-the-application). Jeśli tworzysz zewnętrzny moduł równoważenia obciążenia (do aplikacji lub kontrolera ruchu przychodzącego), upewnij się, że adres IP aprowizowany w poprzedniej sekcji zostanie podany jako `LoadBalancerIP`.

Uwzględnij [zewnętrzne parametry jako zmienne środowiskowe](https://kubernetes.io/docs/tasks/inject-data-application/define-environment-variable-container/). Nie dołączaj wpisów tajnych (takich jak hasła, klucze interfejsu API i parametry połączenia JDBC). Wpisy tajne znajdują się w sekcji [Konfiguracja usługi KeyVault FlexVolume](#configure-keyvault-flexvolume).

### <a name="configure-persistent-storage"></a>Konfigurowanie magazynu trwałego

Jeśli aplikacja wymaga magazynu trwałego, należy skonfigurować co najmniej jeden [wolumin trwały](/azure/aks/azure-disks-dynamic-pv).

Możesz chcieć utworzyć wolumin trwały przy użyciu usługi Azure Files zainstalowanej w katalogu dzienników Tomcat ( */tomcat_logs*), aby zachowywać dzienniki centralnie. Aby uzyskać więcej informacji, zobacz [Dynamiczne tworzenie i korzystanie z trwałego woluminu za pomocą usługi Azure Files w usłudze Azure Kubernetes Service (AKS)](/azure/aks/azure-files-dynamic-pv).

### <a name="configure-keyvault-flexvolume"></a>Konfigurowanie usługi KeyVault FlexVolume

[Utwórz usługę Azure KeyVault](/azure/key-vault/quick-create-cli) i wypełnij wszystkie niezbędne wpisy tajne. Następnie skonfiguruj usługę [KeyVault FlexVolume](https://github.com/Azure/kubernetes-keyvault-flexvol/blob/master/README.md), aby udostępnić te wpisy tajne w zasobnikach.

Aby zaimportować certyfikaty do lokalnego magazynu kluczy w kontenerze, należy zmodyfikować skrypt uruchamiania (*startup.sh* w repozytorium GitHub [Tomcat w kontenerach](https://github.com/Azure/tomcat-container-quickstart)).

### <a name="migrate-scheduled-jobs"></a>Migrowanie zaplanowanych zadań

Aby wykonać zaplanowane zadania w klastrze usługi AKS, zdefiniuj [zadania cron](https://kubernetes.io/docs/tasks/job/automated-tasks-with-cron-jobs/) wedle potrzeby.

## <a name="post-migration"></a>Czynności po migracji

Po przeprowadzeniu migracji aplikacji do usługi AKS należy sprawdzić, czy działa ona zgodnie z oczekiwaniami. Po wykonaniu tych czynności skorzystaj z naszych zaleceń, które mogą sprawić, że aplikacja będzie bardziej natywna w chmurze.

* Rozważ [dodanie nazwy DNS](/azure/aks/ingress-static-ip#configure-a-dns-name) do adresu IP przydzielonego do kontrolera ruchu przychodzącego lub modułu równoważenia obciążenia aplikacji.

* Rozważ [dodanie wykresów HELM do aplikacji](https://helm.sh/docs/topics/charts/). Wykres HELM umożliwia parametryzację wdrożenia aplikacji w celu używania i dostosowywania aplikacji przez bardziej różnorodnych klientów.

* Zaprojektuj i zaimplementuj strategię DevOps. Aby zachować niezawodność przy jednoczesnym zwiększaniu szybkości tworzenia rozwiązań, rozważ [automatyzację wdrożeń i testowania przy użyciu usługi Azure Pipelines](/azure/devops/pipelines/ecosystems/kubernetes/aks-template).

* Włącz [monitorowanie platformy Azure dla klastra](/azure/azure-monitor/insights/container-insights-enable-existing-clusters), aby umożliwić zbieranie dzienników kontenerów, śledzenie użycia i korzystanie z innych funkcji.

* Rozważ ujawnienie metryk właściwych dla aplikacji za pośrednictwem systemu Prometheus. Prometheus to struktura metryk typu open source, szeroko przyjęta w społeczności Kubernetes. [Wycinanie metryk systemu Prometheus można skonfigurować w usłudze Azure Monitor](/azure/azure-monitor/insights/container-insights-prometheus-integration) zamiast hostować własny serwer Prometheus w celu włączenia agregacji metryk z aplikacji i umożliwienia automatycznego reagowania na nietypowe warunki lub ich eskalowania.

* Zaprojektuj i zaimplementuj strategię ciągłości działania i odzyskiwania po awarii. W przypadku aplikacji o krytycznym znaczeniu rozważ zastosowanie [architektury wdrażania w wielu regionach](/azure/aks/operator-best-practices-multi-region).

* Zapoznaj się z [zasadami obsługi wersji platformy Kubernetes](/azure/aks/supported-kubernetes-versions#kubernetes-version-support-policy). To użytkownik ponosi odpowiedzialność za [aktualizowanie klastra usługi AKS](/azure/aks/upgrade-cluster), aby mieć pewność, że zawsze korzysta z obsługiwanej wersji.

* Wszyscy członkowie zespołu odpowiedzialni za administrowanie klastrami i programowanie aplikacji powinni zapoznać się z odpowiednimi [najlepszymi rozwiązaniami dotyczącymi usługi AKS](/azure/aks/best-practices).

* Oceń elementy w pliku *logging.properties*. Rozważ wyeliminowanie lub zredukowanie niektórych danych wyjściowych rejestrowania, aby zwiększyć wydajność.

* Rozważ [monitorowanie rozmiaru pamięci podręcznej kodu](https://docs.oracle.com/javase/8/embedded/develop-apps-platforms/codecache.htm) i dodanie parametrów `-XX:InitialCodeCacheSize` oraz `-XX:ReservedCodeCacheSize` do zmiennej `JAVA_OPTS` w pliku Dockerfile, aby zwiększyć wydajność.
