---
author: edburns
ms.author: edburns
ms.date: 1/21/2020
ms.openlocfilehash: e36fae7adfda8c05e222151872b137fa600cdd97
ms.sourcegitcommit: 367780fe48d977c82cb84208c128b0bf694b1029
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2020
ms.locfileid: "77000901"
---
### <a name="determine-whether-jms-queues-or-topics-are-in-use"></a>Określanie, czy używane są kolejki lub tematy JMS

Jeśli aplikacja korzysta z kolejek lub tematów JMS, należy je zmigrować do zewnętrznie hostowanego serwera JMS. Usługa Azure Service Bus i protokół Advanced Message Queuing Protocol mogą stanowić doskonałą strategię migracji w przypadku korzystania z usługi JMS. Aby uzyskać więcej informacji, zobacz [Używanie usługi Java Message Service (JMS) z usługą Azure Service Bus i protokołem AMQP 1.0](/azure/service-bus-messaging/service-bus-java-how-to-use-jms-api-amqp).

W przypadku skonfigurowania magazynów trwałych usługi JMS należy przechwycić ich konfigurację i zastosować ją po migracji.

W przypadku korzystania z programu Oracle Message Broker można migrować to oprogramowanie do maszyn wirtualnych platformy Azure i używać go w niezmienionym stanie.
