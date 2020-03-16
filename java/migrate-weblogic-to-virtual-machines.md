---
title: Migrowanie aplikacji WebLogic do usługi Azure Virtual Machines
description: W tym przewodniku opisano, na co należy zwrócić uwagę, aby przeprowadzić migrację istniejącej aplikacji WebLogic w celu uruchomienia jej w usłudze Azure Virtual Machines.
author: edburns
ms.author: edburns
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: 86d1afd365c0c1f9126792d57fbeda3817500735
ms.sourcegitcommit: 56e5f51daf6f671f7b6e84d4c6512473b35d31d2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2020
ms.locfileid: "78894158"
---
# <a name="migrate-weblogic-applications-to-azure-virtual-machines"></a>Migrowanie aplikacji WebLogic do usługi Azure Virtual Machines

W tym przewodniku opisano, na co należy zwrócić uwagę, aby przeprowadzić migrację istniejącej aplikacji WebLogic w celu uruchomienia jej w usłudze Azure Virtual Machines.

## <a name="pre-migration"></a>Czynności przed migracją

### <a name="define-what-you-mean-by-migration-complete"></a>Określenie punktu oznaczającego „ukończenie migracji”

Ten przewodnik oraz odpowiednie oferty portalu Azure Marketplace są punktem wyjścia dla przyspieszenia migracji obciążeń serwera WebLogic na platformę Azure. Ważne jest, aby zdefiniować zakres nakładów pracy w związku z migracją. Przykładowo, czy stosujesz rygorystyczną metodę migracji „lift and shift” z istniejącej infrastruktury do usługi Azure Virtual Machines? Jeśli tak, idea równoczesnego zastosowania metody „lift and improve” podczas migracji może być kusząca.

Lepiej jednak trzymać się ściśle planu opartego na metodzie „lift and shift”, uwzględniając niezbędne zmiany zgodnie z opisem zawartym w tym przewodniku. Zdefiniuj punkt kontrolny oznaczający „ukończenie migracji”, aby wiedzieć, kiedy zostanie osiągnięty. Po osiągnięciu „ukończenia migracji” możesz wykonać migawkę usługi Virtual Machines, zgodnie z opisem zawartym w artykule [Tworzenie migawki](/azure/virtual-machines/windows/snapshot-copy-managed-disk). Po upewnieniu się, że możliwe jest pomyślne przywrócenie stanu z migawki, można bezpieczniej wprowadzać ulepszenia bez obaw o utratę osiągniętych postępów migracji.

### <a name="determine-whether-the-pre-built-marketplace-offers-are-a-good-starting-point"></a>Ustalenie, czy wstępnie utworzone oferty witryny Marketplace są dobrym punktem początkowym

