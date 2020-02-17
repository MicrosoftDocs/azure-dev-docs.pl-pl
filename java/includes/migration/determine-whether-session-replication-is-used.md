---
author: edburns
ms.author: edburns
ms.date: 1/21/2020
ms.openlocfilehash: 4c20feba0a91d10846963f8697457735e5932c20
ms.sourcegitcommit: 367780fe48d977c82cb84208c128b0bf694b1029
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2020
ms.locfileid: "77000838"
---
### <a name="determine-whether-session-replication-is-used"></a>Określenie, czy jest używana replikacja sesji

Jeśli aplikacja korzysta z replikacji sesji, z modułem Oracle Coherence*Web lub bez niego, masz trzy możliwości:

1. Moduł Coherence*Web można uruchamiać równolegle z serwerem WebLogic na maszynach wirtualnych platformy Azure, ale należy ręcznie skonfigurować tę opcję po aprowizacji oferty. W przypadku użycia autonomicznego modułu Coherence można również uruchomić go na maszynie wirtualnej platformy Azure, ale należy ręcznie skonfigurować tę opcję po aprowizacji oferty.
2. Refaktoryzacja aplikacji w celu użycia bazy danych do zarządzania sesją.
3. Refaktoryzacja aplikacji w celu eksternalizacji sesji do usługi Azure Redis. Aby uzyskać więcej informacji, zobacz [Pamięć podręczna Azure Cache for Redis](/azure/azure-cache-for-redis/cache-overview).

W przypadku wszystkich tych opcji dobrym pomysłem jest opanowanie sposobu wykonywania replikacji stanu sesji HTTP przez serwer WebLogic. Aby uzyskać więcej informacji, zobacz temat [HTTP Session State Replication (Replikacja stanu sesji HTTP)](https://docs.oracle.com/en/middleware/fusion-middleware/weblogic-server/12.2.1.4/clust/failover.html#GUID-E13D8142-66BA-46A1-854F-4FC6F82992DD) w dokumentacji firmy Oracle.
