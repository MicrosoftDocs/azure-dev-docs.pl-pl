---
author: mriem
ms.author: manriem
ms.date: 2/28/2020
ms.openlocfilehash: 4d2df28d5c752d20454c28a6d4a53698aa7ff5b6
ms.sourcegitcommit: 56e5f51daf6f671f7b6e84d4c6512473b35d31d2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2020
ms.locfileid: "78897440"
---
### <a name="determine-whether-your-application-relies-on-scheduled-jobs"></a>Określanie, czy Twoja aplikacja bazuje na zaplanowanych zadaniach

Z usługą Azure Kubernetes Service NIE POWINNO używać się zaplanowanych zadań, takich jak zadania programów Quartz Scheduler lub Unix cron. Usługa Azure Kubernetes Service nie uniemożliwi wdrożenia aplikacji zawierającej wewnętrznie zaplanowane zadania. Jeśli jednak aplikacja jest skalowana w poziomie, to samo zaplanowane zadanie może zostać uruchomione więcej niż raz w zaplanowanym okresie. Ta sytuacja może prowadzić do niezamierzonych konsekwencji.

Aby wykonywać zaplanowane zadania w klastrze usługi AKS, definiuj zadania Kubernetes CronJob wedle potrzeby. Aby uzyskać więcej informacji, zobacz [Uruchamiania zautomatyzowanych zadań przy użyciu obiektów CronJob](https://kubernetes.io/docs/tasks/job/automated-tasks-with-cron-jobs/).
