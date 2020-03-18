---
author: mriem
ms.author: manriem
ms.date: 2/28/2020
ms.openlocfilehash: 99140db00e7a0a72b96db19b4dab9d7476694ab2
ms.sourcegitcommit: 56e5f51daf6f671f7b6e84d4c6512473b35d31d2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2020
ms.locfileid: "78897525"
---
### <a name="determine-whether-jaas-is-in-use"></a>Określanie, czy usługa JAAS jest w użyciu

Jeśli Twoja aplikacja korzysta z usługi JAAS, należy przechwycić sposób konfiguracji tej usługi. Jeśli korzysta ona z bazy danych, możesz ją przekonwertować na domenę usługi JAAS na serwerze WildFly. Jeśli jest to implementacja niestandardowa, będzie trzeba sprawdzić, czy może być używana na serwerze WildFly.
