---
author: edburns
ms.author: edburns
ms.date: 1/21/2020
ms.openlocfilehash: 26d0422b9ea569b5657a5b7841319e77ce0f1684
ms.sourcegitcommit: 56e5f51daf6f671f7b6e84d4c6512473b35d31d2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2020
ms.locfileid: "78894171"
---
### <a name="determine-whether-session-replication-is-used"></a>Określenie, czy jest używana replikacja sesji

Jeśli aplikacja korzysta z replikacji sesji, z modułem Oracle Coherence*Web lub bez niego, masz trzy możliwości:

* Moduł Coherence*Web można uruchamiać równolegle z serwerem WebLogic na maszynach wirtualnych platformy Azure, ale należy ręcznie skonfigurować tę opcję po aprowizacji oferty. W przypadku użycia autonomicznego modułu Coherence można również uruchomić go na maszynie wirtualnej platformy Azure, ale należy ręcznie skonfigurować tę opcję po aprowizacji oferty.
* Refaktoryzacja aplikacji w celu użycia bazy danych do zarządzania sesją.
* Refaktoryzacja aplikacji w celu eksternalizacji sesji do usługi Azure Redis. Aby uzyskać więcej informacji, zobacz [Pamięć podręczna Azure Cache for Redis](/azure/azure-cache-for-redis/cache-overview).

W przypadku wszystkich tych opcji dobrym pomysłem jest opanowanie sposobu wykonywania replikacji stanu sesji HTTP przez serwer WebLogic. Aby uzyskać więcej informacji, zobacz temat [HTTP Session State Replication (Replikacja stanu sesji HTTP)](https://docs.oracle.com/en/middleware/fusion-middleware/weblogic-server/12.2.1.4/clust/failover.html#GUID-E13D8142-66BA-46A1-854F-4FC6F82992DD) w dokumentacji firmy Oracle.
