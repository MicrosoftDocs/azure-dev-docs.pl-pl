---
author: mriem
ms.author: manriem
ms.date: 2/28/2020
ms.openlocfilehash: bda7ba230d8287101fc8f3f298b08777bb6e1c61
ms.sourcegitcommit: 56e5f51daf6f671f7b6e84d4c6512473b35d31d2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2020
ms.locfileid: "78897474"
---
### <a name="determine-whether-ejb-timers-are-in-use"></a>Określanie, czy są używane czasomierze EJB

Jeśli aplikacja korzysta z czasomierzy EJB, należy zweryfikować, czy kod czasomierza EJB może być wyzwalany niezależnie przez każde wystąpienie serwera WildFly. Ta weryfikacja jest konieczna, ponieważ w scenariuszu wdrażania usługi Azure Kubernetes Service każdy czasomierz EJB będzie wyzwalany we własnym wystąpieniu serwera WildFly.