Firmy Oracle i Microsoft nawiązały współpracę, aby zapewnić zestaw szablonów rozwiązań platformy Azure w witrynie Azure Marketplace, które stanowią solidny punkt wyjścia dla migracji do platformy Azure. Zapoznaj się z dokumentacją usługi [Oracle Fusion Middleware](https://docs.oracle.com/en/middleware/fusion-middleware/weblogic-server/12.2.1.4/wlazu/), aby uzyskać listę ofert i wybrać tę, która najlepiej odpowiada istniejącemu wdrożeniu. Listę ofert można zobaczyć [w dokumentacji firmy Oracle](https://docs.oracle.com/en/middleware/fusion-middleware/weblogic-server/12.2.1.4/wlazu/select-required-oracle-weblogic-server-offer-azure-marketplace.html#GUID-187739C5-EE7A-47C6-B3BA-C0A0333DC398)

Jeśli żadna z istniejących ofert nie jest dobrym punktem początkowym, musisz ręcznie odtworzyć wdrożenie przy użyciu zasobów maszyny wirtualnej platformy Azure. Aby uzyskać więcej informacji, zobacz artykuł [Co to jest IaaS?](https://azure.microsoft.com/overview/what-is-iaas/).

### <a name="determine-whether-the-weblogic-version-is-compatible"></a>Ustalenie, czy wersja serwera WebLogic jest zgodna

Istniejąca wersja serwera WebLogic musi być zgodna z wersją w ofertach IaaS. To zapytanie spowoduje wyświetlenie ofert dla [serwera WebLogic w wersji 12.2.1.3](https://azuremarketplace.microsoft.com/marketplace/apps?search=oracle%20weblogic%2012.2.1.3&page=1). Jeśli istniejąca wersja serwera WebLogic nie jest zgodna z tą wersją, musisz ręcznie odtworzyć wdrożenie przy użyciu zasobów usługi Azure IaaS. Aby uzyskać więcej informacji, zapoznaj się z [dokumentacją platformy Azure](https://azure.microsoft.com/overview/what-is-iaas/).

[!INCLUDE [inventory-server-capacity-virtual-machines](includes/migration/inventory-server-capacity-virtual-machines.md)]

[!INCLUDE [inventory-all-secrets](includes/migration/inventory-all-secrets.md)]

[!INCLUDE [inventory-all-certificates](includes/migration/inventory-all-certificates.md)]

[!INCLUDE [validate-that-the-supported-java-version-works-correctly](includes/migration/validate-that-the-supported-java-version-works-correctly.md)]

[!INCLUDE [inventory-jndi-resources](includes/migration/inventory-jndi-resources.md)]

[!INCLUDE [domain-configuration](includes/migration/domain-configuration.md)]

[!INCLUDE [determine-whether-session-replication-is-used](includes/migration/determine-whether-session-replication-is-used.md)]

[!INCLUDE [document-datasources](includes/migration/document-datasources.md)]

[!INCLUDE [determine-whether-weblogic-has-been-customized](includes/migration/determine-whether-weblogic-has-been-customized.md)]

[!INCLUDE [determine-whether-management-over-rest-is-used](includes/migration/determine-whether-management-over-rest-is-used.md)]

[!INCLUDE [determine-whether-a-connection-to-on-premises-is-needed](includes/migration/determine-whether-a-connection-to-on-premises-is-needed.md)]

[!INCLUDE [determine-whether-jms-queues-or-topics-are-in-use-virtual-machines](includes/migration/determine-whether-jms-queues-or-topics-are-in-use-virtual-machines.md)]

[!INCLUDE [determine-whether-you-are-using-your-own-custom-created-shared-java-ee-libraries](includes/migration/determine-whether-you-are-using-your-own-custom-created-shared-java-ee-libraries.md)]

[!INCLUDE [determine-whether-osgi-bundles-are-used](includes/migration/determine-whether-osgi-bundles-are-used.md)]

[!INCLUDE [determine-whether-your-application-contains-os-specific-code](includes/migration/determine-whether-your-application-contains-os-specific-code.md)]

[!INCLUDE [determine-whether-oracle-service-bus-is-in-use](includes/migration/determine-whether-oracle-service-bus-is-in-use.md)]

[!INCLUDE [determine-whether-your-application-is-composed-of-multiple-wars](includes/migration/determine-whether-your-application-is-composed-of-multiple-wars.md)]

[!INCLUDE [determine-whether-your-application-is-packaged-as-an-ear](includes/migration/determine-whether-your-application-is-packaged-as-an-ear.md)]

[!INCLUDE [identify-all-outside-processes-and-daemons-running-on-the-production-servers](includes/migration/identify-all-outside-processes-and-daemons-running-on-the-production-servers.md)]

[!INCLUDE [determine-whether-wlst-is-used](includes/migration/determine-whether-wlst-is-used.md)]

[!INCLUDE [validate-whether-and-how-the-file-system-is-used](includes/migration/validate-whether-and-how-the-file-system-is-used.md)]

[!INCLUDE [determine-the-network-topology](includes/migration/determine-the-network-topology.md)]

[!INCLUDE [account-for-the-use-of-jca-adapters-and-resource-adapters](includes/migration/account-for-the-use-of-jca-adapters-and-resource-adapters.md)]

[!INCLUDE [account-for-the-use-of-custom-security-providers-and-jaas](includes/migration/account-for-the-use-of-custom-security-providers-and-jaas.md)]

[!INCLUDE [determine-whether-weblogic-clustering-is-used](includes/migration/determine-whether-weblogic-clustering-is-used.md)]

[!INCLUDE [determine-whether-the-java-ee-application-client-feature-is-used](includes/migration/determine-whether-the-java-ee-application-client-feature-is-used.md)]

## <a name="migration"></a>Migracja

### <a name="select-a-weblogic-on-azure-virtual-machines-offer"></a>Wybieranie oferty serwera WebLogic w usłudze Azure Virtual Machines

Następujące oferty są dostępne dla serwera WebLogic w usłudze Azure Virtual Machines.

Podczas wdrażania oferty zostanie wyświetlony monit o wybranie rozmiaru maszyny wirtualnej dla węzłów serwera WebLogic. Ważne jest, aby uwzględnić wszystkie aspekty rozmiaru (pamięć, procesor, dysk) wybranej maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [dokumentację dotyczącą ofert](https://docs.oracle.com/en/middleware/fusion-middleware/weblogic-server/12.2.1.4/wlazu/deploy-oracle-weblogic-server-administration-server-single-node.html), a także zapoznaj się z [dokumentacją platformy Azure dotyczącą ustalania rozmiarów maszyn wirtualnych](/azure/cloud-services/cloud-services-sizes-specs)

#### <a name="weblogic-server-single-node-with-no-admin-server"></a>Pojedynczy węzeł serwera WebLogic bez serwera administracyjnego

Ta oferta tworzy pojedynczą maszynę wirtualną i instaluje na niej serwer WebLogic, ale nie konfiguruje żadnych domen, co jest przydatne w scenariuszach, w których istnieje wysoce dostosowana konfiguracja domeny.

#### <a name="weblogic-server-single-node-with-admin-server"></a>Pojedynczy węzeł serwera WebLogic z serwerem administracyjnym

Ta oferta aprowizuje jedną maszynę wirtualną i instaluje na niej serwer WebLogic 12.1.2.3. Tworzy domenę i uruchamia serwer administracyjny.

#### <a name="weblogic-server-n-node-cluster"></a>Klaster N-węzłowy serwera WebLogic

Ta oferta tworzy klaster o wysokiej dostępności z maszyn wirtualnych serwera WebLogic.

#### <a name="weblogic-server-n-node-dynamic-cluster"></a>N-węzłowy klaster dynamiczny serwera WebLogic

Ta oferta tworzy skalowalny, dynamiczny klaster o wysokiej dostępności z maszyn wirtualnych serwera WebLogic.

### <a name="provision-the-offer"></a>Aprowizacja oferty

Po wybraniu oferty początkowej postępuj zgodnie z instrukcjami w [dokumentacji dotyczącej ofert](https://wls-eng.github.io/arm-oraclelinux-wls/) w celu aprowizacji oferty. Upewnij się, że wybrano nazwę domeny zgodną z istniejącą nazwą domeny. Możesz nawet dopasować hasło domeny do istniejącego hasła domeny.

### <a name="migrate-the-domains"></a>Migrowanie domen

Po aprowizacji oferty możesz sprawdzić konfigurację domeny i skorzystać z [tych wskazówek](https://support.oracle.com/knowledge/Middleware/2336356_1.html), aby uzyskać szczegółowe informacje dotyczące migrowania domen.

### <a name="connect-the-databases"></a>Łączenie baz danych

Po przeprowadzeniu migracji domen można połączyć bazy danych, postępując zgodnie z instrukcjami [w dokumentacji oferty](https://wls-eng.github.io/arm-oraclelinux-wls/#connecting-a-database-to-a-cluster). Te instrukcje ułatwią uwzględnienie wszelkich wpisów tajnych baz danych i ciągów dostępu.

### <a name="account-for-keystores"></a>Konto magazynów kluczy

Należy uwzględnić migrację wszystkich magazynów kluczy SSL używanych przez aplikację. Więcej informacji znajduje się w temacie [Konfigurowanie magazynów kluczy](https://docs.oracle.com/en/middleware/fusion-middleware/weblogic-server/12.2.1.4/secmg/identity_trust.html#GUID-7F03EB9C-9755-430B-8B86-17199E0C01DC).

### <a name="connect-the-jms-sources"></a>Łączenie źródeł interfejsu JMS

Po nawiązaniu połączenia z bazami danych można skonfigurować interfejs JMS, postępując zgodnie z instrukcjami w temacie [Fusion Middleware Administering JMS Resources for Oracle WebLogic Server (Zarządzanie zasobami JMS na platformie Fusion Middleware na potrzeby serwera Oracle WebLogic)](https://docs.oracle.com/middleware/12213/wls/JMSAD/toc.htm) w dokumentacji serwera WebLogic.

### <a name="account-for-logging"></a>Konto na potrzeby rejestrowania

Istniejąca konfiguracja rejestrowania powinna zostać przeniesiona podczas migrowania domeny. Aby uzyskać więcej informacji, zobacz tematy [Configure java.util.logging logger levels (Konfigurowanie poziomów rejestratora java.util.logging)](https://docs.oracle.com/en/middleware/fusion-middleware/weblogic-server/12.2.1.4/wlach/taskhelp/logging/ConfigureJavaLoggingLevels.html) i [Configuring Log Files and Filtering Log Messages for Oracle WebLogic Server (Konfigurowanie plików dziennika i filtrowanie komunikatów dziennika na potrzeby serwera Oracle WebLogic)](https://docs.oracle.com/en/middleware/fusion-middleware/weblogic-server/12.2.1.4/wllog/index.html)

### <a name="migrating-your-applications"></a>Migrowanie aplikacji

Techniki używane do wdrażania aplikacji od zespołu programistycznego do serwerów testowych, przejściowych i produkcyjnych różnią się znacznie w poszczególnych przypadkach. W niektórych przypadkach stosuje się wysoce rozwiniętą platformę ciągłej integracji/ciągłego wdrażania, która powoduje wdrożenie aplikacji na serwerze WebLogic. W innych przypadkach proces może być realizowany w większym stopniu ręcznie. Jedną z korzyści związanych z używaniem usługi Azure Virtual Machines do migrowania aplikacji serwera WebLogic do chmury jest to, że istniejące procesy nadal będą działać.

Musisz skonfigurować sieciową grupę zabezpieczeń, która jest aprowizowana przez ofertę, aby zezwolić na dostęp z potoku ciągłej integracji/ciągłego wdrażania lub ręcznego systemu wdrażania. Aby uzyskać więcej informacji, zobacz [Grupy zabezpieczeń](/azure/virtual-network/security-overview) w dokumentacji platformy Azure.

### <a name="testing"></a>Testowanie

Wszystkie testy w kontenerze wykonywane wobec aplikacji muszą być skonfigurowane w taki sposób, aby uzyskiwać dostęp do nowych serwerów działających na platformie Azure. Podobnie jak w przypadku ciągłej integracji/ciągłego wdrażania, należy upewnić się, że niezbędne reguły zabezpieczeń sieci umożliwiają testom dostęp do aplikacji wdrożonych na platformie Azure. Aby uzyskać więcej informacji, zobacz [Grupy zabezpieczeń](/azure/virtual-network/security-overview) w dokumentacji platformy Azure.

## <a name="post-migration"></a>Czynności po migracji

Po osiągnięciu celów migracji zdefiniowanych w kroku [Czynności przed migracją](#pre-migration) wykonaj niektóre kompleksowe testy akceptacyjne, aby sprawdzić, czy wszystko działa zgodnie z oczekiwaniami. Niektóre tematy dotyczące ulepszeń po migracji obejmują następujące (choć z pewnością nie są do nich ograniczone):

* Użycie usługi Azure Storage do obsługiwania zawartości statycznej instalowanej na maszynach wirtualnych. Aby uzyskać więcej informacji, zobacz [Dołączanie lub odłączanie dysku danych od maszyny wirtualnej](/azure/lab-services/devtest-lab-attach-detach-data-disk).

* Wdróż aplikacje w migrowanym klastrze serwera WebLogic za pomocą usługi Azure DevOps. Aby uzyskać więcej informacji, zobacz [dokumentację dotyczącą wprowadzenia do usługi Azure DevOps](/azure/devops/get-started/?view=azure-devops).

* Popraw topologię sieci dzięki zaawansowanym usługom równoważenia obciążenia. Aby uzyskać więcej informacji, zobacz [Korzystanie z usług równoważenia obciążenia na platformie Azure](/azure/traffic-manager/traffic-manager-load-balancing-azure).

* Skorzystaj z tożsamości zarządzanych przez platformę Azure, aby zarządzać wpisami tajnymi i przypisywać dostęp oparty na rolach do zasobów platformy Azure. Aby uzyskać więcej informacji, zobacz [Czym są tożsamości zarządzane dla zasobów platformy Azure?](/azure/active-directory/managed-identities-azure-resources/overview).

* Zintegruj uwierzytelnianie i autoryzację WebLogic Java EE przy użyciu usługi Azure Active Directory. Aby uzyskać więcej informacji, zobacz [przewodnik z wprowadzeniem do integracji usługi Azure Active Directory](/azure/active-directory/manage-apps/plan-an-application-integration).

* Użyj usługi Azure Key Vault do przechowywania wszelkich informacji, które pełnią funkcję wpisu tajnego. Aby uzyskać więcej informacji, zobacz [Podstawowe pojęcia dotyczące usługi Azure Key Vault](/azure/key-vault/basic-concepts).
