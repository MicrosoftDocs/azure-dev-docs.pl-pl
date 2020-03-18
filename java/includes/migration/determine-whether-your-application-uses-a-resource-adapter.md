---
author: mriem
ms.author: manriem
ms.date: 2/28/2020
ms.openlocfilehash: f5995a3c5efc46bc58b446589ce089bf7d86b2ba
ms.sourcegitcommit: 56e5f51daf6f671f7b6e84d4c6512473b35d31d2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2020
ms.locfileid: "78897508"
---
### <a name="determine-whether-your-application-uses-a-resource-adapter"></a>Określanie, czy aplikacja używa adaptera zasobów

Jeśli aplikacja wymaga adaptera zasobów, musi być zgodna z serwerem WildFly. Ustal, czy adapter zasobów działa prawidłowo na autonomicznym wystąpieniu serwera WildFly, wdrażając go na serwerze i poprawnie konfigurując. Jeśli adapter zasobów będzie działać prawidłowo, trzeba będzie dodać pliki JAR do ścieżki klas serwera obrazu platformy Docker i umieścić niezbędne pliki konfiguracji w poprawnej lokalizacji w katalogach serwera WildFly, aby były dostępne.
