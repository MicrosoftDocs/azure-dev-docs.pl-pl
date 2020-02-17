---
author: yevster
ms.author: yebronsh
ms.date: 1/22/2020
ms.openlocfilehash: 1015c179c0f93485decd77bd89a3ceec8833652e
ms.sourcegitcommit: 367780fe48d977c82cb84208c128b0bf694b1029
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2020
ms.locfileid: "77000964"
---
### <a name="migrate-scheduled-jobs"></a>Migrowanie zaplanowanych zadań

Aby wykonać zaplanowane zadania na platformie Azure, warto użyć [wyzwalacza czasomierza dla usługi Azure Functions](/azure/azure-functions/functions-bindings-timer). Nie musisz migrować kodu zadania do funkcji. Aby wyzwolić zadanie, funkcja może po prostu wywoływać adres URL w aplikacji. Jeśli wykonywane zadania muszą być wywoływane dynamicznie i/lub śledzone centralnie, warto użyć harmonogramu [Spring Batch](https://spring.io/projects/spring-batch).

Można też utworzyć aplikację logiki z wyzwalaczem cyklicznym, aby wywoływać adres URL bez pisania kodu poza aplikacją. Aby uzyskać więcej informacji, zobacz [Co to jest usługa Azure Logic Apps — omówienie](/azure/logic-apps/logic-apps-overview) i [Tworzenie, planowanie i uruchamianie cyklicznych zadań oraz przepływów pracy za pomocą wyzwalacza cyklicznego w usłudze Azure Logic Apps](/azure/connectors/connectors-native-recurrence).

> [!NOTE]
> Aby zapobiec złośliwemu użyciu, prawdopodobnie trzeba będzie wprowadzić wymaganie podania poświadczeń w punkcie końcowym wywoływania zadania. W takim przypadku poświadczenia muszą zostać podane przez funkcję wyzwalacza.
