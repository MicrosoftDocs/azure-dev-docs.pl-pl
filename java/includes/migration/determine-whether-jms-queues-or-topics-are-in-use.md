---
author: edburns
ms.author: edburns
ms.date: 1/21/2020
ms.openlocfilehash: b2458a80eccfcae24a3364208334cce3aedea861
ms.sourcegitcommit: 21ddeb9bd9abd419d143dc2ca8a7c821a1758cf9
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2020
ms.locfileid: "79128797"
---
### <a name="determine-whether-jms-queues-or-topics-are-in-use"></a>Określanie, czy używane są kolejki lub tematy JMS

Jeśli aplikacja korzysta z kolejek lub tematów JMS, należy je zmigrować do zewnętrznie hostowanego serwera JMS (na przykład do usługi Azure Service Bus; zobacz [Używanie usługi Service Bus jako brokera komunikatów](/azure/service-bus-messaging/message-transfers-locks-settlement)).

W przypadku skonfigurowania magazynów trwałych usługi JMS należy przechwycić ich konfigurację i zastosować ją po migracji.
