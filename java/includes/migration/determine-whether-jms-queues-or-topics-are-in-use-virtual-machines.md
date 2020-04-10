---
author: edburns
ms.author: edburns
ms.date: 1/21/2020
ms.openlocfilehash: 69bddfce67388d3392e6908f3ddf1af378b116cf
ms.sourcegitcommit: 951fc116a9519577b5d35b6fb584abee6ae72b0f
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80624142"
---
### <a name="determine-whether-java-message-service-jms-queues-or-topics-are-in-use"></a>Określanie, czy używane są kolejki lub tematy Java Message Service (JMS)

Jeśli aplikacja korzysta z kolejek lub tematów JMS, należy je zmigrować do zewnętrznie hostowanego serwera JMS. Usługa Azure Service Bus i protokół Advanced Message Queuing Protocol mogą stanowić doskonałą strategię migracji w przypadku korzystania z usługi JMS. Aby uzyskać więcej informacji, zobacz [Używanie usługi JMS z usługą Azure Service Bus i protokołem AMQP 1.0](/azure/service-bus-messaging/service-bus-java-how-to-use-jms-api-amqp).

W przypadku skonfigurowania magazynów trwałych usługi JMS należy przechwycić ich konfigurację i zastosować ją po migracji.

W przypadku korzystania z programu Oracle Message Broker można migrować to oprogramowanie do maszyn wirtualnych platformy Azure i używać go w niezmienionym stanie.
